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
ms.openlocfilehash: 3af7515995a305f41fb9b9f85deb9107de51c622
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453493"
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

Jeśli używasz Azure Data Lake Storage Gen2 i otrzymasz `AmbariClusterCreationFailedErrorCode`komunikat o błędzie: ":::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support.":::", Otwórz Azure Portal, przejdź do konta magazynu i w obszarze Access Control (IAM), upewnij się, że **współautor danych obiektów blob magazynu** lub rola **właściciela danych obiektu blob magazynu** ma przypisany dostęp do **tożsamości zarządzanej przypisanej do użytkownika** dla subskrypcji. Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie uprawnień dla tożsamości zarządzanej na koncie Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account).

Jeśli używasz Azure Data Lake Storage Gen1, zobacz instrukcje dotyczące instalacji i konfiguracji w [tym miejscu](../hdinsight-hadoop-use-data-lake-store.md). Data Lake Storage Gen1 nie jest obsługiwana w przypadku klastrów HBase i nie jest obsługiwana w usłudze HDInsight w wersji 4,0.

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
| 168.61.49.99 | *: 443 | Przychodzący |
| 23.99.5.239 | *: 443 | Przychodzący |
| 168.61.48.131 | *: 443 | Przychodzący |
| 138.91.141.162 | *: 443 | Przychodzący |

Należy również dodać adresy IP specyficzne dla regionu, w którym jest tworzony klaster. Zobacz [adresy IP zarządzania usługą HDInsight](../hdinsight-management-ip-addresses.md) , aby uzyskać listę adresów dla każdego regionu świadczenia usługi Azure.

Jeśli używasz trasy Express lub własnego niestandardowego serwera DNS, zobacz [Planowanie sieci wirtualnej dla usługi Azure HDInsight — łączenie wielu sieci](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Blokady zasobów  

Upewnij się, że [w sieci wirtualnej i grupie zasobów](../../azure-resource-manager/management/lock-resources.md)nie ma blokad. Nie można utworzyć lub usunąć klastrów, jeśli grupa zasobów jest zablokowana. 

## <a name="unsupported-component-versions"></a>Nieobsługiwane wersje składników

Upewnij się, że używasz [obsługiwanej wersji usługi Azure HDInsight](../hdinsight-component-versioning.md) i wszystkich [składników Apache Hadoop](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) w rozwiązaniu.  

## <a name="storage-account-name-restrictions"></a>Ograniczenia nazw kont magazynu

Nazwy kont magazynu nie mogą być dłuższe niż 24 znaki i nie mogą zawierać znaku specjalnego. Te ograniczenia dotyczą również nazwy domyślnego kontenera w ramach konta magazynu.

Inne ograniczenia nazewnictwa stosują się również do tworzenia klastra. Aby uzyskać więcej informacji, zobacz [ograniczenia nazw klastrów](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name).

## <a name="service-outages"></a>Awaria usługi

Sprawdź [Stan platformy Azure](https://status.azure.com) , aby uzyskać potencjalne problemy związane z awarią lub usługą.

## <a name="next-steps"></a>Następne kroki

* [Poszerzanie usługi Azure HDInsight przy użyciu usługi Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)
* [Używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Korzystanie z usługi Azure Storage w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych](../hdinsight-hadoop-provision-linux-clusters.md)
