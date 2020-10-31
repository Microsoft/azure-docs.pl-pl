---
title: 'Azure HDInsight: przykłady dla platformy .NET'
description: Przykłady języka C# .NET w witrynie GitHub dla typowych zadań przy użyciu zestawu HDInsight SDK dla platformy .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 12/06/2019
ms.openlocfilehash: c748c7c1887f46df6f2a49c0d92cfc91a94a90c0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080417"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight: przykłady dla platformy .NET

> [!div class="op_single_selector"]
> * [Przykłady dla platformy .NET](hdinsight-sdk-dotnet-samples.md)
> * [Przykłady języka Python](hdinsight-sdk-python-samples.md)
> * [Przykłady kodu Java](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

Ten artykuł zawiera:

* Linki do przykładów dla zadań tworzenia klastra.
* Linki do zawartości referencyjnej dla innych zadań zarządzania.

Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): w ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które można przeznaczyć na płatne usługi platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisite"></a>Wymaganie wstępne

[Zestaw SDK usługi Azure HDInsight dla platformy .NET](/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Zarządzanie klastrem — tworzenie

* [Tworzenie klastra platformy Kafka](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Tworzenie klastra Spark](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Tworzenie klastra Spark przy użyciu Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Tworzenie klastra Spark z pakiet Enterprise Security (ESP)](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

Te przykłady dla platformy .NET można uzyskać, klonowanie repozytorium usługi [HDInsight-dotnet-SDK-Samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) w witrynie GitHub.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Fragmenty kodu dla tej dodatkowej funkcji zestawu SDK znajdują się w [dokumentacji referencyjnej usługi HDINSIGHT SDK dla platformy .NET](/dotnet/api/overview/azure/hdinsight).
