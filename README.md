# answer-space

A [Quarto](https://quarto.org/) extension that leaves blank space after each exercise in PDF output,
so students can write their answers on a printed copy.
HTML output is unchanged.

## Installation

```bash
quarto add Morrison-Lab/answer-space
```

This installs the extension in `_extensions/Morrison-Lab/answer-space/`.

## Usage

Add the filter, and set the space once in the document or project metadata:

```yaml
filters:
  - Morrison-Lab/answer-space
answer-space: 2in
```

In PDF output, the filter then adds `\vspace{2in}` after every div whose id starts with `exr-`.

`answer-space` takes:

- a TeX length, such as `2in`, `5cm` or `30mm`;
- `page`, to start a new page after each exercise;
- `true`, for the default of `2in`;
- `false`, to add no space, for example in a solution profile.

A missing `answer-space` key means `true`.
A value that is none of these stops the render with an error,
rather than producing a PDF with no room to write.

### One exercise

An `answer-space` attribute on an exercise overrides the default for it:

```markdown
::: {#exr-write-proof answer-space="4in"}
Prove that ...
:::
```

`answer-space="0"` gives that exercise no space, and `answer-space="page"` a new page.

### Between the parts of one exercise

The shortcode adds space at any point:

```markdown
{{< answer-space 1in >}}
```

With no argument, it uses the metadata default.
It adds nothing when the metadata says `answer-space: false`, and nothing outside PDF output.

## How it works

The filter runs at the `pre-ast` stage,
because Quarto turns exercise divs into its own theorem nodes before the default filter stage,
where a filter would never see an `exr-` div.

See [`example.qmd`](example.qmd) for a demonstration.
