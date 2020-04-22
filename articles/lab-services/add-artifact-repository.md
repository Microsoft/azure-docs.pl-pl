---
title: Dodawanie repozytorium artefaktów do laboratorium w laboratorium usługi Azure DevTest Labs | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodać repozytorium artefaktów do laboratorium w laboratoriach usługi Azure DevTest.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: spelluru
ms.openlocfilehash: 2bb871119bece71c705ad9621a7c76c4b5ed0bc7
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770255"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Dodawanie repozytorium artefaktów do laboratorium w devtest labs
DevTest Labs umożliwia określenie artefaktu, który ma zostać dodany do maszyny Wirtualnej w czasie tworzenia maszyny Wirtualnej lub po utworzeniu maszyny Wirtualnej. Ten artefakt może być narzędziem lub aplikacją, którą chcesz zainstalować na maszynie wirtualnej. Artefakty są zdefiniowane w pliku JSON załadowanym z repozytorium GitHub lub Azure DevOps Git.

[Publiczne repozytorium artefaktów,](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)obsługiwane przez DevTest Labs, zawiera wiele typowych narzędzi dla systemów Windows i Linux. Łącze do tego repozytorium jest automatycznie dodawane do laboratorium. Można utworzyć własne repozytorium artefaktów za pomocą określonych narzędzi, które nie są dostępne w publicznym repozytorium artefaktów. Aby dowiedzieć się więcej o tworzeniu artefaktów niestandardowych, zobacz [Tworzenie artefaktów niestandardowych](devtest-lab-artifact-author.md).

Ten artykuł zawiera informacje dotyczące sposobu dodawania niestandardowego repozytorium artefaktów przy użyciu witryny Azure Portal, szablonów usługi Azure Resource Management i programu Azure PowerShell. Można zautomatyzować dodawanie repozytorium artefaktów do laboratorium, pisząc skrypty programu PowerShell lub CLI.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby dodać repozytorium do laboratorium, najpierw pobierz kluczowe informacje z repozytorium. W poniższych sekcjach opisano sposób uzyskania wymaganych informacji dotyczących repozytoriów hostowanych w **usłudze GitHub** lub **Azure DevOps.**

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Pobierz adres URL klonowania repozytorium GitHub i osobisty token dostępu

1. Przejdź do strony głównej repozytorium Usługi GitHub zawierającej definicje szablonów artefaktu lub Menedżera zasobów.
2. Wybierz przycisk **Clone or download** (Sklonuj lub pobierz).
3. Aby skopiować adres URL do schowka, wybierz przycisk **adresu URL klonowania HTTPS.** Zapisz adres URL do późniejszego użycia.
4. W prawym górnym rogu githubu wybierz obraz profilu, a następnie wybierz pozycję **Ustawienia**.
5. W menu **Ustawienia osobiste** po lewej stronie wybierz pozycję **Ustawienia dewelopera**.
6. Wybierz **osobiste tokeny dostępu** w menu po lewej stronie.
7. Wybierz **pozycję Generuj nowy token**.
8. Na stronie **Nowy token dostępu osobistego** w obszarze Opis **tokenu**wprowadź opis. Zaakceptuj elementy domyślne w obszarze **Wybierz zakresy**, a następnie wybierz pozycję **Generuj token**.
9. Zapisz wygenerowany token. Token można użyć później.
10. Zamknij GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Pobierz adres URL klonowania usługi Azure i token dostępu osobistego
1. Przejdź do strony głównej kolekcji zespołu `https://contoso-web-team.visualstudio.com`(na przykład ), a następnie wybierz projekt.
2. Na stronie głównej projektu wybierz pozycję **Kod**.
3. Aby wyświetlić adres URL klonowania, na stronie **Kod** projektu wybierz pozycję **Klonuj**.
4. Zapisz adres URL. Później użyjesz adresu URL.
5. Aby utworzyć token dostępu osobistego, w menu rozwijanym konta użytkownika wybierz pozycję **Mój profil**.
6. Na stronie informacji o profilu wybierz pozycję **Zabezpieczenia**.
7. Na karcie **Tokeny dostępu osobistego > zabezpieczeń** wybierz pozycję + Nowy **token**.
8. Na stronie **Tworzenie nowego tokenu dostępu osobistego:**
   1. Wprowadź **nazwę** tokenu.
   2. Na liście **Organizacja** wybierz **pozycję Wszystkie dostępne organizacje**.
   3. Na liście **Wygaśnięcie (UTC)** wybierz **90 dni**lub zdefiniowany na zamówienie okres wygaśnięcia.
   4. Wybierz opcję **Pełny dostęp** dla zakresów.
   5. Wybierz pozycję **Utwórz**.
9. Nowy token pojawia się na liście **tokenów dostępu osobistego.** Wybierz **polecenie Kopiuj token**, a następnie zapisz wartość tokenu do późniejszego użycia.
10. Przejdź do sekcji Połącz laboratorium z repozytorium.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Ta sekcja zawiera kroki, aby dodać repozytorium artefaktów do laboratorium w witrynie Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **pozycję Więcej usług**, a następnie wybierz **devtest labs** z listy usług.
3. Z listy laboratoriów wybierz laboratorium.
4. Wybierz **opcję Konfiguracja i zasady** w menu po lewej stronie.
5. Wybierz **repozytoria** w sekcji **Zasoby zewnętrzne** w menu po lewej stronie.
6. Wybierz **pozycję + Dodaj** na pasku narzędzi.

    ![Przycisk Dodaj repozytorium](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na stronie **Repozytoria** określ następujące informacje:
   1. **Nazwa**. Wprowadź nazwę repozytorium.
   2. **Git Clone Url**. Wprowadź adres URL klonowania protokołu HTTPS git, który został skopiowany wcześniej z usługi GitHub lub Azure DevOps.
   3. **Oddział**. Aby uzyskać definicje, wprowadź gałąź.
   4. **Token dostępu osobistego**. Wprowadź token dostępu osobistego, który został wcześniej odebrany z usługi GitHub lub Azure DevOps.
   5. **Ścieżki folderów**. Wprowadź co najmniej jedną ścieżkę folderu względem adresu URL klonowania zawierającego artefakt lub definicje szablonów Menedżera zasobów. Po określeniu podkatalogu upewnij się, że ukośnik do przodu jest dołączany do ścieżki folderu.

        ![Obszar repozytoriów](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Wybierz pozycję **Zapisz**.

## <a name="use-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager
Szablony usługi Azure Resource Management (Azure Resource Manager) to pliki JSON opisujące zasoby na platformie Azure, które chcesz utworzyć. Aby uzyskać więcej informacji na temat tych szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Ta sekcja zawiera kroki, aby dodać repozytorium artefaktów do laboratorium przy użyciu szablonu usługi Azure Resource Manager.  Szablon tworzy laboratorium, jeśli jeszcze nie istnieje.

### <a name="template"></a>Szablon
Przykładowy szablon użyty w tym artykule zbiera następujące informacje za pośrednictwem parametrów. Większość parametrów mają inteligentne ustawienia domyślne, ale istnieje kilka wartości, które muszą być określone. Należy określić nazwę laboratorium, identyfikator URI dla repozytorium artefaktów i token zabezpieczający dla repozytorium.

- Nazwa laboratorium.
- Wyświetlana nazwa repozytorium artefaktów w interfejsie użytkownika DevTest Labs (UI). Wartość domyślna `Team Repository`to: .
- identyfikatora URI do repozytorium (przykład: `https://github.com/<myteam>/<nameofrepo>.git` lub `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Gałąź w repozytorium, który zawiera artefakty. Wartość domyślna `master`to: .
- Nazwa folderu zawierającego artefakty. Wartość domyślna `/Artifacts`to: .
- Typ repozytorium. Dozwolone wartości `VsoGit` są `GitHub`lub .
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
Istnieje kilka sposobów, aby wdrożyć szablon na platformie Azure i mieć zasób utworzony, jeśli nie istnieje lub zaktualizowane, jeśli istnieje. Aby uzyskać szczegółowe informacje, zobacz następujące artykuły:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/templates/deploy-powershell.md)
- [Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure)](../azure-resource-manager/templates/deploy-cli.md)
- [Deploy resources with Resource Manager templates and Azure portal (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure Portal)](../azure-resource-manager/templates/deploy-portal.md)
- [Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)](../azure-resource-manager/templates/deploy-rest.md)

Przejdźmy dalej i zobaczmy, jak wdrożyć szablon w programie PowerShell. Polecenia cmdlet używane do wdrażania szablonu są specyficzne dla kontekstu, więc używana jest bieżąca dzierżawa i bieżąca subskrypcja. Użyj [Set-AzContext](/powershell/module/az.accounts/set-azcontext) przed wdrożeniem szablonu, jeśli to konieczne, aby zmienić kontekst.

Najpierw utwórz grupę zasobów przy użyciu [programu New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Jeśli grupa zasobów, której chcesz użyć, już istnieje, pomiń ten krok.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Następnie utwórz wdrożenie do grupy zasobów przy użyciu [new-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). To polecenie cmdlet stosuje zmiany zasobów na platformie Azure. Kilka wdrożeń zasobów można dokonać do dowolnej grupy zasobów. Jeśli wdrażasz kilka razy w tej samej grupie zasobów, upewnij się, że nazwa każdego wdrożenia jest unikatowa.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Po new-AzResourceGroupDeployment uruchomić pomyślnie, polecenie wyprowadza ważne informacje, takie jak stan inicjowania obsługi administracyjnej (powinny być skuteczne) i wszelkie dane wyjściowe dla szablonu.

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
W tej sekcji przedstawiono przykładowy skrypt programu PowerShell, który może służyć do dodawania repozytorium artefaktów do laboratorium. Jeśli nie masz programu Azure PowerShell, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell,](/powershell/azure/overview?view=azps-1.2.0) aby uzyskać szczegółowe instrukcje, aby go zainstalować.

### <a name="full-script"></a>Pełny skrypt
Oto pełny skrypt, w tym kilka pełnych wiadomości i komentarzy:

**Nowy-DevTestLabArtifactRepository.ps1**:

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
Script creates a random name for the respository if it is not specified.

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

### <a name="run-the-powershell-script"></a>Uruchom skrypt programu PowerShell
W poniższym przykładzie pokazano, jak uruchomić skrypt:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parametry
Przykładowy skrypt programu PowerShell w tym artykule przyjmuje następujące parametry:

| Parametr | Opis |
| --------- | ----------- |
| Nazwa laboratorium | Nazwa laboratorium. |
| Nazwa pliku ArtifactRepositoryName | Nazwa nowego repozytorium artefaktów. Skrypt tworzy losową nazwę repozytorium, jeśli nie jest określony. |
| Nazwa funkcji ArtifactRepositoryDisplayname | Wyświetlana nazwa repozytorium artefaktów. Jest to nazwa, która jest wyświetlana w witrynie Azure portal (https://portal.azure.com) podczas wyświetlania wszystkich repozytoriów artefaktów dla laboratorium. |
| RepozytoriumUri | Uri do repozytorium. Przykłady: `https://github.com/<myteam>/<nameofrepo>.git` `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`lub .|
| RepozytoriumBranch | Gałąź, w której można znaleźć pliki artefaktów. Domyślnie wartość "master". |
| Folderpath | Folder, w którym można znaleźć artefakty. Wartość domyślna to '/Artifacts' |
| PersonalAccessToken | Token zabezpieczający dostępu do repozytorium GitHub lub VSOGit. Zobacz sekcję wymagań wstępnych, aby uzyskać instrukcje dotyczące uzyskiwania tokenu dostępu osobistego |
| SourceType | Czy artefakt jest VSOGit lub GitHub repozytorium. |

Samo repozytorium wymaga wewnętrznej nazwy do identyfikacji, która różni się od nazwy wyświetlanej widocznej w witrynie Azure portal. Nie widzisz nazwy wewnętrznej przy użyciu witryny Azure portal, ale widać go podczas korzystania z interfejsów API rest platformy Azure lub programu Azure PowerShell. Skrypt zawiera nazwę, jeśli nie jest określony przez użytkownika naszego skryptu.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Polecenia programu PowerShell używane w skrypcie

| Polecenie programu PowerShell | Uwagi |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | To polecenie służy do uzyskania szczegółowych informacji na temat laboratorium, takich jak jego lokalizacja. |
| [Nowe źródło AzResource](/powershell/module/az.resources/new-azresource) | Nie ma żadnego konkretnego polecenia do dodawania repozytoriów artefaktów. Ogólne polecenie cmdlet [New-AzResource](/powershell/module/az.resources/new-azresource) wykonuje zadanie. To polecenie cmdlet wymaga **resourceid** lub **ResourceName** i **ResourceType** pary znać typ zasobu do utworzenia. Ten przykładowy skrypt używa pary nazw zasobów i typów zasobów. <br/><br/>Należy zauważyć, że tworzysz źródło repozytorium artefaktów w tej samej lokalizacji i w tej samej grupie zasobów co laboratorium.|

Skrypt dodaje nowy zasób do bieżącej subskrypcji. Użyj [Get-AzContext,](/powershell/module/az.accounts/get-azcontext) aby zobaczyć te informacje. Użyj [Set-AzContext,](/powershell/module/az.accounts/set-azcontext) aby ustawić bieżącą dzierżawę i subskrypcję.

Najlepszym sposobem odnajdywanie informacji o nazwie zasobu i typie zasobu jest użycie witryny sieci Web [interfejsów API rest platformy Azure na dysku testowym.](https://azure.github.io/projects/apis/) Zapoznaj się z [DevTest Labs — 2016-05-15](https://aka.ms/dtlrestapis) dostawcy, aby zobaczyć dostępne interfejsy API REST dla dostawcy DevTest Labs. Użytkownicy skryptu następujący identyfikator zasobu.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Typ zasobu jest wszystko wymienione po "dostawców" w identyfikatorze URI, z wyjątkiem elementów wymienionych w nawiasach klamrowych. Nazwa zasobu jest wszystko widoczne w nawiasach klamrowych. Jeśli dla nazwy zasobu oczekuje się więcej niż jednego elementu, należy oddzielić każdy element ukośnikiem, tak jak to zrobiliśmy.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Następne kroki
- [Określanie obowiązkowych artefaktów dla laboratorium w laboratoriach devtest azure](devtest-lab-mandatory-artifacts.md)
- [Tworzenie niestandardowych artefaktów dla maszyny wirtualnej DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnozowanie awarii artefaktów w laboratorium](devtest-lab-troubleshoot-artifact-failure.md)
