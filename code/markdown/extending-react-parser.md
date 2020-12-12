# Parsing Markdown

Lately, I've been integrating markdown into a few React apps. There are a lot libraries to work with markdown. I've opted to go with a few libraries for the basics. Then I'll show you how I've extended the parser.

## Basic Dependencies

- [Markdown-it](https://www.npmjs.com/package/markdown-it) - Converts a markdown string into an HTML string.
- [html-react-parser](https://www.npmjs.com/package/html-react-parser) - Converts an HTML string into a react element.

## Simple Example

```
import MarkdownIt from 'markdown-it';
import parse from 'html-react-parser';

const md = new MarkdownIt();
const h1 = parse(md.render('# Hello World');
// Generates <h1>Hello World</h1>
// {h1} to use in JSX
```

## Extending the parse function

```
parse(md.render(markdown), {
  replace: domNode => {
    if (domNode.children && domNode.children[0].type === 'text' && !domNode.children[0].data.includes(' ') && domNode.children[0].data.includes('.gif')) {
      return parse(`<img src="${domNode.children[0].data}" />`);
    }
  }
});
```

Not the most readable code but essentially what we're doing is iterating through the parsed react elements and looking for certain conditions. If those conditions are met, replace the react element with a different element.

Here is the logic:

- See if the element (node) has children. I've run into errors without this condition.
- See if the first child has a type of `text`. This basically looks for a line that would be parsed as a `<p>` tag.
- Make sure the line of text doesn't include a space. This is a hack to make sure there is only a single string of characters which implies a URL. A string with spaces, implies words. This does't always imply a URL but the next condition makes it highly probable.
- See if the url contains a `.gif` extension.

I agree that all of the above logic is loosey goosey. A better regex would reduce false positives. If I were writing a library for mass consumption, I'd tighten things up. But for personal use, highly probable is good enough.

Once a match is found, instead of rendering `<p>https://example.com/image.gif</p>` we'd render `<img src="https://example.com/image.gif" />` which is much more desirable.

## Attribution

I'm using a variation of the above code to build a custom CMS for [The Pleasant Box](https://thepleasantbox.com). Stay tuned for more examples. I'm planning to build a slideshow component from markdown soon.
