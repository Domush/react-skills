# markdownlint Rules Reference

> Source: [DavidAnson/markdownlint](https://github.com/DavidAnson/markdownlint) — Node.js style checker and lint tool for Markdown/CommonMark files.
>
> Uses the [`micromark`](https://github.com/micromark/micromark) parser and honors the [CommonMark](https://commonmark.org/) specification.
> Additionally supports [GitHub Flavored Markdown (GFM)](https://github.github.com/gfm/) syntax (autolinks, tables, etc.)

---

## Complete Rule List

### Headings

| Rule | Alias | Description |
| ------ | ------- | ------------- |
| MD001 | heading-increment | Heading levels should only increment by one level at a time |
| MD003 | heading-style | Heading style (atx, atx_closed, setext, consistent) |
| MD018 | no-missing-space-atx | No space after hash on atx style heading |
| MD019 | no-multiple-space-atx | Multiple spaces after hash on atx style heading |
| MD020 | no-missing-space-closed-atx | No space inside hashes on closed atx style heading |
| MD021 | no-multiple-space-closed-atx | Multiple spaces inside hashes on closed atx style heading |
| MD022 | blanks-around-headings | Headings should be surrounded by blank lines |
| MD023 | heading-start-left | Headings must start at the beginning of the line |
| MD024 | no-duplicate-heading | Multiple headings with the same content |
| MD025 | single-title / single-h1 | Multiple top-level headings in the same document |
| MD026 | no-trailing-punctuation | Trailing punctuation in heading |
| MD036 | no-emphasis-as-heading | Emphasis used instead of a heading |
| MD041 | first-line-heading / first-line-h1 | First line in a file should be a top-level heading |
| MD043 | required-headings | Required heading structure |

### Lists

| Rule | Alias | Description |
| ------ | ------- | ------------- |
| MD004 | ul-style | Unordered list style (asterisk, plus, dash, consistent, sublist) |
| MD005 | list-indent | Inconsistent indentation for list items at the same level |
| MD007 | ul-indent | Unordered list indentation |
| MD029 | ol-prefix | Ordered list item prefix (one, ordered, one_or_ordered, zero) |
| MD030 | list-marker-space | Spaces after list markers |
| MD032 | blanks-around-lists | Lists should be surrounded by blank lines |

### Whitespace

| Rule | Alias | Description |
| ------ | ------- | ------------- |
| MD009 | no-trailing-spaces | Trailing spaces |
| MD010 | no-hard-tabs | Hard tabs |
| MD012 | no-multiple-blanks | Multiple consecutive blank lines |
| MD027 | no-multiple-space-blockquote | Multiple spaces after blockquote symbol |
| MD028 | no-blanks-blockquote | Blank line inside blockquote |
| MD037 | no-space-in-emphasis | Spaces inside emphasis markers |
| MD038 | no-space-in-code | Spaces inside code span elements |
| MD039 | no-space-in-links | Spaces inside link text |
| MD047 | single-trailing-newline | Files should end with a single newline character |

### Code

| Rule | Alias | Description |
| ------ | ------- | ------------- |
| MD014 | commands-show-output | Dollar signs used before commands without showing output |
| MD031 | blanks-around-fences | Fenced code blocks should be surrounded by blank lines |
| MD040 | fenced-code-language | Fenced code blocks should have a language specified |
| MD046 | code-block-style | Code block style (fenced, indented, consistent) |
| MD048 | code-fence-style | Code fence style (backtick, tilde, consistent) |

### Links & Images

| Rule | Alias | Description |
| ------ | ------- | ------------- |
| MD011 | no-reversed-links | Reversed link syntax |
| MD034 | no-bare-urls | Bare URL used |
| MD042 | no-empty-links | No empty links |
| MD045 | no-alt-text | Images should have alternate text (alt text) |
| MD051 | link-fragments | Link fragments should be valid |
| MD052 | reference-links-images | Reference links and images should use a label that is defined |
| MD053 | link-image-reference-definitions | Link and image reference definitions should be needed |
| MD054 | link-image-style | Link and image style |
| MD059 | descriptive-link-text | Link text should be descriptive |

### Emphasis & Formatting

| Rule | Alias | Description |
| ------ | ------- | ------------- |
| MD049 | emphasis-style | Emphasis style (asterisk, underscore, consistent) |
| MD050 | strong-style | Strong style (asterisk, underscore, consistent) |

### HTML

| Rule | Alias | Description |
| ------ | ------- | ------------- |
| MD033 | no-inline-html | Inline HTML |

### Tables

| Rule | Alias | Description |
| ------ | ------- | ------------- |
| MD055 | table-pipe-style | Table pipe style |
| MD056 | table-column-count | Table column count |
| MD058 | blanks-around-tables | Tables should be surrounded by blank lines |
| MD060 | table-column-style | Table column style |

### Other

| Rule | Alias | Description |
| ------ | ------- | ------------- |
| MD013 | line-length | Line length (default: 80, configurable) |
| MD035 | hr-style | Horizontal rule style (consistent, ---, ***, ___) |
| MD044 | proper-names | Proper names should have the correct capitalization |

---

## Rule Tags (Group Enable/Disable)

| Tag | Rules |
| ----- | ------- |
| accessibility | MD045, MD059 |
| atx | MD018, MD019 |
| atx_closed | MD020, MD021 |
| blank_lines | MD012, MD022, MD031, MD032, MD047 |
| blockquote | MD027, MD028 |
| bullet | MD004, MD005, MD007, MD032 |
| code | MD014, MD031, MD038, MD040, MD046, MD048 |
| emphasis | MD036, MD037, MD049, MD050 |
| hard_tab | MD010 |
| headings | MD001, MD003, MD018–MD026, MD036, MD041, MD043 |
| hr | MD035 |
| html | MD033 |
| images | MD045, MD052, MD053, MD054 |
| indentation | MD005, MD007, MD027 |
| language | MD040 |
| line_length | MD013 |
| links | MD011, MD034, MD039, MD042, MD051–MD054, MD059 |
| ol | MD029, MD030, MD032 |
| spaces | MD018, MD019, MD020, MD021, MD023 |
| spelling | MD044 |
| table | MD055, MD056, MD058, MD060 |
| ul | MD004, MD005, MD007, MD030, MD032 |
| url | MD034 |
| whitespace | MD009, MD010, MD012, MD027, MD028, MD030, MD037, MD038, MD039 |

---

## Inline Configuration Comments

Control rules within a file using HTML comments (not rendered):

```markdown
<!-- markdownlint-disable -->              Disable all rules
<!-- markdownlint-enable -->               Enable all rules
<!-- markdownlint-disable MD001 MD005 -->   Disable specific rules
<!-- markdownlint-enable MD001 MD005 -->    Enable specific rules

<!-- markdownlint-disable-line -->                      Current line
<!-- markdownlint-disable-line MD013 -->                 Current line, specific rule
<!-- markdownlint-disable-next-line -->                  Next line
<!-- markdownlint-disable-next-line no-bare-urls -->     Next line, by alias

<!-- markdownlint-capture -->              Save current config
<!-- markdownlint-restore -->              Restore saved config

<!-- markdownlint-disable-file -->         Disable for entire file
<!-- markdownlint-enable-file -->          Enable for entire file
<!-- markdownlint-disable-file MD013 -->   Disable specific rule for entire file

<!-- markdownlint-configure-file { "line-length": { "line_length": 120 } } -->
```

### Common Inline Patterns

```markdown
<!-- Temporarily disable, then restore -->
<!-- markdownlint-disable -->
any violations you want
<!-- markdownlint-restore -->

<!-- Disable a single rule for one line -->
This is a very long line that exceeds the limit <!-- markdownlint-disable-line MD013 -->
```

---

## Configuration File

File: `.markdownlint.json`, `.markdownlint.jsonc`, or `.markdownlint.yaml`

```json
{
  "default": true,
  "MD003": { "style": "atx" },
  "MD007": { "indent": 2 },
  "MD013": { "line_length": 120 },
  "no-hard-tabs": false,
  "no-inline-html": {
    "allowed_elements": ["br", "details", "summary", "sup", "sub"]
  }
}
```

### Configuration Precedence

1. `default` setting applied first
2. Keys processed top-to-bottom
3. Later values override earlier ones
4. Keys are NOT case-sensitive (rule names, aliases, tags all work)

### Using `extends`

```json
{
  "extends": "markdownlint/style/relaxed",
  "MD013": false
}
```

---

## Related Tools

| Tool | Description |
| ------ | ------------- |
| [markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2) | CLI for Node.js |
| [vscode-markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint) | VS Code extension |
| [markdownlint demo](https://dlaa.me/markdownlint/) | Interactive browser playground |
| [GitHub Super-Linter](https://github.com/super-linter/super-linter) | GitHub Actions integration |

---

## References

- [CommonMark Spec](https://spec.commonmark.org/current/)
- [GitHub Flavored Markdown Spec](https://github.github.com/gfm/)
- [markdownlint GitHub](https://github.com/DavidAnson/markdownlint)
- [markdownlint Rules (full details)](https://github.com/DavidAnson/markdownlint/blob/main/doc/Rules.md)
