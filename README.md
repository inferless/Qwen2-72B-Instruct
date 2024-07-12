# Tutorial - Deploy Qwen2-72B-Instruct using Inferless
[Qwen2-72B-Instruct](https://huggingface.co/Qwen/Qwen2-72B-Instruct-AWQ) is a part of the Qwen2 series of large language models ranging from 0.5 to 72 billion parameters. The repository is for the 72B instruction-tuned model for deploying the model in the Inferless platform.

## TL;DR:
- Deployment of Qwen2-72B-Instruct model using [Transformers](https://github.com/huggingface/transformers).
- You can expect an average tokens/sec of `17.83` and a latency of `24.79 sec` for generating a text of `512` tokens. This setup has an average cold start time of `35.59 sec`.
- Dependencies defined in `inferless-runtime-config.yaml`.
- GitHub/GitLab template creation with `app.py`, `inferless-runtime-config.yaml` and `inferless.yaml`.
- Model class in `app.py` with `initialize`, `infer`, and `finalize` functions.
- Custom runtime creation with necessary system and Python packages.
- Model import via GitHub with `input_schema.py` file.
- Recommended GPU: NVIDIA A100 for optimal performance.
- Custom runtime selection in advanced configuration.
- Final review and deployment on the Inferless platform.

### Fork the Repository
Get started by forking the repository. You can do this by clicking on the fork button in the top right corner of the repository page.

This will create a copy of the repository in your own GitHub account, allowing you to make changes and customize it according to your needs.

### Create a Custom Runtime in Inferless
To access the custom runtime window in Inferless, simply navigate to the sidebar and click on the Create new Runtime button. A pop-up will appear.

Next, provide a suitable name for your custom runtime and proceed by uploading the **inferless-runtime-config.yaml** file given above. Finally, ensure you save your changes by clicking on the save button.

### Import the Model in Inferless
Log in to your inferless account, select the workspace you want the model to be imported into and click the Add Model button.

Select the PyTorch as framework and choose **Repo(custom code)** as your model source and select your provider, and use the forked repo URL as the **Model URL**.

Enter all the required details to Import your model. Refer [this link](https://docs.inferless.com/integrations/git-custom-code/git--custom-code) for more information on model import.

---
## Curl Command
Following is an example of the curl command you can use to make inference. You can find the exact curl command in the Model's API page in Inferless.
```bash
curl --location '<your_inference_url>' \
    --header 'Content-Type: application/json' \
    --header 'Authorization: Bearer <your_api_key>' \
    --data '{
        "inputs": [
            {
                "name": "prompt",
                "shape": [1],
                "data": ["What is deep learning?"],
                "datatype": "BYTES"
            },
            {
                "name": "temperature",
                "shape": [1],
                "data": [0.7],
                "datatype": "FP32"
            },
            {
                "name": "top_p",
                "shape": [1],
                "data": [0.1],
                "datatype": "FP32"
            },
            {
                "name": "repetition_penalty",
                "shape": [1],
                "data": [1.18],
                "datatype": "FP32"
            },
            {
                "name": "max_tokens",
                "shape": [1],
                "data": [512],
                "datatype": "INT16"
            },
            {
                "name": "top_k",
                "shape": [1],
                "data": [40],
                "datatype": "INT8"
            }
        ]
    }'

```

---
## Customizing the Code
Open the `app.py` file. This contains the main code for inference. It has three main functions, initialize, infer and finalize.

**Initialize** -  This function is executed during the cold start and is used to initialize the model. If you have any custom configurations or settings that need to be applied during the initialization, make sure to add them in this function.

**Infer** - This function is where the inference happens. The argument to this function `inputs`, is a dictionary containing all the input parameters. The keys are the same as the name given in inputs. Refer to [input](https://docs.inferless.com/model-import/input-output-schema) for more.

```python
def infer(self, inputs):
    prompts = inputs["prompt"]
    temperature = inputs["temperature"]
    top_p = inputs["top_p"]
    repetition_penalty = inputs["repetition_penalty"]
    top_k = inputs["top_k"]
    max_tokens = inputs["max_tokens"]
```

**Finalize** - This function is used to perform any cleanup activity for example you can unload the model from the gpu by setting to `None`.
```python
def finalize(self):
    self.llm = None
```


For more information refer to the [Inferless docs](https://docs.inferless.com/).
