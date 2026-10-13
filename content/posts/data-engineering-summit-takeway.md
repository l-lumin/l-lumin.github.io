+++
title = 'Key takeaway from the Data Engineering Summit 2026'
date = 2026-10-11T12:00:00+09:00
publishDate = 2026-10-11T12:00:00+09:00
draft = false
+++

I recently attended the Data Engineering Summit, looking to understand where the industry is heading. Across multiple sessions, from startups to tech giants, a clear pattern emerged.
We are no longer just struggling with how to store data. The rise in data volume, the shift toward real-time streaming, and the massive demand for AI have create a new bottleneck: context.

AI agents and machine learning models are hungry for data, but feeding them raw, messy database tables leads to hallucinations and broken pipelines. To bridge this gap, two keywords kept dominating the summit presentations: Semantic Layer and Ontology.

# The Problem: The Gap Between Raw Data and AI

Historically, data engineers focused on pipelines, moving data from source to server. But as data scales, companies end up with thousand of tables, from bronze layer to gold layer, each with different format, ingest method, and conflicting definitions.

When a downstream user (or an AI agent) needs to calculate "Daily Revenue" they face a massive hurdle. Which table do they use? Does "revenue" include taxes? Does it count refunded items? If AI agent don't have share a consistent definition with user, the output become worthless, and even untrustworthy.

# The Semantic Layer

A semantic layer is a business-friendly interface that bridges the gap between complex data models and business users. Acting as a middle layer, it translates technical data structures into familiar business terms and concepts, enabling business user to access and gain insight from data without deep technical expertise. 

This concept is first introduced by Business Objects in 1991, and it has evolved significantly with the changing data landscape. Today's semantic layers handle massive data volumes and variety, integrate with cloud-native architectures, support real-time analytics requirements and incorporate machine learning and generative AI capabilities.

# Ontology

While a semantic layer translate terms, an Ontology maps how those concepts relate to one another in the real world. 

Originally, Ontology is a term used in philosophy, the study of being that investigates the nature of existence, the feature all entities have in common, and how they divided into basic categories. In knowledge management, it use to define how computers, AI, and human share a common meaning.

In modern data architecture, the ontology acts as a blueprint, and it is brought to life using a Knowledge Graph. The ontology defines the rule that a `Customer` can place an `Order`, and the knowledge graph stores the actual data connecting a specific customer to their specific orders. 

This structural mapping is critical for AI. When AI agents query raw databases, they frequently hallucinate invalid SQL joins. By grounding the AI in a strict ontology, the agent is forced to respect the real-world relationships of your business, ensuring that its analytical logic is structurally sound.

# The Data Foundation: Apache Hudi
Of course, you cannot build a reliable semantic layer if the underlying data is constantly breaking or out of date. To manage mutable data at scale, tools like Apache Hudi have become essential. As highlighted in sessions discussing Uber's architecture, Hudi brings database-like transactional guarantees (upserts and deletes) to massive data lakes. It allows systems to handle streaming updates efficiently, ensuring that the clean data feeding the semantic layer is always fresh and accurate.

# The Aspirational Goal: Unified Data Architecture (UDA)
The most striking example of this was Netflix’s presentation on their Unified Data Architecture (UDA). They demonstrated that having a Semantic Layer and Ontology isn't just about clean dashboards—it is about automation.

By defining data strictly at the semantic level, they can abstract away the underlying infrastructure. Machine learning engineers don't need to worry about whether the data is streaming via Kafka or sitting in Iceberg; they just request the semantic concept, and the architecture delivers it.

# Conclusion

Taking very first step into data engineering career, the biggest takeaway from summit is clear. The future of data engineering is not just moving data faster, it is about organizing it better. Building robust information architecture, whether through strict data contracts, naming conventions, or formal semantic layers—is the only way to successfully prepare enterprise data for the AI era.