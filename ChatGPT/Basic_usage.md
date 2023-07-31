# Unlocking the Power of ChatGPT through the OpenAI API

ChatGPT has captured the world's attention with its ability to understand natural language and generate human-like responses. While the conversational interface is impressive, the real magic lies in the underlying AI models developed by OpenAI. In this post, we'll explore how you can access these models directly through the OpenAI API to build powerful natural language applications.

## Capabilities of the ChatGPT API

The ChatGPT API allows you to have conversational exchanges with AI assistants. You provide a conversation history and the API returns an assistant's response. 

Conversations are formatted with **system**, **user**, and **assistant** messages:

- **System** - Sets assistant's personality/behavior
- **User** - User requests and comments 
- **Assistant** - AI-generated responses

For example:

```python
messages = [
  {"role": "system", "content": "You are a helpful assistant"},
  {"role": "user", "content": "What is the weather forecast today?"},
  {"role": "assistant", "content": "The weather today is sunny with a high of 70F."} 
]
```

The API can be used for a wide range of applications like answering questions, language translation, text summarization, and more!

## Chatting with the API

Here is some sample Python code to call the ChatGPT API:

```python
import openai

response = openai.ChatCompletion.create(
  model="gpt-3.5-turbo",
  messages=[
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "What is the capital of France?"}
  ]  
)

print(response["choices"][0]["message"]["content"])
```

We provide a system message to set the assistant's behavior. The user message asks a question. 

The API response contains the assistant's answer, which we can extract and print.

## Some helpful functions

```python
def get_completion_from_messages(messages, 
                                 model="gpt-3.5-turbo", 
                                 temperature=0, 
                                 max_tokens=500):
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=temperature, # this is the degree of randomness of the model's output
        max_tokens=max_tokens, # the maximum number of tokens the model can ouptut 
    )
    return response.choices[0].message["content"]
```


```python
def get_completion_and_token_count(messages, 
                                   model="gpt-3.5-turbo", 
                                   temperature=0, 
                                   max_tokens=500):
    
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=temperature, 
        max_tokens=max_tokens,
    )
    
    content = response.choices[0].message["content"]
    
    token_dict = {
'prompt_tokens':response['usage']['prompt_tokens'],
'completion_tokens':response['usage']['completion_tokens'],
'total_tokens':response['usage']['total_tokens'],
    }

    return content, token_dict
```

## Function Calling

The ChatGPT API also supports **function calling**. This allows the assistant to return structured data by calling functions you define.

For example:

```python
import openai
import json

def get_weather(location):
  # Call weather API
  return {"temp": "70F"} 

response = openai.ChatCompletion.create(
  model="gpt-3.5-turbo",
  messages=[
    {"role": "user", "content": "What's the weather in Paris?"}
  ],
  functions=[{
    "name": "get_weather",
    "parameters": {"location": "string"} 
  }]
)

if response["choices"][0]["message"].get("function_call"):
  func_name = response["choices"][0]["message"]["function_call"]["name"] 
  args = json.loads(response["choices"][0]["message"]["function_call"]["arguments"])
  weather = get_weather(location=args["location"])
  
  print(weather) # Prints weather data
```

Here we defined a `get_weather` function. The API response contains JSON to call this function which we execute to get structured weather data.

The ChatGPT API opens up many possibilities for building advanced applications! With proper prompting and techniques, you can harness these models in creative ways.
