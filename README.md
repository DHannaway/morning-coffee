# The Morning Coffee — daily QR audio briefing

Live player: https://dhannaway.github.io/morning-coffee/  
Repository: https://github.com/DHannaway/morning-coffee  
Edit: [briefing_settings.yaml](briefing_settings.yaml)

An automated audio briefing for an Edinburgh listener. The QR URL stays fixed
while GitHub Actions generates and publishes a fresh recording daily.

## How news now reaches the script

1. Collect recent RSS leads from BBC UK, Scotland, World and Technology,
   TechCrunch AI, BleepingComputer and SecurityWeek, plus enabled optional feeds. RSS titles,
   summaries and timestamps are **discovery leads only**.
2. **GPT-5.4 mini** shortlists up to 24 promising leads, preserving a spread of
   Scottish, UK, international, technology and cybersecurity candidates.
3. Concurrently download the selected public HTML articles. **Trafilatura**
   extracts main-body reporting. Inaccessible, truncated, non-HTML, extremely
   short, paywalled or consent-gated pages are excluded from the evidence pool.
   Each usable article supplies up to 6,500 characters of actual reporting.
   If fewer than four complete articles can be read, abort rather than invent
   a briefing from headlines. Previously published audio remains on the site.
4. **GPT-5.4** writes the script using *only* retrieved article bodies and the
   structured Open-Meteo weather forecast as current evidence. It is instructed
   to use multiple concrete details for each story, distinguish fact from
   commentary, and avoid generic filler and unsupported conclusions.
   A programmatic check rejects ungrounded source lists, excessively short
   scripts and the explicitly forbidden phrase "closer to home".
5. The existing **gpt-4o-mini-tts** turns sentence-chunked text into audio,
   FFmpeg concatenates it, and GitHub Pages serves the player, transcript and
   links to the cited articles. TTS migration is needed before its scheduled
   January 2027 retirement.

The JSON metadata includes research coverage counts so failures or thin source
coverage can be diagnosed. The article bodies are used at generation time;
they are **not published** or copied into the player.

## Editorial and geographical boundaries

- Edinburgh is the local reference point. Scottish and UK reporting should lead
  when materially important; South Korea and other countries are **world news**,
  not "closer to home".
- Prefer detailed coverage of several worthwhile stories over headline recitation.
  Do not generate length-filling reflections about how an event "underlines
  the importance" of something.
- A technical concept is included only where genuinely linked to a reported
  AI or cybersecurity story.
- Preserve nuance on allegations, event dates, forecasts and uncertainties.

These guardrails live in `generate.py`, and ordinary editorial preferences
remain editable in `briefing_settings.yaml`. The current settings are validated
with `python generate.py --check-settings`.

## Configuration and automation

Settings support duration (5–10 minutes), style, explanation depth,
news-section switches, general/cyber story targets, voice and direction,
editorial guidance and optional HTTPS feeds.

- `generate.py`: weather/RSS discovery, evidence-based writing, audio and JSON.
- `article_research.py`: candidate shortlist, article fetching/extraction.
- `requirements.txt`: Python runtime dependencies.
- `.github/workflows/publish.yml`: scheduled/manual publishing and model IDs.
- `public/index.html`: the QR player; URL must not change.

The workflow schedules staggered Europe/London runs to improve resilience and
skips automatically if today's edition is already live; **manual Run workflow**
forces a new edition. Actions is best-effort, not a guaranteed 07:30 service.

### Testing

```bash
python -m py_compile generate.py article_research.py
python generate.py --check-settings
```

A complete generation additionally requires network access, an `OPENAI_API_KEY`
repository secret, FFmpeg and API access to the selected models. Source sites
may refuse automated access; the pipeline fails closed rather than using
unsupported headlines. Follow the GitHub Actions run logs when troubleshooting.

## Security and cost

The GitHub Pages player, transcripts, audio and linked sources are public.
The OpenAI key stays in the Actions repository secret. The ChatGPT subscription
does not include this API usage. Moving from a headline-only mini-model pipeline
to full-article GPT-5.4 research adds API usage and article-download time.
Adjust models in the Actions workflow if needed, and inspect OpenAI usage in
the API billing console. The QR link is unchanged.

The TTS model is still `gpt-4o-mini-tts`; changing script models does not
change the narration voice.
