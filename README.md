# 🎤 Ghostword AI

<p align="center">
  <img src="URL_TO_YOUR_GHOSTWORD_GRAPHIC.png" alt="Ghostword Banner" width="600"/>
</p>

<p align="center">
  <em>A real-time AI voice assistant that helps you find the right word when you get stuck during a conversation or public speaking.</em>
</p>

<p align="center">
  <img alt="Python Version" src="https://img.shields.io/badge/python-3.9+-blue.svg">
  <img alt="License" src="https://img.shields.io/badge/license-MIT-green.svg">
</p>

---

## 🌟 Introduction

Have you ever been in the middle of a presentation or a crucial conversation, only to have your mind go blank while searching for the next word? **Ghostword** is an AI-powered agent built to solve this exact problem.

Ghostword actively listens to a speaker and detects unusually long pauses or hesitations that signal word-finding difficulty. Using the context of the conversation, it intelligently suggests appropriate words or phrases and plays them back as audio, helping the speaker maintain fluency and confidence.

This project is a complete end-to-end system, integrating a local Python listener with a cloud-based AI and automation workflow.

## ✨ Key Features

- **Real-time Listening:** Actively listens to the user's microphone for speech and pauses.
- **Contextual Awareness:** Uses the transcribed text of the ongoing conversation as context.
- **AI-Powered Suggestions:** Leverages Google Gemini to generate relevant word and phrase suggestions.
- **Audio Feedback Loop:** Converts suggestions to speech using Google Text-to-Speech and plays them back to the user.
- **Configurable Sensitivity:** Adjustable pause and phrase length thresholds to suit different speaking styles.

## ⚙️ How It Works (Architecture)

Ghostword's architecture is a hybrid of a local client and a cloud-based backend orchestrated by [Make.com](https://www.make.com/).

1.  **Local Listener (`ghostword_listener.py`):**
    -   A Python script running on the user's machine accesses the microphone.
    -   It uses the `SpeechRecognition` library to capture audio and transcribe it to text after detecting a significant pause (defined by `PAUSE_THRESHOLD`).
    -   The transcribed text is sent to a webhook in the main Make.com scenario.

2.  **Make.com Main Scenario (Cloud Workflow):**
    -   A webhook receives the text from the Python script.
    -   The text is passed to a **Google Gemini** module, which generates word suggestions based on the context.
    -   The suggestions are sent to a **Google Cloud Text-to-Speech** module, which creates an audio file (`.mp3`).
    -   This audio file is uploaded to a cloud storage service (e.g., Google Drive).
    -   A shareable, direct-download URL for the audio file is generated.
    -   This URL is saved to a **Make.com Data Store** using a fixed key (`latest_audio_url`).

3.  **Make.com "URL Server" Scenario (API Endpoint):**
    -   A second, simple Make.com scenario acts as a micro-API.
    -   When its webhook is called by the Python script, it retrieves the URL from the Data Store.
    -   It sends the URL back as a webhook response and then deletes the record from the Data Store to prevent re-use.

4.  **Local Playback (Python Script):**
    -   After sending the text, the Python script enters a polling loop, repeatedly calling the "URL Server" webhook.
    -   Once it receives the audio URL, it downloads the `.mp3` file, saves it temporarily, and plays it through the user's speakers using the `playsound` library.

## 🛠️ Tech Stack

-   **Local Client:** Python
    -   `SpeechRecognition`: For microphone input and STT.
    -   `requests`: For interacting with Make.com webhooks.
    -   `playsound`: For audio playback.
-   **Automation Platform:** [Make.com](https://www.make.com/)
-   **AI & Cloud Services:** Google Cloud Platform
    -   **AI Model:** Google Gemini
    -   **Speech-to-Text:** Google Web Speech API (via Python library)
    -   **Text-to-Speech:** Google Cloud Text-to-Speech API
-   **Cloud Storage:** Google Drive (or any other Make.com-supported service like Dropbox)

## 🚀 Setup and Installation

To get Ghostword running, you need to set up both the local Python script and the cloud workflows.

### Prerequisites

-   Python 3.9+ installed.
-   A Make.com account.
-   A Google Cloud Platform account with a project set up.
-   A Google Drive (or Dropbox) account.

### 1. Local Setup

Clone or download this repository to your local machine.

```bash
git clone [https://github.com/YOUR_USERNAME/ghostword.git](https://github.com/YOUR_USERNAME/ghostword.git)
cd ghostword
