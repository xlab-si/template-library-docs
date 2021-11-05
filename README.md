# Template Library documentation
This is the Template Library documentation (published on https://xlab-si.github.io/template-library-docs).

[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/xlab-si/template-library-docs/Template%20Library%20docs%20workflow?label=ci/cd)](https://github.com/xlab-si/template-library-docs/actions/workflows/docs.yaml)
[![GitHub deployments](https://img.shields.io/github/deployments/xlab-si/template-library-docs/github-pages?label=gh-pages)](https://github.com/xlab-si/template-library-docs/deployments)

## Table of Contents
  - [Description](#purpose-and-description)
  - [Local building and testing](#local-building-and-testing)

## Purpose and description
This repository holds the documentation for the [Template Library](https://xlab-si.github.io/template-library-docs).

## Local building and testing
For the docs we use [Sphinx documentation tool](https://www.sphinx-doc.org/en/master/).
Here we can render Sphinx Documentation from RST files and we use [Read the Docs](https://readthedocs.org/) theme.

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
