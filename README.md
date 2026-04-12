# ObviousPDF

Enabling you create standards-compliant, more accessible PDFs in .NET.

**Built from ISO 32000. Accessibility-first by design.**

## Quick Start

### Installation

1. Download `ObviousPDF.dll` from [`lib/net8.0/`](https://github.com/alanfrans/ObviousPDF/tree/main/lib/net8.0) in this repository
2. Add a reference to your .NET 8 project
3. Import the namespace:

```csharp
using ObviousPDF;
```

### Hello World

```csharp
using ObviousPDF;

var doc = new PdfDocument();
var page = doc.AddPage();

// Add text
page.AddText("Hello, World!", 72, 700, 
    new PdfTextOptions { FontSize = 24 });

// Save
doc.Save("output.pdf");
```

## Features

- **Zero External Dependencies** — Ships as a standalone DLL; no NuGet package dependencies required at runtime
- **Easy Accessibility (v1.2.0)** — One-line PDF/UA compliance: `doc.EnableAccessibility()` in C#, `"accessible": true` in JSON, `accessible="true"` in XML, or `accessible,true` in CSV
- **Three Input Pipelines** — Generate accessible PDFs from **JSON** (`ObviousPDF.Json`), **XML** (`ObviousPDF.Xml`), or **CSV** (`ObviousPDF.Csv`) — all produce identical output
- **Accessibility Report (v1.3.0)** — Automated accessibility feedback: `doc.GenerateAccessibilityReport = true` or `"generateAccessibilityReport": true` in JSON/XML/CSV
- **PDF/UA Accessibility** — Built-in support for tagged PDFs, accessibility metadata, and automated compliance checking
- **Encryption & Security** — Digital signatures, user/owner passwords, AES-128/AES-256 encryption
- **Advanced Layouts** — Multi-column layouts, nested tables, reusable form templates (XObjects)
- **Rich Content** — Vector graphics, gradients, patterns, images, embedded fonts, interactive forms
- **Optimization** — Compact serialization, linearized PDFs for web streaming, font subsetting
- **International Support** — CJK (Chinese/Japanese/Korean) text with ruby annotations, pronunciation guides
- **Standards Compliance** — PDF/A archival, PDF/UA accessibility, WCAG guidelines

## Documentation

- **[API Reference](https://obviouspdf.com/api-reference.html)** — Complete class and method documentation
- **[LLM Guide](https://github.com/alanfrans/ObviousPDF/blob/main/docs/LLM_GUIDE.md)** — Comprehensive guide for AI coding assistants (ChatGPT, Claude, etc.)
- **[Full Documentation Site](https://obviouspdf.com)** — Examples, tutorials, and more
- **[License Agreement](https://obviouspdf.com/license.html)** — Licensing terms and conditions

## Licensing

**Free** for individuals and organizations with fewer than 10 employees.

**$100/year** for organizations with 10+ employees (unlimited developers).
Get your enterprise license at [enterprise.obviouspdf.com](https://enterprise.obviouspdf.com) or email [license@obviouspdf.com](mailto:license@obviouspdf.com) for inquiries.

See [LICENSE](https://obviouspdf.com/license.html) for full details.

## Key Characteristics

| Feature | Details |
|---------|---------|
| **Target Framework** | .NET 8.0+ |
| **Runtime Dependencies** | 1 (System.Security.Cryptography.Pkcs — Microsoft, MIT licensed) |
| **Bundled Fonts** | 3 (CMU Serif, Sora, CMU Typewriter — SIL OFL 1.1) |
| **Accessibility** | PDF/UA 1.0 compliant |
| **Encryption** | AES-128, AES-256, RSA-4096 digital signatures |
| **Output Formats** | PDF 1.7 / 2.0 (auto-detected), PDF/A-1b, PDF/A-2b, PDF/A-3b, PDF/UA-1, linearized |

## Getting Started

1. Download `ObviousPDF.dll` from [`lib/net8.0/`](https://github.com/alanfrans/ObviousPDF/tree/main/lib/net8.0) in this repository
2. Add reference to your .NET 8 project
3. Read the **[API Reference](https://obviouspdf.com/api-reference.html)** for comprehensive documentation
4. Use the **[LLM Guide](https://github.com/alanfrans/ObviousPDF/blob/main/docs/LLM_GUIDE.md)** if working with AI coding assistants
5. Visit **[obviouspdf.com](https://obviouspdf.com)** for examples and tutorials

> **⚠️ Disclaimer:** The automated accessibility reports and checks provided by ObviousPDF are intended solely to assist in the assessment of document accessibility. They are not a comprehensive accessibility audit. To confirm that WCAG or PDF/UA standards are fully met, it is recommended to have human assessment by an accessibility specialist.

## Support

For issues, feature requests, or questions:
- Visit **[obviouspdf.com](https://obviouspdf.com)** for documentation and examples
- See [License Agreement](LICENSE.md) for warranty and liability information

## Roadmap

- [ ] Form XObject templates library
- [ ] Barcode & QR code support
- [ ] Advanced typography (kerning, ligatures)
- [ ] PDF comparison & merge utilities

---

**© 2026 Relevant LLC.** ObviousPDF is a product of Relevant LLC.

