---
title: Rozwiązywanie problemów z błędami tworzenia klastra za pomocą usługi Azure HDInsight
description: Dowiedz się, jak rozwiązywać problemy z tworzeniem klastra Apache dla usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: b8be230044d868cc3ec03f6dc3fc2d21e102f121
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856300"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Rozwiązywanie problemów z błędami tworzenia klastra za pomocą usługi Azure HDInsight

Następujące problemy są Najczęstszymi przyczynami głównych niepowodzeń tworzenia klastra:

- Problemy z uprawnieniami
- Ograniczenia zasad zasobów
- Zapory
- Blokady zasobów
- Nieobsługiwane wersje składników
- Ograniczenia nazw kont magazynu
- Awaria usługi

## <a name="permissions-issues"></a>Problemy z uprawnieniami

Jeśli używasz Azure Data Lake Storage Gen2 i otrzymasz komunikat o błędzie `AmbariClusterCreationFailedErrorCode` : " :::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support."::: ", Otwórz Azure Portal, przejdź do konta magazynu i w obszarze Access Control (IAM), upewnij się, że **współautor danych obiektów blob magazynu** lub rola **właściciela danych obiektu blob magazynu** ma przypisany dostęp do **tożsamości zarządzanej przypisanej do użytkownika** dla subskrypcji. Aby uzyskać szczegółowe instrukcje [, zobacz Konfigurowanie uprawnień dla tożsamości zarządzanej na Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2) .

Jeśli używasz Azure Data Lake Storage Gen1, zobacz instrukcje dotyczące instalacji i konfiguracji [używają Azure Data Lake Storage Gen1 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md). Data Lake Storage Gen1 nie jest obsługiwana w przypadku klastrów HBase i nie jest obsługiwana w usłudze HDInsight w wersji 4,0.

W przypadku korzystania z usługi Azure Storage upewnij się, że nazwa konta magazynu jest prawidłowa podczas tworzenia klastra.

## <a name="resource-policy-restrictions"></a>Ograniczenia zasad zasobów

Zasady platformy Azure oparte na subskrypcjach mogą odmówić tworzenia publicznych adresów IP. Do utworzenia klastra usługi HDInsight wymagane są dwa publiczne adresy IP.  

Ogólnie rzecz biorąc, następujące zasady mogą mieć wpływ na tworzenie klastra:

* Zasady uniemożliwiające tworzenie adresów IP & modułów równoważenia obciążenia w ramach subskrypcji.
* Zasady uniemożliwiające utworzenie konta magazynu.
* Zasady uniemożliwiają usuwanie zasobów sieciowych (usługi równoważenia adresów IP/Load).

## <a name="firewalls"></a>Zapory

Zapory w sieci wirtualnej lub koncie magazynu mogą odmówić komunikacji z adresami IP zarządzania usługą HDInsight.

Zezwalaj na ruch z adresów IP w poniższej tabeli.

| Źródłowy adres IP | Element docelowy | Kierunek |
|---|---|---|
| 168.61.49.99 | *: 443 | Inbound |
| 23.99.5.239 | *: 443 | Inbound |
| 168.61.48.131 | *: 443 | Inbound |
| 138.91.141.162 | *: 443 | Inbound |

Należy również dodać adresy IP specyficzne dla regionu, w którym jest tworzony klaster. Zobacz [adresy IP zarządzania usługą HDInsight](../hdinsight-management-ip-addresses.md) , aby uzyskać listę adresów dla każdego regionu świadczenia usługi Azure.

Jeśli używasz trasy Express lub własnego niestandardowego serwera DNS, zobacz [Planowanie sieci wirtualnej dla usługi Azure HDInsight — łączenie wielu sieci](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Blokady zasobów  

Upewnij się, że [w sieci wirtualnej i grupie zasobów](../../azure-resource-manager/management/lock-resources.md)nie ma blokad. Nie można utworzyć lub usunąć klastrów, jeśli grupa zasobów jest zablokowana. 

## <a name="unsupported-component-versions"></a>Nieobsługiwane wersje składników

Upewnij się, że używasz [obsługiwanej wersji usługi Azure HDInsight](../hdinsight-component-versioning.md) i wszystkich [składników Apache Hadoop](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions) w rozwiązaniu.  

## <a name="storage-account-name-restrictions"></a>Ograniczenia nazw kont magazynu

Nazwy kont magazynu nie mogą być dłuższe niż 24 znaki i nie mogą zawierać znaku specjalnego. Te ograniczenia dotyczą również nazwy domyślnego kontenera w ramach konta magazynu.

Inne ograniczenia nazewnictwa stosują się również do tworzenia klastra. Aby uzyskać więcej informacji, zobacz [ograniczenia nazw klastrów](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name).

## <a name="service-outages"></a>Awaria usługi

Sprawdź [Stan platformy Azure](https://status.azure.com) , aby uzyskać potencjalne problemy związane z awarią lub usługą.

## <a name="next-steps"></a>Następne kroki

* [Rozszerzanie usługi Azure HDInsight za pomocą usługi Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Korzystanie z usługi Azure Storage w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych](../hdinsight-hadoop-provision-linux-clusters.md)
