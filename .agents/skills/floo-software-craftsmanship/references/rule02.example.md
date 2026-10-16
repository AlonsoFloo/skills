# Rule 2: Prefer Strategy Pattern Over Unbounded Switches — Reference Examples

Deep dive for [SKILL.md — Rule 2](../SKILL.md).

______________________________________________________________________

## Architectural Deep Dive

When business requirements introduce new variants (e.g. new payment providers,
export formats, notification channels), editing an central `switch`/`when` block
violates the Open/Closed Principle and risks breaking existing handlers.

### Comprehensive Example

**❌ DON'T (Unbounded conditional branching)**

```kotlin
class ReportExporter {
    fun export(data: ReportData, format: String): ByteArray {
        return when (format) {
            "PDF" -> generatePdf(data)
            "CSV" -> generateCsv(data)
            "EXCEL" -> generateExcel(data)
            "JSON" -> generateJson(data)
            else -> throw IllegalArgumentException("Unsupported format: $format")
        }
    }
}
```

**✅ DO (Polymorphic Strategy Pattern with registry lookup)**

```kotlin
interface ExportStrategy {
    val format: ExportFormat
    fun export(data: ReportData): ByteArray
}

class PdfExportStrategy : ExportStrategy {
    override val format = ExportFormat.PDF
    override fun export(data: ReportData): ByteArray = /* PDF generation */
}

class ReportExportService(strategies: List<ExportStrategy>) {
    private val strategyMap = strategies.associateBy { it.format }

    fun export(data: ReportData, format: ExportFormat): ByteArray {
        val strategy = strategyMap[format]
            ?: throw IllegalArgumentException("No exporter registered for $format")
        return strategy.export(data)
    }
}
```
