---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "GSoC'22 - Final Blog Post"
summary: "Summary of the work I did during the last few weeks of GSoC"
authors: ["Prakhar Gurunani"]
tags: ["GSoC", "52North", "Python", "API"]
categories: ["Tech"]
date: 2022-08-05
lastmod: 2022-08-05
featured: true
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: ["ogc-maps-api-client"]
---

I have had an amazing journey so far as a GSoC contributor. I got to learn a hell lot of things, and various open source practices. This is the final blog post where I have shared insights about the work I did in the last few weeks of GSoC.

The terminal client works perfectly as expected, and outputs errors for wrong/no input. It also reports exactly which flags are missing. Each command and flag has helper text to give user information what parameters are needed for a particular map.

The helpers functions are now completely covered with tests. The functions use pre-defined inputs, and the responses are mocked using pytest's `fixture` function.

```python
# Mock response
@fixture
def get_collection_keys():
    yield [
        "links",
        "title",
        "extent",
        "crs",
        "storageCrs",
        "id",
        "dataType",
        "attribution",
        "minScaleDenominator",
        "minCellSize",
    ]


def test_get_collection(get_collection_keys):
    response = collections.get_collection("blueMarble")

    assert isinstance(response, dict)
    assert set(get_collection_keys).issubset(
        response.keys()
    ), "All keys should be in the response"

```

Since all functions make API calls, tests may take a few seconds to complete. These tests are automated using GitHub actions to ensure safe PR merges.

## Docs

I have used Sphinx to setup docs which I have also shared in one of my [previous post](https://www.prakhargurunani.com/blog/gsoc-22-week-3-parse-images-and-setup-docs/).

Sphinx has various methods for building documentation. One of them is to build from docstrings. Each function can have a docstring describing the parameters, response datatype, error codes, and a lot more.

To make this work, I used the `sphinx.ext.autodoc` extension from the Sphinx API wrapper. I have followed the [Google style guide](https://www.sphinx-doc.org/en/master/usage/extensions/example_google.html) for writing the docstrings.

```python
def get_collection_style(collection_id, style_id, f="json") -> dict:
    """Retrieve the style data for a style in a collection.
    Args:
        collection_id (str): Local identifier of a collection
        style_id (str): An identifier representing a specific style.
        f (str, optional): The format of the response. If no value is provided, the
            accept header is used to determine the format. Accepted values are 'json' or
            'html'.
            `Available values : json, html`
    Returns:
        JSON: Style data of a particular style
    Raises:
        None
    """
    .
    .
```

Source files are a collection of files created by Sphinx which define how the documentation is organized. These files are used by Sphinx to build the docs.
```bash
sphinx-apidoc -fo docs/source ogcmaps ogcmaps/utils/*
```
These files can be edited manually to exclude things which we don't want to include in the documentation.
Now we can generate the build files for the documentation -
```bash
# Dumps the build files in ./_build dir
sphinx-build docs/source _build
```

The `_build` contains all the vanilla HTML, CSS and JS. So it can be hosted on platforms supporting static site generation.

But it can be quiet cumbersome if we need to do these steps every time we update any function docstring. So I automated this using GitHub actions.

GH pages is the go-to solution for this case. Every time there is a new commit, GH Actions builds the source files and builds the files to a directory. I used a third party package to push these generated build files to the `gh-pages` branch, which automatically serves them to the internet.

```yml
- name: Deploy
    uses: peaceiris/actions-gh-pages@v3
    if: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    with:
      publish_branch: gh-pages
      github_token: ${{ secrets.GITHUB_TOKEN }}
      publish_dir: _build/
      force_orphan: true
```

The past few weeks have been an amazing journey for me. I got to learn so much from the open source community about writing modular and clean code. I learnt the importance of tests in a codebase and how they help in maintaining code security. Google Summer of Code opens doors to a lot of opportunities and helps in upskilling oneself in this field.

_A note for future contributors: It's not about what tech stack you are working on. It's about who is using your product and how you plan to make it more efficient._

Adios !
