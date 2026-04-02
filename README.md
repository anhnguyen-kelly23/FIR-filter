# Digital Signal Processing with FIR Filters — White Noise Reduction

This project applies Finite Impulse Response (FIR) filters to reduce white noise in audio signals. Two filter designs are compared: a Low-Pass Filter and a Bandpass Filter, both using the Hamming window method.

## How It Works

The pipeline has three main steps:

1. Read the original audio signal from a WAV file
2. Add white noise at a controlled SNR level (20 dB)
3. Apply FIR filters and evaluate noise reduction performance

### Adding White Noise

White noise is added to the original signal at a target SNR of 20 dB using the formula:

    SNR_linear = 10 ^ (SNR_dB / 10)
    noise_power = signal_power / SNR_linear
    noisy_signal = original_signal + noise

### FIR Filter Design — Windowing Method

FIR filters are designed by multiplying an ideal impulse response with a window function:

    h[n] = h_ideal[n] x w[n]

**Hamming Window:**

    w[n] = 0.54 - 0.46 x cos(2pi x n / (N-1))

**Ideal Low-Pass Filter:**

    h_ideal[n] = sin(wc x n) / (pi x n)

**Ideal Bandpass Filter:**

    h_ideal[n] = [sin(wH x n) - sin(wL x n)] / (pi x n)

### Why filtfilt Instead of lfilter

`lfilter` is a causal filter that introduces a group delay of (N-1)/2 samples, causing the output to be shifted relative to the original signal. This shift produces inaccurate SNR measurements. `filtfilt` applies the filter twice — forward and backward — resulting in zero phase distortion and accurate comparison.

## Filter Specifications

| Parameter      | Low-Pass Filter | Bandpass Filter |
|----------------|-----------------|-----------------|
| Filter Order N | 201             | 201             |
| Window         | Hamming         | Hamming         |
| Cutoff Low     | —               | 300 Hz          |
| Cutoff High    | 4500 Hz         | 4000 Hz         |
| Target         | Remove high-frequency noise | Keep speech band only |

## Results

### SNR Comparison

| Signal                        | SNR (dB) | MSE        | RMSE    |
|-------------------------------|----------|------------|---------|
| Before filtering              | 20.00    | 720,439    | 848.79  |
| After Low-Pass (4500 Hz)      | 16.26    | 1,704,153  | 1305.43 |
| After Bandpass (300–4000 Hz)  | 1.86     | 46,981,832 | 6854.33 |

### SNR Change vs Before Filtering

| Filter                   | SNR Change |
|--------------------------|------------|
| Low-Pass (4500 Hz)       | -3.74 dB   |
| Bandpass (300–4000 Hz)   | -18.14 dB  |

**Conclusion: Low-Pass Filter (4500 Hz) performs better** in this experiment. The Bandpass Filter removes too much signal energy outside the 300–4000 Hz band, increasing distortion relative to the original signal.

## Properties of FIR Filters

- **Stability**: Always stable because there is no feedback loop
- **Linear Phase**: Can preserve the shape of signals across all frequencies
- **Ease of Design**: Only requires additions and multiplications, no division
- **Finite Response**: The impulse response settles to zero after N samples

## Performance Metrics

- **SNR (Signal-to-Noise Ratio)**: Measures signal power relative to noise power. Higher is better.
- **MSE (Mean Squared Error)**: Average squared difference between filtered and original signal. Lower is better.
- **RMSE (Root Mean Squared Error)**: Square root of MSE, in the same unit as the signal. Lower is better.

## Requirements

    pip install numpy scipy matplotlib

## Usage

    jupyter notebook do_an_4.ipynb

Run all cells in order. The notebook will output:
- SNR, MSE, RMSE printed to console
- FFT comparison charts saved as fft_comparison.png
- Filter frequency response saved as filter_response.png
- Filtered audio files: filtered_lowpass.wav and filtered_bandpass.wav

## File Structure

    do_an_4.ipynb           Main notebook
    smallgirl.wav           Input audio file
    filtered_lowpass.wav    Output after Low-Pass filtering
    filtered_bandpass.wav   Output after Bandpass filtering
    fft_comparison.png      FFT plots of all four signals
    filter_response.png     Frequency response of both filters
    README.md
