---
title: Odporność & wysoka dostępność
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zwiększyć odporność zasobów Azure Machine Learning na awarie przy użyciu konfiguracji o wysokiej dostępności.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/16/2020
ms.openlocfilehash: 7a1a63893e6e2988fc5f21e84f21c74315d856b4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325484"
---
# <a name="increase-azure-machine-learning-resiliency"></a>Zwiększ odporność Azure Machine Learning



W tym artykule dowiesz się, jak zwiększyć odporność Microsoft Azure Machine Learning zasobów przy użyciu konfiguracji o wysokiej dostępności. Można skonfigurować usługi platformy Azure, które Azure Machine Learning zależy od zapewnienia wysokiej dostępności. Ten artykuł zawiera informacje o usługach, które można skonfigurować pod kątem wysokiej dostępności, oraz linki do dodatkowych informacji na temat konfigurowania tych zasobów.

> [!NOTE]
> Sama Azure Machine Learning nie oferuje opcji odzyskiwania po awarii.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Informacje na temat usług platformy Azure dla Azure Machine Learning

Azure Machine Learning zależy od wielu usług platformy Azure i ma kilka warstw. Niektóre z tych usług są obsługiwane w ramach subskrypcji (klienta). Użytkownik jest odpowiedzialny za konfigurację wysokiej dostępności tych usług. Inne usługi są tworzone w ramach subskrypcji firmy Microsoft i zarządzane przez firmę Microsoft. 

Usługi platformy Azure obejmują:

* **Infrastruktura Azure Machine Learning** : środowisko zarządzane przez firmę Microsoft dla obszaru roboczego Azure Machine Learning.

* **Skojarzone zasoby** : zasoby obsługiwane w ramach subskrypcji podczas tworzenia obszaru roboczego Azure Machine Learning. Te zasoby obejmują usługę Azure Storage, Azure Key Vault, Azure Container Registry i Application Insights. Użytkownik jest odpowiedzialny za konfigurowanie ustawień wysokiej dostępności dla tych zasobów.
  * Magazyn domyślny ma takie dane, jak model, dane dziennika szkoleniowego i zestaw danych.
  * Key Vault ma poświadczenia usługi Azure Storage, Container Registry i magazynów danych.
  * Container Registry zawiera obraz platformy Docker dla środowisk szkoleniowych i inferencing.
  * Application Insights jest dla Azure Machine Learning monitorowania.

* **Zasoby obliczeniowe** : zasoby utworzone po wdrożeniu obszaru roboczego. Można na przykład utworzyć wystąpienie obliczeniowe lub klaster obliczeniowy, aby szkolić model Machine Learning.
  * Wystąpienie obliczeniowe i klaster obliczeniowy: środowiska programistyczne modelu zarządzanego przez firmę Microsoft.
  * Inne zasoby: zasoby obliczeniowe firmy Microsoft, które można dołączyć do Azure Machine Learning, takich jak usługa Azure Kubernetes Service (AKS), Azure Databricks, Azure Container Instances i Azure HDInsight. Użytkownik jest odpowiedzialny za konfigurowanie ustawień wysokiej dostępności dla tych zasobów.

* **Dodatkowe magazyny danych** : Azure Machine Learning mogą instalować dodatkowe magazyny danych, takie jak Azure Storage, Azure Data Lake Storage i Azure SQL Database na potrzeby danych szkoleniowych.  Te magazyny danych są obsługiwane w ramach subskrypcji. Użytkownik jest odpowiedzialny za konfigurowanie ustawień wysokiej dostępności.

W poniższej tabeli przedstawiono, które usługi platformy Azure są zarządzane przez firmę Microsoft, które są zarządzane przez użytkownika, a które domyślnie mają wysoką dostępność.

| Usługa | Zarządzane przez | Wysoka dostępność domyślnie |
| ----- | ----- | ----- |
| **Infrastruktura Azure Machine Learning** | Microsoft | |
| **Skojarzone zasoby** |
| Azure Storage | Ty | |
| Key Vault | Ty | ✓ |
| Container Registry | Ty | |
| Application Insights | Ty | Nie dotyczy |
| **Zasoby obliczeniowe** |
| Wystąpienie obliczeniowe | Microsoft |  |
| Klaster obliczeniowy | Microsoft |  |
| Inne zasoby obliczeniowe, takie jak AKS, <br>Azure Databricks, Container Instances, HDInsight | Ty |  |
| **Dodatkowe magazyny danych** , takie jak Azure Storage, SQL Database,<br> Azure Database for PostgreSQL, Azure Database for MySQL, <br>System plików Azure Databricks | Ty | |

Pozostała część tego artykułu zawiera opis działań, które należy wykonać, aby zapewnić wysoką dostępność każdej z tych usług.

## <a name="associated-resources"></a>Skojarzone zasoby

> [!IMPORTANT]
> Azure Machine Learning nie obsługuje domyślnego trybu failover konta magazynu przy użyciu magazynu geograficznie nadmiarowego (GRS), geograficznie nadmiarowego magazynu (GZRS), magazynu geograficznie nadmiarowego do odczytu (RA-GRS) lub geograficznie nadmiarowego magazynu (RA-GZRS).

Upewnij się, że skonfigurowano ustawienia wysokiej dostępności dla każdego zasobu, odwołując się do następującej dokumentacji:

* **Azure Storage** : Aby skonfigurować ustawienia wysokiej dostępności, zobacz [nadmiarowość usługi Azure Storage](../storage/common/storage-redundancy.md).
* **Key Vault** : Key Vault zapewnia wysoką dostępność domyślnie i nie wymaga żadnej akcji użytkownika.  Zobacz [Azure Key Vault dostępność i nadmiarowość](../key-vault/general/disaster-recovery-guidance.md).
* **Container Registry** : wybierz opcję rejestr Premium dla replikacji geograficznej. Zapoznaj [się z replikacją geograficzną w Azure Container Registry](../container-registry/container-registry-geo-replication.md).
* **Application Insights** : Application Insights nie zapewnia ustawień wysokiej dostępności. Aby dostosować okres przechowywania danych i szczegóły, zobacz [zbieranie, przechowywanie i magazynowanie danych w Application Insights](../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Zasoby obliczeniowe

Upewnij się, że skonfigurowano ustawienia wysokiej dostępności dla każdego zasobu, odwołując się do następującej dokumentacji:

* **Usługa Azure Kubernetes** : Zapoznaj [się z najlepszymi rozwiązaniami dotyczącymi ciągłości działania i odzyskiwania po awarii w usłudze Azure KUBERNETES Service (AKS)](../aks/operator-best-practices-multi-region.md) i [Utwórz klaster usługi Azure Kubernetes (AKS), który używa stref dostępności](../aks/availability-zones.md). Jeśli klaster AKS został utworzony przy użyciu Azure Machine Learning Studio, zestawu SDK lub interfejsu wiersza polecenia, wysoka dostępność w różnych regionach nie jest obsługiwana.
* **Azure Databricks** : Zapoznaj się z [regionalnym odzyskiwaniem po awarii dla klastrów Azure Databricks](/azure/databricks/scenarios/howto-regional-disaster-recovery).
* **Container Instances** : Koordynator jest odpowiedzialny za przejście w tryb failover. Zobacz [Azure Container Instances i koordynatorów kontenerów](../container-instances/container-instances-orchestrator-relationship.md).
* **HDInsight** : zobacz [usługi wysokiej dostępności obsługiwane przez usługę Azure HDInsight](../hdinsight/hdinsight-high-availability-components.md).

## <a name="additional-data-stores"></a>Dodatkowe magazyny danych

Upewnij się, że skonfigurowano ustawienia wysokiej dostępności dla każdego zasobu, odwołując się do następującej dokumentacji:

* **Kontener obiektów blob platformy Azure/Azure Files/Data Lake Storage Gen2** : taki sam jak magazyn domyślny.
* **Data Lake Storage Gen1** : zobacz [wskazówki dotyczące wysokiej dostępności i odzyskiwania po awarii dla Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).
* **SQL Database** : zobacz [wysoką dostępność dla Azure SQL Database i wystąpienia zarządzanego SQL](../azure-sql/database/high-availability-sla.md).
* **Azure Database for PostgreSQL** : Zapoznaj [się z pojęciami dotyczącymi wysokiej dostępności na jednym serwerze Azure Database for PostgreSQL](../postgresql/concepts-high-availability.md).
* **Azure Database for MySQL** : zobacz [Opis ciągłości działania w programie Azure Database for MySQL](../mysql/concepts-business-continuity.md).
* **System plików Azure Databricks** : Zapoznaj się z [regionalnym odzyskiwaniem po awarii dla klastrów Azure Databricks](/azure/databricks/scenarios/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Jeśli postanowisz swój własny klucz zarządzany przez klienta w celu wdrożenia obszaru roboczego Azure Machine Learning, Azure Cosmos DB również zostanie zainicjowany w ramach subskrypcji. W takim przypadku użytkownik jest odpowiedzialny za konfigurowanie ustawień wysokiej dostępności. Zobacz [wysoką dostępność dzięki Azure Cosmos DB](../cosmos-db/high-availability.md).

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć Azure Machine Learning ze skojarzonymi zasobami z ustawieniami wysokiej dostępności, użyj [szablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).