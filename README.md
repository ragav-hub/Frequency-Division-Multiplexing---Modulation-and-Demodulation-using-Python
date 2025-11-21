# Frequency-Division-Multiplexing---Modulation-and-Demodulation-using-Python

__Aim__:

To generate an FDM signal by multiplexing multiple baseband message signals on different carrier frequencies, transmit (sum) them, optionally add channel noise, then recover each message by bandpass filtering and coherent demodulation in Python (Google Colab). Observe time & frequency domain signals and measure recovery quality.


__Apparatus Required__:

Google Colab (or any Python environment)

Python libraries: numpy, matplotlib, scipy (scipy.signal)


__Theory__:

FDM places different message signals in separate, non-overlapping frequency bands by modulating each message onto a distinct carrier frequency. The multiplexed signal is the sum of all modulated channels. At the receiver, bandpass filters (or tuned filters) isolate each channel; then each isolated carrier is demodulated (coherently multiplied by a synchronized carrier) and low-pass filtered to recover the original baseband.

__Procedure__:

1 — Imports and parameters

2 — Create message signals and carriers

3 — Modulate each message (standard AM DSB-SC) and form FDM signal

4 — Frequency domain (spectrum) of FDM signal

5 — (Optional) Add AWGN noise to FDM signal

6 — Receiver: isolate each channel with bandpass filter

7 — Demodulate each isolated channel (coherent) and low-pass filter to recover baseband

__Program__:
```
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter

# Parameters
# -----------------------------
fs = 50000           # Sampling frequency
t = np.arange(0, 0.01, 1/fs)   # Time vector
N = 5                # Number of channels

# Message and carrier frequencies
msg_freq = [120, 240, 340, 500, 800]
carrier_freq = [3000, 6000, 9000, 12000, 15000]

# -----------------------------
# Signal generation
# -----------------------------
messages = np.zeros((N, len(t)))
carriers = np.zeros((N, len(t)))
modulated = np.zeros((N, len(t)))

for i in range(N):
    messages[i, :] = np.sin(2 * np.pi * msg_freq[i] * t)
    carriers[i, :] = np.cos(2 * np.pi * carrier_freq[i] * t)
    modulated[i, :] = messages[i, :] * carriers[i, :]  # DSB-SC modulation

# FDM Composite signal
fdm_signal = np.sum(modulated, axis=0)

# -----------------------------
# Low-Pass Filter (for proper demodulation)
# -----------------------------
def butter_lowpass(cutoff, fs, order=4):
    nyquist = 0.5 * fs
    normal_cutoff = cutoff / nyquist
    b, a = butter(order, normal_cutoff, btype='low')
    return b, a

def apply_filter(data, b, a):
    return lfilter(b, a, data)

# -----------------------------
# Demodulation
# -----------------------------
demodulated = np.zeros((N, len(t)))
b_lp, a_lp = butter_lowpass(1000, fs)   # LPF cutoff slightly > highest message freq

for i in range(N):
    temp = fdm_signal * carriers[i, :]    # Multiply with carrier
    demodulated[i, :] = apply_filter(temp, b_lp, a_lp)  # Low-pass filter

# -----------------------------
# Plotting
# -----------------------------

# 1. Original messages
plt.figure(figsize=(10, 8))
for i in range(N):
    plt.subplot(N, 1, i+1)
    plt.plot(t, messages[i, :])
    plt.title(f"Original Message Signal {i+1}")
    plt.ylabel("Amplitude")
    if i == N-1:
        plt.xlabel("Time (s)")
    plt.tight_layout()
plt.show()

# 2. FDM composite signal
plt.figure(figsize=(8,3))
plt.plot(t, fdm_signal)
plt.title("FDM Composite Signal")
plt.xlabel("Time (s)")
plt.ylabel("Amplitude")
plt.grid(True)
plt.show()

# 3. Demodulated signals
plt.figure(figsize=(10, 8))
for i in range(N):
    plt.subplot(N, 1, i+1)
    plt.plot(t, demodulated[i, :])
    plt.title(f"Demodulated Signal {i+1}")
    plt.ylabel("Amplitude")
    if i == N-1:
        plt.xlabel("Time (s)")
    plt.tight_layout()
plt.show()
```

__Output__:

<img width="989" height="790" alt="image" src="https://github.com/user-attachments/assets/b29e51ad-88ed-46f2-a4af-feb4d7d377f3" />


<img width="689" height="316" alt="image" src="https://github.com/user-attachments/assets/b00ba7ae-b003-4fb0-b9a0-51f9064794c0" />


<img width="989" height="790" alt="image" src="https://github.com/user-attachments/assets/f371021c-f8c2-4781-9367-a1ab40a98cf6" />



__Result__:
Thus, The Frequency-Division-Multiplexing (Modulation-and-Demodulation) using-Python is generated and output is verified successfully.
