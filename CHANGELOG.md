# ObviousPDF — Changelog

All notable changes to this project are documented in this file.  
This project adheres to [Semantic Versioning](https://semver.org/).

---

## [1.0.3] — 2026-03-22

### Added

- **Text decorations** — Underline, strikethrough, overline, superscript, subscript, text outline, text background (highlight), and drop shadow. All decorations work with standard and embedded fonts, all four text methods, and text alignment. New `PdfTextDecoration` flags enum and new properties on `PdfTextOptions`: `Decoration`, `DecorationColor`, `DecorationThickness`, `Superscript`, `Subscript`, `OutlineColor`, `OutlineWidth`, `BackgroundColor`, `ShadowColor`, `ShadowOffsetX`, `ShadowOffsetY`.

- **Text rotation** — Arbitrary counter-clockwise rotation for all text rendering methods (`AddText`, `AddTextBlock`, `AddTaggedText`, `AddTaggedTextBlock`). Background rectangles, drop shadows, and decoration lines rotate together with the text. New `Rotation` property on `PdfTextOptions` (degrees CCW, default `0`). Implemented via PDF CTM `cm` operator (ISO 32000 §8.3.4).

---

## [1.0.1] — 2026-03-12

### Added

- **Text alignment and width properties** (ISO 32000-2 §9.3, §14.8.5.4)
  - New `Alignment` property on `PdfTextOptions` — `Left` (default), `Center`, `Right`, `Justify`.
  - New `Width` property on `PdfTextOptions` — defines the text box width for alignment calculations.
  - Center and right alignment shift the text x-coordinate; justify uses the Tw (word spacing) operator per ISO 32000-2 §9.3.3.
  - `AddTextBlock()` applies per-line alignment with justified text leaving the last line left-aligned.
  - New `MeasureTextWidth()` method on `PdfPageBuilder` and `PdfEmbeddedFont` for glyph-level text measurement.
  - New `PdfTextAlignment` enum: `Left`, `Center`, `Right`, `Justify`.
  - New accessibility layout attributes on `PdfStructureElement`: `TextAlign`, `Placement`, `LayoutWidth`, `LayoutHeight` — conforming to ISO 14289-1 and ISO 14289-2 §8.2.6.2.
  - New `PdfLayoutTextAlign` enum (`Start`, `Center`, `End`, `Justify`) and `PdfPlacement` enum (`Block`, `Inline`, `Before`, `Start`, `End`).

- **Document parts** (ISO 32000-2 §14.12, PDF 2.0)
  - Organize a single PDF into logical sub-documents for variable-data printing (VDP) workflows.
  - `PdfDocument.CreateDocumentPartRoot()` creates the root DPart node.
  - `PdfDocumentPart` supports leaf nodes (page ranges) and intermediate grouping nodes with key-value metadata.

- **Associated files / file attachments** (ISO 32000-2 §14.13, PDF 2.0)
  - Embed any file inside a PDF using `PdfDocument.AddAssociatedFile()` or `PdfPageBuilder.AddAssociatedFile()`.
  - Supports all relationship types: `Source`, `Data`, `Alternative`, `Supplement`, `Schema`, `EncryptedPayload`, `Unspecified`.
  - Compatible with PDF/A-3 archival workflows (ZUGFeRD / Factur-X).

- **Pronunciation hints** (ISO 32000-2 §14.9.6, PDF 2.0)
  - `PdfStructureElement.Phoneme` and `PhoneticAlphabet` properties for IPA and X-SAMPA pronunciation annotations.
  - Screen readers can correctly pronounce proper names, technical terms, and foreign words.

- **Linearization / fast web view** (ISO 32000 Annex F)
  - `PdfDocument.Linearize = true` reorganizes the PDF so the first page displays before the full file downloads.
  - Linearization parameter dictionary, primary hint stream, and two-phase serialization with post-write patching.

- **Patterns and shadings** (ISO 32000 §8.7)
  - Axial (linear) and radial gradient shadings via `PdfShadingPattern`.
  - Coloured tiling patterns via `PdfTilingPattern`.
  - `FillWithShading()`, `FillWithPattern()`, `FillRectangleWithShading()` methods on `PdfPageBuilder`.

- **Optional content layers** (ISO 32000 §8.11)
  - Named layers that can be shown/hidden in viewer layer panels.
  - `PdfDocument.CreateOptionalContentGroup()`, `PdfPageBuilder.BeginOptionalContent()` / `EndOptionalContent()`.

- **Digital signatures**
  - `PdfDigitalSignature` — sign PDFs with X.509 certificates (PKCS #7 / CMS detached signatures).
  - `PdfDocument.Sign()` produces a verifiable signed PDF.

- **Colour spaces** — DeviceRGB, DeviceGray, DeviceCMYK, CalGray, CalRGB, ICCBased, Lab, and Indexed colour support.

- **PDF/A archival** — PDF/A-1b, PDF/A-2b, PDF/A-3b conformance levels with XMP metadata and output intent.

- **PDF/UA compliance** — PDF/UA-1 and PDF/UA-2 tagged PDF generation with structure tree, alt text, and reading order.

- **Encryption** — AES-128 and AES-256 encryption with owner/user passwords and permission flags.

- **Compact serialization** — Object streams and cross-reference streams for smaller file sizes.

- **CJK text support** — Chinese, Japanese, and Korean text rendering with embedded TrueType fonts.

### Fixed

- **PdfString: UTF-16BE encoding for non-Latin-1 text strings** (ISO 32000 §7.3.4.2)
  - Characters above U+00FF are now correctly encoded as UTF-16BE with BOM prefix, preserving IPA, CJK, and other non-Latin text.

- **PdfString: octal escape codes** — Fixed decimal-instead-of-octal encoding for non-printable bytes in literal strings.

---

## [1.0.0] — 2026-03-01

Initial release.

- PDF generation from scratch conforming to ISO 32000-2:2020.
- Tagged PDF with full structure tree (PDF/UA-1 and PDF/UA-2 support).
- Standard 14 fonts with bundled open-source TrueType substitutes (SIL OFL).
- TrueType/OpenType font embedding with subsetting.
- Vector graphics: lines, rectangles, circles, Bézier curves, clipping paths.
- Images: JPEG, PNG (with alpha), and CCITT Fax (Group 4) embedding.
- Hyperlinks, bookmarks, and named destinations.
- Interactive AcroForm fields: text fields, checkboxes, radio buttons, dropdowns.
- Page labels with Roman, Arabic, and alphabetic numbering.
- Form XObjects (reusable templates).
- Zero external dependencies — pure .NET 8.
