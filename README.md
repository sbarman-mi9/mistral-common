# Mistral Common

## What is it? 
mistral-common is a set of tools to help you work with Mistral models. 

Our first release contains tokenization. Our tokenizers go beyond  the usual text <-> tokens, adding parsing of tools and structured conversation. We also release the validation and normalization code that is used in our API. 

We are releasing three versions of our tokenizer powering different sets of models. 

- v1: open-mistral-7b, open-mixtral-8x7b, mistral-embed
- v2: mistral-small-latest, mistral-large-latest
- v3: open-mixtral-8x22b

## Installation 

### pip 
You can install `mistral-common` via pip: 
```
(1.0.2-rc1)
pip install https://github.com/sbarman-mi9/mistral-common/raw/main/dist/mistral_common-1.0.2rc1-py3-none-any.whl

OR 

(1.0.2)
pip install mistral-common
```

### From Source
Alternatively, you can install from source directly. This repo uses poetry as a dependency and virtual environment manager.

You can install poetry with
```
pip install poetry
```

poetry will set up a virtual environment and install dependencies with the following command:
```
poetry install
```

## Examples 
<a target="_blank" href="https://colab.research.google.com/github/mistralai/mistral-common/blob/main/examples/tokenizer.ipynb">
  <img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/>
</a>



```py
# Import needed packages:
from mistral_common.protocol.instruct.messages import UserMessage
from mistral_common.protocol.instruct.request import ChatCompletionRequest
from mistral_common.protocol.instruct.tool_calls import Function, Tool
from mistral_common.tokens.tokenizers.mistral import MistralTokenizer

# Load Mistral tokenizer

model_name = "open-mixtral-8x22b"

tokenizer = MistralTokenizer.from_model(model_name)

# Tokenize a list of messages
tokenized = tokenizer.encode_chat_completion(
    ChatCompletionRequest(
        tools=[
            Tool(
                function=Function(
                    name="get_current_weather",
                    description="Get the current weather",
                    parameters={
                        "type": "object",
                        "properties": {
                            "location": {
                                "type": "string",
                                "description": "The city and state, e.g. San Francisco, CA",
                            },
                            "format": {
                                "type": "string",
                                "enum": ["celsius", "fahrenheit"],
                                "description": "The temperature unit to use. Infer this from the users location.",
                            },
                        },
                        "required": ["location", "format"],
                    },
                )
            )
        ],
        messages=[
            UserMessage(content="What's the weather like today in Paris"),
        ],
        model=model_name,
    )
)
tokens, text = tokenized.tokens, tokenized.text

# Count the number of tokens
print(len(tokens))
```
