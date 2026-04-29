# Hindi Ambiguity Resolution using Large Language Models

##  Project Overview
In Natural Language Processing, ambiguity occurs when a word or sentence has multiple possible meanings.Hindi is especially challenging due to its morphologically rich structure, where the same word can mean different things in different contexts. 

The goal of this project is to fine-tune a small transformer model to identify the correct meaning of ambiguous Hindi words based on their context. By providing the model with a Hindi sentence, the ambiguous word, and the ambiguity type, it predicts the correct contextual meaning.

---

##  Dataset
A custom annotated dataset of 1,000 Hindi sentences was created for this project. It is split into 900 training samples and 100 test samples.

The dataset covers **391 unique ambiguous words** across **10 ambiguity categories**:
* **Time Ambiguity:** e.g., *कल* means yesterday OR tomorrow depending on tense.
* Object-Action / Morphological:** e.g., *सोना* means sleep OR gold.
* **Pronoun Reference:** Clarifying ambiguous subjects (e.g., *वह गया*).
* **Sarcasm:** e.g., *बहुत होशियार हो* as genuine praise vs. a sarcastic remark
* **Idiom Ambiguity:** e.g., *पानी में मछली* (literal vs. idiomatic).
* **Hinglish:** Code-switched text with domain-specific meanings (e.g., *mood off है*).
* **Additional Categories:** Syntactic, Pragmatic, and Place ambiguity.

---

##  Model & Methodology
This project utilizes **Supervised Fine-Tuning (SFT)** on a pre-trained Large Language Model. The raw CSV dataset was structured into an Instruction-Input-Response (Alpaca) format to teach the model how to predict meanings.

* **Base Model:** `Qwen/Qwen2.5-0.5B` (~494 Million parameters). 
* **Architecture:** Decoder-only Transformer loaded in `float16` precision for stable training.
* **Optimization Technique:** **LoRA (Low-Rank Adaptation)** was applied to freeze the original weights and train only small adapter matrices[cite: 191, 192]. This allowed us to update only ~1-2% of total parameters, keeping memory usage highly efficient. 
   *LoRA Config:* Rank (r)=16, Alpha=32, Dropout=0.05.
    * *Target Modules:* Attention projection layers (`q_proj`, `v_proj`, `k_proj`, `o_proj`).

---

## ⚙️ Training Configuration
The model was trained on a single Kaggle T4 GPU with the following hyperparameters:
**Epochs:** 10
* **Batch Size:** 2 per device with 8 gradient accumulation steps (Effective Batch Size: 16) 
* **Learning Rate:** 2e-4 (Cosine Scheduler) 
* **Max Sequence Length:** 256 tokens 

---

##  Results & Evaluation
A 500M parameter model is highly capable of context-aware word sense disambiguation in Hindi[cite: 243].
* **Loss Reduction:** Training loss decreased significantly from `1.097` to `0.293` (a 73% reduction), while validation loss consistently decreased from `0.589` to `0.395`, showing no signs of overfitting.
* **Accuracy:** The model achieved a final accuracy of **56.0%** on a 50-sample evaluation test set. 
* **Performance:** The model effectively handles Hinglish sentences and successfully distinguishes time context (yesterday vs. tomorrow) based purely on sentence tense. Sarcasm and pragmatic ambiguity remain the hardest categories due to highly subtle and implicit context.

---

##  Future Scope
**Dataset Expansion:** Expand the custom dataset to 10,000+ annotated samples for better generalization.
* **Model Scaling:** Experiment with larger models (e.g., Qwen2.5-1.5B).
* **Multilingual Extension:** Extend the ambiguity resolution pipeline to other morphologically rich languages like Bengali, Tamil, and Marathi.
* **Deployment:** Deploy a real-time FastAPI inference service.

---

## 👨‍💻 Author & Credits
* **Author:** Aman kumar
* **Institution:** Department of Artificial Intelligence & Data Science, Sikkim Manipal Institute of Technology (SMIT), SMU 
* **Course:** (CD306A1) Text Analytics & Natural Language Processing
