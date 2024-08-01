## Overview
This API provides endpoints for converting speech to text (STT), text to speech (TTS), and extracting named entities from text (NER). The service is designed to handle audio processing, text generation, and entity extraction with various configurable options.

## Prerequisites
- Ensure that the audio files and text inputs meet the required specifications as outlined in the schemas.
- The service enforces rate limits per IP to prevent abuse.

## Endpoints

### 1. Speech-to-Text (STT)
Converts spoken language in an audio file into written text.

- **URL**: `/stt`
- **Method**: `POST`
- **Headers**: `Content-Type: multipart/form-data`
- **Schema**:
  - `audio`: The audio file to be transcribed. (Required)
  - `lang`: The language of the audio. Supports values like "rw" (Kinyarwanda). (Optional)

- **Example Request**:
  ```python
  ### Python
  import requests

  url = "https://api.pindo.io/v1/transcription/stt"
  data = {
      "lang": "rw"
  }

  # Open the audio file in binary mode
  with open(path/file_name.mp3, 'rb') as audio_file:
      files = {
          'audio': (path/file_name.mp3, audio_file, audio_type(e.g: audio/m4a, audio/mp3)),
      }

      # Send the POST request
      response = requests.post(url, files=files, data=data)

  ### Curl
  curl -X POST "https://api.pindo.io/v1/transcription/stt" \
    -F "audio=@/path/to/your/file_name.mp3" \
    -F "lang=rw"


- **Example Response**:
  ```
    {
        "text": "...",
        "uploaded_audio_url": "path/file_name.mp3"
    }
  ```


### 2. Text-to-Speech (TTS)
Generates audio speech from text.

- **Endpoint**: `/tts`
- **Method**: `POST`
- **Headers**: `Content-Type: application/json`
- **Request Parameters**:
  - `text` (string, required): Text to convert to speech.
  - `lang` (string, required): Language for the speech (e.g., "rw" for Kinyarwanda).
  - `audio` (file, optional): Sample audio file.
  - `speech_rate` (float, optional): Rate of speech, default is 1.0.

- **Example Request**:
  ```python
  ### Python
  import requests

  url = "https://api.pindo.io/v1/transcription/tts"
  data = {
      "text": "Muraho neza!",
      "lang": "rw",
      "speech_rate": 1.0
  }
  response = requests.post(url, json=data)
  
  ### curl
  curl -X POST "https://api.pindo.io/v1/transcription/tts" \
  -H "Content-Type: application/json" \
  -d '{
        "text": "Muraho neza!",
        "lang": "rw",
        "speech_rate": 1.0
      }'

- **Example Response**:
  ```
    {
        "generated_audio_url": "path/file_name.wav"
    }
  ```


### 3. Named Entity Recognition (NER)
Identifies and classifies named entities in text.

- **Endpoint**: `/ner`
- **Method**: `POST`
- **Headers**: `Content-Type: application/json`
- **Request Parameters**:
  - `text` (string, required): Text for entity extraction.
  - `lang` (string, required): Language of the text (e.g., "en" for English).
  - `labels` (list of strings, optional): Specific labels to search for.

- **Example Request**:
  ```python
  ### Python
  import requests

  url = "https://api.pindo.io/v1/transcription/ner"
  data = {
      "text": "Yohani ukorera minisante atuye i musanze.",
      "lang": "rw",
      "labels": ["person", "location", "organisation"]
  }
  response = requests.post(url, json=data)
  
  ### curl
    curl -X POST "https://api.pindo.io/v1/transcription/ner" \
    -H "Content-Type: application/json" \
    -d '{
          "text": "Yohani ukorera minisante atuye i musanze.",
          "lang": "rw",
          "labels": ["person", "location", "organisation"]
        }'

- **Example Response**:
  ```
    {
        " location": [
            "musanze"
        ],
        " organisation": [
            "minisante"
        ],
        "person": [
            "Yohani"
        ]
    }
  ```

