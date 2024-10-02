# Gemma Sprint
Aa part of Google ML bootcamp, I made fine tuned LLM model based on Google’s open model Gemma2-2b-instruct via HuggingFace and PyTorch.
Even though I used my local machien with nVidia GPU but this notebook will work on Google Colab with a single T4 GPU as well.

my goal is to fine-tune Gemma for a specific purpose using a technique known as Q-LoRA.
I focused on fine-tuning the Gemma2-2B Insturct model to translate from Text to SQL to remove hurdles to handle database.
Fine-tuning involves refining LLM by training it further on a specific dataset tailored for a particular task.
This process sharpens the model’s ability to perform that task with higher accuracy.

## Training Dataset
I borrowed dataset from Huggingface (https://huggingface.co/datasets/gretelai/synthetic_text_to_sql)
The idea is to output a SQL query given a SQL Question and SQL Context.

## References
* Efficient Fine-Tuning for Llama-v2–7b on a Single GPU
* Understanding and Using Supervised Fine-Tuning (SFT) for Language Models (substack.com)
* SQL Generation in Text2SQL with TinyLlama’s LLM Fine-tuning (analyticsvidhya.com)
