---
title: Przewodnik programowania procesora zdefiniowany przez użytkownika w języku U-SQL dla Azure Data Lake
description: Zapoznaj się z przewodnikiem programowania U-SQL UDO — zdefiniowanym przez użytkownika procesorem.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512689"
---
# <a name="use-user-defined-processor"></a>Korzystanie z procesora zdefiniowanego przez użytkownika

## <a name="u-sql-udo-user-defined-processor"></a>UDO U-SQL: zdefiniowany przez użytkownika procesor
Procesor zdefiniowany przez użytkownika lub UDP jest typem UDO języka U-SQL, który umożliwia przetwarzanie wierszy przychodzących przez zastosowanie funkcji programowalności. Protokół UDP umożliwia łączenie kolumn, modyfikowanie wartości i dodawanie nowych kolumn w razie potrzeby. W zasadzie ułatwia przetwarzanie zestawu wierszy w celu utworzenia wymaganych elementów danych.

## <a name="how-to-define-and-use-user-defined-processor"></a>Jak zdefiniować i korzystać z procesora zdefiniowanego przez użytkownika
Aby zdefiniować protokół UDP, musimy utworzyć `IProcessor` interfejs z `SqlUserDefinedProcessor` atrybutem, który jest opcjonalny dla protokołu UDP.

Ten interfejs powinien zawierać definicję `IRow` przesłonięcia zestawu wierszy interfejsu, jak pokazano w następującym przykładzie:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** wskazuje, że typ powinien być zarejestrowany jako procesor zdefiniowany przez użytkownika. Klasa ta nie może być dziedziczona.

Atrybut SqlUserDefinedProcessor jest **opcjonalny** dla definicji UDP.

Główne obiekty programowalności są **danymi wejściowymi** i **wyjściowymi**. Obiekt wejściowy służy do wyliczania kolumn wejściowych i danych wyjściowych oraz do ustawiania danych wyjściowych w wyniku działania procesora.

W przypadku wyliczania kolumn wejściowych używamy `input.Get` metody.

```csharp
string column_name = input.Get<string>("column_name");
```

Parametr `input.Get` metody jest kolumną, która jest przenoszona jako część `PRODUCE` klauzuli `PROCESS` instrukcji skryptu podstawowego U-SQL. W tym miejscu musimy użyć poprawnego typu danych.

W przypadku danych wyjściowych Użyj `output.Set` metody.

Należy pamiętać, że producent niestandardowy tylko wyprowadza kolumny i wartości, które są zdefiniowane za pomocą `output.Set` wywołania metody.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

Rzeczywiste dane wyjściowe procesora są wyzwalane przez wywołanie `return output.AsReadOnly();` .

Poniżej znajduje się przykład procesora:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

W tym scenariuszu przypadku użycia procesor generuje nową kolumnę o nazwie "full_description" przez połączenie istniejących kolumn — w tym przypadku "użytkownik" w Wielkiej litery i "des". Powoduje również ponowne wygenerowanie identyfikatora GUID i zwrócenie oryginalnych i nowych wartości identyfikatora GUID.

Jak widać w poprzednim przykładzie, można wywołać metody języka C# podczas `output.Set` wywołania metody.

Poniżej znajduje się przykład podstawowego skryptu U-SQL, który używa niestandardowego procesora:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Następne kroki
* [Przewodnik programowania U-SQL — Omówienie](data-lake-analytics-u-sql-programmability-guide.md)
* [Przewodnik programowania U-SQL — UDT i UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)