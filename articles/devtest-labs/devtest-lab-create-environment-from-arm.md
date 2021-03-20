---
title: Tworzenie środowisk z obsługą wielu maszyn wirtualnych i zasobów PaaS za pomocą szablonów
description: Dowiedz się, jak tworzyć środowiska wielu maszyn wirtualnych i PaaS zasoby w Azure DevTest Labs z szablonu Azure Resource Manager
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: 97659d4ab95fdbe75460161d0ceed71a1cb5cf82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88182412"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager

Środowiska Azure DevTest Labs pozwalają użytkownikom łatwo wdrażać złożone infrastruktury w spójny sposób w ramach dostrojenia laboratorium. Za pomocą [szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) można tworzyć środowiska z zestawami zasobów w DevTest Labs. Te środowiska mogą zawierać dowolne zasoby platformy Azure, które mogą być tworzone przez Menedżer zasobów szablonów.

Do laboratorium można łatwo [dodać jedną maszynę wirtualną (VM)](devtest-lab-add-vm.md) , korzystając z [Azure Portal](https://portal.azure.com). Jednak scenariusze, takie jak wielowarstwowe aplikacje internetowe lub farma programu SharePoint, wymagają mechanizmu tworzenia wielu maszyn wirtualnych w jednym kroku. Za pomocą szablonów Azure Resource Manager można zdefiniować infrastrukturę i konfigurację rozwiązania platformy Azure oraz wielokrotnie wdrożyć wiele maszyn wirtualnych w spójnym stanie.

Szablony Azure Resource Manager również zapewniają następujące korzyści:

- Szablony Azure Resource Manager są ładowane bezpośrednio z repozytorium kontroli źródła w serwisie GitHub lub Azure Repos.
- Użytkownicy mogą utworzyć środowisko poprzez wybranie skonfigurowanego szablonu Azure Resource Manager z Azure Portal, podobnie jak w przypadku innych typów [baz maszyn wirtualnych](devtest-lab-comparing-vm-base-image-types.md).
- Zasoby PaaS platformy Azure oraz maszyny wirtualne IaaS w środowisku można udostępniać przy użyciu szablonu Azure Resource Manager.
- Możesz śledzić koszt środowisk w laboratorium, oprócz pojedynczych maszyn wirtualnych utworzonych przez inne typy baz. Zasoby PaaS są tworzone i będą wyświetlane w usłudze śledzenia kosztów. Jednak automatyczne zamknięcie maszyny wirtualnej nie dotyczy zasobów PaaS.

Aby dowiedzieć się więcej na temat korzyści z używania szablonów Menedżer zasobów do wdrażania, aktualizowania lub usuwania wielu zasobów laboratorium w ramach jednej operacji, zobacz [zalety korzystania z szablonów Menedżer zasobów](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager).

> [!NOTE]
> Jeśli używasz szablonu Menedżer zasobów jako bazy do tworzenia maszyn wirtualnych laboratorium, istnieją pewne różnice między tworzeniem wielu maszyn wirtualnych lub pojedynczą maszyną wirtualną. Aby uzyskać więcej informacji, zobacz [Użyj szablonu Azure Resource Manager maszyny wirtualnej](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>Korzystanie z środowisk publicznych DevTest Labs
Azure DevTest Labs ma [publiczne repozytorium szablonów Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) , których można użyć do tworzenia środowisk bez konieczności samodzielnego łączenia się z zewnętrznym źródłem usługi GitHub. To publiczne repozytorium jest podobne do publicznego repozytorium artefaktów, które są dostępne w Azure Portal dla każdego tworzonego laboratorium. Repozytorium środowiska pozwala szybko rozpocząć pracę z szablonami wstępnie utworzonych środowisk, które mają kilka parametrów wejściowych. Te szablony zapewniają bezproblemowe środowisko uruchamiania dla zasobów PaaS w ramach laboratoriów.

W repozytorium publicznym zespół usługi DevTest Labs i inne osoby utworzyły i udostępnili często używane szablony, takie jak Azure Web Apps, klaster Service Fabric i programowanie środowiska farmy programu SharePoint. Możesz użyć tych szablonów bezpośrednio lub dostosować je do swoich potrzeb. Aby uzyskać więcej informacji, zobacz [Konfigurowanie i używanie środowisk publicznych w programie DevTest Labs](devtest-lab-configure-use-public-environments.md). Po utworzeniu własnych szablonów można je zapisać w tym repozytorium, aby udostępnić je innym osobom, lub skonfigurować własne repozytorium git.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Tworzenie własnych repozytoriów szablonów

Zgodnie z najlepszymi rozwiązaniami dotyczącymi infrastruktury jako kodu i konfiguracji jako kodu, należy zarządzać szablonami środowisk w kontroli źródła. Azure DevTest Labs postępuj zgodnie z tą procedurą i ładuje wszystkie szablony Azure Resource Manager bezpośrednio z repozytorium GitHub lub Azure Repos repozytoriów. W związku z tym można używać szablonów Menedżer zasobów w całym cyklu wydania, ze środowiska testowego do środowiska produkcyjnego.

Istnieje kilka reguł, które należy wykonać, aby zorganizować szablony Azure Resource Manager w repozytorium:

- Należy nazwać plik szablonu głównego *azuredeploy.js*.

- Jeśli chcesz użyć wartości parametrów zdefiniowanych w pliku parametrów, plik parametrów musi mieć nazwę *azuredeploy.parameters.json*.

  Możesz użyć parametrów `_artifactsLocation` i `_artifactsLocationSasToken` w celu skonstruowania wartości identyfikatora URI parametersLink, co umożliwi DevTest laboratoriom automatyczne zarządzanie szablonami zagnieżdżonymi. Aby uzyskać więcej informacji, zobacz [wdrażanie zagnieżdżonych szablonów Azure Resource Manager dla środowisk testowych](deploy-nested-template-environments.md).

- Można zdefiniować metadane, aby określić nazwę wyświetlaną i opis szablonu w pliku o nazwie *metadata.jsw*:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Pliki szablonów Azure Resource Manager klucza](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Dodawanie repozytoriów szablonów do laboratorium

Po utworzeniu i skonfigurowaniu repozytorium możesz dodać je do laboratorium przy użyciu Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
1. Z listy laboratoriów wybierz odpowiednie laboratorium.
1. W okienku **Przegląd** laboratorium wybierz pozycję **Konfiguracja i zasady**.

   ![Konfiguracja i zasady](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Z listy ustawienia **konfiguracji i zasad** wybierz pozycję **repozytoria**. Repozytorium **publicznych repozytoriów artefaktów** jest generowane automatycznie dla wszystkich laboratoriów i nawiązuje połączenie z [publicznym repozytorium GitHub w witrynie DevTest Labs](https://github.com/Azure/azure-devtestlab).

1. Aby dodać repozytorium szablonów Azure Resource Manager, wybierz pozycję **Dodaj**.

   ![Repozytorium publiczne](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. W okienku **repozytoria** wprowadź następujące informacje:

   - **Nazwa**: Wprowadź nazwę repozytorium do użycia w laboratorium.
   - **Adres URL klonowania git**: wprowadź adres URL KLONowania https usługi git z witryny GitHub lub Azure Repos.
   - **Gałąź** (opcjonalnie): Wprowadź nazwę gałęzi, aby uzyskać dostęp do definicji szablonów Azure Resource Manager.
   - **Osobisty token dostępu**: wprowadź osobisty token dostępu, który jest używany do bezpiecznego uzyskiwania dostępu do repozytorium.
     - Aby uzyskać token z Azure Repos, w obszarze Profil wybierz pozycję **Ustawienia użytkownika**  >  **zabezpieczenia**  >  **osobiste tokeny dostępu**.
     - Aby uzyskać token z usługi GitHub, w obszarze Twój profil wybierz kolejno pozycje **Ustawienia** ustawienia  >  **deweloper**  >  **osobiste tokeny dostępu**.
   - **Ścieżki folderów**: wprowadź ścieżkę folderu względem identyfikatora URI klonowania git dla definicji artefaktów lub definicji szablonu Azure Resource Manager.

1. Wybierz pozycję **Zapisz**.

   ![Dodaj nowe repozytorium](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Po dodaniu szablonu Azure Resource Manager do laboratorium użytkownicy laboratorium mogą tworzyć środowiska przy użyciu szablonu.

## <a name="configure-access-rights-for-lab-users"></a>Konfigurowanie praw dostępu dla użytkowników laboratorium

Użytkownicy laboratorium domyślnie mają rolę **czytelnik** , więc nie mogą zmieniać zasobów w grupie zasobów środowiska. Na przykład nie mogą zatrzymywać ani uruchamiać zasobów.

Aby nadać użytkownikom laboratorium rolę **współautor** , aby mogli edytować zasoby w swoich środowiskach, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)w okienku **Przegląd** laboratorium wybierz pozycję **Konfiguracja i zasady**, a następnie wybierz pozycję **Ustawienia laboratorium**.

1. W okienku **Ustawienia laboratorium** wybierz pozycję **współautor**, a następnie wybierz pozycję **Zapisz** , aby przyznać użytkownikom laboratorium uprawnienia do zapisu.

   ![Konfigurowanie praw dostępu użytkownika laboratorium](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

Następna sekcja przeprowadzi Cię przez proces tworzenia środowisk z szablonu Azure Resource Manager.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Twórz środowiska na podstawie szablonów w Azure Portal

Po dodaniu szablonu Azure Resource Manager do laboratorium użytkownicy laboratorium mogą tworzyć środowiska w Azure Portal, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .

1. Z listy laboratoriów wybierz odpowiednie laboratorium.

1. Na stronie laboratorium wybierz pozycję **Dodaj**.

1. W okienku **Wybierz podstawowe** są wyświetlane podstawowe obrazy, których można użyć, z szablonami Azure Resource Manager wymienionymi w pierwszej kolejności. Wybierz szablon Azure Resource Manager, który chcesz.

   ![Wybieranie bazy](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. W okienku **Dodaj** wprowadź wartość **Nazwa środowiska** , która ma być wyświetlana użytkownikom środowiska.

   Szablon Azure Resource Manager definiuje resztę pól wejściowych. Jeśli szablon *azuredeploy.parameter.jsw* pliku definiuje wartości domyślne, pola wejściowe zawierają te wartości.

   Dla parametrów typu *Secure ciąg* można użyć wpisów tajnych z Azure Key Vault. Aby dowiedzieć się więcej o przechowywaniu wpisów tajnych w magazynie kluczy i korzystaniu z nich podczas tworzenia zasobów laboratorium, zobacz [zapisywanie wpisów tajnych w Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

   ![Dodaj okienko](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Następujące wartości parametrów nie są wyświetlane w polach wejściowych, nawet jeśli szablon je określa. Zamiast tego formularz pokazuje puste pola wejściowe, w których użytkownicy laboratorium muszą wprowadzać wartości podczas tworzenia środowiska.
   >
   > - OGÓLNE — UNIKATOWE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - OGÓLNE — HASŁO

1. Wybierz pozycję **Dodaj** , aby utworzyć środowisko.

   Środowisko natychmiast rozpoczyna inicjowanie obsługi przy użyciu stanu wyświetlanego na liście **moje maszyny wirtualne** . Laboratorium automatycznie tworzy nową grupę zasobów, aby udostępnić wszystkie zasoby zdefiniowane w szablonie Azure Resource Manager.

1. Po utworzeniu środowiska Wybierz środowisko na liście **moje maszyny wirtualne** , aby otworzyć okienko Grupa zasobów i przeglądać wszystkie zasoby, które zostały zainicjowane przez środowisko.

   ![Zasoby środowiska](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Można również rozszerzyć środowisko, aby wyświetlić listę maszyn wirtualnych, dla których zainicjowano środowisko.

   ![Lista moich maszyn wirtualnych](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Wybierz dowolne środowisko, aby wyświetlić dostępne akcje, takie jak zastosowanie artefaktów, dołączenie dysków danych, zmiana czasu automatycznego zamykania i inne.

   ![Akcje środowiska](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatyzowanie tworzenia środowiska przy użyciu programu PowerShell

Możliwe jest użycie Azure Portal, aby dodać jedno środowisko do laboratorium, ale gdy scenariusz programowania lub testowania musi tworzyć wiele środowisk, zautomatyzowane wdrażanie jest lepszym rozwiązaniem.

Przed kontynuowaniem upewnij się, że masz szablon Azure Resource Manager, który definiuje zasoby do utworzenia. [Dodaj i skonfiguruj szablon w repozytorium git](#configure-your-own-template-repositories)i [Dodaj repozytorium do laboratorium](#add-template-repositories-to-the-lab).

Poniższy przykładowy skrypt tworzy środowisko w środowisku laboratoryjnym. Komentarze ułatwiają zrozumienie lepszego skryptu.

1. Zapisz następujący przykładowy skrypt programu PowerShell na dysku twardym jako *deployenv.ps1*.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Uruchom skrypt w następujący sposób, korzystając z określonych wartości identyfikatora subskrypcji, LabName, ResourceGroupName, Repositoryname, templateName (folder w repozytorium Git) i Środowiskaname.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Możesz również użyć interfejsu wiersza polecenia platformy Azure do wdrożenia zasobów przy użyciu szablonów Menedżer zasobów. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Tylko użytkownik z uprawnieniami właściciela laboratorium może tworzyć maszyny wirtualne na podstawie szablonu Menedżer zasobów przy użyciu Azure PowerShell. Jeśli chcesz zautomatyzować tworzenie maszyny wirtualnej przy użyciu szablonu Menedżer zasobów i masz tylko uprawnienia użytkownika, możesz użyć polecenia [AZ Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Ograniczenia dotyczące szablonów Menedżer zasobów w laboratoriach DevTest Labs

Te ograniczenia należy wziąć pod uwagę podczas korzystania z szablonów Menedżer zasobów w DevTest Labs:

- Nie można tworzyć formuł ani obrazów niestandardowych z maszyn wirtualnych programu Lab utworzonych na podstawie szablonu Menedżer zasobów.

- Większość zasad nie jest oceniana podczas wdrażania szablonów Menedżer zasobów.

Na przykład mogą istnieć zasady laboratorium, które użytkownik może utworzyć tylko pięć maszyn wirtualnych. Jednak użytkownik może wdrożyć szablon Menedżer zasobów, który tworzy dziesiątki maszyn wirtualnych. Zasady, które nie są oceniane, obejmują:

  - Liczba maszyn wirtualnych na użytkownika

  - Liczba maszyn wirtualnych w warstwie Premium na użytkownika laboratorium

  - Liczba dysków w warstwie Premium na użytkownika laboratorium

## <a name="next-steps"></a>Następne kroki
- Po utworzeniu maszyny wirtualnej można nawiązać połączenie z maszyną wirtualną, wybierając pozycję **Połącz** w okienku zarządzanie maszyną wirtualną.
- Wyświetlaj zasoby w środowisku i zarządzaj nimi, wybierając środowisko na liście **moje maszyny wirtualne** w laboratorium.
- Zapoznaj się [z szablonami Azure Resource Manager w galerii szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates).
