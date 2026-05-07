#### Evidence Pizzas List

[Existing List With Additional Record](evidence.png)

#### working sales summary function

```csharp
void GenerateSalesSummary(IEnumerable<string> salesFiles, double totalSales, string outputDirectory)
{
    StringBuilder summary = new StringBuilder();

    summary.AppendLine("Sales Summary");
    summary.AppendLine(" ");
    summary.AppendLine($"Total Sales: {totalSales.ToString("C")}");
    summary.AppendLine();
    summary.AppendLine("Details:");

    var groupedByStore = salesFiles.GroupBy(file => Path.GetFileName(Path.GetDirectoryName(file)));

    foreach (var storeGroup in groupedByStore)
    {
        summary.AppendLine($"    Store {storeGroup.Key}:");

        foreach (var file in storeGroup)
        {
            string salesJson = File.ReadAllText(file);
            var fileName = Path.GetFileName(file);
            double fileTotal = 0;

            if (fileName == "salestotals.json")
            {
                SalesDataTotal? data = JsonConvert.DeserializeObject<SalesDataTotal?>(salesJson);
                fileTotal = data?.OverallTotal ?? 0;
            }
            else if (fileName == "sales.json")
            {
                SalesData? data = JsonConvert.DeserializeObject<SalesData?>(salesJson);
                fileTotal = data?.Total ?? 0;
            }
            else
            {
                SalesData? data = JsonConvert.DeserializeObject<SalesData?>(salesJson);
                fileTotal = data?.Total ?? 0;
            }

            summary.AppendLine($"        {fileName}: {fileTotal.ToString("C")}");
        }
    }

    string summaryPath = Path.Combine(outputDirectory, "sales_summary.txt");
    File.WriteAllText(summaryPath, summary.ToString());

    Console.WriteLine($"Sales summary saved to: {summaryPath}");
}

// Required record declarations
record SalesData(double Total);
record SalesDataTotal(double OverallTotal);
```
