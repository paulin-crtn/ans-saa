# 🤖 Machine Learning

## 💡 Comprehend

Simplify document processing workflows by extracting text, key phrases, topics, sentiment, and more from documents (e.g. insurance claims, customer support tickets, product reviews, emails, social media feeds, etc).

- Natural Language Processing (NLP)
- Input = Document
- Output = Entities, phrases, language, PII, sentiments
- `Pre-trained` or `Custom` models
- **Real-time** analysis for small workloads or **async jobs** for larger workloads

## 🔍 Kendra

Intelligent search service designed to mimic interacting with a human expert. Quickly create secure, generative AI-powered conversational experiences for your users on top of your enterprise content.

- Support wide range of question types
- Factoid - Who, What, Where
- Descriptive - How do I get my cat to stop being a jerk ?
- Keyword - What time is the keynote address ? ➡️ address can have multiple meanings, Kendra helps determine **intent**

> [!NOTE]
> Kendra connects and indexes from a variety of data sources (structured or unstructured documents).

## 💬 Lex

**Text or Voice conversational interfaces** (powers the `Alexa` service).

- Automatic Speech Recognition (ASR) - Speech to text
- Natural Language Understanding (NLU) - Intent
- Able to understand a question based on the previous one
- Scales, integrates, quick to deploy, pay as you go pricing
- Chatbots, Voice Assistants, Q&A bots, Info/Enterprise Bots

## 🗣️ Polly

**Converts text into "life-like" speech**. NO TRANSLATION.

- **Standard** Text-To-Speech = Concatenative (phonemes)
- **Neural** Text-To-Speech = Phonemes ➡️ spectograms ➡️ vocoder ➡️ audio (much more human/natural sounding but more complex)
- `Speech Synthesis Markup Language` (SSML): additional control over **how** Polly generates speech (emphasis, pronunciation, whispering, "newscaster" speaking style)
- Output formats: `MP3`, `OGG`, `PCM`, etc.

## 📸 Rekognition

Deep learning **Image and Video Analysis**. Identify objects, people, text, activities, content moderation, face detection, face analysis, face comparaison, etc.

- Per image or per minute (video) pricing
- Integrates with applications & event-driven
- Can analyse live video streams - kinesis video streams

## 📄 Textract

**Automatically extract printed text**, handwriting, layout elements, and data from any document + relationship between text + metadata.

Textract is also able to proceed document analysis (names, address, birhtdate...), receipt analysis (prices, vendor, line items, dates...) and identity document analysis (document id...).

- Input: `JPG`, `PNG`, `PDF` or `TIFF`
- Output: Extracted text, structure and analysis
- Synchronous or asynchronous for large documents
- Pay for usage (custom pricing available for large volume)

## ✏️ Transcribe

**Automatically convert speech to text**.

- Language customisation
- Filters for privacy
- Audience-appropriate language
- Speaker identification
- Pay per use (per second of transcribe audio)

> [!NOTE]
> Use-cases: full text indexing of audio, meeting notes, subtitles/captions transcripts, call analytics (characteristics, summarisation, categories and sentiment), etc.

## 🈯 Translate

Text translation service.

- Attention mechanisms ensure **meaning** is translated.
- Auto detect source text language
- Commonly integrates with other services, apps or platforms

> [!NOTE]
> Use-cases: meeting notes, emails, chat, articles, incoming data, etc.

## 📊 Forecast

**Forecasting for times-series data**.

- Import historical & related data
- Understands what's normal

> [!NOTE]
> Use-cases: retail demand, supply chain, staffing, energy, server capacity, web traffic, etc.

## 🚨 Fraud Detector

**Fully managed Fraud Detection service**.

- Upload historical data, choose model type
- Activity is **scored** then `Rules/Decision` logic allow to react

> [!NOTE]
> Use-cases: new account creations, payments, guest checkout, online or transaction fraud, account takeover (e.g. loggin from another country), etc.

## 🧠 SageMaker

Collections of services and tools to create machine learning features and products. Fully managed.

- SageMaker **Studio**: build, train, debug and monitor models - IDE for ML lifecycle
- SageMaker **Domain**: EFS volume, users, apps, policies, VPCs, etc.
- **Container**: Docker containers deployed to ML EC2 instance - ML environments (OS, libs, tooling)
- **Hosting**: deploy endpoints for your models

> [!IMPORTANT]
> SageMaker has no cost but the resources it creates do (complex pricing)
