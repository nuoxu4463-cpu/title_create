# Product Title SKU Master

Codex skill for generating Meituan B2C product long titles, SKU selling specs, and upload-template rows from product text, white-background images, or image spreadsheets.

## Contents

- `product-title-sku-master/SKILL.md`: main skill instructions.
- `product-title-sku-master/references/`: title examples, category long-tail terms, and forbidden words.
- `product-title-sku-master/agents/openai.yaml`: Codex UI metadata.

## Install

Copy `product-title-sku-master` into:

```text
C:\Users\DELL\.codex\skills\product-title-sku-master
```

Then start a new Codex session or reload skills.

## Batch Upload Workflow

The skill supports source workbooks like `商品111.xlsx` that contain WPS `DISPIMG` cell images, and upload templates like `上架product-edit-template.xlsx`.

It fills:

- `主标题`: first 45 title characters.
- `商品补充语`: remaining title characters.
- `SKU1`, `SKU2`, `SKU3`, `SKU5`, `SKU10`: generated SKU specs.
