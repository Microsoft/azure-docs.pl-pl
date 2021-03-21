---
title: Wdrażanie szablonów za pomocą usługi Azure Deployment Manager
description: Dowiedz się, jak wdrażać zasoby platformy Azure za pomocą szablonów Menedżer zasobów za pomocą usługi Azure Deployment Manager.
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 95d5067eccff5c847588834061db8454f75e55d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627586"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Samouczek: używanie usługi Azure Deployment Manager z szablonami usługi Resource Manager (publiczna wersja zapoznawcza)

Dowiedz się, jak używać usługi [Azure Deployment Manager](./deployment-manager-overview.md), aby wdrażać aplikacje w wielu regionach. Jeśli wolisz szybszym podejściem, [usługa Azure Deployment Manager szybki start](https://github.com/Azure-Samples/adm-quickstart) tworzy wymagane konfiguracje w ramach subskrypcji i dostosowuje artefakty do wdrożenia aplikacji w wielu regionach. Przewodnik Szybki Start wykonuje te same zadania, co w tym samouczku.

Aby użyć usługi Deployment Manager, musisz utworzyć dwa szablony:

* **Szablon topologii**: opisuje zasoby platformy Azure składające się na aplikację oraz miejsce ich wdrożenia.
* **Szablon wprowadzania**: opisuje kroki do wykonania podczas wdrażania aplikacji.

> [!IMPORTANT]
> Jeśli Twoja subskrypcja jest oznaczona jako przeznaczona do testowania nowych funkcji platformy Azure, możesz użyć usługi Azure Deployment Manager tylko do wdrożenia w regionach Kanaryjskich.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Omówienie scenariusza
> * Pobieranie plików samouczka
> * Przygotowywanie artefaktów
> * Tworzenie tożsamości zarządzanej zdefiniowanej przez użytkownika
> * Tworzenie szablonu topologii usługi
> * Tworzenie szablonu wprowadzania
> * Wdrażanie szablonów
> * Weryfikowanie wdrożenia
> * Wdrażanie nowszej wersji
> * Czyszczenie zasobów

Dodatkowe zasoby:

* [Dokumentacja interfejsu API REST usługi Azure Deployment Manager](/rest/api/deploymentmanager/).
* [Samouczek: korzystanie z kontroli kondycji w usłudze Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Pewne doświadczenie w opracowywaniu [szablonów usługi Azure Resource Manager](overview.md).
* Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](/powershell/azure/get-started-azureps).
* Polecenia cmdlet usługi Deployment Manager. Aby zainstalować te polecenia cmdlet wersji wstępnej, potrzebujesz najnowszej wersji modułu PowerShellGet. Aby uzyskać najnowszą wersję, zobacz [Installing PowerShellGet (Instalowanie modułu PowerShellGet)](/powershell/scripting/gallery/installing-psget). Po zainstalowaniu modułu PowerShellGet zamknij okno programu PowerShell. Otwórz nowe okno programu PowerShell z podwyższonym poziomem uprawnień i użyj następującego polecenia:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>Omówienie scenariusza

Szablon topologii usług zawiera opis zasobów platformy Azure, które składają się na usługę i gdzie należy je wdrożyć. Definicja topologii usługi ma następującą hierarchię:

* Topologia usługi
  * Usługi
    * Jednostki usługi

Na poniższym diagramie przedstawiono topologię usługi używaną w tym samouczku:

![Diagram przedstawiający scenariusz samouczka dotyczącego usługi Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

W zachodnich stanach USA i regionach Wschodnie stany USA są przydzielono dwie usługi. Każda usługa ma dwie jednostki usługi: aplikację sieci Web frontonu i konto magazynu zaplecza. Definicje jednostek usługi zawierają linki do plików szablonów i parametrów, które tworzą aplikacje sieci Web i konta magazynu.

## <a name="download-the-tutorial-files"></a>Pobieranie plików samouczka

1. Pobierz [szablony i artefakty](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) używane w tym samouczku.
1. Rozpakuj pliki do lokalizacji na komputerze.

W folderze głównym znajdują się dwa foldery:

* _ADMTemplates_: zawiera szablony usługi Deployment Manager, które obejmują następujące pliki:
  * _CreateADMServiceTopology.json_
  * _CreateADMServiceTopology.Parameters.json_
  * _CreateADMRollout.json_
  * _CreateADMRollout.Parameters.json_
* _ArtifactStore_: zawiera artefakty szablonu oraz artefakty binarne. Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).

Istnieją dwa zestawy szablonów. Jeden zestaw to Deployment Manager szablonów, które są używane do wdrożenia topologii usługi i wdrożenia. Drugi zestaw jest wywoływany z jednostek usługi w celu utworzenia usług sieci Web i kont magazynu.

## <a name="prepare-the-artifacts"></a>Przygotowywanie artefaktów

Folder ArtifactStore z plików do pobrania zawiera dwa foldery:

![Diagram przedstawiający źródło artefaktów samouczka dotyczącego usługi Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* Folder _templates_: zawiera artefakty szablonu. Foldery _1.0.0.0_ i _1.0.0.1_ reprezentują dwie wersje artefaktów binarnych. W każdej wersji istnieje folder dla każdej usługi: _ServiceEUS_ (Service Wschodnie stany USA) i _ServiceWUS_ (Service zachodnie stany USA). Każda usługa ma parę plików — szablonu i parametrów — na potrzeby tworzenia konta magazynu, a także kolejną parę na potrzeby tworzenia aplikacji internetowej. Szablon aplikacji internetowej wywołuje skompresowany pakiet, który zawiera pliki aplikacji internetowej. Skompresowany plik to artefakt binarny przechowywany w folderze binaries.
* Folder _binaries_: zawiera artefakty binarne. Foldery _1.0.0.0_ i _1.0.0.1_ reprezentują dwie wersje artefaktów binarnych. W każdej wersji istnieje jeden plik ZIP dla programu, aby utworzyć aplikację sieci Web w lokalizacji zachodnie stany USA, a drugi plik zip, aby utworzyć aplikację sieci Web w lokalizacji Wschodnie stany USA.

Dwie wersje (1.0.0.0 i 1.0.0.1) dotyczą [wdrażania poprawek](#deploy-the-revision). Chociaż artefakty szablonu oraz artefakty binarne mają dwie wersje, tylko artefakty binarne różnią się w tych dwóch wersjach. W praktyce artefakty binarne są aktualizowane częściej w porównaniu do artefaktów szablonu.

1. Otwórz plik _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json_ w edytorze tekstów. Jest to podstawowy szablon służący do tworzenia konta magazynu.
1. Otwórz plik _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json_.

    ![Szablon tworzenia aplikacji internetowej w samouczku dotyczącym usługi Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Szablon wywołuje pakiet wdrożeniowy, który zawiera pliki aplikacji internetowej. W tym samouczku skompresowany pakiet zawiera tylko plik _index.html_ .
1. Otwórz plik _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json_.

    ![Szablon tworzenia aplikacji internetowej w samouczku dotyczącym usługi Azure Deployment Manager — parametry containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Wartość `deployPackageUri` jest ścieżką do pakietu wdrożeniowego. Parametr zawiera `$containerRoot` zmienną. Wartość `$containerRoot` jest podana w [szablonie wdrożenia](#create-the-rollout-template) przez połączenie lokalizacji SAS źródła artefaktu, głównego artefaktu i `deployPackageUri` .
1. Otwórz plik _\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html_.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    KOD HTML pokazuje lokalizację i informacje o wersji. Plik binarny w folderze _1.0.0.1_ pokazuje _wersję 1.0.0.1_. Po wdrożeniu usługi możesz przejść do tych stron.
1. Zapoznaj się z innymi plikami artefaktów. Dzięki temu lepiej zrozumiesz scenariusz.

Artefakty szablonu są używane przez szablon topologii usługi, a artefakty binarne są używane przez szablon wprowadzania. Szablony topologii i wprowadzania definiują zasób źródła artefaktu na platformie Azure, który jest zasobem używanym do kierowania usługi Resource Manager do szablonu i artefaktów binarnych używanych we wdrożeniu. Aby uprościć samouczek, jedno konto magazynu jest używane do przechowywania artefaktów szablonu i artefaktów binarnych. Oba źródła artefaktów wskazują na to samo konto magazynu.

Uruchom następujący skrypt programu PowerShell, aby utworzyć grupę zasobów, utworzyć kontener magazynu, utworzyć kontener obiektów blob, przekazać pobrane pliki, a następnie utworzyć token sygnatury dostępu współdzielonego.

> [!IMPORTANT]
> `projectName` skrypt programu PowerShell służy do generowania nazw usług platformy Azure, które są wdrożone w tym samouczku. Różne usługi platformy Azure mają różne wymagania dotyczące nazw. Aby upewnić się, że wdrożenie zakończyło się pomyślnie, wybierz nazwę o długości mniejszej niż 12 znaków i cyfr.
> Zapisz kopię nazwy projektu. Używasz tego samego `projectName` samouczka.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

Utwórz kopię adresu URL za pomocą tokenu sygnatury dostępu współdzielonego. Ten adres URL jest wymagany do wypełnienia pola w dwóch plikach parametrów: plik parametrów topologii i plik parametrów wdrożenia.

Otwórz kontener z Azure Portal i sprawdź, czy pliki _binarne_ i foldery _szablonów_ są przekazywane.

## <a name="create-the-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

W dalszej części samouczka wdrożysz wprowadzenie. Tożsamość zarządzana przypisana przez użytkownika jest potrzebna do wykonania działań związanych z wdrożeniem (np. wdrożenia aplikacji internetowych i konta magazynu). Ta tożsamość musi uzyskać dostęp do subskrypcji platformy Azure, w której wdrażasz usługę, a także musi mieć wystarczające uprawnienia do ukończenia wdrażania artefaktu.

Musisz utworzyć tożsamość zarządzaną przypisaną przez użytkownika i skonfigurować kontrolę dostępu dla subskrypcji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Tworzenie [tożsamości zarządzanej przypisanej przez użytkownika](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
1. W portalu wybierz opcję **Subskrypcje** z menu po lewej stronie, a następnie wybierz swoją subskrypcję.
1. Wybierz opcję **Kontrola dostępu (IAM)**, a następnie wybierz opcję **Dodaj przypisanie roli**.
1. Wprowadź lub wybierz poniższe wartości:

    ![Samouczek dotyczący usługi Azure Deployment Manager — kontrola dostępu tożsamości zarządzanej przypisanej przez użytkownika](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Rola**: udziel wystarczających uprawnień, aby ukończyć wdrażanie artefaktu (aplikacje internetowe i konta magazynu). Wybierz opcję **Współautor** w tym samouczku. W praktyce chcesz ograniczyć uprawnienia do minimum.
    * **Przypisz dostęp do**: wybierz opcję **tożsamość zarządzana przypisana przez użytkownika**.
    * Wybierz tożsamość zarządzaną przypisaną przez użytkownika, która została utworzona wcześniej w tym samouczku.
1. Wybierz pozycję **Zapisz**.

## <a name="create-the-service-topology-template"></a>Tworzenie szablonu topologii usługi

Otwórz plik _\ADMTemplates\CreateADMServiceTopology.json_.

### <a name="the-parameters"></a>Parametry

Szablon zawiera następujące parametry:

* `projectName`: Ta nazwa służy do tworzenia nazw dla Deployment Manager zasobów. Na przykład przy użyciu **demonstracji** nazwa topologii usługi jest **demonstracją** servicetopology. Nazwy zasobów są zdefiniowane w `variables` sekcji szablonu.
* `azureResourcelocation`: Aby uprościć samouczek, wszystkie zasoby współużytkują tę lokalizację, chyba że zostanie ona określona inaczej.
* `artifactSourceSASLocation`: Identyfikator URI sygnatury dostępu współdzielonego do kontenera obiektów blob, w którym są przechowywane pliki parametrów i szablonów jednostek usługi do wdrożenia. Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
* `templateArtifactRoot`: Ścieżka przesunięcia z kontenera obiektów blob, w którym są przechowywane szablony i parametry. Wartość domyślna to _templates/1.0.0.0_. Nie zmieniaj tej wartości, chyba że chcesz zmienić strukturę folderów wyjaśnioną w sekcji [Przygotowywanie artefaktów](#prepare-the-artifacts). W tym samouczku używa się ścieżek względnych. Pełna ścieżka jest zbudowana przez złączenie `artifactSourceSASLocation` , `templateArtifactRoot` , i `templateArtifactSourceRelativePath` (lub `parametersArtifactSourceRelativePath` ).
* `targetSubscriptionID`: Identyfikator subskrypcji, do której mają zostać wdrożone i rozliczane zasoby Deployment Manager. Użyj identyfikatora swojej subskrypcji w tym samouczku.

### <a name="the-variables"></a>Zmienne

Sekcja zmienne definiuje nazwy zasobów, lokalizacje platformy Azure dla dwóch usług: `ServiceWUS` i `ServiceEUS` i ścieżki artefaktów:

![Samouczek dotyczący usługi Azure Deployment Manager — zmienne szablonu topologii](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Porównaj ścieżki artefaktów ze strukturą folderu przekazaną do konta magazynu. Należy zauważyć, że ścieżki artefaktów są ścieżkami względnymi. Pełna ścieżka jest zbudowana przez złączenie `artifactSourceSASLocation` , `templateArtifactRoot` , i `templateArtifactSourceRelativePath` (lub `parametersArtifactSourceRelativePath` ).

### <a name="the-resources"></a>Zasoby

Na poziomie głównym są zdefiniowane dwa zasoby: *Źródło artefaktu* i *topologię usługi*.

Definicja źródła artefaktu to:

![Samouczek dotyczący usługi Azure Deployment Manager — źródło artefaktu w zasobach szablonu topologii](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Poniższy zrzut ekranu przedstawia tylko pewne części definicji topologii usługi, usług oraz jednostek usługi:

![Samouczek dotyczący usługi Azure Deployment Manager — topologia usługi w zasobach szablonu topologii](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* `artifactSourceId`: Służy do kojarzenia zasobu źródłowego artefaktu z zasobem topologii usługi.
* `dependsOn`: Wszystkie zasoby topologii usług są zależne od zasobu źródłowego artefaktu.
* `artifacts`: Wskaż artefakty szablonu. W tym miejscu używa się ścieżek względnych. Pełna ścieżka jest zbudowana przez złączenie `artifactSourceSASLocation` (zdefiniowane w źródle artefaktu), `artifactRoot` (zdefiniowane w źródle artefaktu) i `templateArtifactSourceRelativePath` (lub `parametersArtifactSourceRelativePath` ).

### <a name="topology-parameters-file"></a>Plik parametrów topologii

Możesz utworzyć plik parametrów używany z szablonem topologii.

1. Otwórz _\ADMTemplates\CreateADMServiceTopology.Parameters.js_ w Visual Studio Code lub dowolnym edytorze tekstu.
1. Wprowadź wartości parametrów:

    * `projectName`: Wprowadź ciąg z 4-5 znaków. Ta nazwa jest używana do tworzenia unikatowych nazw zasobów platformy Azure.
    * `azureResourceLocation`: Jeśli nie znasz już lokalizacji platformy Azure, w tym samouczku Użyj **centrali** .
    * `artifactSourceSASLocation`: Wprowadź identyfikator URI sygnatury dostępu współdzielonego do katalogu głównego (kontenera obiektów BLOB), w którym są przechowywane pliki parametrów i szablonów jednostek usługi do wdrożenia.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
    * `templateArtifactRoot`: Jeśli nie zmienisz struktury folderów artefaktów, użyj _szablonów/1.0.0.0_ w tym samouczku.

> [!IMPORTANT]
> Szablon topologii oraz szablon wprowadzania współdzielą niektóre parametry. Te parametry muszą mieć takie same wartości. Te parametry to: `projectName` , `azureResourceLocation` i `artifactSourceSASLocation` (oba źródła artefaktów współużytkują to samo konto magazynu w tym samouczku).

## <a name="create-the-rollout-template"></a>Tworzenie szablonu wprowadzania

Otwórz plik _\ADMTemplates\CreateADMRollout.json_.

### <a name="the-parameters"></a>Parametry

Szablon zawiera następujące parametry:

![Samouczek dotyczący usługi Azure Deployment Manager — parametry szablonu wprowadzania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* `projectName`: Ta nazwa służy do tworzenia nazw dla Deployment Manager zasobów. Na przykład przy użyciu **demonstracji** nazwa wdrożenia to wprowadzenie **demonstracyjne**. Nazwy są zdefiniowane w `variables` sekcji szablonu.
* `azureResourcelocation`: Aby uprościć samouczek, wszystkie zasoby Deployment Manager korzystają z tej lokalizacji, chyba że zostanie ona określona inaczej.
* `artifactSourceSASLocation`: Identyfikator URI sygnatury dostępu współdzielonego do katalogu głównego (kontenera obiektów BLOB), w którym są przechowywane pliki parametrów i szablonów jednostek usługi do wdrożenia. Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
* `binaryArtifactRoot`: Wartość domyślna to _plików binarnych/1.0.0.0_. Nie zmieniaj tej wartości, chyba że chcesz zmienić strukturę folderów wyjaśnioną w sekcji [Przygotowywanie artefaktów](#prepare-the-artifacts). W tym samouczku używa się ścieżek względnych. Pełna ścieżka jest zbudowana przez złączenie `artifactSourceSASLocation` , `binaryArtifactRoot` i `deployPackageUri` określone w _CreateWebApplicationParameters.json_. Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
* `managedIdentityID`: Tożsamość zarządzana przypisana przez użytkownika, która wykonuje akcje wdrożenia. Zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Zmienne

`variables`Sekcja definiuje nazwy zasobów. Upewnij się, że nazwa topologii usługi, nazwy usług oraz nazwy jednostek usług pasują do nazw określonych w [szablonie topologii](#create-the-service-topology-template).

![Samouczek dotyczący usługi Azure Deployment Manager — zmienne szablonu wprowadzania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Zasoby

Na poziomie głównym istnieją trzy zdefiniowane zasoby: źródło artefaktu, krok oraz wprowadzenie.

Definicja źródła artefaktu jest taka sama, jak definicja określona w szablonie topologii. Zobacz [Tworzenie szablonu topologii usługi](#create-the-service-topology-template), aby uzyskać więcej informacji.

Poniższy zrzut ekranu przedstawia `wait` definicję kroku:

![Samouczek dotyczący usługi Azure Deployment Manager — krok oczekiwania zasobów szablonu wprowadzania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Czas trwania używa [standardu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (wymagane są wielkie litery) to przykład 1-minutowego oczekiwania.

Poniższy zrzut ekranu przedstawia tylko niektóre części definicji wprowadzania:

![Samouczek dotyczący usługi Azure Deployment Manager — wprowadzanie zasobów szablonu wprowadzania](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* `dependsOn`: Zasób wdrażania zależy od zasobu źródłowego artefaktu i dowolnego zdefiniowanego kroku.
* `artifactSourceId`: Służy do kojarzenia zasobu źródłowego artefaktu z zasobem wdrażania.
* `targetServiceTopologyId`: Służy do kojarzenia zasobu topologii usługi z zasobem wdrażania.
* `deploymentTargetId`: Jest to identyfikator zasobu jednostki usługi dla zasobu topologii usługi.
* `preDeploymentSteps` i `postDeploymentSteps` : zawiera kroki wprowadzania. `wait`Krok jest wywoływany w szablonie.
* `dependsOnStepGroups`: Skonfiguruj zależności między grupami kroków.

### <a name="rollout-parameters-file"></a>Plik parametrów wprowadzania

Możesz utworzyć plik parametrów używany z szablonem wprowadzania.

1. Otwórz _\ADMTemplates\CreateADMRollout.Parameters.js_ w Visual Studio Code lub dowolnym edytorze tekstu.
1. Wprowadź wartości parametrów:

    * `projectName`: Wprowadź ciąg z 4-5 znaków. Ta nazwa jest używana do tworzenia unikatowych nazw zasobów platformy Azure.
    * `azureResourceLocation`: Określ lokalizację platformy Azure.
    * `artifactSourceSASLocation`: Wprowadź identyfikator URI sygnatury dostępu współdzielonego do katalogu głównego (kontenera obiektów BLOB), w którym są przechowywane pliki parametrów i szablonów jednostek usługi do wdrożenia.  Zobacz [Przygotowywanie artefaktów](#prepare-the-artifacts).
    * `binaryArtifactRoot`: Jeśli nie zmienisz struktury folderów artefaktów, użyj _plików binarnych/1.0.0.0_ w tym samouczku.
    * `managedIdentityID`: Wprowadź tożsamość zarządzaną przypisaną przez użytkownika. Zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](#create-the-user-assigned-managed-identity). Składnia jest następująca:

        ```json
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Szablon topologii oraz szablon wprowadzania współdzielą niektóre parametry. Te parametry muszą mieć takie same wartości. Te parametry to: `projectName` , `azureResourceLocation` i `artifactSourceSASLocation` (oba źródła artefaktów współużytkują to samo konto magazynu w tym samouczku).

## <a name="deploy-the-templates"></a>Wdrażanie szablonów

Programu Azure PowerShell można użyć do wdrażania szablonów.

1. Uruchom skrypt, aby wdrożyć topologię usługi.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    Jeśli skrypt zostanie uruchomiony z innej sesji programu PowerShell z poziomu tej, w której uruchomiono skrypt [przygotowania artefaktów](#prepare-the-artifacts) , należy najpierw wypełnić zmienne, które obejmują `$resourceGroupName` i `$filePath` .

    > [!NOTE]
    > `New-AzResourceGroupDeployment` jest wywołaniem asynchronicznym. Komunikat o powodzeniu oznacza, że wdrożenie zostało pomyślnie rozpoczęte. Aby sprawdzić wdrożenie, zobacz krok 2 i krok 4 tej procedury.

1. Sprawdź, czy topologia usługi oraz podstawowe zasoby zostały utworzone pomyślnie, korzystając z witryny Azure Portal:

    ![Samouczek dotyczący usługi Azure Deployment Manager — wdrożone zasoby topologii usługi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Opcja **Pokaż ukryte typy** musi być zaznaczona, aby wyświetlić zasoby.

1. <a id="deploy-the-rollout-template"></a>Wdrożenie szablonu wprowadzania:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

1. Sprawdź postęp wprowadzania przy użyciu następującego skryptu programu PowerShell:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Należy zainstalować polecenia cmdlet programu PowerShell usługi Deployment Manager przed uruchomieniem tego polecenia cmdlet. Zobacz [Wymagania wstępne](#prerequisites). `-Verbose`Parametr może służyć do wyświetlania całego danych wyjściowych.

    Następujący przykład przedstawia stan działania:

    ```Output
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Po pomyślnym wdrożeniu wdrożenia zobaczysz dwie więcej grup zasobów utworzonych dla każdej usługi.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. Przejdź do nowo utworzonych aplikacji sieci Web w ramach nowych grup zasobów utworzonych przez wdrożenie wdrożenia.
1. Otwórz aplikację internetową w przeglądarce internetowej. Sprawdź lokalizację i wersję pliku _index.html_ .

## <a name="deploy-the-revision"></a>Wdrażanie poprawki

Jeśli masz nową wersję (1.0.0.1) aplikacji internetowej. Możesz użyć poniższej procedury, aby ponownie wdrożyć aplikację internetową.

1. Otwórz _CreateADMRollout.Parameters.jsna_.
1. Aktualizacja `binaryArtifactRoot` do _plików binarnych/1.0.0.1_.
1. Ponownie wdróż wprowadzanie zgodnie z instrukcjami w sekcji [Wdrażanie szablonów](#deploy-the-rollout-template).
1. Sprawdź wdrożenie zgodnie z instrukcjami w sekcji [Weryfikowanie wdrożenia](#verify-the-deployment). Strona internetowa powinna teraz wyświetlać wersję 1.0.0.1.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
1. Użyj pola **Filtruj według nazwy**, aby zawęzić listę grup zasobów utworzonych w tym samouczku.

    * **&lt; projectName>RG**: zawiera zasoby Deployment Manager.
    * **&lt; ProjectName>ServiceWUSrg**: zawiera zasoby zdefiniowane przez ServiceWUS.
    * **&lt; ProjectName>ServiceEUSrg**: zawiera zasoby zdefiniowane przez ServiceEUS.
    * Grupa zasobów dla tożsamości zarządzanej zdefiniowanej przez użytkownika.
1. Wybierz nazwę grupy zasobów.
1. W górnym menu wybierz pozycję **Usuń grupę zasobów** .
1. Powtórz dwa ostatnie kroki, aby usunąć inne grupy zasobów utworzone w ramach tego samouczka.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia usługi Azure Deployment Manager. Aby zintegrować monitorowanie kondycji w usłudze Azure Deployment Manager, zobacz [Samouczek: używanie sprawdzania kondycji w usłudze azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
