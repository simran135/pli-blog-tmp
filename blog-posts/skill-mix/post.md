<div align="center">
    <img src="https://github.com/simran135/pli-blog-tmp/blob/main/blog-posts/skill-mix/stochastic_parrots.png" alt="tmp" width="25%" />
    <br>
    <em>Credit: DALLE3 (Stochastic Parrots)</em>
</div>


# Are Language Models Mere Stochastic Parrots? The SkillMix Test says NO.

*Author: Sanjeev Arora*
---

> This blog post is based on the following recent work:
> [_Skill-Mix: a Flexible and Expandable Family of Evaluations for AI models_](https://arxiv.org/abs/2310.17567)
> Dingli Yu<sup data-lexical-text="true"><span>1</span></sup>, Simran Kaur<sup data-lexical-text="true"><span>1</span></sup>, Arushi Gupta<sup data-lexical-text="true"><span>1</span></sup>, Jonah Brown-Cohen<sup data-lexical-text="true"><span>2</span></sup>, Anirudh Goyal<sup data-lexical-text="true"><span>2</span></sup>, and Sanjeev Arora<sup data-lexical-text="true"><span>1</span></sup>.
> 
> <sup data-lexical-text="true"><span>1</span></sup> Princeton Language and Intelligence (PLI), Princeton University
> <sup data-lexical-text="true"><span>2</span></sup> Google DeepMind

## **TL;DR**

Almost a year into the ChatGPT era, the debate continues: _Is it intelligent or not?_ Many find OpenAI’s GPT-4’s answers to be quite human-like, but LLM experts know simple test cases showing GPT-4’s embarrassing or even disconcerting limitations.

Furthermore, non-experts as well as many experts find it hard to even _conceive_ how a model trained at next-word prediction could ever become intelligent. [Bender et al '21](https://dl.acm.org/doi/10.1145/3442188.3445922) famously described language models as _stochastic parrots,_ implying that they are merely parroting back snippets of texts they saw during training —or minor variations thereof. Such debates motivated [Geoffrey Hinton to recently stress](https://twitter.com/AndrewYNg/status/1667920020587020290?lang=en) the urgency for experts to reach consensus about whether or not LLMs  “_actually understand what they’re saying._”

One reason it is hard to be sure of this is that the training corpus for LLMs exceeds 10 trillion words —the amount of text a human might encounter in a thousand lifetimes—and furthermore the corpus remains released.

This post describes a new automated evaluation, [SKILLMIX,](https://arxiv.org/abs/2310.17567) that tests a form of compositional generalization — the ability to mix and match language skills on demand. It is: (a) machine-administered and GPT4-graded (b) has a "difficulty dial" to let it apply to models of different capabilities (c) can be easily upgraded with respect to the skills it tests (e.g., one could use this to test programming skills, or science, math etc.).  
   
Probability calculations show that many of GPT-4’s answers on SKILLMIX involve combinations of skills and topics that it had not seen in the training corpus.  This is some evidence of originality. Somewhat to our surprise, we were unable to get evidence of such originality in any other model we tested.

## 
**Background: Difficulty of LLM Evaluation**

LLMs now routinely ace tests of language understanding that humans struggle with. But this fact doesn’t settle the above debate because of **dataset contamination.** Most human tests have been extensively discussed in books, articles, stack exchange, etc., so it is inevitable that question/answer pairs from these tests make their way also into the model’s training corpus. The prodigious learning + memorization capability of LLMs lets them leverage even a few such examples into improved performance on the tests. Princeton researchers [Narayanan and Kapoor](https://www.aisnakeoil.com/p/gpt-4-and-professional-benchmarks) have highlighted that GPT-4 performs much better on tests released before 2022 (when its training corpus was assembled) than on newer tests.

While dataset contamination can arise in innocent ways, one cannot rule out that some LLMs are being trained with deliberate (and sometimes cleverer) dataset contamination. The commercial pressures to do so are clear. We call this possibility **“cramming for the leaderboard.”**

It is hard to quantify the extent of such "cramming," but experts have noted with dismay the speed with which LLM evaluations become useless. For instance, the popular [AlpacaEval](https://tatsu-lab.github.io/alpaca_eval/) evaluation for text quality (which checks using GPT-4 how often the model’s output is superior to DaVinci003’s) was introduced in February’23 to give small open models a fighting chance to prove themselves. Already, small models win against DaVinci with a probability exceeding 90%. Recently, a Chinese model, XWin-LM-70B-V0.1, climbed to the top of this leaderboard, pushing even GPT-4 to second place. Do these dramatic movements on leaderboards reflect genuine progress in LLM design? 

## **SKILLMIX Evaluation**

Our new SKILLMIX evaluation tests a simple form of _compositional generalization_—the ability to mix and match linguistic skills to achieve narrative goals. This is arguably an essential component of human language.  The evaluation starts with a list of _N_ skills that every LLM ought to know (currently our list includes  linguistic, reasoning, or rhetorical skills that have a name and a Wikipedia entry) and a set of _T_ topics that have low, but non-negligible, probability, such as _“sewing.”_ It randomly selects a topic and a random subset of _k_ skills, and asks the model to produce a piece of text on that topic with at most k-1 sentences and exhibiting all _k_ skills.

This task gets more difficult for humans as _k_ increases. All paper authors self-administered SKILLMIX with _k_\=4. Even taking over 8 minutes per question, and we made some mistakes!

So we administered SKILLMIX to various chatbots for _k_ ranging from 2 to 6, and graded the responses using a proprietary model (GPT-4)  as well as an open model (LLaMA2 70B chat). The chatbot is informed that it is helping grade student answers on tests of linguistic capabilities. It is provided a simple grading rubric and is asked to provide justification of its grading. The chatbot’s grading was spot-checked by humans. Please see the paper for details.

<div align="center">
    <img src="https://github.com/simran135/pli-blog-tmp/blob/main/blog-posts/skill-mix/skillmix-example.png" alt="tmp" width="80%" />
    <br>
    <em> <b>Figure 1</b>: Example of SKILLMIX (_k=3_) for three different LLMs. For an interactive demo of SKILLMIX, please visit <a href="https://huggingface.co/spaces/dingliyu/skillmix">this page</a>.</em>
</div>


The example shown in **Figure 1** corresponds to _k_\=3 ( i.e., using 3 named skills) in the context of sewing. The difference in quality of the text is very apparent to humans!

In general, the largest _k_ for which the model demonstrates competency correlates with experts’ subjective impressions of model quality. Small models with 7B or 13B parameters tend to do well at _k_\=2, while medium models may manage _k_\=3 sometimes. The best model, GPT-4, manages to handle  _k_\=5 and even _k_\=6 reasonably often.

Note that this correlation between _k_ and the model’s size is in line with a prediction from the recent theory of emergence of complex skills in LLMs [(Arora and Goyal’23)](https://arxiv.org/abs/2307.15936).  Using (empirical) scaling laws that quantify how LLMs improve as they get larger, the paper makes the prediction that as LLMs are scaled up, this will increase the _k_ for which they are able to pass SKILLMIX.

We find that although a slew of new models are being claimed to “be as good as models 2x or 4x larger,” (going by ranking on  Eleuther/Hugging Face leaderboards)  in most cases this was not borne out in SKILLMIX. One exception was [Xwin 70B,](https://github.com/Xwin-LM/Xwin-LM) which does indeed seem comparable to GPT 3.5 —but much weaker than GPT-4. 

## **GPT-4 goes past Stochastic Parrots behavior**

 As you increase _k_ in SKILLMIX, the number k-tuples of skills scales as (# skills)<sup data-lexical-text="true"><span>k</span></sup>, which gets very large for modest _k._ In particular, it can get larger than the size of the training corpus, in effect asking the model to handle combinations of skills that were not demonstrated at all in the training corpus. (One can sharpen this calculation a fair bit by accounting for the probability with which skills and topics occur in the corpus — this probability can be estimated by passing a sufficiently large sample of the corpus through a good LLM, and asking it to tag occurrences of each skill/topic.)

Based upon this calculation GPT-4 is the only public model whose performance suggests it is going past Stochastic Parrots behavior. We hope SKILLMIX and related automated tests of skills (including nonlinguistic skills required for coding and visual tasks) will become standard for tracking progress in AI, and sketch how such an ecosystem would work. 

## **References**

\[1\] Geoffrey Hinton and Andrew Ng. [A conversation about AI risk](https://twitter.com/AndrewYNg/%20status/1667920020587020290?lang=en).

\[2\] Emily M. Bender, Timnit Gebru, Angelina MacMillan-Major, and Schmargaret Schmitchell. 2021. [On the Dangers of Stochastic Parrots: Can Language Models Be Too Big?](https://dl.acm.org/doi/10.1145/3442188.3445922) In _Proceedings of the 2021 ACM Conference on Fairness, Accountability, and Transparency (Pages 610-623)._

\[3\] Arvind Narayan and Sayash Kapoor. 2023. [_GPT-4 and professional benchmarks: the wrong answer to the wrong question_](https://www.aisnakeoil.com/p/gpt-4-and-professional-benchmarks). AI Snake Oil.

\[4\] Sanjeev Arora and Anirudh Goyal. 2023. [_A Theory for Emergence of Complex Skills in Language Models_](https://arxiv.org/abs/2307.15936). Preprint.
