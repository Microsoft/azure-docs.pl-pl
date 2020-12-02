---
title: Przewodnik programowania ekstraktorów zdefiniowany przez użytkownika w języku U-SQL dla Azure Data Lake
description: Dowiedz się więcej na temat przewodnika programowania U-SQL UDO — wyekstraktora zdefiniowanego przez użytkownika.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a3613c2b95f13e6bbaaf570f522ad1f7b7edd756
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512677"
---
# <a name="use-user-defined-extractor"></a>Użyj wyekstraktora zdefiniowanego przez użytkownika

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL UDO: wyekstraktor zdefiniowany przez użytkownika
Język U-SQL umożliwia importowanie danych zewnętrznych przy użyciu instrukcji EXTRACT. Instrukcja EXTRACT może używać wbudowanych UDO Extracts:  

* *Extracts. Text ()*: zapewnia wyodrębnianie z rozdzielanych plików tekstowych różnych kodowań.

* *Extractors.Csv ()*: zapewnia ekstrakcję z plików z wartościami rozdzielanymi przecinkami (CSV) różnych kodowań.

* *Extracts. tsv ()*: zapewnia ekstrakcję z plików z wartościami rozdzielanymi tabulatorami (tsv) różnych kodowań.

Może być przydatne do opracowania niestandardowego ekstraktora. Może to być przydatne podczas importowania danych, jeśli chcemy wykonać dowolne z następujących zadań:

* Zmodyfikuj dane wejściowe, dzieląc kolumny i modyfikując poszczególne wartości. Funkcja procesora jest lepsza do łączenia kolumn.
* Analizuj dane bez struktury, takie jak strony sieci Web i wiadomości e-mail, lub częściowo niestrukturalne dane, takie jak XML/JSON.
* Analizuj dane w nieobsługiwanym kodowaniu.

## <a name="how-to-define-and-use-user-defined-extractor"></a>Jak zdefiniować i użyć wyekstraktora zdefiniowanego przez użytkownika
Aby zdefiniować zdefiniowany przez użytkownika Ekstraktor lub LUCZ, musimy utworzyć `IExtractor` interfejs. Wszystkie parametry wejściowe do ekstraktora, takie jak ograniczniki kolumn/wierszy i kodowanie, muszą być zdefiniowane w konstruktorze klasy. `IExtractor`Interfejs powinien również zawierać definicję `IEnumerable<IRow>` przesłonięcia w następujący sposób:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

Atrybut **SqlUserDefinedExtractor** wskazuje, że typ powinien być zarejestrowany jako Ekstraktor zdefiniowany przez użytkownika. Klasa ta nie może być dziedziczona.

SqlUserDefinedExtractor jest opcjonalnym atrybutem definicji LUCZ. Służy do definiowania właściwości AtomicFileProcessing dla obiektu LUCZ.

* AtomicFileProcessing bool   

* **true** = wskazuje, że ten Ekstraktor wymaga niepodzielnych plików wejściowych (JSON, XML,...)
* **wartość false** = wskazuje, że ten Ekstraktor może obsłużyć pliki podzielone/rozproszone (CSV, SEQ,...)

Główne obiekty programowalności LUCZ są **danymi wejściowymi** i **wyjściowymi**. Obiekt wejściowy służy do wyliczania danych wejściowych jako `IUnstructuredReader` . Obiekt wyjściowy służy do ustawiania danych wyjściowych w wyniku działania wyodrębniania.

Dane wejściowe są dostępne za pomocą `System.IO.Stream` i `System.IO.StreamReader` .

W przypadku wyliczania kolumn wejściowych należy najpierw podzielić strumień wejściowy przy użyciu ogranicznika wiersza.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Następnie można dodatkowo podzielić wiersz wejściowy na części kolumn.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Aby ustawić dane wyjściowe, używamy `output.Set` metody.

Ważne jest, aby zrozumieć, że niestandardowy Ekstraktor tylko wyprowadza kolumny i wartości, które są zdefiniowane przy użyciu danych wyjściowych. Ustaw wywołanie metody.

```csharp
output.Set<string>(count, part);
```

Rzeczywiste dane wyjściowe wyekstraktoru są wyzwalane przez wywołanie `yield return output.AsReadOnly();` .

Poniżej znajduje się przykład wyekstraktora:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

W tym scenariuszu przypadku użycia Ekstraktor ponownie generuje identyfikator GUID dla kolumny "GUID" i konwertuje wartości kolumny "User" na wielkie litery. Niestandardowe ekstrakty mogą generować bardziej skomplikowane wyniki przez analizowanie danych wejściowych i manipulowanie nimi.

Poniżej przedstawiono podstawowy skrypt U-SQL, który używa wyskakującego ekstraktora:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Następne kroki
* [Przewodnik programowania U-SQL — Omówienie](data-lake-analytics-u-sql-programmability-guide.md)
* [Przewodnik programowania U-SQL — UDT i UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)