---
title: Korzystanie z platformy .NET z usługą Hadoop MapReduce w usłudze HDInsight opartej na systemie Linux — Azure
description: Dowiedz się, jak używać aplikacji .NET do przesyłania strumieniowego MapReduce w usłudze HDInsight opartej na systemie Linux.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 02/27/2018
ms.openlocfilehash: 9e626d08e7fd315ca70765477c5dae56030521c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866407"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrowanie rozwiązań .NET dla usługi HDInsight opartej na systemie Windows do usługi HDInsight w oparciu o system Linux

Klastry HDInsight oparte na systemie Linux używają systemu [mono ( https://mono-project.com) ](https://mono-project.com) do uruchamiania aplikacji .NET. Program mono umożliwia korzystanie ze składników platformy .NET, takich jak aplikacje MapReduce z usługą HDInsight opartą na systemie Linux. W tym dokumencie przedstawiono sposób migrowania rozwiązań .NET utworzonych dla klastrów usługi HDInsight opartych na systemie Windows do pracy z programem mono w usłudze HDInsight opartej na usłudze Linux.

## <a name="mono-compatibility-with-net"></a>Zgodność mono z platformą .NET

W usłudze HDInsight w wersji 3,6 jest dołączony system mono w wersji. Aby uzyskać więcej informacji na temat wersji programu mono zawartej w usłudze HDInsight, zobacz [wersje składników usługi HDInsight](hdinsight-component-versioning.md).

Aby uzyskać więcej informacji na temat zgodności między programami mono i .NET, zobacz [zgodność https://www.mono-project.com/docs/about-mono/compatibility/) z programem mono (](https://www.mono-project.com/docs/about-mono/compatibility/) dokument.

> [!IMPORTANT]  
> Struktura SCP.NET jest zgodna z mono. Aby uzyskać więcej informacji na temat używania SCP.NET z programem mono, zobacz [Korzystanie z programu Visual Studio do tworzenia topologii języka C# dla Apache Storm w usłudze HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Automatyczna analiza przenośności

[Analizatora przenośności platformy .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) można użyć do wygenerowania raportu o niezgodności między aplikacją i mono. Wykonaj następujące kroki, aby skonfigurować Analizator do sprawdzania, czy aplikacja jest przenośna:

1. Zainstaluj [Analizator przenośności platformy .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Podczas instalacji wybierz wersję programu Visual Studio, która ma być używana.

2. W programie Visual Studio 2015 wybierz pozycję __Analizuj__  >  __Ustawienia analizatora przenośności__ i upewnij się, że w sekcji __mono__ jest zaznaczone pole __4,5__ .

    :::image type="content" source="./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png" alt-text="4,5 sprawdzono w sekcji mono dla ustawień analizatora":::

    Wybierz przycisk __OK__, aby zapisać konfigurację.

3. Wybierz pozycję __Analizuj__  >  __Analizuj przenośność zestawu__. Wybierz zestaw, który zawiera Twoje rozwiązanie, a następnie wybierz pozycję __Otwórz__ , aby rozpocząć analizę.

4. Po zakończeniu analizy wybierz kolejno pozycje __Analizuj__  >  __Przeglądaj raporty analizy__. W obszarze __wyniki analizy przenoszenia__ wybierz pozycję __Otwórz raport__ , aby otworzyć raport.

    :::image type="content" source="./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png" alt-text="Okno dialogowe wyników analizatora przenośności":::

> [!IMPORTANT]  
> Analizator nie może przechwytywać każdego problemu z rozwiązaniem. Na przykład ścieżka pliku `c:\temp\file.txt` jest uważana za OK, jeśli mono jest uruchomiony w systemie Windows. Ta sama ścieżka nie jest prawidłowa na platformie Linux.

## <a name="manual-portability-analysis"></a>Ręczna analiza przenośności

Wykonaj ręczną inspekcję kodu, korzystając z informacji z [przenośności aplikacji ( https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/) dokument.

## <a name="modify-and-build"></a>Modyfikowanie i kompilowanie

Możesz nadal używać programu Visual Studio do kompilowania rozwiązań platformy .NET dla usługi HDInsight. Należy jednak upewnić się, że projekt jest skonfigurowany do używania .NET Framework 4,5.

## <a name="deploy-and-test"></a>Wdróż i przetestuj

Po zmodyfikowaniu rozwiązania przy użyciu rekomendacji z analizatora przenośności platformy .NET lub z analizy ręcznej należy przetestować ją z usługą HDInsight. Testowanie rozwiązania w klastrze usługi HDInsight opartego na systemie Linux może ujawnić drobne problemy, które muszą zostać poprawione. Zalecamy włączenie dodatkowego rejestrowania w aplikacji podczas jej testowania.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do dzienników, zobacz następujące dokumenty:

* [Dostęp do Apache Hadoop dzienników aplikacji PRZĘDZy w usłudze HDInsight opartej na systemie Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Następne kroki

* [Używanie języka C# z MapReduce w usłudze HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Korzystanie z funkcji zdefiniowanych przez użytkownika w języku C# z usługami Apache Hive i Apache chlewnej](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Tworzenie topologii języka C# dla Apache Storm w usłudze HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
