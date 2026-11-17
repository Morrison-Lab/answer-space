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

### Student copy and answer key

Quarto lets a document's front matter, and a directory's `_metadata.yml`, override the project's profile files.
So an `answer-space` key set there also reaches the answer key,
even when `_quarto-solution.yml` sets `answer-space: false`.
Two layouts avoid that:

- Set the key only in project and profile files:
  `answer-space: false` in `_quarto.yml` or the solution profile,
  and the size (or `"0"`, for attributes only) in the student profile.
- If only some documents get space, set the size in their front matter,
  and list the filter only in the student profile's `filters`,
  so the answer-key render never runs it.

Avoid naming the filter in a document that is also rendered on its own,
outside the project, such as a generated student `.qmd`:
there the extension cannot be found and the render stops.

### One exercise

An `answer-space` attribute on an exercise overrides the default for it:

```markdown
::: {#exr-write-proof answer-space="4in"}
Prove that ...
:::
```

The attribute takes the same values as the metadata key.
`answer-space="0"` or `"false"` gives that exercise no space, and `answer-space="page"` a new page.
`answer-space: false` in metadata overrides every attribute.

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
It names the filter as `answer-space`, because it uses this repository's own `_extensions/answer-space/` copy;
a document in another repository names it `Morrison-Lab/answer-space`.
