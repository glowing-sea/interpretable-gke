## Abstract

**Title**: Toward an Interpretable Knowledge Tracing Model through Graph Neural Networks and Large Language Models

**Keywords**: Educational Data Mining, Knowledge Tracing, Deep Learning

**Type**: Ongoing Master’s Research Project

**Weight**: 24 Units (25% of Master’s degree)

**Duration**: July 2025 – May 2026

**Course Name**: [COMP8800](https://programsandcourses.anu.edu.au/course/comp8800) – Advanced Computing Research Project

**Supervisors**: [Bernardo Pereira Nunes](https://researchportalplus.anu.edu.au/en/persons/bernardo-pereira-nunes/), [Sergio José Rodríguez Méndez](https://researchportalplus.anu.edu.au/en/persons/sergio-rodriguez-mendez/)

**Deliverable**: [literature review (presentation)](https://github.com/glowing-sea/interpretable-gke/tree/main/deliverables/group-presentation-2025-10-10)

**Description**:

- This ongoing project aims to develop a Knowledge Tracing (KT) model based on Graph Neural Networks (GNNs) and Large Language Models (LLMs) that provides explicit, meaningful insights into the latent Knowledge Components (KCs) of a course and the student’s mastery of each KC over time.
- KT typically involves predicting students’ performance based on their historical learning interactions, which is useful for teachers or online tutoring systems to dynamically adjust teaching pace to maximise students’ knowledge growth.
- Many KT models make predictions by learning the latent KCs of a course and students’ mastery of them.
- Current SOTA KT models mostly focus on improving the accuracy of predicting students’ shallow performance, e.g., quiz question correctness, but the latent KCs remain implicit and non-interpretable.
- The project is going to address the issues by constructing a mapping from the latent KC embeddings of a GNN-based KT model to their real-world names in natural language, by leveraging LLMs and other deep learning techniques, so that teachers can know not only the students’ quiz question correctness, but also whether they have truly understood the KC underlying the questions.
- Evaluation metrics include alignment between the KC names and the course syllabus that is not visible to the model during training, semantics consistency through LLM-as-a-Judge, and downstream usability.