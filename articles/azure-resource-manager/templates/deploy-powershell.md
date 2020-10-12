---
title: Wdrażanie zasobów przy użyciu programu PowerShell i szablonu
description: Użyj Azure Resource Manager i Azure PowerShell do wdrożenia zasobów na platformie Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: e47de54558962215fe3be78f5b9c45c8d46c54a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372446"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Wdrażanie zasobów za pomocą szablonów ARM i Azure PowerShell

W tym artykule wyjaśniono, jak używać Azure PowerShell z szablonami Azure Resource Manager (szablony ARM) do wdrażania zasobów na platformie Azure. Jeśli nie znasz pojęć związanych z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Omówienie wdrażania szablonów](overview.md).

## <a name="deployment-scope"></a>Zakres wdrożenia

Wdrożenie można określić w grupie zasobów, subskrypcji, grupie zarządzania lub dzierżawie. W większości przypadków wdrożenie zostanie ukierunkowane na grupę zasobów. Aby zastosować zasady i przypisania ról w większym zakresie, należy użyć subskrypcji, grupy zarządzania lub wdrożeń dzierżawców. Podczas wdrażania w ramach subskrypcji można utworzyć grupę zasobów i wdrożyć do niej zasoby.

W zależności od zakresu wdrożenia używane są inne polecenia.

* Aby wdrożyć w **grupie zasobów**, użyj polecenie [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* Aby wdrożyć w **ramach subskrypcji**, użyj polecenie New-AzSubscriptionDeployment:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Aby uzyskać więcej informacji o wdrożeniach na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

* Aby wdrożyć w **grupie zarządzania**, użyj polecenie [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Aby uzyskać więcej informacji o wdrożeniach na poziomie grupy zarządzania, zobacz [Tworzenie zasobów na poziomie grupy zarządzania](deploy-to-management-group.md).

* Aby wdrożyć aplikację w **dzierżawie**, użyj polecenie [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Aby uzyskać więcej informacji na temat wdrożeń na poziomie dzierżawy, zobacz [Tworzenie zasobów na poziomie dzierżawy](deploy-to-tenant.md).

W przykładach w tym artykule są używane wdrożenia grup zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć szablon do wdrożenia. Jeśli jeszcze tego nie zrobiono, Pobierz i Zapisz [przykładowy szablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) z repozytorium szablonów szybkiego startu platformy Azure. Nazwa pliku lokalnego użyta w tym artykule jest **c:\MyTemplates\azuredeploy.jsw dniu**.

Jeśli nie używasz Azure Cloud Shell do wdrażania szablonów, musisz zainstalować Azure PowerShell i połączyć się z platformą Azure:

- **Zainstaluj Azure PowerShell polecenia cmdlet na komputerze lokalnym.** Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](/powershell/azure/get-started-azureps).
- **Połącz się z platformą Azure za pomocą polecenia [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Jeśli masz wiele subskrypcji platformy Azure, może być również konieczne uruchomienie [polecenie Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Aby uzyskać więcej informacji, zobacz [Korzystanie z wielu subskrypcji platformy Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Wdróż szablon lokalny

Poniższy przykład tworzy grupę zasobów i wdraża szablon z komputera lokalnego. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może składać się z maksymalnie 90 znaków. Nie może kończyć się kropką.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -Name ExampleDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Wdrożenie może potrwać kilka minut.

## <a name="deployment-name"></a>Nazwa wdrożenia

W poprzednim przykładzie nazwa wdrożenia została nadana `ExampleDeployment` . Jeśli nie podano nazwy wdrożenia, zostanie użyta nazwa pliku szablonu. Na przykład jeśli wdrażasz szablon o nazwie `azuredeploy.json` i nie określisz nazwy wdrożenia, wdrożenie ma nazwę `azuredeploy` .

Za każdym razem, gdy uruchamiasz wdrożenie, do historii wdrożenia grupy zasobów zostanie dodany wpis z nazwą wdrożenia. Jeśli zostanie uruchomione inne wdrożenie i nastąpi taka sama nazwa, wcześniejszy wpis zostanie zastąpiony bieżącym wdrożeniem. Jeśli chcesz zachować unikatowe wpisy w historii wdrożenia, nadaj każdemu wdrożeniu unikatową nazwę.

Aby utworzyć unikatową nazwę, można przypisać liczbę losową.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

Lub Dodaj wartość daty.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

W przypadku uruchamiania współbieżnych wdrożeń w tej samej grupie zasobów o tej samej nazwie wdrożenia zostanie wykonane tylko ostatnie wdrożenie. Wszystkie wdrożenia o tej samej nazwie, które nie zostały zakończone, są zastępowane przez ostatnie wdrożenie. Jeśli na przykład zostanie uruchomione wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage1` , a w tym samym czasie zostanie uruchomione inne wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage2` , zostanie wdrożone tylko jedno konto magazynu. Utworzone konto magazynu ma nazwę `storage2` .

Jeśli jednak zostanie uruchomione wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage1` i natychmiast po jego zakończeniu uruchomisz inne wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage2` , masz dwa konta magazynu. Jeden z nich ma nazwę `storage1` , a drugi o nazwie `storage2` . Jednak w historii wdrożenia znajduje się tylko jeden wpis.

W przypadku określenia unikatowej nazwy dla każdego wdrożenia można uruchomić je współbieżnie bez konfliktu. W przypadku uruchomienia wdrożenia o nazwie, `newStorage1` które wdraża konto magazynu o nazwie `storage1` i w tym samym czasie, należy uruchomić inne wdrożenie o nazwie, `newStorage2` które wdraża konto magazynu o nazwie `storage2` , istnieją dwa konta magazynu i dwa wpisy w historii wdrażania.

Aby uniknąć konfliktów z jednoczesnymi wdrożeniami i zapewnić unikatowe wpisy w historii wdrożenia, należy nadać każdemu wdrożeniu unikatową nazwę.

## <a name="deploy-remote-template"></a>Wdróż zdalny szablon

Zamiast przechowywać szablony ARM na komputerze lokalnym, warto przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła (na przykład GitHub). Możesz również przechowywać je na koncie usługi Azure Storage w celu uzyskania dostępu współdzielonego w organizacji.

Aby wdrożyć szablon zewnętrzny, użyj parametru **TemplateUri** . Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z usługi GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Poprzedni przykład wymaga publicznie dostępnego identyfikatora URI dla szablonu, który działa w większości scenariuszy, ponieważ szablon nie powinien zawierać poufnych danych. Jeśli musisz określić dane poufne (na przykład hasło administratora), przekaż tę wartość jako bezpieczny parametr. Jeśli jednak nie chcesz, aby szablon był dostępny publicznie, możesz go chronić, przechowując go w prywatnym kontenerze magazynu. Informacje o wdrażaniu szablonu wymagającego tokenu sygnatury dostępu współdzielonego (SAS) znajdują się w temacie [Deploy Private Template with SAS token](secure-template-with-sas-token.md). Aby przejść przez samouczek, zobacz [Samouczek: integrowanie Azure Key Vault w wdrożeniu szablonu ARM](template-tutorial-use-key-vault.md).

## <a name="deploy-template-spec"></a>Wdróż specyfikację szablonu

Zamiast wdrażać szablon lokalny lub zdalny, można utworzyć [specyfikację szablonu](template-specs.md). Specyfikacja szablonu jest zasobem w subskrypcji platformy Azure, który zawiera szablon ARM. Ułatwia to bezpieczne udostępnianie szablonu użytkownikom w organizacji. Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można udzielić dostępu do specyfikacji szablonu. Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

W poniższych przykładach pokazano, jak utworzyć i wdrożyć specyfikację szablonu. Te polecenia są dostępne tylko wtedy, gdy [zarejestrowano się w celu uzyskania podglądu](https://aka.ms/templateSpecOnboarding).

Najpierw należy utworzyć specyfikację szablonu, podając szablon ARM.

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateJsonFile ./mainTemplate.json
```

Następnie należy uzyskać identyfikator specyfikacji szablonu i wdrożyć ją.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

Aby uzyskać więcej informacji, zobacz [Azure Resource Manager szablonu specyfikacji (wersja zapoznawcza)](template-specs.md).

## <a name="preview-changes"></a>Podgląd zmian

Przed wdrożeniem szablonu można wyświetlić podgląd zmian wprowadzonych przez szablon w danym środowisku. Użyj [operacji działania warunkowego](template-deploy-what-if.md) , aby sprawdzić, czy szablon wprowadza zmiany, których oczekujesz. Co-jeśli również sprawdza poprawność szablonu pod kątem błędów.

## <a name="deploy-from-azure-cloud-shell"></a>Wdróż z Azure Cloud Shell

Za pomocą [Azure Cloud Shell](https://shell.azure.com) można wdrożyć szablon. Aby wdrożyć szablon zewnętrzny, podaj identyfikator URI szablonu. Aby wdrożyć szablon lokalny, należy najpierw załadować szablon do konta magazynu dla Cloud Shell. Aby przekazać pliki do powłoki, wybierz ikonę menu **przekazywanie/pobieranie plików** z okna powłoki.

Aby otworzyć Cloud Shell, przejdź do [https://shell.azure.com](https://shell.azure.com) lub wybierz opcję **Wypróbuj** z następującej sekcji kodu:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Aby wkleić kod do powłoki, kliknij prawym przyciskiem myszy wewnątrz powłoki, a następnie wybierz **Wklej**.

## <a name="pass-parameter-values"></a>Przekaż wartości parametrów

Aby przekazać wartości parametrów, można użyć parametrów wbudowanych lub pliku parametrów.

### <a name="inline-parameters"></a>Parametry wbudowane

Aby przekazać parametry wbudowane, Podaj nazwy parametru za pomocą `New-AzResourceGroupDeployment` polecenia. Na przykład aby przekazać ciąg i tablicę do szablonu, użyj:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Możesz również pobrać zawartość pliku i podać tę zawartość jako parametr wbudowany.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Pobieranie wartości parametru z pliku jest przydatne, gdy trzeba podać wartości konfiguracyjne. Można na przykład udostępnić [wartości z usługi Cloud-init dla maszyny wirtualnej z systemem Linux](../../virtual-machines/linux/using-cloud-init.md).

Jeśli musisz przekazać tablicę obiektów, Utwórz tabele skrótów w programie PowerShell i Dodaj je do tablicy. Przekaż tę tablicę jako parametr podczas wdrażania.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Pliki parametrów

Zamiast przekazywać parametry jako wartości wbudowane w skrypcie, można ułatwić korzystanie z pliku JSON, który zawiera wartości parametrów. Plik parametru może być plikiem lokalnym lub zewnętrznym z dostępnym identyfikatorem URI.

Aby uzyskać więcej informacji na temat pliku parametrów, zobacz [Tworzenie pliku parametrów Menedżer zasobów](parameter-files.md).

Aby przekazać lokalny plik parametrów, użyj parametru **TemplateParameterFile** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Aby przekazać zewnętrzny plik parametrów, użyj parametru **TemplateParameterUri** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>Następne kroki

- Aby wrócić do pomyślnego wdrożenia, gdy wystąpi błąd, zobacz [wycofywanie po pomyślnym wdrożeniu](rollback-on-error.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Azure Resource Manager trybami wdrożenia](deployment-modes.md).
- Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Opis struktury i składni szablonów ARM](template-syntax.md).
- Informacje o wdrażaniu szablonu wymagającego tokenu SAS można znaleźć w temacie [Deploy Private Template with SAS token](secure-template-with-sas-token.md).
