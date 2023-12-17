# 🦜️🏓 LangServe on Docker

## Overview


LangServe helps developers deploy LangChain runnables and chains as a REST API.

This library is integrated with FastAPI and uses pydantic for data validation.

In addition, it provides a client that can be used to call into runnables deployed on a server. A javascript client is available in LangChainJS.

## Prerequisites

- Docker installed on your machine, an existing openai key.

## Getting Started

Use the `LangChain` CLI to bootstrap a `LangServe` project quickly.

To use the langchain CLI make sure that you have a recent version of `langchain-cli` 
installed. You can install it with `pip install -U langchain-cli`.

```sh
langchain app new apps/myapp
cd apps/myapp
```

And start editing server.py.

To serve the new FastAPI app just use 
```sh
langchain serve
```

To be able to deploy the application with a key on it's own we use dotenv.
```sh
pip install python-dotenv
```

Create a .env file with the OPENAI_API_KEY: 
```sh
echo "OPENAI_API_KEY=[sk-your_openai_api_key_here]" > .env
```




You can now modify the server.py:

```python
from fastapi import FastAPI
from langserve import add_routes
from langchain.prompts import ChatPromptTemplate
from langchain.chat_models import AzureChatOpenAI
from langserve import add_routes

app = FastAPI(
    title="JokeServe",
    description="A joke server that uses GPT-3.5-turbo to generate jokes.",
    version="0.1.0",
)
model = AzureChatOpenAI(
    openai_api_version="2023-05-15",
    deployment_name="GPT4",
    model_version="0613",
)

# a joke telling route
prompt = ChatPromptTemplate.from_template("Tell me a joke about {topic}")

add_routes(app,
           prompt | model,
           path="/chain",
           )

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8001)
```

## Dockerize your service 

See [our example Dockerfile](services/defaults/Dockerfile) and [example docker-compose.yml](services/defaults/docker-compose.yml).

## Use your service with kubernetes 

You can deploy these services on kubernetes using [tilt.dev]("https://tilt.dev/") and (k3d)["https://k3d.io/"] for local kubernetes development. 

To use tilt with k3s use the following commands: 
```sh
cd services 

k3d cluster create langchain --registry-create registry:5000

# wait around 2 minutes for kubernetes to boot

tilt up
```





