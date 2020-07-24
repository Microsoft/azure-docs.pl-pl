---
title: Jak zwiększyć odporność
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zwiększyć odporność Azure Machine Learning powiązanych zasobów na awarie przy użyciu konfiguracji o wysokiej dostępności.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098906"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>Zwiększ odporność Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak zwiększyć odporność Azure Machine Learning powiązanych zasobów przy użyciu konfiguracji o wysokiej dostępności. Usługi platformy Azure, od których zależy Azure Machine Learning, można skonfigurować pod kątem wysokiej dostępności. Ten artykuł zawiera informacje dotyczące usług, które można skonfigurować pod kątem wysokiej dostępności, oraz linki do informacji na temat konfigurowania tych zasobów.

> [!NOTE]
> Sama Azure Machine Learning nie oferuje opcji odzyskiwania po awarii.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Informacje na temat usług platformy Azure dla Azure Machine Learning

Azure Machine Learning zależy od wielu usług platformy Azure i ma kilka warstw. Niektóre z nich są obsługiwane w ramach subskrypcji (klienta). Użytkownik jest odpowiedzialny za konfigurację wysokiej dostępności tych usług. Niektóre z nich są tworzone w ramach subskrypcji firmy Microsoft i są zarządzane przez firmę Microsoft.

* **Infrastruktura Azure Machine Learning**: środowisko zarządzane przez firmę Microsoft dla Azure Machine Learning obszaru roboczego.

* **Skojarzone zasoby**: zasoby obsługiwane w ramach subskrypcji podczas tworzenia obszaru roboczego Azure Machine Learning. Obejmują one usługę Azure Storage, Azure Key Vault, Azure Container Registry (ACR) i szczegółowe informacje o aplikacji. Użytkownik jest odpowiedzialny za ustawienia wysokiej dostępności dla tych zasobów.
  * Magazyn domyślny ma takie dane, jak model, dane dziennika szkoleniowego i zestaw danych.
  * Key Vault ma poświadczenia usługi Storage, ACR i magazynów danych.
  * ACR zawiera obraz platformy Docker na potrzeby szkolenia i środowiska inferencing.
  * Usługi App Insights są przeznaczone do monitorowania Azure Machine Learning.

* **Zasoby obliczeniowe**: zasoby utworzone po wdrożeniu obszaru roboczego. Można na przykład utworzyć wystąpienie obliczeniowe lub klaster obliczeniowy, aby szkolić model uczenia maszynowego.
  * Wystąpienie obliczeniowe i klaster obliczeniowy: środowisko programistyczne modelu zarządzanego przez firmę Microsoft.
  * Inne zasoby: to zasoby obliczeniowe mogą być dołączane do Azure Machine Learning, takich jak usługa Azure Kubernetes Service (AKS), Azure Databricks, Azure Container Instances (ACI) i HDInsight. Użytkownik jest odpowiedzialny za ustawienie wysokiej dostępności.

* **Dodatkowe magazyny danych**: Azure Machine Learning mogą instalować dodatkowe magazyny danych, takie jak Azure Storage, Azure Data Lake Storage i Azure SQL Database na potrzeby danych szkoleniowych.  Są one w ramach subskrypcji i są odpowiedzialne za ustawienie wysokiej dostępności.

W poniższej tabeli przedstawiono, które usługi są zarządzane przez firmę Microsoft, które są zarządzane przez użytkownika, a które domyślnie mają wysoką dostępność:

| Usługa | Zarządzane przez | HA — domyślnie |
| ----- | ----- | ----- |
| **Infrastruktura Azure Machine Learning** | Microsoft | |
| **Skojarzone zasoby** |
| Azure Storage | Ty | |
| Azure Key Vault | Ty | ✓ |
| Azure Container Registry | Ty | |
| Application Insights | Ty | Nie dotyczy |
| **Zasoby obliczeniowe** |
| Wystąpienie obliczeniowe | Microsoft |  |
| Klaster obliczeniowy | Microsoft |  |
| Inne zasoby, takie jak usługa Azure Kubernetes, <br>Azure Databricks, wystąpienie kontenera platformy Azure, usługa Azure HDInsight | Ty |  |
| **Dodatkowe magazyny danych** , takie jak Azure Storage, Azure SQL Database,<br> Azure Database for PostgreSQL, Azure Database for MySQL, <br>System plików Azure Databricks | Ty | |

W pozostałej części tego dokumentu znajdziesz informacje o działaniach, które należy wykonać, aby zapewnić wysoką dostępność każdej z tych usług.

## <a name="associated-resources"></a>Skojarzone zasoby

> [!IMPORTANT]
> Azure Machine Learning nie obsługuje domyślnego trybu failover konta magazynu przy użyciu magazynu geograficznie nadmiarowego (GRS) lub strefy geograficznej nadmiarowej (GZRS) lub magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) lub strefy geograficznej dostępu do odczytu (RA-GZRS).

Upewnij się, że ustawienia wysokiej dostępności każdego zasobu mają poniższe informacje.

* **Azure Storage**: Aby skonfigurować ustawienie wysokiej dostępności, zobacz [nadmiarowość usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* **Azure Key Vault**: zapewnia domyślną usługę wysokiej dostępności i nie jest wymagana żadna akcja użytkownika.  Zobacz [Azure Key Vault dostępność i nadmiarowość](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).
* **Azure Container Registry**: Wybierz jednostkę SKU Premium na potrzeby replikacji geograficznej. Zapoznaj [się z replikacją geograficzną w Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Application Insights**: nie zapewnia ustawienia wysokiej dostępności. Można dostosować okres przechowywania danych oraz szczegóły dotyczące [zbierania i przechowywania danych oraz przechowywania ich w Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Zasoby obliczeniowe

Upewnij się, że ustawienia wysokiej dostępności każdego zasobu są opisane poniżej.

* **Usługa Azure Kubernetes**: Zapoznaj [się z najlepszymi rozwiązaniami dotyczącymi ciągłości działania i odzyskiwania po awarii w usłudze Azure KUBERNETES Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) i [Utwórz klaster usługi Azure Kubernetes (AKS), który używa stref dostępności](https://docs.microsoft.com/azure/aks/availability-zones). Jeśli klaster AKS został utworzony przez Azure Machine Learning (przy użyciu programu Studio, zestawu SDK lub interfejsu wiersza polecenia ML), wysoka dostępność w różnych regionach nie jest obsługiwana.
* **Azure Databricks**: Zapoznaj się z [regionalnym odzyskiwaniem po awarii dla klastrów Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Wystąpienie kontenera platformy Azure**: ACI Orchestrator jest odpowiedzialny za przejście w tryb failover. Zobacz [Azure Container Instances i koordynatorów kontenerów](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship).
* **Azure HDInsight**: zobacz [usługi wysokiej dostępności obsługiwane przez usługę Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>Dodatkowe magazyny danych

Upewnij się, że ustawienia wysokiej dostępności każdego zasobu są opisane poniżej.

* **Kontener obiektów blob platformy Azure/udział plików platformy Azure/Azure Data Lake Gen2**: taki sam jak magazyn domyślny.
* **Azure Data Lake Gen1**: zobacz[wskazówki dotyczące odzyskiwania po awarii dla danych w Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **Azure SQL Database**: zobacz [wysoką dostępność i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **Azure Database for PostgreSQL**: Zapoznaj [się z pojęciami dotyczącymi wysokiej dostępności na jednym serwerze Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **Azure Database for MySQL**: zobacz [Opis ciągłości działania w programie Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-business-continuity).
* **System plików datakostki**: zobacz [regionalne odzyskiwanie po awarii dla klastrów Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Jeśli postanowisz swój własny klucz (klucz zarządzany przez klienta) w celu wdrożenia obszaru roboczego Azure Machine Learning, Cosmos DB również zostanie zainicjowany w ramach subskrypcji. W takim przypadku użytkownik jest odpowiedzialny za wysoką dostępność. Zobacz [wysoką dostępność dzięki Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability)

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć Azure Machine Learning ze skojarzonymi zasobami przy użyciu ustawień wysokiej dostępności, użyj [szablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).