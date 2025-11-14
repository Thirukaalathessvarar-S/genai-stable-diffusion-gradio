## Prototype Development for Image Generation Using the Stable Diffusion Model and Gradio Framework

### AIM:
To design and deploy a prototype application for image generation utilizing the Stable Diffusion model, integrated with the Gradio UI framework for interactive user engagement and evaluation.

### PROBLEM STATEMENT:

### DESIGN STEPS:
#### STEP 1:
Use the pre-trained Stable Diffusion model available from Hugging Face's diffusers library. Ensure all dependencies, including GPU acceleration (if available), are set up for optimal performance.

#### STEP 2:
Use Gradio to create an interface with: Input: Textbox for entering the prompt. Output: Display panel for the generated image.

#### STEP 3:
Load the Stable Diffusion model and tokenizer. Accept a text prompt from the user via Gradio's input. Preprocess the text and generate an image using Stable Diffusion. Display the generated image in the output panel.

#### STEP 4:
Test the application with diverse prompts to ensure quality. Deploy the application on a public platform (e.g., Hugging Face Spaces or local Gradio server).

### PROGRAM:
```
import os
import io
import IPython.display
from PIL import Image
import base64 
from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
hf_api_key = os.environ['HF_API_KEY']
```

```
# Helper function
import requests, json

#Text-to-image endpoint
def get_completion(inputs, parameters=None, ENDPOINT_URL=os.environ['HF_API_TTI_BASE']):
    headers = {
      "Authorization": f"Bearer {hf_api_key}",
      "Content-Type": "application/json"
    }   
    data = { "inputs": inputs }
    if parameters is not None:
        data.update({"parameters": parameters})
    response = requests.request("POST",
                                ENDPOINT_URL,
                                headers=headers,
                                data=json.dumps(data))
    return json.loads(response.content.decode("utf-8"))
```

```
import gradio as gr 

#A helper function to convert the PIL image to base64
#so you can send it to the API
def base64_to_pil(img_base64):
    base64_decoded = base64.b64decode(img_base64)
    byte_stream = io.BytesIO(base64_decoded)
    pil_image = Image.open(byte_stream)
    return pil_image

def generate(prompt):
    output = get_completion(prompt)
    result_image = base64_to_pil(output)
    return result_image

gr.close_all()
demo = gr.Interface(fn=generate,
                    inputs=[gr.Textbox(label="Your prompt")],
                    outputs=[gr.Image(label="Result")],
                    title="Image Generation with Stable Diffusion",
                    description="Generate any image with Stable Diffusion",
                    allow_flagging="never",
                    examples=["the spirit of a tamagotchi wandering in the city of Vienna","a mecha robot in a favela"])

demo.launch(share=True, server_port=int(os.environ['PORT1']))
demo.close()
```

### OUTPUT:
<img width="1010" height="536" alt="image" src="https://github.com/user-attachments/assets/8ad7586e-40c5-4cd1-8317-de980d6b23f8" />

<img width="792" height="533" alt="image" src="https://github.com/user-attachments/assets/cfbfdd58-214f-450b-ba51-1c7bbff3fa7b" />

<img width="612" height="415" alt="image" src="https://github.com/user-attachments/assets/2dd8791b-55f4-4df9-925d-8b54c84a5c44" />

### RESULT:
The application successfully generates high-quality images based on user-provided text prompts. The Stable Diffusion model ensures visually appealing results, and the Gradio interface makes it accessible and interactive.
