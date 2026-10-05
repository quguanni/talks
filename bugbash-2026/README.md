# How Long Linux Kernel Bugs Actually Hide

Lightning talk · BugBash 2026, Antithesis · Eaton Hotel, Washington DC · April 23–24 2026

---

A correctness conference full of people building better ways to catch bugs. This was the
measurement instead: twenty years of a shipping kernel, 125,183 bug-fix pairs, and how long
each bug actually sat there before anyone noticed.

Mean lifetime 2.1 years, median 0.7, with a tail past twenty. Race conditions last roughly
five times longer than anything else, because they do not crash — they silently corrupt state,
and the test sequence that would expose them never runs.

[Recording](https://quguanni.com/talks) (9 min).

**Data** · [kernel-vuln-data](https://github.com/quguanni/kernel-vuln-data)
