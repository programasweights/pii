# PII detection with ProgramAsWeights

Detect and type personally identifiable information locally with one compiled [ProgramAsWeights](https://programasweights.com) neural program.

[Try the live demo](https://programasweights.com/pii)

## Python

```bash
pip install programasweights --extra-index-url https://pypi.programasweights.com/simple/
```

```python
import json
import programasweights as paw

detect_pii = paw.function("73a0e38b8bbe3427cd1d")

text = "Name: Ada Lovelace\nEmail: ada@example.com\nPIN: 4821"
print(json.loads(detect_pii(text)))
```

```text
[['Ada Lovelace', 'private_person'], ['ada@example.com', 'private_email'], ['4821', 'secret']]
```

The program downloads once and then runs locally. See [`example.py`](example.py) for the complete runnable example.

## Output

The function returns a JSON array of `[text, type]` pairs. Each `text` is copied exactly from the input, and each `type` is one of:

```text
private_person  private_email  private_phone  private_address  private_url
private_date    account_number secret         other_pii
```

## Compile it yourself

The public program was compiled from the English specification in [`spec.txt`](spec.txt). [`compile.py`](compile.py) contains the complete compilation script:

```python
from pathlib import Path
import programasweights as paw

program = paw.compile(
    Path("spec.txt").read_text().strip(),
    compiler="paw-ft-bs48",
    public=True,
)

print(program.id)
```

The published compilation used by this repository is:

```text
73a0e38b8bbe3427cd1d
```

Compiling requires a PAW account and API key. Loading the published program by ID does not require recompiling it.

## Results

On an untouched multilingual set of 512 AI4Privacy documents, the published program reached:

- **0.8464 typed-character F1**
- **0.9085 label-agnostic extraction F1**
- **93.9% type accuracy** on characters where the prediction and annotation overlap

We compared compact JSON, reversed JSON, JSON objects, and TSV outputs on a separate 497-document search set, froze the finalists, and selected the published program on another 522 documents before opening the sealed set. All source groups are disjoint. The original compact `[text, type]` JSON interface won; simplifying its specification did not generalize.

See [`RESULTS.md`](RESULTS.md) for every specification, compiled program, failed run, and benchmark result.

## Reproduce the benchmark

The benchmark implementation is under [`scripts/`](scripts), the parser and metrics are under [`src/paw_pii/`](src/paw_pii), and tests are under [`tests/`](tests). Downloaded datasets, model checkpoints, and generated prediction files are intentionally excluded from Git.

```bash
PYTHONPATH=src python -m pytest -q
```

## Citation

If you use this PII program in research, cite Program-as-Weights for the programming paradigm and Compile by Training for the training compiler used to build it.

**[Program-as-Weights: A Programming Paradigm for Fuzzy Functions](https://arxiv.org/abs/2607.02512)**

NeurIPS 2026 · [Preprint](https://arxiv.org/abs/2607.02512)

```bibtex
@inproceedings{zhang2026programasweights,
  title = {{Program-as-Weights: A Programming Paradigm for Fuzzy Functions}},
  author = {Zhang, Wentao and Hotsko, Liliana and Kim, Woojeong and Nie, Pengyu and Shieber, Stuart and Deng, Yuntian},
  booktitle = {The Fortieth Annual Conference on Neural Information Processing Systems},
  year = {2026},
  url = {https://arxiv.org/abs/2607.02512}
}
```

**[Compile by Training: Turning Natural-Language Specifications into Local Neural Functions](https://arxiv.org/abs/2609.04199)**

```bibtex
@inproceedings{paw_2609_04199,
  title = {{Compile by Training: Turning Natural-Language Specifications into Local Neural Functions}},
  author = {Deng, Yuntian and Nie, Pengyu and Shieber, Stuart},
  booktitle = {EMNLP 2026 System Demonstrations},
  year = {2026},
  url = {https://arxiv.org/abs/2609.04199}
}
```
