# Statistics

Summary statistics, hypothesis tests, and regression are built in.
`stats.jm` computes a mean/std and a linear fit.

```bash
jmax run examples/statistics/stats.jm
```

From the command line:

```bash
jmax stats data.txt              # n, mean, std, min, median, max
jmax ttest a.txt b.txt           # Welch two-sample t-test
jmax anova g1.txt g2.txt g3.txt  # one-way ANOVA
```
