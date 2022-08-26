---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "GSoC'22 Week #2 - Building the API wrapper"
summary: "Spent the last week working with the endpoints and building helper functions"
authors: ["Prakhar Gurunani"]
tags: ["GSoC", "52North", "Python", "API"]
categories: ["Tech"]
date: 2022-06-27
lastmod: 2022-06-27
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

_Last week mainly involved understanding the different API endpoints, and creating API definitions for the same._

There are mainly three API endpoints - `/collections`, `/styles`, and `/map`. Each of them have multiple properties, and separate API definitions of their own. To make the API wrapper scalable in the future, it would be better to have separate modules for each of the above endpoints.

I started working on the helper functions. Most of them just required making GET requests, and returning the response as JSON. Some endpoints with multiple optional arguments have to be dealt with differently.

Optional arguments can be passed into the helper functions using `**kwargs`. But to verify if the parameter is valid, a validation was needed. The server would anyways return an error if the param was incorrect, but it would be better to handle this client side. So I created a utility function, which would not only validate the parameters, but would also return the endpoint URI if the params are correct. If params are not correct, a `ValueError` would be returned.

```python
def uri(url, keys, **kwargs) -> dict:

    for key, value in kwargs.items():
        if key in keys:
            pass
        else:
            raise ValueError({"Invalid parameter": f"{key}"})

    query_string = "?" + "&".join(f"{key}={value}" for key, value in kwargs.items())
    endpoint = url + query_string

    return {"endpoint": endpoint}

```

The next week will mainly focus on parsing the responses from the endpoints which return an image. Most of these endpoints have lots of optional parameters which will be partially handled by the above function. The image content (base64 data) will be handled by another utility function.
