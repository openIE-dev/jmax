# signal-processing

The FFT magnitude spectrum of a simple periodic signal.

```bash
jmax run examples/signal-processing/fft.jm
# -> [0, 0, 4, 0, 0, 0, 4, 0]   (energy at the signal's frequency bins)
```

What this shows:
- Vectors as first-class values
- `fft()` returning the magnitude spectrum

The rest of the DSP surface (`ifft`, `spectrogram`, windows, FIR/IIR filters)
is in the [reference](https://openie-dev.github.io/jmax/reference/builtins.html#signal-processing).
