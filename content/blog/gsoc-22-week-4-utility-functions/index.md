---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "GSoC'22 Week #4 - Utility functions"
summary: "Various utility functions used by the API wrapper"
authors: ["Prakhar Gurunani"]
tags: ["GSoC", "52North", "Python", "API"]
categories: ["Tech"]
date: 2022-07-11
lastmod: 2022-07-11
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

The API wrapper uses some utility functions to reduce code reuse. It will also help in extending the wrapper functionality in the future.

## `uri`
A URI generator for appending query parameters. The function also checks if any passed parameter is invalid and returns a `ValueError`. It is used by all the endpoints.

## `write_image`
Function to write image data received as response to a file and save it locally in the current working directory.

## `get_data`
Get response from the specified URI. Returns response in JSON/html format as specified by the user.

## `is_mod_function` and `get_funcs`
`get_funcs` is used to fetch all methods present in a module. This would also return defualt python methods like `__dict__` and `__str__`. `is_mod_function` is used to check this and return only user defined methods.

## `printer`
Checks if `-p`/`--prettify` options was passed, and pretty prints the response to the terminal.

## `parse_options`
Used to parse optional parameters from the terminal client.

## Cache
A caching system has been implemented, which caches all GET requests with a validity of 180 seconds. This can be useful when the API wrapper has been used in a python script requiring repetitive data reuse.
