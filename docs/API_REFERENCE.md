# ObviousPDF API Reference

> Complete API reference for ObviousPDF — a zero-dependency .NET 8 PDF generation library
> built from the ISO 32000 (PDF 1.7 / PDF 2.0) specification.

---

## Table of Contents

- [PdfDocument](#pdfdocument)
- [PdfPageBuilder](#pdfpagebuilder)
- [PdfTextOptions](#pdftextoptions)
- [PdfDrawOptions](#pdfdrawoptions)
- [PdfColor](#pdfcolor)
- [PdfImage](#pdfimage)
- [PdfEmbeddedFont](#pdfembeddedfont)
- [StandardFont](#standardfont)
- [PageSize](#pagesize)
- [PdfDocumentInfo](#pdfdocumentinfo)
- [PdfFormXObject](#pdfformxobject)
- [PdfPathBuilder](#pdfpathbuilder)
- [Accessibility](#accessibility)
  - [PdfStructureElement](#pdfstructureelement)
  - [StructureType](#structuretype)
  - [PdfAccessibilityChecker](#pdfaccessibilitychecker)
  - [PdfAccessibilityReport](#pdfaccessibilityreport)
  - [PdfArtifactType](#pdfartifacttype)
  - [PdfTableScope](#pdftablescope)
  - [PdfPhoneticAlphabet](#pdfphoneticalphabet)
  - [PdfNoteType](#pdfnotetype)
  - [PdfColorContrast](#pdfcolorcontrast)
- [Annotations](#annotations)
  - [PdfAnnotation](#pdfannotation)
  - [PdfAnnotationType](#pdfannotationtype)
  - [PdfLinkAnnotation](#pdflinkannotation)
  - [PdfStampIcon](#pdfstampicon)
- [Forms](#forms)
  - [PdfAcroField](#pdfacrofield)
  - [PdfAcroFieldType](#pdfacrofieldtype)
- [Document Features](#document-features)
  - [PdfOutlineItem](#pdfoutlineitem)
  - [PdfPageLabel](#pdfpagelabel)
  - [PdfPageLabelStyle](#pdfpagelabelstyle)
  - [PdfOptionalContentGroup](#pdfoptionalcontentgroup)
  - [PdfAssociatedFile](#pdfassociatedfile)
  - [PdfAssociatedFileRelationship](#pdfassociatedfilerelationship)
  - [PdfDocumentPart](#pdfdocumentpart)
- [Security](#security)
  - [PdfDigitalSignature](#pdfdigitalsignature)
  - [PdfEncryption](#pdfencryption)
- [Visual Effects](#visual-effects)
  - [PdfShadingPattern](#pdfshadingpattern)
  - [PdfTilingPattern](#pdftilingpattern)
- [Conformance](#conformance)
  - [PdfAConformanceLevel](#pdfaconformancelevel)
  - [PdfUaConformanceLevel](#pdfuaconformancelevel)
  - [PdfEncryptionAlgorithm](#pdfencryptionalgorithm)
- [Enumerations Reference](#enumerations-reference)
  - [PdfTextDecoration](#pdftextdecoration-flags-enum)
  - [PdfTextAlignment](#pdftextalignment)
  - [PdfTextRenderingMode](#pdftextrenderingmode)
  - [PdfColorSpace](#pdfcolorspace)
  - [PdfColor Named Constants](#pdfcolor-named-constants)
  - [PdfLineCap](#pdflinecap)
  - [PdfLineJoin](#pdflinejoin)
  - [PdfPageLabelStyle](#pdfpagelabelstyle)
  - [PdfLayoutTextAlign](#pdflayouttextalign)
  - [PdfPlacement](#pdfplacement)
  - [PdfPhoneticAlphabet](#pdfphoneticalphabet)
  - [PdfNoteType](#pdfnotetype)
  - [BundledFonts](#bundledfonts)

---

## PdfDocument

**Namespace:** `ObviousPDF`

The main entry point for creating PDF files.

```csharp
var doc = new PdfDocument();
```

### Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `Info` | `PdfDocumentInfo` | (empty) | Document metadata (title, author, subject, etc.) |
| `Language` | `string?` | `null` | BCP 47 language tag (e.g. `"en-US"`). Required for PDF/UA. |
| `DisplayDocTitle` | `bool` | `false` | When `true`, viewer shows title instead of filename. Required for PDF/UA. |
| `PdfVersion` | `string?` | `null` | Explicit PDF version (e.g. `"2.0"`). Auto-inferred when null. |
| `PdfAConformance` | `PdfAConformanceLevel` | `None` | PDF/A archival conformance level. |
| `PdfUaConformance` | `PdfUaConformanceLevel` | `None` | PDF/UA accessibility conformance level. |
| `UseCrossReferenceStreams` | `bool` | `false` | Use compact xref streams (PDF 1.5+). |
| `UseObjectStreams` | `bool` | `false` | Pack objects in compressed streams (PDF 1.5+). |
| `Encryption` | `PdfEncryption?` | `null` | Encryption settings (AES-128 or AES-256). |
| `Linearize` | `bool` | `false` | Enable fast web view (linearized PDF). |
| `IsTagged` | `bool` | (read-only) | Whether tagged PDF is enabled. |
| `StructureTreeRoot` | `PdfStructureElement?` | (read-only) | Root of the structure tree, or null. |
| `RoleMappings` | `IReadOnlyDictionary<string, StructureType>` | (read-only) | Custom-to-standard type mappings. |
| `DocumentPartRoot` | `PdfDocumentPart?` | (read-only) | Root of document parts, or null. |

### Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `AddPage(double width = 612, double height = 792)` | `PdfPageBuilder` | Adds a page with custom dimensions (points). |
| `AddPage((double, double) pageSize)` | `PdfPageBuilder` | Adds a page using a `PageSize` constant. |
| `EnableTaggedPdf()` | `PdfStructureElement` | Enables tagged PDF; returns root Document element. |
| `AddRoleMapping(string customType, StructureType standardType)` | `void` | Maps custom structure type to standard type. |
| `AddOutline(string title, int pageIndex)` | `PdfOutlineItem` | Adds a top-level bookmark. |
| `AddOutline(PdfOutlineItem item)` | `PdfOutlineItem` | Adds an existing outline item. |
| `AddPageLabels(PdfPageLabel label)` | `PdfPageLabel` | Adds a page label range. |
| `AddPageLabels(int startPage, PdfPageLabelStyle style, string? prefix, int logicalStart)` | `PdfPageLabel` | Adds a page label range with parameters. |
| `CreateFormXObject(double width, double height)` | `PdfFormXObject` | Creates a reusable form XObject. |
| `CreateOptionalContentGroup(string name, bool visible = true)` | `PdfOptionalContentGroup` | Creates an OCG (layer). |
| `AddAssociatedFile(PdfAssociatedFile file)` | `PdfAssociatedFile` | Attaches a document-level file. |
| `AddAssociatedFile(string fileName, string mimeType, byte[] data, ...)` | `PdfAssociatedFile` | Attaches a file with properties. |
| `CreateDocumentPartRoot()` | `PdfDocumentPart` | Creates root for document part hierarchy. |
| `Save(string filePath)` | `void` | Writes PDF to a file path. |
| `Save(Stream stream)` | `void` | Writes PDF to any stream. |
| `Sign(string filePath, PdfDigitalSignature signature)` | `void` | Saves and digitally signs the PDF. |
| `Sign(Stream stream, PdfDigitalSignature signature)` | `void` | Saves and signs to a stream. |

---

## PdfPageBuilder

**Namespace:** `ObviousPDF`

Fluent API for adding content to a single PDF page. All methods return `this` for chaining.

### Properties

| Property | Type | Description |
|----------|------|-------------|
| `Width` | `double` | Page width in points. |
| `Height` | `double` | Page height in points. |

### Text Methods

| Method | Description |
|--------|-------------|
| `AddText(string text, double x, double y, PdfTextOptions? options)` | Single line of text at (x, y). |
| `AddTextBlock(IEnumerable<string> lines, double x, double y, PdfTextOptions? options)` | Multi-line text with auto line advance. |
| `BeginTextBlock()` | Starts a custom text block for fine-grained control. |
| `EndTextBlock()` | Ends a custom text block. |
| `SetFont(StandardFont font, double size)` | Sets standard font inside a text block. |
| `SetFont(PdfEmbeddedFont font, double size)` | Sets embedded font inside a text block. |
| `SetColor(double r, double g, double b)` | Sets RGB text colour inside a text block. |
| `SetColor(PdfColor color)` | Sets text colour using any colour space. |
| `SetTextRenderingMode(PdfTextRenderingMode mode)` | Sets rendering mode (fill, stroke, clip, invisible). |
| `MoveTextTo(double x, double y)` | Moves text cursor inside a text block. |
| `ShowText(string text)` | Renders text at current position inside a text block. |

### Vector Graphics Methods

| Method | Description |
|--------|-------------|
| `DrawLine(double x1, double y1, double x2, double y2, PdfDrawOptions?)` | Draws a line between two points. |
| `DrawRectangle(double x, double y, double w, double h, PdfDrawOptions?)` | Draws a rectangle outline. |
| `FillRectangle(double x, double y, double w, double h, PdfDrawOptions?)` | Draws a filled rectangle. |
| `DrawAndFillRectangle(double x, double y, double w, double h, PdfDrawOptions?)` | Rectangle with fill and stroke. |
| `DrawCircle(double cx, double cy, double r, PdfDrawOptions?)` | Draws a circle outline. |
| `FillCircle(double cx, double cy, double r, PdfDrawOptions?)` | Draws a filled circle. |
| `DrawEllipse(double cx, double cy, double rx, double ry, PdfDrawOptions?)` | Draws an ellipse outline. |
| `FillEllipse(double cx, double cy, double rx, double ry, PdfDrawOptions?)` | Draws a filled ellipse. |
| `DrawPolygon((double X, double Y)[] points, PdfDrawOptions?)` | Draws a polygon outline. |
| `FillPolygon((double X, double Y)[] points, PdfDrawOptions?)` | Draws a filled polygon. |
| `BeginPath(PdfDrawOptions?)` | Starts low-level path construction; returns `PdfPathBuilder`. |

### Image Methods

| Method | Description |
|--------|-------------|
| `AddImage(PdfImage image, double x, double y, double w, double h)` | Places an image at exact size. |
| `AddImageScaled(PdfImage image, double x, double y, double maxW, double maxH)` | Places an image preserving aspect ratio. |

### Transform Methods

| Method | Description |
|--------|-------------|
| `ConcatMatrix(double a, double b, double c, double d, double e, double f)` | Concatenates a 6-element transformation matrix. |
| `Translate(double tx, double ty)` | Translation transform. |
| `Scale(double sx, double sy)` | Scale transform. |
| `Rotate(double angleDegrees)` | Counter-clockwise rotation. |
| `Skew(double angleXDegrees, double angleYDegrees)` | Skew (shear) transform. |
| `SaveGraphicsState()` | Saves graphics state (q operator). |
| `RestoreGraphicsState()` | Restores graphics state (Q operator). |

### Transparency

| Method | Description |
|--------|-------------|
| `SetAlpha(double strokeAlpha = 1.0, double fillAlpha = 1.0)` | Sets opacity (0.0–1.0). |

### Clipping

| Method | Description |
|--------|-------------|
| `ClipToRectangle(double x, double y, double w, double h)` | Rectangular clip region. |
| `ClipToCircle(double cx, double cy, double radius)` | Circular clip region. |
| `ClipToEllipse(double cx, double cy, double rx, double ry)` | Elliptical clip region. |

### Annotation Methods

| Method | Description |
|--------|-------------|
| `AddLink(string uri, double x, double y, double w, double h)` | Adds a URI hyperlink. |
| `AddTextAnnotation(double x, double y, string contents, ...)` | Adds a sticky note. |
| `AddFreeTextAnnotation(double x, double y, double w, double h, string contents, ...)` | Adds inline text annotation. |
| `AddHighlightAnnotation(double x, double y, double w, double h, ...)` | Highlight markup. |
| `AddUnderlineAnnotation(double x, double y, double w, double h, ...)` | Underline markup. |
| `AddSquigglyAnnotation(double x, double y, double w, double h, ...)` | Squiggly underline markup. |
| `AddStrikeOutAnnotation(double x, double y, double w, double h, ...)` | Strikethrough markup. |
| `AddStampAnnotation(double x, double y, double w, double h, PdfStampIcon icon, ...)` | Stamp annotation. |
| `AddAnnotation(PdfAnnotation annotation)` | Adds a generic annotation. |

### Form Field Methods

| Method | Description |
|--------|-------------|
| `AddTextField(string name, double x, double y, double w, double h, ...)` | Text input field. |
| `AddCheckboxField(string name, double x, double y, double size, ...)` | Checkbox field. |
| `AddDropdownField(string name, double x, double y, double w, double h, string[] options, ...)` | Dropdown list. |
| `AddListBoxField(string name, double x, double y, double w, double h, string[] options, ...)` | List box. |
| `AddPushButtonField(string name, double x, double y, double w, double h, ...)` | Push button. |
| `AddSignatureField(string name, double x, double y, double w, double h, ...)` | Signature placeholder. |
| `AddAcroField(PdfAcroField field)` | Adds a generic form field. |
| `AddTaggedAcroField(PdfStructureElement formElement, PdfAcroField field)` | Adds an accessible tagged form field. |

### Form XObject Methods

| Method | Description |
|--------|-------------|
| `AddFormXObject(PdfFormXObject form, double x, double y)` | Places a form XObject at natural size. |
| `AddFormXObject(PdfFormXObject form, double x, double y, double w, double h)` | Places a form XObject scaled. |

### Optional Content (Layers)

| Method | Description |
|--------|-------------|
| `BeginOptionalContent(PdfOptionalContentGroup ocg)` | Starts layer content. |
| `EndOptionalContent()` | Ends layer content. |

### Pattern / Shading Methods

| Method | Description |
|--------|-------------|
| `FillWithShading(PdfShadingPattern shading, double x, double y, double w, double h)` | Fills area with gradient (clips + sh). |
| `PaintShading(PdfShadingPattern shading)` | Paints gradient into current clip. |
| `FillWithPattern(PdfTilingPattern pattern, double x, double y, double w, double h)` | Fills area with tiling pattern. |
| `FillRectangleWithShading(PdfShadingPattern shading, double x, double y, double w, double h)` | Fills rectangle via pattern colour space. |

### Associated Files

| Method | Description |
|--------|-------------|
| `AddAssociatedFile(PdfAssociatedFile file)` | Attaches a page-level file. |
| `AddAssociatedFile(string fileName, string mimeType, byte[] data, ...)` | Attaches a file with properties. |

### Tagged Content (Accessibility)

| Method | Description |
|--------|-------------|
| `AddTaggedText(PdfStructureElement se, string text, double x, double y, PdfTextOptions?)` | Tagged text linked to structure tree. |
| `AddTaggedTextBlock(PdfStructureElement se, IEnumerable<string> lines, double x, double y, PdfTextOptions?)` | Tagged multi-line text. |
| `AddTaggedLink(PdfStructureElement linkElement, string uri, string text, double x, double y, double w, double h, PdfTextOptions?)` | Accessible tagged hyperlink. |
| `BeginTaggedContent(PdfStructureElement se)` | Starts tagged content region (returns MCID). |
| `EndTaggedContent()` | Ends tagged content region. |
| `BeginArtifact(string? artifactType)` | Starts artifact (non-structural) content. |
| `BeginArtifact(PdfArtifactType type)` | Starts strongly-typed artifact content. |
| `EndArtifact()` | Ends artifact content. |
| `AddArtifactText(string text, double x, double y, PdfArtifactType? type, PdfTextOptions?)` | Adds text marked as artifact. |

---

## PdfTextOptions

**Namespace:** `ObviousPDF`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `Font` | `StandardFont` | `Helvetica` | Standard font to use. |
| `EmbeddedFont` | `PdfEmbeddedFont?` | `null` | Embedded font (overrides `Font`). |
| `FontSize` | `double` | `12` | Size in points. |
| `Color` | `PdfColor` | Black RGB | Text colour. |
| `Leading` | `double?` | `null` (1.2× size) | Line spacing in points. |
| `RenderingMode` | `PdfTextRenderingMode` | `Fill` | Rendering mode. |
| `Alignment` | `PdfTextAlignment` | `Left` | Horizontal text alignment (requires `Width`). |
| `Width` | `double?` | `null` | Width of the text layout box in points. When set, `Alignment` controls positioning. |
| `Decoration` | `PdfTextDecoration` | `None` | Text decoration lines (underline, strikethrough, overline). Flags enum — combine with `\|`. |
| `DecorationColor` | `PdfColor?` | `null` | Colour of decoration lines. When `null`, uses text `Color`. |
| `DecorationThickness` | `double?` | `null` | Thickness of decoration lines in points. When `null`, defaults to ~1/18 of font size. |
| `Superscript` | `bool` | `false` | Renders text as superscript (raised baseline, ~58% font size). Uses PDF text rise operator (Ts). |
| `Subscript` | `bool` | `false` | Renders text as subscript (lowered baseline, ~58% font size). Uses PDF text rise operator (Ts). |
| `OutlineColor` | `PdfColor?` | `null` | Text outline stroke colour. When set, switches to FillAndStroke rendering mode. |
| `OutlineWidth` | `double?` | `null` | Text outline stroke width in points. Defaults to 0.5 if not specified. |
| `BackgroundColor` | `PdfColor?` | `null` | Background colour drawn behind the text (highlight effect). |
| `ShadowColor` | `PdfColor?` | `null` | Drop shadow colour. When set, a shadow copy is drawn at the specified offset. |
| `ShadowOffsetX` | `double` | `1.0` | Horizontal shadow offset in points (positive = right). |
| `ShadowOffsetY` | `double` | `-1.0` | Vertical shadow offset in points (negative = down in PDF coordinates). |
| `Rotation` | `double` | `0` | Rotation angle in degrees, counter-clockwise. Rotates the text and all associated rendering (background, shadow, decoration lines) around the text anchor point. Applies to `AddText`, `AddTextBlock`, `AddTaggedText`, and `AddTaggedTextBlock`. |

### Text Rotation Examples

```csharp
// 45-degree diagonal text
page.AddText("Diagonal", 200, 400, new PdfTextOptions { Rotation = 45 });

// Vertical text — reads bottom to top
page.AddText("Vertical", 400, 300, new PdfTextOptions { Rotation = 90, FontSize = 14 });

// Vertical text — reads top to bottom
page.AddText("Downward", 450, 600, new PdfTextOptions { Rotation = -90, FontSize = 14 });

// White text on a black background
page.AddText("White on Black", 72, 350, new PdfTextOptions
{
    Color = PdfColor.FromRgb(1, 1, 1),
    BackgroundColor = PdfColor.Black,
    FontSize = 16
});

// Rotation with coloured background — background rotates with the text
page.AddText("Rotated highlight", 200, 500, new PdfTextOptions
{
    Rotation = 30,
    BackgroundColor = PdfColor.FromRgb(1.0, 1.0, 0.0),
    FontSize = 14
});

// Multi-line rotated block
page.AddTextBlock(new[] { "Line 1", "Line 2", "Line 3" }, 300, 500,
    new PdfTextOptions { Rotation = 20, FontSize = 12 });

// Tagged (accessible) rotated text
var para = root.AddChild(StructureType.P);
page.AddTaggedText(para, "Rotated accessible text", 200, 400,
    new PdfTextOptions { Rotation = 45, FontSize = 14 });
```

> **Implementation note:** `Rotation` uses a PDF CTM (`cm` operator) wrapped in a
> `q`/`Q` graphics-state save/restore. This means background rectangles, drop shadows,
> and decoration lines all rotate together with the text. For rotating arbitrary
> graphics (shapes, images), use `SaveGraphicsState()` / `Rotate()` /
> `RestoreGraphicsState()` on `PdfPageBuilder` instead.

---

## PdfDrawOptions

**Namespace:** `ObviousPDF`

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `StrokeColor` | `PdfColor` | Black | Stroke (outline) colour. |
| `FillColor` | `PdfColor` | Black | Fill colour. |
| `LineWidth` | `double` | `1.0` | Line width in points. |
| `LineCap` | `PdfLineCap` | `Butt` | Line cap style. |
| `LineJoin` | `PdfLineJoin` | `Miter` | Line join style. |
| `MiterLimit` | `double` | `10.0` | Miter limit for joins. |
| `DashPattern` | `double[]?` | `null` | Dash pattern array. |
| `DashPhase` | `double` | `0` | Dash pattern phase offset. |
| `StrokeAlpha` | `double` | `1.0` | Stroke opacity (0.0–1.0). |
| `FillAlpha` | `double` | `1.0` | Fill opacity (0.0–1.0). |

---

## PdfColor

**Namespace:** `ObviousPDF`

Represents a colour in DeviceGray, DeviceRGB, or DeviceCMYK colour space.

### Constructors / Factory Methods

```csharp
// RGB (each 0.0–1.0)
var red = new PdfColor(1.0, 0.0, 0.0);

// Grayscale (0.0 = black, 1.0 = white)
var gray = PdfColor.Gray(0.5);

// CMYK (each 0.0–1.0)
var cmykBlue = PdfColor.Cmyk(1.0, 0.5, 0.0, 0.0);

// Implicit from tuple
PdfColor c = (0.2, 0.4, 0.8);
```

### Properties

| Property | Type | Description |
|----------|------|-------------|
| `ColorSpace` | `PdfColorSpace` | The colour space (DeviceGray, DeviceRGB, DeviceCMYK). |
| `R`, `G`, `B` | `double` | RGB components (0.0–1.0). |
| `Gray` | `double` | Grayscale component (0.0–1.0). |
| `C`, `M`, `Y`, `K` | `double` | CMYK components (0.0–1.0). |

---

## PdfImage

**Namespace:** `ObviousPDF`

### Factory Methods

| Method | Description |
|--------|-------------|
| `PdfImage.FromFile(string path)` | Loads a JPEG or PNG image from file. |
| `PdfImage.FromStream(Stream stream)` | Loads a JPEG or PNG image from stream. |
| `PdfImage.FromBytes(byte[] data)` | Loads a JPEG or PNG image from byte array. |

### Properties

| Property | Type | Description |
|----------|------|-------------|
| `WidthPx` | `int` | Image width in pixels. |
| `HeightPx` | `int` | Image height in pixels. |

---

## PdfEmbeddedFont

**Namespace:** `ObviousPDF`

### Factory Methods

| Method | Description |
|--------|-------------|
| `PdfEmbeddedFont.FromFile(string path)` | Loads a TrueType/OpenType font from file. |
| `PdfEmbeddedFont.FromStream(Stream stream)` | Loads from stream. |
| `PdfEmbeddedFont.FromBytes(byte[] data)` | Loads from byte array. |

---

## StandardFont

**Namespace:** `ObviousPDF.Fonts`

The 14 standard PDF fonts. When used, ObviousPDF automatically embeds SIL OFL substitute fonts.

| Value | PDF Name |
|-------|----------|
| `Helvetica` | Helvetica (→ Sora Regular) |
| `HelveticaBold` | Helvetica-Bold (→ Sora Bold) |
| `HelveticaOblique` | Helvetica-Oblique (→ Sora Italic) |
| `HelveticaBoldOblique` | Helvetica-BoldOblique (→ Sora BoldItalic) |
| `TimesRoman` | Times-Roman (→ CMU Serif Roman) |
| `TimesBold` | Times-Bold (→ CMU Serif Bold) |
| `TimesItalic` | Times-Italic (→ CMU Serif Italic) |
| `TimesBoldItalic` | Times-BoldItalic (→ CMU Serif BoldItalic) |
| `Courier` | Courier (→ CMU Typewriter Regular) |
| `CourierBold` | Courier-Bold (→ CMU Typewriter Bold) |
| `CourierOblique` | Courier-Oblique (→ CMU Typewriter Italic) |
| `CourierBoldOblique` | Courier-BoldOblique (→ CMU Typewriter BoldItalic) |
| `Symbol` | Symbol |
| `ZapfDingbats` | ZapfDingbats |

---

## PageSize

**Namespace:** `ObviousPDF`

Predefined page size tuples (width, height in points).

| Constant | Dimensions | Size |
|----------|------------|------|
| `PageSize.Letter` | 612 × 792 | 8.5 × 11 in |
| `PageSize.Legal` | 612 × 1008 | 8.5 × 14 in |
| `PageSize.A4` | 595.28 × 841.89 | 210 × 297 mm |
| `PageSize.A3` | 841.89 × 1190.55 | 297 × 420 mm |
| `PageSize.A5` | 419.53 × 595.28 | 148 × 210 mm |
| `PageSize.Tabloid` | 792 × 1224 | 11 × 17 in |

---

## PdfDocumentInfo

**Namespace:** `ObviousPDF`

| Property | Type | Description |
|----------|------|-------------|
| `Title` | `string?` | Document title. |
| `Author` | `string?` | Author name. |
| `Subject` | `string?` | Document subject. |
| `Keywords` | `string?` | Comma-separated keywords. |
| `Creator` | `string?` | Application that created the content. |
| `Producer` | `string` | (auto-set) Library that produced the PDF. |

---

## PdfFormXObject

**Namespace:** `ObviousPDF`

Reusable content stream for headers, footers, watermarks. Created via `PdfDocument.CreateFormXObject()`.

| Property | Type | Description |
|----------|------|-------------|
| `Width` | `double` | Bounding box width. |
| `Height` | `double` | Bounding box height. |

| Method | Description |
|--------|-------------|
| `AddText(string text, double x, double y, PdfTextOptions?)` | Adds text to the form. |
| `DrawLine(...)` | Draws a line in the form. |
| `DrawRectangle(...)` | Draws a rectangle in the form. |
| `FillRectangle(...)` | Fills a rectangle in the form. |

---

## PdfPathBuilder

**Namespace:** `ObviousPDF`

Low-level path construction. Obtained via `PdfPageBuilder.BeginPath()`.

| Method | Returns | Description |
|--------|---------|-------------|
| `MoveTo(double x, double y)` | `PdfPathBuilder` | Moves to a point. |
| `LineTo(double x, double y)` | `PdfPathBuilder` | Line to a point. |
| `CurveTo(double x1, double y1, double x2, double y2, double x3, double y3)` | `PdfPathBuilder` | Cubic Bézier curve. |
| `ClosePath()` | `PdfPathBuilder` | Closes the current subpath. |
| `Stroke()` | `PdfPageBuilder` | Strokes the path. |
| `Fill()` | `PdfPageBuilder` | Fills the path. |
| `FillAndStroke()` | `PdfPageBuilder` | Fills and strokes. |

---

## Accessibility

### PdfStructureElement

**Namespace:** `ObviousPDF.Accessibility`

A node in the PDF structure tree (ISO 32000 §14.7.2).

#### Properties

| Property | Type | Description |
|----------|------|-------------|
| `Type` | `StructureType` | The structure type (P, H1, Table, etc.). |
| `CustomType` | `string?` | Custom type name (for role-mapped types). |
| `AltText` | `string?` | Alternative text (required for figures). |
| `ActualText` | `string?` | Actual replacement text. |
| `Language` | `string?` | BCP 47 language override. |
| `Title` | `string?` | Element title. |
| `Phoneme` | `string?` | Phonemic transcription (PDF 2.0). |
| `PhoneticAlphabet` | `PdfPhoneticAlphabet?` | Phonetic alphabet (IPA or X-SAMPA). |
| `Scope` | `PdfTableScope?` | Table header scope (Row/Column/Both). |
| `Short` | `string?` | Abbreviated header text. |
| `Expansion` | `string?` | Expanded abbreviation text. |
| `NoteType` | `PdfNoteType?` | Note type (Footnote/Endnote/Rearnote). |
| `OptionalContentGroup` | `PdfOptionalContentGroup?` | Associated OCG. |
| `AssociatedFile` | `PdfAssociatedFile?` | Associated file (PDF/UA-2). |
| `DestinationPageIndex` | `int?` | TOC item destination page. |
| `BBox` | `double[]?` | Bounding box [llx, lly, urx, ury]. |
| `Id` | `string?` | Unique ID for header associations. |
| `Headers` | `IReadOnlyList<string>` | Header IDs for data cells. |
| `Children` | `IReadOnlyList<PdfStructureElement>` | Child elements in reading order. |
| `Parent` | `PdfStructureElement?` | Parent element. |
| `ReadingOrderIndex` | `int` | Position in parent's children list. |

#### Methods

| Method | Returns | Description |
|--------|---------|-------------|
| `AddChild(StructureType type)` | `PdfStructureElement` | Adds child at end. |
| `AddChild(StructureType type, string? altText)` | `PdfStructureElement` | Adds child with alt text. |
| `AddCustomChild(string name, StructureType mappedType)` | `PdfStructureElement` | Adds custom-typed child. |
| `AddCaption()` | `PdfStructureElement` | Adds Caption child (Table/Figure only). |
| `AddNote(PdfNoteType? noteType)` | `PdfStructureElement` | Adds Note child. |
| `AddRuby(bool includeParentheses)` | `(Ruby, RB, RT)` | Adds Ruby annotation group. |
| `AddWarichu(bool includeParentheses)` | `(Warichu, WT)` | Adds Warichu commentary group. |
| `AddTableOfContents()` | `PdfStructureElement` | Adds TOC child. |
| `AddTableOfContentsItem(string? title, int? page)` | `PdfStructureElement` | Adds TOCI to TOC. |
| `InsertChild(int index, StructureType type)` | `PdfStructureElement` | Inserts at position. |
| `MoveChild(PdfStructureElement child, int newIndex)` | `void` | Moves child to new position. |
| `RemoveChild(PdfStructureElement child)` | `bool` | Removes a child. |
| `ReorderChildren(IEnumerable<PdfStructureElement>)` | `void` | Reorders all children. |
| `AddTableHead()` | `PdfStructureElement` | Adds THead child. |
| `AddTableBody()` | `PdfStructureElement` | Adds TBody child. |
| `AddTableFoot()` | `PdfStructureElement` | Adds TFoot child. |
| `AddTableRow()` | `PdfStructureElement` | Adds TR child. |
| `AddHeaderCell(PdfTableScope? scope, string? id, string? shortText)` | `PdfStructureElement` | Adds TH child. |
| `AddDataCell(params string[] headerIds)` | `PdfStructureElement` | Adds TD child. |
| `AddHeader(string headerId)` | `PdfStructureElement` | Adds header ID to TD. |
| `CountContentItems()` | `int` | Counts leaf content items in subtree. |
| `EnumerateContentInReadingOrder()` | `IEnumerable<PdfStructureElement>` | Iterates leaf items in order. |

### StructureType

| Value | Description | PDF Tag |
|-------|-------------|---------|
| `Document` | Root document | `/Document` |
| `Part` | Generic group | `/Part` |
| `Art` | Article | `/Art` |
| `Sect` | Section | `/Sect` |
| `Div` | Generic container | `/Div` |
| `P` | Paragraph | `/P` |
| `H1`–`H6` | Headings level 1–6 | `/H1`–`/H6` |
| `H` | Generic heading | `/H` |
| `BlockQuote` | Block quotation | `/BlockQuote` |
| `Caption` | Table/figure caption | `/Caption` |
| `Span` | Inline span | `/Span` |
| `Quote` | Inline quotation | `/Quote` |
| `Link` | Hyperlink | `/Link` |
| `Note` | Footnote/endnote | `/Note` |
| `Reference` | Cross-reference | `/Reference` |
| `Code` | Code fragment | `/Code` |
| `L` | List | `/L` |
| `LI` | List item | `/LI` |
| `Lbl` | List item label | `/Lbl` |
| `LBody` | List item body | `/LBody` |
| `Table` | Table | `/Table` |
| `TR` | Table row | `/TR` |
| `TH` | Header cell | `/TH` |
| `TD` | Data cell | `/TD` |
| `THead` | Header row group | `/THead` |
| `TBody` | Body row group | `/TBody` |
| `TFoot` | Footer row group | `/TFoot` |
| `Figure` | Figure/image | `/Figure` |
| `Formula` | Math formula | `/Formula` |
| `Form` | Form widget | `/Form` |
| `Ruby` | Ruby annotation | `/Ruby` |
| `RB` | Ruby base text | `/RB` |
| `RT` | Ruby annotation text | `/RT` |
| `RP` | Ruby punctuation | `/RP` |
| `Warichu` | Warichu annotation | `/Warichu` |
| `WT` | Warichu body text | `/WT` |
| `WP` | Warichu punctuation | `/WP` |
| `TOC` | Table of contents | `/TOC` |
| `TOCI` | TOC item | `/TOCI` |

### PdfAccessibilityChecker

```csharp
var checker = new PdfAccessibilityChecker();
PdfAccessibilityReport report = checker.Check(document);
```

Runs 43+ checks covering PDF/UA-1, PDF/UA-2, WCAG 2.2, and ISO 32000.

### PdfAccessibilityReport

| Member | Description |
|--------|-------------|
| `IsFullyCompliant` | `true` if no non-compliant items. |
| `CompliantItems` | List of passing checks. |
| `NonCompliantItems` | List of failing checks with remediation. |
| `ToString()` | Human-readable report text. |

### PdfArtifactType

| Value | Description |
|-------|-------------|
| `Pagination` | Headers, footers, page numbers. |
| `Layout` | Decorative lines, backgrounds. |
| `Page` | Cut marks, colour bars. |
| `Background` | Background images. |

### PdfTableScope

| Value | Description |
|-------|-------------|
| `Row` | Header applies to rest of row. |
| `Column` | Header applies to rest of column. |
| `Both` | Header applies to row and column. |

### PdfColorContrast

Static utility for WCAG contrast checking.

| Method | Description |
|--------|-------------|
| `CalculateRatio(PdfColor foreground, PdfColor background)` | Returns contrast ratio (1.0–21.0). |
| `MeetsAA(double ratio, bool largeText)` | Whether ratio meets WCAG AA. |
| `MeetsAAA(double ratio, bool largeText)` | Whether ratio meets WCAG AAA. |

---

## Annotations

### PdfAnnotationType

`Text`, `FreeText`, `Highlight`, `Underline`, `Squiggly`, `StrikeOut`, `Stamp`

### PdfStampIcon

`Approved`, `Experimental`, `NotApproved`, `AsIs`, `Expired`, `NotForPublicRelease`, `Confidential`, `Final`, `Sold`, `Departmental`, `ForComment`, `TopSecret`, `Draft`, `ForPublicRelease`

---

## Security

### PdfDigitalSignature

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `Certificate` | `X509Certificate2` | (required) | Signing certificate with private key. |
| `Reason` | `string?` | `null` | Reason for signing. |
| `Location` | `string?` | `null` | Signing location. |
| `ContactInfo` | `string?` | `null` | Signer contact info. |
| `SignerName` | `string?` | `null` | Signer name (defaults to cert CN). |
| `PageIndex` | `int` | `0` | Page for signature field. |
| `X`, `Y` | `double` | `0` | Signature field position. |
| `Width` | `double` | `200` | Signature field width. |
| `Height` | `double` | `50` | Signature field height. |
| `FieldName` | `string` | `"Signature1"` | Signature field name. |

### PdfEncryption

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `UserPassword` | `string` | `""` | Open password (empty = no password to open). |
| `OwnerPassword` | `string` | `""` | Full-access password. |
| `Algorithm` | `PdfEncryptionAlgorithm` | `Aes128` | `Aes128` or `Aes256`. |
| `AllowPrinting` | `bool` | `true` | Allow printing. |
| `AllowCopying` | `bool` | `true` | Allow copy/paste. |
| `AllowModifying` | `bool` | `true` | Allow editing. |
| `AllowAnnotating` | `bool` | `true` | Allow annotations. |

---

## Conformance

### PdfAConformanceLevel

`None`, `PdfA1B`, `PdfA2B`, `PdfA3B`

### PdfUaConformanceLevel

`None`, `PdfUA1`, `PdfUA2`

### PdfEncryptionAlgorithm

`Aes128`, `Aes256`

---

## Enumerations Reference

### PdfTextDecoration (Flags Enum)

**Namespace:** `ObviousPDF`

Specifies text decoration lines (ISO 32000 §9.3, §8.5). Multiple values can be combined with bitwise OR.

| Value | Int | Description |
|-------|-----|-------------|
| `None` | `0` | No decoration (default). |
| `Underline` | `1` | Line below the text baseline. |
| `Strikethrough` | `2` | Line through the middle of the text. |
| `Overline` | `4` | Line above the text at the ascender position. |

```csharp
// Combine multiple decorations
var opts = new PdfTextOptions
{
    Decoration = PdfTextDecoration.Underline | PdfTextDecoration.Strikethrough
};
```

### PdfTextAlignment

**Namespace:** `ObviousPDF`

Horizontal text alignment within a specified width (ISO 32000-2 §14.8.5.4).

| Value | Description |
|-------|-------------|
| `Left` | Left-aligned (default). |
| `Center` | Centred within the width. |
| `Right` | Right-aligned within the width. |
| `Justify` | Word spacing adjusted to fill the width. Last line is left-aligned in text blocks. |

### PdfTextRenderingMode

**Namespace:** `ObviousPDF`

Text rendering modes (ISO 32000 §9.3.6, Table 106).

| Value | Mode | Description |
|-------|------|-------------|
| `Fill` | 0 | Fill text (default). |
| `Stroke` | 1 | Stroke (outline only). |
| `FillAndStroke` | 2 | Fill and stroke. |
| `Invisible` | 3 | Invisible (for searchable text over images). |
| `FillAndClip` | 4 | Fill and add to clipping path. |
| `StrokeAndClip` | 5 | Stroke and add to clipping path. |
| `FillStrokeAndClip` | 6 | Fill, stroke, and clip. |
| `Clip` | 7 | Add to clipping path only. |

### PdfColorSpace

**Namespace:** `ObviousPDF`

PDF device colour spaces (ISO 32000 §8.6.4).

| Value | Description |
|-------|-------------|
| `DeviceGray` | Single gray component. Operators: `g` / `G`. |
| `DeviceRGB` | Red, green, blue components. Operators: `rg` / `RG`. |
| `DeviceCMYK` | Cyan, magenta, yellow, key (black) components. Operators: `k` / `K`. |

### PdfColor Named Constants

| Constant | Value |
|----------|-------|
| `PdfColor.Black` | RGB (0, 0, 0) |
| `PdfColor.White` | RGB (1, 1, 1) |
| `PdfColor.Red` | RGB (1, 0, 0) |
| `PdfColor.Green` | RGB (0, 1, 0) |
| `PdfColor.Blue` | RGB (0, 0, 1) |

### PdfLineCap

**Namespace:** `ObviousPDF`

Line cap styles (ISO 32000 §8.4.3.3).

| Value | Description |
|-------|-------------|
| `Butt` | Squared off at the endpoint (default). |
| `Round` | Semicircular arc at the endpoint. |
| `ProjectingSquare` | Stroke extends beyond endpoint by half the line width. |

### PdfLineJoin

**Namespace:** `ObviousPDF`

Line join styles (ISO 32000 §8.4.3.4).

| Value | Description |
|-------|-------------|
| `Miter` | Outer edges extended until they meet (default). |
| `Round` | Circular arc at the join point. |
| `Bevel` | Triangle fill between butt-capped ends. |

### PdfPageLabelStyle

**Namespace:** `ObviousPDF`

Page label numbering styles (ISO 32000 §12.4.2).

| Value | Description |
|-------|-------------|
| `Decimal` | Arabic numerals (1, 2, 3…). |
| `UpperRoman` | Uppercase Roman (I, II, III…). |
| `LowerRoman` | Lowercase Roman (i, ii, iii…). |
| `UpperAlpha` | Uppercase letters (A, B, C…). |
| `LowerAlpha` | Lowercase letters (a, b, c…). |
| `None` | No numbering; prefix only. |

### PdfLayoutTextAlign

**Namespace:** `ObviousPDF.Accessibility`

Accessibility-layer text alignment attribute (ISO 32000-2 §14.8.5.4, Table 379).

| Value | Description |
|-------|-------------|
| `Start` | Aligned to start edge (left in LTR). Default. |
| `Center` | Centred between start and end edges. |
| `End` | Aligned to end edge (right in LTR). |
| `Justify` | Spacing adjusted so lines fill full width. |

### PdfPlacement

**Namespace:** `ObviousPDF.Accessibility`

Structure element positioning (ISO 32000-2 §14.8.5.4, Table 379).

| Value | Description |
|-------|-------------|
| `Block` | Block-level element. |
| `Inline` | Inline-level element. |
| `Before` | Placed before enclosing reference area. |
| `Start` | Placed at start edge of reference area. |
| `End` | Placed at end edge of reference area. |

### PdfPhoneticAlphabet

**Namespace:** `ObviousPDF.Accessibility`

Phonetic notation systems for pronunciation hints (PDF 2.0, ISO 32000-2 §14.9.6).

| Value | Description |
|-------|-------------|
| `Ipa` | International Phonetic Alphabet. |
| `XSampa` | Extended Speech Assessment Methods Phonetic Alphabet (ASCII-compatible). |

### PdfNoteType

**Namespace:** `ObviousPDF.Accessibility`

Note structure element types (ISO 32000-2 §14.8.4.4.2).

| Value | Description |
|-------|-------------|
| `Footnote` | Note at the bottom of the page. |
| `Endnote` | Note at the end of a section or document. |
| `Rearnote` | Note at the back of a publication. |

### BundledFonts

**Namespace:** `ObviousPDF.Fonts`

Open-source fonts (SIL OFL) bundled as embedded resources. Drop-in substitutes for the standard 14 fonts.

| Property | Family | Style | Substitutes |
|----------|--------|-------|-------------|
| `SerifRegular` | CMU Serif | Regular | Times-Roman |
| `SerifBold` | CMU Serif | Bold | Times-Bold |
| `SerifItalic` | CMU Serif | Italic | Times-Italic |
| `SerifBoldItalic` | CMU Serif | Bold Italic | Times-BoldItalic |
| `SansRegular` | Sora | Regular | Helvetica |
| `SansBold` | Sora | Bold | Helvetica-Bold |
| `SansItalic` | Sora | Italic | Helvetica-Oblique |
| `SansBoldItalic` | Sora | Bold Italic | Helvetica-BoldOblique |
| `MonoRegular` | CMU Typewriter | Regular | Courier |
| `MonoBold` | CMU Typewriter | Bold | Courier-Bold |
| `MonoItalic` | CMU Typewriter | Italic | Courier-Oblique |
| `MonoBoldItalic` | CMU Typewriter | Bold Italic | Courier-BoldOblique |

| Method | Description |
|--------|-------------|
| `GetSubstitute(StandardFont font)` | Returns the bundled substitute for a standard font (or `null` for Symbol/ZapfDingbats). |
| `HasSubstitute(StandardFont font)` | Returns `true` if a substitute exists. |

---

## Coordinate System

- **Origin (0, 0)** is at the **bottom-left** corner
- **X** increases to the right
- **Y** increases upward
- **Units** are **points** (1 point = 1/72 inch)

For US Letter (612 × 792):
- Top-left: `(0, 792)` · Bottom-right: `(612, 0)` · 1-inch margins: `(72, 720)`
