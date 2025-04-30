# Voice Assistant Project

import pyttsx3
import speech_recognition as sr
import pyautogui
import webbrowser
import datetime
import os
import pywhatkit as kit
import time

# Initialize the speech engine
engine = pyttsx3.init()
engine.setProperty('rate', 150)  # Speed of speech
engine.setProperty('volume', 1)  # Volume level (0.0 to 1.0)

def speak(text):
    """Convert text to speech."""
    engine.say(text)
    engine.runAndWait()

def listen():
    """Listen to the user's command and return it."""
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        print("Listening...")
        recognizer.adjust_for_ambient_noise(source)
        audio = recognizer.listen(source)
    try:
        command = recognizer.recognize_google(audio)
        print(f"Command received: {command}")
        return command.lower()
    except sr.UnknownValueError:
        print("Sorry, I did not hear that.")
        return None
    except sr.RequestError:
        print("Sorry, the service is down.")
        return None

def open_app(app_name):
    """Open installed desktop applications."""
    apps = {
        "chrome": r"C:\Program Files\Google\Chrome\Application\chrome.exe",
        "firefox": r"C:\Program Files\Mozilla Firefox\firefox.exe",
        "notepad": r"C:\Windows\System32\notepad.exe",
        "whatsapp": r"C:\Users\YourUsername\AppData\Local\WhatsApp\WhatsApp.exe",
        "spotify": r"C:\Users\YourUsername\AppData\Roaming\Spotify\Spotify.exe"
    }
    
    app_path = apps.get(app_name)
    if app_path:
        speak(f"Opening {app_name}")
        os.startfile(app_path)
    else:
        speak(f"Sorry, I cannot find {app_name}. Please update my app list.")

def open_web_app(app_name):
    """Open websites in the browser."""
    web_apps = {
        "gmail": "https://mail.google.com",
        "whatsapp web": "https://web.whatsapp.com",
        "google drive": "https://drive.google.com",
        "facebook": "https://www.facebook.com",
        "twitter": "https://twitter.com",
        "youtube": "https://www.youtube.com",
        "instagram": "https://www.instagram.com"
    }
    
    app_url = web_apps.get(app_name)
    if app_url:
        speak(f"Opening {app_name} in your browser.")
        webbrowser.open(app_url)
    else:
        speak(f"Sorry, I can't find {app_name} in my list.")

def tell_time():
    """Tell the current time."""
    now = datetime.datetime.now()
    current_time = now.strftime("%H:%M")
    speak(f"The current time is {current_time}")

def tell_date():
    """Tell today's date."""
    today = datetime.date.today()
    speak(f"Today's date is {today.strftime('%B %d, %Y')}")

def greet():
    """Greet the user."""
    speak("Hello! How can I assist you today?")

def send_whatsapp_message(contact_number, message):
    """Send a WhatsApp message."""
    try:
        hour = datetime.datetime.now().hour
        minute = datetime.datetime.now().minute + 2  # 2 minutes later to adjust for processing time
        if minute >= 60:
            minute -= 60
            hour += 1
        speak(f"Sending your message shortly.")
        kit.sendwhatmsg(f"+{contact_number}", message, hour, minute)
        time.sleep(10)  # Allow enough time for pywhatkit to start the message
        pyautogui.press('enter')
        speak("Message sent successfully.")
    except Exception as e:
        print(e)
        speak("Sorry, I couldn't send the message. Please try again.")

def search_google(query):
    """Search Google for a query."""
    speak(f"Searching for {query} on Google.")
    webbrowser.open(f"https://www.google.com/search?q={query}")

def play_youtube_music(song_name):
    """Play a song on YouTube."""
    speak(f"Playing {song_name} on YouTube.")
    kit.playonyt(song_name)

def main():
    greet()
    while True:
        command = listen()
        if command:
            if "open chrome" in command:
                open_app("chrome")
            elif "open firefox" in command:
                open_app("firefox")
            elif "open notepad" in command:
                open_app("notepad")
            elif "open whatsapp" in command:
                open_app("whatsapp")
            elif "open spotify" in command:
                open_app("spotify")
            elif "open gmail" in command:
                open_web_app("gmail")
            elif "open whatsapp web" in command:
                open_web_app("whatsapp web")
            elif "open google drive" in command:
                open_web_app("google drive")
            elif "open facebook" in command:
                open_web_app("facebook")
            elif "open twitter" in command:
                open_web_app("twitter")
            elif "open youtube" in command:
                open_web_app("youtube")
            elif "open instagram" in command:
                open_web_app("instagram")
            elif "tell me the time" in command:
                tell_time()
            elif "tell me the date" in command:
                tell_date()
            elif "send whatsapp message" in command:
                speak("Please say the contact number with country code, without plus sign.")
                contact_number = listen()
                if contact_number:
                    speak("What is the message?")
                    message = listen()
                    if message:
                        send_whatsapp_message(contact_number, message)
            elif "search on google" in command:
                speak("What would you like to search?")
                query = listen()
                if query:
                    search_google(query)
            elif "play music" in command or "play song" in command:
                speak("Which song would you like to play?")
                song = listen()
                if song:
                    play_youtube_music(song)
            elif "exit" in command or "quit" in command:
                speak("Goodbye! Have a great day!")
                break
            else:
                speak("Sorry, I did not understand that. Please try again.")

if __name__ == "__main__":
    main()
