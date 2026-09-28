# Microsoft Foundry Generative AI Chat Application

A Python-based generative AI chat application built with Microsoft Foundry and the OpenAI Responses API. This project demonstrates both synchronous and asynchronous approaches to interacting with a deployed AI model while using Microsoft Entra ID authentication and maintaining multi-turn conversation context.

## Overview

This project demonstrates how a Python application can securely connect to a model deployed through Microsoft Foundry and provide an interactive conversational experience.

Two implementations are included:

- `chat-app.py` — synchronous client with streamed model responses
- `chat-async.py` — asynchronous client using Python `asyncio`

Both applications use environment-based configuration, Microsoft Entra ID authentication, and the OpenAI Responses API.

## Architecture

```text
                    User
                      |
                      v
             Python Chat Client
              /             \
             /               \
            v                 v
    Synchronous Client   Asynchronous Client
       chat-app.py          chat-async.py
            |                  |
            +--------+---------+
                     |
                     v
          DefaultAzureCredential
                     |
                     v
             Microsoft Entra ID
                     |
                     v
          Microsoft Foundry Endpoint
                     |
                     v
             OpenAI Responses API
                     |
                     v
              Model Deployment
                     |
                     v
               AI Response
```

## Key Features

### Microsoft Foundry Integration

Connects a Python application to an AI model deployment through a Microsoft Foundry endpoint.

### Microsoft Entra ID Authentication

Uses `DefaultAzureCredential` and a bearer token provider instead of embedding API keys directly in application source code.

### Synchronous Chat Client

`chat-app.py` uses the synchronous `OpenAI` client and streams response text as it is generated.

### Asynchronous Chat Client

`chat-async.py` uses `AsyncOpenAI` and Python's `asyncio` framework to perform asynchronous model requests.

### Multi-Turn Conversation Context

Both implementations track the previous response ID and pass it back to the Responses API:

```python
previous_response_id=last_response_id
```

This allows subsequent prompts to continue the existing conversation context.

### Streaming Responses

The synchronous implementation enables streaming:

```python
stream=True
```

and processes response events as they arrive.

### Environment-Based Configuration

Deployment-specific configuration is stored outside application source code using environment variables.

## Technology Stack

- Python
- Microsoft Foundry
- OpenAI Python SDK
- OpenAI Responses API
- Microsoft Entra ID
- Azure Identity SDK
- `DefaultAzureCredential`
- `AsyncOpenAI`
- `asyncio`
- `python-dotenv`
- `aiohttp`

## Project Structure

```text
03-microsoft-foundry-generative-ai-chat/
├── .env.example
├── .gitignore
├── README.md
├── chat-app.py
├── chat-async.py
└── requirements.txt
```

## Synchronous vs. Asynchronous Implementations

| Capability | Synchronous Client | Asynchronous Client |
|---|---|---|
| File | `chat-app.py` | `chat-async.py` |
| OpenAI client | `OpenAI` | `AsyncOpenAI` |
| Azure Identity | Synchronous | Asynchronous |
| Python `asyncio` | No | Yes |
| Response handling | Streaming | Awaited response |
| Conversation context | Yes | Yes |
| Entra ID authentication | Yes | Yes |

The synchronous implementation is useful when application execution can wait for each operation to complete.

The asynchronous implementation demonstrates a non-blocking programming model that can be extended for applications that need to manage concurrent operations efficiently.

## Prerequisites

Before running the project, ensure you have:

- Python installed
- An Azure subscription
- Access to Microsoft Foundry
- A deployed model
- Azure CLI installed
- Permission to access the deployed model

You should also be authenticated to Azure:

```bash
az login
```

## Installation

Clone the repository:

```bash
git clone <repository-url>
cd 03-microsoft-foundry-generative-ai-chat
```

Create a Python virtual environment:

```bash
python3 -m venv .venv
```

Activate it on macOS or Linux:

```bash
source .venv/bin/activate
```

Install the dependencies:

```bash
python -m pip install -r requirements.txt
```

## Environment Configuration

Copy the environment template:

```bash
cp .env.example .env
```

Configure the following values in `.env`:

```text
AZURE_OPENAI_ENDPOINT=<your-microsoft-foundry-endpoint>
MODEL_DEPLOYMENT=<your-model-deployment-name>
```

Do not commit the `.env` file.

## Authentication

The application uses Microsoft Entra ID rather than storing an Azure API key in the source code.

The synchronous implementation uses:

```python
from azure.identity import DefaultAzureCredential, get_bearer_token_provider
```

The asynchronous implementation uses:

```python
from azure.identity.aio import DefaultAzureCredential, get_bearer_token_provider
```

The token provider requests an access token for the Microsoft Foundry service and supplies it to the OpenAI client.

## Running the Synchronous Application

Run:

```bash
python chat-app.py
```

Enter a prompt when requested:

```text
Enter a prompt (or type "quit" to exit):
```

The response is streamed to the terminal as it is generated.

Enter:

```text
quit
```

to exit.

## Running the Asynchronous Application

Run:

```bash
python chat-async.py
```

The asynchronous client waits for the model response using:

```python
response = await async_client.responses.create(...)
```

The application then displays the assistant response and retains the response ID for subsequent conversation turns.

## Conversation Continuity

A key feature demonstrated by both clients is multi-turn conversational state.

The application begins with:

```python
last_response_id = None
```

After receiving a response, the response ID is retained:

```python
last_response_id = response.id
```

On the next request, that ID is supplied as:

```python
previous_response_id=last_response_id
```

This enables the model to continue the conversation without the client manually resending the entire conversation history.

## Security Practices

This repository follows several basic security practices:

- No API keys are embedded in source code.
- Microsoft Entra ID is used for authentication.
- `DefaultAzureCredential` provides credential discovery.
- Deployment configuration is stored in environment variables.
- `.env` is excluded from Git.
- `.env.example` contains placeholders only.
- Python virtual environments and cache files are excluded from source control.

Never commit credentials, tokens, production endpoints, or other secrets to a public repository.

## Error Handling

Both implementations wrap application execution in exception handling so authentication, configuration, networking, and API errors can be surfaced without exposing credentials in source code.

The asynchronous implementation also closes its Azure credential session during cleanup.

## Skills Demonstrated

This project demonstrates hands-on experience with:

- Microsoft Foundry application development
- Generative AI application integration
- OpenAI Responses API
- Python synchronous programming
- Python asynchronous programming
- `asyncio`
- Streaming AI responses
- Microsoft Entra ID authentication
- Azure Identity SDK
- Environment-based configuration
- Multi-turn conversational state
- Secure source-control practices

## Portfolio Progression

This project is part of a progressive Microsoft Foundry and Azure AI engineering portfolio.

### Project 01
**Microsoft Foundry First AI Agent**

Introduces agent creation and application integration with Microsoft Foundry.

### Project 02
**Microsoft Foundry Agent-Driven Workflow**

Introduces structured AI outputs, confidence-based routing, conditional workflow logic, automated resolution, and human escalation.

### Project 03
**Microsoft Foundry Generative AI Chat**

Introduces generative AI application development, streaming responses, multi-turn conversation state, and synchronous versus asynchronous API integration.

Together, these projects demonstrate progression from individual AI agents to agent-driven workflows and then to application-level generative AI integration.

## Learning Objectives Demonstrated

Through this project, I implemented and validated:

1. Secure authentication to Microsoft Foundry using Microsoft Entra ID.
2. Model interaction through the OpenAI Responses API.
3. Streaming responses using a synchronous Python client.
4. Asynchronous model requests using `AsyncOpenAI` and `asyncio`.
5. Multi-turn conversation continuity using response identifiers.
6. Environment-based application configuration.
7. Secure handling of local configuration files and source-control exclusions.

## Acknowledgments

This project was developed as part of hands-on Microsoft Foundry training based on Microsoft Learn exercises and was organized as a standalone portfolio project to demonstrate the underlying Azure AI and Python development concepts.