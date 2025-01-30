# fastapi-Email
Install dependencies via
```plaintext
pip install fastapi uvicorn pydantic
```
#Approach 1(Brute force,Simple leass secure)

Sending mail via fastapi
```plaintext
from fastapi import FastAPI, BackgroundTasks
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.mime.application import MIMEApplication
from pydantic import BaseModel

app = FastAPI()

class EmailRequest(BaseModel):
    to_email: str
    subject: str
    body: str

# Configuration for Gmail (you can use your own SMTP server settings)
SMTP_SERVER = "smtp.gmail.com"
SMTP_PORT = 587 #port for email sending recommended
SENDER_EMAIL = "your-email@gmail.com"
SENDER_PASSWORD = "dzkm dyor upcc ksue"  # Use google setting->security->enable 2FA->in search bar search "App password"->give any name & press create->copy 16 digited password & paste here

def send_email(to_email: str, subject: str, body: str):
    # Create the email message
    message = MIMEMultipart()
    message["From"] = SENDER_EMAIL
    message["To"] = to_email
    message["Subject"] = subject
    
    # Attach the body of the email
    message.attach(MIMEText(body, "plain"))

    # Connect to the SMTP server and send the email
    try:
        with smtplib.SMTP(SMTP_SERVER, SMTP_PORT) as server:
            server.starttls()  # Secure the connection
            server.login(SENDER_EMAIL, SENDER_PASSWORD)
            server.sendmail(SENDER_EMAIL, to_email, message.as_string())
            print(f"Email sent to {to_email}")
    except Exception as e:
        print(f"Error sending email: {e}")

@app.post("/send-email/")
async def send_email_background(email: EmailRequest, background_tasks: BackgroundTasks):
    # Send email in the background
    background_tasks.add_task(send_email, email.to_email, email.subject, email.body)
    return {"message": "Email sent in background!"}
```
