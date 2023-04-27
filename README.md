# gpt-stories: Using GPT-neo to create stories from a text prompt


Large language models like those in the GPT suite have no concept of facts or
relations; they simply follow a prompt with the statistically most likely continuation. This
leads to contradictions and a loss of coherence as the model can’t detect when a story
wouldn’t make sense to a human reader. In addition, large language models have no
“memory” of the story outside the current context window, leading to even more
contradictions and incoherence as later events in the story fail to relate to early ones.

To improve the coherence of large language model generated stories, our system
uses a knowledge graph. A knowledge graph is a concise means of representing
relationships between and facts about entities. By using a knowledge graph, we can
detect and prevent contradictions and the introduction of unrelated entities. Using a
knowledge graph also effectively expands the language model’s context window, as the
entire knowledge graph, rather than just the limited prompt, can be used for scoring.

## The System
Our story generation system uses GPT-Neo paired with a custom-built knowledge graph
generation system. GPT-Neo is used to generate candidate continuations for the story, and the
knowledge graph is used to score and select the best candidate.

### Building and Updating the Knowledge Graph
Our knowledge graph generator first uses spaCy to extract entities, disambiguate pronouns, and
find semantics-agnostic relations. Then it uses that information to generate more verb-specific
relations with VerbNet.

### Generating Candidate Continuations
Our system generates candidate continuations using GPT-Neo. We experimented with two
methods of generating a prompt:

1. Simply use the last `n` tokens of the story, 
2. The second method was to use the knowledge graph to form the first `j` tokens of the
prompt and the story to form the last `n-j` tokens.

### Scoring
We developed four distinct knowledge-graph-based scoring functions to compare to a default of
scoring all candidates equally well.

| name | description |
| ---- | ----------- |
| kg_scoring_fn | Our first scoring function is designed to reward reusing entities from the knowledge graph and penalize adding new entities and introducing contradictions. The score is calculated by simply subtracting the number of new entities and contradictions from the number of reused entities. Sentences that are exact repetitions of each other are also given an extremely low score, though in practice that did little to prevent repetitive stories as sentences with slight differences fail to be penalized. |
| kg_scoring_fn2 | Our second scoring function is the simplest. It rewards reusing entities by returning the number of entities from the knowledge graph that are also mentioned in the candidate. |
| kg_scoring_fn3 | Our third scoring function rewards reusing entities and penalizes adding new entities by calculating an F1 score. Entities in both the knowledge graph and the candidate are considered true positives, and entities in one but not the other are false positives (if they are in the candidate only) and false negatives (if they are in the knowledge graph only). | 
| kg_scoring_fn4 | Our third scoring function rewards reusing entities and penalizes adding new entities by calculating the BLEU score between the entities in the knowledge graph and the entities in the candidate. |


---
### Contributors:
Developed by students at Georgia Institute of Technology:
- Barun Das <bdas31@gatech.edu>
- Lasya Venneti <svenneti3@gatech.edu>
- Kaylah Facey

                      
