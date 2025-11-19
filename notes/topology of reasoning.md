## topology of reasoning

**method**

1. each question the model generate reasoning steps separated by /n, r_t
2. for each such step r_t at a chosen layer l, compute the mean hidden state over its tokens
3.  run k means over all step representation, and each centroid becomes a node, which is supposed to capture similar computational motifs
4. For each question, map the step to nearest node, connect them in order to obtain edges.
5. **cycle detection ratio** is the proportion of reasoning graph that contain at least one cycle across all samples, **cycle count** is the maximum of repeated visit to any single node
6. Define **daimeter** as the maximum shortest path distance. Large diameter indicates means the graph explore a wider range of potential reasoning nodes during inference.
7. **Small world index** compares clustering and path length against random graph, high index means dense local clusters and short global connections, which means more efficient network.

**discoveries**

1. more meaningful cycles than base model, avg 5 cycles per sample
2. cd ratio increases with task difficulty
3. not all cycles are good
4. **Effective models repreatedly revisit key reasoning states to refine, yet degenerate cycles don't help.**
5. reasoning models have larger diameter at all layers
6. diameters grow in deeper layers
7. 32b largest, showing correlation between exploration breath and performance
8. higher clustering coefficient, longer path length
9. Better reasoning dataset induce broader exploration, and more reflective/cyclic behavior



**Empirical Topology of LRMs vs Base Models**

- LRMs show:
    - higher **cyclicity** (reflective loops),
    - larger **diameters** (broader exploration),
    - stronger **small-world properties** (clustered but well-connected).
- These properties amplify with harder tasks and larger models and correlate with accuracy.

reasoning graph metric can be used as a way to evaluate reasoning focused training data

## geometric of self verification

Train a model task specific on countdown, reward for formatting and answer correctness.

**mode collapse**, model produces output that are less diverse than expected. Leverage this fact to produce this model that consistently generate structured cot that we can parse. (reliable?)

Then since we can parse the answers, hidden state right before it produces this or not

**Top down**





## reasoning vector

activation?
