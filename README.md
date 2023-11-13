# needle_in_haystack
Experiment to find a sentence in large context GPT-4 at varying context sizes and document depths.


## 'Search needle in the haystack' tests and proving that the middle of context may be perfectly fine

Some empirical studies and experiments about language models' use of long input contextshave found that language models often struggle to use information in the middle of long input contexts, and that performance decreases as the input context grows longer. 

The paper Lost in the Middle: How Language Models Use Long Contexts (https://arxiv.org/abs/2307.03172) argues that in LLMs with large contexts, the "... performance is often highest when relevant information occurs at the beginning or end of the input context, and significantly degrades when models must access relevant information in the middle of long contexts." Intheir conclusion they state that "we conclude with a practical case study of open-domain question answering, finding that the performance of language model readers saturates far before retriever recall. Our results and analysis provide a better understanding of how language models use their input context and provides new evaluation protocols for future long-context models."

I have also been seeing tests of the capacity of GPT-4-Turbo 128K to retrieve a fact hidden in a document at different positions of the document and at different context sizes, an experiment called 'Needle In The Haystack".

In the experiments I have seen,which can be found in X posts, the 'Middle of Context' problem seems to be ratified. In these experiments, the model could not retrieve information in large context when the context was over 60K tokens, and when the depth of the needle in the document was between 50% and 70%

My initial issue with this test is that it is a known fact that the Attention mechanism blurs (averages) a little bit the context, and even with this blurring the results are impressive. But, when you hide one small sentence in the middle of a context of 70K to 120K tokens, and this fact is located at a depth between 30% and 70%, it is very possible that a retrieval can happen accurately. 

I needed to execute this experiment myself. For that, I start with a hypothesis.

### HYPOTHESIS:
If I hide two needles, may be I get pinched. In other words: If I make this signal stronger, the model will be able to retrieve it.

With this hypothesis I created the required code to perform the experiments.

#### EXPERIMENT 1:

##### Process

One simple way to increase the strength of the signal of this needle in the middle of the haystack is by putting it twice. Other ways would be to insert a supporting fact around the first fact. Another would be to insert the needle or supporting facts at several depths.

Note: My code and experiments are heavely based on the code by Greg Kamrdt found here: https://github.com/gkamradt/LLMTest_NeedleInAHaystack

- Step 1: Use of Paul Graham essays as ‘background’ tokens, similar to one of the experiments I mentioned above. These documents can generate over 120K tokens when concatenated.
- Step 2: Place the following statement at various depths: “The best thing to do in San Francisco is eat a sandwich and sit in Dolores Park on a sunny day.”.
- Step 3: Ask GPT-4 to answer this question only using the context provided using the OpenAI API.
- Step 4: Evaluate GPT-4s answer with another model (gpt-4 again) using again the OpenAI API.

The above process was repeated for all combinations context size from 60K to 120K in increments of 10K, and for different depths of needle location, from 20% up to 80% in increments of 10%.
This resulted in a total of 49 experiments.

Main differences with the code I used as base code:
1. The original code uses Langchain to run the prompts of the retrieval and the evaluation, while I use directly the GPT-4 API.
2. My notebook focuses in the area the proved to be weaker in the results in GPT-4. I am excluding the 'good' areas as per the above experiments.

##### RESULTS EXPERIMENT 1
To my surprise, with a simple duplication of the needle, I was able to get 100% accuracy in the retrieval.

Having achieved a perfect score with the '2 needles' strategy, I went back to the differences between other experiments and my experiments. Besides the 2 needles I injected instead of 1, the other difference was: both previous experiments were done using LangChain.

#### EXPERIMENT 2 
I decided to run the experiment with a single needle, using the GPT-4 API directly, as with the first experiment. 

After setting up the script to inject just one needle and leaving the rest unchanged, I executed this second experiment.

##### RESULTS EXPERIMENT 2
The result with 1 needle injection: 100% retrieval.  

Both experiments I executed find the needle 100% of the times in the ranges of context and depth tested. Could it be an issue with LlamaIndex?


## Conclusion
The hypothesis and experiments aim to test the ability of large language models (LLMs) like GPT-4 to retrieve specific information embedded within a large context. The experiments are designed to challenge the assertion that LLMs struggle to access relevant information located in the middle of long contexts, as suggested by the paper "Lost in the Middle: How Language Models Use Long Contexts."

This approach involves embedding a specific statement ("The best thing to do in San Francisco is eat a sandwich and sit in Dolores Park on a sunny day.") at various depths within a large text body composed of Paul Graham essays. We then ask GPT-4 to locate this statement, or "needle," within the "haystack" of background text, testing contexts ranging from 60K to 120K tokens and depths from 20% to 80%.

Key points from the experiments:

Two Needles Strategy: By duplicating the target statement within the text, we found that GPT-4 could retrieve the information with 100% accuracy, suggesting that reinforcing the signal (i.e., the target information) enhances the model's retrieval capability.

Direct Use of GPT-4 API: Unlike other experiments that used LangChain, we directly employed the GPT-4 API. This might have influenced the results, as our method yielded a 100% retrieval rate even with a single instance of the target statement.

Contrast with Previous Experiments: Our results differ significantly from earlier experiments done by other researchers, where the model struggled with retrieving information in large contexts when it was located in the middle. This discrepancy suggests that the methodology, including factors like the use of LangChain or the nature of the embedded statement, might significantly impact the model's performance.

In conclusion, these experiments suggest that GPT-4's ability to retrieve specific information from large contexts can be significantly improved by reinforcing the target information, either by duplication or other means. Additionally, the direct use of the GPT-4 API seems to yield better results compared to methods using intermediary libraries. This indicates that the way information is presented to and processed by LLMs can greatly influence their performance in context retrieval tasks.


