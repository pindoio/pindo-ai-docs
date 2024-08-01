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
  from io import BytesIO

  url = "https://api.pindo.io/v1/transcription/stt"
  data = {
      "lang": "rw"
  }

  # Path to your audio file
  # Supported audio formats
  # "wav", "wave", "mp3", "ogg", "flac", "aac", "wma", "webm", "mp4", "m4a"
  audio_path = "path/to/your/file.mp3"

  # Read the audio file into BytesIO
  with open(audio_path, 'rb') as audio_file:
      audio_content = audio_file.read()

  audio_file_io = BytesIO(audio_content)

  files = {
      'audio': ('file.mp3', audio_file_io, 'audio/mp3')  # Adjust MIME type if necessary
  }

  # Send the POST request
  response = requests.post(url, files=files, data=data)

  ### Curl
  curl -X POST "https://api.pindo.io/v1/transcription/stt" \
       -F "audio=@/path/to/your/file_name.mp3" \
       -F "lang=rw"

  ### JavaScript
  const FormData = require('form-data');
  const fs = require('fs');
  const axios = require('axios');

  const url = "https://api.pindo.io/v1/transcription/stt";
  const form = new FormData();
  form.append('audio', fs.createReadStream('path/to/your/file.mp3'));
  form.append('lang', 'rw');

  axios.post(url, form, {
      headers: form.getHeaders()
  }).then(response => {
      console.log(response.data);
  }).catch(error => {
      console.error(error);
  });


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
  ### python
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
       -d '{"text": "Muraho neza!", "lang": "rw", "speech_rate": 1.0}'

  ### JavaScript
  const axios = require('axios');

  const url = "https://api.pindo.io/v1/transcription/tts";
  const data = {
      text: "Muraho neza!",
      lang: "rw",
      speech_rate: 1.0
  };

  axios.post(url, data, {
      headers: { 'Content-Type': 'application/json' }
  }).then(response => {
      console.log(response.data);
  }).catch(error => {
      console.error(error);
  });

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

  ### JavaScript
    const axios = require('axios');

    const url = "https://api.pindo.io/v1/transcription/ner";
    const data = {
        text: "Yohani ukorera minisante atuye i musanze.",
        lang: "rw",
        labels: ["person", "location", "organisation"]
    };

    axios.post(url, data, {
        headers: { 'Content-Type': 'application/json' }
    }).then(response => {
        console.log(response.data);
    }).catch(error => {
        console.error(error);
    });

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


## Status Codes

| Status Code | Description                                             |
|--------------|---------------------------------------------------------|
| `200`        | **OK**: The request was successful, and the response contains the requested data. |
| `400`        | **Bad Request**: The request was invalid or malformed. This could be due to missing or incorrect parameters. |
| `401`        | **Unauthorized**: Authentication credentials are missing or incorrect. |
| `403`        | **Forbidden**: The user does not have permission to access the requested resource. |
| `404`        | **Not Found**: The requested resource could not be found. |
| `413`        | **Payload Too Large**: The uploaded audio file exceeds the allowed size limit. |
| `429`        | **Too Many Requests**: Daily limit of AI model requests per IP address has been reached. |
| `500`        | **Internal Server Error**: An unexpected error occurred on the server. |
