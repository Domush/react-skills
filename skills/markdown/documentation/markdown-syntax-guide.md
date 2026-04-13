# Markdown Syntax Guide

> Comprehensive reference for CommonMark and GitHub Flavored Markdown (GFM) syntax.
>
> Sources: [Markdown Guide](https://www.markdownguide.org/), [CommonMark Spec](https://commonmark.org/), [GFM Spec](https://github.github.com/gfm/)

---

## Basic Syntax (CommonMark)

### Headings

```markdown
# Heading 1
## Heading 2
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6
```

**Alternative (Setext) syntax** — only supports H1 and H2:

```markdown
Heading 1
=========

Heading 2
---------
```

> **Best practice:** Use ATX style (`#`) headings consistently. Always put a space after the `#`. Surround headings with blank lines.

### Paragraphs

Separate paragraphs with one or more blank lines. Do not indent paragraphs with spaces or tabs.

```markdown
First paragraph.

Second paragraph.
```

### Line Breaks

End a line with two or more spaces (trailing spaces), then type return.

```markdown
First line.  
Second line.
```

> **Best practice:** Use `<br>` for explicit line breaks if your renderer supports HTML. Trailing spaces are invisible and error-prone.

### Emphasis

```markdown
*italic* or _italic_
**bold** or __bold__
***bold italic*** or ___bold italic___
```

> **Best practice:** Use asterisks (`*`) for emphasis, not underscores. Underscores in the middle of words are not always handled consistently (`intra_word_emphasis`).

### Blockquotes

```markdown
> Single-level blockquote
>
> Multiple paragraphs in blockquote

> First level
>> Nested blockquote
```

### Lists

#### Unordered

```markdown
- Item 1
- Item 2
  - Nested item
  - Another nested item
- Item 3
```

> **Best practice:** Use dashes (`-`) consistently. Don't mix `-`, `*`, and `+` in the same document.

#### Ordered

```markdown
1. First item
2. Second item
3. Third item
   1. Sub-item
   2. Sub-item
```

> **Best practice:** Start ordered lists with `1.`. Use `1.` for all items (lazy numbering) OR use sequential numbers — be consistent.

### Code

#### Inline Code

```markdown
Use `code` for inline code spans.
```

#### Code Blocks (Indented)

```markdown
    // Indent each line by 4 spaces
    function hello() {
      return "world";
    }
```

#### Code Blocks (Fenced)

````markdown
```javascript
function hello() {
  return "world";
}
```
````

> **Best practice:** Always use fenced code blocks with a language identifier. Never use indented code blocks.

### Links

```markdown
[Link text](https://example.com "Optional title")

[Reference link][link-id]

[link-id]: https://example.com "Optional title"
```

### Images

```markdown
![Alt text](image.png "Optional title")

![Alt text][image-id]

[image-id]: image.png "Optional title"
```

> **Best practice:** Always include alt text for accessibility (MD045).

### Horizontal Rules

```markdown
---
```

> **Best practice:** Use three dashes `---` consistently. Surround with blank lines.

### Escaping Characters

```markdown
\* Escaped asterisk
\# Escaped hash
\[ Escaped bracket
\| Escaped pipe
```

Characters that can be escaped: `\` `` ` `` `*` `_` `{}` `[]` `()` `#` `+` `-` `.` `!` `|`

---

## Extended Syntax (GFM)

### Tables

#### Aligned tables

> Pipes are aligned in every row, no before/after space requirement.

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Cell 1   | Cell 2   | Cell 3   |
| Cell 4   | Cell 5   | Cell 6   |
```

#### Compact tables

> One space before and after the pipe.

```markdown
| Column 1 | Column 2 | Column 3 |
| --- | --- | --- |
| Cell 1 | Cell 2 | Cell 3 |
| Cell 4 | Cell 5 | Cell 6 |
```

#### Tight tables

> No space before or after the pipe.

```markdown
|Column 1|Column 2|Column 3|
| --- | --- | --- |
|Cell 1|Cell 2|Cell 3|
|Cell 4|Cell 5|Cell 6|
```

#### Column Alignment

> Use colons to align columns.

```markdown
| Left     | Center   | Right    |
|:---------|:--------:|---------:|
| Aligned  | Aligned  | Aligned  |
```

- `:---` = Left-aligned (default)
- `:---:` = Center-aligned
- `---:` = Right-aligned

> **Best practice:** Surround tables with blank lines (MD058). Use leading and trailing pipes. Keep column counts consistent (MD056).

### Task Lists

```markdown
- [x] Completed task
- [ ] Incomplete task
- [ ] Another task
```

### Strikethrough

```markdown
~~Strikethrough text~~
```

### Autolinks

```markdown
https://example.com          <!-- Automatic link in GFM -->
<https://example.com>        <!-- Explicit autolink -->
```

> **Best practice:** Wrap URLs in angle brackets or proper link syntax. Don't use bare URLs (MD034).

### Footnotes

```markdown
Here is a footnote reference.[^1]

[^1]: This is the footnote content.
```

### Definition Lists

```markdown
Term
: Definition of the term

Another Term
: Another definition
```

> **Note:** Supported by some processors (PHP Markdown Extra, MultiMarkdown) but not standard GFM.

### Heading IDs

```markdown
### My Heading {#custom-id}
```

> **Note:** Supported by some processors. GitHub auto-generates IDs from heading text.

---

## YAML Front Matter

```markdown
---
title: Document Title
author: Author Name
date: 2026-01-01
---

# Document Content
```

Most linting tools ignore front matter by default.

---

## HTML in Markdown

Markdown supports inline HTML. Common uses:

```markdown
<br>                          Line break
<sup>superscript</sup>        Superscript
<sub>subscript</sub>          Subscript
<details>
<summary>Click to expand</summary>
Hidden content here.
</details>
```

> **Best practice:** Minimize HTML usage. Use `allowed_elements` in markdownlint config to whitelist necessary HTML tags while keeping MD033 enabled.

---

## References

- [CommonMark Spec](https://spec.commonmark.org/current/)
- [GitHub Flavored Markdown Spec](https://github.github.com/gfm/)
- [Markdown Guide — Basic Syntax](https://www.markdownguide.org/basic-syntax/)
- [Markdown Guide — Extended Syntax](https://www.markdownguide.org/extended-syntax/)
- [Markdown Guide — Cheat Sheet](https://www.markdownguide.org/cheat-sheet/)
