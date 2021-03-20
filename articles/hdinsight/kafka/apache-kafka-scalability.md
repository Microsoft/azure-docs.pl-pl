---
title: Apache Kafka zwiększyć skalę — usługa Azure HDInsight
description: Dowiedz się, jak skonfigurować zarządzane dyski klastra Apache Kafka w usłudze Azure HDInsight w celu zwiększenia skalowalności.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: f22642ae94ea01a798b1eab639c93fda31f87581
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944059"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Konfigurowanie magazynu i skalowalności klastra Apache Kafka w usłudze HDInsight

Dowiedz się, jak skonfigurować liczbę dysków zarządzanych używanych przez [Apache Kafka](https://kafka.apache.org/) w usłudze HDInsight.

Platforma Kafka w usłudze HDInsight używa dysku lokalnego maszyn wirtualnych w klastrze usługi HDInsight. Ze względu na duże obciążenie we/wy platformy Kafka funkcja [Dyski zarządzane platformy Azure](../../virtual-machines/managed-disks-overview.md) jest używana do zapewnienia wysokiej przepływności i zwiększenia miejsca do magazynowania w każdym węźle. Jeśli platforma Kafka korzysta z tradycyjnych wirtualnych dysków twardych (VHD), rozmiar każdego węzła nie przekracza 1 TB. W przypadku dysków zarządzanych można użyć wielu dysków, aby osiągnąć 16 TB pamięci dla każdego węzła w klastrze.

Poniższy diagram przedstawia porównanie platformy Kafka w usłudze HDInsight przed użyciem dysków zarządzanych i platformy Kafka w usłudze HDInsight z dyskami zarządzanymi:

![Kafka z architekturą Managed disks](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Konfigurowanie dysków zarządzanych: witryna Azure Portal

1. Aby zapoznać się z typowymi czynnościami tworzenia klastra przy użyciu witryny, wykonaj kroki opisane w temacie [Tworzenie klastra usługi HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md). Nie Dokończ procesu tworzenia portalu.

2. W sekcji **cennik & konfiguracja** Użyj pola __Liczba węzłów__ , aby skonfigurować liczbę dysków.

    > [!NOTE]  
    > Można wybrać typ dysku zarządzanego __Standardowy__ (HDD) lub __Premium__ (SSD). Dyski w warstwie Premium są używane przez maszyny wirtualne serii DS i GS. Wszystkie pozostałe typy maszyn wirtualnych korzystają z dysków standardowych.

    ![Sekcja rozmiar klastra z wyróżnionymi dyskami dla każdego węzła procesu roboczego](./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Konfigurowanie dysków zarządzanych: szablon usługi Resource Manager

Aby kontrolować liczbę dysków używanych przez węzły procesu roboczego w klastrze Kafka, użyj następującej sekcji szablonu:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Możesz znaleźć kompletny szablon, który pokazuje, jak skonfigurować dyski zarządzane pod adresem [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json) .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z usługą Apache Kafka w usłudze HDInsight, zobacz następujące dokumenty:

* [Tworzenie repliki Apache Kafka w usłudze HDInsight przy użyciu programu narzędzia MirrorMaker](apache-kafka-mirroring.md)
* [Używanie Apache Storm z Apache Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Używanie Apache Spark z Apache Kafka w usłudze HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Nawiązywanie połączenia z usługą Apache Kafka za pomocą usługi Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)

* [Blog usługi HDInsight na dyskach zarządzanych z Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
