# SolarFactRAG CLI

## Install

#### 1. install all python packages

```console
pip install ollama networkx leidenalg cdlib python-igraph python-dotenv langchain huggingface_hub langchain-ollama==0.1.0 langchain-community==0.2.19 sentence-transformers==2.7.0 grobid-client-python==0.0.3
```

#### 2. install ollama

more details about ollama please visit the [offical ollama website](https://ollama.com/)

```console
curl -fsSL https://ollama.com/install.sh | sh
```

#### 3. install grobid client

please reference this part to the [offical grobid github page](https://grobid.readthedocs.io/en/latest/Install-Grobid/)

## Run FactRAG CLI

After installed all prerequisite libraries and software, you can simply run the cli by:

```console
python FactsRAG.py --llm_id llama3.2:3b --embedding_id nomic-embed-text --input_file_path XXX/paper_2.pdf --prompt_file XXX/prompt.json --context_file_path XXX/test.json
```

Here is a table that describe the parameters to run the FactRAG cli

| Parameter | Definition | DataType | Example |
| -------- | ------- | ------- | ------- |
| llm_id  | the parameter of which LLM model from ollama to use | String | [llama3.2:3b](https://ollama.com/library/llama3.2) |
| embedding_id | the parameter of which embedding model from ollama to use | String | [nomic-embed-text](https://ollama.com/library/nomic-embed-text) |
| input_file_path | path for input data, pdf file or extracted json file | String | ../chunk_bench/paper_1.pdf |
| prompt_file | path for the prompt json file | String | ../FactRAG/prompt.json |
| context_file_path | path for save the output json file | String | ../FactRAG/context.json |



## Result Spec

Here is the table that describe the saved output json file:

| Category | Definition | DataType | Example |
| -------- | ------- | ------- | ------- |
| paper_title  | The title of the paper | String | Effect of silver doping on the TiO2 for photocatalytic reduction of CO2 |
| DOI | The DOI of the paper(extracted from the paper pdf file) | String | 10.1016/j.apcatb.2010.02.030 |
| generation_model | The ollama model id for llm generation | String | llama3.2:3b |
| similarity_model | The ollama model id for text embedding | String | nomic-embed-text |
| similarity_metric | The metric for calculating the similarity between embeddings  | String | Cosine_Similarity |
| result | The list of generation result from the llm  | List | Details given velow |

The details for `result` part of the saved output json file:

| Category | Definition | DataType | Example | Range(if applicable) |
| -------- | ------- | ------- | ------- | ------- |
| question_category  | The category of the question for the llm | String | Light_source | [catalyst, co_catalyst, Light_source, Lamp, Reaction_medium, Reactor_type, Operation_mode] |
| query | The query for the llm | String | What is the Light_source used in the experiment? | Not applicable |
| generation | The generated answer from the llm | String | ###Light_source: UV | Not applicable |
| RAG_source | The information source provided for the RAG pipeline | String | generated_facts | Not applicable |
| selected_answer | The corresponding answer from the selection of choices | String | UV | Details are given below |
| evidences | The list of evidences for the RAG pipeline  | list | Details given below | Not applicable |

The range for `selected_answer` with correspongind `question_category`:
| question_category | Range(if applicable) |
| -------- | ------- |
| catalyst | Not applicable |
| co_catalyst | Not applicable |
| Light_source | 'UV', 'Solar', 'UV-Vis', 'Monochromatic', 'Solar Simulator', 'Do not Know' |
| Lamp | 'Fluorescent', 'Mercury', 'Halogen', 'Mercury-Xenon', 'LED', 'Tungsten', 'Xenon', 'Tungsten-Halide', 'Solar Simulator', 'Do not Know' |
| Reaction_medium | 'Liquid', 'Gas', 'Do not Know' |
| Reactor_type | 'Slurry', 'Fixed-bed', 'Optical Fiber', 'Monolithic', 'Membrane', 'Fluidised-bed', 'Do not Know' |
| Operation_mode | 'Batch', 'Continuous', 'Batch/Continuous', 'Do not Know' |

The details for `evidence` part of the `result` from the output json:

| Category | Definition | DataType | Example |
| -------- | ------- | ------- | ------- |
| similairty_score  | The similairty score between the query and correspond text provided to the embedding model | Float | 0.6205 |
| pdf_reference | The original text that are extracted from the paper | String | in the conventional focusing... |
| generated_fact | The generated facts based on the pdf_reference | String | Facts: 1. A homemade apparatus is used... |
