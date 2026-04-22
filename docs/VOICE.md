---
name: VOICE
description: Pipeline vocal complet de STRUCTORAI — STT (Whisper OpenAI / Deepgram fallback), TTS (ElevenLabs Multilingual v2 / OpenAI TTS fallback), 6 langues (FR/EN/TR/PT/AR/ES), latence cible <1.5s entre fin de parole et début de réponse, voix configurable homme/femme, streaming audio bidirectionnel, robustesse au bruit chantier, mode mains libres (push-to-talk + hotword), fallback Web Speech API sur PWA iOS. Voix = différenciateur n°1 vs concurrents (aucun ne fait le vocal bidirectionnel BTP).
type: technical-pipeline
scope: backend-services, mobile-audio, voice-service, whisper-elevenlabs
priority: critical
date: 2026-04-18
version: 1.0
latency_target: <1.5s (fin de parole artisan → début réponse vocale)
languages: FR (primaire), EN, TR, PT, AR, ES
stt_default: OpenAI Whisper API
stt_fallback: Deepgram
tts_default: ElevenLabs Multilingual v2
tts_fallback: OpenAI TTS
voice_options: male_pro / female_pro / male_casual / female_casual
---

# docs/VOICE.md — Pipeline vocal STRUCTORAI

> **Ce fichier est la source de vérité du pipeline vocal.**
> Le vocal est LE différenciateur n°1 vs concurrents (Obat/Tolteck/Batigest : 0 vocal bidirectionnel, Renalto/Cegid Boby : vocal dictée uniquement).
> Latence cible : **<1.5s** entre fin de parole artisan et début de réponse vocale.
> Reference : `docs/ARCH.md` §13 Performance + `docs/AGENTS.md` §2-15 utilisation par agents.

---

## 0. SOMMAIRE

1. [Principes fondateurs du vocal](#1-principes-fondateurs-du-vocal)
2. [Architecture du pipeline](#2-architecture-du-pipeline)
3. [STT — Speech-to-Text (Whisper + Deepgram)](#3-stt--speech-to-text-whisper--deepgram)
4. [TTS — Text-to-Speech (ElevenLabs + OpenAI)](#4-tts--text-to-speech-elevenlabs--openai)
5. [Streaming bidirectionnel](#5-streaming-bidirectionnel)
6. [Les 6 langues supportées](#6-les-6-langues-supportées)
7. [Voix configurables homme/femme](#7-voix-configurables-hommefemme)
8. [Mode mains libres (push-to-talk + hotword)](#8-mode-mains-libres-push-to-talk--hotword)
9. [Robustesse au bruit chantier](#9-robustesse-au-bruit-chantier)
10. [Fallback Web Speech API (PWA iOS)](#10-fallback-web-speech-api-pwa-ios)
11. [Backend — voice_service.py](#11-backend--voice_servicepy)
12. [Mobile — useVoice hook + Expo Audio](#12-mobile--usevoice-hook--expo-audio)
13. [Latence — budget et optimisations](#13-latence--budget-et-optimisations)
14. [Coûts et optimisations économiques](#14-coûts-et-optimisations-économiques)
15. [Garde-fous anti-abus](#15-garde-fous-anti-abus)
16. [Accessibilité et transparence AI Act](#16-accessibilité-et-transparence-ai-act)
17. [Tests et métriques](#17-tests-et-métriques)
18. [Troubleshooting](#18-troubleshooting)
19. [Références croisées](#19-références-croisées)

---

## 1. PRINCIPES FONDATEURS DU VOCAL

### 1.1 — Pourquoi le vocal est critique

**Contexte artisan BTP** (voir `PRODUCT_CONTEXT.md`) :
- Mains dans le plâtre, la peinture, le ciment
- Impossible de taper sur un écran
- Voiture, chantier, bruit ambiant
- 8h-19h travail physique, pas le temps de s'asseoir devant un écran

**Le vocal doit être comme un collègue** :
- On parle, il comprend
- Il répond à voix haute avec une voix naturelle
- On enchaîne sans toucher l'écran
- Il annonce les choses importantes sans qu'on demande

### 1.2 — Différenciation concurrentielle

**Aucun concurrent BTP** ne fait le vocal bidirectionnel complet :

| Concurrent | Vocal ? |
|---|---|
| Obat | ❌ Aucun |
| Tolteck | ❌ Aucun |
| Batappli | ❌ Aucun |
| EBP | ❌ Aucun |
| Sage Batigest | ❌ Aucun |
| Renalto (Rita) | ⚠️ Dictée uniquement, pas de réponse vocale |
| Cegid Boby | ⚠️ Dictée + multilingue, pas de réponse vocale naturelle |
| **STRUCTORAI** | ✅ **Bidirectionnel + 6 langues + streaming + homme/femme** |

### 1.3 — Les 5 exigences non négociables

1. **Latence totale <1.5s** entre fin de parole artisan et début de réponse vocale
2. **Voix naturelle** (pas robotique) — ElevenLabs Multilingual v2 en défaut
3. **6 langues** supportées dès le launch (FR/EN/TR/PT/AR/ES)
4. **Robustesse au bruit** chantier (disqueuse, perceuse, circulation)
5. **Streaming bidirectionnel** — le cerveau commence à parler avant d'avoir fini de générer

---

## 2. ARCHITECTURE DU PIPELINE

### 2.1 — Diagramme complet

```
┌──────────────────────────────────────────────────────────────┐
│                    MOBILE (Expo Audio)                        │
│                                                               │
│  1. Bouton micro FAB flottant pressé (push-to-talk)          │
│      OU                                                       │
│     Hotword "Hey [Nom]" détecté (mode mains libres)          │
│                                                               │
│  2. Expo Audio Recording                                     │
│     - Format : m4a (AAC) ou opus                             │
│     - Sample rate : 16 kHz                                   │
│     - Mono                                                    │
│     - VAD (Voice Activity Detection) — coupe au silence     │
│                                                               │
│  3. Upload streaming → POST /v1/chat/audio                   │
│     Content-Type: multipart/form-data                        │
│     Fields: audio (file), context, conversation_id,          │
│             response_mode, voice                             │
└────────────────────────────┬─────────────────────────────────┘
                             │ HTTPS streaming upload
                             ▼
┌──────────────────────────────────────────────────────────────┐
│                    BACKEND (FastAPI)                          │
│                                                               │
│  4. chat_audio.py endpoint                                   │
│      ↓                                                        │
│  5. voice_service.py — Pipeline STT                          │
│     - Whisper API (OpenAI) — défaut                          │
│     - Deepgram — fallback si Whisper down                    │
│     - Détection langue auto (sauf si forcée)                 │
│     - Retour : transcript + confidence + language            │
│      ↓                                                        │
│  6. Supervisor.handle_chat(transcript, context)              │
│     - Intent detection                                        │
│     - Routage agent adapté                                    │
│     - Recall Mem0 + MemPalace                                 │
│     - Appel Claude LLM (streaming=true)                      │
│      ↓                                                        │
│  7. Réponse Claude en streaming                              │
│     - Dès les premiers tokens → envoi vers TTS               │
│      ↓                                                        │
│  8. voice_service.py — Pipeline TTS                          │
│     - ElevenLabs Multilingual v2 (défaut)                    │
│     - OpenAI TTS — fallback si ElevenLabs down               │
│     - Streaming chunks audio → WebSocket / HTTP chunked      │
│      ↓                                                        │
│  9. Audio chunks renvoyés au mobile                          │
│     - Format : mp3 (compatibilité large)                     │
│     - Chunks : 1-2 sec audio chacun                          │
└────────────────────────────┬─────────────────────────────────┘
                             │ HTTPS streaming (chunked)
                             ▼
┌──────────────────────────────────────────────────────────────┐
│                    MOBILE (Expo Audio)                        │
│                                                               │
│  10. Audio chunks reçus → buffer de lecture                  │
│  11. Playback dès le 1er chunk (parle avant fin génération)  │
│  12. UI met à jour transcript en temps réel                  │
│  13. Artisan peut enchaîner (interrupt possible)             │
└──────────────────────────────────────────────────────────────┘
```

### 2.2 — Étapes clés en durée

| Étape | Durée cible | Tech |
|---|---|---|
| Upload audio (3s de parole) | ~200ms | HTTPS compressé |
| STT Whisper | **~500-800ms** | API OpenAI |
| Intent + recall mémoire | ~100ms | Redis + SQL |
| LLM streaming (1er chunk) | ~300-500ms | Claude Sonnet streaming |
| TTS streaming (1er chunk) | ~200-400ms | ElevenLabs WebSocket |
| Download premier chunk audio | ~100ms | HTTP chunked |
| **TOTAL (1er chunk audio)** | **~1.4s** | ✅ <1.5s cible |

### 2.3 — Les 2 modes de réponse

**Mode `text`** (économique) :
- STT → LLM → retour texte only
- Coût : STT + LLM
- Usage : quand l'artisan lit l'écran (dashboard)

**Mode `voice`** (pleine vocal) :
- STT → LLM → TTS → audio streaming
- Coût : STT + LLM + TTS
- Usage : mode mains libres, chantier

**Mode `both`** (défaut — hybride) :
- Texte + audio renvoyés en parallèle
- L'artisan peut lire ET écouter

---

## 3. STT — SPEECH-TO-TEXT (WHISPER + DEEPGRAM)

### 3.1 — Whisper API (OpenAI) — défaut

**Modèle** : `whisper-1` (API OpenAI)

**Endpoint** : `https://api.openai.com/v1/audio/transcriptions`

**Caractéristiques** :
- Multi-langue natif (99 langues, dont les 6 STRUCTORAI)
- Auto-détection de langue (ou paramètre `language` pour forcer)
- Sortie : texte plain + timestamps optionnels + détection langue
- Coût : **$0.006/minute** audio
- Latence : **500-800ms** pour 10s d'audio

**Request minimal** :
```python
import openai

client = openai.OpenAI(api_key=settings.OPENAI_API_KEY)

transcription = client.audio.transcriptions.create(
    model="whisper-1",
    file=audio_file,                     # m4a/mp3/ogg/webm/wav
    language=user_locale[:2],            # "fr" / "en" / "tr" / "pt" / "ar" / "es"
    response_format="verbose_json",      # inclut confidence + segments
    temperature=0.0                      # déterministe
)
# Retour : {text, language, duration, segments}
```

### 3.2 — Deepgram — fallback

**Utilisé si** : Whisper API timeout > 3s OU erreur 5xx 3× consécutives.

**Modèle** : `nova-2` (meilleur rapport qualité/vitesse)

**Avantages** :
- Latence encore plus faible (~300ms pour 10s audio)
- Streaming natif (Whisper non)
- Moins cher ($0.0043/min)

**Trade-off** : qualité multi-langue légèrement inférieure à Whisper sur TR/AR.

**Fallback chain** :
```
Whisper API (défaut)
  ↓ (timeout > 3s ou 5xx × 3)
Deepgram Nova-2
  ↓ (timeout > 3s)
Retour erreur "STT_FAILED" à l'utilisateur avec bouton "Réessayer"
```

### 3.3 — Pré-traitement audio côté mobile

Avant d'envoyer à Whisper :
1. **Format** : m4a (AAC) ou opus (meilleur ratio qualité/taille)
2. **Sample rate** : 16 kHz (Whisper optimal)
3. **Mono** (pas stéréo — Whisper ne l'utilise pas)
4. **VAD** (Voice Activity Detection) — coupe aux silences >1.5s pour ne pas envoyer du vide
5. **Compression** : OGG Opus @ 24kbps pour upload rapide (qualité vocale OK)

**Validation côté client** (anti-abus, voir §15) :
- Durée > 1s et < 180s (3 min max)
- Taille > 500 bytes (pas de fichier vide)
- Taille < 10 MB

### 3.4 — Gestion des erreurs STT

| Erreur | Code | Action |
|---|---|---|
| Audio vide / trop court | `STT_AUDIO_TOO_SHORT` | 400 — message "Vocal trop court" |
| Audio trop long (>3 min) | `STT_AUDIO_TOO_LONG` | 400 — message "Maximum 3 minutes" |
| Whisper timeout | (tenter Deepgram) | 502 si tous fallbacks échouent |
| Langue non détectée | `STT_LANGUAGE_UNKNOWN` | 422 — fallback FR |
| Transcription vide | `STT_NO_SPEECH` | 200 avec `transcript=""` + message |

### 3.5 — Confidence score

Whisper retourne un score par segment. Agrégation :
- Moyenne pondérée par durée segment
- **< 0.6** → afficher warning "J'ai peut-être mal compris, tu peux répéter ?"
- **>= 0.8** → pas d'indication

---

## 4. TTS — TEXT-TO-SPEECH (ELEVENLABS + OPENAI)

### 4.1 — ElevenLabs Multilingual v2 — défaut

**Modèle** : `eleven_multilingual_v2`

**Endpoint streaming WebSocket** : `wss://api.elevenlabs.io/v1/text-to-speech/{voice_id}/stream-input`

**Caractéristiques** :
- Voix **naturelle humaine** (meilleure qualité du marché)
- Multi-langue natif (29 langues dont les 6 STRUCTORAI)
- Streaming WebSocket (latence <400ms pour 1er chunk)
- Voix configurables (catalogue + clone possible en Pro)
- Coût : **$0.12 / 1K caractères** (Multilingual v2 plan Creator)

**Request streaming (pseudo-code)** :
```python
import websockets

async def stream_elevenlabs_tts(text: str, voice_id: str):
    uri = f"wss://api.elevenlabs.io/v1/text-to-speech/{voice_id}/stream-input?model_id=eleven_multilingual_v2"
    
    async with websockets.connect(uri, extra_headers={"xi-api-key": API_KEY}) as ws:
        # Envoi config initial
        await ws.send(json.dumps({
            "text": " ",                    # bootstrap
            "voice_settings": {
                "stability": 0.5,
                "similarity_boost": 0.75,
                "style": 0.0,
                "use_speaker_boost": True
            },
            "generation_config": {
                "chunk_length_schedule": [50, 80, 120, 160]
            }
        }))
        
        # Envoi texte au fil du LLM streaming
        async for token in llm_stream:
            await ws.send(json.dumps({"text": token}))
        
        await ws.send(json.dumps({"text": ""}))   # EOS
        
        # Réception chunks audio
        async for message in ws:
            audio_chunk = json.loads(message)
            if audio_chunk.get("audio"):
                yield base64.b64decode(audio_chunk["audio"])
```

### 4.2 — OpenAI TTS — fallback

**Modèles** : `tts-1` (standard) ou `tts-1-hd` (haute qualité, plus lent)

**Endpoint** : `https://api.openai.com/v1/audio/speech`

**Voix OpenAI** : `alloy`, `echo`, `fable`, `onyx`, `nova`, `shimmer`

**Trade-offs** :
- Coût : **$15/1M chars** (tts-1), $30/1M chars (tts-1-hd) → **~87% moins cher qu'ElevenLabs**
- Qualité : correcte mais **moins naturelle** qu'ElevenLabs
- Streaming : supporté (HTTP chunked)
- Latence : ~500ms 1er chunk

**Utilisé comme fallback** ou **option économique** dans settings artisan (plan Starter par défaut OpenAI TTS pour économiser).

### 4.3 — Fallback chain TTS

```
ElevenLabs Multilingual v2 (défaut Pro/Business)
  ↓ (timeout > 3s ou 5xx × 3)
OpenAI tts-1
  ↓ (timeout > 3s)
Réponse texte uniquement (pas d'audio) + message "Voix indisponible, réponse texte"
```

### 4.4 — Pré-traitement texte avant TTS

Avant envoi au TTS, le texte est nettoyé :
- Suppression markdown (`**`, `*`, `_`, `#`)
- Conversion abréviations → lettres (ex: "SDB" → "salle de bain" dans le premier usage, puis garder "SDB" si répété)
- Formatage nombres (ex: "1500€" → "mille cinq cents euros")
- Dates (ex: "15/03/2026" → "15 mars 2026")
- Acronymes (ex: "TVA" → prononcé "T V A")

**Service** : `backend/app/services/tts_preprocessor.py`

### 4.5 — SSML (Speech Synthesis Markup Language)

ElevenLabs supporte un SSML limité. STRUCTORAI l'utilise pour :
- **Pauses** : `<break time="500ms"/>` entre éléments de liste
- **Emphasis** : `<emphasis level="strong">urgent</emphasis>`
- **Changement de rythme** : accelerate pour les listes longues

OpenAI TTS ne supporte PAS SSML → fallback sans enrichissement.

---

## 5. STREAMING BIDIRECTIONNEL

### 5.1 — Pourquoi le streaming est critique

Sans streaming :
- Whisper (1s) + Claude (2-3s complet) + ElevenLabs (2s) = **5-6s** de latence
- L'artisan attend trop, l'UX est cassée

Avec streaming :
- Whisper (1s) + Claude **1er chunk** (500ms) + ElevenLabs **1er chunk** (300ms) = **~1.8s**
- Avec optimisations (upload streaming + LLM→TTS pipeline) → **~1.4s**

### 5.2 — Streaming côté LLM (Claude)

Claude API supporte `stream=true` :
- Chunks de tokens envoyés dès génération
- Premiers tokens arrivent après ~300ms
- Permet de déclencher TTS avant fin de génération

**Code pattern** :
```python
async with anthropic_client.messages.stream(
    model="claude-sonnet-4-6",
    messages=messages,
    max_tokens=1000
) as stream:
    async for text in stream.text_stream:
        # Accumuler par phrase complète (point, ! ?)
        buffer += text
        if sentence_complete(buffer):
            # Envoyer au TTS pour génération parallèle
            await tts_queue.put(buffer)
            buffer = ""
```

### 5.3 — Streaming côté TTS

**ElevenLabs WebSocket** supporte le streaming bidirectionnel :
- Envoi de texte token par token (ou phrase par phrase)
- Réception audio chunks immédiatement
- Le cerveau commence à "parler" avant d'avoir fini de "penser"

### 5.4 — Streaming côté mobile (réception)

**Expo Audio** avec `Audio.Sound.createAsync()` en mode `progressiveDownload: true` :
- Playback commence au 1er chunk audio
- Pas d'attente du fichier complet

**Alternative WebSocket** : ouverture WS `/v1/chat/ws` + events :
```json
{"event": "voice_stream_chunk", "data": {"audio_b64": "...", "seq": 1}}
{"event": "voice_stream_chunk", "data": {"audio_b64": "...", "seq": 2}}
{"event": "voice_stream_end"}
```

### 5.5 — Interruption (barge-in)

L'artisan peut interrompre la réponse vocale en appuyant sur le micro :
1. Nouveau press micro → `{"event": "cancel"}` WS
2. Backend stoppe streaming LLM + TTS
3. Mobile arrête playback immédiatement
4. Nouvelle écoute démarre

---

## 6. LES 6 LANGUES SUPPORTÉES

### 6.1 — Liste et priorités

Voir `docs/SINGLE_SOURCE_OF_TRUTH.md` §14 :

| Langue | Code i18n | Code Whisper | Priorité | Notes |
|---|---|---|---|---|
| Français | `fr-FR` | `fr` | 🔴 Primaire | Langue mère, 80%+ des users |
| Anglais | `en-US` | `en` | 🟡 International | Expats, artisans bilingues |
| Turc | `tr-TR` | `tr` | 🟢 Communauté FR | Forte communauté BTP en France |
| Portugais | `pt-PT` | `pt` | 🟢 Communauté FR | Forte communauté BTP en France |
| Arabe | `ar-SA` | `ar` | 🟢 Communauté FR | Forte communauté BTP en France (algérien/marocain/tunisien) |
| Espagnol | `es-ES` | `es` | 🟢 Communauté FR | Communauté + expansion Espagne prévue |

### 6.2 — Détection automatique vs forcée

**Par défaut** : détection automatique par Whisper (retourne `language` dans sa réponse).

**Forcée** si user préférence définie dans `settings.locale`.

**Règles de détection** :
- Si user a défini `locale`, forcer (sauf si confidence détection >0.95 et langue différente → warning)
- Si pas de locale définie (anonyme, sandbox) → auto-détection

### 6.3 — Voix TTS par langue

**ElevenLabs Multilingual v2** gère les 6 langues nativement avec la même voix (pas de voice switching).

**Voice IDs recommandés** (à affiner en production) :

| Profil | Voice ID ElevenLabs |
|---|---|
| `male_pro` (défaut pro) | `pNInz6obpgDQGcFmaJgB` (Adam) |
| `female_pro` (défaut pro) | `EXAVITQu4vr4xnSDxMaL` (Bella) |
| `male_casual` | `ErXwobaYiN019PkySvjV` (Antoni) |
| `female_casual` | `21m00Tcm4TlvDq8ikWAM` (Rachel) |

**Fallback OpenAI TTS** :

| Profil | Voix OpenAI |
|---|---|
| `male_pro` | `onyx` |
| `female_pro` | `nova` |
| `male_casual` | `echo` |
| `female_casual` | `shimmer` |

### 6.4 — Adaptation culturelle par langue

Voir `docs/AGENTS.md` §6.5 (Planning) et `FEATURES.md` F133 :
- **Ramadan** (AR/TR) : horaires briefing décalés, pauses adaptées
- **Shabbat** : pas de notifications vendredi 18h → samedi 20h
- **Formules de politesse** : plus formelles en AR/TR, plus décontractées en EN

### 6.5 — Terminologie BTP multi-langue

Le vocabulaire BTP technique (SDB, carrelage, plomberie, etc.) doit être correctement transcrit par Whisper :
- FR : référentiel complet `docs/METIER.md`
- EN : mapping termes BTP anglais/américains
- TR/PT/AR/ES : termes techniques usuels BTP

**Dictionnaire par langue** : `data/btp_terminology_{locale}.json` (à constituer, collab avec artisans bilingues beta-testeurs).

---

## 7. VOIX CONFIGURABLES HOMME/FEMME

### 7.1 — 4 profils disponibles

| Profil | Description | Usage suggéré |
|---|---|---|
| `male_pro` (défaut) | Voix grave, professionnelle, posée | Artisan installateur traditionnel |
| `female_pro` | Voix claire, professionnelle, rassurante | Alternative pour femmes artisans ou préférence |
| `male_casual` | Voix plus jeune, décontractée | Artisans <40 ans |
| `female_casual` | Voix jeune, dynamique | Préférence client |

### 7.2 — Configuration par l'artisan

**UI Settings** :
```
┌─────────────────────────────────────────┐
│ 🔊 Voix de ton assistant                │
│                                         │
│ ◉ Homme professionnel                   │
│ ○ Femme professionnelle                 │
│ ○ Homme décontracté                     │
│ ○ Femme décontractée                    │
│                                         │
│ [▶ Tester la voix]                     │
│                                         │
│ Vitesse : [−] 1.0x [+]                  │
└─────────────────────────────────────────┘
```

### 7.3 — Persistence

Stocké dans `users.voice_preference` (ex: `male_pro`) + `users.voice_speed` (ex: `1.0`).

### 7.4 — Vitesse

L'artisan peut ajuster la vitesse entre 0.8x et 1.3x (ElevenLabs `voice_settings.speed`).

### 7.5 — Voice clone (Business uniquement) — roadmap V2

Plan Business pourra cloner sa propre voix (ou celle d'un collègue) :
- Upload 3 min d'audio de référence
- ElevenLabs Instant Voice Clone
- Utilisable pour répondre à des appels clients en "sa" voix

**Non V1** — roadmap post-launch.

---

## 8. MODE MAINS LIBRES (PUSH-TO-TALK + HOTWORD)

### 8.1 — Push-to-talk (défaut V1)

**Interaction** :
1. L'artisan appuie sur le bouton micro FAB flottant
2. Il parle pendant qu'il appuie
3. Il relâche → envoi automatique
4. Réponse vocale immédiate

**UX** :
- Bouton visible sur TOUS les écrans (FAB flottant)
- Animation visuelle pendant l'enregistrement (waveform)
- Indication "Écoute..." → "Réfléchit..." → "Répond..."

### 8.2 — Mode hotword (V1.5)

**Planifié V1.5 (juillet-août 2026)** — pas critique au launch.

**Technologie** : Picovoice Porcupine ou Vosk (offline, battery-friendly).

**Comportement** :
1. L'artisan active "mode mains libres" dans settings
2. L'app écoute en fond le hotword personnalisable (ex: "Hey StructorAI" ou "Cerveau")
3. Détection hotword → démarre enregistrement
4. VAD détecte fin de parole → envoi
5. Réponse vocale

**Limitations** :
- Consomme batterie (~5-10% en plus en background)
- Nécessite permissions permanentes micro (dialogue user)
- Pas disponible sur PWA iOS (limitations Safari)

### 8.3 — Tap-to-speak (mode continu — V1.5)

**Alternative au hotword** :
1. L'artisan tap 2× sur le FAB → mode continu
2. Après chaque réponse du cerveau, micro se réactive automatiquement 3s
3. Si l'artisan ne parle pas → sort du mode continu
4. Permet conversation naturelle sans re-tap

---

## 9. ROBUSTESSE AU BRUIT CHANTIER

### 9.1 — Réalité du chantier

Niveau sonore typique :
- Disqueuse : 90-110 dB
- Perceuse : 85-100 dB
- Marteau-piqueur : 100-120 dB
- Circulation : 60-80 dB
- Conversation chantier : 70-85 dB

→ Le vocal doit marcher **malgré** ce bruit.

### 9.2 — Noise cancellation côté mobile

**Expo Audio** ne supporte pas nativement noise cancellation, mais :
- Micro natif Android/iOS a déjà un traitement hardware (noise suppression, echo cancellation)
- Utiliser les paramètres `interruptionModeAndroid: InterruptionModeAndroid.DuckOthers`

**Bibliothèque optionnelle** : `react-native-audio-recorder-player` avec config bruit.

### 9.3 — Pré-filtrage côté backend

Avant Whisper :
- Détection niveau audio (RMS) — rejette si SNR trop bas
- Optionnel : pré-traitement FFmpeg (filter noise reduction)

**Trade-off** : le noise filter peut altérer la voix → Whisper moins précis. Tests à faire.

### 9.4 — Recommandations artisan

UX : message au 1er usage :
> *"Pour des résultats optimaux, tiens ton téléphone à ~20cm de la bouche. Si l'environnement est très bruyant, parle plus lentement et plus fort."*

### 9.5 — Fallback texte

Si 3 transcriptions consécutives < 0.5 confidence :
- Message : "Je n'arrive pas à bien te comprendre. Tu peux taper ton message ?"
- Ouvre clavier automatiquement

---

## 10. FALLBACK WEB SPEECH API (PWA iOS)

### 10.1 — Contexte PWA iOS

Safari sur iOS supporte **Web Speech API** (Speech Recognition + Speech Synthesis) depuis iOS 14.5+ :
- `SpeechRecognition` (reconnaissance)
- `SpeechSynthesisUtterance` (synthèse)

**Trade-offs vs pipeline Whisper+ElevenLabs** :
- ✅ **Gratuit** (pas d'appel API)
- ✅ **Offline** possible
- ✅ **Latence ultra-faible** (on-device)
- ❌ Qualité **inférieure** à Whisper (surtout multi-langue)
- ❌ Voix **robotique** (surtout en FR)
- ❌ **Pas de streaming** fin
- ❌ Disponibilité variable selon version iOS

### 10.2 — Stratégie de dégradation

**PWA iOS — défaut** :
- Utilise Web Speech API pour éviter coûts API
- MAIS : si user abonné Pro/Business, utiliser Whisper+ElevenLabs quand même (qualité premium payée)

**Settings utilisateur** :
- Option "Voix haute qualité (coût API)" vs "Voix standard (gratuit)"

### 10.3 — Implementation Web Speech API

**STT** :
```typescript
const recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
recognition.lang = userLocale;                     // 'fr-FR'
recognition.continuous = false;
recognition.interimResults = true;

recognition.onresult = (event) => {
    const transcript = event.results[event.results.length - 1][0].transcript;
    // Envoi backend avec transcript
};

recognition.start();
```

**TTS** :
```typescript
const utterance = new SpeechSynthesisUtterance(text);
utterance.lang = userLocale;
utterance.voice = speechSynthesis.getVoices().find(v => v.name.includes('Thomas')); // exemple FR homme
utterance.rate = 1.0;
utterance.pitch = 1.0;

speechSynthesis.speak(utterance);
```

### 10.4 — Limites

- iOS Safari : Web Speech API **nécessite une interaction utilisateur** pour démarrer (tap)
- Pas de mode hotword / continu
- Voix FR robotique sur iOS (OK en EN, moins bien FR/AR/TR)

### 10.5 — Android PWA (Chrome)

Chrome Android supporte Web Speech API avec bonne qualité FR → utilisable comme fallback économique.

---

## 11. BACKEND — `voice_service.py`

### 11.1 — Responsabilités

**Fichier** : `backend/app/services/voice_service.py`

Encapsule :
- STT (Whisper + Deepgram fallback)
- TTS (ElevenLabs + OpenAI fallback)
- Pre/post-processing
- Gestion cache audio (voix récurrentes)
- Métriques latence

### 11.2 — Interface publique

```python
class VoiceService:
    async def transcribe(
        self,
        audio_bytes: bytes,
        locale: str | None = None,
        organization_id: UUID,
    ) -> TranscriptionResult:
        """STT avec fallback chain."""
        ...
    
    async def synthesize_streaming(
        self,
        text_stream: AsyncIterator[str],
        voice_profile: str,
        speed: float,
        locale: str,
        organization_id: UUID,
    ) -> AsyncIterator[bytes]:
        """TTS streaming avec fallback chain."""
        ...
    
    async def get_usage_stats(
        self,
        organization_id: UUID,
        period: str,
    ) -> VoiceUsageStats:
        """Stats usage vocal (pour billing)."""
        ...


@dataclass
class TranscriptionResult:
    transcript: str
    language: str
    confidence: float
    duration_seconds: float
    provider: str                 # "whisper" | "deepgram" | "webspeech"
    latency_ms: int
    cost_usd: float
```

### 11.3 — Cache audio (optimisation)

Pour les réponses récurrentes (ex: "Ton devis est prêt", "Facture envoyée"), cache Redis :
- Key : hash(texte + voice + speed + locale)
- TTL : 30 jours
- Économie estimée : ~15-20% des appels TTS

### 11.4 — Logging

Chaque appel STT/TTS loggé avec :
- `organization_id`
- `agent_name` (qui a demandé)
- `duration_ms_audio`
- `duration_ms_latency`
- `provider_used`
- `cost_usd`
- `fallback_triggered` (bool)

Export pour `ai_decisions` (audit AI Act, voir `docs/AI-ACT-COMPLIANCE.md`).

---

## 12. MOBILE — `useVoice` HOOK + EXPO AUDIO

### 12.1 — Structure du hook

**Fichier** : `mobile/hooks/useVoice.ts`

```typescript
export const useVoice = () => {
    const [state, setState] = useState<VoiceState>('idle');
    const [transcript, setTranscript] = useState('');
    const recording = useRef<Audio.Recording | null>(null);
    
    const startRecording = async () => {
        // Permission mic
        const { status } = await Audio.requestPermissionsAsync();
        if (status !== 'granted') throw new Error('Mic permission denied');
        
        // Config audio
        await Audio.setAudioModeAsync({
            allowsRecordingIOS: true,
            playsInSilentModeIOS: true,
            interruptionModeAndroid: InterruptionModeAndroid.DuckOthers,
        });
        
        recording.current = new Audio.Recording();
        await recording.current.prepareToRecordAsync({
            android: {
                extension: '.m4a',
                outputFormat: Audio.AndroidOutputFormat.MPEG_4,
                audioEncoder: Audio.AndroidAudioEncoder.AAC,
                sampleRate: 16000,
                numberOfChannels: 1,
                bitRate: 24000,
            },
            ios: {
                extension: '.m4a',
                audioQuality: Audio.IOSAudioQuality.MEDIUM,
                sampleRate: 16000,
                numberOfChannels: 1,
                bitRate: 24000,
            },
            web: { mimeType: 'audio/webm', bitsPerSecond: 24000 },
        });
        
        await recording.current.startAsync();
        setState('recording');
    };
    
    const stopRecording = async () => {
        if (!recording.current) return;
        
        await recording.current.stopAndUnloadAsync();
        const uri = recording.current.getURI();
        
        setState('processing');
        
        // Upload vers /v1/chat/audio avec streaming
        const response = await uploadAudio(uri, { ... });
        
        // Playback audio réponse
        await playAudioStream(response);
        
        setState('idle');
    };
    
    const cancelRecording = async () => {
        if (recording.current) {
            await recording.current.stopAndUnloadAsync();
        }
        setState('idle');
    };
    
    return { state, transcript, startRecording, stopRecording, cancelRecording };
};
```

### 12.2 — Composant FAB micro flottant

**Fichier** : `mobile/components/MicFAB.tsx`

- Position : bottom-right, toujours visible
- Taille : 64×64px
- Couleur : primaire app
- Animation : pulsation pendant recording
- Haptic feedback : vibration légère au press/release

### 12.3 — Playback audio streaming

Utilise `Audio.Sound` avec `progressiveDownload: true` pour commencer playback dès 1er chunk reçu.

### 12.4 — Gestion de l'état audio

3 états :
- `idle` : attente
- `recording` : micro actif
- `processing` : upload + LLM + TTS en cours
- `speaking` : playback réponse

---

## 13. LATENCE — BUDGET ET OPTIMISATIONS

### 13.1 — Budget latence détaillé

**Cible** : **<1.5s** entre fin de parole et début de réponse vocale.

**Décomposition** :

| Étape | Budget | Tech |
|---|---|---|
| Finalisation recording | 50ms | VAD côté mobile |
| Upload audio (3s compressé) | 200ms | HTTPS |
| STT Whisper (3s audio) | 600ms | API OpenAI |
| Fetch context (Mem0 + MemPalace) | 100ms | Redis + Postgres |
| LLM 1er chunk (Claude streaming) | 400ms | Claude API |
| TTS 1er chunk (ElevenLabs WS) | 250ms | ElevenLabs |
| Download 1er chunk audio | 100ms | HTTP chunked |
| **Total** | **~1.7s** | ⚠️ Serré mais OK |

**Optimisations pour descendre à ~1.4s** :
- Pre-warm connection ElevenLabs WS (keep-alive)
- Cache Mem0 dans Redis (skip 100ms)
- Prompt caching Claude (skip 150ms système prompt)

### 13.2 — Monitoring latence

**Métriques P95 à surveiller** :
- `voice.stt_latency_ms` — P95 < 1000ms
- `voice.llm_first_chunk_ms` — P95 < 700ms
- `voice.tts_first_chunk_ms` — P95 < 500ms
- `voice.total_e2e_latency_ms` — P95 < 2000ms

Alerte Sentry si P95 > 2× cible.

### 13.3 — Pas de streaming sur plan Starter

Pour économiser les coûts :
- **Starter** : réponse vocale NON-streaming (attendre fin LLM puis générer TTS batch)
- Latence ~3-4s acceptable en freemium
- Seuls Pro/Business ont le streaming complet <1.5s

---

## 14. COÛTS ET OPTIMISATIONS ÉCONOMIQUES

### 14.1 — Coûts par artisan/mois (voir `COUT_REEL.md`)

| Service | Usage typique | Coût mensuel artisan |
|---|---|---|
| Whisper STT | 30 vocaux × 20s = 10 min | **~$0.06** |
| ElevenLabs TTS | 30 réponses × 200 chars = 6K chars | **~$0.72** |
| **TOTAL vocal / artisan** | | **~$0.78/mois** |

### 14.2 — Optimisations TTS (critique)

**PIÈGE ElevenLabs** (voir `COUT_REEL.md`) : avec 50 artisans × 30 réponses/jour = **450K chars/mois** = **$54/mois** juste pour le TTS.

**Protections** :

1. **Cache Redis** — réponses récurrentes → **-20% coût**
2. **Context compaction** — texte plus court → **-15% chars**
3. **Option fallback OpenAI TTS** en settings Starter → **-87% coût TTS**
4. **Cap quotidien** — max X minutes TTS/jour par plan

### 14.3 — Plan de coûts par plan tarifaire

| Plan | STT | TTS | Total voix |
|---|---|---|---|
| Starter (€0) | Whisper | OpenAI (standard) | ~$0.20/mois |
| Pro (€29) | Whisper | ElevenLabs | ~$0.78/mois |
| Business (€79) | Whisper | ElevenLabs | ~$1.20/mois (plus d'usage) |

### 14.4 — Coûts variables Whisper

Whisper = cheap ($0.006/min). Pas d'optim critique requise, mais :
- VAD strict côté mobile → pas de silence envoyé
- Durée max 3 min → pas de monologues

### 14.5 — Projection 500 clients

| Poste | Coût mensuel |
|---|---|
| Whisper STT | ~$15 |
| ElevenLabs TTS (avec optims) | ~$330 |
| **Total vocal** | **~$345/mois** (sur $1 830 total, ~19%) |

---

## 15. GARDE-FOUS ANTI-ABUS

### 15.1 — Validation côté client

Avant envoi audio :
- Durée minimum : **1s** (anti-tap accidentel)
- Durée maximum : **180s** (3 min, anti-monologue)
- Taille minimum : **500 bytes** (anti-fichier vide)
- Taille maximum : **10 MB**

Si invalide → rejet local, pas d'appel API.

### 15.2 — Rate limits vocaux

Par artisan :
- **Starter** : 20 vocaux/jour max
- **Pro** : 100 vocaux/jour max
- **Business** : 300 vocaux/jour max

Au-delà → message "Limite quotidienne vocale atteinte" + bouton "Continuer en texte".

### 15.3 — Cap budget LLM quotidien

Voir `docs/SINGLE_SOURCE_OF_TRUTH.md` §4 — caps par plan ($0.50 / $2 / $5 quotidien). Si dépassé, vocal bascule en **mode texte only** (pas de TTS).

### 15.4 — Détection abus

**Pattern** : 5+ vocaux de 3 min consécutifs → alerte Fabrice, possible usage agentique non autorisé.

### 15.5 — Interdiction vocal sandbox publique

La sandbox `/v1/public/chat` (F115) ne supporte **pas** le vocal (texte uniquement) — protection contre abus coûteux.

---

## 16. ACCESSIBILITÉ ET TRANSPARENCE AI ACT

### 16.1 — Transparence Art. 50 AI Act

Voir `docs/AI-ACT-COMPLIANCE.md` §6.3.

**Script obligatoire** au premier appel de l'Agent Téléphone V2 :

> *"Bonjour, vous êtes bien chez [Nom Entreprise]. [Prénom] est actuellement indisponible. **Je suis son assistant IA**, je peux prendre votre demande et il vous rappellera dans l'heure. Vous souhaitez continuer ?"*

### 16.2 — Marquage audio généré

Selon Art. 50.2 AI Act : les providers d'AI générative doivent marquer le contenu synthétique.

**STRUCTORAI** :
- Métadonnées MP3 : `TXXX:AI_Generated=STRUCTORAI_v1.0`
- Pas de marqueur audible (dégraderait UX)
- Transparence via page `/transparency`

### 16.3 — Accessibilité (handicap)

- **Sourds/malentendants** : transcription texte toujours disponible en parallèle
- **Muets** : clavier disponible en alternative
- **Difficultés motrices** : hotword V1.5 permettra mains libres totales

### 16.4 — Contrôle utilisateur

- Bouton "Couper la voix" accessible partout
- Désactivation TTS dans settings
- Réglage vitesse de parole
- Choix voix homme/femme respecté

---

## 17. TESTS ET MÉTRIQUES

### 17.1 — Tests unitaires

**Fichier** : `backend/tests/test_voice_service.py`

- Test STT Whisper mocké (fixture audio)
- Test fallback Deepgram
- Test TTS ElevenLabs mocké
- Test fallback OpenAI TTS
- Test préprocessing texte
- Test cache Redis
- Test validation audio size/duration

### 17.2 — Tests d'intégration

**Test E2E** :
- Upload audio réel FR → transcription → LLM → TTS → audio
- Mesurer latence bout-en-bout
- Vérifier format audio retour

### 17.3 — Tests de charge

Pre-launch :
- 100 vocaux simultanés → vérifier latences P95 < 2s
- Stress test fallback chain (Whisper OFF → vérifier Deepgram actif)

### 17.4 — Métriques production

Voir §13.2. Dashboard Grafana/Sentry.

### 17.5 — Tests qualité subjective

Panel de beta-testeurs artisans :
- Évaluation qualité voix (1-5)
- Compréhension réponses (1-5)
- Latence perçue (1-5)

---

## 18. TROUBLESHOOTING

### 18.1 — Problèmes fréquents

| Symptôme | Cause probable | Solution |
|---|---|---|
| "Le cerveau ne comprend pas ce que je dis" | Bruit chantier + mic éloigné | Rapprocher téléphone de la bouche, réduire bruit si possible |
| Réponse vocale tronquée | Réseau lent, chunks perdus | Vérifier signal 4G/WiFi, réessayer |
| Voix robotique | Fallback OpenAI TTS actif (ElevenLabs down) | Attendre retour ElevenLabs ou accepter |
| Pas de voix du tout | Permission audio refusée | Settings mobile → activer audio pour app |
| Latence >3s | Plan Starter (pas streaming) | Upgrade Pro pour streaming <1.5s |
| Mot BTP mal transcrit | Whisper weak sur jargon | Dictionnaire BTP (`data/btp_terminology_*.json`) |

### 18.2 — Log analysis

Logs Sentry + logs Railway pour tracer :
- `trace_id` d'une requête vocal
- Vérifier STT provider / TTS provider utilisé
- Vérifier latence par étape

### 18.3 — Reset pipeline vocal

Si pipeline vocal bloqué :
- Côté user : relancer l'app (force kill + relaunch)
- Côté backend : redémarrer `voice_service` via Railway
- Cache Redis voice : `FLUSHDB` si corruption

---

## 19. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/ARCH.md` | Architecture globale — voice_service dans §4 Gateway |
| `docs/API.md` | Endpoint `/v1/chat/audio` §7.2 |
| `docs/AGENTS.md` | Quels agents utilisent le vocal |
| `docs/AI-ACT-COMPLIANCE.md` | §6.3 transparence vocale |
| `docs/I18N.md` (à créer) | 6 langues détaillées |
| `docs/ENV.md` (à créer) | `OPENAI_API_KEY`, `DEEPGRAM_API_KEY`, `ELEVENLABS_API_KEY` |
| `docs/ERRORS.md` (à créer) | Codes `STT_*`, `TTS_*` |
| `docs/OFFLINE.md` (à créer) | Vocal en mode hors-ligne (Web Speech API fallback) |
| `docs/TESTS.md` (à créer) | Tests vocaux |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | §9 modèles, §14 langues, §4 budget LLM |
| `COUT_REEL.md` | Coûts Whisper + ElevenLabs + pièges |
| `BUILD_PLAN.md` | Sprint 1 voice pipeline |
| `PRODUCT_CONTEXT.md` | Vision vocal (canal 1 chat FAB) |

### 19.1 — Références externes

- **OpenAI Whisper API** : https://platform.openai.com/docs/guides/speech-to-text
- **OpenAI TTS API** : https://platform.openai.com/docs/guides/text-to-speech
- **ElevenLabs Streaming** : https://elevenlabs.io/docs/api-reference/text-to-speech-websockets
- **ElevenLabs Multilingual v2** : https://elevenlabs.io/docs/speech-synthesis/models
- **Deepgram Nova-2** : https://developers.deepgram.com/docs/models-languages-overview
- **Expo Audio** : https://docs.expo.dev/versions/latest/sdk/audio/
- **Web Speech API** : https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API

---

> **Ce fichier est la spec complète du pipeline vocal.**
> **Latence cible non négociable** : <1.5s entre fin de parole et début de réponse.
> **Règle d'or** : le vocal doit fonctionner sur chantier bruyant, sinon ça ne sert à rien.
> **Différenciation n°1** : aucun concurrent BTP ne fait le vocal bidirectionnel. C'est notre moat.
