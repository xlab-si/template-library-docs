# Template Library documentation
This is the Template Library documentation (published on https://xlab-si.github.io/template-library-docs).

[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/xlab-si/template-library-docs/Template%20Library%20docs%20workflow?label=ci/cd)](https://github.com/xlab-si/template-library-docs/actions/workflows/docs.yaml)
[![GitHub deployments](https://img.shields.io/github/deployments/xlab-si/template-library-docs/github-pages?label=gh-pages)](https://github.com/xlab-si/template-library-docs/deployments)

## Table of Contents
  - [Description](#purpose-and-description)
  - [Local building and testing](#local-building-and-testing)
  - [License](#license)
  - [Contact](#contact)
  - [Acknowledgement](#acknowledgement)

## Purpose and description
This repository holds the documentation for the [Template Library].

## Local building and testing
For the docs we use [Sphinx documentation tool].
Here we can render Sphinx Documentation from RST files and we use [Read the Docs] theme.

To test the documentation locally run the commands below:

```console
# create and activate a new virualenv
python3 -m venv .venv && . .venv/bin/activate

# update pip and install Sphinx requirements
pip install --upgrade pip
pip install -r requirements.txt

# build the HTML documentation
sphinx-build -M html docs build

# build the Latex and PDF documentation
sphinx-build -M latexpdf docs build
```

After that you will found rendered documentation HTML files in `build` folder and you can open and view them inside 
your browser. 

## License
This work is licensed under the [Apache License 2.0].

## Contact
You can contact the xOpera team by sending an email to [xopera@xlab.si].

## Acknowledgement
This project has received funding from the European Union’s Horizon 2020 research and innovation programme under Grant 
Agreement No. 825040 ([RADON]).

[Template Library]: https://xlab-si.github.io/template-library-docs
[Sphinx documentation tool]: https://www.sphinx-doc.org/en/master/
[Read the Docs]: https://readthedocs.org/
[Apache License 2.0]: https://www.apache.org/licenses/LICENSE-2.0
[xopera@xlab.si]: mailto:xopera@xlab.si
[RADON]: http://radon-h2020.eu
