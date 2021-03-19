---
id: installation
title: Installation
sidebar_label: Installation
---
You can install Tacto through `pip`:
```bash
pip install tacto
```

Alternatively, you can manually clone the repository and install the package using:
```bash
git clone https://github.com/facebookresearch/tacto.git
cd tacto
pip install -e .
```

### Additional Packages for Examples
Additional packages ([torch](https://github.com/pytorch/pytorch), [gym](https://github.com/openai/gym), [pybulletX](https://github.com/facebookresearch/pybulletX))
are required to run the demos in `examples/`.
You can install them by `pip install -r requirements/examples.txt`.
