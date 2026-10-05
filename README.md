# 🧠 EEG Spectral Analysis via Kalman Smoother

![MATLAB](https://img.shields.io/badge/MATLAB-e16737?style=for-the-badge&logo=mathworks&logoColor=white)
![SignalProcessing](https://img.shields.io/badge/Domain-Signal_Processing-blue?style=for-the-badge)
![OptimalEstimation](https://img.shields.io/badge/Control_Theory-Kalman_%7C_RTS_Smoother-success?style=for-the-badge)

A data analysis and signal processing project focused on the optimal estimation of non-stationary dynamic systems. Developed for the *Filtering and Identification of Dynamical Systems* course at Università della Calabria.

This project applies the **Rauch-Tung-Striebel (RTS) Kalman Smoother** to estimate the time-varying parameters of an Autoregressive (AR) model to track the spectral evolution of Electroencephalographic (EEG) signals. Specifically, it analyzes the transient neurophysiological phase shift between **Event-Related Desynchronization (ERD)** and **Event-Related Synchronization (ERS)**.

<p align="center">
  <img src="link_al_tuo_spettrogramma_PSD_figura_4.4.png" width="600" alt="Time-Varying PSD via Kalman Smoother">
</p>
*Time-varying Power Spectral Density (Spectrogram) generated via smoothed AR(6) coefficients. Notice the sudden emergence of the Alpha Rhythm (8-12 Hz) exactly at t = 60s when the subject closes their eyes.*

## 🚀 Engineering Highlights

*   **Autoregressive (AR) Modeling:** Modeled the raw, non-stationary EEG signal as a time-varying AR(p) process with order $p = 6$ to capture brain rhythms without overfitting.
*   **State-Space Formulation:** Translated the AR equation into a state-space representation where the hidden state vector $x_k$ contains the dynamic AR coefficients, driven by a random walk process noise.
*   **Forward Kalman Filter:** Implemented a standard recursive causal filter to estimate the coefficients in real-time, handling heavy measurement noise ($R = 0.1$) and process tuning ($Q = 10^{-6}I$).
*   **RTS Backward Smoother:** Executed a non-causal backward pass across the recorded dataset (Fixed-Interval Smoothing)[cite: 166, 167]. The RTS algorithm significantly refined the coefficient trajectories by "looking into the future" to ignore transient noise spikes.
*   **Neurophysiological Validation:** Reconstructed the dynamic Power Spectral Density (PSD) from the smoothed coefficients, effectively capturing the transition from asynchronous cortex activity (eyes open) to macroscopic Alpha wave synchronization (eyes closed).

## 🧠 Algorithmic Architecture

### 1. Data Cleaning & Pre-processing
Real EEG recordings were acquired from the open PhysioNet database. 
*   **Electrode Selection:** Data from the **Oz** channel (occipital visual cortex) was extracted.
*   **Filtering:** Applied a $1 - 40 \text{ Hz}$ bandpass filter at a sampling rate of $160 \text{ Hz}$ to eliminate baseline drifts (sweat/breathing) and high-frequency noise.

### 2. Forward Pass (Kalman Filter)
The filter recursively predicts and updates the state based on the innovation error.
Because it relies solely on past and present data, the causal filter tends to overreact to abrupt noise spikes, mistaking them for structural system changes.

### 3. Backward Pass (RTS Smoother)
The RTS algorithm performs a second pass from the final time step down to $k=1$. 
The smoothing gain $G(k)$ optimally weights future information. As seen in the results, the smoother completely ignores false peaks and maintains a stable, highly accurate parameter estimate.

<p align="center">
  <img src="link_al_grafico_confronto_figura_4.5.png" width="600" alt="Recursive Tracking of AR(6) Parameters: Filter vs Smoother">
</p>
*Visual demonstration of the RTS Smoother (solid line) ignoring a false noise peak that destabilizes the standard forward Kalman Filter (dashed line).*

## 📊 Limitations of Classical Approaches
To prove the necessity of the Kalman-based approach, a classical Welch's method PSD was also computed directly on the raw signal. 
While it successfully identifies the 10 Hz Alpha peak, it strictly requires the signal to be split manually a priori (knowing exactly when the eyes were closed)[cite: 179]. Being an averaged approach, it inherently fails to visualize the true dynamic time-varying evolution of the transient phenomena.

## 💻 Repository Structure
*   `/src/`: MATLAB `.m` scripts containing the data downloading logic (`websave`), pre-processing, and the implementation loops for the Kalman Filter and the RTS Smoother.
*   `/data/`: (Optional) Local copies of the `.edf` PhysioNet data files.
*   `/Report/`: The complete PDF project report detailing the Bayesian probabilistic foundations of the RTS algorithm and the covariance mathematical proofs.

## 🔮 Future Developments
*   Implementation of advanced optimization algorithms for the automatic tuning of the $Q$ and $R$ covariance matrices.
*   Extension of the dynamic spectral tracking to multiple EEG channels simultaneously.
*   Adoption of an Extended Kalman Filter (EKF) to process non-linear dynamic models.
