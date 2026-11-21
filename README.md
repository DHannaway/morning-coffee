# The Morning Coffee — QR-driven daily audio briefing

A working **starter implementation** of a fixed-address personal audio briefing: OpenAI writes a script from current RSS headlines and Open-Meteo weather, generates narrated audio, and publishes a player to GitHub Pages. The QR code will point to the **site URL**, which does not change from day to day.

**Status:** Not deployed. The frontend and automation are packaged here, but no live URL or finished QR code exists until you deploy the repository and run the workflow. The generator cannot be fully tested without your own OpenAI API access and network.

## Deployment (roughly 10–20 minutes of account setup)

1. Create a GitHub repository named `morning-coffee`. A public repository is simplest for free GitHub Pages. Do not upload sensitive personal data.
2. Upload **the contents of this directory** (including `.github/workflows/publish.yml`) to the repository's `main` branch.
3. Create a separate OpenAI **API** account / key in [the OpenAI platform](https://platform.openai.com/api-keys). ChatGPT subscriptions do **not** include API usage. API calls incur charges. Set a budget limit if your account supports one.
4. In the GitHub repository, select **Settings → Secrets and variables → Actions → New repository secret**, name it `OPENAI_API_KEY`, and paste your key. Do not paste your key into chat, code or a webpage.
5. Under **Settings → Pages**, choose **Build and deployment: GitHub Actions**.
6. Under **Actions**, select **Publish Morning Coffee** and choose **Run workflow** to generate the first edition. Check the job logs if it fails.
7. After a successful run, visit `https://YOUR_GITHUB_USERNAME.github.io/morning-coffee/` — the URL is now stable. It will display the latest date and a playable audio briefing. Test on your actual phone.
8. Generate and print **one QR code** linking to that exact deployed URL, not to ChatGPT. If you later migrate away from GitHub Pages, keep the same custom domain or add a redirect before moving it.

## Schedule and service levels

The workflow starts at **07:05 Europe/London every day** (including daylight-saving changes), leaving a 25-minute buffer for 07:30 coffee. GitHub scheduled workflows can be delayed or even skipped at times. This is best-effort, **not a guaranteed 07:30 SLA**. The previous published recording remains available if a new build fails, and the webpage clearly labels stale audio.

A new script and recording are generated every run. The source inputs are RSS headlines **and not full-text articles**; the prompt explicitly forbids inventing missing article details. For rigorously sourced long-form stories, integrate a licensed full-article research API or an agentic web search step in a future iteration.

## Costs, security and known limitations

- GitHub Pages hosting may be free on eligible repositories. OpenAI API **text and audio usage is billed separately** from ChatGPT, with daily recurring costs depending on selected models and input/output length.
- The published site and audio are accessible to anyone with the URL. The script and selected source links are also public. This is not a private authenticated player.
- The page requires **one tap on Play**. Mobile browsers restrict audio autoplay when opened through a QR code.
- The website is **powered by OpenAI's API**, but does not run inside the ChatGPT app. ChatGPT scheduled tasks cannot directly upload their output to this public web player.
- Audio uses `gpt-4o-mini-tts` for the initial implementation. **OpenAI's documentation says this endpoint's TTS models are scheduled to be retired on 6 January 2027.** Plan to migrate `render_audio()` to the supported realtime voice API or another maintained TTS provider well before that date. The webpage, URL and QR code will not need to change for the migration.
- RSS feeds can change, be throttled, or include incomplete descriptions. Failed content collection stops publication rather than fabricating the latest news. Feed headlines are untrusted text.
- The voice is AI generated and labeled as such on the page.

## Personalisation

Edit `build_script()` in `generate.py` to change the editorial instructions and the topics. Change `FEEDS` to adjust news sources. The local weather location is in `retrieve_weather()`. Change `OPENAI_TTS_VOICE` in the workflow to switch voice.

## Testing

Run `python -m py_compile generate.py` for syntax. Use `python -m http.server 8000 -d public` and visit `http://localhost:8000` for a preview of the webpage. Run `OPENAI_API_KEY=... python generate.py` only in a safe local shell or GitHub Actions, never in browser code.
