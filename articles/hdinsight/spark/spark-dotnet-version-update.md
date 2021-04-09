---
title: Aktualizowanie platformy .NET dla Apache Spark do wersji 1.0 w programie HDI
description: Dowiedz się więcej o aktualizowaniu programu .NET for Apache Spark w wersji do 1,0 w HDI oraz o tym, jak ma to wpływ na istniejący kod i klastry.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788133"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>Aktualizowanie programu .NET dla Apache Spark do wersji 1.0 w usłudze HDInsight

Ten dokument przedstawia informacje na temat pierwszej głównej wersji [platformy .NET dla Apache Spark](https://github.com/dotnet/spark)i wpływu na bieżące potoki produkcyjne w klastrach usługi HDInsight.

## <a name="about-net-for-apache-spark-version-100"></a>Informacje o programie .NET for Apache Spark w wersji 1.0.0

Jest to pierwsza [główna oficjalna wersja](https://github.com/dotnet/spark/releases/tag/v1.0.0) platformy .net dla Apache Spark i zapewnia kompletność interfejsu API Dataframe dla platformy Spark 2.4. x, a także platformy Spark 3.0. x oraz innych funkcji. Pełną listę wszystkich funkcji, ulepszeń i poprawek błędów można znaleźć w oficjalnych [informacjach o wersji programu v 1.0.0](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md).
Inną ważną kwestią jest to, że ta wersja **nie** jest zgodna z wcześniejszymi wersjami systemów `Microsoft.Spark` i `Microsoft.Spark.Worker` . Zapoznaj się z [przewodnikiem migracji](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) , jeśli planujesz uaktualnić aplikację .net for Apache Spark, aby była zgodna z 1.0.0.

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>Korzystanie z platformy .NET dla Apache Spark v 1.0 w usłudze HDInsight

Nie wpłynie to na bieżące klastry HDI (tj. nadal będą mieć tę samą wersję jak wcześniej), nowo utworzone klastry HDI przeprowadzą tę najnowszą wersję programu .NET 1.0.0 dla Apache Spark. Co to oznacza, jeśli:

- Istnieje **starszy klaster HDI**: Jeśli chcesz uaktualnić aplikację platformy Spark .NET do wersji 1.0.0 (zalecane), musisz zaktualizować `Microsoft.Spark.Worker` wersję w klastrze HDI. Aby uzyskać więcej informacji, zobacz [sekcję zmiana wersji platformy .NET dla Apache Spark w KLASTRZE HDI](#changing-net-for-apache-spark-version-on-hdinsight).
Jeśli nie chcesz aktualizować bieżącej wersji platformy .NET dla Apache Spark w aplikacji, nie trzeba wykonywać żadnych dalszych kroków.  

- **Masz nowy klaster HDI**: Jeśli chcesz uaktualnić aplikację platformy Spark .NET do wersji 1.0.0 (zalecane), nie trzeba wykonywać żadnych kroków, aby zmienić proces roboczy na HDI, jednak należy zapoznać się z [przewodnikiem migracji](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) , aby poznać kroki wymagane do zaktualizowania kodu i potoków.
Jeśli nie chcesz zmieniać bieżącej wersji platformy .NET dla Apache Spark w aplikacji, musisz zmienić wersję w klastrze HDI z wersji 1.0 (domyślnie w nowych klastrach) na dowolną używaną wersję. Aby uzyskać więcej informacji, zobacz [sekcję zmiana wersji platformy .NET dla Apache Spark w KLASTRZE HDI](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight).  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>Zmienianie wersji platformy .NET dla Apache Spark w usłudze HDInsight

### <a name="deploy-microsoftsparkworker"></a>Wdróż aplikację Microsoft. Spark. Worker

`Microsoft.Spark.Worker` jest składnikiem zaplecza, który znajduje się w poszczególnych węzłach procesu roboczego klastra Spark. Jeśli chcesz wykonać w języku C# UDF (funkcja zdefiniowana przez użytkownika), platforma Spark musi zrozumieć, jak uruchomić program .NET CLR w celu wykonania tego UDF. `Microsoft.Spark.Worker` dostarcza kolekcję klas do platformy Spark, która umożliwia korzystanie z tej funkcji. Wybierz wersję procesu roboczego w zależności od wersji programu .NET dla Apache Spark chcesz wdrożyć w klastrze HDI.

1. Pobierz wersję systemu Linux Microsoft. Spark. Worker dla danej wersji. Na przykład, jeśli chcesz `.NET for Apache Spark v1.0.0` , Pobierz [Microsoft. Spark. Worker. netcoreapp 3.1. linux-x64-1.0.0. tar. gz](https://github.com/dotnet/spark/releases/tag/v1.0.0).  

2. Pobierz skrypt [Install-Worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) , aby zainstalować pliki binarne procesu roboczego pobrane w kroku 1 do wszystkich węzłów procesu roboczego w klastrze HDI.  

3. Przekaż powyższe wymienione pliki na konto usługi Azure Storage, do którego klaster ma dostęp. Więcej informacji można znaleźć w [artykule dotyczącym wdrażania programu .net for Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) .

4. Uruchom `install-worker.sh` skrypt na wszystkich węzłach procesu roboczego klastra przy użyciu akcji skryptu. Aby uzyskać więcej informacji, zapoznaj się z [artykułem dotyczącym wdrażania programu .net for Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) .

### <a name="update-your-application-to-use-specific-version"></a>Aktualizowanie aplikacji do korzystania z określonej wersji

Możesz zaktualizować aplikację .NET for Apache Spark, aby używała określonej wersji, wybierając w projekcie wymaganą wersję [pakietu NuGet Microsoft. Spark](https://www.nuget.org/packages/Microsoft.Spark/) . Zapoznaj się z informacjami o wersji określonej wersji i [przewodnikiem](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) dotyczącym migracji, jak wspomniano powyżej, jeśli chcesz zaktualizować aplikację do wersji v 1.0.0.

## <a name="faqs"></a>Często zadawane pytania

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>Czy istniejący klaster HDI z wersją < 1.0.0 zostanie uruchomiony z nową wersją?

Istniejące klastry HDI nadal będą mieć tę samą poprzednią wersję dla platformy .NET dla Apache Spark i istniejąca aplikacja (z poprzednią wersją platformy Spark .NET) nie będzie miała zastosowania.

## <a name="next-steps"></a>Następne kroki

[Wdrażanie aplikacji .NET dla Apache Spark w usłudze HDInsight](/dotnet/spark/tutorials/hdinsight-deployment)