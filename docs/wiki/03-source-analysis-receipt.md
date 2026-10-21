# Source Analysis — Africa Aluminium letterhead

## What the file actually is

`docs/source_materials/Africa aluminium.docx` contains no text. It is a single
full-page image: the company's **blank letterhead**, not a filled invoice.

That matters for the product. There is no existing invoice layout to copy, no
column set, no legal block, no numbering scheme. What exists is a branded page
the company already puts its documents on, and a document generator has to fill
the empty middle of it.

## Verified company details

Read directly off the letterhead. These are no longer placeholders.

| Field | Value |
|---|---|
| Name | AFRICA ALUMINIUM |
| Website | www.africaluminium.com |
| Phone | +222 42 11 22 21 |
| Phone | +222 37 88 88 34 |
| Email | h.beddy@africaluminium.com |
| Email | boune@africaluminium.com |
| Address | Zone industrielle, Port Autonome de Nouakchott |
| Postal | BP 8629, Nouakchott, Mauritanie |

They are seeded onto the organisation record and will appear in the footer of
every generated document.

## Visual system of the document

- Dark green header band with a green wave, wordmark reversed out in white and
  metallic grey, with the African continent set into the letter A.
- Matching green wave at the foot, carrying phones, emails and address.
- Large watermark of the continent mark, centred, low contrast.
- Otherwise white, portrait A4 proportions.

## What still is not known

No tax or registration number appears on the letterhead, and no invoice number
format exists because no invoice was supplied. Neither should be invented. When
the invoice generator is built it will need:

- whether the invoice is a tax document, and any registration line required in
  Mauritania (`docs/wiki/22` #22, #23)
- the numbering the company wants
- whether a signature or stamp is required (`docs/wiki/22` #30)
