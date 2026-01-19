---
title: ""
---

# Llama2 Safety Evaluation Anatomy

## TL;DR
I took a look into the internal mechanism of how a Llama2-13b model responds/refuses to safety-evaluation/harmful prompts. This model has been red-teamed iteratively and trained with safety fine-tuning and RLHF specifically targeting safety and helpfulness. But how does the model internalize these trainings?
