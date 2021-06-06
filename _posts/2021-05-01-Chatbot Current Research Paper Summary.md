---
tags: NLP chatbot research retnref Facebook wizard Xiaoice Wikipedia Microsoft dialo GPT plato Bsaidu blender
categories: NLP ChatBots
excerpt: A summary of Machine Learning focused chatbot research. Includes RenRef++, 🧙‍♀️ of Wikipedia, XiaoIce, DialoGPT, Blender, and PLATO-2.
header:
  teaser: "/assets/images/chatbot.png"
---

Below is my current (2021/05/18) summary of Machine Learning focused chatbot research. It is not close to a complete summary, but I will present some of the more influential papers within the research field of conversational chatbots I came across. Another note is I will assume prior knowledge of NLP and will not go into the mathematics of each paper. The goal of this summary is to highlight the novel ideas of each paper, you would be best to dissect the paper yourself if  your goal is deeper understanding.

Let's start with what is not in scope.

A distinction should be made between primarily rule-based chatbots and conversational chatbots.

Rule-based chatbots follow very structured and defined conversation flows. This isn't to say that they are not useful, in fact they are perfectly suited to answer simple queries for information about a business, with the benefit of getting one running within minutes using one of many open source frameworks, [BotPress](https://botpress.com/docs) being a prime example. 

Conversational chatbots typically understand the context and intent of a conversation, and are able to masquerade as a human typically with the use of machine learning models.

This article is focused on conversational chatbots.

## RetNRef++ 2018 Facebook

*[Retrieve and Refine: Improved Sequence Generation Models for Dialogue](https://arxiv.org/abs/1808.04776)*

Sequence generation models have several problems including but not limited to: generating short, generic sentences, toxic behaviour, and hallucinating information. A classic example of this is when trying to answer a question, a chatbot can always respond with "I don't know". Although contextually this works, it does not provide a very convincing experience for the user.

A retrieval system can produce very specific and useful information, with the huge caveat that it can only do this given it has the requested information. 

This paper's innovation was to include retrieval models to try and alleviate some of these problems.

Previously models which use two stages consisting of retrieval and then refinement have become very popular in the NLP space including their applications to [search engines](https://arxiv.org/abs/1705.07267) but at this time had not yet been applied to a dialogue context.

For the architecture they used a 2-layer LSTM with attention ([Seq2Seq model](https://arxiv.org/abs/1409.3215)) for the generator and a [Key-Value Memory Network](https://arxiv.org/abs/1606.03126) for the retriever.

Their approach is surprisingly simple, they take a standard generative model and concatenate the output of a retrieval model to its usual input, then train under this condition. They trained on the [ConvAI2 dataset](http://convai.io/).

![Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled.png](/jakyll/assets/Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled.png)

Key-Value Memory Network for Question Answering

![Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%201.png](/jakyll/assets/Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%201.png)

2-layer Seq2Seq model architecture

Practically this allows the model to have some form of memory to recall previous conversations and generate more relevant responses.

![Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%202.png](/jakyll/assets/Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%202.png)

You can see in this example dialogue that the characteristics in the two personas are directly used in the dialogue. Persona 1 is a human and Persona 2 is RetNRef++

This really laid the foundations for chatbots being able to use prior-information in their sessions.

## 🧙‍♀️ of Wikipedia 2019 Facebook

*[Knowledge-Powered Conversational Agents](https://arxiv.org/abs/1811.01241v2)*

This paper tried to fill the skill gap "General-Knowledge" for chatbots. They use a [memory neural network](https://arxiv.org/pdf/1503.08895.pdf) for retrieval and a transformer using attention to output embeddings and thus refinement.

They frame the chat in the following way

> two participants engage in chitchat, with one of the participants selecting a beginning topic, and during the conversation the topic is allowed to naturally change. The two participants, however, are not quite symmetric: one will
play the role of a knowledgeable expert (which we refer to as the wizard) while the other is a curious learner (the apprentice).

The whole pipeline can be seen below, first using standard information retrieval (IR) techniques to retrieve the current and last two turn topics, these are then fed into the encoder along with the dialogue context. Then using a standard attention mechanism, they look for the most relevant knowledge which is passed to step two.  

![Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%203.png](/jakyll/assets/Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%203.png)

The model is trained on an annotated but unstructured Wikipedia corpus (obviously) along with the Reddit and SQuAD dataset to create an agent (Wizard) that is able to chat about a subject with an (eager to learn) student.

## XiaoIce 2019 Microsoft

*[The Design and Implementation of XiaoIce, an Empathetic Social Chatbot](https://direct.mit.edu/coli/article/46/1/53/93380/The-Design-and-Implementation-of-XiaoIce-an)*

Microsoft's aim was slightly different to what we had seen in the current research, it was to pass the time-sharing test; keeping users engaged with the bot over long periods and multiple sessions. The Core Chat skill allows this bot to engage in long and open domain conversations with users fills the function we have seen from the previous two papers.

Performance is measured (unlike task-driven bots) by Conversations-turns Per Session (CPS).

To maintain user interest, XiaoIce promotes diversity of conversation by using different modes/skills. Examples:

- General Chat
- Music Chat
- Song-On-Demand
- Ticket-Booking

Choosing skills can be seen as a decision-making process with a natural hierarchy, thus they utilised Markov Decision Processes (MDPs).

![Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%204.png](/jakyll/assets/Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%204.png)

## DialoGPT 2020 Microsoft

*[Toward Human-Quality Conversational Response Generation via Large-Scale Pretraining](https://www.microsoft.com/en-us/research/project/large-scale-pretraining-for-response-generation/)*

Jumping on the back of the success of GPT-2, Microsoft extended GPT-2 to natural dialogue responses. However it differs as it is trained on dialogue pairs/sessions extracted from Reddit discussion chains. They model the multi-turn dialogue as a long text and frame the generation task as language modeling.

Great lengths were taken to try and filter out toxic, repetitive or malicious samples from the data from the Reddit corpus.

## Blender 2020 Facebook

*[Recipes for building an open-domain chatbot](https://arxiv.org/abs/2004.13637v1)*

Facebook's goal was to focus on personality, engagingness, knowledge, empathy and other positive traits. Hence their model BLENDS multiple models together using [BlendedSkillTalk](https://arxiv.org/abs/2004.08449).

![Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%205.png](/jakyll/assets/Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%205.png)

The bot follows the familiar steps of retrieve and refine followed by a decoding step for the response generation. The model has multiple retrieval methods:

**Dialogue Retriever**

Given a dialogue history (context) as input, the retrieval systems select the next dialogue utterance by scoring a large set of candidate responses and outputting the highest scoring ones. This is then appended to the input of the generator with a special separator token.

**Knowledge Retrieval**

Using the same mechanism but instead retrieve from a large knowledge base. Then condition the generation on the retrieved knowledge as in *Wizard of Wikipedia*.

There is also a binary classifier to discriminate between contexts that require knowledge or not in the fine-tuning tasks.

**Decoder**

At inference time, a decoder method is needed to generate a response to the dialogue context given the input. There are two methods they explored:

Greedy Search

Selects the highest probability token at each time step.

Beam Search

Maintains a fixed-size set of partially decoded sentences (hypotheses). At each time step new hypotheses are created by appending each token in the vocabulary to each existing hypothesis then rescoring and selecting the highest scoring sequence.

A fine-tuned hyperparameter beam search came out on top for their experiments.

## PLATO-2 2020 Baidu

*[Towards Building an Open-Domain Chatbot via Curriculum Learning](https://arxiv.org/abs/2006.16779)*

Baidu's focus was to tackle the one-to-many mapping problem when it comes to multiple appropriate responses. They train using Curriculum learning and have two stages.

![Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%206.png](/jakyll/assets/Chatbot%20Current%20Research%20Paper%20Summary%20f4418c86bf154313876c79491d17738d/Untitled%206.png)

**Coarse-Grained Generation**

Trained to learn general response generation mapping under the one-to-one mapping.

**Fine-Grained Generation**

Based on the previous model this model is trained on one-to-many mappings. This is achieved using a latent variable which is estimated and then samples on for generation.

Also trained on Reddit dataset and open-domain Chinese social data.