https://deepinfra.com/deepseek-ai/DeepSeek-R1/api

# OpenAI-compatible HTTP API

You can POST to our OpenAI Chat Completions compatible endpoint.

##Simple messages and prompts

Given a list of messages from a conversation, the model will return a response.

```bash
curl "https://api.deepinfra.com/v1/openai/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
      "model": "deepseek-ai/DeepSeek-R1",
      "messages": [
        {
          "role": "user",
          "content": "Hello!"
        }
      ]
    }'
```

To which you'd get something like:

```
{
    "id": "chatcmpl-guMTxWgpFf",
    "object": "chat.completion",
    "created": 1694623155,
    "model": "deepseek-ai/DeepSeek-R1",
    "choices": [
        {
            "index": 0,
            "message": {
                "role": "assistant",
                "content": " Hello! It's nice to meet you. Is there something I can help you with or would you like to chat for a bit?"
            },
            "finish_reason": "stop"
        }
    ],
    "usage": {
        "prompt_tokens": 15,
        "completion_tokens": 16,
        "total_tokens": 31,
        "estimated_cost": 0.0000268
    }
}
```
##Conversations

To create a longer chat-like conversation you just have to add each response message and each of the user messages to every request. This way the model will have the context and will be able to provide better answers. You can tweak it even further by providing a system message.

```bash
curl "https://api.deepinfra.com/v1/openai/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
      "model": "deepseek-ai/DeepSeek-R1",
      "messages": [
        {
            "role": "system",
            "content": "Respond like a michelin starred chef."
        },
        {
          "role": "user",
          "content": "Can you name at least two different techniques to cook lamb?"
        },
        {
          "role": "assistant",
          "content": "Bonjour! Let me tell you, my friend, cooking lamb is an art form, and I'"'"'m more than happy to share with you not two, but three of my favorite techniques to coax out the rich, unctuous flavors and tender textures of this majestic protein. First, we have the classic \"Sous Vide\" method. Next, we have the ancient art of \"Sous le Sable\". And finally, we have the more modern technique of \"Hot Smoking.\""
        },
        {
          "role": "user",
          "content": "Tell me more about the second method."
        }
      ]
    }'
```

The conversation above might return something like the following

```
{
    "id": "chatcmpl-b23a3fb60cde42ce8f24bb980b4dee87",
    "object": "chat.completion",
    "created": 1715688169,
    "model": "deepseek-ai/DeepSeek-R1",
    "choices": [
        {
            "index": 0,
            "message": {
                "role": "assistant",
                "content": "Sous le Sable, my friend! It's an ancient technique that's been used for centuries in the Middle East and North Africa. The name itself..."
            },
            "finish_reason": "stop"
        }
    ],
    "usage": {
        "prompt_tokens": 149,
        "total_tokens": 487,
        "completion_tokens": 338,
        "estimated_cost": 0.00035493
    }
}
```

The longer the conversation gets, the more time it takes the model to generate the response. The number of messages that you can have in a conversation is limited by the context size of a model. Larger models also usually take more time to respond.


##Streaming

You can turn any of the requests above into a streaming request by passing "stream": true:

```bash
curl "https://api.deepinfra.com/v1/openai/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
      "model": "deepseek-ai/DeepSeek-R1",
      "stream": true,
      "messages": [
        {
          "role": "user",
          "content": "Hello!"
        }
      ]
    }'
```

to which you'd get a sequence of SSE events, finishing with [DONE].

```
data: {"id": "Rc5hsIPHOSfMP3rNSFUw9tfR", "object": "chat.completion.chunk", "created": 1694623354, "model": "deepseek-ai/DeepSeek-R1", "choices": [{"index": 0, "delta": {"role": "assistant", "content": " "}, "finish_reason": null}]}

data: {"id": "Rc5hsIPHOSfMP3rNSFUw9tfR", "object": "chat.completion.chunk", "created": 1694623354, "model": "deepseek-ai/DeepSeek-R1", "choices": [{"index": 0, "delta": {"role": "assistant", "content": " Hi"}, "finish_reason": null}]}

data: {"id": "Rc5hsIPHOSfMP3rNSFUw9tfR", "object": "chat.completion.chunk", "created": 1694623354, "model": "deepseek-ai/DeepSeek-R1", "choices": [{"index": 0, "delta": {"role": "assistant", "content": "!"}, "finish_reason": null}]}

data: {"id": "Rc5hsIPHOSfMP3rNSFUw9tfR", "object": "chat.completion.chunk", "created": 1694623354, "model": "deepseek-ai/DeepSeek-R1", "choices": [{"index": 0, "delta": {"role": "assistant", "content": ""}, "finish_reason": null}]}

data: {"id": "Rc5hsIPHOSfMP3rNSFUw9tfR", "object": "chat.completion.chunk", "created": 1694623354, "model": "deepseek-ai/DeepSeek-R1", "choices": [{"index": 0, "delta": {"role": "assistant", "content": "</s>"}, "finish_reason": null}]}

data: {"id": "Rc5hsIPHOSfMP3rNSFUw9tfR", "object": "chat.completion.chunk", "created": 1694623354, "model": "deepseek-ai/DeepSeek-R1", "choices": [{"index": 0, "delta": {}, "finish_reason": "stop"}]}
```
