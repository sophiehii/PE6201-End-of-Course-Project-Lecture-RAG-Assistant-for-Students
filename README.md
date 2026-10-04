# PE6201-End-of-Course-Project-Lecture-RAG-Assistant-for-Students
## 1. Persona

A postgraduate/undergraduate student who is reviewing for exams. They have a large collection of lecture slides, PDFs, and notes from multiple courses. They struggle to quickly locate specific concepts across different documents and need traceable, factual answers.

## 2. Input

- User Input: A natural language question (e.g., "What is RAG?").
- System Input: 26 PDF course materials (Class 1 to Class 6), which are processed into 425 text chunks with metadata (paragraph_id, class, page).
- Evaluation set: 50 manually annotated QA pairs, each with one gold paragraph_id.

## 3. Output

An LLM-generated answer based strictly on the retrieved context, ending with a mandatory citation in the format: `Source: <paragraph_id>` (e.g., `Source: CL2_C3_P003`). If the retrieval confidence is below the threshold, the system outputs an abstention message: "I couldn't find a reliable answer."

## 4.High-level Product Architecture
<img width="2280" height="2640" alt="rag_architecture" src="https://github.com/user-attachments/assets/1b6dfb19-ab12-4fe2-b703-8101acdd9629" />


## 5. Evaluation Setup

- 50 manually annotated QA pairs across Class 1–6.
- Each QA has one gold paragraph_id.
- Recall@k: gold paragraph_id appears in top-k retrieved chunks.
- Hallucination rate: among non-abstained answers, % where the cited paragraph does not support the claim, judged by post-hoc checker + manual spot check.

## 6. Metrics Targeted

- **Target Recall@1: >50%** (to outperform the measured BM25 baseline of 38%).  
  Definition: the gold paragraph_id appears in the top-1 retrieved chunk.
- **Target Abstention Rate: <10%**, while maintaining Hallucination Rate <10%.  
  Definition: the system outputs “I couldn't find a reliable answer.” instead of attempting an answer.  
  This ensures the system attempts most questions without answering unsafely.
- **Target Hallucination Rate: <10%** among non-abstained answers.  
  Definition: the cited paragraph is missing, invalid, or does not support the generated claim, as judged by the post-hoc citation checker.
- **Target Citation Accuracy: >90%** among non-abstained answers.  
  Definition: the cited paragraph exists and supports the generated claim, as judged by the post-hoc citation checker.

## 7. Results

| Method       | Recall@1    | Recall@3    |
| ------------ | -----------:| -----------:|
| BM25         | 38% (19/50) | 62% (31/50) |
| FAISS local  | 26% (13/50) | 48% (24/50) |
| FAISS OpenAI | 54% (27/50) | 86% (43/50) |
| Generation:  |             |             |

- Abstention: 1/50 = 2.00%
- Answered: 49
- Post-hoc flagged hallucination: 26/49 = 46.94%
- Citation accuracy among answered: 23/49 = 46.94%
  Cost:
- Corpus embedding: text-embedding-3-small, 425 chunks, 89,750 tokens, $0.0018.
- Per query: 1,000 tokens, $0.00024.


