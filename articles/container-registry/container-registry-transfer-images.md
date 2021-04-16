---
title: Transfer artefaktów
description: Transferowanie kolekcji obrazów lub innych artefaktów z jednego rejestru kontenerów do innego rejestru przez utworzenie potoku transferu przy użyciu kont usługi Azure Storage
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: e921880eb0b8ae5a38e69c9c0045f6a26d84084d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497986"
---
# <a name="transfer-artifacts-to-another-registry"></a>Transfer artefaktów do innego rejestru

W tym artykule przedstawiono sposób przenoszenia kolekcji obrazów lub innych artefaktów rejestru z jednego rejestru kontenerów platformy Azure do innego rejestru. Rejestry źródłowe i docelowe mogą być w tej samej lub różnych subskrypcjach, dzierżawach usługi Active Directory, chmurach platformy Azure lub chmurach odłączonych fizycznie. 

Aby przenieść artefakty, należy utworzyć *potok transferu,* który replikuje artefakty między dwoma rejestrami przy użyciu magazynu [obiektów blob:](../storage/blobs/storage-blobs-introduction.md)

* Artefakty z rejestru źródłowego są eksportowane do obiektu blob na źródłowym koncie magazynu 
* Obiekt blob jest kopiowany ze źródłowego konta magazynu na docelowe konto magazynu
* Obiekt blob na docelowym koncie magazynu jest importowany jako artefakty w rejestrze docelowym. Potok importu można skonfigurować tak, aby był wyzwalany przy każdej aktualizacji obiektu blob artefaktu w magazynie docelowym.

Transfer jest idealnym rozwiązaniem do kopiowania zawartości między dwoma rejestrami kontenerów platformy Azure w fizycznie odłączonych chmurach za pomocą kont magazynu w każdej chmurze. Jeśli zamiast tego chcesz skopiować obrazy z rejestrów kontenerów w połączonych chmurach, w tym Docker Hub i innych dostawców chmury, zaleca się [importowanie](container-registry-import-images.md) obrazów.

W tym artykule użyjemy Azure Resource Manager szablonu do utworzenia i uruchomienia potoku transferu. Interfejs wiersza polecenia platformy Azure służy do aprowizowanie skojarzonych zasobów, takich jak wpisy tajne magazynu. Zalecany jest interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Jeśli konieczna będzie instalacja interfejsu wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Ta funkcja jest dostępna w warstwie usługi rejestru kontenerów **Premium.** Aby uzyskać informacje o warstwach i limitach usługi rejestru, [zobacz Azure Container Registry warstwy.](container-registry-skus.md)

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

* **Rejestry kontenerów** — potrzebny jest istniejący rejestr źródłowy z artefaktami do przeniesienia oraz rejestr docelowy. Transfer ACR jest przeznaczony do przenoszenia między fizycznie odłączone chmury. W przypadku testowania rejestry źródłowe i docelowe mogą być w tej samej lub innej subskrypcji platformy Azure, dzierżawie usługi Active Directory lub w chmurze. 

   Jeśli musisz utworzyć rejestr, zobacz Szybki start: tworzenie prywatnego rejestru kontenerów przy użyciu interfejsu [wiersza polecenia platformy Azure.](container-registry-get-started-azure-cli.md) 
* **Konta magazynu** — utwórz źródłowe i docelowe konta magazynu w wybranej subskrypcji i lokalizacji. Do celów testowych możesz użyć tej samej subskrypcji lub subskrypcji, co rejestry źródłowe i docelowe. W scenariuszach międzychmurowych zwykle tworzy się oddzielne konto magazynu w każdej chmurze. 

  W razie potrzeby utwórz konta magazynu przy użyciu interfejsu wiersza [polecenia platformy Azure](../storage/common/storage-account-create.md?tabs=azure-cli) lub innych narzędzi. 

  Utwórz kontener obiektów blob do transferu artefaktów na każdym koncie. Na przykład utwórz kontener o nazwie *transfer*. Co najmniej dwa potoki transferu mogą współdzielić to samo konto magazynu, ale powinny używać różnych zakresów kontenerów magazynu.
* **Magazyny kluczy — magazyny** kluczy są potrzebne do przechowywania wpisów tajnych tokenu SAS używanych do uzyskiwania dostępu do źródłowych i docelowych kont magazynu. Utwórz źródłowe i docelowe magazyny kluczy w tej samej subskrypcji platformy Azure co rejestry źródłowe i docelowe. W celach demonstracyjnych szablony i polecenia używane w tym artykule zakładają również, że źródłowe i docelowe magazyny kluczy znajdują się w tych samych grupach zasobów, co rejestry źródłowe i docelowe. Takie użycie typowych grup zasobów nie jest wymagane, ale upraszcza szablony i polecenia używane w tym artykule.

   W razie potrzeby utwórz magazyny kluczy przy użyciu interfejsu wiersza [polecenia platformy Azure](../key-vault/secrets/quick-create-cli.md) lub innych narzędzi.

* **Zmienne środowiskowe —** na przykład polecenia w tym artykule należy ustawić następujące zmienne środowiskowe dla środowiska źródłowego i docelowego. Wszystkie przykłady są sformatowane dla powłoki Bash.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Omówienie scenariusza

Utworzysz następujące trzy zasoby potoku do transferu obrazów między rejestrami. Wszystkie są tworzone przy użyciu operacji PUT. Te zasoby działają na *źródłowych i* *docelowych* rejestrach oraz kontach magazynu. 

Uwierzytelnianie magazynu używa tokenów SAS zarządzanych jako wpisy tajne w magazynach kluczy. Potoki używają tożsamości zarządzanych do odczytywania wpisów tajnych w magazynach.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** — długotrwały zasób, który zawiera  informacje wysokiego poziomu dotyczące rejestru źródłowego i konta magazynu. Te informacje obejmują źródłowy klucz URI kontenera obiektów blob magazynu i magazyn kluczy zarządzający źródłowym tokenem SAS. 
* **[ImportPipeline —](#create-importpipeline-with-resource-manager)** długotrwały zasób, który zawiera  informacje wysokiego poziomu dotyczące rejestru docelowego i konta magazynu. Te informacje obejmują docelowy klucz URI kontenera obiektów blob magazynu i magazyn kluczy zarządzający docelowym tokenem SAS. Wyzwalacz importu jest domyślnie włączony, więc potok jest uruchamiany automatycznie, gdy obiekt blob artefaktu znajduje się w docelowym kontenerze magazynu. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** — zasób używany do wywoływania zasobu ExportPipeline lub ImportPipeline.  
  * Potok ExportPipeline można uruchomić ręcznie, tworząc zasób PipelineRun i określając artefakty do wyeksportowania.  
  * Jeśli wyzwalacz importu jest włączony, element ImportPipeline jest uruchamiany automatycznie. Można go również uruchomić ręcznie przy użyciu funkcji PipelineRun. 
  * Obecnie za pomocą każdego uruchomienia potoku można przenieść maksymalnie **50** artefaktów.

### <a name="things-to-know"></a>Co należy wiedzieć
* ExportPipeline i ImportPipeline zwykle będą się wiązać z różnymi dzierżawami usługi Active Directory skojarzonymi z chmurą źródłową i docelową. W tym scenariuszu eksportowanie i importowanie zasobów wymaga oddzielnych tożsamości zarządzanych i magazynów kluczy. W celach testowych te zasoby można umieścić w tej samej chmurze, współużytkując tożsamości.
* Domyślnie szablony ExportPipeline i ImportPipeline umożliwiają tożsamości zarządzanej przypisanej przez system dostęp do wpisów tajnych magazynu kluczy. Szablony ExportPipeline i ImportPipeline obsługują również tożsamość przypisaną przez użytkownika, która jest dostarczana. 

## <a name="create-and-store-sas-keys"></a>Tworzenie i przechowywanie kluczy sygnatury dostępu współdzielonego

Transfer używa tokenów sygnatury dostępu współdzielonego (SAS) w celu uzyskania dostępu do kont magazynu w środowisku źródłowym i docelowym. Wygeneruj i przechowuj tokeny zgodnie z opisem w poniższych sekcjach.  

### <a name="generate-sas-token-for-export"></a>Generowanie tokenu SAS do eksportowania

Uruchom polecenie [az storage container generate-sas,][az-storage-container-generate-sas] aby wygenerować token SAS dla kontenera na źródłowym koncie magazynu używany do eksportowania artefaktów.

*Zalecane uprawnienia tokenu:* odczyt, zapis, lista, dodawanie. 

W poniższym przykładzie dane wyjściowe polecenia są przypisywane do EXPORT_SAS środowiskowej z prefiksem "?". Zaktualizuj `--expiry` wartość dla swojego środowiska:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Przechowywanie tokenu SAS do eksportu

Przechowuj token SAS w źródłowym magazynie kluczy platformy Azure za pomocą [narzędzia az keyvault secret set:][az-keyvault-secret-set]

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>Generowanie tokenu SAS do zaimportowania

Uruchom polecenie [az storage container generate-sas,][az-storage-container-generate-sas] aby wygenerować token SAS dla kontenera na docelowym koncie magazynu używany do importowania artefaktów.

*Zalecane uprawnienia tokenu:* odczyt, usuwanie, lista

W poniższym przykładzie dane wyjściowe polecenia są przypisywane do IMPORT_SAS środowiskowej z prefiksem "?". Zaktualizuj `--expiry` wartość dla swojego środowiska:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>Przechowywanie tokenu SAS do zaimportowania

Przechowuj token SAS w docelowym magazynie kluczy platformy Azure za pomocą [narzędzia az keyvault secret set:][az-keyvault-secret-set]

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Tworzenie pliku ExportPipeline za pomocą Resource Manager

Utwórz zasób ExportPipeline dla źródłowego rejestru kontenerów przy użyciu Azure Resource Manager wdrożenia szablonu.

Skopiuj plik ExportPipeline Resource Manager [szablonu](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) do folderu lokalnego.

Wprowadź następujące wartości parametrów w pliku `azuredeploy.parameters.json` :

|Parametr  |Wartość  |
|---------|---------|
|registryName (nazwa rejestru)     | Nazwa źródłowego rejestru kontenerów      |
|exportPipelineName     |  Nazwa wybierana dla potoku eksportu       |
|Targeturi     |  URI kontenera magazynu w środowisku źródłowym (element docelowy potoku eksportu).<br/>Przykład: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Nazwa źródłowego magazynu kluczy  |
|sasTokenSecretName  | Nazwa klucza tajnego tokenu sygnatury dostępu współdzielonego w magazynie kluczy źródłowych <br/>Przykład: acrexportsas

### <a name="export-options"></a>Opcje eksportowania

Właściwość `options` potoków eksportu obsługuje opcjonalne wartości logiczne. Zalecane są następujące wartości:

|Parametr  |Wartość  |
|---------|---------|
|opcje | OverwriteBlobs — zastępowanie istniejących docelowych obiektów blob<br/>ContinueOnErrors — kontynuuj eksportowanie pozostałych artefaktów w rejestrze źródłowym, jeśli jeden eksport artefaktu zakończy się niepowodzeniem.

### <a name="create-the-resource"></a>Tworzenie zasobu

Uruchom [az deployment group create,][az-deployment-group-create] aby utworzyć zasób o nazwie *exportPipeline,* jak pokazano w poniższych przykładach. Domyślnie przy pierwszej opcji przykładowy szablon włącza tożsamość przypisaną przez system w zasobie ExportPipeline. 

Drugą opcją jest podanie tożsamości przypisanej przez użytkownika do zasobu. (Tworzenie tożsamości przypisanej przez użytkownika nie jest wyświetlane).

W przypadku każdej z tych opcji szablon konfiguruje tożsamość w celu uzyskania dostępu do tokenu SAS w magazynie kluczy eksportu. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Opcja 1. Tworzenie zasobu i włączanie tożsamości przypisanej przez system

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Opcja 2. Tworzenie zasobu i zapewnianie tożsamości przypisanej przez użytkownika

W tym poleceniu podaj identyfikator zasobu tożsamości przypisanej przez użytkownika jako dodatkowy parametr.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

W danych wyjściowych polecenia zanotuj identyfikator zasobu `id` () potoku. Tę wartość można przechowywać w zmiennej środowiskowej do późniejszego użycia, uruchamiając [az deployment group show][az-deployment-group-show]. Na przykład:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Tworzenie pliku ImportPipeline za pomocą Resource Manager 

Utwórz zasób ImportPipeline w docelowym rejestrze kontenerów przy użyciu Azure Resource Manager szablonu. Domyślnie potok jest włączony do automatycznego importowania, gdy konto magazynu w środowisku docelowym ma obiekt blob artefaktu.

Skopiuj plik ImportPipeline Resource Manager [szablonu](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) do folderu lokalnego.

Wprowadź następujące wartości parametrów w pliku `azuredeploy.parameters.json` :

Parametr  |Wartość  |
|---------|---------|
|registryName (nazwa rejestru)     | Nazwa docelowego rejestru kontenerów      |
|importPipelineName     |  Nazwa wybierana dla potoku importowania       |
|Sourceuri     |  URI kontenera magazynu w środowisku docelowym (źródło potoku importu).<br/>Przykład: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  Nazwa docelowego magazynu kluczy |
|sasTokenSecretName     |  Nazwa klucza tajnego tokenu sygnatury dostępu współdzielonego w docelowym magazynie kluczy<br/>Przykład: acr importsas |

### <a name="import-options"></a>Opcje importowania

Właściwość `options` potoku importu obsługuje opcjonalne wartości logiczne. Zalecane są następujące wartości:

|Parametr  |Wartość  |
|---------|---------|
|opcje | OverwriteTags — zastępowanie istniejących tagów docelowych<br/>DeleteSourceBlobOnSuccess — usuwanie źródłowego obiektu blob magazynu po pomyślnym zaimportowaniu do rejestru docelowego<br/>ContinueOnErrors — kontynuuj importowanie pozostałych artefaktów w rejestrze docelowym, jeśli importowanie jednego artefaktu zakończy się niepowodzeniem.

### <a name="create-the-resource"></a>Tworzenie zasobu

Uruchom [az deployment group create,][az-deployment-group-create] aby utworzyć zasób o nazwie *importPipeline,* jak pokazano w poniższych przykładach. Domyślnie przy pierwszej opcji przykładowy szablon włącza tożsamość przypisaną przez system w zasobie ImportPipeline. 

Drugą opcją jest podanie tożsamości przypisanej przez użytkownika do zasobu. (Tworzenie tożsamości przypisanej przez użytkownika nie jest wyświetlane).

W przypadku każdej z tych opcji szablon konfiguruje tożsamość w celu uzyskania dostępu do tokenu SAS w magazynie kluczy importu. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Opcja 1. Tworzenie zasobu i włączanie tożsamości przypisanej przez system

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Opcja 2. Tworzenie zasobu i zapewnianie tożsamości przypisanej przez użytkownika

W tym poleceniu podaj identyfikator zasobu tożsamości przypisanej przez użytkownika jako dodatkowy parametr.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Jeśli planujesz ręcznie uruchomić importowanie, zanotuj identyfikator zasobu ( `id` ) potoku. Tę wartość można przechowywać w zmiennej środowiskowej do późniejszego użycia, uruchamiając [polecenie az deployment group show.][az-deployment-group-show] Na przykład:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Tworzenie potoku PipelineRun do eksportowania za pomocą Resource Manager 

Utwórz zasób PipelineRun dla źródłowego rejestru kontenerów przy użyciu Azure Resource Manager szablonu. Ten zasób uruchamia utworzony wcześniej zasób ExportPipeline i eksportuje określone artefakty z rejestru kontenerów jako obiekt blob do źródłowego konta magazynu.

Skopiuj potok PipelineRun Resource Manager [plików szablonów](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) do folderu lokalnego.

Wprowadź następujące wartości parametrów w pliku `azuredeploy.parameters.json` :

|Parametr  |Wartość  |
|---------|---------|
|registryName (nazwa rejestru)     | Nazwa źródłowego rejestru kontenerów      |
|pipelineRunName     |  Nazwa, która jest wybierana dla uruchomienia       |
|pipelineResourceId     |  Identyfikator zasobu potoku eksportu.<br/>Przykład: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|Targetname     |  Nazwa obiektu blob artefaktów wyeksportowanego na źródłowe konto magazynu, takiego jak *myblob*
|Artefakty | Tablica artefaktów źródłowych do przeniesienia jako tagi lub skróty manifestu<br/>Przykład: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

W przypadku ponownego uruchomienia zasobu PipelineRun o identycznych właściwościach należy również użyć właściwości [forceUpdateTag.](#redeploy-pipelinerun-resource)

Uruchom [az deployment group create,][az-deployment-group-create] aby utworzyć zasób PipelineRun. W poniższym przykładzie nazwa wdrożenia *exportPipelineRun*.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Do późniejszego użycia przechowuj identyfikator zasobu uruchomienia potoku w zmiennej środowiskowej:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Eksportowanie artefaktów może potrwać kilka minut. Po pomyślnym zakończeniu wdrażania zweryfikuj eksport artefaktu, wymieniając wyeksportowany obiekt blob w *kontenerze transferu* źródłowego konta magazynu. Na przykład uruchom polecenie [az storage blob list:][az-storage-blob-list]

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Transfer obiektu blob (opcjonalnie) 

Użyj narzędzia AzCopy lub innych metod, aby przenieść dane [obiektów blob](../storage/common/storage-use-azcopy-v10.md#transfer-data) ze źródłowego konta magazynu na docelowe konto magazynu.

Na przykład następujące polecenie kopiuje myblob z kontenera transferu na koncie źródłowym do [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) kontenera *transferu* na koncie docelowym.  Jeśli obiekt blob istnieje na koncie docelowym, zostanie zastąpiony. Uwierzytelnianie używa tokenów SAS z odpowiednimi uprawnieniami dla kontenerów źródłowych i docelowych. (Kroki tworzenia tokenów nie są wyświetlane).

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Wyzwalanie zasobu ImportPipeline

Jeśli parametr ImportPipeline (wartość domyślna) został włączony, potok jest wyzwalany po skopiowaniu obiektu `sourceTriggerStatus` blob na docelowe konto magazynu. Importowanie artefaktów może potrwać kilka minut. Po pomyślnym zakończeniu importowania zweryfikuj importowanie artefaktów, wymieniając repozytoria w docelowym rejestrze kontenerów. Na przykład uruchom [az acr repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

Jeśli parametr potoku importu nie został włączyć, uruchom zasób ImportPipeline ręcznie, jak pokazano `sourceTriggerStatus` w poniższej sekcji. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Tworzenie potoku PipelineRun do zaimportowania Resource Manager (opcjonalnie) 
 
Możesz również użyć zasobu PipelineRun, aby wyzwolić potok ImportPipeline na użytek importowania artefaktów do docelowego rejestru kontenerów.

Skopiuj plik PipelineRun Resource Manager [szablonu](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) do folderu lokalnego.

Wprowadź następujące wartości parametrów w pliku `azuredeploy.parameters.json` :

|Parametr  |Wartość  |
|---------|---------|
|registryName     | Nazwa docelowego rejestru kontenerów      |
|pipelineRunName     |  Nazwa wybierana dla uruchomienia       |
|pipelineResourceId     |  Identyfikator zasobu potoku importu.<br/>Przykład: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|Nazwaobiektu     |  Nazwa istniejącego obiektu blob dla wyeksportowanych artefaktów na koncie magazynu, na przykład *myblob*

W przypadku ponownego uruchomienia zasobu PipelineRun o identycznych właściwościach należy również użyć właściwości [forceUpdateTag.](#redeploy-pipelinerun-resource)

Uruchom [az deployment group create,][az-deployment-group-create] aby uruchomić zasób.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Do późniejszego użycia przechowuj identyfikator zasobu uruchomienia potoku w zmiennej środowiskowej:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Po pomyślnym zakończeniu wdrażania zweryfikuj import artefaktów, wymieniając repozytoria w docelowym rejestrze kontenerów. Na przykład uruchom [az acr repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>Ponowne wdychaj zasób PipelineRun

W przypadku ponownego uruchomienia zasobu PipelineRun o *identycznych* właściwościach należy użyć właściwości **forceUpdateTag.** Ta właściwość wskazuje, że zasób PipelineRun powinien zostać ponownie utworzony, nawet jeśli konfiguracja nie uległa zmianie. Upewnij się, że tag forceUpdateTag jest inny przy każdym ponownego wdychowania zasobu PipelineRun. W poniższym przykładzie zostanie ponownie utworzony potok PipelineRun do wyeksportowania. Bieżąca wartość datetime jest używana do ustawienia forceUpdateTag, dzięki czemu ta właściwość jest zawsze unikatowa.

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

Następujące przykładowe polecenia używają [polecenia az resource delete,][az-resource-delete] aby usunąć zasoby potoku utworzone w tym artykule. Identyfikatory zasobów były wcześniej przechowywane w zmiennych środowiskowych.

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

* **Template deployment lub błędy**
  * Jeśli uruchomienie potoku zakończy się niepowodzeniem, sprawdź `pipelineRunErrorMessage` właściwość zasobu uruchomienia.
  * Aby uzyskać informacje o typowych błędach wdrażania szablonów, zobacz [Troubleshoot ARM template deployments (Rozwiązywanie problemów z wdrożeniami szablonów usługi ARM)](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **Problemy z eksportowaniem lub importowaniem obiektów blob magazynu**
  * Token SAS może wygasł lub może mieć niewystarczające uprawnienia do określonego uruchomienia eksportu lub importu
  * Istniejący obiekt blob magazynu na źródłowym koncie magazynu może nie zostać zastąpiony podczas wielu przebiegów eksportu. Upewnij się, że opcja OverwriteBlob jest ustawiona w uruchomieniu eksportu, a token SAS ma wystarczające uprawnienia.
  * Obiekt blob magazynu na docelowym koncie magazynu może nie zostać usunięty po pomyślnym uruchomieniu importu. Upewnij się, że opcja DeleteBlobOnSuccess jest ustawiona podczas uruchamiania importowania, a token SAS ma wystarczające uprawnienia.
  * Nie utworzono ani nie usunięto obiektu blob magazynu. Upewnij się, że kontener określony w uruchomieniu eksportu lub importu istnieje lub że istnieje określony obiekt blob magazynu na czas ręcznego uruchamiania importu. 
* **Problemy dotyczące programu AzCopy**
  * Zobacz [Rozwiązywanie problemów z narzędziem AzCopy.](../storage/common/storage-use-azcopy-configure.md)  
* **Problemy z transferem artefaktów**
  * Nie wszystkie artefakty lub żadne artefakty są przenoszone. Potwierdź pisownię artefaktów w uruchomieniu eksportu oraz nazwę obiektu blob w przebiegach eksportu i importu. Upewnij się, że przesyłasz maksymalnie 50 artefaktów.
  * Uruchomienie potoku mogło nie zostać zakończone. Uruchomienie eksportu lub importu może trochę potrwać. 
  * W przypadku innych problemów z potokiem podaj identyfikator [korelacji](../azure-resource-manager/templates/deployment-history.md) wdrożenia uruchomienia eksportu lub uruchomienia importu do Azure Container Registry zespołem.
* **Problemy z ściąganiem obrazu w środowisku izolowanym fizycznie**
  * Jeśli podczas próby ściągnnięcia obrazu w środowisku izolowanym fizycznie występują błędy dotyczące warstw obcych lub próby rozwiązania problemu mcr.microsoft.com, manifest obrazu prawdopodobnie ma warstwy niedystrybuowalne. Ze względu na charakter fizycznie izolowanego środowiska ściąganie tych obrazów często zakończy się niepowodzeniem. Możesz potwierdzić, że tak jest, sprawdzając manifest obrazu pod celu sprawdzenia, czy nie ma żadnych odwołań do rejestrów zewnętrznych. W takim przypadku należy wypchnąć warstwy niedystrybuowalne do usługi ACR w chmurze publicznej przed wdrożeniem uruchomienia potoku eksportu dla tego obrazu. Aby uzyskać wskazówki dotyczące tego, jak to zrobić, Jak mogę wypchnąć warstwy [niedystrybuowalne do rejestru?](./container-registry-faq.md#how-do-i-push-non-distributable-layers-to-a-registry)

## <a name="next-steps"></a>Następne kroki

Aby zaimportować obrazy pojedynczego kontenera do rejestru kontenerów platformy Azure z rejestru publicznego lub innego rejestru prywatnego, zobacz az acr import command reference (Odwołanie do polecenia [az acr import).][az-acr-import]

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
