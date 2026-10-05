# Why Most ML Vulnerability Detection Fails

**(And What Actually Worked for Kernel Bugs)**
[un]prompted, San Francisco, March 2026 · [slides](slides.pptx)

---

### The dataset was free

The kernel community's `Fixes:` convention has been quietly producing labelled training data
for twenty years. 125,183 bug-fix pairs with exact temporal provenance and zero annotation
cost. Split train to 2022, validate 2023, test 2024, so the model has to predict forward
rather than interpolate.

Only 28% of fix commits carry a `Fixes:` tag. The other 72% are unlabelled, and that is the
real limit on the dataset.

### Before training anything, I built nine ways to cheat

Bag-of-words TF-IDF on the diff reaches 0.825 AUC. Diff size alone reaches 0.779. Any neural
model that cannot substantially beat those has learned how big a commit is, not what makes it
dangerous. Most published work in this space does not report a baseline this adversarial.

### At 512 tokens, a transformer reads the commit message, not the code

| Setup | AUC |
|---|---|
| CodeBERT on full diffs, 512 tokens | 0.815 |
| CodeBERT on commit subjects only | 0.808 |

Seven thousandths of AUC for all of the code. At 8,192 tokens ModernBERT reaches 0.852 and
truncation on vulnerable commits falls from 91.8% to 14.8%. Context length unlocked code
understanding. Architecture did not.

### Vulnerability patterns have a shelf life

Every model degrades from older to more recent test data. Hand-engineered XGBoost falls from
0.874 to 0.809. ModernBERT falls from 0.870 to 0.829, so it degrades less, but it degrades.
If you are not retraining on recent commits, your detector is quietly getting worse right now.
This applies to static analysis rules and LLM agents too.

### The ceiling

`d205dc40798d`, netfilter, August 2006. A deadlock fix removed a `nf_conntrack_put()` and
replaced it with nothing. The matching `nf_conntrack_get()` stayed. That is a refcount leak,
and it survived until August 2025.

The model scores it 0.51. A coin flip.

The vulnerability is the *absence* of an operation. Every feature computed over the diff says
the change is fine, because the change is fine in isolation. Detecting it requires tracing
refcount state across the call graph. This is information-theoretic, not an architecture
problem, and no amount of model will fix it.

### Three things to take home

1. **Build adversarial baselines before you build models.** If you cannot beat bag-of-words,
   you have built a fancy tokenizer.
2. **Where you look matters more than how you look.** Average bug lifetime is 1.4 years in
   `gpu/i915` and 4.2 years in `drivers/can`. Same kernel, same language. The difference is
   fuzzing infrastructure and reviewer coverage. Point your tools at the subsystems nobody
   is watching.
3. **The hardest bugs don't crash.** Race conditions hide five years. Refcount leaks persist
   for decades. Crash-finding is exciting and it is a different problem.

> The hard problem is detecting what isn't there.

---

**Data** · [kernel-vuln-data](https://github.com/quguanni/kernel-vuln-data)
**Code** · [kernel-archaeology](https://github.com/quguanni/kernel-archaeology)
