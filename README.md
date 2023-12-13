# marked-highlight-copy

Highlighting the code block and providing a copy `button`, you can quickly implement code copying using `Clipboard.js`

# Usage

You will need to provide a function that transforms the `code` to html.

```js
import {Marked} from "marked";
import {markedHighlight} from "marked-highlight-copy";
import hljs from 'highlight.js';

// or UMD script
// <script src="https://cdn.jsdelivr.net/npm/marked/lib/marked.umd.js"></script>
// <script src="https://cdn.jsdelivr.net/npm/marked-highlight/lib/index.umd.js"></script>
// const {markedHighlight} = globalThis.markedHighlight;
const marked = new Marked(
  markedHighlight({
    langPrefix: 'hljs language-',
    highlight(code, lang) {
      const language = hljs.getLanguage(lang) ? lang : 'plaintext';
      return hljs.highlight(code, { language }).value;
    }
  })
);


marked.parse(`
\`\`\`javascript
const highlight = "code";
\`\`\`
`);
// <div class='code-box'><button class='copy-btn'>复制</button>
// <pre><code class="hljs language-javascript">
//   <span class="hljs-keyword">const</span> highlight = <span class="hljs-string">&quot;code&quot;</span>;
// </code></pre></div>
```

The `async` option should be set to `true` if the `highlight` function returns a `Promise`.

```js
import {Marked} from "marked";
import {markedHighlight} from "marked-highlight-copy";
import pygmentize from 'pygmentize-bundled';

const marked = new Marked(
  markedHighlight({
    async: true,
    highlight(code, lang) {
      return new Promise((resolve, reject) => {
        pygmentize({ lang, format: 'html' }, code, function (err, result) {
          if (err) {
            resolve(err);
            return;
          }

          resolve(result.toString());
        });
      });
    }
  })
)

marked.parse(`
\`\`\`javascript
const highlight = "code";
\`\`\`
`);
// <div class='code-box'><button class='copy-btn'>复制</button>
// <pre><code class="language-javascript">
//   <div class="highlight">
//     <pre>
//       <span class="kr">const</span> <span class="nx">highlight</span> <span class="o">=</span> <span class="s2">&quot;code&quot;</span><span class="p">;</span>
//     </pre>
//   </div>
// </code></pre></div>
```

## `options`

| option |  type  | default | description |
|--------|--------|---------|:------------|
| async  | boolean | `false` | If the highlight function returns a promise set this to `true`. Don't forget to `await` the call to `marked.parse` |
| langPrefix | string | `'language-'` | A prefix to add to the class of the `code` tag. |
| highlight | function | `(code: string, lang: string) => {}` | Required. The function to transform the code to html. |

## style copy button

```css
.code-box {
	position: relative;
	/* user style */
}

.copy-btn {
	position: absolute;
	top: 0;
	right: 0
	/* user style */
}
```

## copy

When rendering the html you can add the copy function.

```js
import Clipboard from 'clipboard'

document.body.appendChild(htmlElement)

new Clipboard('.copy-btn')
```
