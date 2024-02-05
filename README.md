# Use Voice in a back-and-forth conversation with any text Foundational Model supported by Amazon Bedrock.

This repository provides a sample implementation of using [Amazon Bedrock](https://aws.amazon.com/bedrock/) and other supporting AWS services to have a voice conversation with a Foundational AI model. The code demonstrates how to build an application with GenAI that supports natural back-and-forth voice conversations.

Key aspects shown in the code:

- Streaming transcription of user speech to text with [Amazon Transcribe](https://aws.amazon.com/pm/transcribe)
- Making requests to Amazon Bedrock with transcribed text
- Streaming text responses from Amazon Bedrock to speech with [Amazon Polly](https://aws.amazon.com/polly/)
- Playing back Amazon Polly speech audio to user
- Buffering user speech and Amazon Bedrock responses to enable conversational flow

In summary, this code serves as an example implementation for developers to reference when building voice-enabled applications powered by Foundational AI through Amazon Bedrock and related AWS services.

## Architecture reference

To provide the best possible user experience for voice conversations, this solution utilizes streaming wherever supported by the underlying services. Specifically, streaming is used at every step except for the HTTP request to Amazon Bedrock. The Amazon Bedrock response is also streamed back to the user.

![arch](./docs/amazon-bedrock-voice-conversation.png)

1. User voice audio is streamed in chunks to Amazon Transcribe for speech-to-text transcription.
2. Amazon Transcribe processes the audio chunks as they arrive, transcribing them to text incrementally.
3. The transcribed text is buffered in a memory object, representing the user's full message for Amazon Bedrock.
4. When the user finishes speaking, an HTTP request is sent to Amazon Bedrock with the final transcribed text message.
5. The Amazon Bedrock text response is streamed back for text-to-speech conversion.
6. As text chunks from the Amazon Bedrock response arrive, they are submitted to Amazon Polly to synthesize into speech audio. This process uses streaming.
7. The Polly speech audio chunks are played back incrementally on the user's device as they arrive.

## Prerequisites

For this solution, you need the following prerequisites:

* Python 3.9 or later  
  `Note`: We recommend that you use a [virtual environment](https://docs.python.org/3.9/library/venv.html) or [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/) to isolate the solution from the rest of your Python environment.
* An [IAM](https://aws.amazon.com/iam/) user with enough permissions to use [Amazon Bedrock](https://aws.amazon.com/bedrock/), [Amazon Transcribe](https://aws.amazon.com/pm/transcribe), and [Amazon Polly](https://aws.amazon.com/polly/).  
  `Note`: Please ensure that the underlying Foundational Model in Amazon Bedrock, that you plan to use, is enabled in your AWS Account. To enable access, please see [Model access](https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html).

Run command below to install python libraries.

```shell
python install -r ./requirements.txt
```

## Running the app

First you need to set your AWS credentials as environment variables.

```shell
export AWS_ACCESS_KEY_ID=<...>
export AWS_SECRET_ACCESS_KEY=<...>
export AWS_DEFAULT_REGION=<...> # Optional, defaults to us-east-1
```

Optionally, you can set the Foundational Model (FM) to be used. Default FM is `amazon.titan-text-express-v1`.

```shell
export MODEL_ID=<...>
```

Finally, run the python application.

```shell
python ./app.py
```

When you run the app, it will log your current configurations. Below is a sample log of a configuration.

```text
*************************************************************
[INFO] Supported FM models: ['amazon.titan-text-express-v1', 'amazon.titan-text-lite-v1', 'anthropic.claude-v2:1', 'anthropic.claude-v2', 'meta.llama2-13b-chat-v1', 'meta.llama2-70b-chat-v1', 'cohere.command-text-v14', 'cohere.command-light-text-v14'].
[INFO] Change FM model by setting <MODEL_ID> environment variable. Example: export MODEL_ID=meta.llama2-70b-chat-v1

[INFO] AWS Region: us-east-1
[INFO] Amazon Bedrock model: amazon.titan-text-express-v1
[INFO] Polly config: engine neural, voice Joanna
[INFO] Log level: none

[INFO] Hit ENTER to interrupt Amazon Bedrock. After you can continue speaking!
[INFO] Go ahead with the voice chat with Amazon Bedrock!
*************************************************************
```

### Interrupting Amazon Bedrock voice
You can interrupt Amazon Bedrock voice speech by hitting `Enter` keyboard. With that, you don't have to wait for Amazon Bedrock speech completion, and can ask your next question right away!


## Further configuration fine-tuning

1. Model API request attributes config
   `api_request_schema.py` has the FM api request schema for all supported models. You can change for each individual model as per your needs.
   For instance, for the `amazon.titan-text-express-v1` model, you can change the default values for `maxTokenCount`, `temperature` or any other valid and applicable to your needs attributes.

2. Global config map in
   `app.py` creates a `config` dict, which you can update to further change the configuration. For instance, you can change the audio voice to any other [supported by Amazon Polly](https://docs.aws.amazon.com/polly/latest/dg/voicelist.html).
   For instance, by setting the `VoiceId` to `Joey`.


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

