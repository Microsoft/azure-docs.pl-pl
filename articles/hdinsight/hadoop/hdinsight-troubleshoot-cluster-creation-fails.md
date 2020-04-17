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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453493"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Rozwiązywanie problemów z błędami tworzenia klastra za pomocą usługi Azure HDInsight

Następujące problemy są najczęstszymi przyczynami błędów tworzenia klastra:

- Problemy z uprawnieniami
- Ograniczenia zasad zasobów
- Zapory
- Blokady zasobów
- Nieobsługiwały wersje składników
- Ograniczenia nazwy konta magazynu
- Przerwy w dostawie usług

## <a name="permissions-issues"></a>Problemy z uprawnieniami

Jeśli używasz usługi Azure Data Lake Storage `AmbariClusterCreationFailedErrorCode`Gen2:::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support.":::i otrzymasz błąd: " ", otwórz witrynę Azure portal, przejdź do konta magazynu i w obszarze Kontrola dostępu (IAM), upewnij się, że **współautor danych obiektów blob magazynu** lub rola **właściciela danych obiektu blob magazynu** ma przypisany dostęp do użytkownika **przypisanego tożsamości zarządzanej** dla subskrypcji. Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie uprawnień dla tożsamości zarządzanej na koncie Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account).

Jeśli używasz usługi Azure Data Lake Storage Gen1, zobacz instrukcje konfiguracji i konfiguracji [tutaj](../hdinsight-hadoop-use-data-lake-store.md). Data Lake Storage Gen1 nie jest obsługiwany dla klastrów HBase i nie jest obsługiwany w hdinsight w wersji 4.0.

Jeśli używasz usługi Azure Storage, upewnij się, że nazwa konta magazynu jest prawidłowa podczas tworzenia klastra.

## <a name="resource-policy-restrictions"></a>Ograniczenia zasad zasobów

Zasady platformy Azure oparte na subskrypcji mogą odmówić utworzenia publicznych adresów IP. Do utworzenia klastra usługi HDInsight wymagane są dwa publiczne adresy IP.  

Ogólnie rzecz biorąc, następujące zasady mogą mieć wpływ na tworzenie klastra:

* Zasady uniemożliwiające tworzenie modułów równoważenia obciążenia adresu IP & w ramach subskrypcji.
* Zasady uniemożliwiające tworzenie konta magazynu.
* Zasady zapobiegające usuwaniu zasobów sieciowych (ip address /Load Balancers).

## <a name="firewalls"></a>Zapory

Zapory sieci wirtualnej lub konta magazynu mogą odmówić komunikacji z adresami IP zarządzania hdinsight.

Zezwalaj na ruch z adresów IP w poniższej tabeli.

| Źródłowy adres IP | Element docelowy | Kierunek |
|---|---|---|
| 168.61.49.99 | *:443 | Przychodzący |
| 23.99.5.239 | *:443 | Przychodzący |
| 168.61.48.131 | *:443 | Przychodzący |
| 138.91.141.162 | *:443 | Przychodzący |

Dodaj również adresy IP specyficzne dla regionu, w którym jest tworzony klaster. Zobacz [adresy IP zarządzania hdinsight](../hdinsight-management-ip-addresses.md) dla listy adresów dla każdego regionu platformy Azure.

Jeśli używasz trasy ekspresowej lub własnego niestandardowego serwera DNS, zobacz [Planowanie sieci wirtualnej dla usługi Azure HDInsight — łączenie wielu sieci](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Blokady zasobów  

Upewnij się, że [w sieci wirtualnej i grupie zasobów](../../azure-resource-manager/management/lock-resources.md)nie ma żadnych blokad . Klastrów nie można utworzyć ani usunąć, jeśli grupa zasobów jest zablokowana. 

## <a name="unsupported-component-versions"></a>Nieobsługiwały wersje składników

Upewnij się, że używasz [obsługiwanej wersji usługi Azure HDInsight](../hdinsight-component-versioning.md) i wszelkich [składników Apache Hadoop](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) w rozwiązaniu.  

## <a name="storage-account-name-restrictions"></a>Ograniczenia nazwy konta magazynu

Nazwy kont magazynu nie mogą zawierać więcej niż 24 znaków i nie mogą zawierać znaku specjalnego. Te ograniczenia dotyczą również nazwy domyślnego kontenera w ramach konta magazynu.

Inne ograniczenia nazewnictwa mają również zastosowanie do tworzenia klastra. Aby uzyskać więcej informacji, zobacz [Ograniczenia nazw klastrów.](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name)

## <a name="service-outages"></a>Przerwy w dostawie usług

Sprawdź [stan platformy Azure](https://status.azure.com) pod kątem potencjalnych awarii lub problemów z usługą.

## <a name="next-steps"></a>Następne kroki

* [Rozszerzanie usługi Azure HDInsight przy użyciu sieci wirtualnej platformy Azure](../hdinsight-plan-virtual-network-deployment.md)
* [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Korzystanie z usługi Azure Storage w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych](../hdinsight-hadoop-provision-linux-clusters.md)
