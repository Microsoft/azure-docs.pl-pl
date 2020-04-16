---
title: Zabezpieczenia przedsiębiorstwa
titleSuffix: Azure Machine Learning
description: 'Bezpiecznie korzystaj z usługi Azure Machine Learning: uwierzytelnianie, autoryzacja, bezpieczeństwo sieci, szyfrowanie danych i monitorowanie.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 9f3a1c3455aadfbd243cdc6ab2920849c8558841
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414626"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Zabezpieczenia przedsiębiorstwa dla usługi Azure Machine Learning

W tym artykule dowiesz się o funkcjach zabezpieczeń dostępnych dla usługi Azure Machine Learning.

Podczas korzystania z usługi w chmurze najlepszym rozwiązaniem jest ograniczenie dostępu tylko do użytkowników, którzy jej potrzebują. Zacznij od zrozumienia modelu uwierzytelniania i autoryzacji używanego przez usługę. Można również ograniczyć dostęp do sieci lub bezpiecznie dołączyć zasoby w sieci lokalnej z chmurą. Szyfrowanie danych jest również niezbędne, zarówno w spoczynku, jak i podczas przenoszenia danych między usługami. Na koniec musisz mieć możliwość monitorowania usługi i tworzenia dziennika inspekcji wszystkich działań.

> [!NOTE]
> Informacje zawarte w tym artykule działa z azure machine learning Python SDK w wersji 1.0.83.1 lub nowszej.

## <a name="authentication"></a>Authentication

Uwierzytelnianie wieloskładnikowe jest obsługiwane, jeśli usługa Azure Active Directory (Azure AD) jest skonfigurowana do używania. Oto proces uwierzytelniania:

1. Klient loguje się do usługi Azure AD i pobiera token usługi Azure Resource Manager.  Użytkownicy i podmioty usługi są w pełni obsługiwane.
1. Klient przedstawia token do usługi Azure Resource Manager i do wszystkich usługi Azure Machine Learning.
1. Usługa uczenia maszynowego udostępnia token usługi uczenia maszynowego do obiektu docelowego obliczeń użytkownika (na przykład machine learning compute). Ten token jest używany przez obiekt docelowy obliczeń użytkownika do wywołania z powrotem do usługi uczenia maszynowego po zakończeniu uruchamiania. Zakres jest ograniczony do obszaru roboczego.

[![Uwierzytelnianie w usłudze Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania dla zasobów i przepływów pracy usługi Azure Machine Learning](how-to-setup-authentication.md). Ten artykuł zawiera informacje i przykłady dotyczące uwierzytelniania, w tym przy użyciu podmiotów usługi i zautomatyzowanych przepływów pracy.

### <a name="authentication-for-web-service-deployment"></a>Uwierzytelnianie dla wdrażania usługi sieci Web

Usługa Azure Machine Learning obsługuje dwie formy uwierzytelniania dla usług sieci web: klucz i token. Każda usługa sieci web może włączyć tylko jedną formę uwierzytelniania naraz.

|Metoda uwierzytelniania|Opis|Azure Container Instances|AKS|
|---|---|---|---|
|Klucz|Klawisze są statyczne i nie trzeba ich odświeżać. Klucze mogą być regenerowane ręcznie.|Domyślnie wyłączone| Domyślnie włączone|
|Token|Tokeny wygasają po upływie określonego czasu i muszą zostać odświeżone.| Niedostępne| Domyślnie wyłączone |

Przykłady kodu można znaleźć w [sekcji uwierzytelniania usługi sieci Web](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autoryzacja

Można utworzyć wiele obszarów roboczych, a każdy obszar roboczy może być współużytkowane przez wiele osób. Podczas udostępniania obszaru roboczego można kontrolować dostęp do niego, przypisując te role użytkownikom:

* Właściciel
* Współautor
* Czytelnik

W poniższej tabeli wymieniono niektóre główne operacje usługi Azure Machine Learning i role, które można wykonać:

| Operacja usługi Azure Machine Learning | Właściciel | Współautor | Czytelnik |
| ---- |:----:|:----:|:----:|
| Tworzenie obszaru roboczego | ✓ | ✓ | |
| Udostępnianie obszaru roboczego | ✓ | |  |
| Uaktualnianie obszaru roboczego do wersji Enterprise | ✓ | |
| Tworzenie celu obliczeniowego | ✓ | ✓ | |
| Dołączanie obiektu docelowego obliczeń | ✓ | ✓ | |
| Dołączanie magazynów danych | ✓ | ✓ | |
| Uruchom eksperyment | ✓ | ✓ | |
| Wyświetlanie przebiegów/metryk | ✓ | ✓ | ✓ |
| Rejestrowanie modelu | ✓ | ✓ | |
| Tworzenie obrazu | ✓ | ✓ | |
| Wdrażanie usługi sieci web | ✓ | ✓ | |
| Wyświetlanie modeli/obrazów | ✓ | ✓ | ✓ |
| Zadzwoń do serwisu internetowego | ✓ | ✓ | ✓ |

Jeśli wbudowane role nie spełniają Twoich potrzeb, możesz utworzyć role niestandardowe. Role niestandardowe są obsługiwane tylko dla operacji w obszarze roboczym i obliczeniach uczenia maszynowego. Role niestandardowe mogą mieć uprawnienia do odczytu, zapisu lub usuwania w obszarze roboczym i zasobie obliczeniowym w tym obszarze roboczym. Można udostępnić rolę na określonym poziomie obszaru roboczego, na poziomie określonej grupy zasobów lub na określonym poziomie subskrypcji. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami i rolami w obszarze roboczym usługi Azure Machine Learning](how-to-assign-roles.md).

> [!WARNING]
> Usługa Azure Machine Learning nie jest obecnie obsługiwana w ramach współpracy między firmami usługi Azure Active Directory.

### <a name="securing-compute-targets-and-data"></a>Zabezpieczanie celów obliczeniowych i danych

Właściciele i współautorzy mogą używać wszystkich obiektów docelowych i magazynów danych obliczeń, które są dołączone do obszaru roboczego.  

Każdy obszar roboczy ma również skojarzoną tożsamość zarządzaną przypisaną do systemu, która ma taką samą nazwę jak obszar roboczy. Tożsamość zarządzana ma następujące uprawnienia do dołączonych zasobów używanych w obszarze roboczym.

Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Zasób | Uprawnienia |
| ----- | ----- |
| Workspace | Współautor |
| Konto magazynu | Współautor danych obiektów blob magazynu |
| Magazyn kluczy | Dostęp do wszystkich kluczy, wpisów tajnych, certyfikatów |
| Azure Container Registry | Współautor |
| Grupa zasobów zawierająca obszar roboczy | Współautor |
| Grupa zasobów zawierająca magazyn kluczy (jeśli różni się od tej, która zawiera obszar roboczy) | Współautor |

Nie zaleca się, aby administratorzy odwołali dostęp do tożsamości zarządzanej do zasobów wymienionych w powyższej tabeli. Dostęp można przywrócić za pomocą operacji kluczy ponownej synchronizi.

Usługa Azure Machine Learning tworzy dodatkową aplikację `aml-` `Microsoft-AzureML-Support-App-`(nazwa zaczyna się od lub) z dostępem na poziomie współautora w subskrypcji dla każdego regionu obszaru roboczego. Na przykład jeśli masz jeden obszar roboczy we wschodnich stanach USA i jeden w Europie Północnej w tej samej subskrypcji, zobaczysz dwie z tych aplikacji. Te aplikacje umożliwiają usługi Azure Machine Learning, aby ułatwić zarządzanie zasobami obliczeniowymi.

## <a name="network-security"></a>Bezpieczeństwo sieci

Usługa Azure Machine Learning opiera się na innych usługach platformy Azure dla zasobów obliczeniowych. Zasoby obliczeniowe (obiekty docelowe obliczeń) są używane do szkolenia i wdrażania modeli. Te obiekty docelowe obliczeń można utworzyć w sieci wirtualnej. Na przykład można użyć maszyny wirtualnej usługi Azure Data Science do uczenia modelu, a następnie wdrożyć model do usługi AKS.  

Aby uzyskać więcej informacji, zobacz [Jak przeprowadzić eksperymenty i wnioskować w sieci wirtualnej](how-to-enable-virtual-network.md).

Można również włączyć azure private link dla obszaru roboczego. Łącze prywatne umożliwia ograniczenie komunikacji do obszaru roboczego z sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować łącze prywatne](how-to-configure-private-link.md).

> [!TIP]
> Można połączyć sieć wirtualną i łącze prywatne, aby chronić komunikację między obszarem roboczym a innymi zasobami platformy Azure. Jednak niektóre kombinacje wymagają obszaru roboczego w wersji Enterprise. Poniższa tabela służy do zrozumienia, jakie scenariusze wymagają wersji Enterprise:
>
> | Scenariusz | Enterprise</br>Edition | Podstawowy</br>Edition |
> | ----- |:-----:|:-----:| 
> | Brak sieci wirtualnej lub łącza prywatnego | ✔ | ✔ |
> | Obszar roboczy bez łącza prywatnego. Inne zasoby (z wyjątkiem usługi Azure Container Registry) w sieci wirtualnej | ✔ | ✔ |
> | Obszar roboczy bez łącza prywatnego. Inne zasoby z linkiem prywatnym | ✔ | |
> | Obszar roboczy z łączem prywatnym. Inne zasoby (z wyjątkiem usługi Azure Container Registry) w sieci wirtualnej | ✔ | ✔ |
> | Obszar roboczy i inne zasoby z łączem prywatnym | ✔ | |
> | Obszar roboczy z łączem prywatnym. Inne zasoby bez private linku lub sieci wirtualnej | ✔ | ✔ |
> | Usługa Azure Container Registry w sieci wirtualnej | ✔ | |
> | Klucze zarządzane przez klienta dla obszaru roboczego | ✔ | |
> 

> [!WARNING]
> Podgląd wystąpień obliczeniowych usługi Azure Machine Learning nie jest obsługiwany w obszarze roboczym, w którym jest włączone łącze prywatne.
> 
> Usługa Azure Machine Learning nie obsługuje korzystania z usługi Azure Kubernetes, która ma włączone łącze prywatne. Zamiast tego można użyć usługi Azure Kubernetes w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Bezpieczne zadania eksperymentowania i wnioskowania usługi Azure ML w ramach sieci wirtualnej platformy Azure.](how-to-enable-virtual-network.md)

## <a name="data-encryption"></a>Szyfrowanie danych

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

> [!IMPORTANT]
> Jeśli obszar roboczy zawiera poufne dane, zaleca się ustawienie [flagi hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) podczas tworzenia obszaru roboczego. 

Flaga `hbi_workspace` steruje ilością danych zbieranych przez firmę Microsoft do celów diagnostycznych i umożliwia dodatkowe szyfrowanie w środowiskach zarządzanych firmy Microsoft. Ponadto umożliwia następujące czynności:

* Rozpoczyna szyfrowanie lokalnego dysku magazynującego w klastrze Amlcompute, pod warunkiem że nie utworzono żadnych poprzednich klastrów w tej subskrypcji. W przeciwnym razie musisz podnieść bilet pomocy technicznej, aby włączyć szyfrowanie dysku odchrapania klastrów obliczeniowych 
* Czyści lokalny dysk odchrapania między przebiegami
* Bezpiecznie przekazuje poświadczenia dla konta magazynu, rejestru kontenerów i konta SSH z warstwy wykonania do klastrów obliczeniowych przy użyciu magazynu kluczy
* Umożliwia filtrowanie adresów IP w celu zapewnienia, że podstawowe pule partii nie mogą być wywoływane przez żadne usługi zewnętrzne inne niż AzureMachineLearningService


Aby uzyskać więcej informacji na temat działania szyfrowania w spoczynku na platformie Azure, zobacz [Szyfrowanie danych platformy Azure w spoczynku.](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Usługa Azure Machine Learning przechowuje migawki, dane wyjściowe i dzienniki na koncie magazynu obiektów Blob platformy Azure powiązanym z obszarem roboczym usługi Azure Machine Learning i subskrypcją. Wszystkie dane przechowywane w magazynie obiektów Blob platformy Azure są szyfrowane w spoczynku za pomocą kluczy zarządzanych przez firmę Microsoft.

Aby uzyskać informacje na temat używania własnych kluczy do danych przechowywanych w magazynie obiektów Blob platformy Azure, zobacz [Szyfrowanie usługi Azure Storage z kluczami zarządzanymi przez klienta w usłudze Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

Dane szkoleniowe są zazwyczaj również przechowywane w magazynie obiektów Blob platformy Azure, dzięki czemu są dostępne dla docelowych obliczeń szkoleniowych. Ten magazyn nie jest zarządzany przez usługę Azure Machine Learning, ale jest instalowany do obliczania obiektów docelowych jako zdalnego systemu plików.

Jeśli chcesz __obrócić lub odwołać__ klucz, możesz to zrobić w dowolnym momencie. Podczas obracania klucza konto magazynu rozpocznie używanie nowego klucza (najnowszej wersji) do szyfrowania danych w spoczynku. Podczas odwoływania (wyłączania) klucza, konto magazynu zajmuje się żądaniami w przypadku awarii. Zwykle trwa godzinę dla obrotu lub odwołania być skuteczne.

Aby uzyskać informacje na temat ponownego generowania kluczy dostępu, zobacz [Ponowne generowanie kluczy dostępu do magazynu](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Usługa Azure Machine Learning przechowuje metryki i metadane w wystąpieniu usługi Azure Cosmos DB. To wystąpienie jest skojarzone z subskrypcją firmy Microsoft zarządzaną przez usługę Azure Machine Learning. Wszystkie dane przechowywane w usłudze Azure Cosmos DB są szyfrowane w spoczynku za pomocą kluczy zarządzanych przez firmę Microsoft.

Aby użyć własnych kluczy (zarządzanych przez klienta) do szyfrowania wystąpienia usługi Azure Cosmos DB, można utworzyć dedykowane wystąpienie usługi Cosmos DB do użycia z obszarem roboczym. Firma Microsoft zaleca to podejście, jeśli chcesz przechowywać dane, takie jak informacje o historii uruchamiania, poza wystąpieniem usługi Cosmos DB wielu dzierżawy hostowane w naszej subskrypcji firmy Microsoft. 

Aby włączyć inicjowanie obsługi administracyjnej wystąpienia usługi Cosmos DB w ramach subskrypcji za pomocą kluczy zarządzanych przez klienta, wykonaj następujące akcje:

* Włącz funkcje klucza zarządzanego przez klienta dla usługi Cosmos DB. W tej chwili należy zażądać dostępu do korzystania z tej funkcji. W tym celu [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)należy skontaktować się z firmą .

* Zarejestruj dostawców zasobów usługi Azure Machine Learning i Azure Cosmos DB w ramach subskrypcji, jeśli nie zostało to już wykonane.

* Autoryzuj aplikację uczenia maszynowego (w zarządzania tożsamościami i dostępem) z uprawnieniami współautora w ramach subskrypcji.

    ![Autoryzowanie "aplikacji usługi Azure Machine Learning" w zarządzania tożsamościami i dostępem w portalu](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Podczas tworzenia obszaru roboczego usługi Azure Machine Learning należy użyć następujących parametrów. Oba parametry są obowiązkowe i obsługiwane w zestawu SDK, interfejsie wiersza polecenia, interfejsach API rest i szablonach Menedżera zasobów.

    * `resource_cmk_uri`: Ten parametr jest pełnym identyfikatorem URI zasobu klucza zarządzanego przez klienta w magazynie kluczy, w tym [informacjami o wersji klucza.](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning) 

    * `cmk_keyvault`: Ten parametr jest identyfikatorem zasobu magazynu kluczy w subskrypcji. Ten magazyn kluczy musi znajdować się w tym samym regionie i subskrypcji, który będzie używany dla obszaru roboczego usługi Azure Machine Learning. 
    
        > [!NOTE]
        > To wystąpienie magazynu kluczy może się różnić od magazynu kluczy, który jest tworzony przez usługę Azure Machine Learning podczas inicjowania obsługi administracyjnej obszaru roboczego. Jeśli chcesz użyć tego samego wystąpienia magazynu kluczy dla obszaru roboczego, przekaż ten sam magazyn kluczy podczas inicjowania obsługi administracyjnej obszaru roboczego przy użyciu [parametru key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

To wystąpienie usługi Cosmos DB jest tworzone w grupie zasobów zarządzanych przez firmę Microsoft w ramach subskrypcji. 

> [!IMPORTANT]
> * Jeśli chcesz usunąć to wystąpienie usługi Cosmos DB, należy usunąć obszar roboczy usługi Azure Machine Learning, który go używa. 
> * Domyślne [__jednostki żądań__](../cosmos-db/request-units.md) dla tego konta usługi Cosmos DB są ustawione na __8000__. Zmiana tej wartości nie jest podparta. 

Jeśli chcesz __obrócić lub odwołać__ klucz, możesz to zrobić w dowolnym momencie. Podczas obracania klucza usługa Cosmos DB rozpocznie używanie nowego klucza (najnowszej wersji) do szyfrowania danych w spoczynku. Podczas odwoływania (wyłączania) klucza usługa Cosmos DB zajmuje się żądaniami w przypadku niepowodzenia. Zwykle trwa godzinę dla obrotu lub odwołania być skuteczne.

Aby uzyskać więcej informacji na temat kluczy zarządzanych przez klienta za pomocą usługi Cosmos DB, zobacz [Konfigurowanie kluczy zarządzanych przez klienta dla konta usługi Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Azure Container Registry

Wszystkie obrazy kontenerów w rejestrze (Azure Container Registry) są szyfrowane w spoczynku. Platforma Azure automatycznie szyfruje obraz przed zapisaniem go i odszyfrowuje go, gdy usługa Azure Machine Learning pobiera obraz.

Aby użyć własnych kluczy (zarządzanych przez klienta) do szyfrowania rejestru kontenerów platformy Azure, należy utworzyć własny program ACR i dołączyć go podczas inicjowania obsługi administracyjnej obszaru roboczego lub szyfrowania domyślnego wystąpienia, które zostanie utworzone w czasie inicjowania obsługi administracyjnej obszaru roboczego.

Na przykład tworzenia obszaru roboczego przy użyciu istniejącego rejestru kontenerów platformy Azure zobacz następujące artykuły:

* [Utwórz obszar roboczy dla usługi Azure Machine Learning za pomocą interfejsu wiersza polecenia platformy Azure](how-to-manage-workspace-cli.md).
* [Tworzenie obszaru roboczego usługi Azure Machine Learning za pomocą szablonu usługi Azure Resource Manager](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Wystąpienie kontenera platformy Azure

Można zaszyfrować wdrożony zasób wystąpienia kontenera platformy Azure (ACI) przy użyciu kluczy zarządzanych przez klienta. Klucz zarządzany przez klienta używany dla usługi ACI może być przechowywany w magazynie azure key dla obszaru roboczego. Aby uzyskać informacje na temat generowania klucza, zobacz [Szyfrowanie danych za pomocą klucza zarządzanego przez klienta](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Aby użyć klucza podczas wdrażania modelu w wystąpieniu kontenera `AciWebservice.deploy_configuration()`platformy Azure, utwórz nową konfigurację wdrożenia przy użyciu programu . Podaj kluczowe informacje, korzystając z następujących parametrów:

* `cmk_vault_base_url`: Adres URL magazynu kluczy zawierającego klucz.
* `cmk_key_name`: Nazwa klucza.
* `cmk_key_version`: Wersja klucza.

Aby uzyskać więcej informacji na temat tworzenia i używania konfiguracji wdrożenia, zobacz następujące artykuły:

* Odwołanie [do usługi AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Gdzie i jak wdrażać modele](how-to-deploy-and-where.md)
* [Wdrażanie modelu w wystąpieniach kontenera platformy Azure](how-to-deploy-azure-container-instance.md)

Aby uzyskać więcej informacji na temat używania klucza zarządzanego przez klienta za pomocą usługi ACI, zobacz [Szyfrowanie danych za pomocą klucza zarządzanego przez klienta](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

W dowolnym momencie można zaszyfrować wdrożony zasób usługi Azure Kubernetes przy użyciu kluczy zarządzanych przez klienta. Aby uzyskać więcej informacji, zobacz [Przywładki własne klucze z usługą Azure Kubernetes .](../aks/azure-disk-customer-managed-keys.md) 

Ten proces umożliwia szyfrowanie zarówno danych, jak i dysku systemu operacyjnego wdrożonych maszyn wirtualnych w klastrze Kubernetes.

> [!IMPORTANT]
> Ten proces działa tylko z AKS K8s w wersji 1.17 lub nowszej. Usługa Azure Machine Learning dodała obsługę usługi AKS 1.17 w dniu 13 stycznia 2020 r.

#### <a name="machine-learning-compute"></a>Uczenie maszynowe Obliczenie

Dysk systemu operacyjnego dla każdego węzła obliczeniowego przechowywanego w usłudze Azure Storage jest szyfrowany przy pomocą kluczy zarządzanych przez firmę Microsoft na kontach magazynu usługi Azure Machine Learning. Ten cel obliczeniowy jest tymczasowy, a klastry są zazwyczaj skalowane w dół, gdy żadne przebiegi nie są umieszczane w kolejce. Podstawowa maszyna wirtualna jest usuwana z aprowizowania, a dysk systemu operacyjnego jest usuwany. Szyfrowanie dysków platformy Azure nie jest obsługiwane dla dysku systemu operacyjnego.

Każda maszyna wirtualna ma również lokalny dysk tymczasowy dla operacji systemu operacyjnego. Jeśli chcesz, możesz użyć dysku do etapu danych szkoleniowych. Dysk jest domyślnie szyfrowany dla `hbi_workspace` obszarów `TRUE`roboczych z parametrem ustawionym na . To środowisko jest krótkotrwałe tylko przez cały czas trwania uruchamiania, a obsługa szyfrowania jest ograniczona tylko do kluczy zarządzanych przez system.

#### <a name="azure-databricks"></a>Azure Databricks

Usługa Azure Databricks może być używana w potokach usługi Azure Machine Learning. Domyślnie system plików Databricks (DBFS) używany przez usługę Azure Databricks jest szyfrowany przy użyciu klucza zarządzanego przez firmę Microsoft. Aby skonfigurować usługę Azure Databricks do używania kluczy zarządzanych przez klienta, zobacz [Konfigurowanie kluczy zarządzanych przez klienta przy domyślnym (głównym) DBFS](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu

Usługa Azure Machine Learning używa protokołu TLS do zabezpieczania komunikacji wewnętrznej między różnymi mikrousługami usługi Azure Machine Learning. Dostęp do usługi Azure Storage odbywa się również za pomocą bezpiecznego kanału.

Aby zabezpieczyć wywołania zewnętrzne do punktu końcowego oceniania usługi Azure Machine Learning używa protokołu TLS. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie usługi sieci web za pośrednictwem usługi Azure Machine Learning za pomocą protokołu TLS.](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)

### <a name="using-azure-key-vault"></a>Korzystanie z usługi Azure Key Vault

Usługa Azure Machine Learning używa wystąpienia usługi Azure Key Vault skojarzonego z obszarem roboczym do przechowywania poświadczeń różnego rodzaju:

* Parametry połączenia skojarzonego konta magazynu
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Parametry połączeń z magazynami danych

Hasła ssh i klucze do obliczania obiektów docelowych, takich jak usługi Azure HDInsight i maszyny wirtualne, są przechowywane w oddzielnym magazynie kluczy skojarzonym z subskrypcją firmy Microsoft. Usługa Azure Machine Learning nie przechowuje żadnych haseł ani kluczy dostarczonych przez użytkowników. Zamiast tego generuje, autoryzuje i przechowuje własne klucze SSH, aby połączyć się z maszynami wirtualnymi i hdinsight do uruchamiania eksperymentów.

Każdy obszar roboczy ma skojarzoną tożsamość zarządzaną przypisaną do systemu, która ma taką samą nazwę jak obszar roboczy. Ta tożsamość zarządzana ma dostęp do wszystkich kluczy, wpisów tajnych i certyfikatów w magazynie kluczy.

## <a name="data-collection-and-handling"></a>Gromadzenie i przetwarzanie danych

### <a name="microsoft-collected-data"></a>Microsoft zbierał dane

Firma Microsoft może zbierać informacje identyfikujące osoby niebędące użytkownikiem, takie jak nazwy zasobów (na przykład nazwa zestawu danych lub nazwa eksperymentu uczenia maszynowego) lub zmienne środowiskowe zadania do celów diagnostycznych. Wszystkie takie dane są przechowywane przy użyciu kluczy zarządzanych przez firmę Microsoft w pamięci masowej hostowanej w subskrypcjach należących do firmy Microsoft i są zgodne [ze standardowymi zasadami ochrony prywatności firmy Microsoft i standardami obsługi danych.](https://privacy.microsoft.com/privacystatement)

Firma Microsoft zaleca również, aby nie przechowywać poufnych informacji (takich jak wpisy tajne klucza konta) w zmiennych środowiskowych. Zmienne środowiskowe są rejestrowane, szyfrowane i przechowywane przez nas. Podobnie podczas nazywania [runid](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py), należy unikać w tym poufnych informacji, takich jak nazwy użytkowników lub tajnych nazw projektów. Te informacje mogą pojawić się w dziennikach telemetrii dostępnych dla inżynierów pomocy technicznej firmy Microsoft.

Możesz zrezygnować z gromadzonych danych `hbi_workspace` diagnostycznych, ustawiając parametr `TRUE` podczas inicjowania obsługi administracyjnej obszaru roboczego. Ta funkcja jest obsługiwana podczas korzystania z zestawów SDK języka AzureML Python, interfejsu wiersza polecenia, interfejsów API rest lub szablonów usługi Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Dane wygenerowane przez firmę Microsoft

Podczas korzystania z usług, takich jak automatyczne uczenie maszynowe, firma Microsoft może wygenerować przejściowe, wstępnie przetworzone dane do szkolenia wielu modeli. Te dane są przechowywane w magazynie danych w obszarze roboczym, co pozwala odpowiednio wymusić kontrole dostępu i szyfrowanie.

Można również zaszyfrować [informacje diagnostyczne zarejestrowane z wdrożonego punktu końcowego](how-to-enable-app-insights.md) do wystąpienia usługi Azure Application Insights.

## <a name="monitoring"></a>Monitorowanie

### <a name="metrics"></a>Metryki

Metryki usługi Azure Monitor umożliwiają wyświetlanie i monitorowanie metryk usługi Azure Machine Learning. W [witrynie Azure portal](https://portal.azure.com)wybierz swój obszar roboczy, a następnie wybierz **metryki:**

[![Zrzut ekranu przedstawiający przykładowe metryki obszaru roboczego](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Metryki obejmują informacje o działach, wdrożeniach i rejestracjach.

Aby uzyskać więcej informacji, zobacz [metryki w usłudze Azure Monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Dziennik aktywności

Można wyświetlić dziennik aktywności obszaru roboczego, aby wyświetlić różne operacje, które są wykonywane w obszarze roboczym. Dziennik zawiera podstawowe informacje, takie jak nazwa operacji, inicjator zdarzenia i sygnatura czasowa.

Ten zrzut ekranu przedstawia dziennik aktywności obszaru roboczego:

[![Zrzut ekranu przedstawiający dziennik aktywności obszaru roboczego](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Szczegóły żądania oceniania są przechowywane w usłudze Application Insights. Usługa Application Insights jest tworzona w ramach subskrypcji podczas tworzenia obszaru roboczego. Zarejestrowane informacje obejmują pola, takie jak:

* Metod HTTPM
* Useragent
* Typ obliczeń
* WniosekUrl
* Statuscode
* Requestid
* Czas trwania

> [!IMPORTANT]
> Niektóre akcje w obszarze roboczym usługi Azure Machine Learning nie rejestrują informacji w dzienniku aktywności. Na przykład rozpoczęcie przebiegu szkolenia i rejestracja modelu nie są rejestrowane.
>
> Niektóre z tych akcji są wyświetlane w obszarze **Działania** obszaru roboczego, ale te powiadomienia nie wskazują, kto zainicjował działanie.

## <a name="data-flow-diagrams"></a>Diagramy przepływu danych

### <a name="create-workspace"></a>Tworzenie obszaru roboczego

Na poniższym diagramie przedstawiono przepływ pracy tworzenia obszaru roboczego.

* Zaloguj się do usługi Azure AD z jednego z obsługiwanych klientów usługi Azure Machine Learning (interfejs wiersza polecenia platformy Azure, zestaw SDK języka Python, witryna Azure portal) i zażądać odpowiedniego tokenu usługi Azure Resource Manager.
* W celu utworzenia obszaru roboczego należy wywołać usługę Azure Resource Manager. 
* Usługa Azure Resource Manager kontaktuje się z dostawcą zasobów usługi Azure Machine Learning w celu zainicjowania obsługi administracyjnej obszaru roboczego.

Dodatkowe zasoby są tworzone w subskrypcji użytkownika podczas tworzenia obszaru roboczego:

* Magazyn kluczy (do przechowywania wpisów tajnych)
* Konto magazynu platformy Azure (w tym obiekt blob i udział plików)
* Usługa Azure Container Registry (do przechowywania obrazów platformy Docker do wnioskowania/oceniania i eksperymentowania)
* Usługa Application Insights (do przechowywania danych telemetrycznych)

Użytkownik może również aprowizować inne obiekty docelowe obliczeń, które są dołączone do obszaru roboczego (np. usługi Azure Kubernetes lub maszyn wirtualnych) w razie potrzeby.

[![Tworzenie przepływu pracy obszaru roboczego](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Zapisywanie kodu źródłowego (skrypty szkoleniowe)

Na poniższym diagramie przedstawiono przepływ pracy migawki kodu.

Skojarzone z obszarem roboczym usługi Azure Machine Learning są katalogi (eksperymenty), które zawierają kod źródłowy (skrypty szkoleniowe). Te skrypty są przechowywane na komputerze lokalnym i w chmurze (w magazynie obiektów Blob platformy Azure dla subskrypcji). Migawki kodu są używane do wykonywania lub inspekcji do inspekcji historycznej.

[![Przepływ pracy migawki kodu](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Szkolenia

Na poniższym diagramie przedstawiono przepływ pracy szkolenia.

* Usługa Azure Machine Learning jest wywoływana z identyfikatorem migawki dla migawki kodu zapisanej w poprzedniej sekcji.
* Usługa Azure Machine Learning tworzy identyfikator uruchomienia (opcjonalnie) i token usługi uczenia maszynowego, który jest później używany przez obiekty docelowe obliczeń, takie jak obliczenia/maszyny wirtualne uczenia maszynowego do komunikowania się z usługą uczenia maszynowego.
* Można wybrać zarządzany obiekt docelowy obliczeń (np. obliczenia uczenia maszynowego) lub niezarządzany obiekt docelowy obliczeń (np. maszyn wirtualnych), aby uruchamiać zadania szkoleniowe. Oto przepływy danych dla obu scenariuszy:
   * Maszyny wirtualne/HDInsight, uzyskiwały dostęp poświadczeń SSH w magazynie kluczy w ramach subskrypcji firmy Microsoft. Usługa Azure Machine Learning uruchamia kod zarządzania w celu obliczeniowym, który:

   1. Przygotowuje środowisko. (Docker jest opcją dla maszyn wirtualnych i komputerów lokalnych. Zobacz następujące kroki dotyczące obliczeń uczenia maszynowego, aby dowiedzieć się, jak działa uruchamianie eksperymentów na kontenerach platformy Docker).
   1. Pobiera kod.
   1. Konfiguruje zmienne środowiskowe i konfiguracje.
   1. Uruchamia skrypty użytkownika (migawka kodu wymieniona w poprzedniej sekcji).

   * Obliczenia uczenia maszynowego, dostępne za pośrednictwem tożsamości zarządzanej przez obszar roboczy.
Ponieważ przetwarzanie obliczeń jest zarządzanym celem obliczeniowym (oznacza to, że jest zarządzany przez firmę Microsoft), działa w ramach subskrypcji firmy Microsoft.

   1. Zdalna konstrukcja platformy Docker jest uruchamiana, jeśli to konieczne.
   1. Kod zarządzania jest zapisywany w udziale usługi Azure Files użytkownika.
   1. Kontener jest uruchamiany z poleceniem początkowym. Oznacza to, że kod zarządzania, zgodnie z opisem w poprzednim kroku.

#### <a name="querying-runs-and-metrics"></a>Wykonywanie zapytań o przebiegi i metryki

Na poniższym diagramie przepływu ten krok występuje, gdy obiekt docelowy obliczeń szkoleniowych zapisuje metryki uruchamiania z powrotem do usługi Azure Machine Learning z magazynu w bazie danych usługi Cosmos DB. Klienci mogą wywoływać usługę Azure Machine Learning. Uczenie maszynowe z kolei będzie pobierać metryki z bazy danych usługi Cosmos DB i zwracać je z powrotem do klienta.

[![Przepływ pracy szkoleniowej](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Tworzenie usług sieci web

Na poniższym diagramie przedstawiono przepływ pracy wnioskowania. Wnioskowanie lub ocenianie modelu jest fazą, w której wdrożony model jest używany do przewidywania, najczęściej na danych produkcyjnych.

Oto szczegóły:

* Użytkownik rejestruje model przy użyciu klienta, takiego jak zestaw SDK usługi Azure Machine Learning.
* Użytkownik tworzy obraz przy użyciu modelu, pliku wynik i innych zależności modelu.
* Obraz platformy Docker jest tworzony i przechowywany w rejestrze kontenerów platformy Azure.
* Usługa sieci web jest wdrażana do obiektu docelowego obliczeń (wystąpienia kontenera/usługi AKS) przy użyciu obrazu utworzonego w poprzednim kroku.
* Szczegóły żądania oceniania są przechowywane w usłudze Application Insights, która jest w subskrypcji użytkownika.
* Telemetria jest również wypychany do subskrypcji Microsoft/Azure.

[![Przepływ pracy wnioskowania](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* [Bezpieczne usługi sieci Web usługi azure machine learning z tls](how-to-secure-web-service.md)
* [Korzystanie z modelu uczenia maszynowego wdrożonego jako usługa sieci web](how-to-consume-web-service.md)
* [Jak uruchomić prognozy partii](how-to-use-parallel-run-step.md)
* [Monitoruj swoje modele usługi Azure Machine Learning za pomocą usługi Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w produkcji](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Korzystanie z usługi Azure Machine Learning w sieci wirtualnej platformy Azure](how-to-enable-virtual-network.md)
* [Najważniejsze wskazówki dotyczące tworzenia systemów rekomendacji](https://github.com/Microsoft/Recommenders)
* [Tworzenie interfejsu API rekomendacji w czasie rzeczywistym na platformie Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
