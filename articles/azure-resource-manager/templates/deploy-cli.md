---
title: Wdrażanie zasobów za pomocą interfejsu wiersza polecenia platformy Azure i szablonu
description: Użyj usługi Azure Resource Manager i azure cli do wdrażania zasobów na platformie Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8ee15699a085178add05137be895fe6b660b715b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685700"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Wdrażanie zasobów za pomocą szablonów ARM i interfejsu wiersza polecenia platformy Azure

W tym artykule wyjaśniono, jak używać interfejsu wiersza polecenia platformy Azure z szablonami usługi Azure Resource Manager (ARM) do wdrażania zasobów na platformie Azure. Jeśli nie znasz pojęć wdrażania rozwiązań platformy Azure i zarządzania nimi, zobacz [omówienie wdrażania szablonów.](overview.md)

Polecenia wdrażania zostały zmienione w usłudze Azure CLI w wersji 2.2.0. Przykłady w tym artykule wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Jeśli nie masz zainstalowanego interfejsu wiersza polecenia platformy Azure, możesz użyć [powłoki Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Zakres wdrożenia

Wdrożenie można kierować do grupy zasobów, subskrypcji, grupy zarządzania lub dzierżawy. W większości przypadków zostaniesz skierowany do grupy zasobów. Aby zastosować zasady i przypisania ról w większym zakresie, użyj subskrypcji, grupy zarządzania lub wdrożeń dzierżawy. Podczas wdrażania w ramach subskrypcji można utworzyć grupę zasobów i wdrożyć do niej zasoby.

W zależności od zakresu wdrożenia należy użyć różnych poleceń.

Aby wdrożyć w **grupie zasobów,** użyj [grupy wdrażania AZ create:](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create)

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

Aby wdrożyć w **ramach subskrypcji,** należy użyć [sub tworzenia wdrożenia az:](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create)

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

Aby uzyskać więcej informacji na temat wdrożeń na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

Aby wdrożyć w **grupie zarządzania,** należy użyć [az deployment mg create:](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

Aby uzyskać więcej informacji na temat wdrożeń na poziomie grupy zarządzania, zobacz [Tworzenie zasobów na poziomie grupy zarządzania](deploy-to-management-group.md).

Aby wdrożyć w **dzierżawie,** użyj [az wdrożenia dzierżawy utworzyć:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

Aby uzyskać więcej informacji na temat wdrożeń na poziomie dzierżawy, zobacz [Tworzenie zasobów na poziomie dzierżawy.](deploy-to-tenant.md)

Przykłady w tym artykule używają wdrożeń grup zasobów.

## <a name="deploy-local-template"></a>Wdrażanie szablonu lokalnego

Podczas wdrażania zasobów na platformie Azure:

1. Zaloguj się do swojego konta platformy Azure
2. Utwórz grupę zasobów, która służy jako kontener dla wdrożonych zasobów. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może to być do 90 znaków. To nie może zakończyć się w okresie.
3. Wdrażanie w grupie zasobów szablonu definiujący zasoby do utworzenia

Szablon może zawierać parametry, które umożliwiają dostosowanie wdrożenia. Na przykład można podać wartości, które są dostosowane do określonego środowiska (takich jak dev, test i produkcji). Przykładowy szablon definiuje parametr jednostki SKU konta magazynu.

Poniższy przykład tworzy grupę zasobów i wdraża szablon z komputera lokalnego:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat zawierający wynik:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Wdrażanie szablonu zdalnego

Zamiast przechowywać szablony ARM na komputerze lokalnym, możesz chcieć przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła (na przykład GitHub). Można też przechowywać je na koncie magazynu platformy Azure w celu uzyskania dostępu współużytkowego w organizacji.

Aby wdrożyć szablon zewnętrzny, należy użyć parametru **template-uri.** Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z usługi GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

W poprzednim przykładzie wymaga publicznie dostępnego identyfikatora URI dla szablonu, który działa w większości scenariuszy, ponieważ szablon nie powinien zawierać poufnych danych. Jeśli musisz określić poufne dane (takie jak hasło administratora), przekaż tę wartość jako bezpieczny parametr. Jednak jeśli nie chcesz, aby szablon był publicznie dostępny, możesz go chronić, przechowując go w kontenerze magazynu prywatnego. Aby uzyskać informacje dotyczące wdrażania szablonu, który wymaga tokenu sygnatury dostępu współdzielonego (SAS), zobacz [Wdrażanie szablonu prywatnego za pomocą tokenu sygnatury dostępu Współdzielonego.](secure-template-with-sas-token.md)

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

W przysłonie chmury użyj następujących poleceń:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parametry

Aby przekazać wartości parametrów, można użyć parametrów wbudowanych lub pliku parametrów.

### <a name="inline-parameters"></a>Parametry wbudowane

Aby przekazać parametry wbudowane, `parameters`podaj wartości w pliku . Na przykład, aby przekazać ciąg i tablicę do szablonu jest powłoka Bash, użyj:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Jeśli używasz narzędzia Azure CLI z wierszem polecenia systemu Windows (CMD) `exampleArray="['value1','value2']"`lub programem PowerShell, przekaż tablicę w formacie: .

Można również uzyskać zawartość pliku i podać tę zawartość jako parametr wbudowany.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Uzyskanie wartości parametru z pliku jest przydatne, gdy trzeba podać wartości konfiguracji. Na przykład można podać [wartości cloud-init dla maszyny wirtualnej systemu Linux.](../../virtual-machines/linux/using-cloud-init.md)

Format arrayContent.json jest:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Pliki parametrów

Zamiast przekazywania parametrów jako wartości wbudowanych w skrypcie, może być łatwiej użyć pliku JSON, który zawiera wartości parametrów. Plik parametrów musi być plikiem lokalnym. Pliki parametrów zewnętrznych nie są obsługiwane w usłudze Azure CLI.

Aby uzyskać więcej informacji na temat pliku parametrów, zobacz [Tworzenie pliku parametrów Menedżera zasobów](parameter-files.md).

Aby przekazać lokalny plik `@` parametrów, należy określić plik lokalny o nazwie storage.parameters.json.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Obsługa rozszerzonego formatu JSON

Aby wdrożyć szablon z ciągami wielowierszowymi lub komentarzami przy użyciu interfejsu wiersza polecenia `--handle-extended-json-format` platformy Azure w wersji 2.3.0 lub starszej, należy użyć przełącznika.  Przykład:

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

## <a name="test-a-template-deployment"></a>Testowanie wdrożenia szablonu

Aby przetestować wartości szablonu i parametrów bez faktycznego wdrażania jakichkolwiek zasobów, należy użyć [grupy wdrażania AZ.](/cli/azure/group/deployment)

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Jeśli nie zostaną wykryte żadne błędy, polecenie zwraca informacje o wdrożeniu testowym. W szczególności należy zauważyć, że wartość **błędu** jest null.

```output
{
  "error": null,
  "properties": {
      ...
```

Jeśli zostanie wykryty błąd, polecenie zwraca komunikat o błędzie. Na przykład przekazanie niepoprawnej wartości dla jednostki SKU konta magazynu zwraca następujący błąd:

```output
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Jeśli szablon ma błąd składniowy, polecenie zwraca błąd wskazujący, że nie można przeanalizować szablonu. Komunikat wskazuje numer wiersza i położenie błędu analizy.

```output
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Następne kroki

- Aby przywrócić pomyślne wdrożenie po wyświetleniu błędu, zobacz [Wycofywanie w przypadku błędu do pomyślnego wdrożenia](rollback-on-error.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Tryby wdrażania usługi Azure Resource Manager](deployment-modes.md).
- Aby dowiedzieć się, jak zdefiniować parametry w szablonie, zobacz [Opis struktury i składni szablonów ARM](template-syntax.md).
- Aby uzyskać wskazówki dotyczące rozwiązywania typowych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure za pomocą usługi Azure Resource Manager](common-deployment-errors.md).
- Aby uzyskać informacje dotyczące wdrażania szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [Wdrażanie szablonu prywatnego za pomocą tokenu sygnatury dostępu Współdzielonego](secure-template-with-sas-token.md).
- Aby bezpiecznie wdrożyć usługę w więcej niż jednym regionie, zobacz [Usługa Azure Deployment Manager](deployment-manager-overview.md).
