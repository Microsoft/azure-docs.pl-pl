---
title: Dodaj repozytorium artefaktów do laboratorium w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak określić własne repozytorium artefaktów dla laboratorium w Azure DevTest Labs, aby przechowywać narzędzia niedostępne w repozytorium artefaktów publicznych.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5dd7d89020bf077e29b177f6871f43b52467b0d8
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97512015"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Dodawanie repozytorium artefaktów do laboratorium w DevTest Labs
DevTest Labs pozwala określić artefakt, który ma zostać dodany do maszyny wirtualnej podczas tworzenia maszyny wirtualnej lub po utworzeniu maszyny wirtualnej. Ten artefakt może być narzędziem lub aplikacją, która ma zostać zainstalowana na maszynie wirtualnej. Artefakty są zdefiniowane w pliku JSON załadowanym z repozytorium GitHub lub Azure DevOps git.

[Publiczne repozytorium artefaktów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), obsługiwane przez DevTest Labs, udostępnia wiele popularnych narzędzi dla systemów Windows i Linux. Link do tego repozytorium jest automatycznie dodawany do laboratorium. Możesz utworzyć własne repozytorium artefaktów z określonymi narzędziami, które nie są dostępne w publicznym repozytorium artefaktów. Aby dowiedzieć się więcej na temat tworzenia artefaktów niestandardowych, zobacz [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md).

Ten artykuł zawiera informacje dotyczące sposobu dodawania niestandardowego repozytorium artefaktów przy użyciu Azure Portal, szablonów zarządzania zasobami platformy Azure i Azure PowerShell. Możesz zautomatyzować Dodawanie repozytorium artefaktów do laboratorium, pisząc skrypty programu PowerShell lub interfejsu wiersza polecenia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby dodać repozytorium do laboratorium, najpierw Pobierz informacje o kluczu z repozytorium. W poniższych sekcjach opisano, jak uzyskać wymagane informacje dotyczące repozytoriów hostowanych w witrynie **GitHub** lub **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Pobieranie adresu URL klonowania repozytorium GitHub i osobistego tokenu dostępu

1. Przejdź do strony głównej repozytorium GitHub, która zawiera definicje artefaktów lub Menedżer zasobów szablonów.
2. Wybierz przycisk **Clone or download** (Sklonuj lub pobierz).
3. Aby skopiować adres URL do schowka, wybierz przycisk **adres URL klonowania https** . Zapisz adres URL do późniejszego użycia.
4. W prawym górnym rogu usługi GitHub wybierz obraz profilu, a następnie wybierz pozycję **Ustawienia**.
5. W menu **Ustawienia osobiste** po lewej stronie wybierz pozycję **Ustawienia dewelopera**.
6. Wybierz pozycję **osobiste tokeny dostępu** w menu po lewej stronie.
7. Wybierz pozycję **Generuj nowy token**.
8. Na stronie **nowy osobisty token dostępu** w obszarze **Opis tokenu** wprowadź opis. Zaakceptuj domyślne elementy w obszarze **Wybierz zakresy**, a następnie wybierz pozycję **Generuj token**.
9. Zapisz wygenerowany token. Token jest używany później.
10. Zamknij witrynę GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Pobierz adres URL klonowania Azure Repos i osobistego tokenu dostępu
1. Przejdź do strony głównej kolekcji zespołu (na przykład `https://contoso-web-team.visualstudio.com` ), a następnie wybierz projekt.
2. Na stronie głównej projektu wybierz pozycję **kod**.
3. Aby wyświetlić adres URL klonowania, na stronie **kod** projektu wybierz pozycję **Klonuj**.
4. Zapisz adres URL. Ten adres URL jest używany później.
5. Aby utworzyć osobisty token dostępu, w menu rozwijanym konto użytkownika wybierz pozycję **mój profil**.
6. Na stronie informacje o profilu wybierz pozycję **zabezpieczenia**.
7. Na karcie **zabezpieczenia > osobiste tokeny dostępu** wybierz pozycję **+ nowy token**.
8. Na stronie **Tworzenie nowego osobistego tokenu dostępu** :
   1. Wprowadź **nazwę** tokenu.
   2. Z listy **organizacja** wybierz pozycję **wszystkie dostępne organizacje**.
   3. Na liście **wygaśnięcie (UTC)** wybierz pozycję **90 dni** lub niestandardowy okres ważności.
   4. Wybierz opcję **pełny dostęp** dla zakresów.
   5. Wybierz pozycję **Utwórz**.
9. Nowy token zostanie wyświetlony na liście **osobiste tokeny dostępu** . Wybierz pozycję **Kopiuj token**, a następnie Zapisz wartość tokenu do późniejszego użycia.
10. Przejdź do sekcji Łączenie laboratorium z repozytorium.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Ta sekcja zawiera kroki umożliwiające dodanie repozytorium artefaktów do laboratorium w Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **więcej usług**, a następnie wybierz pozycję **DevTest Labs** z listy usług.
3. Z listy laboratoriów wybierz laboratorium.
4. W menu po lewej stronie wybierz pozycję **Konfiguracja i zasady** .
5. Wybierz pozycję **repozytoria** w sekcji **Zasoby zewnętrzne** w menu po lewej stronie.
6. Wybierz pozycję **+ Dodaj** na pasku narzędzi.

    ![Przycisk Dodaj repozytorium](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na stronie **repozytoria** podaj następujące informacje:
   1. **Nazwa**. Wprowadź nazwę repozytorium.
   2. **Adres Url git clone**. Wprowadź adres URL klonowania HTTPS usługi git, który został skopiowany wcześniej z witryny GitHub lub Azure DevOps Services.
   3. **Gałąź**. Aby uzyskać definicje, wprowadź gałąź.
   4. **Osobisty token dostępu**. Wprowadź osobisty token dostępu uzyskany wcześniej z witryny GitHub lub Azure DevOps Services.
   5. **Ścieżki folderów**. Wprowadź co najmniej jedną ścieżkę folderu względem adresu URL klonowania zawierającego artefakt lub Menedżer zasobów definicje szablonu. Po określeniu podkatalogu upewnij się, że w ścieżce folderu znajduje się ukośnik do przodu.

        ![Obszar repozytoriów](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Wybierz pozycję **Zapisz**.

## <a name="use-azure-resource-manager-template"></a>Użyj szablonu Azure Resource Manager
Szablony zarządzania zasobami platformy Azure (Azure Resource Manager) to pliki JSON opisujące zasoby na platformie Azure, które mają zostać utworzone. Aby uzyskać więcej informacji na temat tych szablonów, zobacz [Tworzenie szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Ta sekcja zawiera kroki umożliwiające dodanie repozytorium artefaktów do laboratorium przy użyciu szablonu Azure Resource Manager.  Szablon tworzy laboratorium, jeśli jeszcze nie istnieje.

### <a name="template"></a>Template
Przykładowy szablon używany w tym artykule zbiera następujące informacje za pośrednictwem parametrów. Większość parametrów ma inteligentne wartości domyślne, ale istnieje kilka wartości, które muszą zostać określone. Należy określić nazwę laboratorium, identyfikator URI repozytorium artefaktu i token zabezpieczający dla repozytorium.

- Nazwa laboratorium.
- Nazwa wyświetlana repozytorium artefaktów w interfejsie użytkownika (UI) DevTest Labs. Wartość domyślna to: `Team Repository` .
- Identyfikator URI repozytorium (przykład: `https://github.com/<myteam>/<nameofrepo>.git` lub `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .
- Gałąź w repozytorium, która zawiera artefakty. Wartość domyślna to: `master` .
- Nazwa folderu zawierającego artefakty. Wartość domyślna to: `/Artifacts` .
- Typ repozytorium. Dozwolone wartości to `VsoGit` lub `GitHub` .
- Token dostępu dla repozytorium.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Wdrożenie szablonu
Istnieje kilka sposobów na wdrożenie szablonu na platformie Azure i utworzenie zasobu (jeśli nie istnieje) lub aktualizacji, jeśli istnieje. Aby uzyskać szczegółowe informacje, zobacz następujące artykuły:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/templates/deploy-powershell.md)
- [Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure)](../azure-resource-manager/templates/deploy-cli.md)
- [Deploy resources with Resource Manager templates and Azure portal (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure Portal)](../azure-resource-manager/templates/deploy-portal.md)
- [Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)](../azure-resource-manager/templates/deploy-rest.md)

Przejdźmy dalej i zobacz, jak wdrożyć szablon w programie PowerShell. Polecenia cmdlet służące do wdrażania szablonu są specyficzne dla kontekstu, więc używane są bieżące dzierżawy i bieżące subskrypcje. Użyj [Set-AzContext](/powershell/module/az.accounts/set-azcontext) przed wdrożeniem szablonu, jeśli jest to konieczne, aby zmienić kontekst.

Najpierw utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Jeśli grupa zasobów, której chcesz użyć, już istnieje, Pomiń ten krok.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Następnie Utwórz wdrożenie do grupy zasobów przy użyciu polecenia [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). To polecenie cmdlet stosuje zmiany zasobów na platformie Azure. Kilka wdrożeń zasobów można wykonać dla każdej grupy zasobów. W przypadku wdrażania kilku razy w tej samej grupie zasobów upewnij się, że nazwa każdego wdrożenia jest unikatowa.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Po pomyślnym uruchomieniu New-AzResourceGroupDeployment polecenie generuje ważne informacje, takie jak stan aprowizacji (powinno być zakończone powodzeniem) i wszelkie dane wyjściowe szablonu.

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Ta sekcja zawiera przykładowy skrypt programu PowerShell, który może służyć do dodawania repozytorium artefaktów do laboratorium. Jeśli nie masz Azure PowerShell, zobacz [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/?view=azps-1.2.0) , aby uzyskać szczegółowe instrukcje dotyczące instalacji.

### <a name="full-script"></a>Pełny skrypt
Oto pełny skrypt, w tym wszystkie komunikaty i komentarze pełne:

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the repository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Uruchamianie skryptu programu PowerShell
Poniższy przykład pokazuje, jak uruchomić skrypt:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parametry
Przykładowy skrypt programu PowerShell w tym artykule przyjmuje następujące parametry:

| Parametr | Opis |
| --------- | ----------- |
| LabName | Nazwa laboratorium. |
| ArtifactRepositoryName | Nazwa nowego repozytorium artefaktów. Skrypt tworzy losową nazwę dla repozytorium, jeśli nie została określona. |
| ArtifactRepositoryDisplayName | Nazwa wyświetlana repozytorium artefaktów. Jest to nazwa, która jest wyświetlana w Azure Portal ( https://portal.azure.com) podczas wyświetlania wszystkich repozytoriów artefaktów dla laboratorium. |
| RepositoryUri | Identyfikator URI repozytorium. Przykłady: `https://github.com/<myteam>/<nameofrepo>.git` lub `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .|
| RepositoryBranch | Gałąź, w której można znaleźć pliki artefaktów. Wartość domyślna to "Master". |
| FolderPath | Folder, w którym można znaleźć artefakty. Wartość domyślna to "/Artifacts" |
| PersonalAccessToken | Token zabezpieczający służący do uzyskiwania dostępu do repozytorium GitHub lub VSOGit. Zapoznaj się z sekcją wymagania wstępne, aby uzyskać instrukcje dotyczące uzyskiwania osobistego tokenu dostępu |
| SourceType | Czy artefakt jest VSOGit lub repozytorium GitHub. |

Samo repozytorium wymaga nazwy wewnętrznej do identyfikacji, która różni się od nazwy wyświetlanej widocznej w Azure Portal. Nie widzisz wewnętrznej nazwy przy użyciu Azure Portal, ale zobaczysz ją w przypadku korzystania z interfejsów API REST platformy Azure lub Azure PowerShell. Skrypt zawiera nazwę, jeśli nie jest określona przez użytkownika naszego skryptu.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Polecenia programu PowerShell używane w skrypcie

| Polecenie programu PowerShell | Uwagi |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | To polecenie służy do uzyskiwania szczegółowych informacji o laboratorium, takich jak lokalizacja. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Nie ma określonego polecenia do dodawania repozytoriów artefaktów. Ogólne polecenie cmdlet [New-AzResource](/powershell/module/az.resources/new-azresource) wykonuje zadanie. To polecenie cmdlet wymaga, aby parametr **ResourceID** lub **ResourceType** i **ResourceType** miał znać typ zasobu do utworzenia. Ten przykładowy skrypt używa pary nazwa zasobu i typ zasobu. <br/><br/>Zwróć uwagę, że tworzysz Źródło repozytorium artefaktów w tej samej lokalizacji i w tej samej grupie zasobów co laboratorium.|

Skrypt dodaje nowy zasób do bieżącej subskrypcji. Aby wyświetlić te informacje, użyj [Get-AzContext](/powershell/module/az.accounts/get-azcontext) . Użyj [Set-AzContext](/powershell/module/az.accounts/set-azcontext) , aby ustawić bieżącą dzierżawę i subskrypcję.

Najlepszym sposobem odnajdywania informacji o nazwie zasobu i typie zasobu jest użycie witryny sieci Web [interfejsu API REST platformy Azure](https://azure.github.io/projects/apis/) . Zapoznaj się z dostawcą [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) , aby wyświetlić dostępne interfejsy API REST dla dostawcy DevTest Labs. Skrypt ma następujący identyfikator zasobu.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Typ zasobu to wszystko wymienione po elemencie "Providers" w identyfikatorze URI, z wyjątkiem elementów wymienionych w nawiasach klamrowych. Nazwa zasobu to wszystko widoczne w nawiasach klamrowych. Jeśli dla nazwy zasobu oczekiwano więcej niż jednego elementu, oddziel każdy element ukośnikiem.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Następne kroki
- [Określ obowiązkowe artefakty dla laboratorium w Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Tworzenie niestandardowych artefaktów dla maszyny wirtualnej DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnozowanie błędów artefaktów w laboratorium](devtest-lab-troubleshoot-artifact-failure.md)
