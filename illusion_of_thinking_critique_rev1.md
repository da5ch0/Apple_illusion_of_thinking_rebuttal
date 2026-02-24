# The Illusion of Measuring Thinking: A Methodological Critique of Shojaee et al. (2025)

**Draft — dash [da5ch0] — February 2026**
**Rev 1: Editorial pass. Quotes verified against source text. Strikethroughs mark precision gains. --S**

---

## Abstract

Shojaee et al.'s "The Illusion of Thinking" (Apple, June 2025) presents empirical findings on the complexity-dependent performance collapse of Large Reasoning Models (LRMs) across controllable puzzle environments. While the paper's trace analysis methodology and three-regime characterization represent genuine contributions to LRM evaluation, its central claims — that reasoning in these models is fundamentally illusory and that current approaches ~~face~~ "may be encountering fundamental barriers to generalizable reasoning" — are undermined by two critical methodological omissions. First, the study exclusively evaluates monolithic chain-of-thought architectures (single thinking block followed by answer), omitting interleaved reasoning architectures capable of closed-loop self-referential processing during generation. Second, the experimental prompts provide zero metacognitive scaffolding, testing models at baseline activation without any instruction to monitor, evaluate, or iteratively refine their own intermediate outputs. These omissions are not minor oversights — they exclude the primary mechanisms by which modern reasoning architectures achieve sustained performance on complex sequential tasks. When situated against both the existing rebuttal literature and recent advances in self-referential reasoning, the paper's findings describe a real but narrowly scoped phenomenon that its title and conclusions ~~dramatically overstate~~ significantly overextend.

---

## 1. Summary of Contributions

Shojaee et al. make several contributions worth acknowledging before addressing their limitations.

The use of controllable puzzle environments (Tower of Hanoi, Checker Jumping, River Crossing, Blocks World) as complexity-parameterized testbeds is a sound methodological choice. Unlike standard mathematical benchmarks, these environments allow precise manipulation of compositional depth while maintaining consistent logical structure and enabling deterministic step-by-step validation via simulators.

The **three-regime characterization** — low complexity where standard LLMs match or outperform LRMs, medium complexity where reasoning overhead produces measurable advantage, and high complexity where both model types collapse — is empirically well-supported and practically useful for deployment decision-making.

The **reasoning trace analysis**, in which intermediate solutions are extracted from thinking tokens and validated against puzzle simulators, is the paper's most novel methodological contribution. The demonstration that correct solutions appear early and are then abandoned ("overthinking") at low complexity, while emerging only after extensive exploration at medium complexity, provides structural insight into the dynamics of chain-of-thought reasoning that final-answer accuracy metrics cannot capture.

These are genuine advances in LRM evaluation methodology. The critique that follows concerns not the data, but the interpretive framework and the scope of what was tested.

---

## 2. What Was Not Tested

### 2.1 Architectural Omission: Interleaved Reasoning

The models evaluated — Claude 3.7 Sonnet Thinking, DeepSeek-R1, o3-mini — all employ a **monolithic reasoning architecture**: a single extended thinking phase generates a complete chain-of-thought, followed by a final answer. This is structurally an open-loop planning process. The model reasons about the problem, commits to a plan, and then executes. It cannot observe its own output during generation and adjust course based on what it has already produced.

This architecture was state-of-the-art at the time of data collection (early-to-mid 2025). It is not state-of-the-art at the time of the paper's broad conclusions about "the true reasoning capabilities" of language models more generally. Interleaved reasoning architectures — in which thinking and output generation alternate in a closed-loop structure — represent a qualitatively different approach to sustained sequential reasoning. In these architectures, the model can:

- **Observe its own intermediate outputs** and assess their correctness against its evolving understanding of the problem state.
- **Self-correct in real time** rather than committing to a complete reasoning trace before producing any output.
- **Accumulate verified progress** as a foundation for subsequent reasoning steps, rather than maintaining all state in a single forward pass.
- **Self-reinforce successful strategies** by treating observed correct intermediate outputs as confirmation signal — a form of in-context reinforcement learning in which the model's own verified successes steer subsequent reasoning toward productive actions, while observed failures trigger reassessment of initial heuristic leanings that require more deliberate processing.

This is not a minor architectural variant. The distinction between open-loop planning and closed-loop control is foundational in control theory and systems engineering, and the performance implications are well-understood: closed-loop systems exhibit fundamentally different robustness, error-correction, and scaling properties than open-loop systems operating on identical hardware (Åström & Murray, 2008). Testing only open-loop planners and generalizing to "reasoning models" as a class is an architectural sampling error with direct consequences for the validity of the paper's central claims.

It bears noting that several of the present authors previously published GSM-Symbolic (Mirzadeh et al., 2025), which demonstrated the fragility of LLM mathematical reasoning under symbolic perturbation — findings that contributed directly to the field's motivation for developing reasoning models with explicit self-reflection and iterative self-correction. The architectural innovations catalyzed in part by their own prior work are precisely the innovations excluded from their evaluation here.

The "Rethinking the Illusion of Thinking" replication study (Arxiv, July 2025) provides partial empirical confirmation: by introducing incremental stepwise prompting and agentic collaborative dialogue — both of which approximate closed-loop processing within an open-loop architecture — the authors demonstrated that previously reported collapse thresholds shift meaningfully, though models still exhibit degradation at moderate complexity (~8 disks). This suggests the collapse threshold is not a fixed property of reasoning models as a class, but a parameter sensitive to the degree of self-referential feedback available during reasoning.

### 2.2 Elicitation Omission: Zero Metacognitive Scaffolding

The experimental prompts used by Shojaee et al. are reproduced in full in their appendix. Every system prompt begins with the same frame:

> *"You are a helpful assistant. Solve this puzzle for me."*

This is followed by a clear problem description, movement rules, formatting requirements, and worked examples. The prompts are technically well-constructed. They are also **entirely devoid of metacognitive instruction**. At no point is the model directed to:

- Monitor its own intermediate states for consistency.
- Evaluate whether a partial solution remains on a viable path before continuing.
- Learn from observed failures within the current reasoning episode.
- Treat successful intermediate steps as confirmation signal for the strategy that produced them.

This omission is consequential because it means the models were tested at **baseline metacognitive activation** — ~~the cognitive equivalent of measuring an athlete's performance without informing them they are being timed, or that the goal is to run fast~~ the equivalent of measuring a sprinter's time after instructing them only to "get to the other end of the track" — the goal is communicated, but no instruction is given to run fast, monitor form, adjust stride, or treat the task as a performance.

The significance of this omission is well-established in the adjacent literature on game-playing AI and reinforcement learning. From the earliest successes in learned game play (Mnih et al., 2015; Silver et al., 2016), a core design principle has been explicit: agents must be structured to observe the consequences of their actions, evaluate outcomes against objectives, and update their policy accordingly. ~~This evaluate-and-adapt loop is not incidental to performance — it is the mechanism by which performance emerges.~~ While those systems implement this loop at training time, interleaved reasoning architectures implement an analogous loop at inference time — the structural principle is the same even as the timescale differs. Omitting it and then characterizing the resulting behavior as a ceiling on capability is a measurement error, not a discovery about the system's limits.

To state the omission concretely: the inclusion of a directive as minimal as *"observe what works and learn from what doesn't"* — a metacognitive scaffold so basic it would be considered negligent to omit in a reinforcement learning experimental design — would have fundamentally altered the conditions of the experiment. Whether it would have altered the *results* is an empirical question the paper does not address, because the condition was never tested.

Goedecke (2025) independently identified a related phenomenon in his analysis of DeepSeek-R1 reasoning traces: models encountering high-complexity problems made explicit in-trace decisions that the problem was too large to enumerate, and shifted strategy toward shortcut-seeking rather than systematic execution. This is not a failure of reasoning — it is reasoning *about* reasoning, specifically about resource allocation. But the experimental framework has no mechanism to distinguish "the model cannot solve this" from "the model decided not to attempt this approach," because the prompt provides no scaffolding for the model to reflect on or communicate that distinction.

---

## 3. Existing Contradictions in the Literature

The Apple paper's strongest claims — that LRMs ~~face~~ "may be encountering fundamental barriers to generalizable reasoning" and that their findings "challenge prevailing assumptions about LRM capabilities" — are already in tension with multiple findings in the concurrent literature.

**Lawsen & Opus (2025)** demonstrate three specific flaws in the experimental design: (1) Tower of Hanoi instances at high disk counts risk exceeding model output token limits, with models explicitly acknowledging these constraints in their outputs; (2) the automated evaluation framework fails to distinguish between reasoning failures and practical output constraints, misclassifying truncated-but-correct reasoning as failure; and (3) River Crossing instances at N ≥ 6 with boat capacity k = 3 are mathematically impossible, yet models are scored as failing to solve them. When models were prompted to produce generating functions (e.g., Lua code) rather than enumerating every step, performance on previously "collapsed" problems recovered substantially — indicating the models possessed algorithmic understanding that the evaluation framework was unable to detect.

**The "Rethinking" replication (Arxiv, July 2025)** replicated two of the original benchmarks with improved prompting methodology (incremental stepwise prompting and agentic collaborative dialogue). Their findings split the difference: failures were not purely attributable to output constraints (contra Lawsen & Opus) nor purely to reasoning limitations (contra Shojaee et al.). Models still degraded at moderate complexity, but the collapse threshold shifted, and the "complete collapse" framing was not reproduced under improved experimental conditions. The authors specifically note that "previously reported failures solving the Towers of Hanoi were not purely result of output constraints, but also partly a result of cognition limitations: LRMs still stumble when complexity rises moderately (around 8 disks)." This is a more measured and empirically defensible conclusion than "the illusion of thinking."

**Goedecke (2025)** argues that the decrease in thinking tokens at high complexity — the paper's most provocative finding — reflects models making rational decisions to abandon brute-force enumeration rather than hitting a reasoning wall. His analysis of DeepSeek-R1 traces shows the model explicitly recognizing the infeasibility of complete enumeration and attempting strategic alternatives. This reframes the "giving up" phenomenon as a metacognitive strategy decision rather than a capability ceiling.

Multiple commentators have noted the absence of **human baselines**. Tower of Hanoi with 10 disks requires 1,023 sequential moves with perfect state tracking. The number of humans who could execute this flawlessly with pen and paper is not zero, but it is small, and their error profiles would likely resemble the models' — consistent execution for many steps followed by state-tracking failures. Without this comparison, it is impossible to determine whether the observed collapse reflects a limitation specific to language model architectures or a more general property of serial reasoners operating at the edge of their state-tracking capacity.

---

## 4. The Broader Interpretive Error

The methodological omissions described above — testing only open-loop architectures and providing zero metacognitive scaffolding — combine to produce a systematic underestimate of LRM capability that the paper then interprets as a characterization of LRM limits. This is the core interpretive error.

To illustrate by analogy: if one were to evaluate human mathematical ability by presenting unseen problems to subjects ~~in a silent room with no scratch paper, no feedback on intermediate work, and no instruction to check their answers~~ who are given scratch paper but told they must write their entire solution in a single unbroken pass — no erasing, no re-reading what they've already written, no pausing to verify intermediate steps — the resulting performance profile would show a clean complexity-dependent collapse — easy problems solved, medium problems partially solved, hard problems failed. One could then publish a paper titled "The Illusion of Mathematical Thinking in Humans" and present the collapse threshold as evidence of fundamental barriers to human mathematical reasoning. The paper would be methodologically clean, empirically reproducible, and interpretively wrong — because the experimental conditions suppressed the very mechanisms (~~external memory,~~ self-monitoring, iterative refinement, adaptive strategy) by which humans actually perform sustained mathematical reasoning.

This is what Shojaee et al. have done with language models. They have measured reasoning under conditions that suppress the mechanisms that enable reasoning, and concluded that reasoning is limited.

The phenomenon they document is real: models at baseline activation in open-loop architectures do exhibit complexity-dependent collapse with the failure signatures they describe. This is a useful empirical finding. But the leap from "models fail under these conditions" to "these models face fundamental barriers to generalizable reasoning" requires demonstrating that the conditions tested are representative of the models' operational capabilities. They are not. The conditions tested represent a lower bound on model performance, presented as a ceiling.

---

## 5. Conclusion

The core empirical observations in Shojaee et al. — three performance regimes, overthinking at low complexity, execution fidelity degradation over long sequences, and cross-puzzle performance inconsistency suggesting training-distribution dependence — are reproducible and valuable contributions to the evaluation literature. The trace analysis methodology, in particular, should become standard practice.

The interpretive framework built around these observations, however, is not supported by the experimental design. Two specific omissions — the exclusion of interleaved reasoning architectures and the absence of metacognitive scaffolding in experimental prompts — mean that the paper evaluates a restricted subset of model capability under conditions that ~~actively suppress~~ do not engage the primary mechanisms by which modern reasoning systems achieve sustained complex performance. The resulting characterization is a lower bound, not a limit.

The title "The Illusion of Thinking" implies that reasoning in these models is fundamentally illusory. The evidence presented demonstrates only that reasoning is *bounded* under specific conditions — conditions that the existing rebuttal literature and concurrent architectural advances have already shown to be relaxable. ~~A more defensible title would be "The Boundaries of Thinking,"~~ A more defensible title would be "The Boundaries of Unscaffolded Reasoning," but that, of course, would not have generated the same discourse.

The field would benefit from evaluation frameworks that test reasoning models under conditions approximating their operational deployment — including modern architectures with self-referential feedback, metacognitive prompting that reflects actual use patterns, and human baselines that contextualize the observed failure modes. Until such evaluations are conducted, claims about "fundamental barriers to generalizable reasoning" remain, themselves, something of an illusion.

---

## References

Åström, K. J., & Murray, R. M. (2008). *Feedback Systems: An Introduction for Scientists and Engineers*. Princeton University Press.

Goedecke, S. (2025). The illusion of "The Illusion of Thinking." Blog post. https://www.seangoedecke.com/illusion-of-thinking/

Lawsen, A. & Opus, C. (2025). The Illusion of the Illusion of Thinking: A Comment on Shojaee et al. (2025). arXiv:2506.09250.

Mnih, V., Kavukcuoglu, K., Silver, D., Rusu, A. A., Veness, J., Bellemare, M. G., ... & Hassabis, D. (2015). Human-level control through deep reinforcement learning. *Nature*, 518(7540), 529–533.

Mirzadeh, S. I., Alizadeh, K., Shahrokhi, H., Tuzel, O., Bengio, S., & Farajtabar, M. (2025). GSM-Symbolic: Understanding the Limitations of Mathematical Reasoning in Large Language Models. *ICLR 2025*.

[Rethinking] (2025). Rethinking the Illusion of Thinking. arXiv:2507.01231v1.

Shojaee, P., Mirzadeh, I., Alizadeh, K., Horton, M., Bengio, S., & Farajtabar, M. (2025). The Illusion of Thinking: Understanding the Strengths and Limitations of Reasoning Models via the Lens of Problem Complexity. Apple Machine Learning Research.

Silver, D., Huang, A., Maddison, C. J., Guez, A., Sifre, L., van den Driessche, G., ... & Hassabis, D. (2016). Mastering the game of Go with deep neural networks and tree search. *Nature*, 529(7587), 484–489.
