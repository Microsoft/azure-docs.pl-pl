---
title: Przewodnik programowania w języku U-SQL zdefiniowany przez użytkownika dla Azure Data Lake
description: Dowiedz się więcej na temat przewodnika programowania U-SQL UDO, który definiuje użytkownik.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512638"
---
# <a name="use-user-defined-outputter"></a>Korzystanie z funkcji generującej dane wyjściowe zdefiniowanej przez użytkownika

## <a name="u-sql-udo-user-defined-outputter"></a>UDO U-SQL: zdefiniowane przez użytkownika
Wypełnianie zdefiniowane przez użytkownika to inny UDO języka U-SQL, który pozwala na rozbudowa wbudowanej funkcji języka U-SQL. Podobnie jak w przypadku wyodrębniania, istnieje kilka wbudowanych wypełnień.

* *. Text ()*: zapisuje dane do rozdzielanych plików tekstowych różnych kodowań.
* *Outputters.Csv ()*: zapisuje dane w plikach z wartościami rozdzielanymi przecinkami (CSV) różnych kodowań.
* *. Tsv ()*: zapisuje dane w plikach z wartościami rozdzielanymi tabulatorami (tsv) różnych kodowań.

Niestandardowy element wypełnień umożliwia zapisanie danych w niestandardowym formacie zdefiniowanym. Może to być przydatne w przypadku następujących zadań:

* Zapisywanie danych w plikach z częściową strukturą lub bez struktury.
* Zapisywanie danych nie jest obsługiwane.
* Modyfikowanie danych wyjściowych lub Dodawanie atrybutów niestandardowych.

## <a name="how-to-define-and-use-user-defined-outputter"></a>Jak zdefiniować i używać wystawcy zdefiniowanego przez użytkownika
Aby zdefiniować wystawcy zdefiniowany przez użytkownika, musimy utworzyć `IOutputter` interfejs.

Poniżej znajduje się `IOutputter` Implementacja klasy bazowej:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Wszystkie parametry wejściowe do elementu wypełnienia, takie jak ograniczniki kolumn/wierszy, kodowanie i tak dalej, muszą być zdefiniowane w konstruktorze klasy. `IOutputter`Interfejs powinien również zawierać definicję `void Output` przesłonięcia. `[SqlUserDefinedOutputter(AtomicFileProcessing = true)`Opcjonalnie można ustawić atrybut na potrzeby przetwarzania plików niepodzielnych. Aby uzyskać więcej informacji, zobacz następujące szczegóły.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` jest wywoływana dla każdego wiersza wejściowego. Zwraca `IUnstructuredWriter output` zestaw wierszy.
* Klasa konstruktora służy do przekazywania parametrów do zdefiniowanego przez użytkownika wystawcy.
* `Close` służy do opcjonalnego przesłonięcia w celu zwolnienia drogi lub określenia czasu zapisania ostatniego wiersza.

Atrybut **SqlUserDefinedOutputter** wskazuje, że typ powinien być zarejestrowany jako element, zdefiniowany przez użytkownika. Klasa ta nie może być dziedziczona.

SqlUserDefinedOutputter jest opcjonalnym atrybutem dla definicji elementu wyszukania zdefiniowanej przez użytkownika. Służy do definiowania właściwości AtomicFileProcessing.

* AtomicFileProcessing bool   

* **true** = wskazuje, że ten wynik nie wymaga niepodzielnych plików wyjściowych (JSON, XML,...)
* **wartość false** = wskazuje, że ten wynik może dotyczyć plików podzielonych/rozproszonych (CSV, SEQ,...)

Główne obiekty programowalności są **wierszami** i **danymi wyjściowymi**. Obiekt **Row** służy do wyliczania danych wyjściowych jako `IRow` interfejsów. Dane **wyjściowe** są używane do ustawiania danych wyjściowych w pliku docelowym.

Dostęp do danych wyjściowych uzyskuje się za pomocą `IRow` interfejsu. Dane wyjściowe są przekazywane w wierszu jednocześnie.

Poszczególne wartości są wyliczane przez wywołanie metody get interfejsu IRow:

```csharp
row.Get<string>("column_name")
```

Nazwy poszczególnych kolumn można określić, wywołując `row.Schema` :

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Takie podejście umożliwia tworzenie elastycznego elementu wymawianego dla dowolnego schematu metadanych.

Dane wyjściowe są zapisywane do pliku przy użyciu `System.IO.StreamWriter` . Parametr Stream jest ustawiany `output.BaseStream` jako część `IUnstructuredWriter output` .

Należy pamiętać, że należy zwrócić bufor danych do pliku po każdej iteracji wiersza. Ponadto, `StreamWriter` obiekt musi być używany z włączonym atrybutem jednorazowym (domyślnie) i słowem kluczowym **using** :

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

W przeciwnym razie wywołanie metody Flush () jawnie po każdej iteracji. Pokazujemy to w poniższym przykładzie.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Ustawianie nagłówków i stopek dla elementu predefiniowanego zdefiniowanego przez użytkownika
Aby ustawić nagłówek, Użyj pojedynczego przepływu wykonywania iteracji.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Kod w pierwszym `if (isHeaderRow)` bloku jest wykonywany tylko raz.

Dla stopki Użyj odwołania do wystąpienia `System.IO.Stream` obiektu ( `output.BaseStream` ). Napisz stopkę w metodzie Close () `IOutputter` interfejsu.  (Aby uzyskać więcej informacji, zobacz Poniższy przykład).

Poniżej znajduje się przykład elementu predefiniowanego zdefiniowanego przez użytkownika:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

I skrypt podstawowy U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Jest to niektóry element HTML, który tworzy plik HTML z danymi tabeli.

### <a name="call-outputter-from-u-sql-base-script"></a>Wywołaj instrukcję z podstawowego skryptu języka U-SQL
Aby wywołać niestandardowy element wywołujący z podstawowego skryptu U-SQL, należy utworzyć nowe wystąpienie tego obiektu.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Aby uniknąć tworzenia wystąpienia obiektu w skrypcie podstawowym, możemy utworzyć otokę funkcji, jak pokazano w poprzednim przykładzie:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

W takim przypadku oryginalne wywołanie wygląda następująco:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>Następne kroki
* [Przewodnik programowania U-SQL — Omówienie](data-lake-analytics-u-sql-programmability-guide.md)
* [Przewodnik programowania U-SQL — UDT i UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)