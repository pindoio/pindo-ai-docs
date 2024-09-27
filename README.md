## Overview
This API provides endpoints for converting speech to text (STT), text to speech (TTS), and extracting named entities from text (NER). The service is designed to handle audio processing, text generation, and entity extraction with various configurable options.

## Prerequisites
- Ensure that the audio files and text inputs meet the required specifications as outlined in the schemas.
- The service enforces rate limits per IP to prevent abuse.

## Languages Supported

The Pindo VoiceAI API currently supports three languages: Kinyarwanda, Kiswahili, and Luganda. These languages can be specified using their corresponding ISO 639-1 language codes in the API requests. The API expects the language code to be included as part of the request to identify the language of the audio file being processed.

Here are the currently supported languages along with their respective ISO 639-1 codes:

- Kinyarwanda: `"rw"`
- Kiswahili: `"sw"`
- Luganda: `"lg"`

## Endpoints

### 1. Speech-to-Text (STT)
Converts spoken language in an audio file into written text. 

- **URL**: `/stt`
- **Method**: `POST`
- **Headers**: `Content-Type: multipart/form-data`
- **Schema**:
  - `audio` (string, required): The audio file to be transcribed. (Required)
  - `lang` (string, optional): The language of the audio. Supports values like "rw" (Kinyarwanda). Defaults to "rw".

- **Example Request**:
### Python
  ```python
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
```
### Curl
```bash
curl -X POST "https://api.pindo.io/v1/transcription/stt" \
     -F "audio=@/path/to/your/file_name.mp3" \
     -F "lang=rw"
```
### JavaScript
```javascript
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
```
- **Example Response**:
```json
  {
      "text": "...",
      "uploaded_audio_url": "path/file_name.mp3"
  }
```


### 2. Text-to-Speech (TTS)

The Pindo VoiceAI API offers Text-to-Speech (TTS) capabilities, allowing you to generate high quality audio speech from text in supported languages. With TTS, you can input text, and the API will convert it into natural-sounding speech. The API currently supports Kinyarwanda only.

- **Endpoint**: `/tts`
- **Method**: `POST`
- **Headers**: `Content-Type: application/json`
- **Request Parameters**:
  - `text` (string, required): Text to convert to speech.
  - `lang` (string, required): Language for the speech (e.g., "rw" for Kinyarwanda).
  - `audio` (file, optional): Sample audio file.
  - `speech_rate` (float, optional): Rate of speech, default is 1.0.

- **Example Request**:
 ### python
  ```python
  import requests

  url = "https://api.pindo.io/v1/transcription/tts"
  data = {
      "text": "Muraho neza!",
      "lang": "rw",
      "speech_rate": 1.0
  }
  response = requests.post(url, json=data)
```
### curl
```script
  curl -X POST "https://api.pindo.io/v1/transcription/tts" \
       -H "Content-Type: application/json" \
       -d '{"text": "Muraho neza!", "lang": "rw", "speech_rate": 1.0}'
```
### JavaScript
```javascript
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
```
- **Example Response**:
  ```json
    {
        "generated_audio_url": "path/file_name.wav"
    }
  ```


### 3. Named Entity Recognition (NER)

The Pindo VoiceAI API includes Named Entity Recognition (NER) functionality, which identifies and classifies named entities in text. NER allows you to automatically detect entities such as names of people, organizations, locations, dates, and other specific categories from textual data. The supported languages for NER are Kinyarwanda, and English.

- **Endpoint**: `/ner`
- **Method**: `POST`
- **Headers**: `Content-Type: application/json`
- **Request Parameters**:
  - `text` (string, required): Text for entity extraction.
  - `lang` (string, required): Language of the text (e.g., "en" for English).
  - `labels` (list of strings, optional): Specific labels to search for. You can specify one or more of an arbitrary list of entity keywords you want to extract from a given text. Some example of entities could be: "person", "location", "organisation", "date", "time", "money", "percent", "quantity", "ordinal", "cardinal". If no labels are specified, the API will default to `["person", "location", "organisation", "date"]`.

- **Example Request**:
### Python
  ```python
  import requests

  url = "https://api.pindo.io/v1/transcription/ner"
  data = {
      "text": "Yohani ukorera minisante atuye i musanze.",
      "lang": "rw",
      "labels": ["person", "location", "organisation"]
  }
  response = requests.post(url, json=data)
 ``` 
### curl
```script
  curl -X POST "https://api.pindo.io/v1/transcription/ner" \
       -H "Content-Type: application/json" \
       -d '{
          "text": "Yohani ukorera minisante atuye i musanze.",
          "lang": "rw",
          "labels": ["person", "location", "organisation"]
        }'
```
### JavaScript
```javascript
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
```
- **Example Response**:
  ```json
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
| `400`        | **Bad Request**: The request was invalid or malformed. This includes cases where the file size or audio file is too large. |
| `429`        | **Too Many Requests**: Daily limit of AI model requests per IP address has been reached. |
| `500`        | **Internal Server Error**: An unexpected error occurred on the server. |
