# Result Specification

## Generation

#### Generation Result

The Generation Result is dedicated in the file `Generation.json`. File contains:

- model_id: the reference id for the generative model
- prompt_template: the template for the prompt for generate the result
- result:

    - reference_index: paper index given in the dataset
    - DOI: DOI reference for the paper
    - generation: generated result

#### Ground Truth

The Ground Truth is dedicated in the file `Ground_Truth.json`. File contains:

- reference_index: paper index given in the dataset
- DOI: DOI reference for the paper
- ground_truth: annotated result

#### Generation Range
The Generation range for each item is given below:

```
    "Light_source": ["UV", "Solar", "UV-Vis", "Monochromatic", "Solar Simulator"],
    "Lamp" : ["Fluorescent", "Mercury", "Halogen", "Mercury-Xenon", "LED", "Tungsten", "Xenon", "Tungsten-Halide", "Solar Simulator"],
    "Reactor_type": ["Slurry", "Fixed-bed", "Optical Fiber", "Monolithic", "Membrane", "Fluidised-bed"],
    "Reaction_medium": ["Liquid", "Gas"],
    "Operation_mode" : ["Batch", "Continuous", "Batch/Continuous"]
```

#### Generation Example
Result based on LLama-3-70B:

```
generation:
{
    "catalyst": " TiO2",
    "co_catalyst": " Ag",
    "light_source": " UV",
    "lamp": " Hg",
    "reaction_medium": " Liquid",
    "reactor_type": " Slurry",
    "operation_mode": " Batch"
}
```


## Evaluation
The Average accuarcy for each item, calculated according to `Evaluation Process` in `README`.
The evaluation result is dedicated in `Evaluation.json`. File contains:

- generation_model_id: id reference for the generation model
- similarity_model_id: id refernce for the similarity model
- source_ground_truth: path for the file that contains the ground_truth
- source_generation: path for the file that contains the generation result
- evaluation_strategy: the evaluation strategy we adopt, detailed in `Evaluation Process` in `README`
- metric: the evaluation metric
- result:

    - item: the targeted item
    - value: evaluation numerical value based on the evalution metric   

#### Evaluation Example
Result based on LLama-3-70B:

```
evaluation:
{
    "generation_model_id": "meta-llama/Meta-Llama-3-70B-Instruct",
    "similarity_model_id": "Salesforce/SFR-Embedding-Mistral",
    "source_ground_truth": "/Solar/result/LLama_3_70B/Ground_Truth.json",
    "source_generation": "/Solar/result/LLama_3_70B/Generation.json",
    "evaluation_strategy": "rule-based",
    "metric": "accuracy",
    "result": [
        {"item": "catalyst",
        "acc": 0.8275862068965517},
        ...
        ]
}
```


## Context

### Context for SolarRAG

The context or chunks that RAG system has selected to provide the context for the generative model.
The context is dedicated in `Context.json`. File contains:

- similarity_model_id: id refernce for the similarity model
- similarity_method: the method of calculating similarity
- context:

    - reference_index: paper index given in the dataset
    - contexts:

        - item: targeted item
        - context: a list of all the selected chunks from the original paper


```
context:
{
    "similarity_model_id": "Salesforce/SFR-Embedding-Mistral",
    "similarity_method": "Cosine_Similarity",
    "context": [
        {"reference_index": "1",
        "context": {
            "item": ["Operation_mode"],
            "chunk": ["XXXXX", "XXXXX"]
        }},
        ...
        ]
}
```


### Context for SolarFactRAG


SolarFactRAG is a RAG approach that based on the extracted facts from chunks.
The process of extracting facts from chunks are given:

- Use LLM to extract entities from the chunks
- Based on the extracted entities and the corresponding chunks, use llm to extract relations between entities.
- Use LLM to constructing simple, short factual sentences based on entities and relations.
  
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


