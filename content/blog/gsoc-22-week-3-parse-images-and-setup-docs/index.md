---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "GSoC'22 Week #3 - Parse images and setup docs"
summary: "Worked mainly on image based endpoints"
authors: ["Prakhar Gurunani"]
tags: ["GSoC", "52North", "Python", "API"]
categories: ["Tech"]
date: 2022-07-04
lastmod: 2022-07-04
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

_The previous week went without much problems. Some image based endpoints of `/collections` and `/styles` were implemented._

Image based endpoints also use the `uri()` function (see my [previous post](/blog/gsoc-22-week-2-building-the-api-wrapper/) if you haven't read it) to validate the parameters and generate the URI. The endpoint response is a plain `base64` data. The helper function writes the data in binary (`wb`) mode to save it as an image file.

Documentation is an important part of the project and the API wrapper is incomplete without it. The initial plan was to make the docs after finishing the wrapper. But others may find it difficult to test it during the development phase. It would be better to make the docs along the way, while developing the wrapper.

The documentation can be made in a variety of ways. Developing a separate website, using SwaggerHub, or some documentation generation tools. To make it easier, I zeroed down to Sphinx.

Sphinx has multiple ways of generating documentation. One way is to use Python docstrings. Functions docstrings can hold anything. They usually define what the functions does, and it's expected output. Sphinx has a definite format of the docstring which it uses to generate the documentation. A simple example of a Google style Python docstring -

```python
def function_with_types_in_docstring(param1, param2):
    """Example function with types documented in the docstring.

    :pep:`484` type annotations are supported. If attribute, parameter, and
    return types are annotated according to `PEP 484`_, they do not need to be
    included in the docstring:

    Args:
        param1 (int): The first parameter.
        param2 (str): The second parameter.

    Returns:
        bool: The return value. True for success, False otherwise.
    """
```

Sphinx generates the output in the `docs/build` directory, which is basically a collection of HTML, CSS, and JS files.

The API wrapper and the documentation will be completed till the coming week and I will start working on the terminal client soon.
