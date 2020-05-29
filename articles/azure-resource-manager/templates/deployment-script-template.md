---
title: Używanie skryptów wdrażania w szablonach | Microsoft Docs
description: Użyj skryptów wdrażania w szablonach Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: jgao
ms.openlocfilehash: e3f3301ac78480c4d8ebbf909bafcefa025ff395
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84168577"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Używanie skryptów wdrażania w szablonach (wersja zapoznawcza)

Dowiedz się, jak używać skryptów wdrażania w szablonach zasobów platformy Azure. Po wywołaniu nowego typu zasobu `Microsoft.Resources/deploymentScripts` Użytkownicy mogą wykonywać skrypty wdrażania w ramach wdrożeń szablonów i przeglądać wyniki wykonania. Skrypty te mogą służyć do wykonywania czynności niestandardowych, takich jak:

- Dodawanie użytkowników do katalogu
- Wykonaj operacje płaszczyzny danych, na przykład skopiuj obiekty blob lub bazę danych inicjatora
- Wyszukiwanie i sprawdzanie poprawności klucza licencji
- Tworzenie certyfikatu z podpisem własnym
- Tworzenie obiektu w usłudze Azure AD
- Wyszukiwanie bloków adresów IP z niestandardowego systemu

Zalety skryptu wdrażania:

- Łatwe do kodu, używania i debugowania. Skrypty wdrażania można opracowywać w ulubionych środowiskach deweloperskich. Skrypty mogą być osadzone w szablonach lub w zewnętrznych plikach skryptów.
- Możesz określić język skryptu i platformę. Obecnie obsługiwane są Azure PowerShell i skrypty wdrażania interfejsu wiersza polecenia platformy Azure w środowisku systemu Linux.
- Zezwalaj na Określanie tożsamości, które są używane do wykonywania skryptów. Obecnie obsługiwana jest tylko [tożsamość zarządzana przypisana przez użytkownika platformy Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .
- Zezwalaj na przekazywanie do skryptu argumentów wiersza polecenia.
- Można określić dane wyjściowe skryptu i przekazać je z powrotem do wdrożenia.

Zasób skryptu wdrożenia jest dostępny tylko w regionach, w których usługa Azure Container instance jest dostępna.  Zobacz [dostępność zasobów dla Azure Container Instances w regionach świadczenia usługi Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Konto magazynu i wystąpienie kontenera są niezbędne do wykonania skryptu i rozwiązywania problemów. Dostępne są opcje określania istniejącego konta magazynu. w przeciwnym razie konto magazynu oraz wystąpienie kontenera są tworzone automatycznie przez usługę skryptów. Dwa automatycznie tworzone zasoby są zwykle usuwane przez usługę skryptów, gdy wykonywanie skryptu wdrożenia zostanie oddzielone w stanie terminalu. Opłaty są naliczane za zasoby do momentu usunięcia zasobów. Aby dowiedzieć się więcej, zobacz [Zasoby skryptu wdrożenia oczyszczanie](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Wymagania wstępne

- **Tożsamość zarządzana przypisana przez użytkownika z rolą współautor do docelowej grupy zasobów**. Ta tożsamość jest używana do wykonywania skryptów wdrażania. Aby wykonać operacje poza grupą zasobów, należy udzielić dodatkowych uprawnień. Na przykład Przypisz tożsamość do poziomu subskrypcji, jeśli chcesz utworzyć nową grupę zasobów.

  > [!NOTE]
  > Usługa skryptów tworzy konto magazynu (chyba że użytkownik określi istniejące konto magazynu) i wystąpienie kontenera w tle.  Tożsamość zarządzana przypisana przez użytkownika z rolą współautor na poziomie subskrypcji jest wymagana, jeśli subskrypcja nie zarejestrowała dostawców zasobów konta usługi Azure Storage (Microsoft. Storage) i Azure Container Instance (Microsoft. ContainerInstance).

  Aby utworzyć tożsamość, zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)lub przy [użyciu interfejsu wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)lub przy [użyciu Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Identyfikator tożsamości jest wymagany podczas wdrażania szablonu. Format tożsamości:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Użyj poniższego skryptu interfejsu wiersza polecenia lub programu PowerShell, aby uzyskać identyfikator, podając nazwę grupy zasobów i nazwę tożsamości.

  # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g $resourceGroupName -n $idName --query id
  ```

  # <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** lub **interfejs wiersza polecenia platformy Azure**. Listę obsługiwanych wersji Azure PowerShell można znaleźć [tutaj](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Aby zapoznać się z listą obsługiwanych wersji interfejsu wiersza polecenia platformy Azure, zobacz [tutaj](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > Skrypt wdrażania używa dostępnych obrazów interfejsu wiersza polecenia firmy Microsoft Container Registry (MCR). Zaświadczanie obrazu interfejsu wiersza polecenia dla skryptu wdrożenia trwa około miesiąca. Nie używaj wersji interfejsu wiersza polecenia, które zostały wydane w ciągu 30 dni. Aby znaleźć daty wydania dla obrazów, zobacz informacje o [wersji interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest). Jeśli używana jest nieobsługiwana wersja, komunikat o błędzie zawiera listę obsługiwanych wersji.

    Te wersje nie są potrzebne do wdrażania szablonów. Jednak te wersje są zbędne do lokalnego testowania skryptów wdrażania. Zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Można użyć wstępnie skonfigurowanego obrazu platformy Docker.  Zobacz [Konfigurowanie środowiska deweloperskiego](#configure-development-environment).

## <a name="sample-templates"></a>Przykładowe szablony

Poniższy kod JSON jest przykładem.  Najnowszy schemat szablonu można znaleźć [tutaj](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Przykład służy do celów demonstracyjnych.  **scriptContent** i **primaryScriptUris** nie mogą współistnieć w szablonie.

Szczegóły wartości właściwości:

- **Tożsamość**: usługa skryptu wdrażania używa zarządzanej tożsamości przypisanej przez użytkownika do wykonywania skryptów. Obecnie obsługiwana jest tylko tożsamość zarządzana przypisana przez użytkownika.
- **rodzaj**: Określ typ skryptu. Obecnie obsługiwane są Azure PowerShell i skrypty interfejsu wiersza polecenia platformy Azure. Wartości to **AzurePowerShell** i **AzureCLI**.
- **forceUpdateTag**: zmiana tej wartości między wdrożeniami szablonów Wymusza ponowne wykonanie skryptu wdrażania. Użyj funkcji newGuid () lub utcNow (), która musi być ustawiona jako wartość domyślna parametru. Aby dowiedzieć się więcej, zobacz [Uruchamianie skryptu więcej niż raz](#run-script-more-than-once).
- **containerSettings**: Określ ustawienia umożliwiające dostosowanie wystąpienia kontenera platformy Azure.  **containerGroupName** służy do określania nazwy grupy kontenerów.  Jeśli nie zostanie określony, nazwa grupy zostanie wygenerowana automatycznie.
- **storageAccountSettings**: Określ ustawienia do użycia istniejącego konta magazynu. Jeśli nie zostanie określony, konto magazynu zostanie utworzone automatycznie. Zobacz [Korzystanie z istniejącego konta magazynu](#use-an-existing-storage-account).
- **azPowerShellVersion** / **azCliVersion**: Określ wersję modułu, która ma zostać użyta. Aby zapoznać się z listą obsługiwanych wersji programu PowerShell i interfejsu wiersza polecenia, zobacz [wymagania wstępne](#prerequisites).
- **argumenty**: Określ wartości parametrów. Wartości są rozdzielone spacjami.
- **environmentVariables**: Określ zmienne środowiskowe, które mają zostać przekazane do skryptu. Aby uzyskać więcej informacji, zobacz temat [programowanie skryptów wdrażania](#develop-deployment-scripts).
- **scriptContent**: Określ zawartość skryptu. Aby uruchomić zewnętrzny skrypt, należy `primaryScriptUri` zamiast tego użyć. Aby zapoznać się z przykładami, zobacz [używanie skryptu wbudowanego](#use-inline-scripts) i [używanie skryptu zewnętrznego](#use-external-scripts).
- **primaryScriptUri**: Określ publicznie dostępny adres URL dla podstawowego skryptu wdrażania z obsługiwanymi rozszerzeniami plików.
- **supportingScriptUris**: Określ tablicę dostępnych publicznie adresów URL do obsługi plików, które są wywoływane w `ScriptContent` lub `PrimaryScriptUri` .
- **limit czasu**: Określ maksymalny dozwolony czas wykonywania skryptu określony w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Wartość domyślna to **P1D**.
- **cleanupPreference**. Określ preferencję oczyszczania zasobów wdrożenia, gdy wykonywanie skryptu jest odbierane w stanie terminalu. Ustawieniem domyślnym jest **zawsze**, co oznacza usunięcie zasobów pomimo stanu terminalu (zakończone powodzeniem, zakończone niepowodzeniem, anulowane). Aby dowiedzieć się więcej, zobacz [Oczyszczanie zasobów skryptu wdrażania](#clean-up-deployment-script-resources).
- **retentionInterval**: Określ interwał, dla którego usługa zachowuje zasoby skryptu wdrożenia po osiągnięciu przez wykonanie skryptu wdrożenia stanu terminalu. Zasoby skryptu wdrażania zostaną usunięte po upływie tego czasu trwania. Czas trwania zależy od [wzorca ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Wartość domyślna to **P1D**, co oznacza siedem dni. Ta właściwość jest używana, gdy cleanupPreference jest ustawiony na *onwygaśnięcia*. Właściwość *onwygaśnięcia* nie jest obecnie włączona. Aby dowiedzieć się więcej, zobacz [Oczyszczanie zasobów skryptu wdrażania](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Dodatkowe przykłady

- [Tworzenie i przypisywanie certyfikatu do magazynu kluczy](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [Utwórz i przypisz tożsamość zarządzaną przez użytkownika do grupy zasobów, a następnie uruchom skrypt wdrożenia](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> Zaleca się utworzenie tożsamości przypisanej do użytkownika i przyznanie uprawnień z wyprzedzeniem. Po utworzeniu tożsamości i udzieleniu uprawnień w tym samym szablonie, w którym uruchamiane są skrypty wdrażania, może zostać wyświetlony błąd związany z logowaniem i uprawnieniami. Trwa to trochę czasu, zanim uprawnienia staną się skuteczne.

## <a name="use-inline-scripts"></a>Użyj skryptów wbudowanych

Następujący szablon ma zdefiniowany jeden zasób z `Microsoft.Resources/deploymentScripts` typem. Wyróżniona część jest wbudowanym skryptem.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Ponieważ wbudowane skrypty wdrażania są ujęte w podwójne cudzysłowy, ciągi wewnątrz skryptów wdrażania muszą być wyprowadzane przy użyciu **&#92;** lub ujęte w apostrofy. Można również rozważyć użycie podstawienia ciągów, tak jak pokazano w poprzednim przykładzie JSON.

Skrypt przyjmuje jeden parametr i wyprowadza wartość parametru. **DeploymentScriptOutputs** jest używany do przechowywania danych wyjściowych.  W sekcji dane wyjściowe wiersz **wartości** pokazuje, jak uzyskać dostęp do przechowywanych wartości. `Write-Output`jest używany do celów debugowania. Aby dowiedzieć się, jak uzyskać dostęp do pliku wyjściowego, zobacz [debugowanie skryptów wdrażania](#debug-deployment-scripts).  Aby zapoznać się z opisami właściwości, zobacz [przykładowe szablony](#sample-templates).

Aby uruchomić skrypt, wybierz opcję **Wypróbuj** , aby otworzyć Cloud Shell, a następnie wklej poniższy kod do okienka powłoki.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

Dane wyjściowe wyglądają następująco:

![Skrypt wdrożenia szablonu Menedżer zasobów Witaj świecie Output](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Korzystanie ze skryptów zewnętrznych

Oprócz skryptów wbudowanych można również używać zewnętrznych plików skryptów. Obsługiwane są tylko podstawowe skrypty programu PowerShell z rozszerzeniem pliku **ps1** . W przypadku skryptów interfejsu wiersza polecenia skrypty podstawowe mogą mieć dowolne rozszerzenia (lub bez rozszerzenia), o ile skrypty są prawidłowymi skryptami bash. Aby użyć zewnętrznych plików skryptu, Zamień `scriptContent` na `primaryScriptUri` . Przykład:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Aby zobaczyć przykład, wybierz [tutaj](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Pliki skryptów zewnętrznych muszą być dostępne.  Aby zabezpieczyć pliki skryptów, które są przechowywane na kontach usługi Azure Storage, zobacz [wdrażanie prywatnego szablonu usługi ARM z tokenem SAS](./secure-template-with-sas-token.md).

Użytkownik jest odpowiedzialny za zapewnienie integralności skryptów, do których odwołuje się skrypt wdrożenia, to **PrimaryScriptUri** lub **SupportingScriptUris**.  Odwołuj się tylko do skryptów zaufanych.

## <a name="use-supporting-scripts"></a>Używanie skryptów pomocniczych

Skomplikowane logiki można rozdzielić na jeden lub więcej plików skryptów pomocniczych. `supportingScriptURI`Właściwość pozwala udostępnić tablicę identyfikatorów URI w plikach skryptów pomocniczych w razie potrzeby:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Pliki skryptów pomocniczych mogą być wywoływane zarówno ze skryptów wbudowanych, jak i podstawowych plików skryptów. Pliki skryptów pomocniczych nie mają żadnych ograniczeń dotyczących rozszerzenia pliku.

Pliki pomocnicze są kopiowane do azscripts/azscriptinput w czasie wykonywania. Użyj ścieżki względnej, aby odwoływać się do plików pomocniczych ze skryptów wbudowanych i podstawowych plików skryptów.

## <a name="work-with-outputs-from-powershell-script"></a>Pracuj z wynikami z poziomu skryptu programu PowerShell

Poniższy szablon pokazuje, jak przekazać wartości między dwoma zasobami deploymentScripts:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

W pierwszym zasobie należy zdefiniować zmienną o nazwie **$DeploymentScriptOutputs**i użyć jej do przechowywania wartości wyjściowych. Aby uzyskać dostęp do wartości wyjściowej z innego zasobu w ramach szablonu, użyj:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Pracuj z wynikami z poziomu skryptu interfejsu wiersza polecenia

Inaczej niż w przypadku skryptu wdrażania programu PowerShell obsługa interfejsu wiersza polecenia/bash nie uwidacznia wspólnej zmiennej do przechowywania danych wyjściowych skryptu, a zamiast tego występuje zmienna środowiskowa o nazwie **AZ_SCRIPTS_OUTPUT_PATH** , która przechowuje lokalizację, w której znajduje się plik danych wyjściowych skryptu. Jeśli skrypt wdrożenia jest uruchamiany z szablonu Menedżer zasobów, ta zmienna środowiskowa jest ustawiana automatycznie przez powłokę bash.

Dane wyjściowe skryptu wdrożenia muszą być zapisane w lokalizacji AZ_SCRIPTS_OUTPUT_PATH, a wyjście musi być prawidłowym obiektem ciągu JSON. Zawartość pliku musi być zapisana jako para klucz-wartość. Na przykład tablica ciągów jest przechowywana jako {"result": ["foo", "bar"]}.  Przechowywanie tylko wyników tablicowych, na przykład ["foo", "bar"], jest nieprawidłowe.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[JQ](https://stedolan.github.io/jq/) jest używany w poprzednim przykładzie. Zawiera obrazy kontenerów. Zobacz [Konfigurowanie środowiska deweloperskiego](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Programowanie skryptów wdrażania

### <a name="handle-non-terminating-errors"></a>Obsługuj błędy niepowodujące zakończenia

Można kontrolować, jak program PowerShell reaguje na błędy niepowodujące zakończenia przy użyciu zmiennej [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) w skrypcie wdrożenia. Usługa skryptu nie ustawia/nie zmienia wartości.  Pomimo wartości ustawionej dla $ErrorActionPreference skrypt wdrażania ustawia stan aprowizacji zasobów na *Niepowodzenie* , gdy wystąpi błąd w skrypcie.

### <a name="pass-secured-strings-to-deployment-script"></a>Przekaż zabezpieczone ciągi do skryptu wdrażania

Ustawienie zmiennych środowiskowych (zmiennych środowiskowych) w wystąpieniach kontenera pozwala na zapewnienie dynamicznej konfiguracji aplikacji lub skryptu uruchamianego przez kontener. Skrypt wdrażania obsługuje niezabezpieczone i zabezpieczone zmienne środowiskowe w taki sam sposób, jak wystąpienie kontenera platformy Azure. Aby uzyskać więcej informacji, zobacz [Ustawianie zmiennych środowiskowych w wystąpieniach kontenerów](../../container-instances/container-instances-environment-variables.md#secure-values).

Maksymalny dozwolony rozmiar zmiennych środowiskowych to 64 KB.

## <a name="debug-deployment-scripts"></a>Debuguj skrypty wdrażania

Usługa skryptów tworzy [konto magazynu](../../storage/common/storage-account-overview.md) (o ile nie zostanie określone istniejące konto magazynu) i [wystąpienie kontenera](../../container-instances/container-instances-overview.md) na potrzeby wykonywania skryptu. Jeśli te zasoby są tworzone automatycznie przez usługę skryptów, oba zasoby mają sufiks **azscripts** w nazwach zasobów.

![Nazwy zasobów skryptu wdrożenia szablonu Menedżer zasobów](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Skrypt użytkownika, wyniki wykonywania oraz plik stdout są przechowywane w udziałach plików konta magazynu. Istnieje folder o nazwie **azscripts**. W folderze istnieją dwa więcej folderów dla danych wejściowych i plików wyjściowych: **azscriptinput** i **azscriptoutput**.

Folder wyjściowy zawiera plik **ExecutionResult. JSON** i skrypt. Komunikat o błędzie wykonywania skryptu można zobaczyć w pliku **ExecutionResult. JSON**. Plik wyjściowy jest tworzony tylko po pomyślnym wykonaniu skryptu. Folder wejściowy zawiera systemowy plik skryptu programu PowerShell i pliki skryptów wdrażania użytkownika. Można zastąpić plik skryptu wdrożenia użytkownika zmienionym i ponownie uruchomić skrypt wdrażania z wystąpienia kontenera platformy Azure.

Informacje o wdrożeniu zasobów skryptu wdrażania można uzyskać na poziomie grupy zasobów i na poziomie subskrypcji przy użyciu interfejsu API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

W poniższym przykładzie zastosowano [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Dane wyjściowe są podobne do następujących:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

Dane wyjściowe pokazują stan wdrożenia i identyfikatory zasobów skryptu wdrażania.

Następujący interfejs API REST zwraca dziennik:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Działa tylko przed usunięciem zasobów skryptu wdrażania.

Aby wyświetlić zasób deploymentScripts w portalu, wybierz pozycję **Pokaż ukryte typy**:

![Skrypt wdrażania szablonu Menedżer zasobów, wyświetlanie ukrytych typów, Portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="use-an-existing-storage-account"></a>Użyj istniejącego konta magazynu

Konto magazynu i wystąpienie kontenera są niezbędne do wykonania skryptu i rozwiązywania problemów. Dostępne są opcje określania istniejącego konta magazynu. w przeciwnym razie konto magazynu oraz wystąpienie kontenera są tworzone automatycznie przez usługę skryptów. Wymagania dotyczące korzystania z istniejącego konta magazynu:

- Obsługiwane rodzaje kont magazynu:

    | SKU             | Obsługiwany rodzaj     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Magazyn, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Magazyn, StorageV2 |
    | Standard_RAGRS  | Magazyn, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Te kombinacje obsługują udział plików.  Aby uzyskać więcej informacji, zobacz [Tworzenie udziału plików platformy Azure](../../storage/files/storage-how-to-create-file-share.md) i [typów kont magazynu](../../storage/common/storage-account-overview.md).
- Reguły zapory konta magazynu nie są jeszcze obsługiwane. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../../storage/common/storage-network-security.md).
- Tożsamość zarządzana przypisana przez użytkownika skryptu wdrożenia musi mieć uprawnienia do zarządzania kontem magazynu, które obejmuje odczyt, tworzenie i usuwanie udziałów plików.

Aby określić istniejące konto magazynu, Dodaj następujący kod JSON do elementu właściwości `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName**: Określ nazwę konta magazynu.
- **storageAccountKey "**: Określ jeden z kluczy konta magazynu. Możesz użyć funkcji, [`listKeys()`](./template-functions-resource.md#listkeys) Aby pobrać klucz. Przykład:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Zobacz [przykładowe szablony](#sample-templates) , aby zapoznać się z kompletnym `Microsoft.Resources/deploymentScripts` przykładem definicji.

W przypadku korzystania z istniejącego konta magazynu usługa skryptów tworzy udział plików z unikatową nazwą. Zobacz [Oczyszczanie zasobów skryptu wdrażania](#clean-up-deployment-script-resources) , aby usługa skryptów czyści udział plików.

## <a name="clean-up-deployment-script-resources"></a>Czyszczenie zasobów skryptu wdrożenia

Konto magazynu i wystąpienie kontenera są niezbędne do wykonania skryptu i rozwiązywania problemów. Dostępne są opcje określania istniejącego konta magazynu. w przeciwnym razie konto magazynu oraz wystąpienie kontenera są tworzone automatycznie przez usługę skryptów. Dwa automatycznie utworzone zasoby są usuwane przez usługę skryptów, gdy wykonywanie skryptu wdrożenia zostanie oddzielone w stanie terminalu. Opłaty są naliczane za zasoby do momentu usunięcia zasobów. Aby uzyskać informacje o cenach, zobacz [Container Instances Cennik](https://azure.microsoft.com/pricing/details/container-instances/) i [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Cykl życia tych zasobów jest kontrolowany przez następujące właściwości w szablonie:

- **cleanupPreference**: Wyczyść preferencję, gdy wykonywanie skryptu zostanie odszukane w stanie terminalu. Obsługiwane są następujące wartości:

  - **Zawsze**: Usuń automatycznie utworzone zasoby, gdy wykonywanie skryptu zostanie rozpoczęte w stanie terminalu. Jeśli używane jest istniejące konto magazynu, usługa skryptów usuwa udział plików utworzony na koncie magazynu. Ponieważ zasób deploymentScripts może nadal występować po oczyszczeniu zasobów, usługi skryptów utrwalają wyniki wykonywania skryptu, na przykład stdout, Output, Value Return itp. przed usunięciem zasobów.
  - **OnSuccess**: usuwanie automatycznie utworzonych zasobów tylko wtedy, gdy wykonywanie skryptu zakończyło się pomyślnie. Jeśli używane jest istniejące konto magazynu, usługa skryptów usuwa udział plików tylko po pomyślnym wykonaniu skryptu. Nadal możesz uzyskać dostęp do zasobów, aby znaleźć informacje debugowania.
  - **Onwygaśnięcia**: usuwanie zasobów automatycznie tylko wtedy, gdy ustawienie **retentionInterval** wygasło. Jeśli używane jest istniejące konto magazynu, usługa skryptów usuwa udział plików, ale zachowa konto magazynu.

- **retentionInterval**: Określ interwał czasu, przez który zasób skryptu zostanie zachowany, a następnie zostanie usunięty.

> [!NOTE]
> Nie zaleca się używania konta magazynu i wystąpienia kontenera, które są generowane przez usługę skryptów do innych celów. Te dwa zasoby mogą zostać usunięte w zależności od cyklu życia skryptu.

## <a name="run-script-more-than-once"></a>Uruchom skrypt więcej niż raz

Wykonanie skryptu wdrożenia jest operacją idempotentne. Jeśli żadna z właściwości zasobów deploymentScripts (łącznie z skryptem wbudowanym) nie zostanie zmieniona, skrypt nie zostanie wykonany po ponownym wdrożeniu szablonu. Usługa skryptu wdrażania porównuje nazwy zasobów w szablonie z istniejącymi zasobami w tej samej grupie zasobów. Istnieją dwie opcje, jeśli chcesz wykonać ten sam skrypt wdrażania wielokrotnie:

- Zmień nazwę zasobu deploymentScripts. Na przykład użyj funkcji szablonu [UtcNow](./template-functions-date.md#utcnow) jako nazwy zasobu lub jako części nazwy zasobu. Zmiana nazwy zasobu powoduje utworzenie nowego zasobu deploymentScripts. Jest to dobre dla zachowania historii wykonywania skryptu.

    > [!NOTE]
    > Funkcja utcNow może być używana tylko w wartości domyślnej dla parametru.

- Określ inną wartość we `forceUpdateTag` Właściwości szablonu.  Na przykład użyj utcNow jako wartości.

> [!NOTE]
> Napisz skrypty wdrażania, które są idempotentne. Pozwala to zagwarantować, że jeśli zostaną uruchomione ponownie przypadkowo, nie spowoduje to zmian w systemie. Jeśli na przykład skrypt wdrożenia jest używany do tworzenia zasobów platformy Azure, przed utworzeniem zasobu należy sprawdzić, czy zasób nie istnieje, więc skrypt powiedzie się lub nie utworzysz ponownie zasobu.

## <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

Możesz użyć wstępnie skonfigurowanego obrazu kontenera Docker jako środowiska programistycznego skryptu wdrożenia. Poniższa procedura przedstawia sposób konfigurowania obrazu platformy Docker w systemie Windows. W przypadku systemów Linux i Mac można znaleźć informacje z Internetu.

1. Zainstaluj program [Docker Desktop](https://www.docker.com/products/docker-desktop) na komputerze deweloperskim.
1. Otwórz pulpit Docker.
1. Wybierz ikonę platformy Docker na pasku zadań, a następnie wybierz pozycję **Ustawienia**.
1. Wybierz pozycję **dyski udostępnione**, wybierz dysk lokalny, który ma być dostępny dla kontenerów, a następnie wybierz pozycję **Zastosuj** .

    ![Dysk platformy Docker wdrażania szablonu Menedżer zasobów](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Wprowadź swoje poświadczenia systemu Windows w wierszu polecenia.
1. Otwórz okno terminalu, w wierszu polecenia lub w programie Windows PowerShell (nie używaj programu PowerShell ISE).
1. Pobierz obraz kontenera skryptu wdrożenia na komputer lokalny:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    W przykładzie używane jest 2.7.0 wersji programu PowerShell.

    Aby ściągnąć obraz interfejsu wiersza polecenia z Container Registry firmy Microsoft (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    W tym przykładzie zastosowano interfejs wiersza polecenia w wersji 2.0.80. Skrypt wdrażania używa domyślnych obrazów kontenerów interfejsu wiersza polecenia znalezionych w [tym miejscu](https://hub.docker.com/_/microsoft-azure-cli).

1. Uruchom lokalnie obraz platformy Docker.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Zastąp ** &lt; literę>sterownika hosta** i ** &lt; nazwę katalogu hosta>** z istniejącym folderem na dysku udostępnionym.  Mapuje folder do folderu **/Data** w kontenerze. Aby uzyskać przykłady mapowania D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-oznacza to** utrzymywanie obrazu kontenera.

    Przykład interfejsu wiersza polecenia:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Po wyświetleniu monitu wybierz pozycję **Udostępnij** .
1. Poniższy zrzut ekranu przedstawia sposób uruchomienia skryptu programu PowerShell z uwzględnieniem pliku HelloWorld. ps1 w folderze d:\docker.

    ![Skrypt wdrażania szablonu Menedżer zasobów — polecenie Docker](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Po pomyślnym przetestowaniu skryptu można go użyć jako skryptu wdrażania.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania ze skryptów wdrażania. Aby zapoznać się z samouczkiem dotyczącym skryptu wdrożenia:

> [!div class="nextstepaction"]
> [Samouczek: używanie skryptów wdrażania w szablonach Azure Resource Manager](./template-tutorial-deployment-script.md)