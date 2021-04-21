---
title: Wdrażanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure i szablonu
description: Wdrażanie Azure Resource Manager na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Zasoby są definiowane w szablonie Resource Manager lub pliku Bicep.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: f616a40f2683268f0cc26314fcc88ecca23bdbcf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782067"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Wdrażanie zasobów przy użyciu szablonów usługi ARM i interfejsu wiersza polecenia platformy Azure

W tym artykule wyjaśniono, jak używać interfejsu wiersza polecenia platformy Azure z szablonami Azure Resource Manager (szablony ARM) lub plikami Bicep w celu wdrożenia zasobów na platformie Azure. Jeśli nie znasz pojęć związanych z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz Omówienie wdrażania szablonów [lub](overview.md) Omówienie [rozwiązania Bicep](bicep-overview.md).

Polecenia wdrażania zostały zmienione w interfejsie wiersza polecenia platformy Azure w wersji 2.2.0. Przykłady w tym artykule wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Do wdrożenia plików Bicep potrzebny jest interfejs wiersza polecenia platformy Azure w wersji [2.20.0 lub nowszej.](/cli/azure/install-azure-cli)

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Jeśli nie masz zainstalowanego interfejsu wiersza polecenia platformy Azure, możesz użyć Azure Cloud Shell. Aby uzyskać więcej informacji, zobacz [Wdrażanie szablonów usługi ARM z Azure Cloud Shell](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Zakres wdrożenia

Wdrożenie można ukierunkować na grupę zasobów, subskrypcję, grupę zarządzania lub dzierżawę. W zależności od zakresu wdrożenia są dostępne różne polecenia.

* Aby wdrożyć w **grupie zasobów,** użyj [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template-or-bicep>
  ```

* Aby wdrożyć w **subskrypcji,** użyj [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Aby uzyskać więcej informacji na temat wdrożeń na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji.](deploy-to-subscription.md)

* Aby wdrożyć w **grupie zarządzania,** użyj [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Aby uzyskać więcej informacji na temat wdrożeń na poziomie grupy zarządzania, zobacz [Tworzenie zasobów na poziomie grupy zarządzania](deploy-to-management-group.md).

* Aby wdrożyć w **dzierżawie,** użyj [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Aby uzyskać więcej informacji na temat wdrożeń na poziomie dzierżawy, zobacz [Tworzenie zasobów na poziomie dzierżawy.](deploy-to-tenant.md)

Dla każdego zakresu użytkownik wdrażający szablon lub plik Bicep musi mieć uprawnienia wymagane do tworzenia zasobów.

## <a name="deploy-local-template-or-bicep-file"></a>Wdrażanie szablonu lokalnego lub pliku Bicep

Szablon można wdrożyć z komputera lokalnego lub z komputera przechowywanego zewnętrznie. W tej sekcji opisano wdrażanie szablonu lokalnego.

Jeśli wdrażasz w grupie zasobów, która nie istnieje, utwórz grupę zasobów. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może mieć do 90 znaków. Nazwa nie może kończyć się okresem.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Aby wdrożyć lokalny szablon lub plik Bicep, użyj `--template-file` parametru w poleceniu wdrożenia. W poniższym przykładzie pokazano również sposób ustawienia wartości parametru.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat, który zawiera wynik:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Wdrażanie szablonu zdalnego

> [!NOTE]
> Obecnie interfejs wiersza polecenia platformy Azure nie obsługuje wdrażania zdalnych plików Bicep. Użyj [interfejsu wiersza polecenia bicep,](./bicep-install.md#development-environment) aby skompilować plik Bicep do szablonu JSON, a następnie załaduj plik JSON do lokalizacji zdalnej.

Zamiast przechowywać szablony usługi ARM na komputerze lokalnym, możesz wolisz przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła (na przykład GitHub). Można je również przechowywać na koncie usługi Azure Storage w celu uzyskania dostępu współdzielonego w organizacji.

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

Jeśli wdrażasz w grupie zasobów, która nie istnieje, utwórz grupę zasobów. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może mieć do 90 znaków. Nazwa nie może kończyć się okresem.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Aby wdrożyć szablon zewnętrzny, użyj parametru `template-uri`.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Poprzedni przykład wymaga publicznie dostępnego dla szablonu URI, który działa w większości scenariuszy, ponieważ szablon nie powinien zawierać poufnych danych. Jeśli musisz określić poufne dane (takie jak hasło administratora), przekaż te wartości jako bezpieczny parametr. Jeśli jednak chcesz zarządzać dostępem do szablonu, rozważ użycie [specyfikacji szablonu](#deploy-template-spec).

Aby wdrożyć zdalne połączone szablony ze ścieżką względną przechowywaną na koncie magazynu, użyj , `query-string` aby określić token SAS:

```azurecli-interactive
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

Aby uzyskać więcej informacji, zobacz [Używanie ścieżki względnej dla połączonych szablonów.](./linked-templates.md#linked-template)

## <a name="deployment-name"></a>Nazwa wdrożenia

Podczas wdrażania szablonu usługi ARM można nadać nazwę wdrożenia. Ta nazwa może ułatwić pobranie wdrożenia z historii wdrożenia. Jeśli nie po podaj nazwy wdrożenia, zostanie użyta nazwa pliku szablonu. Jeśli na przykład wdrożysz szablon o nazwie i nie określisz nazwy `azuredeploy.json` wdrożenia, wdrożenie będzie nosiło nazwę `azuredeploy` .

Za każdym razem, gdy uruchamiasz wdrożenie, do historii wdrażania grupy zasobów jest dodawany wpis o nazwie wdrożenia. Jeśli zostanie uruchomione inne wdrożenie i nadasz mu taką samą nazwę, wcześniejszy wpis zostanie zastąpiony bieżącym wdrożeniem. Jeśli chcesz zachować unikatowe wpisy w historii wdrażania, nadaj każdemu wdrożeniu unikatową nazwę.

Aby utworzyć unikatową nazwę, możesz przypisać liczbę losową.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Możesz też dodać wartość daty.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Jeśli współbieżne wdrożenia są uruchamiane w tej samej grupie zasobów o tej samej nazwie wdrożenia, ukończone jest tylko ostatnie wdrożenie. Wszystkie wdrożenia o tej samej nazwie, które nie zostały ukończone, są zastępowane przez ostatnie wdrożenie. Jeśli na przykład uruchamiasz wdrożenie o nazwie , które wdraża konto magazynu o nazwie , a jednocześnie uruchamiasz inne wdrożenie o nazwie , które wdraża konto magazynu o nazwie , wdrażasz tylko jedno `newStorage` `storage1` konto `newStorage` `storage2` magazynu. Wynikowe konto magazynu ma nazwę `storage2` .

Jeśli jednak uruchamiasz wdrożenie o nazwie , które wdraża konto magazynu o nazwie , a natychmiast po jego zakończeniu uruchamiasz kolejne wdrożenie o nazwie , które wdraża konto magazynu o nazwie , masz dwa `newStorage` `storage1` konta `newStorage` `storage2` magazynu. Jeden ma nazwę `storage1` , a drugi o nazwie `storage2` . Jednak w historii wdrażania jest tylko jeden wpis.

Po określeniu unikatowej nazwy dla każdego wdrożenia można uruchamiać je współbieżnie bez konfliktu. Jeśli uruchamiasz wdrożenie o nazwie , które wdraża konto magazynu o nazwie , a jednocześnie uruchamiasz inne wdrożenie o nazwie , które wdraża konto magazynu o nazwie , wówczas w historii wdrażania masz dwa konta magazynu i dwa `newStorage1` `storage1` `newStorage2` `storage2` wpisy.

Aby uniknąć konfliktów z wdrożeniami współbieżnych i zapewnić unikatowe wpisy w historii wdrażania, nadaj każdemu wdrożeniu unikatową nazwę.

## <a name="deploy-template-spec"></a>Wdrażanie specyfikacji szablonu

> [!NOTE]
> Obecnie interfejs wiersza polecenia platformy Azure nie obsługuje tworzenia specyfikacji szablonu przez udostępnianie plików Bicep. Można jednak utworzyć plik Bicep z zasobem [Microsoft.Resources/templateSpecs,](/azure/templates/microsoft.resources/templatespecs) aby wdrożyć specyfikację szablonu. Oto [przykład](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep).

Zamiast wdrażać szablon lokalny lub zdalny, można utworzyć [specyfikację szablonu](template-specs.md). Specyfikacja szablonu to zasób w subskrypcji platformy Azure, który zawiera szablon usługi ARM. Ułatwia bezpieczne udostępnianie szablonu użytkownikom w organizacji. Aby udzielić dostępu do specyfikacji szablonu, należy użyć kontroli dostępu opartej na rolach (RBAC) platformy Azure. Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

Poniższe przykłady pokazują, jak utworzyć i wdrożyć specyfikację szablonu.

Najpierw utwórz specyfikację szablonu, podając szablon usługi ARM.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Następnie pobierz identyfikator specyfikacji szablonu i wdaj go.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Aby uzyskać więcej informacji, [zobacz Azure Resource Manager szablonu (wersja zapoznawcza).](template-specs.md)

## <a name="preview-changes"></a>Podgląd zmian

Przed wdrożeniem szablonu można wyświetlić podgląd zmian wprowadzonych w środowisku przez szablon. Użyj operacji [what-if,](template-deploy-what-if.md) aby sprawdzić, czy szablon wprowadza spodziewane zmiany. What-if również weryfikuje szablon pod celu sprawdzenia, czy wystąpiły błędy.

## <a name="parameters"></a>Parametry

Aby przekazać wartości parametrów, można użyć parametrów w tekście lub pliku parametrów.

### <a name="inline-parameters"></a>Parametry wbudowane

Aby przekazać parametry wbudowane, podaj wartości w parametrze `parameters` . Aby na przykład przekazać ciąg i tablicę do szablonu w powłoce Bash, użyj:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Jeśli używasz interfejsu wiersza polecenia platformy Azure z wierszem polecenia systemu Windows (CMD) lub programem PowerShell, przekaż tablicę w formacie: `exampleArray="['value1','value2']"` .

Możesz również pobrać zawartość pliku i podać ją jako parametr w tekście.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Pobieranie wartości parametru z pliku jest przydatne, gdy trzeba podać wartości konfiguracji. Możesz na przykład podać wartości [cloud-init dla maszyny wirtualnej z systemem Linux.](../../virtual-machines/linux/using-cloud-init.md)

Format _arrayContent.js_ jest:

```json
[
    "value1",
    "value2"
]
```

Aby przekazać obiekt, na przykład w celu ustawienia tagów, użyj kodu JSON. Na przykład szablon może zawierać parametr podobny do tego:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

W takim przypadku możesz przekazać ciąg JSON, aby ustawić parametr, jak pokazano w poniższym skrypcie powłoki Bash:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Wokół kodu JSON, który chcesz przekazać do obiektu, użyj podwójnych cudzysłowów.

### <a name="parameter-files"></a>Pliki parametrów

Zamiast przekazywania parametrów jako wartości śródwierszowych w skrypcie prostszym może się okazać użycie pliku JSON zawierającego wartości parametrów. Plik parametrów musi być plikiem lokalnym. Pliki parametrów zewnętrznych nie są obsługiwane w interfejsie wiersza polecenia platformy Azure. Zarówno szablon ARM, jak i plik Bicep używają plików parametrów JSON.

Aby uzyskać więcej informacji na temat pliku parametrów, zobacz [Tworzenie pliku parametrów usługi Resource Manager](parameter-files.md).

Aby przekazać plik parametrów lokalnych, użyj , aby określić plik lokalny `@` o nazwiestorage.parameters.jsna _._

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Obsługa rozszerzonego formatu JSON

Aby wdrożyć szablon z ciągami wielo wierszowymi lub komentarzami przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.3.0 lub starszej, należy użyć `--handle-extended-json-format` przełącznika .  Na przykład:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>Następne kroki

* Aby wrócić do pomyślnego wdrożenia, gdy wystąpi błąd, zobacz Wycofywanie po [błędzie pomyślnego wdrożenia.](rollback-on-error.md)
* Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Azure Resource Manager wdrożenia](deployment-modes.md).
* Aby zrozumieć, jak definiować parametry w szablonie, zobacz Understand the structure and syntax of ARM templates (Opis struktury [i składni szablonów usługi ARM).](template-syntax.md)
* Aby uzyskać porady dotyczące rozwiązywania typowych błędów wdrażania, zobacz [Troubleshoot common Azure deployment errors with Azure Resource Manager](common-deployment-errors.md)(Rozwiązywanie typowych błędów wdrażania platformy Azure za pomocą Azure Resource Manager ).
