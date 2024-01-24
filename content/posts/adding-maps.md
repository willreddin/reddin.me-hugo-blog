+++
title = 'Adding maps from github gists'
date = 2024-01-24T14:29:24Z
draft = false
tags = ['hello-world', 'maps'] 
+++

## Creating a map in a Gtihub gist using geojson data
You can add geojson in the markdown of gists to easily add a quick unstyled map. Once you've done that you can embed it in the page using a shortcode I added to `layouts/shortcodes` in the markdown:

{{< github-code-snippets 3b4f41a0a6b3fa433932d6b8c66301ed >}}

Hugo has a built in `gist` [shortcode](https://gohugo.io/content-management/shortcodes/) which makes this easier:

{{gist user willreddin}}


