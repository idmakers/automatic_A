---
title: "Adding a Link Tag to the HTML Head Section with Dash"
tags:
  - dash
  - html
  - link tag
---

# Using the Title

## Core Problem
When building web applications with Dash, adding meta tags to the head section is straightforward. However, linking external resources using the <link> tag in the same way can be tricky.

## Solution & Analysis
To add a link tag to the HTML head section with Dash, you need to create an HTML Link element and specify its href attribute. You can do this by creating a custom component that wraps the Link element.

### Example Code

```python
from dash import Dash, html

app = Dash(
    __name__, 
    meta_tags= [
        dict(name = 'description', content = 'Description of the site'),
        {'http-equiv':'content-language', 'content':'pt-br'},
        {'http-equiv':'Link', 'content':"""<https://example.com>;rel=""canonical""""},
    ]
)

app.layout = [
    html.Header(
        # Create a custom Link component
        html.Link(href='https://example.com', rel='canonical')
    ),
    html.P('Some text')
]

# Create a custom Link component with the link tag
class CustomLink(html.A):
    def __init__(self, href, **props):
        super(CustomLink, self).__init__(href, **props)
        self.props['rel'] = 'canonical'

app.layout = [
    html.Header(
        CustomLink(href='https://example.com', rel='canonical')
    ),
    html.P('Some text')
]

if __name__ == '__main__':
    app.run(debug=True)
```

### Explanation
In this example, we create a custom Link component called `CustomLink` that inherits from Dash's `html.A`. We override the `props` method to add the `rel` attribute with the value 'canonical'. This allows us to specify the link tag in our HTML head section.

## Conclusion
By using a custom Link component, you can easily add the <link> tag to your HTML head section with Dash. Remember to specify the href and rel attributes correctly to achieve the desired effect.