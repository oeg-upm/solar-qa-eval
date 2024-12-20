# Solar RAG Corpus Guideline

This corpus contains annotated and non-annotated data based on the generation from [soalr-qa](https://github.com/OEG-Clark/solar-qa/tree/UPDATE). The non-annotated data is directly generated from the soalr-qa pipeline, which follow the format [here](https://github.com/OEG-Clark/solar-qa/blob/UPDATE/CLI/Result_Spec.md).

## Annotation Format:

The annotation format is based on the solar-qa-pipeline generated format, added the information of `voter_name`, and the voted result as `vote`. 

The annotated data following the table given below:

| Category | Definition | DataType | Example |
| -------- | ------- | ------- | ------- |
| paper_title  | The title of the paper | String | Effect of silver doping on the TiO2 for photocatalytic reduction of CO2 |
| DOI | The DOI of the paper(extracted from the paper pdf file) | String | 10.1016/j.apcatb.2010.02.030 |
| voter_name | The name or perferred reference title of the voter | String | ClarkW |
| generation_model | The ollama model id for llm generation | String | llama3.2:3b |
| similarity_model | The ollama model id for text embedding | String | nomic-embed-text |
| similarity_metric | The metric for calculating the similarity between embeddings  | String | Cosine_Similarity |
| rag_type | the type of rag pipeline, range=['fact', 'naive'] | String | fact |
| result | The list of generation result from the llm  | List | Details given velow |


The details for `result` part of the annotated file:

| Category | Definition | DataType | Example | Range(if applicable) |
| -------- | ------- | ------- | ------- | ------- |
| question_category  | The category of the question for the llm | String | Light_source | [catalyst, co_catalyst, Light_source, Lamp, Reaction_medium, Reactor_type, Operation_mode] |
| query | The query for the llm | String | What is the Light_source used in the experiment? | Not applicable |
| generation | The generated answer from the llm | String | ###Light_source: UV | Not applicable |
| RAG_source | The information source provided for the RAG pipeline | String | generated_facts | Not applicable |
| selected_answer | The corresponding answer from the selection of choices | String | UV | Details are given below |
| evidences | The list of evidences for the RAG pipeline  | list | Details given below | Not applicable |

And if the `vote` is contained at the `evidences`, which the detail is given below:

| Category | Definition | DataType | Example |
| -------- | ------- | ------- | ------- |
| similairty_score  | The similairty score between the query and correspond text provided to the embedding model | Float | 0.6205 |
| pdf_reference | The original text that are extracted from the paper | String | in the conventional focusing... |
| generated_fact | The generated facts based on the pdf_reference | String | Facts: 1. A homemade apparatus is used... |
| vote | The value for the vote | String | One of [`Negative`, `Positive`, `Dont Know`] |
