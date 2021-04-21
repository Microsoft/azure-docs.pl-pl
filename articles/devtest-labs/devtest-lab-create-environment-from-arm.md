---
title: Tworzenie środowisk z wieloma maszynami wirtualnym i zasobów PaaS przy użyciu szablonów
description: Dowiedz się, jak tworzyć środowiska z wieloma maszynami wirtualnym i zasoby PaaS w Azure DevTest Labs z szablonu Azure Resource Manager wirtualnej
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: f285acffe642a85fa27792ee51ea67a57f6d35a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790117"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager

Azure DevTest Labs umożliwiają użytkownikom spójne wdrażanie złożonych infrastruktur w granicach laboratorium. Za pomocą szablonów [Azure Resource Manager można](../azure-resource-manager/templates/template-syntax.md) tworzyć środowiska z zestawami zasobów w uciece DevTest Labs. Te środowiska mogą zawierać dowolne zasoby platformy Azure, Resource Manager tworzyć szablony.

Możesz łatwo [dodać jedną maszynę wirtualną na](devtest-lab-add-vm.md) raz do laboratorium przy użyciu Azure Portal. [](https://portal.azure.com) Jednak scenariusze, takie jak wielowarstwowe aplikacje internetowe lub farma programu SharePoint, wymagają mechanizmu tworzenia wielu maszyn wirtualnych w jednym kroku. Za pomocą Azure Resource Manager szablonów można zdefiniować infrastrukturę i konfigurację rozwiązania platformy Azure oraz wielokrotnie wdrażać wiele maszyn wirtualnych w spójnym stanie.

Azure Resource Manager szablonów zapewniają również następujące korzyści:

- Azure Resource Manager są ładowane bezpośrednio z usługi GitHub lub Azure Repos repozytorium kontroli źródła.
- Użytkownicy mogą utworzyć środowisko, wybierając skonfigurowany szablon Azure Resource Manager z Azure Portal, podobnie jak w przypadku innych typów baz [maszyn wirtualnych.](devtest-lab-comparing-vm-base-image-types.md)
- Zasoby PaaS platformy Azure oraz maszyny wirtualne IaaS można aprowizować w środowisku z Azure Resource Manager szablonu.
- Oprócz poszczególnych maszyn wirtualnych utworzonych przez inne typy baz można śledzić koszty środowisk w laboratorium. Zasoby PaaS są tworzone i będą wyświetlane w śledzeniu kosztów. Automatyczne zamykanie maszyny wirtualnej nie ma jednak zastosowania do zasobów PaaS.

Aby dowiedzieć się więcej o zaletach używania szablonów Resource Manager do wdrażania, aktualizowania lub usuwania wielu zasobów laboratorium w ramach jednej operacji, zobacz Korzyści z używania szablonów Resource Manager [laboratorium.](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)

> [!NOTE]
> Jeśli używasz szablonu Resource Manager jako podstawy do tworzenia maszyn wirtualnych laboratorium, istnieją pewne różnice między tworzeniem wielu maszyn wirtualnych lub pojedynczej maszyny wirtualnej. Aby uzyskać więcej informacji, [zobacz Use a virtual machine's Azure Resource Manager template](devtest-lab-use-resource-manager-template.md)(Używanie szablonu maszyny Azure Resource Manager wirtualnej).
>

## <a name="use-devtest-labs-public-environments"></a>Korzystanie ze środowisk publicznych laboratoriów DevTest Labs
Azure DevTest Labs dostępne jest publiczne repozytorium szablonów [Azure Resource Manager,](https://github.com/Azure/azure-devtestlab/tree/master/Environments) których można użyć do tworzenia środowisk bez konieczności samodzielnie nawiązywania połączenia z zewnętrznym źródłem usługi GitHub. To repozytorium publiczne jest podobne do publicznego repozytorium artefaktów, które jest dostępne w Azure Portal dla każdego laboratorium, które utworzysz. Repozytorium środowiska umożliwia szybkie rozpoczynanie pracy z wstępnie autorami szablonów środowisk, które mają niewiele parametrów wejściowych. Te szablony zapewniają bezproblemowe środowisko rozpoczynania pracy z zasobami PaaS w laboratoriach.

W repozytorium publicznym zespół usługi DevTest Labs i inne osoby utworzyły i udostępniły często używane szablony, takie jak Azure Web Apps, Service Fabric Cluster i deweloperska farma programu SharePoint. Możesz użyć tych szablonów bezpośrednio lub dostosować je do własnych potrzeb. Aby uzyskać więcej informacji, zobacz Configure and use public environments in DevTest Labs (Konfigurowanie środowisk publicznych [i korzystanie z nich w u usługach DevTest Labs).](devtest-lab-configure-use-public-environments.md) Po utworzeniu własnych szablonów można je przechowywać w tym repozytorium, aby udostępnić je innym osobom, lub skonfigurować własne repozytorium Git.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Tworzenie własnych repozytoriów szablonów

Jednym z najlepszych rozwiązań w zakresie infrastruktury jako kodu i konfiguracji jako kodu jest zarządzanie szablonami środowisk w kontroli źródła. Azure DevTest Labs tej praktyki i ładuje wszystkie szablony Azure Resource Manager bezpośrednio z usługi GitHub lub Azure Repos repozytoriów. W związku z tym można używać Resource Manager w całym cyklu wydań, od środowiska testowego do środowiska produkcyjnego.

Istnieje kilka reguł, które należy wykonać, aby zorganizować Azure Resource Manager szablonów w repozytorium:

- Plik szablonu głównego należy nazwać tak, *abyazuredeploy.jsna .*

- Jeśli chcesz użyć wartości parametrów zdefiniowanych w pliku parametrów, plik parametrów musi mieć nazwęazuredeploy.parameters.js *na*.

  Możesz użyć parametrów i , aby skonstruować wartość parametersLink URI, co umożliwia laboratoriom DevTest Labs automatyczne zarządzanie `_artifactsLocation` `_artifactsLocationSasToken` zagnieżdżonych szablonami. Aby uzyskać więcej informacji, zobacz Deploy nested Azure Resource Manager templates for testing environments (Wdrażanie [zagnieżdżonych szablonów aplikacji dla środowisk testowych).](deploy-nested-template-environments.md)

- Metadane można zdefiniować, aby określić nazwę wyświetlaną i opis szablonu w pliku o *nazwiemetadata.jsna*, w następujący sposób:

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
1. Wybierz **pozycję Wszystkie usługi,** a następnie wybierz pozycję **DevTest Labs** z listy.
1. Z listy laboratoriów wybierz odpowiednie laboratorium.
1. W okienku Przegląd **laboratorium** wybierz pozycję Konfiguracja **i zasady.**

   ![Konfiguracja i zasady](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Z listy **Ustawienia konfiguracji i** zasad wybierz pozycję **Repozytoria.** Repozytorium **publicznego repozytorium** artefaktów jest generowane automatycznie dla wszystkich laboratoriów i łączy się z publicznym repozytorium [GitHub usługi DevTest Labs.](https://github.com/Azure/azure-devtestlab)

1. Aby dodać repozytorium Azure Resource Manager szablonu, wybierz pozycję **Dodaj**.

   ![Publiczne repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. W **okienku Repozytoria** wprowadź następujące informacje:

   - **Nazwa:** wprowadź nazwę repozytorium do użycia w laboratorium.
   - **Adres URL klonowania Git:** wprowadź adres URL klonowania HTTPS usługi Git z usługi GitHub lub Azure Repos.
   - **Gałąź** (opcjonalnie): wprowadź nazwę gałęzi, aby uzyskać dostęp do Azure Resource Manager szablonu.
   - **Osobisty token dostępu:** wprowadź osobisty token dostępu używany do bezpiecznego uzyskiwania dostępu do repozytorium.
     - Aby uzyskać token z witryny Azure Repos, w profilu wybierz pozycję Ustawienia użytkownika  >  **Osobiste**  >  **tokeny dostępu zabezpieczeń.**
     - Aby uzyskać token z usługi GitHub, w profilu wybierz pozycję Ustawienia  >  **Ustawienia dewelopera**  >  **Osobiste tokeny dostępu.**
   - **Ścieżki folderów:** wprowadź ścieżkę folderu względną do twojego URI klonowania Git dla definicji artefaktów lub Azure Resource Manager szablonu.

1. Wybierz pozycję **Zapisz**.

   ![Dodawanie nowego repozytorium](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Po dodaniu szablonu Azure Resource Manager do laboratorium użytkownicy laboratorium mogą tworzyć środowiska przy użyciu tego szablonu.

## <a name="configure-access-rights-for-lab-users"></a>Konfigurowanie praw dostępu dla użytkowników laboratorium

Użytkownicy laboratorium **mają** domyślnie rolę Czytelnik, więc nie mogą zmieniać zasobów w grupie zasobów środowiska. Na przykład nie mogą zatrzymać ani uruchomić swoich zasobów.

Aby nadać użytkownikom laboratorium rolę **Współautor,** aby użytkownicy mieli możliwość edytowania zasobów w swoich środowiskach, wykonaj następujące kroki:

1. W [okienku Azure Portal](https://portal.azure.com)laboratorium w  okienku Przegląd wybierz pozycję Konfiguracja i **zasady,** a następnie wybierz pozycję **Ustawienia laboratorium**.

1. W **okienku Ustawienia laboratorium** wybierz pozycję **Współautor,** a następnie wybierz pozycję **Zapisz,** aby przyznać uprawnienia do zapisu użytkownikom laboratorium.

   ![Konfigurowanie praw dostępu użytkowników laboratorium](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

W następnej sekcji otworzymy środowiska na podstawie Azure Resource Manager szablonu.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Tworzenie środowisk na podstawie szablonów w Azure Portal

Po dodaniu szablonu Azure Resource Manager laboratorium użytkownicy laboratorium mogą tworzyć środowiska w środowisku Azure Portal następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **pozycję Wszystkie usługi,** a następnie wybierz pozycję **DevTest Labs** z listy.

1. Z listy laboratoriów wybierz odpowiednie laboratorium.

1. Na stronie laboratorium wybierz pozycję **Dodaj**.

1. W **okienku Wybierz bazę** są wyświetlane obrazy podstawowe, których można użyć, Azure Resource Manager szablony są wymienione jako pierwsze. Wybierz Azure Resource Manager szablon.

   ![Wybieranie bazy](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. W **okienku** Dodaj wprowadź wartość **Nazwa środowiska** do wyświetlenia użytkownikom środowiska.

   Szablon Azure Resource Manager definiuje pozostałe pola wejściowe. Jeśli szablonazuredeploy.parameter.js *pliku* definiuje wartości domyślne, pola wejściowe pokazują te wartości.

   W przypadku parametrów typu *bezpieczny ciąg* możesz użyć wpisów tajnych z Azure Key Vault. Aby dowiedzieć się więcej o przechowywaniu wpisów tajnych w magazynie kluczy i używaniu ich podczas tworzenia zasobów laboratorium, zobacz Przechowywanie wpisów tajnych w [Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

   ![Dodawanie okienka](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Następujące wartości parametrów nie są wyświetlane w polach wejściowych, nawet jeśli szablon je określa. Zamiast tego formularz zawiera puste pola wejściowe, w których użytkownicy laboratorium muszą wprowadzać wartości podczas tworzenia środowiska.
   >
   > - UNIKATOWA GENERACJA
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. Wybierz **pozycję Dodaj,** aby utworzyć środowisko.

   Środowisko natychmiast rozpoczyna aprowizowanie ze stanem wyświetlanym na **liście Moje maszyny** wirtualne. Laboratorium automatycznie tworzy nową grupę zasobów w celu aprowizowania wszystkich zasobów zdefiniowanych w Azure Resource Manager szablonu.

1. Po utworzeniu środowiska wybierz środowisko  z listy Moje maszyny wirtualne, aby otworzyć okienko grupy zasobów i przejrzeć wszystkie zasoby, które aprowizuje środowisko.

   ![Zasoby środowiska](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Możesz również rozwinąć środowisko, aby wyświetlić tylko listę maszyn wirtualnych, które aprowizuje środowisko.

   ![Lista moje maszyny wirtualne](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Wybierz dowolne ze środowisk, aby wyświetlić dostępne akcje, takie jak stosowanie artefaktów, dołączanie dysków danych, zmienianie czasu automatycznego zamykania i nie tylko.

   ![Akcje środowiska](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatyzowanie tworzenia środowiska przy użyciu programu PowerShell

Użycie narzędzia do Azure Portal w celu dodania pojedynczego środowiska do laboratorium jest możliwe, ale gdy scenariusz tworzenia lub testowania musi tworzyć wiele środowisk, wdrożenie automatyczne jest lepszym środowiskiem.

Przed kontynuowaniem upewnij się, że masz szablon Azure Resource Manager, który definiuje zasoby do utworzenia. [Dodaj i skonfiguruj szablon w repozytorium Git, a](#configure-your-own-template-repositories)następnie [dodaj repozytorium do laboratorium](#add-template-repositories-to-the-lab).

Poniższy przykładowy skrypt tworzy środowisko w laboratorium. Komentarze pomagają lepiej zrozumieć skrypt.

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

1. Uruchom skrypt w następujący sposób, używając określonych wartości dla wartości SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (folder w repozytorium Git) i EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Za pomocą interfejsu wiersza polecenia platformy Azure można również wdrażać zasoby Resource Manager szablonów. Aby uzyskać więcej informacji, zobacz Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów [za pomocą szablonów Resource Manager interfejsu wiersza polecenia platformy Azure).](../azure-resource-manager/templates/deploy-cli.md)

> [!NOTE]
> Tylko użytkownik z uprawnieniami właściciela laboratorium może tworzyć maszyny wirtualne na podstawie Resource Manager przy użyciu Azure PowerShell. Jeśli chcesz zautomatyzować tworzenie maszyny wirtualnej przy użyciu szablonu Resource Manager i masz tylko uprawnienia użytkownika, możesz użyć polecenia [interfejsu](/cli/azure/lab/vm#az_lab_vm_create)wiersza polecenia az lab vm create .

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Resource Manager szablonów w u usługi DevTest Labs

Podczas korzystania z szablonów Resource Manager devTest Labs należy wziąć pod uwagę następujące ograniczenia:

- Nie można tworzyć formuł ani obrazów niestandardowych z maszyn wirtualnych laboratorium, które zostały utworzone na podstawie Resource Manager szablonu.

- Większość zasad nie jest oceniana podczas wdrażania Resource Manager szablonów.

Na przykład możesz mieć zasady laboratorium, które użytkownik może utworzyć tylko pięć maszyn wirtualnych. Użytkownik może jednak wdrożyć szablon Resource Manager który tworzy dziesiątki maszyn wirtualnych. Zasady, które nie są oceniane, obejmują:

  - Liczba maszyn wirtualnych na użytkownika

  - Liczba maszyn wirtualnych w premium na użytkownika laboratorium

  - Liczba dysków w warstwie Premium na użytkownika laboratorium

## <a name="next-steps"></a>Następne kroki
- Po utworzeniu maszyny wirtualnej możesz połączyć się z maszyną wirtualną, wybierając pozycję **Połącz** w okienku zarządzania maszyny wirtualnej.
- Wyświetl zasoby i zarządzaj nimi w środowisku, wybierając środowisko na liście **Moje maszyny** wirtualne w laboratorium.
- Zapoznaj się [z Azure Resource Manager szablonów z galerii szablonów szybkiego startu platformy Azure.](https://github.com/Azure/azure-quickstart-templates)
