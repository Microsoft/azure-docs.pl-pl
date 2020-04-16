---
title: Korzystanie z platformy .NET for Apache Spark w usłudze Azure Synapse Analytics
description: Dowiedz się więcej o używaniu platformy .NET i Apache Spark do przetwarzania wsadowego, przesyłania strumieniowego w czasie rzeczywistym, uczenia maszynowego i pisania zapytań ad hoc w notesach usługi Azure Synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430515"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Korzystanie z platformy .NET for Apache Spark w usłudze Azure Synapse Analytics

[.NET for Apache Spark](https://dot.net/spark) jest bezpłatną, open-source i międzyplatformową obsługą platformy .NET dla platformy Spark. .NET for Apache Spark udostępnia powiązania .NET dla platformy Spark, które umożliwiają dostęp do interfejsów API platformy Spark za pośrednictwem języka C# i F#. Z .NET dla Apache Spark, masz możliwość pisania i wykonywania funkcji zdefiniowanych przez użytkownika dla platformy Spark przy użyciu platformy .NET. Interfejsy API platformy .NET dla platformy Spark umożliwiają dostęp do wszystkich aspektów platformy Spark, które ułatwiają analizowanie danych, w tym programu Spark SQL i usługi Structured Streaming.

Można analizować dane za pomocą platformy .NET for Apache Spark za pomocą definicji zadań wsadowych platformy Spark lub za pomocą interaktywnych notesów usługi Azure Synapse Analytics. W tym artykule dowiesz się, jak używać platformy .NET for Apache Spark z platformą Azure Synapse przy użyciu obu technik. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Przesyłanie zadań wsadowych przy użyciu definicji zadania platformy Spark

Zapoznaj się z samouczkiem, aby dowiedzieć się, jak używać usługi Azure Synapse Analytics do [tworzenia definicji zadań platformy Apache Spark dla pul synapse spark.](apache-spark-job-definitions.md) Jeśli aplikacja nie została spakowana do przesłania do usługi Azure Synapse, wykonaj następujące kroki.

1. Uruchom następujące polecenia, aby opublikować aplikację. Pamiętaj, aby zastąpić *mySparkApp* ścieżką do aplikacji.

   **W systemie Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **W systemie Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Program .NET for Apache Spark w notesach usługi Azure Synapse Analytics

Podczas tworzenia nowego notesu wybierasz jądro języka, które chcesz wyrazić swoją logikę biznesową. Obsługa jądra dla kilku języków, w tym C#.

Aby użyć platformy .NET for Apache Spark w notesie usługi Azure Synapse Analytics, wybierz jako jądro **opcję .NET Spark (C#)** i dołącz notes do istniejącej puli platformy Spark.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Program .NET for Apache Spark w notesach usługi Azure Synapse Analytics 

Notesy są doskonałym rozwiązaniem do tworzenia prototypów potoków i scenariuszy platformy .NET dla platformy Apache Spark. Możesz szybko i wydajnie rozpocząć pracę, zrozumienie, filtrowanie, wyświetlanie i wizualizowanie danych. Inżynierowie danych, analitycy danych, analitycy biznesowi i inżynierowie uczenia maszynowego mogą współpracować nad udostępnionym, interaktywnym dokumentem. Widoczne są natychmiastowe wyniki eksploracji danych i można wizualizować dane w tym samym notesie.

### <a name="how-to-use-notebooks"></a>Jak korzystać z notesów

Podczas tworzenia nowego notesu należy wybrać jądro języka, które ma wyrażać logikę biznesową. Obsługa jądra dla kilku języków, w tym C#. 

Aby użyć platformy .NET for Apache Spark w notesie usługi Azure Synapse Analytics, wybierz jako jądro **opcję .NET Spark (C#)** i dołącz notes do istniejącej puli platformy Spark. 

Notes platformy .NET Spark jest oparty na interaktywnych środowiskach platformy .NET i zapewnia interaktywne środowiska języka `spark` C# z możliwością używania platformy .NET for Spark po wyjęciu z pudełkiem ze zmienną sesji platformy Spark już wstępnie zdefiniowaną.

### <a name="sparknet-c-kernel-features"></a>funkcje jądra Spark.NET C#

Następujące funkcje są dostępne podczas korzystania z platformy .NET for Apache Spark w notesie usługi Azure Synapse Analytics:

* Deklaratywny kod HTML: Generowanie danych wyjściowych z komórek przy użyciu składni HTML, takich jak nagłówki, listy punktowane, a nawet wyświetlanie obrazów.
* Proste instrukcje C# (takie jak przypisania, drukowanie do konsoli, zgłaszanie wyjątków i tak dalej).
* Wielowierszowe bloki kodu języka C# (na przykład if statements, foreach loops, class definitions, and so on).
* Dostęp do standardowej biblioteki Języka C# (takich jak System, LINQ, Wyliczeń i tak dalej).
* Obsługa [funkcji języka C# 8.0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* "iskra" jako wstępnie zdefiniowana zmienna, która zapewnia dostęp do sesji Apache Spark.
* Obsługa definiowania [funkcji zdefiniowanych przez użytkownika platformy .NET, które można uruchamiać w programie Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Obsługa wizualizacji danych wyjściowych z zadań platformy Spark przy użyciu różnych wykresów (takich jak linia, pasek lub histogram) `XPlot.Plotly` i układów (takich jak pojedyncze, nałożone itd.) przy użyciu biblioteki.
* Możliwość dołączania pakietów NuGet do notesu języka C#.

## <a name="next-steps"></a>Następne kroki

* [.NET dla apache Spark dokumentacji](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [Interaktywna sieć .NET](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)