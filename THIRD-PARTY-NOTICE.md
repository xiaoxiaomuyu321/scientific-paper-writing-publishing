# Third-Party Notice — Book Provenance

This skill embeds the full extracted text of two commercial books as a
local, private, searchable knowledge base. The books themselves are **not**
included in this repository; the source files are kept locally by the
maintainer for provenance and rebuilds. Use is subject to the repository
[LICENSE](LICENSE).

## Embedded book text

| # | Book | Edition | ISBN (stored as source id) | Role in build |
|---|---|---|---|---|
| 1 | Barbara Gastel, Robert A. Day — *How to Write and Publish a Scientific Paper* | 8th ed. (2016, Cambridge University Press) | `urn:isbn:9781316640432` | PDF text layer = text authority; DOCX = section structure |
| 2 | Kate L. Turabian — *A Manual for Writers of Research Papers, Theses, and Dissertations: Chicago Style for Students and Researchers* | 7th ed. (2003; 2007 paperback printing, University of Chicago Press) | `urn:isbn:9780226823379` | PDF text layer = text authority; DOCX = section structure |

## Source-file integrity (SHA-256)

The build sources are kept outside the repository (deliberately excluded —
see [book-dataset-profile.md](references/book-dataset-profile.md)). SHA-256
of each build source:

| File | SHA-256 |
|---|---|
| `how_to_write_and_publish_a_scientific_paper.pdf` | `9B8D562AA3F28F0F942990365E555D18F276B8F7E9BF53B742CF202F3B5422A7` |
| `how_to_write_and_publish_a_scientific_paper.docx` | `C003927737427B690360559C0E773C04D31318BB18B0BCC90DFF101AA1241610` |
| `A manual for writers of research papers, theses, and dissertations.pdf` | `9D1156CA4F8347F53A5F7585EBDAD2FA74FF43AB0F84921DA208B3FE41371283` |
| `A manual for writers of research papers, theses, and dissertations.docx` | `0EE848C55752AB1A4EF7612DFAE712282E1389262AE44CFAC812BA3FEE6B6C5E` |

Rebuild the database from your own legally obtained copies with
`scripts/build_kb.py` (usage in [README.md](README.md)); see
[references/knowledge-base.md](references/knowledge-base.md) for the
rebuild rules.

## Licensing note

The copyright terms of both books have not been verified against the
publishers' current policies. Treat the embedded text as private
study/assistance material and do not redistribute it.
