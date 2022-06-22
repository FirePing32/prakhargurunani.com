---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "GSoC'22 Week #1 - Getting Started"
summary: "The first week has been great so far, with a smooth onboarding experience."
authors: ["Prakhar Gurunani"]
tags: ["GSoC", "52North", "Python", "API"]
categories: ["Tech"]
date: 2022-06-20
lastmod: 2022-06-20
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
projects: []
---

_This day marks the end of the first week of the coding period. The past week mostly involved setting up the codebase and building the helper functions, which is summarized in brief below._

_I have also written an introductory blog post on the 52°North blog [here](https://blog.52north.org/2022/06/20/introduction-to-the-python-client-for-ogc-maps-api/), sharing my objectives for the next few weeks._

Currently, OGC Maps API does not have a API wrapper or Python client to interact with. This will be my primary task during the programme. ALong with this, I will be writing tests for the helper functions and a new documentation for the API.

The Python package was easy to setup. Then started building the API functions. Since it is not convenient to write URL strings every time making a request to the API, a better way to handle this was to wrap all the endpoints inside a `urls()` class.

```python
class urls:

    def __init__(self):
        self.base_url = "URL_string"

    def get_collection(self):
        return self.base_url + "/collections"
```

Calling `urls().get_collection()` would return the URL for the particular endpoint. This would make it easy to write tests for the endpoints.

Till now, I have written the metadata functions for `/collections`, `/conformance`, and `/map` endpoints. These main endpoints have a separate independent class, which need to be first initiated in order to use them. This would pretty print the output to the terminal, using `json.dumps()`. All the endpoints accept either `application/json` or `image/jpeg` as headers, depending on type of response the server sends. This will serve as a boolean check to ensure the data type that is being returned without actually analyzing the response.

The following week will mostly involve adding other endpoints to the wrapper, along with setting up the terminal client. Endpoints which return images will need to be handled differently, making it optional for the user to download the image, or just return the base64 data.

Cheers !
