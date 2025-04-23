# Exxeta AI API Documentation



## Overview
Exxeta provides an AI API service compatible with OpenAI APIs and can be accessed via proxy as well as direct HTTP requests using tools such as `requests`. This documentation covers usage examples for general API interaction, embeddings, and structured output.


## OpenAI Compatible Proxy

### Setup

To utilize the Exxeta AI service using OpenAI Python SDK, set up your client with the appropriate **proxy URL** (https://ai.exxeta.info) and your **API-Key**.

### Swagger Documentation
For a more detailed API reference, visit the [Swagger documentation](https://ai.exxeta.info/docs).



```python
import openai

# Initialize OpenAI client
client = openai.OpenAI(
    api_key="your_api_key",
    base_url="https://ai.exxeta.info"  
)

# Create a chat completion request
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "user",
            "content": "this is a test request, write a short poem"
        }
    ]
)



print(response)
```

### Additional Information
Ensure your API key is correctly set in the api_key parameter.
The base_url must point to the Exxeta proxy URL **https://ai.exxeta.info.**

## Embeddings
### Example in Python
To obtain text embeddings using Python with the OpenAI SDK:

from openai import OpenAI
```python
# Initialize OpenAI client
client = OpenAI()

# Create an embedding request
response = client.embeddings.create(
    input="Your text string goes here",
    model="text-embedding-3-large"
)

# Print the embedding result
print(response.data[0].embedding)
```

### Explanation
- The model parameter specifies the embedding model to use (text-embedding-3-large).
- The input parameter is the text string you want to embed.
- This example prints the first embedding returned in the response.

## Structured Output

### Pydantic

```python
from pydantic import BaseModel
from openai import OpenAI

class Event(BaseModel):
    name: str
    date: str
    participants: list[str]

# Initialize OpenAI client
client = OpenAI()

# Example usage
response = client.responses.create(
    model="gpt-4o-2024-08-06",
    input=[
        {"role": "system", "content": "Extract the event information."},
        {"role": "user", "content": "Alice and Bob are going to a science fair on Friday."}
    ]
)

# Assuming the response returns JSON formatted data that matches the Event schema
event_data = {
    "name": "Science Fair",
    "date": "Friday",
    "participants": ["Alice", "Bob"]
}

# Validate the response against the Event model
event = Event(**event_data)

print(event)
```

### JSON Mode

```python
import json
from openai import OpenAI

# Initialize OpenAI client
client = OpenAI()

# Create the structured response request
response = client.chat.completions.create(
    model="gpt-4o-2024-08-06",
    input=[
        {"role": "system", "content": "Extract the event information."},
        {"role": "user", "content": "Alice and Bob are going to a science fair on Friday."}
    ],
    text={
        "format": {
            "type": "json_schema",
            "name": "calendar_event",
            "schema": {
                "type": "object",
                "properties": {
                    "name": {
                        "type": "string"
                    },
                    "date": {
                        "type": "string"
                    },
                    "participants": {
                        "type": "array",
                        "items": {
                            "type": "string"
                        }
                    }
                },
                "required": ["name", "date", "participants"],
                "additionalProperties": False
            },
            "strict": True
        }
    }
)

# Parse the JSON formatted output text
event = json.loads(response.output_text)

print(event)
```

## Getting Models
You can also interact with the Exxeta AI service directly using requests for making HTTP GET requests.
In this example we can retrieve all the available AI models.



```python
import requests

# Define the URL for models
url = "https://ai.exxeta.info/models"

# Set up headers including authorization token
headers = {
    'Authorization': 'your_authorization_token',
    'Cookie': 'ARRAffinity=your_cookie_value; ARRAffinitySameSite=your_cookie_value'
}

# Initialize payload (if needed)
payload = {}

# Make the GET request
response = requests.request("GET", url, headers=headers, data=payload)

# Print the response text
print(response.text)
```


