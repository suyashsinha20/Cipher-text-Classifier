# AES Mode Classification using LSTM

## 📌 Objective
The primary goal of this project is to classify ciphertexts based on the Advanced Encryption Standard (AES) mode used for encryption. Specifically, the project focuses on AES stream modes that do not require padding—**OFB, CFB, and CTR**—and aims to identify the encryption mode solely by analyzing the resulting ciphertext characteristics.

## 🚀 Project Pipeline

### Phase 1: Data Extraction and Encryption
- **Dataset Generation**: Text samples are normalized to a strict 256-byte length to eliminate length-based biases.
- **Encryption**: Plaintexts are encrypted into CFB, OFB, and CTR stream modes using AES-256. A unique 32-byte key and 16-byte IV are generated for every individual sample.
- **Formatting**: The raw ciphertext sequences are labeled with their respective modes, randomly shuffled to remove sequential bias, and exported to a JSON file.

### Phase 2: Statistical Preprocessing via Sequential Byte Differencing
To extract meaningful, trackable features rather than feeding raw bytes to the model:
- The script calculates the bitwise difference (XOR) between adjacent bytes: `D_i = C_i ⊕ C_{i-1}`.
- This transforms the raw ciphertext array of length $N$ into a sequence of transitionary values of length $N-1$.
- This processed sequence is bundled with its original mode label to serve as the final feature set for training.

### Phase 3: Binary Sequence Classification via LSTM
An LSTM (Long Short-Term Memory) network built with **TensorFlow Keras** is used to classify the preprocessed sequences.
- **Input**: Normalized bitwise difference arrays reshaped for sequential time-step processing.
- **Architecture**:
  - **LSTM layer (64 units)** to capture sequence patterns within the 256-byte ciphertexts.
  - **Dense layer (32 neurons, ReLU activation)** to learn more complex features.
  - **Dense output layer (2 neurons, Softmax activation)** for binary classification of the target AES modes.
- **Compilation**: Trained using the **Adam optimizer** and **Sparse Categorical Crossentropy** loss function.

## 📊 Results & Observations
The LSTM model was used to evaluate binary classifiers distinguishing between different pairs of AES modes:

| Classification Task | Accuracy | Observation |
| :--- | :--- | :--- |
| **CFB vs. CTR** | `81.31%` | High distinguishability; CTR exhibits readily identifiable patterns. |
| **OFB vs. CTR** | `77.93%` | Good distinguishability against CTR mode. |
| **CFB vs. OFB** | `48.50%` | Close to random guessing. CFB and OFB produce highly similar ciphertext characteristics making them difficult to separate using this feature extraction approach. |

## 💡 Conclusion
The study successfully demonstrates that machine learning techniques like LSTMs can capture cryptanalytic differences between certain AES modes, particularly when distinguishing against CTR mode. At the same time, the results highlight the challenge of separating modes that produce highly similar ciphertext patterns (such as CFB and OFB), establishing a baseline for future improvements in feature engineering, dataset generation, and model architecture.
