# Snap & Study

A Streamlit chat app where a student enters their name and WhatsApp number
once, then chats with an AI study buddy - typing a question or attaching a
photo of notes, a textbook page, or a problem - and gets an explanation,
a key-points summary, and a practice question. One button quizzes them on
what they just studied; another sends a full recap of the session straight
to their WhatsApp.

Built with Gemini (chat + vision) and Twilio (WhatsApp). Same architecture
as the MacroSnap workshop project, re-skinned for studying.

## Project structure

snap-study/
├── app.py                        # the app itself
├── prompts.py                    # the AI's personality, kept separate
├── requirements.txt              # dependencies
├── .gitignore                    # keeps secrets.toml out of GitHub
└── .streamlit/
    └── secrets.toml.example      # template - copy to secrets.toml and fill in

## Setup

1. Create a virtual environment and activate it:
   python -m venv venv
   source venv/bin/activate        # Windows: .\venv\Scripts\Activate.ps1
2. Install dependencies:
   pip install -r requirements.txt
3. Get a Gemini API key from Google AI Studio (aistudio.google.com → Get API key).
4. Sign up free at twilio.com/try-twilio. From the Console copy your
   Account SID and Auth Token.
5. Under Messaging → Try it out → Send a WhatsApp message, find your sandbox
   number and join code (e.g. "join happy-tiger"). From the WhatsApp number
   you will test with, send that join message to the sandbox number - this
   opt-in is required and expires after ~72 hours of inactivity.
6. In the Twilio Console, go to Messaging → Content Template Builder and
   create a Text template with one variable for the recipient's name and one
   for the summary, e.g.:

       Hi {{1}}, here's your Snap & Study recap:\n\n{{2}}

   Note its Content SID (starts with HX...). A Content Template is required
   because WhatsApp only allows free-form replies within a 24-hour window
   opened by the customer; the app's recap button sends a business-initiated
   message, which needs an approved template.
7. Copy .streamlit/secrets.toml.example to .streamlit/secrets.toml and fill
   in your real values. Never commit secrets.toml.

## Run locally

    streamlit run app.py

Opens at http://localhost:8501. Onboard with the WhatsApp number that joined
your sandbox, ask a question or attach a photo, then try the Quiz me and
Send to WhatsApp buttons.

## Deploy - Streamlit Community Cloud

1. Push the project to a GitHub repo. Do not commit .streamlit/secrets.toml -
   only secrets.toml.example should be tracked.
2. Go to share.streamlit.io and sign in with GitHub.
3. Click "New app", pick the repo, branch, and app.py as the entry point.
4. In the app's Settings → Secrets panel, paste the same content your local
   secrets.toml has.
5. Deploy - you get a public URL, no separate server to manage.

## How it differs from MacroSnap

- Personality and prompts in prompts.py are study-focused: explain, summarize,
  quiz. Off-topic questions are politely steered back to studying.
- A bare photo gets the instruction "Summarize the key points and give me one
  practice question on it."
- Added a "Quiz me" header button next to "Send to WhatsApp". It reuses the
  same ask_gemini() path but sends a hidden QUIZ_REQUEST_PROMPT, so the quiz
  is generated from the live conversation - no new chat session needed.
- The WhatsApp summary is a study recap: topics covered, key takeaways, and a
  "revise these" section for anything difficult.
