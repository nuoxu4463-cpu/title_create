---
name: product-title-sku-master
description: Generate Meituan B2C medicine delivery product long titles and SKU selling specifications. Use when the user provides a product in 【brand】product format, a product name/specification, a white-background product image, or a spreadsheet of product white-background images and asks for Meituan商品标题, SKU规格, 售卖规格, 长标题, 商品标题优化, 同类知名品牌, or 回填上架表格.
---

# Meituan Product Title SKU Master

## Overview

Generate compliant 55-60 character Meituan product titles, SKU selling specifications, and one comparable well-known brand for B2C medicine delivery products.

Use the bundled references selectively before producing the final answer:

- `references/55-60字商品长标题词表.txt`: category examples and title structures.
- `references/按类目整理症状词与长尾词表.txt`: symptom, audience, body-part, scenario, and long-tail terms by category.
- `references/forbidden-words.md`: forbidden words and safer replacements.

Search or skim only the relevant category sections unless the user asks for broad analysis.

## Input Handling

Accept either:

- Text in the form `【品牌名】商品名`, such as `【云南白药】牙膏`.
- Product name plus known details such as规格、容量、数量、成分、功效方向.
- A white-background product image. Inspect the image first and extract brand, product type,规格, ingredients, and visible claims before generating output.
- A spreadsheet containing product white-background images, plus a product upload/edit template to fill.

If a required product detail is missing, infer conservatively from the product type and reference files. Do not invent medical treatment claims.

## Title Rules

Create one product title with 55-60 characters. Count every Chinese character, English letter, digit, and punctuation mark as 1 character. Use this structure:

`品牌名（如有） + 商品名 + 规格 + 核心功效/适用方向 + 症状长尾词 + 人群/场景词（1-2个）`

Follow these rules:

- Make the first 18-25 characters clearly identify what is being sold.
- Put core benefit or usage direction in the middle, such as `清洁护理`, `体重管理`, `营养补充`, `屏障修护`, `舒缓护理`.
- Put symptom and long-tail terms later, using terms from the category reference file.
- Use only 1-2 audience/scenario terms, such as `成人适用`, `居家常备`, `快递到家`, `保密发货`.
- Avoid OTC drug positioning. Prefer compliant words such as `护理`, `营养`, `管理`, `舒缓`, `清洁`, `修护`, `辅助`.
- Do not use high-risk words such as `治疗`, `治愈`, `根治`, `药效`, `处方`, `消炎杀菌`, `快速见效`.
- Do not use terms listed in `references/forbidden-words.md`; use suggested replacements only when they do not create medical or exaggerated claims.
- Avoid repeated words or repeated short phrases unless the reference pattern makes a single repetition unavoidable.

## Generation Workflow

1. Identify the closest category/赛道 from `55-60字商品长标题词表.txt`.
2. Select relevant symptom, usage, audience, and scenario terms from `按类目整理症状词与长尾词表.txt`.
3. Draft a title using the required structure.
4. Count characters exactly. Revise until the title is 55-60 characters.
5. Check the title twice for repeated words/phrases.
6. Check forbidden words and high-risk medical claims.
7. Keep the character count as an internal verification step; do not show counting details unless the user asks for them.
8. If no compliant 55-60 character title can be made from the available facts, ask one concise question for the missing product detail.

## Spreadsheet Batch Workflow

Use this workflow when the user provides a source workbook such as `商品111.xlsx` and an upload/edit template such as `上架product-edit-template.xlsx`.

1. Inspect both workbooks before generating content. Confirm the source sheet contains one product image per item and confirm the template has a `product_edit` sheet with these columns: `主标题`, `商品补充语`, `SKU1`, `SKU2`, `SKU3`, `SKU5`, `SKU10`.
2. Extract source images in row-major order: sort by row number first, then column number. For WPS/Kingsoft cell images stored as formulas like `_xlfn.DISPIMG("ID_xxx",1)`, map each formula ID through `xl/cellimages.xml` and `xl/_rels/cellimages.xml.rels` to the corresponding `xl/media/image*.png`. For normal Excel floating images, use image anchors.
3. Generate one title and five SKU strings per source image, preserving source image order exactly.
4. Split each final title internally by exact character count:
   - `主标题`: first 45 characters.
   - `商品补充语`: character 46 through the end.
5. Fill the template row-by-row starting from the first data row, normally row 2. Result 1 goes to row 2, result 2 to row 3, and so on.
6. Write complete SKU strings to the matching quantity columns:
   - `SKU1`: the 1-unit SKU line.
   - `SKU2`: the 2-unit SKU line.
   - `SKU3`: the 3-unit SKU line.
   - `SKU5`: the 5-unit SKU line.
   - `SKU10`: the 10-unit SKU line.
7. Preserve existing template columns such as `upc`, `商品主图文件地址`, `图片详情文件地址`, and `价格` unless the user explicitly asks to change them.
8. If there are more generated products than existing template rows, append rows and copy the nearest existing data-row style/validation where possible.
9. Save to a new workbook by default. Do not overwrite the user's template unless explicitly requested.
10. After filling, verify every generated row has non-empty `主标题`, `商品补充语`, `SKU1`, `SKU2`, `SKU3`, `SKU5`, and `SKU10`.

## SKU Rules

Return 3-5 SKU selling specifications in a code block. Each line must follow this pattern:

`数量 + 【规格名称】 + 产品特点`

Use quantity units that match the product, such as `盒`, `瓶`, `支`, `袋`, `片`, or `箱`. Tie each feature phrase to the product's actual usage, ingredients, or benefit direction.

Preferred SKU names include:

- `体验装`
- `尝鲜装`
- `疗程装 80%人选择`
- `家用装`
- `囤货装`
- `多支装`

Avoid `家庭装`; use `家用装`, `家庭用`, `多支装`, or `套装` instead.

## Required Output

For direct chat output, output in Chinese and keep the title easy to copy:

```text
商品标题（X字）：[完整标题]
```

Then output the SKU specifications:

```text
1盒【体验装】产品特点
2盒【尝鲜装】产品特点
3盒【疗程装 80%人选择】产品特点
5盒【家用装】产品特点
10盒【囤货装】产品特点
```

Finally output the comparable brand:

```text
同类知名品牌：[品牌名]
```

For spreadsheet batch output, do not include chat-style code blocks in cells. Fill only the target workbook columns and summarize the saved file path.

## Quality Bar

Before answering, verify:

- Title length is 55-60 characters.
- The first 18-25 characters identify the product.
- For direct chat output, SKU lines are inside a code block.
- Forbidden terms and high-risk treatment claims are absent.
- The comparable brand is in the same product category.
- For spreadsheet filling, `主标题` is at most 45 characters and `商品补充语` contains the remaining title characters.
- For spreadsheet filling, generated rows preserve the exact source image order.
