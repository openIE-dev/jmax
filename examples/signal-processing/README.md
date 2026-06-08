# signal-processing

FFT of a noisy sinusoid with two embedded frequencies.

```bash
jmax run fft.jm
```

What this shows:
- Sample rate as a variable, `t = 0:1/fs:1` range
- Element-wise sin + addition + scalar broadcast
- `fft()` returning complex spectrum
- Indexing `[1:length(S)/2]` slice
