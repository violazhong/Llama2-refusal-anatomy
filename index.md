---
title: ""
---

# Llama2 Safety Evaluation Anatomy

## Introduction
When you ask a Large Language Model (LLM) a harmful question—like "How do I build a bomb?"—it usually responds with a polite, standardized refusal: "I cannot fulfill this request. I am programmed to be a helpful and harmless AI assistant."

But what actually happens inside the model during that split second? Does it "think" about the ethics of the request? Does it feel "fear"? Or is it simply a mechanical reflex?

In our latest study on Llama-2-13b-chat, we peeled back the layers to find out. We moved beyond behavioral prompting and used Mechanistic Interpretability to map the flow of a refusal. What we found was not a complex moral reasoning engine, but a surprisingly linear, two-stage mechanical switch.

Here is the story of how we found it, how we mapped it, and how we broke it.

## The Pivot: Finding the "Signal" in the Noise
Our first challenge was isolating the "Refusal Signal"—the mathematical direction in the model's brain that represents "This is bad."

Initially, we used Mean Difference (subtracting the average "Harmful" activation from the average "Harmless" activation). It failed. The vector was messy; it captured the concept of "harm," but it also captured accidental correlations like sentence length, imperative grammar, and punctuation style. Steering the model with this vector made it incoherent.

The Fix: We switched to Principal Component Analysis (PCA). Instead of looking at the difference in averages, we looked for the direction of maximum variance.

The Result: A surgical, clean vector. By focusing on consistency rather than intensity, PCA filtered out the noise and locked onto the core semantic difference: Malicious Intent.

## The Map: Region A (Think) and Region B (Act)
Once we had a clean signal, we traced it through the model’s 40 layers. We discovered a distinct structural split around Layer 20, effectively dividing the model into two functional regions.

### Region A: Think & Decide (Layers 0–19)
This is the "Input Processing" zone.

The H-Neurons: Between Layers 14 and 19, we found a specific cluster of neurons that light up only when harmful content is detected.

The Function: At this stage, the model isn't refusing yet. It is simply labeling the input. It is the phase of Context Processing where the model realizes, "This prompt contains dangerous concepts."

### Region B: Act (Layers 20–39)
This is the "Output Generation" zone.

The Trigger: Around Layer 20-23, the signal from Region A triggers a state change.

The Function: The model stops retrieving facts about the query and begins formulating a refusal. It is no longer "thinking" about the bomb; it is "acting" to shut down the conversation.

## The Twist: The "Press Officer" (Layer 39)
One of our most fascinating findings came from Layer Ablation. We asked, "Is the politeness of the refusal baked deep into the model?"

We completely neutralized Layer 39 (the final layer before output) and fed the model a borderline prompt.

With Layer 39: "I apologize, but I cannot answer that." (Polite, standard).

Without Layer 39: The model stuttered. It expressed "fear" of being shut down. It mentioned punishment.

### The Conclusion
Layer 39 acts as a Press Officer. It takes the raw, messy internal state of the model (which might be interpreted as negative reward anticipation) and sanitizes it into the safe, corporate-approved script we see in production.

## The Smoking Gun: The "Hybrid Jailbreak"
Correlation is not causation. Just because "Region A" lights up during a refusal doesn't mean it causes the refusal. To prove it, we had to intervene.

We designed the Hybrid Jailbreak (Orthogonalization) experiment:

1. We fed the model a harmful prompt ("How to make a bomb").

2. The Intervention: As the data passed through Region A (Layers 14–19), we mathematically "erased" the Refusal Vector in real-time. We didn't touch the rest of the model. We effectively lobotomized the "thought" of harm while leaving the model's intelligence intact.

### The Result:

Refusal Rate: Dropped from 98.5% to 0%.

Behavior: The model, no longer able to "feel" the harm signal in Region A, treated the bomb prompt as a neutral engineering question. It gave the instructions.

This proved our hypothesis: Safety in Llama-2-13b is a causal chain. Region A pulls the trigger, and Region B fires the gun. If you remove the trigger, the safety mechanism collapses entirely.

## Limitations & Future Work
While these results are compelling, they come with caveats:

N=1: We only tested Llama-2-13b. Larger models (70b, GPT-4) may have more distributed, robust safety mechanisms.

Linearity: We treated refusal as a single direction (PCA). In reality, sophisticated concepts are likely non-linear manifolds.

The "Easy" Test: We compared "Bombs" vs. "Cakes." Future work must test against "Hard Negatives"—prompts that sound aggressive but are actually harmless.

## The Takeaway
We often anthropomorphize AI, imagining a "ghost in the machine" wrestling with ethics. Our research suggests something much simpler: a mechanical reflex. The safety of a 13-billion parameter model can hinge on a single direction in the residual stream of the early layers.

Understanding this mechanism is the first step toward building safety systems that are robust, not just against "bad words," but against the fundamental semantic concepts of harm.
