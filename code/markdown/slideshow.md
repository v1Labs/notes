# Slideshows in Markdown

In a [previous note](https://github.com/v1Labs/notes/blob/main/code/markdown/extending-react-parser.md), I showed a simple example of extending markdown in React to convert image urls into `<img>` tags.

Now let's take it to the next level and convert markdown into slideshows. [See the demo](https://h0ly0.csb.app/).

## App.js

```
import React from "react";
import ParseMarkdown from "./ParseMarkdown";
import "./styles.css";

const markdown = `
::: slideshow
- slide one
- slide two
- slide three
- slide four
- slide five
:::
`

export default function App() {
  return (
    <div className="App">
      {ParseMarkdown(markdown)}
    </div>
  );
}
```

This is the file you'll embed your slideshow into. Take note of the format for the slideshow in the markdown.

```
::: slideshow
- slide one
- slide two
- slide three
- slide four
- slide five
:::
```

We'll be using [markdown-it-container](https://github.com/markdown-it/markdown-it-container) to wrap `<div class="slideshow">` around an unordered list. Each list element will be converted into a slide in our custom parser.

## ParseMarkdown.js

```
import React from "react";
import MarkdownIt from 'markdown-it';
import MarkdownContainer from 'markdown-it-container';
import parse from 'html-react-parser';

import Slideshow from './Slideshow.js';

const md = new MarkdownIt();
md.use(MarkdownContainer, 'slideshow');

const ParseMarkdown = (markdown) => {
  return parse(md.render(markdown), {
    replace: domNode => {
      if (!domNode.parent && domNode.attribs?.class === 'slideshow') {
        const domContainer = domNode.children?.filter(c => c.name === 'ul');
        const domChildren = domContainer[0].children?.filter(c => c.name === 'li');
        const slides = domChildren.map(c => c.children[0].data);
        return <Slideshow slides={slides} />
      }
    }
  });
}

export default ParseMarkdown;
```

This is our custom parser. This chunk of code will convert `::: slideshow` into `<div class="slideshow">`.

```
const md = new MarkdownIt();
md.use(MarkdownContainer, 'slideshow');
```

This chunk of code will take the custom rendered slideshow div and replace it with the react `<Slideshow>` element. The filters and mapping is just a way to take the complex `domNode` data structure and convert it to a simple array for the `<Slideshow>`.

```
const ParseMarkdown = (markdown) => {
  return parse(md.render(markdown), {
    replace: domNode => {
      if (!domNode.parent && domNode.attribs?.class === 'slideshow') {
        const domContainer = domNode.children?.filter(c => c.name === 'ul');
        const domChildren = domContainer[0].children?.filter(c => c.name === 'li');
        const slides = domChildren.map(c => c.children[0].data);
        return <Slideshow slides={slides} />
      }
    }
  });
}
```

## Slideshow.js

```

import React, { useState } from "react";
import parse from 'html-react-parser';

const Slideshow = ({slides = []}) => {
  const [activeSlide, setActiveSlide] = useState(1);
  return (
    <div className={`slideshow slide-${activeSlide}`}>
      {slides.map((slide, index) => (
        <div className="slide" key={`slide_${index}`}>
          {slide}
        </div>
      ))}
      <div className="pagination">
        {slides.map((slide, index) => (
          <div className={`dot ${activeSlide === index+1 ? 'active' : ''}`} key={`dot_${index+1}`} onClick={() => setActiveSlide(index+1)} />
        ))}
      </div>
    </div>
  );
};

export default Slideshow;
```

By creating our own react element we can do a few simple things to enable the slideshow.

- Add state for the `activeSlide` which determines which slide to show.
- The slideshow div will include the active slide class like this `<div class="slideshow slide-1">`. This class is useful for CSS.
- The pagination with dots allows the user to make a different slide active.

## styles.css

```
.App {
  font-family: sans-serif;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
}

a {
  color: #000;
}

.slideshow {
  position: relative;
  width: 700px;
  height: 450px;
  overflow: hidden;
}

.slideshow .slide {
  background: #f2f2f2;
  position: absolute;
  top: 0;
  left: 0;
  width: 700px;
  height: 400px;
  display: flex;
  align-items: center;
  justify-content: center;
  transform: translateX(150%);
  opacity: 0;
  transition: all 350ms ease-in-out;
}

.slideshow .pagination {
  position: absolute;
  bottom: 0;
  height: 50px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  align-items: center;
  justify-content: center;
}

.slideshow .pagination .dot {
  width: 15px;
  height: 15px;
  background: #eaeaea;
  cursor: pointer;
  border-radius: 100%;
  margin: 0 3px;
  transition: all 120ms ease-in-out;
}

.slideshow .pagination .dot.active {
  background: #03131e;
}

.slideshow.slide-1 .slide:nth-child(1),
.slideshow.slide-2 .slide:nth-child(2),
.slideshow.slide-3 .slide:nth-child(3),
.slideshow.slide-4 .slide:nth-child(4),
.slideshow.slide-5 .slide:nth-child(5),
.slideshow.slide-6 .slide:nth-child(6),
.slideshow.slide-7 .slide:nth-child(7),
.slideshow.slide-8 .slide:nth-child(8),
.slideshow.slide-9 .slide:nth-child(9),
.slideshow.slide-10 .slide:nth-child(10) {
  transform: translateX(0%);
  opacity: 1;
}

.slideshow.slide-2 .slide:nth-child(1),
.slideshow.slide-3 .slide:nth-child(1),
.slideshow.slide-3 .slide:nth-child(2),
.slideshow.slide-4 .slide:nth-child(1),
.slideshow.slide-4 .slide:nth-child(2),
.slideshow.slide-4 .slide:nth-child(3),
.slideshow.slide-5 .slide:nth-child(1),
.slideshow.slide-5 .slide:nth-child(2),
.slideshow.slide-5 .slide:nth-child(3),
.slideshow.slide-5 .slide:nth-child(4),
.slideshow.slide-6 .slide:nth-child(1),
.slideshow.slide-6 .slide:nth-child(2),
.slideshow.slide-6 .slide:nth-child(3),
.slideshow.slide-6 .slide:nth-child(4),
.slideshow.slide-6 .slide:nth-child(5),
.slideshow.slide-7 .slide:nth-child(1),
.slideshow.slide-7 .slide:nth-child(2),
.slideshow.slide-7 .slide:nth-child(3),
.slideshow.slide-7 .slide:nth-child(4),
.slideshow.slide-7 .slide:nth-child(5),
.slideshow.slide-7 .slide:nth-child(6),
.slideshow.slide-8 .slide:nth-child(1),
.slideshow.slide-8 .slide:nth-child(2),
.slideshow.slide-8 .slide:nth-child(3),
.slideshow.slide-8 .slide:nth-child(4),
.slideshow.slide-8 .slide:nth-child(5),
.slideshow.slide-8 .slide:nth-child(6),
.slideshow.slide-8 .slide:nth-child(7),
.slideshow.slide-9 .slide:nth-child(1),
.slideshow.slide-9 .slide:nth-child(2),
.slideshow.slide-9 .slide:nth-child(3),
.slideshow.slide-9 .slide:nth-child(4),
.slideshow.slide-9 .slide:nth-child(5),
.slideshow.slide-9 .slide:nth-child(6),
.slideshow.slide-9 .slide:nth-child(7),
.slideshow.slide-9 .slide:nth-child(8),
.slideshow.slide-10 .slide:nth-child(1),
.slideshow.slide-10 .slide:nth-child(2),
.slideshow.slide-10 .slide:nth-child(3),
.slideshow.slide-10 .slide:nth-child(4),
.slideshow.slide-10 .slide:nth-child(5),
.slideshow.slide-10 .slide:nth-child(6),
.slideshow.slide-10 .slide:nth-child(7),
.slideshow.slide-10 .slide:nth-child(8),
.slideshow.slide-10 .slide:nth-child(9) {
  transform: translateX(-150%);
}
```

Here is the basic CSS for powering the slideshow display and animations between slides. Most of the styling is a basic flexbox layout. But there are a few things to note here.

- By default, slides are translated (moved) off to the right with `transform: translateX(150%);`
- I'm using `transform: translateX(0);` on slide elements associated with the active slide to center the slide `.slideshow.slide-1 .slide:nth-child(1)`
- If the active slide is greater than a slide element, we need to position it off to the left with `transform: translateX(-150%);`. Notice the wall of selectors here. This is really ugly and maxes out at 10 slides in this example. If CSS had better support for variables, this could be much cleaner.

## Thoughts

There is a lot I could do here to clean things up and extend slideshows even more. Feel free to submit an [idea in discussions](https://github.com/v1Labs/notes/discussions) or submit a PR if you want to contribute.

## Attribution

We're using this in [The Pleasant Box](https://thepleasantbox.com) to embed a slideshow of Youtube videos into workout posts.
