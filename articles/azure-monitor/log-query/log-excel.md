---
title: Integracja Log Analytics i programu Excel
description: Pobierz Log Analytics zapytanie do programu Excel i Odśwież wyniki w programie Excel.
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: d903d1bb16ba3576d0092979f1cc6b82fac1c0be
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507523"
---
# <a name="integrate-log-analytics-and-excel"></a>Integracja Log Analytics i programu Excel

Można zintegrować Azure Monitor Log Analytics i Microsoft Excel przy użyciu zapytań M i interfejsu API Log Analytics.  Ta Integracja umożliwia wysyłanie rekordów 500 000 do programu Excel.

> [!NOTE]
> Ponieważ program Excel to lokalna aplikacja kliencka, lokalne ograniczenia sprzętu i oprogramowania mają wpływ na wydajność i możliwość przetwarzania dużych zestawów danych.

## <a name="create-your-m-query-in-log-analytics"></a>Utwórz zapytanie M w Log Analytics 

1. **Twórz i uruchamiaj zapytanie w usłudze** log Analytics, jak zwykle. Nie martw się, jeśli osiągnięto ograniczenie 10 000 rekordów w interfejsie użytkownika.  Zalecamy użycie dat względnych, takich jak funkcja "temu" lub selektor czasu interfejsu użytkownika — program Excel odświeża właściwy zestaw danych.
  
2. **Eksportuj zapytanie** — gdy jesteś zadowolony z zapytania i jego wyników, wyeksportuj zapytanie do M przy użyciu opcji log Analytics **Eksportuj do Power BI (M Query)** w menu *Eksportuj* :

:::image type="content" source="media/log-excel/export-query.png" alt-text="Log Analytics zapytanie z opcją danych i eksportu" border="true":::



Wybranie tej opcji spowoduje pobranie pliku txt zawierającego kod M, którego można użyć w programie Excel.

Zapytanie pokazane powyżej eksportuje następujący kod M. Oto przykład kodu M wyeksportowanego dla zapytania w naszym przykładzie:

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>Połącz zapytanie z programem Excel 

W celu zaimportowania zapytania. 

1. Otwórz program Microsoft Excel. 
1. Na Wstążce przejdź do menu **dane** . Wybierz pozycję **Pobierz dane**. Z **innych źródeł** wybierz pozycję **puste zapytanie** :
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Importuj z pustej opcji programu Excel" border="true":::

1. W oknie dodatku PowerShell wybierz pozycję **Edytor zaawansowany** :

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Zaawansowany edytor zapytań programu Excel" border="true":::

 
1. Zastąp tekst w edytorze zaawansowanym kwerendą wyeksportowaną z Log Analytics:

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="Tworzenie pustego zapytania" border="true":::
 
1. Wybierz opcję **gotowe** , a następnie **Załaduj i Zamknij**. Program Excel wykonuje zapytanie przy użyciu interfejsu API usługi log Analytics, a następnie pokazuje zestaw wyników.
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Wyniki zapytania w programie Excel" border="true":::

##  <a name="refreshing--data"></a>Odświeżanie danych

Możesz odświeżyć dane bezpośrednio z programu Excel. W grupie menu **dane** na wstążce programu Excel wybierz przycisk **Odśwież** .
 
## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat integracji programu Excel z zewnętrznymi źródłami danych, zobacz [Importowanie danych z zewnętrznych źródeł danych (Power Query)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)