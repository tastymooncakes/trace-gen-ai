
# Traceable Generative AI Instill Hackathon

## Introduction
The blurring boundaries between human and AI creations has many expressing concerns. The creator community has also expressed discontent at their works being used for training without their expressed consent. All of these issues can be managed if there was a robust way of verifying digital content.

Instill's hackathon offers us an opportunity to create responsible generative AI by leveraging its pipeline constructor as well as its promising [Numbers Protocol]([url](https://www.numbersprotocol.io/)) connector which seems to be a promising provenance solution.

## Numbers Protocol
The Numbers Protocol connector is interesting because it offers a well constructed way of creating traceable media by creating asset histories coined as "commits" on their native Numbers blockchain. Similar to git commits, Numbers Protocol commits represent a snapshot of the digital media. By leveraging this intuitive feature immutable proveance records can be created that are able to navigate around the limitations of even established standards such as [C2PA]([url](https://c2pa.org/)). Its important to note that the way proveance is stored with Numbers Protocol also aheres to the C2PA standard making it arguable a more robust solution due to its simple but creative application of blockchain.

## Pipeline
Link: https://instill.tech/tasymooncakes/pipelines/trace_gen_ai

Trace Gen AI pipeline is designed to assist users with no knowledge of creating effective text-to-image prompts by leveraging OpenAI's GPT3.5 to generative prompts that are fed into Stability AI's stable diffusion-xl-1024-v1-0 and registered with Numbers Protocol connector to create on-chain provenance records. These records range from creator_name, license, AI training permissions and more.

Inputs: 
prompt (text)
creator (text)

Outputs:
final_image (imgae)
provenance (text)

<img width="1291" alt="Screenshot 2024-06-03 at 10 37 20 PM" src="https://github.com/tastymooncakes/c2pa-generative-ai/assets/171561374/957b0e04-0059-4598-b578-41327332b321">
<img width="1470" alt="Screenshot 2024-06-03 at 9 49 27 PM" src="https://github.com/tastymooncakes/c2pa-generative-ai/assets/171561374/390e047c-0c06-4f3f-aede-53d6c9743a2d">

## Demo Project
To better showcase Trace Gen AI pipeline, a simple demo project was created to showcase how to create Traceable Generative AI.

![Copy of Untitled](https://github.com/tastymooncakes/c2pa-generative-ai/assets/171561374/1d230ba9-301e-47f8-87ae-09f426b8dc90)

Application allows users to input a text prompt into the input field and will generate an image with Stable Diffusion. Users can click on provenance to navigate to the media profile page and view the commits.

## Outputs
See below for an example output from the demo project

final_image: https://ipfs-pin.numbersprotocol.io/ipfs/bafybeigago4x7xpdck7pqui3sbg7iwnb6rswrn5fsvrp7sbkv2sgmcrx2q

provenance: https://verify.numbersprotocol.io/asset-profile/bafybeigago4x7xpdck7pqui3sbg7iwnb6rswrn5fsvrp7sbkv2sgmcrx2q

on-chain record: https://ipfs-pin.numbersprotocol.io/ipfs/bafkreic23z6fajlyd53fk3z3hiauapgxhheopkrjfhuhmmp6ywho6dfqea

```
{
  "assetCid": "bafybeigago4x7xpdck7pqui3sbg7iwnb6rswrn5fsvrp7sbkv2sgmcrx2q",
  "assetSha256": "f98011cb7ccf4d6aa07319c209cfaf93cbdb5205b6e9d1c6f8d262df6f83b9c4",
  "encodingFormat": "image/png",
  "assetTimestampCreated": 1717472544,
  "assetCreator": "tastymooncakes",
  "abstract": "pokemon trainer",
  "headline": "pokemon trainer",
  "creatorWallet": "0xa90899f84b31c426E8C07D5795Fd76b9f797E994",
  "digitalSourceType": "trainedAlgorithmicMedia",
  "license": {
    "name": "CC-BY-4.0",
    "document": "https://creativecommons.org/licenses/by/4.0/legalcode"
  },
  "generatedBy": "Stable Diffusion",
  "instillMetadata": {
    "Pipeline": {
      "UID": "bf56286f-3126-488f-8419-f3d89f98130c",
      "Recipe": {
        "trigger": {
          "trigger_by_request": {
            "request_fields": {
              "prompt": {
                "title": "prompt",
                "description": "",
                "instill_format": "string",
                "instill_ui_order": 0,
                "instill_ui_multiline": false
              },
              "creator": {
                "title": "creator",
                "description": "",
                "instill_format": "string",
                "instill_ui_order": 0,
                "instill_ui_multiline": false
              }
            },
            "response_fields": {
              "provenance": {
                "title": "provenance",
                "value": "${numbers_0.output.asset_urls}",
                "description": "",
                "instill_ui_order": 1
              },
              "final_image": {
                "title": "final_image",
                "value": "${stability_0.output.images}",
                "description": "",
                "instill_ui_order": 0
              }
            }
          }
        },
        "version": "v1beta",
        "components": [
          {
            "id": "openai_0",
            "metadata": null,
            "connector_component": {
              "task": "TASK_TEXT_GENERATION",
              "input": {
                "n": 1,
                "model": "gpt-3.5-turbo",
                "top_p": 1,
                "prompt": "${trigger.prompt}",
                "temperature": 1,
                "system_message": "You are an AI image prompt generator. I will describe an image to you and you will return 1 text to image prompt. Style list: studio ghibli-inspired, star wars-inspired, anime, disney-inspired, 90's anime-inspired. Pick a style from the style list and return a prompt in the format: <style> (<prompt>).",
                "response_format": {
                  "type": "text"
                }
              },
              "connection": {
                "api_key": "${secrets.INSTILL_SECRET}"
              },
              "definition_name": "connector-definitions/9fb6a2cb-bff5-4c69-bc6d-4538dd8e3362"
            }
          },
          {
            "id": "stability_0",
            "metadata": null,
            "connector_component": {
              "task": "TASK_TEXT_TO_IMAGE",
              "input": {
                "steps": 30,
                "width": 1024,
                "engine": "stable-diffusion-xl-1024-v1-0",
                "height": 1024,
                "prompts": "${openai_0.output.texts}",
                "sampler": "K_DPM_2_ANCESTRAL",
                "samples": 1,
                "cfg_scale": 7,
                "style_preset": "anime",
                "clip_guidance_preset": "NONE"
              },
              "connection": {
                "api_key": "${secrets.INSTILL_SECRET}"
              },
              "definition_name": "connector-definitions/c86a95cc-7d32-4e22-a290-8c699f6705a4"
            }
          },
          {
            "id": "numbers_0",
            "metadata": null,
            "connector_component": {
              "task": "TASK_REGISTER",
              "input": {
                "images": "${stability_0.output.images}",
                "caption": "${trigger.prompt}",
                "license": {
                  "name": "CC-BY-4.0",
                  "document": "https://creativecommons.org/licenses/by/4.0/legalcode"
                },
                "headline": "${trigger.prompt}",
                "generated_by": "Stable Diffusion",
                "asset_creator": "${trigger.creator}",
                "mining_preference": "notAllowed",
                "digital_source_type": "trainedAlgorithmicMedia"
              },
              "connection": {
                "capture_token": "${secrets.capturetoken}"
              },
              "definition_name": "connector-definitions/70d8664a-d512-4517-a5e8-5d4da81756a7"
            }
          }
        ]
      }
    }
  },
  "generatedThrough": "https://instill.tech",
  "miningPreference": "notAllowed",
  "assetSourceType": "others"
}
```

On-chain records showcase license, miningPreference, AI models used, creator, and more.

## More Examples

### Pokemon Trainer
https://verify.numbersprotocol.io/asset-profile/bafybeicrznkjxdtoytakguumd3e6fpbqyef47f3d6nmaedoikh5xlng7ci?nid=bafybeicrznkjxdtoytakguumd3e6fpbqyef47f3d6nmaedoikh5xlng7ci

https://verify.numbersprotocol.io/asset-profile/bafybeierxlqeuk4druqklie6chi3emklqwzmmowux46sl6svsi4tab4umi?nid=bafybeierxlqeuk4druqklie6chi3emklqwzmmowux46sl6svsi4tab4umi

https://verify.numbersprotocol.io/asset-profile/bafybeigvvlnwu7vtuespxjopuuncfwu3z2ikcvlgkuo7vdnofp7bk7znju-1?nid=bafybeigvvlnwu7vtuespxjopuuncfwu3z2ikcvlgkuo7vdnofp7bk7znju

### Naruto
https://verify.numbersprotocol.io/asset-profile/bafybeic5s3lhjageswexrvkigs7yqk4egsvgalfast6ebyivxrhbbmtpky?nid=bafybeic5s3lhjageswexrvkigs7yqk4egsvgalfast6ebyivxrhbbmtpky

### Luffy One Piece
https://verify.numbersprotocol.io/asset-profile/bafybeiau77vmi2vhauctgr6usfo3rpw57fvc5evog4q4mhthtrshzkjmhi?nid=bafybeiau77vmi2vhauctgr6usfo3rpw57fvc5evog4q4mhthtrshzkjmhi


### Rukia Kuchiki
https://verify.numbersprotocol.io/asset-profile/bafybeic57ndqhqkfpyuja37nyxje2c7f2xvejssqkndvyktpk3douwidxm?nid=bafybeic57ndqhqkfpyuja37nyxje2c7f2xvejssqkndvyktpk3douwidxm
