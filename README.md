# Self Ask

Self Ask (Press et al. 2023 "Measuring and Narrowing the Compositionality Gap in Language Models" https://arxiv.org/abs/2210.03350) is a technique introduced to allow an LLM to address questions that require compositional reasoning, i.e. questions that require the model to retrieve intermediate information. Simple RAG (retrieval augmented generation) fail because the question itself cannot be used as query to retrieve useful documents

Direct Prompting
* Question: Who was president of the U.S. when superconductivity was discovered?
* Answer: Franklin D. Roosevelt

Chain-of-Thought:
* Question: Who was president of the U.S. when superconductivity was discovered?
* Answer: Superconductivity was discovered in 1911 by Heike Kamerlingh Onnes. Woodrow Wilson was president of the United States from 1913 to 1921. So the final answer (the name of the president) is: Woodrow Wilson.

* Question: Who was president of the U.S. when superconductivity was discovered?
* Are follow up questions needed here: Yes.
* Follow up: When was superconductivity discovered?
* Intermediate answer: Superconductivity was discovered in 1911.
* Follow up: Who was president of the U.S. in 1911?
* Intermediate answer: William Howard Taft.
* So the final answer is: William Howard Taft.

Intermediate answers can be provided by means of retrieval augmented generation, using follow up questions as search queries. 

If the model reaches a final (wrong) answer, the outputs is only kept up to the first answer. The final answer is only accepted when it is the next being produced.

Here, I apply SelfAsk as well as summarization to a collection of transcripts to address questions such as:
- "How many times is water mentioned in the text?"
- "Tell me everything I need to know about the size of animals": i.e. questions that require the LLM to understand that summarization must be leveraged (i.e. SelfAsk is used more as an Agent)

I find that the most effective method for address this is to perform an initial retrieval of past examples to create a few shot prompt. This allow for both
- understanding the format of the SelfAsk/Summarizer Agent
- Understanding the type of reasoning beyond different types of questions.

The entire reasoning process is shown to verify whether the final response is to be considered as LLM hallucination.

As an experiment, the question is also addressed directly, with the cumulative retrieved documents, without intermediate question-answering.

# Different types of hallucinations can occur
* Useless Follow up questions get proposed (e.g. they merely rephrase the initial question), likely resulting in the final answer to be "I don't know". This can only be improved by correctly retrieving the appropriate few shot examples.
* Useful Follow up questions get proposed, but yield wrong intermediate answers, then the final answer gets biased to the wrong intermediate information and therefore results wrong as well. This is mitigated by asking to quote the text directly (which immediately reveals hallucinations when present).

# Future Directions
* Improving few shot retrieval
* Addressing questions that leverages the reasoning that emerges from the text(s), but requires generalization capabilities to cases that are not directly mentioned in the text. E.g.: "How would a giraffe deal with gravity?" (The text mentioning how gravity affects different animals, but not giraffes specifically)
* Adding an automatic hallucination detection mechanism that alerts the user when the answer is not to be trusted (and potentially flags the question as one that requires a dedicated few shot example)
