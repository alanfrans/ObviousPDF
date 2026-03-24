# ObviousPDF — Comprehensive Guide for Coding LLMs

> This document provides everything a coding LLM needs to generate correct, accessible
> PDF documents using the ObviousPDF library. It covers the complete API surface, patterns,
> accessibility requirements, and common pitfalls.

---

## Overview

**ObviousPDF** enables .NET developers to create standards-compliant, more accessible PDFs. It is a zero-dependency .NET 8 library built from ISO 32000.

- **Namespace:** `ObviousPDF` (main), `ObviousPDF.Accessibility` (tagged PDF), `ObviousPDF.Fonts` (fonts)
- **Entry point:** `new PdfDocument()`
- **Fluent API:** Most builder methods return `this` for chaining
- **Coordinate system:** Origin at bottom-left, Y up, units in points (1 pt = 1/72 inch)
- **Page sizes:** `PageSize.Letter` (612×792), `PageSize.A4` (595.28×841.89), etc.

---

## Quick Reference Patterns

### Minimal PDF

```csharp
using ObviousPDF;

var doc = new PdfDocument();
var page = doc.AddPage();
page.AddText("Hello, World!", 72, 720);
doc.Save("output.pdf");
```

### Minimal Accessible PDF (PDF/UA Compliant)

```csharp
using ObviousPDF;
using ObviousPDF.Accessibility;

var doc = new PdfDocument();
doc.Language = "en-US";
doc.Info.Title = "My Document";
doc.DisplayDocTitle = true;
doc.PdfUaConformance = PdfUaConformanceLevel.PdfUA1;

var root = doc.EnableTaggedPdf();
var h1 = root.AddChild(StructureType.H1);
var para = root.AddChild(StructureType.P);

var page = doc.AddPage();
page.AddTaggedText(h1, "Document Title", 72, 750,
    new PdfTextOptions { FontSize = 24 });
page.AddTaggedText(para, "Body text content.", 72, 710);

doc.Save("accessible.pdf");
```

---

## Core Concepts

### 1. PdfDocument — The Document Container

```csharp
var doc = new PdfDocument();

// Metadata (always set for accessibility)
doc.Info.Title = "Report";
doc.Info.Author = "Jane Smith";
doc.Info.Subject = "Q4 Analysis";
doc.Info.Keywords = "finance, quarterly";
doc.Language = "en-US";           // Required for PDF/UA
doc.DisplayDocTitle = true;        // Required for PDF/UA

// Conformance declarations
doc.PdfUaConformance = PdfUaConformanceLevel.PdfUA1;
doc.PdfAConformance = PdfAConformanceLevel.PdfA2B;

// PDF version control (auto-inferred when null)
doc.PdfVersion = "2.0";

// Advanced serialization options
doc.UseCrossReferenceStreams = true;
doc.UseObjectStreams = true;
doc.Linearize = true;  // Fast web view
```

### 2. PdfPageBuilder — Page Content

Pages are created via `doc.AddPage()` and content is added with the fluent builder.

```csharp
// Standard page sizes
var page = doc.AddPage();                    // US Letter (default)
var a4Page = doc.AddPage(PageSize.A4);       // A4
var custom = doc.AddPage(500, 800);          // Custom dimensions
```

### 3. Text

```csharp
// Simple text
page.AddText("Hello", 72, 720);

// With options
page.AddText("Bold red text", 72, 700, new PdfTextOptions
{
    Font = StandardFont.HelveticaBold,
    FontSize = 16,
    Color = new PdfColor(1, 0, 0)
});

// Multi-line text block
page.AddTextBlock(new[] { "Line 1", "Line 2", "Line 3" }, 72, 680,
    new PdfTextOptions { Leading = 16 });

// Fine-grained text block
page.BeginTextBlock()
    .SetFont(StandardFont.Helvetica, 24)
    .MoveTextTo(72, 600)
    .ShowText("Title")
    .SetFont(StandardFont.Helvetica, 12)
    .SetColor(0.5, 0.5, 0.5)
    .MoveTextTo(0, -20)
    .ShowText("Subtitle")
    .EndTextBlock();

// Embedded font (Unicode support)
var font = PdfEmbeddedFont.FromFile("path/to/font.ttf");
page.AddText("Hello 世界 مرحبا", 72, 500,
    new PdfTextOptions { EmbeddedFont = font, FontSize = 14 });

// Text alignment — requires Width to be set
page.AddText("Left-aligned", 72, 460, new PdfTextOptions
{
    FontSize = 14,
    Alignment = PdfTextAlignment.Left,
    Width = 468
});
page.AddText("Center-aligned", 72, 440, new PdfTextOptions
{
    FontSize = 14,
    Alignment = PdfTextAlignment.Center,
    Width = 468
});
page.AddText("Right-aligned", 72, 420, new PdfTextOptions
{
    FontSize = 14,
    Alignment = PdfTextAlignment.Right,
    Width = 468
});
page.AddText("Justified text spreads across the full width.", 72, 400, new PdfTextOptions
{
    FontSize = 14,
    Alignment = PdfTextAlignment.Justify,
    Width = 468
});

// Measure text width (useful for layout calculations)
double width = page.MeasureTextWidth("Hello", new PdfTextOptions { FontSize = 14 });
```

### 3b. Italic / Bold Fonts

ObviousPDF supports italic and bold text through font selection (not a style toggle).

```csharp
using ObviousPDF.Fonts;

// Standard 14 fonts — italic and bold variants
page.AddText("Italic serif", 72, 680, new PdfTextOptions
{
    Font = StandardFont.TimesItalic, FontSize = 14
});
page.AddText("Bold sans-serif", 72, 660, new PdfTextOptions
{
    Font = StandardFont.HelveticaBold, FontSize = 14
});
page.AddText("Bold italic serif", 72, 640, new PdfTextOptions
{
    Font = StandardFont.TimesBoldItalic, FontSize = 14
});
page.AddText("Oblique monospace", 72, 620, new PdfTextOptions
{
    Font = StandardFont.CourierOblique, FontSize = 14
});

// Bundled embedded fonts — italic and bold variants
page.AddText("Embedded italic serif", 72, 600, new PdfTextOptions
{
    EmbeddedFont = BundledFonts.SerifItalic, FontSize = 14
});
page.AddText("Embedded bold sans", 72, 580, new PdfTextOptions
{
    EmbeddedFont = BundledFonts.SansBold, FontSize = 14
});
page.AddText("Embedded bold italic mono", 72, 560, new PdfTextOptions
{
    EmbeddedFont = BundledFonts.MonoBoldItalic, FontSize = 14
});

// Auto-substitute: get embedded equivalent of a standard font
var font = BundledFonts.GetSubstitute(StandardFont.HelveticaOblique);
page.AddText("Substituted italic", 72, 540, new PdfTextOptions
{
    EmbeddedFont = font, FontSize = 14
});
```

### 3c. Text Decorations

```csharp
// Underline
page.AddText("Underlined text", 72, 500, new PdfTextOptions
{
    FontSize = 14,
    Decoration = PdfTextDecoration.Underline
});

// Strikethrough
page.AddText("Deleted text", 72, 480, new PdfTextOptions
{
    FontSize = 14,
    Decoration = PdfTextDecoration.Strikethrough
});

// Overline
page.AddText("Overlined text", 72, 460, new PdfTextOptions
{
    FontSize = 14,
    Decoration = PdfTextDecoration.Overline
});

// Combined decorations (flags enum — use bitwise OR)
page.AddText("Underline + Strikethrough", 72, 440, new PdfTextOptions
{
    FontSize = 14,
    Decoration = PdfTextDecoration.Underline | PdfTextDecoration.Strikethrough
});

// Custom decoration colour and thickness
page.AddText("Red thick underline", 72, 420, new PdfTextOptions
{
    FontSize = 14,
    Decoration = PdfTextDecoration.Underline,
    DecorationColor = PdfColor.Red,
    DecorationThickness = 2.0
});

// Superscript (E=mc²)
page.AddText("E=mc", 72, 400);
page.AddText("2", 108, 400, new PdfTextOptions
{
    FontSize = 14,
    Superscript = true
});

// Subscript (H₂O)
page.AddText("H", 72, 380);
page.AddText("2", 82, 380, new PdfTextOptions
{
    FontSize = 14,
    Subscript = true
});
page.AddText("O", 90, 380);

// Text outline
page.AddText("Outlined heading", 72, 350, new PdfTextOptions
{
    FontSize = 24,
    OutlineColor = PdfColor.Red,
    OutlineWidth = 1.0
});

// Text background (highlight)
page.AddText("Highlighted text", 72, 320, new PdfTextOptions
{
    FontSize = 14,
    BackgroundColor = PdfColor.FromRgb(1.0, 1.0, 0.0)  // Yellow
});

// Text shadow
page.AddText("Shadow text", 72, 290, new PdfTextOptions
{
    FontSize = 18,
    ShadowColor = PdfColor.FromGray(0.7),
    ShadowOffsetX = 2.0,
    ShadowOffsetY = -2.0
});

// All decorations combined
page.AddText("Fully decorated", 72, 260, new PdfTextOptions
{
    FontSize = 18,
    Decoration = PdfTextDecoration.Underline | PdfTextDecoration.Strikethrough,
    DecorationColor = PdfColor.Blue,
    DecorationThickness = 1.5,
    OutlineColor = PdfColor.Red,
    OutlineWidth = 0.5,
    BackgroundColor = PdfColor.FromRgb(1.0, 1.0, 0.8),
    ShadowColor = PdfColor.FromGray(0.5)
});

// Decorations with tagged text (accessible)
page.AddTaggedText(para, "Tagged underlined text", 72, 230, new PdfTextOptions
{
    FontSize = 14,
    Decoration = PdfTextDecoration.Underline
});
```

### 3d. Text Rotation

Set `PdfTextOptions.Rotation` to rotate text counter-clockwise around its anchor point.
The background, drop shadow, and decoration lines all rotate with the text.
Works with `AddText`, `AddTextBlock`, `AddTaggedText`, and `AddTaggedTextBlock`.

```csharp
// 45-degree diagonal text
page.AddText("Diagonal", 200, 400, new PdfTextOptions { Rotation = 45 });

// Vertical text — reads bottom to top
page.AddText("Vertical", 400, 300, new PdfTextOptions { Rotation = 90, FontSize = 14 });

// Vertical text — reads top to bottom
page.AddText("Downward", 450, 600, new PdfTextOptions { Rotation = -90, FontSize = 14 });

// White text on a black background (no rotation needed)
page.AddText("White on Black", 72, 350, new PdfTextOptions
{
    Color = PdfColor.FromRgb(1, 1, 1),
    BackgroundColor = PdfColor.Black,
    FontSize = 16
});

// Rotation combined with background — the background rectangle rotates too
page.AddText("Rotated highlight", 200, 500, new PdfTextOptions
{
    Rotation = 30,
    BackgroundColor = PdfColor.FromRgb(1.0, 1.0, 0.0),
    FontSize = 14
});

// White text on black + rotated
page.AddText("Rotated White on Black", 72, 250, new PdfTextOptions
{
    Color = PdfColor.FromRgb(1, 1, 1),
    BackgroundColor = PdfColor.Black,
    FontSize = 16,
    Rotation = 15
});

// Multi-line rotated block
page.AddTextBlock(new[] { "Line 1", "Line 2", "Line 3" }, 300, 500,
    new PdfTextOptions { Rotation = 20, FontSize = 12 });

// Tagged (accessible) rotated text
var para = root.AddChild(StructureType.P);
page.AddTaggedText(para, "Rotated accessible text", 200, 400,
    new PdfTextOptions { Rotation = 45, FontSize = 14 });
```

> **Note:** For rotating shapes or images use `SaveGraphicsState()` / `Rotate()` /
> `RestoreGraphicsState()`. `PdfTextOptions.Rotation` is the simpler, self-contained
> option specifically for text.

### 4. Vector Graphics

```csharp
// Lines
page.DrawLine(72, 700, 540, 700);
page.DrawLine(72, 700, 540, 700, new PdfDrawOptions
{
    StrokeColor = new PdfColor(1, 0, 0),
    LineWidth = 2.0,
    DashPattern = new[] { 6.0, 3.0 }
});

// Rectangles
page.DrawRectangle(100, 600, 200, 100);          // Stroke only
page.FillRectangle(100, 600, 200, 100,            // Fill only
    new PdfDrawOptions { FillColor = new PdfColor(0.2, 0.4, 0.8) });
page.DrawAndFillRectangle(100, 600, 200, 100,     // Both
    new PdfDrawOptions
    {
        FillColor = new PdfColor(0.9, 0.9, 1),
        StrokeColor = new PdfColor(0, 0, 0.5),
        LineWidth = 1.5
    });

// Circles and ellipses
page.DrawCircle(300, 400, 50);
page.FillCircle(300, 400, 50, new PdfDrawOptions { FillColor = new PdfColor(0, 0.7, 0) });
page.DrawEllipse(300, 300, 80, 40);

// Polygons
page.DrawPolygon(new[] { (100.0, 200.0), (150.0, 280.0), (200.0, 200.0) });
page.FillPolygon(new[] { (100.0, 200.0), (150.0, 280.0), (200.0, 200.0) },
    new PdfDrawOptions { FillColor = new PdfColor(1, 0.5, 0) });

// Custom paths with Bézier curves
page.BeginPath()
    .MoveTo(100, 500)
    .LineTo(200, 500)
    .CurveTo(250, 550, 250, 450, 200, 400)
    .LineTo(100, 400)
    .ClosePath()
    .FillAndStroke();
```

### 5. Images

```csharp
// Load images
var jpeg = PdfImage.FromFile("photo.jpg");
var png = PdfImage.FromFile("logo.png");

// Place at exact size
page.AddImage(jpeg, 72, 600, 200, 150);

// Scale to fit preserving aspect ratio
page.AddImageScaled(png, 72, 400, maxWidth: 300, maxHeight: 200);
```

### 6. Transforms, Clipping, Transparency

```csharp
// Text rotation — simplest approach, use PdfTextOptions.Rotation
page.AddText("Diagonal", 200, 400, new PdfTextOptions { Rotation = 45 });
page.AddText("Vertical", 400, 300, new PdfTextOptions { Rotation = 90 });

// Graphics rotation (around a point) — for shapes and images
page.SaveGraphicsState()
    .Translate(300, 500)
    .Rotate(45)
    .DrawRectangle(-50, -25, 100, 50)
    .RestoreGraphicsState();

// Scale
page.SaveGraphicsState()
    .Scale(2, 0.5)
    .DrawCircle(100, 100, 30)
    .RestoreGraphicsState();

// Clipping
page.ClipToCircle(250, 250, 60);
page.FillRectangle(200, 200, 100, 100,
    new PdfDrawOptions { FillColor = new PdfColor(1, 0, 0) });
page.RestoreGraphicsState();

// Transparency
page.SaveGraphicsState();
page.SetAlpha(strokeAlpha: 1.0, fillAlpha: 0.5);
page.FillRectangle(100, 100, 200, 200,
    new PdfDrawOptions { FillColor = new PdfColor(0, 0, 1) });
page.RestoreGraphicsState();
```

### 7. Annotations

```csharp
// Hyperlink
page.AddLink("https://example.com", 72, 700, 100, 14);

// Sticky note
page.AddTextAnnotation(400, 700, "Review this section",
    title: "Reviewer", color: new PdfColor(1, 1, 0));

// Free text
page.AddFreeTextAnnotation(72, 650, 200, 30, "Inline note");

// Markup annotations
page.AddHighlightAnnotation(72, 600, 200, 14, color: new PdfColor(1, 1, 0));
page.AddUnderlineAnnotation(72, 580, 200, 14);
page.AddStrikeOutAnnotation(72, 560, 200, 14, color: new PdfColor(1, 0, 0));

// Stamp
page.AddStampAnnotation(400, 600, 80, 40, PdfStampIcon.Approved);
```

### 8. Interactive Forms (AcroForm)

```csharp
page.AddTextField("name", 72, 700, 200, 20,
    defaultValue: "Enter name", tooltip: "Full name", required: true);

page.AddCheckboxField("agree", 72, 670, 14,
    isChecked: false, tooltip: "I agree to terms");

page.AddDropdownField("country", 72, 640, 200, 20,
    new[] { "USA", "UK", "Canada" }, selectedValue: "USA",
    tooltip: "Select country");

page.AddListBoxField("colors", 72, 580, 200, 60,
    new[] { "Red", "Green", "Blue" }, tooltip: "Select colours");

page.AddPushButtonField("submit", 72, 550, 100, 25, label: "Submit");

page.AddSignatureField("sig", 72, 510, 200, 40, tooltip: "Sign here");
```

### 9. Bookmarks / Outlines

```csharp
var ch1 = doc.AddOutline("Chapter 1", 0);    // Links to page 0
ch1.AddChild("Section 1.1", 0);
ch1.AddChild("Section 1.2", 1);

doc.AddOutline("Chapter 2", 2);
```

### 10. Page Labels

```csharp
doc.AddPageLabels(0, PdfPageLabelStyle.LowercaseRoman);    // i, ii, iii...
doc.AddPageLabels(3, PdfPageLabelStyle.Decimal);            // 1, 2, 3...
doc.AddPageLabels(3, PdfPageLabelStyle.Decimal, "A-", 1);  // A-1, A-2...
```

### 11. Form XObjects (Reusable Content)

```csharp
// Create once
var header = doc.CreateFormXObject(612, 40);
header.AddText("CONFIDENTIAL", 250, 10,
    new PdfTextOptions { FontSize = 14, Color = new PdfColor(0.7, 0, 0) });

// Use on every page
for (int i = 0; i < 10; i++)
{
    var p = doc.AddPage();
    p.AddFormXObject(header, 0, 752);
    p.AddText($"Page {i + 1} content", 72, 700);
}
```

### 12. Encryption

```csharp
doc.Encryption = new PdfEncryption
{
    UserPassword = "",           // Anyone can open
    OwnerPassword = "secret123", // Full access password
    Algorithm = PdfEncryptionAlgorithm.Aes256,
    AllowPrinting = true,
    AllowCopying = false,
    AllowModifying = false
};
```

### 13. Digital Signatures

```csharp
var cert = new X509Certificate2("certificate.pfx", "password");
var sig = new PdfDigitalSignature(cert)
{
    Reason = "Document approval",
    Location = "New York",
    ContactInfo = "signer@example.com"
};
doc.Sign("signed_output.pdf", sig);
```

### 14. Gradients and Patterns

```csharp
// Linear gradient
var gradient = new PdfShadingPattern(PdfShadingType.Axial,
    x0: 0, y0: 0, x1: 200, y1: 0,
    color0: new PdfColor(1, 0, 0), color1: new PdfColor(0, 0, 1));
page.FillWithShading(gradient, 72, 600, 200, 100);

// Radial gradient
var radial = new PdfShadingPattern(PdfShadingType.Radial,
    x0: 100, y0: 100, x1: 100, y1: 100,
    color0: new PdfColor(1, 1, 0), color1: new PdfColor(1, 0, 0));
radial.R0 = 0; radial.R1 = 80;
page.FillWithShading(radial, 50, 50, 100, 100);

// Tiling pattern (stripes)
var pattern = new PdfTilingPattern(10, 10);
pattern.Content.SetNonStrokingColorRgb(0.9, 0.9, 0.9);
pattern.Content.AppendRectangle(0, 0, 10, 5);
pattern.Content.Fill();
page.FillWithPattern(pattern, 72, 300, 200, 100);
```

### 15. Optional Content Groups (Layers)

```csharp
var watermark = doc.CreateOptionalContentGroup("Watermark", visible: true);
var annotations = doc.CreateOptionalContentGroup("Annotations", visible: false);

page.BeginOptionalContent(watermark);
page.AddText("DRAFT", 200, 400,
    new PdfTextOptions { FontSize = 72, Color = new PdfColor(0.9, 0.9, 0.9) });
page.EndOptionalContent();
```

### 16. Associated Files (PDF 2.0)

```csharp
doc.AddAssociatedFile("data.xml", "application/xml",
    System.Text.Encoding.UTF8.GetBytes("<data/>"),
    PdfAssociatedFileRelationship.Data, "Supplementary data");
```

### 17. Document Parts (Variable Data Printing)

```csharp
var dpartRoot = doc.CreateDocumentPartRoot();
var part1 = dpartRoot.AddChild(startPageIndex: 0, endPageIndex: 2);
var part2 = dpartRoot.AddChild(startPageIndex: 3, endPageIndex: 5);
```

---

## Accessibility — Complete Guide

### Required Steps for PDF/UA Compliance

Every accessible PDF must have ALL of the following:

1. **Document language:** `doc.Language = "en-US";`
2. **Title:** `doc.Info.Title = "Document Title";`
3. **Display title:** `doc.DisplayDocTitle = true;`
4. **Tagged PDF enabled:** `doc.EnableTaggedPdf();`
5. **All content tagged or artifact:** Use `AddTaggedText()` or `BeginArtifact()`
6. **Figures have alt text:** `root.AddChild(StructureType.Figure, "Description")`
7. **Figures have BBox:** `figureElement.BBox = new[] { llx, lly, urx, ury };`
8. **Correct heading hierarchy:** H1 → H2 → H3 (no skipping)
9. **Table headers with scope:** Use `AddHeaderCell(PdfTableScope.Column)`
10. **PDF/UA identifier:** `doc.PdfUaConformance = PdfUaConformanceLevel.PdfUA1;`

### Structure Tree Pattern

```csharp
var root = doc.EnableTaggedPdf();

// Sections with headings
var sect = root.AddChild(StructureType.Sect);
var h1 = sect.AddChild(StructureType.H1);
var p1 = sect.AddChild(StructureType.P);

// Nested sections
var sect2 = root.AddChild(StructureType.Sect);
var h2 = sect2.AddChild(StructureType.H2);

// Figure with alt text and bounding box
var fig = sect.AddChild(StructureType.Figure, "Photo of sunset over ocean");
fig.BBox = new[] { 72.0, 500.0, 300.0, 700.0 };

// Lists
var list = sect.AddChild(StructureType.L);
var li = list.AddChild(StructureType.LI);
var lbl = li.AddChild(StructureType.Lbl);     // "•" or "1."
var lbody = li.AddChild(StructureType.LBody);  // Item content
```

### Accessible Table Pattern

```csharp
var table = root.AddChild(StructureType.Table);
var caption = table.AddCaption();

// Header
var thead = table.AddTableHead();
var hrow = thead.AddTableRow();
var th1 = hrow.AddHeaderCell(PdfTableScope.Column, id: "name");
var th2 = hrow.AddHeaderCell(PdfTableScope.Column, id: "age");

// Body
var tbody = table.AddTableBody();
var row = tbody.AddTableRow();
var td1 = row.AddDataCell("name");
var td2 = row.AddDataCell("age");

// Render
page.AddTaggedText(caption, "Employee Data", 72, 750);
page.AddTaggedText(th1, "Name", 72, 720);
page.AddTaggedText(th2, "Age", 200, 720);
page.AddTaggedText(td1, "Alice", 72, 700);
page.AddTaggedText(td2, "30", 200, 700);
```

### Accessible Links

```csharp
var para = root.AddChild(StructureType.P);
var link = para.AddChild(StructureType.Link);

page.AddTaggedLink(link, "https://example.com", "Visit Example",
    72, 700, 80, 14,
    new PdfTextOptions { Color = new PdfColor(0, 0, 1) });
```

### Accessible Forms

```csharp
var formSE = root.AddChild(StructureType.Form);
var field = new PdfAcroField(PdfAcroFieldType.Text, "email", 72, 700, 200, 20)
{
    Tooltip = "Email address (required)",
    Required = true
};
page.AddTaggedAcroField(formSE, field);
```

### Artifacts (Non-Structural Content)

```csharp
// Page numbers, headers, footers, decorative elements
page.AddArtifactText("Page 1", 280, 30, PdfArtifactType.Pagination);

page.BeginArtifact(PdfArtifactType.Layout);
page.DrawLine(72, 750, 540, 750);  // Decorative rule
page.EndArtifact();

page.BeginArtifact(PdfArtifactType.Background);
page.FillRectangle(0, 0, 612, 792,
    new PdfDrawOptions { FillColor = new PdfColor(0.98, 0.98, 1) });
page.EndArtifact();
```

### Language of Parts

```csharp
// Override language for a specific element
var spanFr = para.AddChild(StructureType.Span);
spanFr.Language = "fr";
page.AddTaggedText(spanFr, "Bonjour le monde", 72, 600);
```

### Pronunciation Hints (PDF 2.0)

```csharp
var name = para.AddChild(StructureType.Span);
name.Phoneme = "ˈniːtʃə";
name.PhoneticAlphabet = PdfPhoneticAlphabet.Ipa;
page.AddTaggedText(name, "Nietzsche", 72, 580);
```

### Layout Attributes (Text Alignment in Structure Tree)

```csharp
// Set layout attributes on structure elements for accessibility
var para = root.AddChild(StructureType.P);
para.TextAlign = PdfLayoutTextAlign.Center;   // Start, Center, End, Justify
para.Placement = PdfPlacement.Block;          // Block, Inline, Before, Start, End
para.LayoutWidth = 468;
para.LayoutHeight = 50;

// Combine with visual alignment
page.AddTaggedText(para, "Centered paragraph", 72, 700,
    new PdfTextOptions
    {
        FontSize = 14,
        Alignment = PdfTextAlignment.Center,
        Width = 468
    });
```

### Abbreviation Expansion

```csharp
var abbr = para.AddChild(StructureType.Span);
abbr.Expansion = "Web Content Accessibility Guidelines";
page.AddTaggedText(abbr, "WCAG", 72, 560);
```

### Notes (Footnotes/Endnotes)

```csharp
var note = para.AddNote(PdfNoteType.Footnote);
page.AddTaggedText(note, "1. Source: ISO 14289-1:2014", 72, 100);
```

### Reading Order Correction

```csharp
// When visual layout doesn't match reading order:
root.ReorderChildren(new[] { heading, mainContent, sidebar, footer });
// Or move individual children:
root.MoveChild(sidebar, 2);  // Move to position 2
```

### Custom Structure Types (Role Mapping)

```csharp
doc.AddRoleMapping("Sidebar", StructureType.Div);
doc.AddRoleMapping("CodeBlock", StructureType.Code);

var sidebar = sect.AddCustomChild("Sidebar", StructureType.Div);
```

### Accessibility Validation

```csharp
var checker = new PdfAccessibilityChecker();
var report = checker.Check(doc);

if (!report.IsFullyCompliant)
{
    Console.WriteLine("Accessibility issues found:");
    Console.WriteLine(report.ToString());
}
```

---

## Colour Contrast Checking

```csharp
var fg = new PdfColor(0.2, 0.2, 0.2);  // Dark gray text
var bg = new PdfColor(1, 1, 1);         // White background

double ratio = PdfColorContrast.CalculateRatio(fg, bg);
bool passesAA = PdfColorContrast.MeetsAA(ratio, largeText: false);    // 4.5:1
bool passesAAA = PdfColorContrast.MeetsAAA(ratio, largeText: false);   // 7:1
```

---

## PDF/A Archival Conformance

```csharp
doc.PdfAConformance = PdfAConformanceLevel.PdfA2B;
// Automatically includes: XMP metadata, OutputIntent, sRGB ICC profile
// Caller responsibility: Use embedded fonts (not standard fonts for strict compliance)
```

---

## Serialization Options

```csharp
// Compact format (recommended for modern PDFs)
doc.UseCrossReferenceStreams = true;
doc.UseObjectStreams = true;

// Fast web view (incompatible with encryption/object streams)
doc.Linearize = true;

// Explicit PDF version
doc.PdfVersion = "2.0";  // Or null for auto-inference
```

---

## Common Pitfalls

1. **Forgetting `doc.Language`** — PDF/UA requires it
2. **Using `AddText()` in tagged documents** — Use `AddTaggedText()` instead; untagged content fails PDF/UA
3. **Missing `BBox` on figures** — Required by PDF/UA §7.3
4. **Skipping heading levels** — H1 → H3 without H2 fails WCAG
5. **Forms without tooltips** — `PdfAcroField.Tooltip` is required for PDF/UA
6. **Not marking decorative content as artifacts** — Page numbers, rules, backgrounds must use `BeginArtifact()`
7. **Calling `Save()` instead of `Sign()`** — Use `Sign()` when digital signatures are needed
8. **Encryption + Linearization** — Incompatible; linearization is silently skipped
9. **Object streams + traditional xref** — Object streams require cross-reference streams (auto-enabled)
10. **Coordinates from top-left** — PDF origin is bottom-left; Y increases upward

---

## Thread Safety

`PdfDocument` and `PdfPageBuilder` are **not thread-safe**. Create separate instances per thread.

---

## Output Targets

```csharp
// File
doc.Save("output.pdf");

// Stream
using var ms = new MemoryStream();
doc.Save(ms);
byte[] bytes = ms.ToArray();

// Signed to file
doc.Sign("signed.pdf", signature);

// Signed to stream
doc.Sign(stream, signature);
```

---

## Enumerations Quick Reference

### PdfTextDecoration (Flags Enum)

| Value | Int | Description |
|-------|-----|-------------|
| `None` | `0` | No decoration (default). |
| `Underline` | `1` | Line below the baseline. |
| `Strikethrough` | `2` | Line through the middle. |
| `Overline` | `4` | Line above at ascender. |

Combine with `|`: `PdfTextDecoration.Underline | PdfTextDecoration.Strikethrough`

### PdfTextAlignment

`Left` (default), `Center`, `Right`, `Justify`

> Requires `PdfTextOptions.Width` to be set. Without `Width`, alignment is ignored.

### PdfTextRenderingMode

`Fill` (0, default), `Stroke` (1), `FillAndStroke` (2), `Invisible` (3), `FillAndClip` (4), `StrokeAndClip` (5), `FillStrokeAndClip` (6), `Clip` (7)

### StandardFont

| Serif | Sans-Serif | Monospace | Special |
|-------|------------|-----------|----------|
| `TimesRoman` | `Helvetica` | `Courier` | `Symbol` |
| `TimesBold` | `HelveticaBold` | `CourierBold` | `ZapfDingbats` |
| `TimesItalic` | `HelveticaOblique` | `CourierOblique` | |
| `TimesBoldItalic` | `HelveticaBoldOblique` | `CourierBoldOblique` | |

### BundledFonts (Embedded Substitutes)

| Serif (CMU Serif) | Sans (Sora) | Mono (CMU Typewriter) |
|-------------------|-------------|------------------------|
| `SerifRegular` | `SansRegular` | `MonoRegular` |
| `SerifBold` | `SansBold` | `MonoBold` |
| `SerifItalic` | `SansItalic` | `MonoItalic` |
| `SerifBoldItalic` | `SansBoldItalic` | `MonoBoldItalic` |

Use `BundledFonts.GetSubstitute(StandardFont)` to auto-map.

### PdfColor Named Constants

`PdfColor.Black`, `PdfColor.White`, `PdfColor.Red`, `PdfColor.Green`, `PdfColor.Blue`

Factory methods: `PdfColor.FromRgb(r, g, b)`, `PdfColor.FromGray(gray)`, `PdfColor.FromCmyk(c, m, y, k)`

### PdfColorSpace

`DeviceGray`, `DeviceRGB`, `DeviceCMYK`

### PdfLineCap

`Butt` (default), `Round`, `ProjectingSquare`

### PdfLineJoin

`Miter` (default), `Round`, `Bevel`

### PdfPageLabelStyle

`Decimal`, `UpperRoman`, `LowerRoman`, `UpperAlpha`, `LowerAlpha`, `None`

### PdfArtifactType

`Pagination`, `Layout`, `Page`, `Background`

### PdfTableScope

`Row`, `Column`, `Both`

### PdfAnnotationType

`Text`, `FreeText`, `Highlight`, `Underline`, `Squiggly`, `StrikeOut`, `Stamp`

### PdfStampIcon

`Approved`, `Experimental`, `NotApproved`, `AsIs`, `Expired`, `NotForPublicRelease`, `Confidential`, `Final`, `Sold`, `Departmental`, `ForComment`, `TopSecret`, `Draft`, `ForPublicRelease`

### PdfLayoutTextAlign (Accessibility)

`Start` (default), `Center`, `End`, `Justify`

### PdfPlacement (Accessibility)

`Block`, `Inline`, `Before`, `Start`, `End`

### PdfPhoneticAlphabet

`Ipa`, `XSampa`

### PdfNoteType

`Footnote`, `Endnote`, `Rearnote`

### PdfEncryptionAlgorithm

`Aes128`, `Aes256`

### PdfAConformanceLevel

`None`, `PdfA1B`, `PdfA2B`, `PdfA3B`

### PdfUaConformanceLevel

`None`, `PdfUA1`, `PdfUA2`

### PageSize Constants

`PageSize.Letter` (612×792), `PageSize.Legal` (612×1008), `PageSize.A4` (595.28×841.89), `PageSize.A3` (841.89×1190.55), `PageSize.A5` (419.53×595.28), `PageSize.Tabloid` (792×1224)
