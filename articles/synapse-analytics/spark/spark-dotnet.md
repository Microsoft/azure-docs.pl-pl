---
title: Korzystanie z platformy .NET dla Apache Spark przy użyciu usługi Azure Synapse Analytics
description: Dowiedz się więcej o korzystaniu z platformy .NET i Apache Spark do przetwarzania wsadowego, przesyłania strumieniowego w czasie rzeczywistym, uczenia maszynowego i zapisywania zapytań ad hoc w notesach usługi Azure Synapse Analytics.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 3882352c7e1d484818a58d7bd4410cbd66bd6637
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587803"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Korzystanie z platformy .NET dla Apache Spark przy użyciu usługi Azure Synapse Analytics

[Platforma .NET dla Apache Spark](https://dot.net/spark) to bezpłatna, otwarta i wieloplatformowa obsługa platformy .NET dla platformy Spark. Platforma .NET dla Apache Spark zawiera powiązania platformy .NET dla platformy Spark, które umożliwiają dostęp do interfejsów API platformy Spark za poorednictwem języków C# i F #. Za pomocą platformy .NET dla Apache Spark można pisać i uruchamiać funkcje zdefiniowane przez użytkownika dla platformy Spark przy użyciu platformy .NET. Interfejsy API platformy .NET dla platformy Spark umożliwiają dostęp do wszystkich aspektów platformy Spark, które pomagają analizować dane, w tym Spark SQL i strukturalne przesyłanie strumieniowe.

Dane można analizować za pomocą platformy .NET dla Apache Spark za pomocą definicji zadań wsadowych platformy Spark lub interakcyjnych notesów usługi Azure Synapse Analytics. W tym artykule dowiesz się, jak używać platformy .NET do Apache Spark z usługą Azure Synapse przy użyciu obu tych technik.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Przesyłanie zadań wsadowych przy użyciu definicji zadania platformy Spark

Zapoznaj się z samouczkiem, aby dowiedzieć się, jak [utworzyć Apache Spark definicje zadań dla pul Synapse Spark za](apache-spark-job-definitions.md)pomocą usługi Azure Synapse Analytics. Jeśli aplikacja nie została spakowana w celu przesłania do usługi Azure Synapse, wykonaj następujące czynności.

1. Uruchom następujące polecenia, aby opublikować aplikację. Pamiętaj, aby zastąpić *mySparkApp* ścieżką do aplikacji.

   **W systemie Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **W systemie Linux:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Platforma .NET dla Apache Spark w notesach usługi Azure Synapse Analytics

Podczas tworzenia nowego notesu należy wybrać jądro języka, które ma być używane do prowadzenia logiki biznesowej. Obsługa jądra w kilku językach, w tym C#.

Aby używać platformy .NET do Apache Spark w notesie usługi Azure Synapse Analytics, wybierz opcję **.NET Spark (C#)** jako jądro i Dołącz Notes do istniejącej puli platformy Spark.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Platforma .NET dla Apache Spark w notesach usługi Azure Synapse Analytics 

Notesy są świetną opcją tworzenia prototypów platformy .NET na potrzeby potoków Apache Spark i scenariuszy. Możesz szybko i wydajnie zacząć pracować z, zrozumieć, filtrować, wyświetlać i wizualizować dane. Inżynierowie ds. ds. danych, analityków biznesowych i inżynierów uczenia maszynowego mogą współpracować nad udostępnionym, interaktywnym dokumentem. Zobaczysz natychmiastowe wyniki z eksploracji danych i można wizualizować dane w tym samym notesie.

### <a name="how-to-use-notebooks"></a>Jak korzystać z notesów

Podczas tworzenia nowego notesu należy wybrać jądro języka, które ma być używane do prowadzenia logiki biznesowej. Obsługa jądra w kilku językach, w tym C#.

Aby używać platformy .NET do Apache Spark w notesie usługi Azure Synapse Analytics, wybierz opcję **.NET Spark (C#)** jako jądro i Dołącz Notes do istniejącej puli platformy Spark.

Notes .NET Spark jest oparty na interaktywnych środowiskach .NET i udostępnia interaktywne środowisko C# z możliwością używania platformy .NET dla platformy Spark z użyciem `spark` już wstępnie zdefiniowanej zmiennej sesji platformy Spark.

### <a name="sparknet-c-kernel-features"></a>Funkcje jądra języka C# Spark.NET

Następujące funkcje są dostępne w przypadku korzystania z platformy .NET dla Apache Spark w notesie usługi Azure Synapse Analytics:

* Deklaratywny kod HTML: generowanie danych wyjściowych z komórek przy użyciu składni HTML, takich jak nagłówki, listy punktowane, a nawet wyświetlanie obrazów.
* Proste instrukcje języka C# (takie jak przypisania, drukowanie do konsoli, zgłaszanie wyjątków itp.).
* Wielowierszowe bloki kodu w języku C# (takie jak instrukcje if, pętle foreach, definicje klas itd.).
* Dostęp do standardowej biblioteki języka C# (takiej jak system, LINQ, wyliczalne itd.).
* Obsługa [funkcji języka C# 8,0](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* "Spark" jako wstępnie zdefiniowaną zmienną umożliwiającą dostęp do sesji Apache Spark.
* Obsługa definiowania [funkcji zdefiniowanych przez użytkownika platformy .NET, które mogą być uruchamiane w Apache Spark](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql).
* Obsługa wizualizacji danych wyjściowych z zadań platformy Spark przy użyciu różnych wykresów (takich jak linie, paski lub histogram) i układów (takich jak pojedyncze, nałożone itd.) przy użyciu `XPlot.Plotly` biblioteki.
* Możliwość dołączania pakietów NuGet do notesu w języku C#.

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja platformy .NET dla Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interactive](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)