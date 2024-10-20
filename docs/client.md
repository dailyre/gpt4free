
### G4F - Client API

#### Introduction

Welcome to the G4F Client API, a cutting-edge tool for seamlessly integrating advanced AI capabilities into your Python applications. This guide is designed to facilitate your transition from using the OpenAI client to the G4F Client, offering enhanced features while maintaining compatibility with the existing OpenAI API.

#### Getting Started

**Switching to G4F Client:**

To begin using the G4F Client, simply update your import statement in your Python code:

Old Import:
```python
from openai import OpenAI
```

New Import:
```python
from g4f.client import Client as OpenAI
```

The G4F Client preserves the same familiar API interface as OpenAI, ensuring a smooth transition process.

### Initializing the Client

To utilize the G4F Client, create an new instance. Below is an example showcasing custom providers:

```python
from g4f.client import Client
from g4f.Provider import BingCreateImages, OpenaiChat, Gemini

client = Client(
    provider=OpenaiChat,
    image_provider=Gemini,
    # Add any other necessary parameters
)
```

## Configuration

You can set an "api_key" for your provider in the client.
And you also have the option to define a proxy for all outgoing requests:

```python
from g4f.client import Client

client = Client(
    api_key="...",
    proxies="http://user:pass@host",
    # Add any other necessary parameters
)
```

#### Usage Examples

**Text Completions:**

You can use the `ChatCompletions` endpoint to generate text completions as follows:

```python
from g4f.client import Client

client = Client()
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "Say this is a test"}],
    # Add any other necessary parameters
)
print(response.choices[0].message.content)
```

Also streaming are supported:

```python
from g4f.client import Client

client = Client()
stream = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "Say this is a test"}],
    stream=True,
)

for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content or "", end="")
```

**Image Generation:**

Generate images using a specified prompt:

```python
from g4f.client import Client

client = Client()
response = client.images.generate(
    model="dall-e-3",
    prompt="a white siamese cat",
    # Add any other necessary parameters
)

image_url = response.data[0].url
print(f"Generated image URL: {image_url}")
```

**Creating Image Variations:**

Create variations of an existing image:

```python
from g4f.client import Client

client = Client()
response = client.images.create_variation(
    image=open("cat.jpg", "rb"),
    model="bing",
    # Add any other necessary parameters
)

image_url = response.data[0].url
print(f"Generated image URL: {image_url}")
```
Original / Variant:

[![Original Image](/docs/cat.jpeg)](/docs/client.md) [![Variant Image](/docs/cat.webp)](/docs/client.md)

#### Use a list of providers with RetryProvider

```python
from g4f.client import Client
from g4f.Provider import RetryProvider, Phind, FreeChatgpt, Liaobots

import g4f.debug
g4f.debug.logging = True
g4f.debug.version_check = False

client = Client(
    provider=RetryProvider([Phind, FreeChatgpt, Liaobots], shuffle=False)
)
response = client.chat.completions.create(
    model="",
    messages=[{"role": "user", "content": "Hello"}],
)
print(response.choices[0].message.content)
```

```
Using RetryProvider provider
Using Phind provider
How can I assist you today?
```

#### Advanced example using GeminiProVision

```python
from g4f.client import Client
from g4f.Provider.GeminiPro import GeminiPro

client = Client(
    api_key="...",
    provider=GeminiPro
)
response = client.chat.completions.create(
    model="gemini-pro-vision",
    messages=[{"role": "user", "content": "What are on this image?"}],
    image=open("docs/waterfall.jpeg", "rb")
)
print(response.choices[0].message.content)
```

```
User: What are on this image?
```

![Waterfall](/docs/waterfall.jpeg)
```
Bot: There is a waterfall in the middle of a jungle. There is a rainbow over...
```

### Example: Using a Vision Model
The following code snippet demonstrates how to use a vision model to analyze an image and generate a description based on the content of the image. This example shows how to fetch an image, send it to the model, and then process the response.

```python
import g4f
import requests
from g4f.client import Client

image = requests.get("https://raw.githubusercontent.com/xtekky/gpt4free/refs/heads/main/docs/cat.jpeg", stream=True).raw
# Or: image = open("docs/cat.jpeg", "rb")

client = Client()
response = client.chat.completions.create(
    model=g4f.models.default,
    messages=[{"role": "user", "content": "What are on this image?"}],
    provider=g4f.Provider.Bing,
    image=image,
    # Add any other necessary parameters
)
print(response.choices[0].message.content)
```

#### Advanced example: A command-line program
```python
import g4f
from g4f.client import Client

# Initialize the GPT client with the desired provider
client = Client()

# Initialize an empty conversation history
messages = []

while True:
    # Get user input
    user_input = input("You: ")
    
    # Check if the user wants to exit the chat
    if user_input.lower() == "exit":
        print("Exiting chat...")
        break  # Exit the loop to end the conversation

    # Update the conversation history with the user's message
    messages.append({"role": "user", "content": user_input})

    try:
        # Get GPT's response
        response = client.chat.completions.create(
            messages=messages,
            model=g4f.models.default,
        )

        # Extract the GPT response and print it
        gpt_response = response.choices[0].message.content
        print(f"Bot: {gpt_response}")

        # Update the conversation history with GPT's response
        messages.append({"role": "assistant", "content": gpt_response})
    except Exception as e:
        print(f"An error occurred: {e}")
```

[Return to Home](/)
