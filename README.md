# An Efficient Lossless Compression Method for Periodic Signals Based on Adaptive Dictionary Predictive Coding

## 📖 Overview

This project implements a highly efficient **lossless compression algorithm** for periodic signals based on the **Adaptive Dictionary Predictive Coding (ADPC)** scheme. The method is designed to exploit the time-domain redundancy inherent in signals from applications like **ECG monitoring** and **Power Quality Monitoring (PQM)**.

The core technique uses a dynamic dictionary to predict future signal samples based on the context of the previous two samples. This process transforms the highly redundant input signal into an **energy-concentrated prediction error stream** ($\{e(k)\}$), which is optimally suited for high-ratio compression via subsequent entropy coding (e.g., LZW). This approach guarantees **perfect reconstruction** of the original signal.

***

## ✨ Key Features

* **Lossless Compression:** Ensures **perfect reconstruction** of the original signal ($u(k) = s(k)$).
* **Adaptive Prediction:** Uses a **two-dimensional dictionary** ($D$) to dynamically learn and predict repeating patterns based on the context $(s(k-2), s(k-1))$.
* **Linear Time Complexity ($O(n)$):** The $O(1)$ dictionary lookup operation maintains an overall linear time complexity, making the method suitable for **real-time** and embedded systems.
* **Energy Concentration:** The output stream $\{e(k)\}$ exhibits a high frequency of zero values, significantly **reducing information entropy** ($H(X)$) for maximum compressibility.

***

## 📐 Mathematical Foundation

The hybrid prediction strategy uses the following steps:

1.  **Dictionary-based Prediction (Primary):** Retrieves the predicted value $s'(k)$ from the dictionary $D$ using the previous two samples as the two-dimensional address:
    $$s'(k) = D(s(k-2), s(k-1))$$

2.  **Dictionary Update (Adaptivity):** The current sample is stored for future prediction:
    $$D(s(k-2), s(k-1)) \leftarrow s(k)$$

3.  **Linear Prediction Fallback:** Used when the dictionary entry is missing (incomplete state):
    $$s'(k) = a \cdot s(k-2) + b \cdot s(k-1)$$
    *(Constants used in the example: $a=-1, b=2$)*

4.  **Prediction Error Calculation (Encoder):**
    $$e(k) = s(k) - s'(k)$$

5.  **Reconstruction (Decoder):** The decoder adds the received error $e(k)$ to its own synchronized prediction $u'(k)$:
    $$u(k) = e(k) + u'(k)$$

***

## 💻 Implementation Details

* **Platform:** Implemented in **Python 3** within a Google Colab environment.
* **Dictionary Structure:** Uses **nested Python dictionaries** to model the 2D storage structure, enabling efficient $O(1)$ lookup and update operations.
* **Special Case Handling:** The code correctly implements logic for:
    * Directly outputting the first two non-contextual samples.
    * Switching to linear prediction when the adaptive dictionary context is initially empty.
* **Verification:** Includes a step-by-step trace to verify the encoder and decoder logic.

### Example Trace

Given the input signal sequence: $S_{in} = [2, 3, 1, 2, 3, 1, 2, 3]$

| k | s(k) | Context | s'(k) | e(k) | Dictionary Update |
|---|------|---------|-------|------|-----------------|
| 1 | 2    | N/A     | N/A   | 2    | N/A             |
| 2 | 3    | N/A     | N/A   | 3    | N/A             |
| 3 | 1    | (2,3)   | -1    | 2    | $D(2,3) \leftarrow 1$     |
| 4 | 2    | (3,1)   | -1    | 3    | $D(3,1) \leftarrow 2$     |
| 5 | 3    | (1,2)   | 2     | 1    | $D(1,2) \leftarrow 3$     |
| 6 | 1    | (2,3)   | 1     | **0** | $D(2,3) \leftarrow 1$     |
| 7 | 2    | (3,1)   | 2     | **0** | $D(3,1) \leftarrow 2$     |
| 8 | 3    | (1,2)   | 3     | **0** | $D(1,2) \leftarrow 3$     |

**Final Prediction Error Stream:**
$$E_{out} = [2, 3, 2, 3, 1, 0, 0, 0]$$

***

## 📈 Performance Analysis

The ADPC method achieves significantly higher compression efficiency compared to common techniques due to its highly concentrated error output.

| Method         | Avg. Compression Ratio ($\text{CR}_{\text{mean}}$ with LZW) | Time Complexity |
|----------------|-------------------------------------------------------|----------------|
| **ADPC (Proposed)**| **64.9** | **O(n)** |
| 2-D DCT        | 46.8                                                  | $O(n^2)$         |
| 2-D LWT        | 20.7                                                  | $O(n)$           |
| DPCM           | 21.0                                                  | $O(n)$           |

* **Result:** ADPC achieves the highest compression ratio while maintaining the fast $O(n)$ complexity.

***

## 🚀 How to Run

1.  Open the [Google Colab Notebook](https://colab.research.google.com/drive/1bHTsEhnFeVEs8WV6ygRRuHfWkoXiOTcZ?usp=sharing).
2.  Run all cells sequentially (Runtime $\rightarrow$ Run all).
3.  The output will display the encoding and decoding steps, confirming **perfect lossless signal reconstruction**.

***

## ⚠️ Limitations and Future Scope

* **Noise Sensitivity:** The current model performs best on noise-free signals. Additive noise can increase prediction errors, degrading compression efficiency.
* **Memory Growth:** Dictionary storage size can increase with very long input signals, although the 2D design mitigates this compared to higher dimensions.

**Future Work:** Research could involve integrating thresholding or lightweight denoising filters into the ADPC pipeline to better handle noise-affected data.

***

## 📚 References

S. Dai, W. Liu, Z. Wang, K. Li, P. Zhu, and P. Wang, "An Efficient Lossless Compression Method for Periodic Signals Based on Adaptive Dictionary Predictive Coding," *Applied Sciences*, vol. 10, p. 4918, 2020.
