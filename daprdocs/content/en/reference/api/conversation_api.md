---
type: docs
title: "Conversation API reference"
linkTitle: "Conversation API"
description: "Detailed documentation on the conversation API"
weight: 1400
---

{{% alert title="Alpha" color="primary" %}}
The conversation API is currently in [alpha]({{< ref "certification-lifecycle.md#certification-levels" >}}).
{{% /alert %}}

Dapr provides an API to interact with Large Language Models (LLMs) and enables critical performance and security functionality with features like prompt caching and PII data obfuscation.

## Converse

This endpoint lets you converse with LLMs.

```
POST http://localhost:<daprPort>/v1.0-alpha1/conversation/<llm-name>/converse
```

### URL parameters

| Parameter | Description |
| --------- | ----------- |
| `llm-name` | The name of the LLM component. [See a list of all available conversation components.]({{< ref supported-conversation >}})

### Request body

| Field | Description |
| --------- | ----------- |
| `conversationContext` | The ID of an existing chat room (like in ChatGPT). |
| `inputs` | Inputs for the conversation. Multiple inputs at one time are supported. |
| `metadata` | [Metadata](#metadata) passed to conversation components. |

#### Metadata

Metadata can be sent in the request’s URL. It must be prefixed with `metadata.`, as shown in the table below.

| Parameter | Description |
| --------- | ----------- |
| `metadata.key` | The API key for the component. `key` is not applicable to the [AWS Bedrock component]({{< ref "aws-bedrock.md#authenticating-aws" >}}). |
| `metadata.model` | The Large Language Model you're using. Value depends on which conversation component you're using. `model` is not applicable to the [DeepSeek component]({{< ref deepseek.md >}}). |
| `metadata.cacheTTL` | A time-to-live value for a prompt cache to expire. Uses Golang duration format. |

For example, to call for [Anthropic]({{< ref anthropic.md >}}):

```bash
curl POST http://localhost:3500/v1.0-alpha1/conversation/anthropic/converse?metadata.key=key1&metadata.model=claude-3-5-sonnet-20240620&metadata.cacheTTL=10m
```

{{% alert title="Note" color="primary" %}}
The metadata parameters available depend on the conversation component you use. [See all the supported components for the conversation API.]({{< ref supported-conversation >}})
{{% /alert %}}

### Request content

```json
REQUEST = {
  "inputs": ["what is Dapr", "Why use Dapr"],
  "metadata": {
    "model": "model-type-based-on-component-used",
    "key": "authKey",
    "cacheTTL": "10m",
  }
}
```

### HTTP response codes

Code | Description
---- | -----------
`202`  | Accepted
`400`  | Request was malformed
`500`  | Request formatted correctly, error in Dapr code or underlying component

### Response content

```json
RESPONSE  = {
  "outputs": {
    {
       "result": "Dapr is distribution application runtime ...",
       "parameters": {},
    },
    {
       "result": "Dapr can help developers ...",
       "parameters": {},
    }
  },
}
```

## Next steps

- [Conversation API overview]({{< ref conversation-overview.md >}})
- [Supported conversation components]({{< ref supported-conversation >}})