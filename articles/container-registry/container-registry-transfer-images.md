---
title: Przenieś artefakty
description: Przenoszenie kolekcji obrazów lub innych artefaktów z jednego rejestru kontenerów do innego rejestru przez utworzenie potoku transferu przy użyciu kont usługi Azure Storage
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: fd2cee972ef173853572b871bc80b92b28c505cd
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932604"
---
# <a name="transfer-artifacts-to-another-registry"></a>Przenoszenie artefaktów do innego rejestru

W tym artykule przedstawiono sposób transferu kolekcji obrazów lub innych artefaktów rejestru z jednego rejestru kontenera platformy Azure do innego rejestru. Rejestr źródłowy i docelowy mogą znajdować się w tych samych lub różnych subskrypcjach, Active Directory dzierżawców, chmurach platformy Azure lub chmurach fizycznie odłączonych. 

Aby przenieść artefakty, tworzysz *potok transferu* , który replikuje artefakty między dwoma rejestrami przy użyciu usługi [BLOB Storage](../storage/blobs/storage-blobs-introduction.md):

* Artefakty z rejestru źródłowego są eksportowane do obiektu BLOB na źródłowym koncie magazynu 
* Obiekt BLOB jest kopiowany z konta magazynu źródłowego na docelowe konto magazynu
* Obiekt BLOB na docelowym koncie magazynu zostanie zaimportowany jako artefakty w rejestrze docelowym. Można skonfigurować potok importowania do wyzwalania za każdym razem, gdy obiekt BLOB artefaktu zostanie zaktualizowany w magazynie docelowym.

Transfer jest idealny do kopiowania zawartości między dwoma rejestrami kontenerów platformy Azure w chmurach fizycznie odłączonych, korygowanych przez konta magazynu w poszczególnych chmurach. Jeśli zamiast tego chcesz kopiować obrazy z rejestrów kontenerów w połączonych chmurach, w tym w przypadku usługi Docker Hub i innych dostawców chmury, zaleca się [Importowanie obrazów](container-registry-import-images.md) .

W tym artykule opisano tworzenie i uruchamianie potoku transferu przy użyciu wdrożeń szablonów Azure Resource Manager. Interfejs wiersza polecenia platformy Azure służy do udostępniania skojarzonych zasobów, takich jak wpisy tajne magazynu. Zalecany jest interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Jeśli konieczna będzie instalacja interfejsu wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Ta funkcja jest dostępna w warstwie usługi kontenera **Premium** . Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry warstw](container-registry-skus.md).

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

* Rejestry **kontenerów** — należy dysponować istniejącym rejestrem źródłowym z artefaktami do przetransferowania oraz rejestrem docelowym. Transfer ACR jest przeznaczony do przenoszenia w chmurach fizycznie odłączonych. Do testowania rejestr źródłowy i docelowy mogą znajdować się w tej samej lub innej subskrypcji platformy Azure, w Active Directory dzierżawie lub w chmurze. 

   Jeśli musisz utworzyć rejestr, zobacz [Szybki Start: Tworzenie prywatnego rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md). 
* **Konta magazynu** — Utwórz źródłowe i docelowe konta magazynu w wybranej subskrypcji i lokalizacji. Do celów testowych możesz użyć tej samej subskrypcji lub subskrypcji jako rejestrów źródłowych i docelowych. W przypadku scenariuszy obejmujących wiele chmur zazwyczaj tworzysz oddzielne konto magazynu w każdej chmurze. 

  W razie konieczności Utwórz konta magazynu za pomocą [interfejsu wiersza polecenia platformy Azure](../storage/common/storage-account-create.md?tabs=azure-cli) lub innych narzędzi. 

  Utwórz kontener obiektów BLOB na potrzeby transferu artefaktów na każdym koncie. Na przykład utwórz kontener o nazwie *transfer*. Dwa lub więcej potoków transferu mogą współużytkować to samo konto magazynu, ale należy używać różnych zakresów kontenera magazynu.
* **Magazyny kluczy** — magazyny kluczy są konieczne do przechowywania wpisów tajnych tokenów SAS używanych do uzyskiwania dostępu do źródłowych i docelowych kont magazynu. Utwórz źródłowe i docelowe magazyny kluczy w tej samej subskrypcji lub subskrypcjach platformy Azure jako rejestry źródłowe i docelowe. W celach demonstracyjnych szablony i polecenia używane w tym artykule zakładają również, że źródłowe i docelowe magazyny kluczy znajdują się w tych samych grupach zasobów co rejestr źródłowy i docelowy. Użycie wspólnych grup zasobów nie jest wymagane, ale upraszcza szablony i polecenia używane w tym artykule.

   W razie konieczności Utwórz magazyny kluczy za pomocą [interfejsu wiersza polecenia platformy Azure](../key-vault/secrets/quick-create-cli.md) lub innych narzędzi.

* **Zmienne środowiskowe** — w przypadku przykładowych poleceń w tym artykule ustaw następujące zmienne środowiskowe dla środowiska źródłowego i docelowego. Wszystkie przykłady są sformatowane dla powłoki bash.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Omówienie scenariusza

Tworzysz następujące trzy zasoby potoku do transferu obrazów między rejestrami. Wszystkie są tworzone przy użyciu operacji PUT. Te zasoby działają na *źródłowym* i *docelowym* rejestrach oraz na kontach magazynu. 

Uwierzytelnianie magazynu używa tokenów SAS zarządzanych jako wpisy tajne w magazynie kluczy. Potoki używają tożsamości zarządzanych do odczytywania wpisów tajnych w magazynach.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** -długotrwały zasób zawierający ogólne informacje o rejestrze *źródłowym* i koncie magazynu. Te informacje obejmują źródłowy identyfikator URI kontenera obiektów blob magazynu i Magazyn kluczy zarządzający źródłowym tokenem SAS. 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** -długotrwały zasób zawierający ogólne informacje o *docelowym* rejestrze i koncie magazynu. Te informacje obejmują docelowy identyfikator URI kontenera obiektów blob magazynu i Magazyn kluczy zarządzający docelowym tokenem sygnatury dostępu współdzielonego. Wyzwalacz importu jest domyślnie włączony, więc potok jest uruchamiany automatycznie, gdy obiekt BLOB artefaktu znajduje się w docelowym kontenerze magazynu. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** — zasób używany do wywołania zasobu ExportPipeline lub ImportPipeline.  
  * ExportPipeline można uruchomić ręcznie przez utworzenie zasobu PipelineRun i określenie artefaktów do wyeksportowania.  
  * Jeśli wyzwalacz importu jest włączony, ImportPipeline zostanie uruchomiony automatycznie. Można go również uruchomić ręcznie przy użyciu PipelineRun. 
  * Obecnie można przenieść maksymalnie **50 artefaktów** z każdym PipelineRunem.

### <a name="things-to-know"></a>Co należy wiedzieć
* ExportPipeline i ImportPipeline są zwykle w różnych dzierżawach Active Directory skojarzonych z chmurami źródłową i docelową. Ten scenariusz wymaga oddzielnych tożsamości zarządzanych i magazynów kluczy do zasobów eksportu i importu. Do celów testowych te zasoby mogą być umieszczane w tej samej chmurze i udostępniane tożsamości.
* Domyślnie szablony ExportPipeline i ImportPipeline umożliwiają zarządzanie tożsamościami zarządzanymi przez system w celu uzyskania dostępu do wpisów tajnych magazynu kluczy. Szablony ExportPipeline i ImportPipeline obsługują również tożsamość przypisaną przez użytkownika. 

## <a name="create-and-store-sas-keys"></a>Tworzenie i przechowywanie kluczy SAS

Transfer używa tokenów sygnatury dostępu współdzielonego (SAS), aby uzyskać dostęp do kont magazynu w środowisku źródłowym i docelowym. Generuj i przechowuj tokeny zgodnie z opisem w poniższych sekcjach.  

### <a name="generate-sas-token-for-export"></a>Generuj token sygnatury dostępu współdzielonego na potrzeby eksportu

Uruchom polecenie [AZ Storage Container Generate-SAS][az-storage-container-generate-sas] , aby wygenerować token sygnatury dostępu współdzielonego dla kontenera na źródłowym koncie magazynu, który służy do eksportowania artefaktów.

*Zalecane uprawnienia tokenu*: Odczyt, zapis, lista, Dodawanie. 

W poniższym przykładzie dane wyjściowe polecenia są przypisywane do zmiennej środowiskowej EXPORT_SAS, poprzedzone znakiem "?". Zaktualizuj `--expiry` wartość dla środowiska:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Token sygnatury dostępu współdzielonego magazynu na potrzeby eksportu

Zapisz token sygnatury dostępu współdzielonego w źródłowym magazynie kluczy platformy Azure przy użyciu polecenia AZ Key Source [Secret][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>Generuj token sygnatury dostępu współdzielonego na potrzeby importu

Uruchom polecenie [AZ Storage Container Generate-SAS][az-storage-container-generate-sas] w celu wygenerowania tokenu sygnatury dostępu współdzielonego dla kontenera na docelowym koncie magazynu używanego do importowania artefaktów.

*Zalecane uprawnienia tokenu*: Odczyt, usuwanie, lista

W poniższym przykładzie dane wyjściowe polecenia są przypisywane do zmiennej środowiskowej IMPORT_SAS, poprzedzone znakiem "?". Zaktualizuj `--expiry` wartość dla środowiska:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>Token sygnatury dostępu współdzielonego magazynu na potrzeby importu

Zapisz token sygnatury dostępu współdzielonego w docelowym magazynie kluczy platformy Azure przy użyciu polecenia [AZ Key magazynu Secret][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Utwórz ExportPipeline z Menedżer zasobów

Utwórz zasób ExportPipeline dla rejestru kontenerów źródłowych przy użyciu wdrożenia szablonu Azure Resource Manager.

Skopiuj [pliki szablonów](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) Menedżer zasobów ExportPipeline do folderu lokalnego.

Wprowadź następujące wartości parametrów w pliku `azuredeploy.parameters.json` :

|Parametr  |Wartość  |
|---------|---------|
|registryname     | Nazwa źródłowego rejestru kontenerów      |
|exportPipelineName     |  Wybrana nazwa dla potoku eksportu       |
|Parametru TargetUri     |  Identyfikator URI kontenera magazynu w środowisku źródłowym (obiekt docelowy potoku eksportu).<br/>Przykład: `https://sourcestorage.blob.core.windows.net/transfer`       |
|Nazwakluczamagazynu     |  Nazwa magazynu kluczy źródłowych  |
|sasTokenSecretName  | Nazwa wpisu tajnego tokenu sygnatury dostępu współdzielonego w magazynie kluczy źródłowych <br/>Przykład: acrexportsas

### <a name="export-options"></a>Opcje eksportu

`options`Właściwość dla potoku eksportu obsługuje opcjonalne wartości logiczne. Zalecane są następujące wartości:

|Parametr  |Wartość  |
|---------|---------|
|opcje | OverwriteBlobs — Zastąp istniejące obiekty blob obiektów docelowych<br/>ContinueOnErrors — Kontynuuj eksportowanie pozostałych artefaktów w rejestrze źródłowym w przypadku niepowodzenia eksportu artefaktów.

### <a name="create-the-resource"></a>Tworzenie zasobu

Uruchom [AZ Deployment Group Create][az-deployment-group-create] , aby utworzyć zasób o nazwie *exportPipeline* , jak pokazano w poniższych przykładach. Domyślnie przy pierwszej opcji przykładowy szablon włącza tożsamość przypisaną przez system w zasobie ExportPipeline. 

Za pomocą drugiej opcji można dostarczyć zasób z tożsamością przypisaną przez użytkownika. (Utworzenie tożsamości przypisanej przez użytkownika nie jest pokazane).

Za pomocą dowolnej z tych opcji szablon konfiguruje tożsamość, aby uzyskać dostęp do tokenu sygnatury dostępu współdzielonego w magazynie kluczy eksportu. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Opcja 1. Tworzenie zasobu i włączanie tożsamości przypisanej do systemu

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Opcja 2: Tworzenie zasobu i udostępnianie tożsamości przypisanej przez użytkownika

W tym poleceniu podaj identyfikator zasobu tożsamości przypisanej do użytkownika jako dodatkowy parametr.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

W danych wyjściowych polecenia Zanotuj identyfikator zasobu ( `id` ) potoku. Tę wartość można zapisać w zmiennej środowiskowej w celu późniejszego użycia, uruchamiając [AZ Deployment Group Show][az-deployment-group-show]. Na przykład:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Utwórz ImportPipeline z Menedżer zasobów 

Utwórz zasób ImportPipeline w docelowym rejestrze kontenera przy użyciu wdrożenia szablonu Azure Resource Manager. Domyślnie potok jest automatycznie importowany, gdy konto magazynu w środowisku docelowym ma obiekt BLOB artefaktu.

Skopiuj [pliki szablonów](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) Menedżer zasobów ImportPipeline do folderu lokalnego.

Wprowadź następujące wartości parametrów w pliku `azuredeploy.parameters.json` :

Parametr  |Wartość  |
|---------|---------|
|registryname     | Nazwa docelowego rejestru kontenerów      |
|importPipelineName     |  Wybrana nazwa dla potoku importowania       |
|sourceUri     |  Identyfikator URI kontenera magazynu w środowisku docelowym (Źródło dla potoku importu).<br/>Przykład: `https://targetstorage.blob.core.windows.net/transfer/`|
|Nazwakluczamagazynu     |  Nazwa docelowego magazynu kluczy |
|sasTokenSecretName     |  Nazwa wpisu tajnego tokenu sygnatury dostępu współdzielonego w magazynie kluczy docelowych<br/>Przykład: ACR importsas |

### <a name="import-options"></a>Opcje importowania

`options`Właściwość potoku importu obsługuje opcjonalne wartości logiczne. Zalecane są następujące wartości:

|Parametr  |Wartość  |
|---------|---------|
|opcje | OverwriteTags — Zastąp istniejące Tagi docelowe<br/>DeleteSourceBlobOnSuccess — Usuń źródłowy obiekt blob magazynu po pomyślnym zaimportowaniu do rejestru docelowego<br/>ContinueOnErrors — Kontynuuj Importowanie pozostałych artefaktów w rejestrze docelowym, jeśli wystąpił błąd jednego z nich.

### <a name="create-the-resource"></a>Tworzenie zasobu

Uruchom [AZ Deployment Group Create][az-deployment-group-create] , aby utworzyć zasób o nazwie *importPipeline* , jak pokazano w poniższych przykładach. Domyślnie przy pierwszej opcji przykładowy szablon włącza tożsamość przypisaną przez system w zasobie ImportPipeline. 

Za pomocą drugiej opcji można dostarczyć zasób z tożsamością przypisaną przez użytkownika. (Utworzenie tożsamości przypisanej przez użytkownika nie jest pokazane).

Przy użyciu dowolnej opcji szablon konfiguruje tożsamość w celu uzyskania dostępu do tokenu SAS w magazynie kluczy importu. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Opcja 1. Tworzenie zasobu i włączanie tożsamości przypisanej do systemu

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Opcja 2: Tworzenie zasobu i udostępnianie tożsamości przypisanej przez użytkownika

W tym poleceniu podaj identyfikator zasobu tożsamości przypisanej do użytkownika jako dodatkowy parametr.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Jeśli planujesz uruchamianie importu ręcznie, zanotuj identyfikator zasobu ( `id` ) potoku. Tę wartość można zapisać w zmiennej środowiskowej w celu późniejszego użycia przez uruchomienie polecenia [AZ Deployment Group Show][az-deployment-group-show] . Na przykład:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Utwórz PipelineRun do eksportowania za pomocą Menedżer zasobów 

Utwórz zasób PipelineRun dla rejestru kontenerów źródłowych przy użyciu wdrożenia szablonu Azure Resource Manager. Ten zasób uruchamia wcześniej utworzony zasób ExportPipeline i eksportuje określone artefakty z rejestru kontenerów jako obiekt BLOB do źródłowego konta magazynu.

Skopiuj [pliki szablonów](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) Menedżer zasobów PipelineRun do folderu lokalnego.

Wprowadź następujące wartości parametrów w pliku `azuredeploy.parameters.json` :

|Parametr  |Wartość  |
|---------|---------|
|registryname     | Nazwa źródłowego rejestru kontenerów      |
|pipelineRunName     |  Wybrana nazwa dla przebiegu       |
|pipelineResourceId     |  Identyfikator zasobu potoku eksportu.<br/>Przykład: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  Wybrana nazwa dla obiektu BLOB artefaktów wyeksportowanego do źródłowego konta magazynu, takiego jak *obiekt BLOB*
|pojawia | Tablica artefaktów źródłowych do przetransferowania, jako Tagi lub skróty manifestu<br/>Przykład: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

W przypadku ponownego wdrażania zasobu PipelineRun z identycznymi właściwościami należy również użyć właściwości [forceUpdateTag](#redeploy-pipelinerun-resource) .

Uruchom [AZ Deployment Group Create][az-deployment-group-create] , aby utworzyć zasób PipelineRun. Poniższy przykład nazywa *exportPipelineRun*wdrożenia.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

W celu późniejszego użycia Zapisz identyfikator zasobu dla uruchomienia potoku w zmiennej środowiskowej:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Eksportowanie artefaktów może potrwać kilka minut. Po pomyślnym zakończeniu wdrożenia Sprawdź pozycję Eksportuj artefakty, wyświetlając wyeksportowany obiekt BLOB w kontenerze *transfer* na źródłowym koncie magazynu. Na przykład uruchom polecenie [AZ Storage BLOB list][az-storage-blob-list] :

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Transfer obiektów BLOB (opcjonalnie) 

Użyj narzędzia AzCopy lub innych metod [transferu danych obiektów BLOB](../storage/common/storage-use-azcopy-blobs.md#copy-blobs-between-storage-accounts) z konta magazynu źródłowego na docelowe konto magazynu.

Na przykład następujące [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) polecenie kopiuje obiekt BLOB z kontenera *transferu* na koncie źródłowym do kontenera *transferu* na koncie docelowym. Jeśli obiekt BLOB istnieje na koncie docelowym, zostanie nadpisany. Uwierzytelnianie używa tokenów SAS z odpowiednimi uprawnieniami dla kontenerów źródłowy i docelowy. (Kroki tworzenia tokenów nie są wyświetlane).

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Wyzwalanie zasobu ImportPipeline

Jeśli włączono `sourceTriggerStatus` parametr ImportPipeline (wartość domyślna), potok jest wyzwalany po skopiowaniu obiektu BLOB na docelowe konto magazynu. Importowanie artefaktów może potrwać kilka minut. Po pomyślnym zakończeniu importowania Sprawdź zaimportowanie artefaktów, wyświetlając listę repozytoriów w docelowym rejestrze kontenerów. Na przykład uruchom [AZ ACR Repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

Jeśli nie włączono `sourceTriggerStatus` parametru potoku importu, uruchom ręcznie zasób ImportPipeline, jak pokazano w poniższej sekcji. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Utwórz PipelineRun do zaimportowania z Menedżer zasobów (opcjonalnie) 
 
Można również użyć zasobu PipelineRun, aby wyzwolić ImportPipeline do importowania artefaktów do docelowego rejestru kontenerów.

Skopiuj [pliki szablonów](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) Menedżer zasobów PipelineRun do folderu lokalnego.

Wprowadź następujące wartości parametrów w pliku `azuredeploy.parameters.json` :

|Parametr  |Wartość  |
|---------|---------|
|registryname     | Nazwa docelowego rejestru kontenerów      |
|pipelineRunName     |  Wybrana nazwa dla przebiegu       |
|pipelineResourceId     |  Identyfikator zasobu potoku importu.<br/>Przykład: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  Nazwa istniejącego obiektu BLOB dla eksportowanych artefaktów na koncie magazynu, takich jak *obiekt BLOB*

W przypadku ponownego wdrażania zasobu PipelineRun z identycznymi właściwościami należy również użyć właściwości [forceUpdateTag](#redeploy-pipelinerun-resource) .

Uruchom [AZ Deployment Group Create][az-deployment-group-create] , aby uruchomić zasób.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

W celu późniejszego użycia Zapisz identyfikator zasobu dla uruchomienia potoku w zmiennej środowiskowej:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)

When deployment completes successfully, verify artifact import by listing the repositories in the target container registry. For example, run [az acr repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>Wdróż ponownie zasób PipelineRun

W przypadku ponownego wdrażania zasobu PipelineRun z *identycznymi właściwościami*należy użyć właściwości **forceUpdateTag** . Ta właściwość wskazuje, że zasób PipelineRun należy utworzyć ponownie, nawet jeśli konfiguracja nie została zmieniona. Upewnij się, że forceUpdateTag jest inna po każdym ponownym wdrożeniu zasobu PipelineRun. Poniższy przykład odtworzy PipelineRun do eksportu. Bieżąca data i godzina jest używana do ustawiania forceUpdateTag, co zapewnia, że ta właściwość zawsze jest unikatowa.

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>Usuwanie zasobów potoku

W następujących przykładowych poleceń użyto polecenia [AZ Resource Delete][az-resource-delete] do usuwania zasobów potoku utworzonych w tym artykule. Identyfikatory zasobów były wcześniej przechowywane w zmiennych środowiskowych.

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Niepowodzenia Template deployment lub błędy**
  * Jeśli uruchomienie potoku nie powiedzie się, sprawdź `pipelineRunErrorMessage` Właściwość uruchomienia zasobu.
  * Typowe błędy wdrażania szablonów można znaleźć w temacie [Rozwiązywanie problemów z wdrożeniami szablonów ARM](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **Problemy z eksportem lub importem obiektów blob magazynu**
  * Token SAS może wygasnąć lub mieć niewystarczające uprawnienia do określonego eksportu lub przebiegu importowania
  * Istniejący obiekt blob magazynu w źródłowym koncie magazynu może nie zostać nadpisany podczas wielu przebiegów eksportu. Upewnij się, że opcja OverwriteBlob jest ustawiona w przebiegu eksportu i token sygnatury dostępu współdzielonego ma wystarczające uprawnienia.
  * Po pomyślnym uruchomieniu importu nie można usunąć obiektu blob magazynu na docelowym koncie magazynu. Upewnij się, że opcja DeleteBlobOnSuccess jest ustawiona w przebiegu importowania, a token SAS ma wystarczające uprawnienia.
  * Obiekt blob magazynu nie został utworzony ani usunięty. Upewnij się, że kontener określony w przebiegu eksportowania lub importowania istnieje lub określony obiekt blob magazynu istnieje dla ręcznego przebiegu importu. 
* **AzCopy problemy**
  * Zobacz [Rozwiązywanie problemów z AzCopy](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues).  
* **Problemy z transferem artefaktów**
  * Nie wszystkie artefakty, ani żadne nie są transferowane. Potwierdź pisownię artefaktów w przebiegu eksportu i nazwę obiektu BLOB w ramach przebiegów eksportu i importu. Potwierdź, że przesyłasz maksymalnie 50 artefaktów.
  * Uruchomienie potoku mogło nie zostać zakończone. Przebieg eksportu lub importu może zająć trochę czasu. 
  * W przypadku innych problemów z potokiem Podaj [Identyfikator korelacji](../azure-resource-manager/templates/deployment-history.md) wdrożenia przebiegu eksportowania lub import do zespołu Azure Container Registry.


## <a name="next-steps"></a>Następne kroki

Aby zaimportować pojedyncze obrazy kontenera do usługi Azure Container Registry z rejestru publicznego lub innego rejestru prywatnego, zobacz [AZ ACR import][az-acr-import] Command Reference.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-resource-delete]: /cli/azure/resource#az-resource-delete
