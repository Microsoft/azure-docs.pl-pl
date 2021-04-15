---
title: Używanie .NET na Apache Spark
description: Dowiedz się więcej o używaniu platformy .NET i Apache Spark do przetwarzania wsadowego, przesyłania strumieniowego w czasie rzeczywistym, uczenia maszynowego i pisania zapytań ad hoc w Azure Synapse Analytics notesach.
author: luisquintanilla
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: luquinta
ms.reviewer: jrasnick
ms.openlocfilehash: 8d045c1ec96bb7b31a710a28e30e3d428922b65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378554"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Korzystanie z platformy .NET for Apache Spark z usługą Azure Synapse Analytics

[Platforma .NET for Apache Spark](https://dot.net/spark) zapewnia bezpłatną, [międzyplatformową](https://github.com/dotnet/spark)obsługę platformy .NET dla platformy Spark. 

Udostępnia on powiązania platformy .NET dla platformy Spark, które umożliwiają dostęp do interfejsów API platformy Spark za pośrednictwem języka C# i F#. Platforma .NET for Apache Spark umożliwia również pisanie i wykonywanie funkcji zdefiniowanych przez użytkownika dla platformy Spark napisanych na platformie .NET. Interfejsy API platformy .NET dla platformy Spark umożliwiają dostęp do wszystkich aspektów ramek danych platformy Spark, które ułatwiają analizowanie danych, w tym Spark SQL, Delta Lake i przesyłanie strumieniowe ze strukturą.

Dane można analizować za pomocą platformy .NET na Apache Spark za pomocą definicji zadań wsadowych platformy Spark lub za pomocą Azure Synapse Analytics notesów. Z tego artykułu dowiesz się, jak używać programu .NET na Apache Spark z Azure Synapse obiema technikami.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Przesyłanie zadań wsadowych przy użyciu definicji zadania spark

Zapoznaj się z samouczkiem, aby dowiedzieć się, jak za pomocą Azure Synapse Analytics tworzyć definicje [Apache Spark dla pul synapse Spark.](apache-spark-job-definitions.md) Jeśli aplikacja nie została spakowana do przesyłania do usługi Azure Synapse, wykonaj następujące kroki.

1. Uruchom następujące polecenia, aby opublikować aplikację. Pamiętaj, aby zastąpić *aplikację mySparkApp* ścieżką do aplikacji.
   
   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.18.04-x64
   ```

2. Spakuj zawartość folderu publish, na przykład utworzonego `publish.zip` w wyniku kroku 1. Wszystkie zestawy powinny być w pierwszej warstwie pliku ZIP i nie powinny mieć żadnej warstwy folderów pośrednich. Oznacza to, że podczas rozpakowania `publish.zip` wszystkie zestawy są wyodrębnione do bieżącego katalogu roboczego.

    **W systemie Windows:**

    Użyj programu wyodrębniania, takiego jak [7-Zip](https://www.7-zip.org/) lub [WinZip,](https://www.winzip.com/)aby wyodrębnić plik do katalogu bin ze wszystkimi opublikowanymi plikami binarnymi.

    **W systemie Linux:**

    Otwórz powłokę bash i cd w katalogu bin ze wszystkimi opublikowanymi plikami binarnymi i uruchom następujące polecenie.

    ```bash
    zip -r publish.zip
    ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET dla Apache Spark w Azure Synapse Analytics notesach 

Notesy to doskonałe rozwiązanie do tworzenia prototypów dla programu .NET na Apache Spark potoków i scenariuszy. Możesz szybko i wydajnie rozpocząć pracę z danymi, zrozumieć je, filtrować, wyświetlać i wizualizować. 

Inżynierowie danych, analitycy danych, analitycy biznesowi i inżynierowie uczenia maszynowego mogą współpracować nad udostępnionym, interaktywnym dokumentem. Zobaczysz natychmiastowe wyniki eksploracji danych i możesz zwizualizować dane w tym samym notesie.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>Jak używać programu .NET na Apache Spark notesach

Podczas tworzenia nowego notesu wybierasz jądro języka, które chcesz wyrazić logiką biznesową. Obsługa jądra jest dostępna dla kilku języków, w tym C#.

Aby użyć platformy .NET Apache Spark w notesie programu Azure Synapse Analytics, wybierz platformę **.NET Spark (C#)** jako jądro i dołącz notes do istniejącej puli Apache Spark bez serwera.

Notes platformy .NET Spark jest oparty na interaktywnych funkcjach platformy [.NET](https://github.com/dotnet/interactive) i zapewnia interaktywne środowisko języka C# z możliwością korzystania z gotowej do użycia platformy .NET dla platformy Spark ze wstępnie zdefiniowaną zmienną sesji platformy `spark` Spark.

### <a name="install-nuget-packages-in-notebooks"></a>Instalowanie pakietów NuGet w notesach

Wybrane pakiety NuGet można zainstalować w notesie za pomocą polecenia `#r nuget` magic przed nazwą pakietu NuGet. Na poniższym diagramie przedstawiono przykład:

![Zrzut ekranu przedstawiający używanie #r do instalowania pakietu NuGet notesu platformy Spark dla platformy .NET](./media/apache-spark-development-using-notebooks/synapse-spark-dotnet-notebook-nuget.png)

Aby dowiedzieć się więcej na temat pracy z pakietami NuGet w notesach, zobacz interaktywną dokumentację programu [.NET](https://github.com/dotnet/interactive/blob/main/docs/nuget-overview.md).

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET dla Apache Spark jądra języka C#

Następujące funkcje są dostępne w przypadku używania programu .NET Apache Spark w notesie Azure Synapse Analytics notesie:

* Deklaratywny kod HTML: generowanie danych wyjściowych z komórek przy użyciu składni HTML, takiej jak nagłówki, listy punktowane, a nawet wyświetlanie obrazów.
* Proste instrukcje języka C# (takie jak przypisania, drukowanie w konsoli, zgłaszanie wyjątków itd.).
* Wielo wierszowe bloki kodu języka C# (takie jak instrukcje if, pętle foreach, definicje klas itd.).
* Dostęp do standardowej biblioteki języka C# (takiej jak System, LINQ, Enumerables itd.).
* Obsługa funkcji języka C# 8.0.
* `spark` jako wstępnie zdefiniowaną zmienną, aby zapewnić dostęp do Apache Spark aplikacji.
* Obsługa definiowania funkcji zdefiniowanych przez użytkownika na platformie [.NET, które mogą być uruchamiane w Apache Spark](/dotnet/spark/how-to-guides/udf-guide). Zalecamy pisanie i wywołanie funkcji UDF na platformie .NET dla Apache Spark [interaktywnych](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue) środowisk, aby dowiedzieć się, jak używać funkcji UDF na platformie .NET na Apache Spark środowisk interaktywnych.
* Obsługa wizualizowania danych wyjściowych z zadań Spark przy użyciu różnych wykresów (takich jak linia, pasek lub histogram) i układów (takich jak pojedyncze, nałogowe itp.) przy użyciu `XPlot.Plotly` biblioteki .
* Możliwość dołączania pakietów NuGet do notesu języka C#.

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja programu .NET Apache Spark dla programu](/dotnet/spark/)
* [.NET dla Apache Spark przewodników interaktywnych](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue)
* [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)
* [.NET Interactive](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
