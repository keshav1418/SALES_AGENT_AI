
AI Call – AI-Powered Voice Sales Assistant
An automated voice-calling application that helps explain courses, answer prospective students' questions, and guide them through booking a free demo session. It uses Python, Flask, and Twilio Programmable Voice, with a simple web dashboard for initiating calls.
Important: This repository contains calling functionality. Only call people who have given appropriate consent, follow applicable telemarketing and privacy laws, and provide a clear way to decline further calls.

Features
- Outbound voice calls: Start a call to a phone number through Twilio.
- Web dashboard: Enter a phone number and initiate a call from a browser.
- Interactive voice conversations: Uses Twilio speech gathering and text-to-speech to converse with the recipient.
- Course information and FAQs: Answers common questions about Python Full Stack and Java Full Stack courses, fees, schedules, projects, and career support.
- Demo booking flow: Guides students through course selection, demo-day and time selection, and collection of booking details.
- Booking records: Saves demo booking information in a local JSON file.
- Sales conversation support: Includes introduction, interest detection, objection handling, follow-up prompts, and closing messages.
- Simulation mode: Allows webhook testing without placing a real outbound call.
Technology Stack
- Python
- Flask
- Twilio Programmable Voice
- Twilio Voice Response / Gather (TwiML)
- Python dotenv
- HTML, CSS, and JavaScript
- JSON file storage
The dependency list also includes the google-generativeai package. The current application flow should be checked before assuming a Gemini model is actively used.
Project Structure
AI_CALL/
├── app.py                  # Flask application and voice conversation routes
├── call.py                 # Command-line outbound call utility
├── booking_system.py       # Demo booking and course/slot data
├── course_booking.py       # Course selection and booking conversation logic
├── knowledge_base.py       # Course information and FAQs
├── question_answerer.py    # Question detection and answer generation
├── sales_intelligence.py   # Sales conversation and objection-handling logic
├── requirements.txt        # Python dependencies
├── .env.example            # Environment variable template
├── .env                    # Local secrets; do not commit
├── student_bookings.json   # Local booking records (created/updated at runtime)
├── static/
│   ├── app.js              # Dashboard interactions
│   └── style.css           # Dashboard styles
└── templates/
    └── index.html          # Dashboard page
Requirements
- Python 3.10 or later recommended
- A Twilio account
- A Twilio phone number capable of making outbound voice calls
- A publicly accessible HTTPS URL for your Flask voice webhook (for example, a deployed server or a secure tunnel)
- A destination phone number in international format, such as +919876543210
Installation
1. Clone the repository
git clone https://github.com/YOUR_USERNAME/AI_CALL.git
cd AI_CALL
Replace YOUR_USERNAME with your GitHub username and adjust the repository name if needed.
2. Create and activate a virtual environment
Windows (PowerShell):
python -m venv venv
.\venv\Scripts\Activate.ps1
macOS / Linux:
python3 -m venv venv
source venv/bin/activate
3. Install dependencies
pip install -r requirements.txt
4. Configure environment variables
Create a .env file in the project root. Use .env.example as a template.
GEMINI_API_KEY=
GENIE_MODEL_NAME=

TWILIO_ACCOUNT_SID=your_twilio_account_sid
TWILIO_AUTH_TOKEN=your_twilio_auth_token
TWILIO_FROM_NUMBER=+1XXXXXXXXXX

STUDENT_PHONE_NUMBER=+919876543210
VOICE_WEBHOOK_URL=https://your-public-domain.example/voice

SIMULATE_MODE=true
Set the values using your own credentials:
Variable	Description
GEMINI_API_KEY	Gemini API key, if you enable Gemini functionality
GENIE_MODEL_NAME	Model name, if used by your implementation
TWILIO_ACCOUNT_SID	Twilio Account SID
TWILIO_AUTH_TOKEN	Twilio Auth Token
TWILIO_FROM_NUMBER	Your Twilio phone number
STUDENT_PHONE_NUMBER	Optional default destination number for call.py
VOICE_WEBHOOK_URL	Public HTTPS URL pointing to your /voice endpoint
SIMULATE_MODE	Set to true to use the simulation path; set to false for real calls


Keep credentials private. Never commit .env, API keys, Twilio tokens, or other secrets to GitHub. If a credential has been exposed, revoke or rotate it.
5. Expose the local Flask server
Twilio must be able to reach your voice webhook over the internet. For local development, use a secure tunneling service or deploy the application to a public HTTPS host.

For example, if your public tunnel URL is:
https://your-public-domain.example
Set:
VOICE_WEBHOOK_URL=https://your-public-domain.example/voice
Keep the tunnel running while testing. The URL must point to the /voice route, not just the dashboard.
Running the Application
Start the Flask server
From the project directory:
python app.py
Open the dashboard in your browser:
http://127.0.0.1:5000/dashboard
The root route (/) can also be used as a basic health check.
Start a call from the dashboard
1. Make sure the Flask server and public HTTPS tunnel are running.
2. Confirm your Twilio environment variables are configured.
3. Open /dashboard.
4. Enter the recipient's phone number in international format.
5. Click Call.
Start a call from the command line
python call.py +919876543210
If STUDENT_PHONE_NUMBER is set in .env, you can run:
python call.py
The script will prompt you to confirm or change the default number.
Run in simulation mode
Simulation mode invokes the configured webhook without creating a real outbound Twilio call.

Set this in .env:
SIMULATE_MODE=true
Or run:
python call.py +919876543210 --simulate
The Flask server and publicly reachable webhook must still be running for this test.
Voice Conversation Flow
The application uses these main routes:
Route	Method	Purpose
/	GET	Basic application health response
/dashboard	GET	Displays the call dashboard
/trigger_call	POST	Starts or simulates an outbound call
/voice	POST	Returns the initial TwiML voice response
/process	POST	Processes speech input and continues the conversation


A typical conversation follows this flow:
1. The assistant introduces itself and checks the student's interest.
2. It answers questions about courses, fees, schedules, placements, and projects.
3. It asks which course the student is interested in.
4. It offers a free demo session.
5. It collects the preferred day, time, and student details.
6. It records the booking and provides a confirmation message.
Course and Booking Data
Course details, demo days, available time slots, and FAQ responses are maintained in the Python modules:
- knowledge_base.py
- booking_system.py
- course_booking.py
Booking records are stored locally in student_bookings.json.

For production use, consider replacing the JSON file with a database such as PostgreSQL or SQLite. The current JSON approach is intended for a simple local prototype and is not suitable for concurrent, multi-worker production use without additional safeguards.
Troubleshooting
Twilio cannot reach the voice webhook
- Check that the Flask server is running.
- Check that your tunnel or deployment is active.
- Verify that VOICE_WEBHOOK_URL is a public HTTPS URL ending in /voice.
Missing Twilio configuration
- Confirm that .env exists in the project directory.
- Check that the required Twilio variables are populated.
- Restart the Flask server after changing environment variables.
Twilio trial account cannot call a number
- Trial accounts may only call verified destination numbers.
- Verify the destination number in your Twilio account or review your account's calling restrictions.
Dashboard reports a network error
- Confirm the Flask server is running.
- Open the dashboard from the Flask server's address.
- Check the browser developer console and Flask terminal for errors.
Speech is not recognized
- Check that the Twilio call reaches /voice and /process.
- Confirm the speech-gathering configuration and language settings.
- Test in a quiet environment and check Twilio call logs.
Security and Production Notes
Before deploying this project for real users:
- Keep .env and all credentials out of version control.
- Add authentication to the dashboard and protect the call-trigger endpoint.
- Validate and normalize destination phone numbers.
- Add rate limits and safeguards against unauthorized or repeated calls.
- Obtain consent and honor opt-out / do-not-call requests.
- Protect student names, phone numbers, and booking records.
- Use a database with appropriate access controls and backups.
- Add error handling, logging, monitoring, and webhook request validation.
- Review local laws and Twilio policies for automated calls and recording. Do not record calls unless you have met applicable consent requirements.
- Avoid exposing debug information or sensitive call details to users.
Contributing
Contributions are welcome.
1. Fork the repository.
2. Create a feature branch:
   git checkout -b feature/your-feature
3. Commit your changes:
   git commit -m "Add your feature"
4. Push your branch and open a Pull Request.
License
No license is currently specified. Add a LICENSE file before distributing or reusing this project publicly. Until then, do not assume that the code is released under an open-source license.
Built with Python, Flask, and Twilio.
