---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "GSoC'22 Week #5 - Terminal Client"
summary: "A walk through of building the terminal client for OGC Maps API"
authors: ["Prakhar Gurunani"]
tags: ["GSoC", "52North", "Python", "API"]
categories: ["Tech"]
date: 2022-07-18
lastmod: 2022-07-18
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

The terminal client has been built using Python's `click` library. Other alternative was to use `*args` and `**kwargs` and process CLI arguments accordingly. But keeping the mind the large number of endpoints, with each having optional parameters, this was not an effective long term solution. It would be difficult to scale the application in the future.

`click` has ton of features to build terminal clients for different use cases. I have described my approach which I have implemented to build the terminal client.

A `ctx` object (or a context object) holds the metadata, which can be used to process commands under specific conditions specified by the user. This is a very broad explanation of the context object. Basically it can be used to hold any information which can alter command execution during processing.

Click has a default context object, which is useful in most cases. But click allows us to make our own context object.

```python
class Context(object):

    def __init__(self):
        self.pretty_print = False

pass_context = click.make_pass_decorator(Context, ensure=True)
```

A `click.MultiCommand` can be used to process custom multiple commands. A class object initialized with this argument can be passed inside `@click.command(cls=CLASS_NAME)`.

```python
class OGCmapsCLI(click.MultiCommand):

    def list_commands(self, ctx):
        rv = get_funcs(["collections", "conformance", "maps", "styles", "landing"])
        rv.sort()
        return rv

    def get_command(self, ctx, name):
        try:
            mod = importlib.import_module("ogcmaps.utils.commands")
            return getattr(mod, name)
        except Exception:
            return ctx.fail(f"No such command: {name}")
```

`list_commands` and `get_command` are two default functions of `click` which can be used to read and load commands from different sources. The above class is implemented to build the terminal client, which loads commands from the package files.

A separate file is used to specify the commands and options for each command, which is parsed and passed to the helper functions. This file acts as a bridge between the terminal client and the helper functions.

```python
@click.command(
    "collections_metadata",
    short_help=(
        "Retrieve the list of geospatial data "
        "collections available from this service."
    ),
    context_settings=dict(
        ignore_unknown_options=True,
        allow_extra_args=True,
    ),
)
@click.option(
    "-f",
    default="json",
    help="The format of the response. Accepted values are 'json' or 'html'.",
)
@click.pass_context
def collections_metadata(ctx, f):

    click.echo(printer(ctx, collections_obj.metadata(f=f)))

```
