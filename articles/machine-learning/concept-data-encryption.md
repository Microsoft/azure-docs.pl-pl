---
title: Szyfrowanie danych za pomocą usługi Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób Azure Machine Learning obliczenia i magazyny danych zapewniają szyfrowanie danych w czasie spoczynku i podczas przesyłania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: 13d5c02fbb4ae06c7a5279ab7c5d3af90c263f71
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521071"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Szyfrowanie danych za pomocą Azure Machine Learning

Azure Machine Learning używa różnych usług magazynu danych platformy Azure i zasobów obliczeniowych podczas szkoleń modeli i wykonywania wnioskowania. Każdy z nich ma własny scenariusz, w jaki zapewniają szyfrowanie danych przechowywanych i podczas przesyłania. W tym artykule zapoznaj się z informacjami o każdej z nich i najlepszym dla tego scenariusza.

> [!IMPORTANT]
> W przypadku szyfrowania klasy produkcyjnej w trakcie __szkolenia__ firma Microsoft zaleca korzystanie z Azure Machine Learning klastra obliczeniowego. W przypadku szyfrowania klasy produkcyjnej podczas __wnioskowania__ firma Microsoft zaleca korzystanie z usługi Azure Kubernetes Service.
>
> Wystąpienie obliczeniowe Azure Machine Learning jest środowiskiem deweloperskim/testowym. W przypadku korzystania z tego programu zalecamy przechowywanie plików, takich jak Notesy i skrypty, w udziale plików. Dane powinny być przechowywane w magazynie danych.

## <a name="encryption-at-rest"></a>Szyfrowanie danych magazynowanych

> [!IMPORTANT]
> Jeśli obszar roboczy zawiera dane poufne, zalecamy ustawienie [flagi hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) podczas tworzenia obszaru roboczego. `hbi_workspace`Flagę można ustawić tylko podczas tworzenia obszaru roboczego. Nie można go zmienić dla istniejącego obszaru roboczego.

`hbi_workspace`Flaga kontroluje ilość [danych zbieranych przez firmę Microsoft do celów diagnostycznych](#microsoft-collected-data) i umożliwia [dodatkowe szyfrowanie w środowiskach zarządzanych przez firmę Microsoft](../security/fundamentals/encryption-atrest.md). Ponadto włącza następujące akcje:

* Program uruchamia szyfrowanie lokalnego dysku magazynującego w klastrze obliczeniowym Azure Machine Learning, pod warunkiem że nie utworzono żadnych wcześniejszych klastrów w tej subskrypcji. W przeciwnym razie musisz zgłosić bilet pomocy technicznej, aby umożliwić szyfrowanie dysku magazynującego klastrów obliczeniowych 
* Oczyszcza lokalny dysk tymczasowy między przebiegami.
* Bezpieczne przekazanie poświadczeń dla konta magazynu, rejestru kontenerów i konta SSH z warstwy wykonywania do klastrów obliczeniowych przy użyciu magazynu kluczy
* Włącza filtrowanie adresów IP, aby upewnić się, że źródłowe pule usługi Batch nie mogą być wywoływane przez żadną zewnętrzną usługę inną niż AzureMachineLearningService
* W obszarze roboczym HBI nie są obsługiwane wystąpienia obliczeniowe.

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning przechowuje migawki, dane wyjściowe i dzienniki na koncie usługi Azure Blob Storage, które jest powiązane z obszarem roboczym Azure Machine Learning i subskrypcją. Wszystkie dane przechowywane w usłudze Azure Blob Storage są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft.

Aby uzyskać informacje na temat korzystania z własnych kluczy dla danych przechowywanych w usłudze Azure Blob Storage, zobacz [szyfrowanie usługi Azure Storage z kluczami zarządzanymi przez klienta w Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md).

Dane szkoleniowe są zazwyczaj przechowywane w usłudze Azure Blob Storage, dzięki czemu są dostępne do uczenia celów obliczeniowych. Ten magazyn nie jest zarządzany przez Azure Machine Learning ale jest instalowany do celów obliczeniowych jako zdalny system plików.

Jeśli musisz __obrócić lub odwołać__ klucz, możesz to zrobić w dowolnym momencie. Podczas obracania klucza konto magazynu rozpocznie się przy użyciu nowego klucza (najnowszej wersji) do szyfrowania danych przechowywanych w spoczynku. Podczas odwoływania (wyłączania) klucza konto magazynu zajmuje się niepowodzeniem żądaniami. Trwa to zwykle godzinę, w której obrót lub odwołanie będzie obowiązywać.

Aby uzyskać informacje na temat ponownego generowania kluczy dostępu, zobacz Ponowne [generowanie kluczy dostępu do magazynu](how-to-change-storage-access-key.md).

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning przechowuje metadane w wystąpieniu Azure Cosmos DB. To wystąpienie jest skojarzone z subskrypcją firmy Microsoft zarządzaną przez Azure Machine Learning. Wszystkie dane przechowywane w Azure Cosmos DB są szyfrowane w stanie spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft.

Aby użyć własnych kluczy (zarządzanych przez klienta) do zaszyfrowania wystąpienia Azure Cosmos DB, można utworzyć dedykowane wystąpienie Cosmos DB do użycia z Twoim obszarem roboczym. Zalecamy to podejście, jeśli chcesz przechowywać dane, takie jak informacje o historii uruchamiania, poza wystąpieniem Cosmos DB wielodostępnego hostowanym w naszej subskrypcji firmy Microsoft. 

Aby włączyć obsługę administracyjną wystąpienia Cosmos DB w ramach subskrypcji z kluczami zarządzanymi przez klienta, wykonaj następujące czynności:

* Zarejestruj dostawców zasobów Microsoft. MachineLearning i Microsoft.DocumentDB w ramach subskrypcji, jeśli jeszcze nie zostało to zrobione.

* Podczas tworzenia obszaru roboczego Azure Machine Learning Użyj następujących parametrów. Oba parametry są obowiązkowe i obsługiwane w zestawach SDK, interfejsu wiersza polecenia, interfejsach API REST i szablonach Menedżer zasobów.

    * `resource_cmk_uri`: Ten parametr to pełny identyfikator URI zasobu klucza zarządzanego przez klienta w magazynie kluczy, w tym [Informacje o wersji klucza](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Ten parametr jest IDENTYFIKATORem zasobu magazynu kluczy w subskrypcji. Ten magazyn kluczy musi znajdować się w tym samym regionie i subskrypcji, który będzie używany dla obszaru roboczego Azure Machine Learning. 
    
        > [!NOTE]
        > To wystąpienie magazynu kluczy może być inne niż magazyn kluczy tworzony przez Azure Machine Learning podczas aprowizacji obszaru roboczego. Jeśli chcesz użyć tego samego wystąpienia magazynu kluczy dla obszaru roboczego, Przekaż ten sam magazyn kluczy podczas aprowizacji obszaru roboczego za pomocą [parametru key_vault](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Jeśli musisz __obrócić lub odwołać__ klucz, możesz to zrobić w dowolnym momencie. Podczas obracania klucza Cosmos DB rozpocznie się przy użyciu nowego klucza (Najnowsza wersja) do szyfrowania danych przechowywanych w spoczynku. W przypadku odwoływania się (wyłączania) klucza Cosmos DB są ważne żądania. Trwa to zwykle godzinę, w której obrót lub odwołanie będzie obowiązywać.

Aby uzyskać więcej informacji na temat kluczy zarządzanych przez klienta Cosmos DB, zobacz [Konfigurowanie kluczy zarządzanych przez klienta dla konta usługi Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

### <a name="azure-container-registry"></a>Azure Container Registry

Wszystkie obrazy kontenerów w rejestrze (Azure Container Registry) są szyfrowane w stanie spoczynku. Platforma Azure automatycznie szyfruje obraz przed jego zapisaniem i odszyfrowuje go, gdy Azure Machine Learning pobiera obraz.

Aby użyć własnych kluczy (zarządzanych przez klienta) do zaszyfrowania Azure Container Registry, należy utworzyć własne ACR i dołączyć je podczas aprowizacji obszaru roboczego lub zaszyfrować domyślne wystąpienie, które jest tworzone w momencie aprowizacji obszaru roboczego.

> [!IMPORTANT]
> Azure Machine Learning wymaga włączenia konta administratora na Azure Container Registry. Domyślnie to ustawienie jest wyłączone podczas tworzenia rejestru kontenerów. Aby uzyskać informacje na temat włączania konta administratora, zobacz [konto administratora](../container-registry/container-registry-authentication.md#admin-account).
>
> Po utworzeniu Azure Container Registry dla obszaru roboczego nie należy go usuwać. Spowoduje to przerwanie obszaru roboczego Azure Machine Learning.

Przykład tworzenia obszaru roboczego przy użyciu istniejącego Azure Container Registry można znaleźć w następujących artykułach:

* [Utwórz obszar roboczy dla Azure Machine Learning przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-manage-workspace-cli.md).
* [Utwórz obszar roboczy z zestawem SDK języka Python](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Użyj szablonu Azure Resource Manager, aby utworzyć obszar roboczy dla Azure Machine Learning](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Wystąpienie kontenera platformy Azure

Wdrożony zasób wystąpienia kontenera platformy Azure (ACI) można zaszyfrować przy użyciu kluczy zarządzanych przez klienta. Klucz zarządzany przez klienta używany przez ACI może być przechowywany w Azure Key Vault dla obszaru roboczego. Aby uzyskać informacje na temat generowania klucza, zobacz [szyfrowanie danych za pomocą klucza zarządzanego przez klienta](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Aby użyć klucza podczas wdrażania modelu w usłudze Azure Container instance, Utwórz nową konfigurację wdrożenia przy użyciu programu `AciWebservice.deploy_configuration()` . Podaj informacje o kluczu przy użyciu następujących parametrów:

* `cmk_vault_base_url`: Adres URL magazynu kluczy, który zawiera klucz.
* `cmk_key_name`: Nazwa klucza.
* `cmk_key_version`: Wersja klucza.

Aby uzyskać więcej informacji na temat tworzenia i używania konfiguracji wdrożenia, zobacz następujące artykuły:

* Informacje o [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Gdzie i jak wdrażać modele](how-to-deploy-and-where.md)
* [Wdrażanie modelu w usłudze Azure Container Instances](how-to-deploy-azure-container-instance.md)

Aby uzyskać więcej informacji na temat używania klucza zarządzanego przez klienta z usługą ACI, zobacz [szyfrowanie danych za pomocą klucza zarządzanego przez klienta](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Wdrożony zasób usługi Azure Kubernetes można zaszyfrować w dowolnym momencie przy użyciu kluczy zarządzanych przez klienta. Aby uzyskać więcej informacji, zobacz temat [przenoszenie własnych kluczy za pomocą usługi Azure Kubernetes Service](../aks/azure-disk-customer-managed-keys.md). 

Ten proces umożliwia zaszyfrowanie zarówno danych, jak i dysku systemu operacyjnego wdrożonych maszyn wirtualnych w klastrze Kubernetes.

> [!IMPORTANT]
> Ten proces działa tylko z AKS K8s w wersji 1,17 lub nowszej. Azure Machine Learning dodano obsługę AKS 1,17 w dniu 13 stycznia 2020.

### <a name="machine-learning-compute"></a>środowisko obliczeniowe usługi Machine Learning

Dysk systemu operacyjnego dla każdego węzła obliczeniowego przechowywanego w usłudze Azure Storage jest szyfrowany przy użyciu kluczy zarządzanych przez firmę Microsoft w ramach kont magazynu Azure Machine Learning. Ten obiekt docelowy obliczeń jest nieulotny, a klastry są zwykle skalowane w dół, gdy żadne przebiegi nie są umieszczane w kolejce. Podstawowa maszyna wirtualna jest nieobsługiwana i dysk systemu operacyjnego zostanie usunięty. Azure Disk Encryption nie jest obsługiwana w przypadku dysku systemu operacyjnego.

Każda maszyna wirtualna ma także lokalny dysk tymczasowy dla operacji systemu operacyjnego. Jeśli chcesz, możesz użyć dysku do przygotowania danych szkoleniowych. Dysk jest domyślnie szyfrowany dla obszarów roboczych z `hbi_workspace` parametrem ustawionym na `TRUE` . To środowisko jest krótko w czasie trwania uruchomienia, a obsługa szyfrowania jest ograniczona tylko do kluczy zarządzanych przez system.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks można używać w potokach Azure Machine Learning. Domyślnie system plików dataDBFSs używany przez Azure Databricks jest szyfrowany przy użyciu klucza zarządzanego przez firmę Microsoft. Aby skonfigurować Azure Databricks do korzystania z kluczy zarządzanych przez klienta, zobacz [Konfigurowanie kluczy zarządzanych przez klienta na domyślnym (głównym) DBFS](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="microsoft-generated-data"></a>Dane generowane przez firmę Microsoft

W przypadku korzystania z usług, takich jak automatyczne Machine Learning, firma Microsoft może generować przejściowe, wstępnie przetworzone dane do szkolenia wielu modeli. Te dane są przechowywane w magazynie danych w obszarze roboczym, co umożliwia odpowiednie wymuszanie kontroli dostępu i szyfrowania.

Możesz również zaszyfrować [informacje diagnostyczne zarejestrowane ze wdrożonego punktu końcowego](how-to-enable-app-insights.md) w wystąpieniu usługi Azure Application Insights.

## <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu

Azure Machine Learning używa protokołu TLS do zabezpieczania komunikacji wewnętrznej między różnymi mikrousługami Azure Machine Learning. Cały dostęp do usługi Azure Storage odbywa się również za pośrednictwem bezpiecznego kanału.

Aby zabezpieczyć wywołania zewnętrzne w punkcie końcowym oceniania, Azure Machine Learning używa protokołu TLS. Aby uzyskać więcej informacji, zobacz [Korzystanie z protokołu TLS w celu zabezpieczenia usługi sieci Web za pomocą Azure Machine Learning](./how-to-secure-web-service.md).

## <a name="data-collection-and-handling"></a>Zbieranie i obsługa danych

### <a name="microsoft-collected-data"></a>Zebrane dane firmy Microsoft

Firma Microsoft może zbierać informacje o tożsamościach nienależących do użytkownika, takie jak nazwy zasobów (np. Nazwa zestawu danych lub nazwa eksperymentu usługi Machine Learning) lub zmienne środowiskowe zadań dla celów diagnostycznych. Wszystkie takie dane są przechowywane przy użyciu kluczy zarządzanych przez firmę Microsoft w magazynie hostowanym w subskrypcjach firmy Microsoft i zgodne ze [standardowymi zasadami ochrony prywatności firmy Microsoft i standardami obsługi danych](https://privacy.microsoft.com/privacystatement).

Firma Microsoft zaleca również, aby nie przechowywać poufnych informacji (takich jak wpisy tajne klucza konta) w zmiennych środowiskowych. Zmienne środowiskowe są rejestrowane, szyfrowane i przechowywane przez nas. Podobnie podczas nazewnictwa [run_id](/python/api/azureml-core/azureml.core.run%28class%29)należy unikać uwzględniania poufnych informacji, takich jak nazwy użytkowników lub nazwy projektów tajnych. Te informacje mogą pojawić się w dziennikach telemetrii dostępnych dla pomoc techniczna firmy Microsoft inżynierów.

Możesz zrezygnować z zbierania danych diagnostycznych, ustawiając `hbi_workspace` parametr na `TRUE` czas inicjowania obsługi obszaru roboczego. Ta funkcja jest obsługiwana w przypadku korzystania z zestawu SDK środowiska Azure Python, interfejsu wiersza polecenia, interfejsów API REST lub Azure Resource Manager szablonów.

## <a name="using-azure-key-vault"></a>Używanie Azure Key Vault

Azure Machine Learning używa wystąpienia Azure Key Vault skojarzonego z obszarem roboczym do przechowywania poświadczeń różnych rodzajów:

* Powiązane parametry połączenia konta magazynu
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Parametry połączenia do magazynów danych

Hasła i klucze SSH do obliczeń docelowych, takich jak usługa Azure HDInsight i maszyny wirtualne, są przechowywane w osobnym magazynie kluczy skojarzonym z subskrypcją firmy Microsoft. W Azure Machine Learning nie są przechowywane żadne hasła ani klucze udostępniane przez użytkowników. Zamiast tego generuje, autoryzuje i przechowuje własne klucze SSH do łączenia się z maszynami wirtualnymi i HDInsight w celu uruchamiania eksperymentów.

Każdy obszar roboczy ma skojarzoną w systemie tożsamość zarządzaną, która ma taką samą nazwę jak obszar roboczy. Ta tożsamość zarządzana ma dostęp do wszystkich kluczy, wpisów tajnych i certyfikatów w magazynie kluczy.

## <a name="next-steps"></a>Następne kroki

* [Łączenie z usługą Azure Storage](how-to-access-data.md)
* [Pobieranie danych z magazynu danych](how-to-create-register-datasets.md)
* [Łączenie z danymi](how-to-connect-data-ui.md)
* [Szkolenie przy użyciu zestawów danych](how-to-train-with-datasets.md)