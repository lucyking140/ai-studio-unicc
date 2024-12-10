# UNICC: Development of an Open AI-enabled BOT Platform for UNECE

### Project Description

This project aims to develop an **AI-driven BOT platform** powered by **Large Language Models (LLMs)**. The platform is designed to assist:

- **Policymakers**
- **Research Students**
- **Other Stakeholders**

in efficiently querying and extracting relevant knowledge and insights from the extensive [**UNECE PDF documentation and library**](./documents_readme.md).

The platform:

- **Empowers policymakers and researchers** to quickly access and analyze large amounts of critical information.
- **Aligns with SDG 13: Climate Action**, supporting efforts to combat climate change through better-informed decision-making.

### Table of Contents

- [Installation & Setup](#installation-and-setup)
- [Model Implementation](#model-implementation)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgements](#acknowledgments)

### Installation and Setup

Ensure you have installed Python 3.10+. Otherwise, all of the required packages are installed in the notebook itself. We prepared the model to run on Google Colab's T4 GPU, so if running locally ensure you have access to a similar amount of memory.

The model notebook links to a user's Google Drive to identify the PDF database from which to process text for fine-tuning and RAG. To access this database, please download the [Sample Database](./Sample Dataset) folder or save PDFs from the [list of UNECE sources](./documents_readme.md), then change the `folder_path` in the **Setup** section of the notebook to the location of your dataset.

To access the Llama 3.1 model via Hugging Face, please [request access](https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct) through your Hugging Face account and add your HF token as a Colab Secret. The document also currently uses pre-generated authtokens for `ngrok` and Weights and Biases in the **Setup** section of the notebook. If you have your own accounts on those platforms, please replace those with your own tokens.

If you need to run the frontend demo, you'll need to configure the [Google Drive API](https://developers.google.com/drive/api/guides/search-files#python) for file search to give Colab access to URLs based on filenames (this is what supports access to relevant documents as a user submits queries). Then, you'll need to upload your resulting `service_account.json` file to the Colab before running the server.

### Model Implementation

1. **Performing text splitting:** parses PDF database of UNECE policy documents and session resolutions into 50-500 character "chunks" using font size, boldness, etc. to identify section headers. Stores these chunks along with document metadata to later feed into the RAG pipeline.

   - Uses `llama-index` ([open source embedding library](https://docs.llamaindex.ai/en/stable/examples/embeddings/huggingface/)) to embed and store these chunks in a vector-based document index for later collection. Currently set to use the `BAAI/bge-m3` [embedding model](https://huggingface.co/BAAI/bge-m3) from Hugging Face to support multilingual parsing.

2. **Llama 4-bit quantized:** prepares the model itself, using [unsloth](https://huggingface.co/unsloth/Meta-Llama-3.1-8B-bnb-4bit) to get a pre-trained 4-bit quantized version of Meta's [Llama 3.1 8B Instruct](https://www.llama.com/) model. Re-loads the same PDFs from the text splitting phase but as entire documents to pass into the model for fine-tuning.

   - Uses Low-Rank Adaptation (LoRA) for fine-tuning
   - Trains with Hugging Face's [Supervised Fine-tuning Trainer](https://huggingface.co/docs/trl/en/sft_trainer)

3. **Front end:** basic chatbot website set up with Flask for demo purposes -- allows user to input questions, view responses, and interact with relevant documents based on submitted queries (collected from the chunk embeddings metadata). Uses `ngrok` to simulate a mini server on Colab.

### Usage

Run the entire file until reaching the **Inference** section. At this point, your model is trained, fine-tuned, and ready for inference! You can send queries directly at this point in the file, or continue on to the **Front End** section where you'll be able to load a Flask demo web app for further testing. Running the cell to begin the Flask application will produce an `ngrok` URL that is accessible from any browser. Once the app has been started, feel free to experiment with any sample questions or browse the full dataset of policy documents in the "View PDFs" tab!

### Contributing

Create a pull request to contribute to this project!

### License  

This project is licensed under the **Apache License 2.0**, a permissive open-source license that:  
- Allows commercial use, modification, distribution, and private use.  
- Requires preservation of copyright and license notices.  

📄 **[Read the Full License](LICENSE)**


### Acknowledgments

We would like to thank the following people who made this project possible:

   - The UNICC student team: Aleena Sultan, Alexa Celis, Aniya Smith, Chinmayi Ranade, Chloe Nam, Hailey Manuel, Journei Ferguson, Kim Hernandez, Lucy King, Sadia Sharmin, Sara Wang
   - UNICC Challenge Advisors: Cosimo D'Oria, Devyani Rastogi, Diana Haj, Jason Jin, James Vence, Shambhavi Mohan, Tourad Baba
   - Break Through Tech AI Studio TA: Dev Ashar
   - Break Through Tech Program
