Transformer-Based Token-Level Detection of Human–Machine Text Boundaries
📌 Project Overview
This project focuses on identifying the word position at which a text transitions from human-written content to machine-generated content.

It addresses SemEval-2024 Task 8, Subtask C. Unlike whole-document classification, which predicts whether an entire document is human-written or machine-generated, this task predicts the boundary position within a mixed text.

The proposed approach fine-tunes a pretrained RoBERTa-base model for token-level classification. Each token is classified as HUMAN or MACHINE, and a single-cut decoding method reconstructs the final boundary.
🎯 Objectives
- Detect the transition point between human-written and machine-generated text.
- Fine-tune a pretrained RoBERTa model for token-level classification.
- Convert word-level boundary labels into subword token labels.
- Reconstruct one HUMAN-to-MACHINE transition from token predictions.
- Evaluate boundary prediction using Mean Absolute Error and related metrics.
- Provide an interactive demonstration with colour-coded text output.
📊 Dataset
- Benchmark: SemEval-2024 Task 8
- Subtask: Subtask C – Human–Machine Mixed Text Detection
- Benchmark context: M4
- Total instances: 15,277
Split	Number of Texts
Training	3,649
Development	505
Test	11,123
Total	15,277


Each sample begins with a human-written part and continues with a machine-generated part. The target label is the word index at which machine-generated text starts. A boundary value of zero represents a fully machine-generated text.
🧠 Methodology
1. Data Loading and Validation
Load the training, development and test data and perform sanity checks on the text and boundary labels.
2. Word-Level Labelling
Convert the boundary index into word-level labels:
- HUMAN (0): words before the boundary.
- MACHINE (1): words at and after the boundary.
3. Tokenization and Label Alignment
RoBERTa uses subword tokenization. The word-level labels are aligned with the corresponding subword tokens so that the model can learn token-level classifications.
4. RoBERTa Token Classifier
Fine-tune the pretrained roberta-base model with a two-class token-classification head to predict HUMAN or MACHINE for each token.
5. Word-Level Probabilities
Obtain the probability of a token belonging to the MACHINE class. When a word is divided into multiple subword tokens, average their probabilities to obtain one probability per word.
6. Boundary Reconstruction
Use the Best Single Cut decoding method. The decoder evaluates possible word positions and selects the boundary that minimises disagreement between the word-level probabilities and the expected HUMAN-to-MACHINE label sequence.

This ensures that the final prediction contains exactly one HUMAN-to-MACHINE transition.
7. Evaluation
Evaluate the predicted boundary positions using Mean Absolute Error (MAE), median error and the percentage of predictions within specified word distances of the actual boundary.
8. Interactive Demonstration
The notebook provides a demonstration in which users can enter text and view the predicted boundary with colour-coded HUMAN and MACHINE spans.
⚙️ Model Configuration
Parameter	Value
Pretrained model	roberta-base
Number of labels	2
Labels	HUMAN, MACHINE
Maximum sequence length	512 tokens
Training batch size	8
Development batch size	16
Epochs	3
Learning rate	2e-5
Weight decay	0.01
Warm-up	10% of total steps
Scheduler	Linear decay
Gradient clipping	1.0
Mixed precision	Enabled on GPU
Random seed	42


The model with the lowest development loss is saved as the final model.
📈 Results
Training and Validation Performance
Epoch	Training Loss	Development Loss	Development Token Accuracy
1	0.1807	0.1246	95.66%
2	0.0609	0.0861	97.09%
3	0.0447	0.0664	97.79%


Boundary Detection Performance
Metric	Development Set	Test Set
Mean Absolute Error	4.64	17.91
Median Error	2 words	7 words
Within 5 words	73.5%	45.3%
Within 10 words	87.7%	60.8%
Within 25 words	97.6%	79.8%


Comparison with the Base Paper
Method	Development MAE	Test MAE
Base paper weighted ensemble	31.71	60.78
Base paper RoBERTa + ElasticNet	33.28	62.34
Base paper Longformer baseline	About 3.53	21.54
Proposed RoBERTa token classifier	4.64	17.91


The proposed approach achieved a lower test MAE than the base paper's weighted ensemble and Longformer baseline reported in the documentation.
🔍 Key Findings
- RoBERTa learned token-level distinctions between human-written and machine-generated text.
- Development token accuracy increased across the three training epochs.
- The proposed single-cut decoder produced one consistent HUMAN-to-MACHINE boundary.
- The test MAE was 17.91 words.
- The test performance was better than the base paper's reported weighted ensemble and Longformer test MAE.
- Boundary errors increased for texts with later transition positions.
⚠️ Limitations
- RoBERTa processes a maximum of 512 tokens, so later parts of longer texts are not analysed.
- The dataset is drawn from a specific domain, which limits conclusions about other writing styles and domains.
- The model uses one training seed, so variation across different initialisations is not established.
- Newer text-generation models may produce patterns different from those represented in the benchmark.
- The decoder assumes exactly one transition from HUMAN to MACHINE.
- Detection predictions are estimates and should not be treated as definitive proof of AI authorship.
🚀 Future Enhancements
- Use sliding windows or long-context models such as Longformer for longer texts.
- Experiment with RoBERTa-large or DeBERTa.
- Introduce Conditional Random Fields or monotonic constraints.
- Explore boundary-aware loss functions.
- Evaluate on additional domains and languages.
- Repeat experiments across multiple random seeds and report confidence intervals.
- Develop the detector as a web application.
🛠️ Technologies Used
Technology / Library	Purpose
Python 3	Programming language
Google Colab	Development and training environment
PyTorch	Neural network training and inference
Hugging Face Transformers	RoBERTa model and tokenizer
Hugging Face Datasets	Dataset loading
Accelerate	Training support
scikit-learn	Evaluation metrics
NumPy and Pandas	Numerical processing and analysis
tqdm	Progress bars
ipywidgets and IPython.display	Interactive demonstration and coloured output


▶️ Running the Project
The implementation is provided as a Google Colab notebook.
1. Open the notebook in Google Colab.
2. Select a GPU runtime. The documentation specifies a T4 GPU.
3. Load the SemEval-2024 Task 8 Subtask C training, development and test data.
4. Install any required dependencies that are not already available.
5. Run the notebook cells in order to validate the data, prepare labels, tokenize the text, fine-tune RoBERTa and evaluate the model.
6. Use the interactive demonstration to enter text and inspect the predicted boundary.
The trained model weights are stored on Google Drive by the notebook. Update the notebook filename and data-loading paths in this section to match the files included in the repository.
📁 Suggested Repository Structure
human-machine-boundary-detection/
│
├── README.md
├── notebooks/
│   └── human_machine_boundary_detection.ipynb
├── data/
│   └── (SemEval data - follow dataset licence and access conditions)
├── models/
│   └── (saved model weights, if included)
├── results/
│   └── (evaluation outputs)
└── documentation/
    └── project_documentation.pdf
📚 Base Paper
Puspo, S. S. C., Raihan, M. N., Goswami, D., Emran, A. N. B., Ganguly, A., and Uzuner, Ö. (2024). MasonTigers at SemEval-2024 Task 8: Performance Analysis of Transformer-based Models on Machine-Generated Text Detection. Proceedings of SemEval-2024, pp. 1364–1372.
⚖️ Responsible Use
This project is an experimental research prototype. A false positive may lead to an unjustified accusation of AI-generated writing. Its predictions should be interpreted as estimates, reviewed by humans and never used as the sole basis for academic, employment or other high-impact decisions.
👩‍💻 Author
Kaavya S
M.Sc. Data Science – II Year
SASTRA Deemed University
Academic Year: 2026–2027
