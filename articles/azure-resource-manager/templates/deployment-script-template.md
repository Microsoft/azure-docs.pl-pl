---
title: Używanie skryptów wdrażania w szablonach | Microsoft Docs
description: używanie skryptów wdrażania w Azure Resource Manager szablonów.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: jgao
ms.openlocfilehash: c39b332e9ee62a8502d5e2fdf155819194a30e34
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762195"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Używanie skryptów wdrażania w szablonach usługi ARM

Dowiedz się, jak używać skryptów wdrażania w szablonach zasobów platformy Azure (szablonach usługi ARM). W przypadku nowego typu zasobu o nazwie użytkownicy mogą wykonywać skrypty we wdrożeniach `Microsoft.Resources/deploymentScripts` szablonów i przeglądać wyniki wykonywania. Te skrypty mogą służyć do wykonywania niestandardowych kroków, takich jak:

- dodawanie użytkowników do katalogu
- wykonywanie operacji na płaszczyźnie danych, na przykład kopiowanie obiektów blob lub inicjuje bazę danych
- sprawdzanie i weryfikowanie klucza licencji
- tworzenie certyfikatu z podpisem własnym
- tworzenie obiektu w usłudze Azure AD
- wyszukiwania bloków adresów IP z systemu niestandardowego

Zalety skryptu wdrażania:

- Łatwo kodować, używać i debugować. Skrypty wdrażania można opracowywać w ulubionych środowiskach deweloperskich. Skrypty można osadzić w szablonach lub zewnętrznych plikach skryptów.
- Można określić język i platformę skryptów. Obecnie obsługiwane Azure PowerShell i skrypty wdrażania interfejsu wiersza polecenia platformy Azure w środowisku systemu Linux.
- Zezwalaj na przekazywanie argumentów wiersza polecenia do skryptu.
- Może określać dane wyjściowe skryptu i przekazywać je z powrotem do wdrożenia.

Zasób skryptu wdrażania jest dostępny tylko w regionach, w których jest dostępne wystąpienie kontenera platformy Azure.  Zobacz [Dostępność zasobów dla Azure Container Instances w regionach świadczenia usługi Azure.](../../container-instances/container-instances-region-availability.md)

> [!IMPORTANT]
> Konto magazynu i wystąpienie kontenera są potrzebne do wykonywania skryptów i rozwiązywania problemów. Dostępne są opcje określania istniejącego konta magazynu. W przeciwnym razie konto magazynu wraz z wystąpieniem kontenera są tworzone automatycznie przez usługę skryptu. Dwa automatycznie utworzone zasoby są zwykle usuwane przez usługę skryptów, gdy wykonywanie skryptu wdrażania jest w stanie terminalu. Za zasoby są naliczane faktury do momentu usunięcia zasobów. Aby dowiedzieć się więcej, zobacz [Oczyszczanie zasobów skryptu wdrażania.](#clean-up-deployment-script-resources)

> [!IMPORTANT]
> Interfejs API zasobów deploymentScripts w wersji 2020-10-01 obsługuje [usługę OnBehalfofTokens(OBO).](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md) Za pomocą OBO usługa skryptu wdrażania używa tokenu podmiotu zabezpieczeń wdrożenia do utworzenia podstawowych zasobów do uruchamiania skryptów wdrażania, takich jak wystąpienie kontenera platformy Azure, konto usługi Azure Storage i przypisania ról dla tożsamości zarządzanej. W starszej wersji interfejsu API tożsamość zarządzana jest używana do tworzenia tych zasobów.
> Logika ponawiania prób logowania do platformy Azure jest teraz wbudowana w skrypt otoki. Jeśli udzielisz uprawnień w tym samym szablonie, w którym są uruchamiane skrypty wdrażania. Usługa skryptu wdrażania ponownych prób logowania się przez 10 minut z 10-sekundowym interwałem, dopóki przypisanie roli tożsamości zarządzanej nie zostanie zreplikowane.

## <a name="configure-the-minimum-permissions"></a>Konfigurowanie minimalnych uprawnień

W przypadku interfejsu API skryptu wdrażania w wersji 2020-10-01 lub nowszej podmiot zabezpieczeń wdrożenia służy do tworzenia podstawowych zasobów wymaganych do wykonania zasobu skryptu wdrożenia — konta magazynu i wystąpienia kontenera platformy Azure. Jeśli skrypt wymaga uwierzytelnienia na platformie Azure i wykonania akcji specyficznych dla platformy Azure, zalecamy udostępnienie skryptu z tożsamością zarządzaną przypisaną przez użytkownika. Tożsamość zarządzana musi mieć wymagany dostęp do ukończenia operacji w skrypcie.

Aby skonfigurować uprawnienia o najmniejszych uprawnieniach, potrzebne są:

- Przypisz rolę niestandardową z następującymi właściwościami do jednostki wdrożenia:

  ```json
  {
    "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
    "description": "Configure least privilege for the deployment principal in deployment script",
    "type": "customRole",
    "IsCustom": true,
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/*",
          "Microsoft.ContainerInstance/containerGroups/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/deploymentScripts/*"
        ],
      }
    ],
    "assignableScopes": [
      "[subscription().id]"
    ]
  }
  ```

  Jeśli usługi Azure Storage i dostawcy zasobów wystąpienia kontenera platformy Azure nie zostały zarejestrowane, należy również dodać i `Microsoft.Storage/register/action` `Microsoft.ContainerInstance/register/action` .

- Jeśli jest używana tożsamość zarządzana,  podmiot zabezpieczeń wdrożenia musi mieć rolę Operator tożsamości zarządzanej (rolę wbudowaną) przypisaną do zasobu tożsamości zarządzanej.

## <a name="sample-templates"></a>Przykładowe szablony

Poniższy kod JSON jest przykładem. Aby uzyskać więcej informacji, zobacz najnowszy [schemat szablonu](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
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
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
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
> Przykład ma na celu demonstracyjne. Właściwości i `scriptContent` `primaryScriptUri` nie mogą współistnieć w szablonie.

> [!NOTE]
> _ScriptContent_ pokazuje skrypt z wieloma wierszami.  Potok Azure Portal i Azure DevOps nie może ująć skryptu wdrożenia w wiele wierszy. Możesz utworzyć łańcuch poleceń programu PowerShell (przy użyciu średników lub _\\ r \\ n_ _\\ lub n_) w jeden wiersz lub użyć właściwości z zewnętrznym `primaryScriptUri` plikiem skryptu. Dostępnych jest wiele bezpłatnych narzędzi ucieczki/unescape ciągu JSON. Przykładowy adres URL to [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html).

Szczegóły wartości właściwości:

- `identity`: w przypadku interfejsu API skryptu wdrażania w wersji 2020-10-01 lub nowszej tożsamość zarządzana przypisana przez użytkownika jest opcjonalna, chyba że w skrypcie należy wykonać jakiekolwiek akcje specyficzne dla platformy Azure.  W przypadku interfejsu API w wersji 2019-10-01-preview tożsamość zarządzana jest wymagana, ponieważ usługa skryptu wdrażania używa jej do wykonywania skryptów. Po podano właściwość tożsamości, usługa skryptu wywołuje przed `Connect-AzAccount -Identity` wywołania skryptu użytkownika. Obecnie obsługiwana jest tylko tożsamość zarządzana przypisana przez użytkownika. Aby zalogować się przy użyciu innej tożsamości, możesz wywołać [connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) w skrypcie.
- `kind`: określ typ skryptu. Obecnie obsługiwane Azure PowerShell interfejsu wiersza polecenia platformy Azure i skrypty interfejsu wiersza polecenia platformy Azure. Wartości to **AzurePowerShell** i **AzureCLI.**
- `forceUpdateTag`: Zmiana tej wartości między wdrożeniami szablonów wymusza ponowne wykonanie skryptu wdrożenia. Jeśli używasz funkcji lub , obie funkcje mogą być używane tylko w wartości `newGuid()` `utcNow()` domyślnej parametru. Aby dowiedzieć się więcej, zobacz [Uruchamianie skryptu więcej niż raz.](#run-script-more-than-once)
- `containerSettings`: określ ustawienia, aby dostosować wystąpienie kontenera platformy Azure. Skrypt wdrażania wymaga nowego wystąpienia kontenera platformy Azure. Nie można określić istniejącego wystąpienia kontenera platformy Azure. Można jednak dostosować nazwę grupy kontenerów przy użyciu funkcji `containerGroupName` . Jeśli nie zostanie określona, nazwa grupy jest generowana automatycznie.
- `storageAccountSettings`: określ ustawienia, aby użyć istniejącego konta magazynu. Jeśli `storageAccountName` nie zostanie określony, konto magazynu zostanie utworzone automatycznie. Zobacz [Używanie istniejącego konta magazynu.](#use-existing-storage-account)
- `azPowerShellVersion`/`azCliVersion`: określ wersję modułu, która ma być używana. Zobacz listę [obsługiwanych wersji Azure PowerShell .](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list) Zobacz listę obsługiwanych [wersji interfejsu wiersza polecenia platformy Azure.](https://mcr.microsoft.com/v2/azure-cli/tags/list)

  >[!IMPORTANT]
  > Skrypt wdrażania używa dostępnych obrazów interfejsu wiersza polecenia Microsoft Container Registry (MCR). Certyfikowanie obrazu interfejsu wiersza polecenia dla skryptu wdrażania trwa około miesiąca. Nie używaj wersji interfejsu wiersza polecenia, które zostały wydane w ciągu 30 dni. Aby znaleźć daty wydania obrazów, zobacz Informacje o wersji interfejsu [wiersza polecenia platformy Azure](/cli/azure/release-notes-azure-cli). Jeśli jest używana nieobsługiwana wersja, komunikat o błędzie zawiera listę obsługiwanych wersji.

- `arguments`: określ wartości parametrów. Wartości są rozdzielone spacjami.

  Skrypty wdrażania dzielą argumenty na tablicę ciągów, wywołując wywołanie systemowe [CommandLineToArgvW.](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) Ten krok jest niezbędny, ponieważ argumenty są przekazywane jako właściwość polecenia do wystąpienia kontenera platformy Azure, [a](/rest/api/container-instances/containergroups/createorupdate#containerexec) właściwość polecenia jest tablicą ciągów.

  Jeśli argumenty zawierają znaki ucieczki, użyj [JsonEscaper,](https://www.jsonescaper.com/) aby dwukrotnie użyć znaków ucieczki. Wklej oryginalny ciąg ze znakami ucieczki do narzędzia, a następnie wybierz pozycję **Ucieczka**.  Narzędzie wyprowadza podwójny ciąg ucieczki. Na przykład w poprzednim przykładowym szablonie argumentem jest `-name \"John Dole\"` . Ciąg ucieczki to `-name \\\"John Dole\\\"` .

  Aby przekazać parametr szablonu usługi ARM typu obiekt jako argument, przekonwertuj obiekt na ciąg przy użyciu funkcji [string(),](./template-functions-string.md#string) a następnie użyj funkcji [replace(),](./template-functions-string.md#replace) aby zastąpić dowolny element elementem `\"` `\\\"` . Na przykład:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  Aby uzyskać więcej informacji, zobacz [przykładowy szablon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- `environmentVariables`: określ zmienne środowiskowe do przekazania do skryptu. Aby uzyskać więcej informacji, zobacz [Tworzenie skryptów wdrażania.](#develop-deployment-scripts)
- `scriptContent`: określ zawartość skryptu. Aby uruchomić skrypt zewnętrzny, użyj `primaryScriptUri` polecenia . Przykłady można znaleźć w [tematach Use inline script](#use-inline-scripts) (Używanie skryptu wbudowanego) [i Use external script (Używanie skryptu zewnętrznego).](#use-external-scripts)
- `primaryScriptUri`: określ publicznie dostępny adres URL podstawowego skryptu wdrażania z obsługiwanymi rozszerzeniami plików. Aby uzyskać więcej informacji, zobacz [Use external scripts (Używanie skryptów zewnętrznych).](#use-external-scripts)
- `supportingScriptUris`: określ tablicę publicznie dostępnych adresów URL dla plików obsługi, które są wywoływane w pliku `scriptContent` lub `primaryScriptUri` . Aby uzyskać więcej informacji, zobacz [Use external scripts (Używanie skryptów zewnętrznych).](#use-external-scripts)
- `timeout`: określ maksymalny dozwolony czas wykonywania skryptu określony w [formacie ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601) Wartość domyślna to **P1D.**
- `cleanupPreference`. Określ preferencję czyszczenia zasobów wdrożenia, gdy wykonywanie skryptu jest w stanie terminalu. Ustawieniem domyślnym **jest Zawsze**, co oznacza usunięcie zasobów pomimo stanu terminalu (Powodzenie, Niepowodzenie, Anulowano). Aby dowiedzieć się więcej, zobacz [Oczyszczanie zasobów skryptu wdrażania.](#clean-up-deployment-script-resources)
- `retentionInterval`: określ interwał przechowywania zasobów skryptu wdrożenia przez usługę po osiągnięciu stanu terminalu przez wykonanie skryptu wdrożenia. Zasoby skryptu wdrożenia zostaną usunięte po upływie tego czasu. Czas trwania jest oparty na [wzorcu ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601) Okres przechowywania wynosi od 1 do 26 godzin (PT26H). Ta właściwość jest używana, gdy `cleanupPreference` jest ustawiona **na wartość OnExpiration**. Aby dowiedzieć się więcej, zobacz [Oczyszczanie zasobów skryptu wdrażania.](#clean-up-deployment-script-resources)

### <a name="additional-samples"></a>Dodatkowe przykłady

- [Przykład 1:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)tworzenie magazynu kluczy i używanie skryptu wdrażania w celu przypisania certyfikatu do magazynu kluczy.
- [Przykład 2:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json)utwórz grupę zasobów na poziomie subskrypcji, utwórz magazyn kluczy w grupie zasobów, a następnie użyj skryptu wdrażania, aby przypisać certyfikat do magazynu kluczy.
- [Przykład 3:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)tworzenie tożsamości zarządzanej przypisanej przez użytkownika, przypisywanie roli współautora do tożsamości na poziomie grupy zasobów, tworzenie magazynu kluczy, a następnie przypisywanie certyfikatu do magazynu kluczy przy użyciu skryptu wdrażania.

## <a name="use-inline-scripts"></a>Korzystanie ze skryptów w tekście

Poniższy szablon ma jeden zasób zdefiniowany za pomocą `Microsoft.Resources/deploymentScripts` typu . Wyróżniona część to skrypt w tekście.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Ponieważ skrypty wdrażania wbudowanego są ujęte w cudzysłów, ciągi wewnątrz skryptów wdrażania muszą być ucieczką przy użyciu ukośnika odwrotnego (**&#92;**) lub ujęty w pojedynczy cudzysłów. Możesz również rozważyć użycie podstawienia ciągu, jak pokazano w poprzednim przykładzie JSON.

Skrypt przyjmuje jeden parametr i wyprowadza wartość parametru. `DeploymentScriptOutputs` Służy do przechowywania danych wyjściowych. W sekcji outputs (dane `value` wyjściowe) wiersz pokazuje, jak uzyskać dostęp do przechowywanych wartości. `Write-Output` Jest używany na potrzeby debugowania. Aby dowiedzieć się, jak uzyskać dostęp do pliku wyjściowego, zobacz [Monitorowanie skryptów wdrażania i rozwiązywanie problemów.](#monitor-and-troubleshoot-deployment-scripts) Opisy właściwości zawiera temat [Przykładowe szablony](#sample-templates).

Aby uruchomić skrypt, wybierz pozycję **Wypróbuj,** aby otworzyć Cloud Shell, a następnie wklej następujący kod w okienku powłoki.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

Dane wyjściowe wyglądają następująco:

![Resource Manager danych wyjściowych hello world skryptu wdrażania szablonu](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Korzystanie ze skryptów zewnętrznych

Oprócz skryptów w tekście można również używać zewnętrznych plików skryptów. Obsługiwane są tylko podstawowe skrypty programu PowerShell z rozszerzeniem pliku _ps1._ W przypadku skryptów interfejsu wiersza polecenia skrypty podstawowe mogą mieć dowolne rozszerzenia (lub bez rozszerzenia), o ile skrypty są prawidłowymi skryptami powłoki bash. Aby użyć zewnętrznych plików skryptów, zastąp `scriptContent` . `primaryScriptUri` Na przykład:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Aby uzyskać więcej informacji, zobacz [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Pliki skryptów zewnętrznych muszą być dostępne. Aby zabezpieczyć pliki skryptów przechowywane na kontach usługi Azure Storage, wygeneruj token SAS i uwzględnij go w adresie URI szablonu. Ustaw czas wygaśnięcia, aby umożliwić wystarczająco dużo czasu na ukończenie wdrożenia. Aby uzyskać więcej informacji, zobacz Deploy private ARM template with SAS token (Wdrażanie [prywatnego szablonu usługi ARM przy użyciu tokenu SAS).](./secure-template-with-sas-token.md)

Odpowiadasz za zapewnienie integralności skryptów, do których odwołuje się skrypt wdrażania, `primaryScriptUri` lub `supportingScriptUris` . Przywołuj tylko zaufane skrypty.

## <a name="use-supporting-scripts"></a>Korzystanie ze skryptów obsługi

Złożone logike można rozdzielić na jeden lub więcej plików skryptów obsługi. Właściwość umożliwia dostarczenie tablicy URI do plików skryptów obsługi `supportingScriptUris` w razie potrzeby:

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

Pliki skryptów obsługi mogą być wywoływane zarówno ze skryptów w tekście, jak i z plików skryptów podstawowych. Pliki skryptów obsługi nie mają żadnych ograniczeń dotyczących rozszerzenia pliku.

Pliki obsługi są kopiowane do pliku `azscripts/azscriptinput` w środowisku uruchomieniowym. Użyj ścieżki względnej, aby odwoływać się do plików obsługi ze skryptów w tekście i podstawowych plików skryptów.

## <a name="work-with-outputs-from-powershell-script"></a>Praca z wyjściami ze skryptu programu PowerShell

Poniższy szablon przedstawia sposób przekazania wartości między dwoma `deploymentScripts` zasobami:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

W pierwszym zasobie należy zdefiniować zmienną o nazwie i użyć `$DeploymentScriptOutputs` jej do przechowywania wartości wyjściowych. Aby uzyskać dostęp do wartości wyjściowej z innego zasobu w szablonie, użyj:

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>Praca z wyjściami ze skryptu interfejsu wiersza polecenia

Inaczej niż w skrypcie wdrażania programu PowerShell obsługa interfejsu wiersza polecenia/powłoki bash nie uwidacznia wspólnej zmiennej do przechowywania danych wyjściowych skryptu. Zamiast tego istnieje zmienna środowiskowa o nazwie , która przechowuje lokalizację, w której znajduje się plik wyjściowy `AZ_SCRIPTS_OUTPUT_PATH` skryptu. Jeśli skrypt wdrażania jest uruchamiany z szablonu Resource Manager, ta zmienna środowiskowa jest ustawiana automatycznie przez powłokę Bash. Wartość to `AZ_SCRIPTS_OUTPUT_PATH` */mnt/azscripts/azscriptoutput/scriptoutputs.jsna*.

Dane wyjściowe skryptu wdrażania muszą być zapisane w lokalizacji, a dane wyjściowe muszą być prawidłowym obiektem `AZ_SCRIPTS_OUTPUT_PATH` ciągu JSON. Zawartość pliku musi być zapisana jako para klucz-wartość. Na przykład tablica ciągów jest przechowywana jako `{ "MyResult": [ "foo", "bar"] }` .  Przechowywanie tylko wyników tablicy, na przykład `[ "foo", "bar" ]` , jest nieprawidłowe.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

W poprzednim przykładzie jest używane [jq.](https://stedolan.github.io/jq/) Zawiera ona obrazy kontenerów. Zobacz [Konfigurowanie środowiska projektowego.](#configure-development-environment)

## <a name="use-existing-storage-account"></a>Korzystanie z istniejącego konta magazynu

Konto magazynu i wystąpienie kontenera są potrzebne do wykonywania skryptu i rozwiązywania problemów. Dostępne są opcje określania istniejącego konta magazynu. W przeciwnym razie konto magazynu wraz z wystąpieniem kontenera są automatycznie tworzone przez usługę skryptu. Wymagania dotyczące korzystania z istniejącego konta magazynu:

- Obsługiwane rodzaje kont magazynu to:

    | SKU             | Obsługiwany rodzaj     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Storage, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Storage, StorageV2 |
    | Standard_RAGRS  | Storage, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Te kombinacje obsługują udziały plików. Aby uzyskać więcej informacji, zobacz Create an Azure file share (Tworzenie udziału [plików platformy Azure)](../../storage/files/storage-how-to-create-file-share.md) [i Types of storage accounts (Typy kont magazynu).](../../storage/common/storage-account-overview.md)

- Reguły zapory konta magazynu nie są jeszcze obsługiwane. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../../storage/common/storage-network-security.md).
- Podmiot zabezpieczeń wdrożenia musi mieć uprawnienia do zarządzania kontem magazynu, w tym do odczytu, tworzenia i usuwania udziałów plików.

Aby określić istniejące konto magazynu, dodaj następujący kod JSON do elementu właściwości `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: określ nazwę konta magazynu.
- `storageAccountKey`: określ jeden z kluczy konta magazynu. Aby pobrać klucz, możesz użyć funkcji [listKeys().](./template-functions-resource.md#listkeys) Na przykład:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Zobacz [przykładowe szablony,](#sample-templates) aby uzyskać kompletny `Microsoft.Resources/deploymentScripts` przykład definicji.

W przypadku korzystania z istniejącego konta magazynu usługa skryptów tworzy udział plików o unikatowej nazwie. Zobacz [Oczyszczanie zasobów skryptu wdrażania,](#clean-up-deployment-script-resources) aby dowiedzieć się, jak usługa skryptów czyści udział plików.

## <a name="develop-deployment-scripts"></a>Tworzenie skryptów wdrażania

### <a name="handle-non-terminating-errors"></a>Obsługa błędów niepowiązyujących się z zakończeniem

Możesz kontrolować, jak program PowerShell reaguje na błędy niepowiązywające się z błędami, używając zmiennej `$ErrorActionPreference` w skrypcie wdrażania. Jeśli zmienna nie jest ustawiona w skrypcie wdrażania, usługa skryptów używa wartości domyślnej **Kontynuuj.**

Usługa skryptów ustawia stan inicjowania obsługi zasobów na **Niepowodzenie,** gdy skrypt napotka błąd pomimo ustawienia `$ErrorActionPreference` .

### <a name="use-environment-variables"></a>Używanie zmiennych środowiskowych

Skrypt wdrażania używa tych zmiennych środowiskowych:

|Zmienna środowiskowa|Wartość domyślna|Zarezerwowane przez system|
|--------------------|-------------|---------------|
|AZ_SCRIPTS_AZURE_ENVIRONMENT|AzureCloud|N|
|AZ_SCRIPTS_CLEANUP_PREFERENCE|OnExpiration|N|
|AZ_SCRIPTS_OUTPUT_PATH|<AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY>/<AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME>|Y|
|AZ_SCRIPTS_PATH_INPUT_DIRECTORY|/mnt/azscripts/azscriptinput|Y|
|AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY|/mnt/azscripts/azscriptoutput|Y|
|AZ_SCRIPTS_PATH_USER_SCRIPT_FILE_NAME|Azure PowerShell: userscript.ps1; Interfejs wiersza polecenia platformy Azure: userscript.sh|Y|
|AZ_SCRIPTS_PATH_PRIMARY_SCRIPT_URI_FILE_NAME|primaryscripturi.config|Y|
|AZ_SCRIPTS_PATH_SUPPORTING_SCRIPT_URI_FILE_NAME|supportingscripturi.config|Y|
|AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME|scriptoutputs.jswł.|Y|
|AZ_SCRIPTS_PATH_EXECUTION_RESULTS_FILE_NAME|executionresult.jswł.|Y|
|AZ_SCRIPTS_USER_ASSIGNED_IDENTITY|/subscriptions/|N|

Aby uzyskać więcej informacji na temat korzystania `AZ_SCRIPTS_OUTPUT_PATH` z usługi , zobacz Work with [outputs from CLI script (Praca z wyjściami ze skryptu interfejsu wiersza polecenia).](#work-with-outputs-from-cli-script)

### <a name="pass-secured-strings-to-deployment-script"></a>Przekaż zabezpieczone ciągi do skryptu wdrażania

Ustawienie zmiennych środowiskowych (EnvironmentVariable) w wystąpieniach kontenera umożliwia zapewnienie dynamicznej konfiguracji aplikacji lub skryptu uruchamianego przez kontener. Skrypt wdrażania obsługuje niezabędne i zabezpieczone zmienne środowiskowe w taki sam sposób jak wystąpienie kontenera platformy Azure. Aby uzyskać więcej informacji, zobacz Set environment variables in container instances (Ustawianie [zmiennych środowiskowych w wystąpieniach kontenerów).](../../container-instances/container-instances-environment-variables.md#secure-values) Aby uzyskać przykład, zobacz [Przykładowe szablony](#sample-templates).

Maksymalny dozwolony rozmiar zmiennych środowiskowych wynosi 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Monitorowanie i rozwiązywanie problemów ze skryptami wdrażania

Usługa skryptu tworzy konto [magazynu (chyba](../../storage/common/storage-account-overview.md) że określisz istniejące konto magazynu) i wystąpienie [kontenera do](../../container-instances/container-instances-overview.md) wykonania skryptu. Jeśli te zasoby są tworzone automatycznie przez usługę skryptów, oba zasoby mają `azscripts` sufiks w nazwach zasobów.

![Resource Manager zasobów skryptu wdrażania szablonu](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Skrypt użytkownika, wyniki wykonywania i plik stdout są przechowywane w udziałach plików konta magazynu. Istnieje folder o nazwie `azscripts` . W folderze znajdują się dwa kolejne foldery dla plików wejściowych i wyjściowych: `azscriptinput` i `azscriptoutput` .

Folder wyjściowy zawiera plik _executionresult.jsi_ plik wyjściowy skryptu. Komunikat o błędzie wykonywania skryptu jest wyświetlany w _skrypcieexecutionresult.jsna stronie_. Plik wyjściowy jest tworzony tylko wtedy, gdy skrypt zostanie wykonany pomyślnie. Folder input zawiera systemowy plik skryptu programu PowerShell i pliki skryptów wdrażania użytkownika. Można zastąpić poprawiony plik skryptu wdrażania użytkownika i ponownie uruchomić skrypt wdrożenia z wystąpienia kontenera platformy Azure.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Po wdrożeniu zasobu skryptu wdrażania jest on wymieniony w grupie zasobów w Azure Portal. Poniższy zrzut ekranu przedstawia **stronę Przegląd** zasobu skryptu wdrażania:

![Resource Manager portalu skryptów wdrażania szablonu](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

Na stronie przeglądu są wyświetlane pewne ważne informacje dotyczące zasobu, takie jak stan aprowowania, konto **magazynu,** wystąpienie **kontenera** i **dzienniki**.

W menu po lewej stronie można wyświetlić zawartość skryptu wdrażania, argumenty przekazane do skryptu i dane wyjściowe. Można również wyeksportować szablon dla skryptu wdrażania, w tym skrypt wdrożenia.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Za Azure PowerShell można zarządzać skryptami wdrażania w zakresie subskrypcji lub grupy zasobów:

- [Get-AzDeploymentScript:](/powershell/module/az.resources/get-azdeploymentscript)pobiera lub wyświetla skrypty wdrażania.
- [Get-AzDeploymentScriptLog:](/powershell/module/az.resources/get-azdeploymentscriptlog)pobiera dziennik wykonywania skryptu wdrożenia.
- [Remove-AzDeploymentScript:](/powershell/module/az.resources/remove-azdeploymentscript)usuwa skrypt wdrażania i skojarzone z nim zasoby.
- [Save-AzDeploymentScriptLog:](/powershell/module/az.resources/save-azdeploymentscriptlog)zapisuje dziennik wykonywania skryptu wdrożenia na dysku.

Dane `Get-AzDeploymentScript` wyjściowe są podobne do:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure można zarządzać skryptami wdrażania w zakresie subskrypcji lub grupy zasobów:

- [az deployment-scripts delete](/cli/azure/deployment-scripts#az_deployment_scripts_delete): Usuń skrypt wdrażania.
- [az deployment-scripts list:](/cli/azure/deployment-scripts#az_deployment_scripts_list)Lista wszystkich skryptów wdrażania.
- [az deployment-scripts show](/cli/azure/deployment-scripts#az_deployment_scripts_show): Pobierz skrypt wdrożenia.
- [az deployment-scripts show-log:](/cli/azure/deployment-scripts#az_deployment_scripts_show_log)Pokaż dzienniki skryptów wdrażania.

Dane wyjściowe polecenia listy są podobne do:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>Korzystanie z interfejsu API REST

Informacje o wdrożeniu zasobów skryptu wdrożenia można uzyskać na poziomie grupy zasobów i subskrypcji przy użyciu interfejsu API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

W poniższym przykładzie użyto [usługi ARMClient:](https://github.com/projectkudu/ARMClient)

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

Dane wyjściowe są podobne do następujących:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

Następujący interfejs API REST zwraca dziennik:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

Działa tylko przed usunięciem zasobów skryptu wdrożenia.

Aby wyświetlić zasób deploymentScripts w portalu, wybierz pozycję **Pokaż ukryte typy:**

![Resource Manager wdrażania szablonu, pokazywanie ukrytych typów, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Oczyszczanie zasobów skryptu wdrażania

Konto magazynu i wystąpienie kontenera są potrzebne do wykonywania skryptów i rozwiązywania problemów. Dostępne są opcje określania istniejącego konta magazynu. W przeciwnym razie konto magazynu wraz z wystąpieniem kontenera są automatycznie tworzone przez usługę skryptu. Dwa automatycznie utworzone zasoby są usuwane przez usługę skryptów, gdy wykonywanie skryptu wdrażania jest w stanie terminalu. Za zasoby są naliczane faktury do momentu usunięcia zasobów. Aby uzyskać informacje o cenach, zobacz [cennik Container Instances i](https://azure.microsoft.com/pricing/details/container-instances/) Cennik usługi Azure [Storage.](https://azure.microsoft.com/pricing/details/storage/)

Cykl życiowy tych zasobów jest kontrolowany przez następujące właściwości w szablonie:

- `cleanupPreference`: wyczyść preferencje, gdy wykonywanie skryptu jest w stanie terminalu. Obsługiwane wartości to:

  - **Zawsze:** usuń automatycznie utworzone zasoby, gdy wykonywanie skryptu stanie się w stanie terminalu. Jeśli używane jest istniejące konto magazynu, usługa skryptów usuwa udział plików utworzony na koncie magazynu. Ponieważ zasób może nadal być obecny po wyczyszczeniu zasobów, usługa skryptów utrwala wyniki wykonywania skryptu, na przykład stdout, dane wyjściowe i wartość zwracaną przed usunięciem `deploymentScripts` zasobów.
  - **OnSuccess:** Usuń automatycznie utworzone zasoby tylko wtedy, gdy wykonanie skryptu powiedzie się. Jeśli jest używane istniejące konto magazynu, usługa skryptów usuwa udział plików tylko wtedy, gdy wykonanie skryptu powiedzie się. Nadal możesz uzyskać dostęp do zasobów, aby znaleźć informacje debugowania.
  - **OnExpiration**: usuń automatycznie utworzone zasoby tylko wtedy, gdy `retentionInterval` ustawienie wygasło. Jeśli jest używane istniejące konto magazynu, usługa skryptów usuwa udział plików, ale zachowuje konto magazynu.

- `retentionInterval`: określ przedział czasu, w którym zasób skryptu będzie zachowywany, a następnie będzie wygasły i usunięty.

> [!NOTE]
> Nie zaleca się używania konta magazynu i wystąpienia kontenera, które są generowane przez usługę skryptów do innych celów. Te dwa zasoby mogą zostać usunięte w zależności od cyklu życia skryptu.

Wystąpienie kontenera i konto magazynu są usuwane zgodnie z `cleanupPreference` . Jeśli jednak skrypt zakończy się niepowodzeniem i nie zostanie ustawiony na wartość Always , proces wdrażania automatycznie utrzymuje działanie kontenera `cleanupPreference` przez jedną godzinę.  Ta godzina umożliwia rozwiązywanie problemów ze skryptem. Jeśli chcesz zachować działanie kontenera po pomyślnych wdrożeniach, dodaj krok uśpienia do skryptu. Na przykład dodaj [pozycję Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) na końcu skryptu. Jeśli nie dodasz kroku uśpienia, kontener zostanie ustawiony na stan terminalu i nie będzie można uzyskać do niego dostępu, nawet jeśli nie został jeszcze usunięty.

## <a name="run-script-more-than-once"></a>Uruchamianie skryptu więcej niż raz

Wykonywanie skryptu wdrażania jest operacją idempotentną. Jeśli żadna z właściwości zasobu (w tym skrypt w tekście) nie zostanie zmieniona, skrypt nie zostanie wykonany podczas `deploymentScripts` ponownego wykonywania szablonu. Usługa skryptu wdrożenia porównuje nazwy zasobów w szablonie z istniejącymi zasobami w tej samej grupie zasobów. Istnieją dwie opcje, jeśli chcesz wykonać ten sam skrypt wdrażania wielokrotnie:

- Zmień nazwę `deploymentScripts` zasobu. Na przykład użyj funkcji [szablonu utcNow](./template-functions-date.md#utcnow) jako nazwy zasobu lub jako części nazwy zasobu. Zmiana nazwy zasobu powoduje utworzenie nowego `deploymentScripts` zasobu. Jest to dobre zachowanie historii wykonywania skryptu.

    > [!NOTE]
    > Funkcji `utcNow` można używać tylko w wartości domyślnej parametru.

- Określ inną wartość we `forceUpdateTag` właściwości szablonu. Na przykład użyj `utcNow` wartości .

> [!NOTE]
> Napisz skrypty wdrażania, które są idempotentne. Dzięki temu, jeśli zostaną one uruchomione ponownie przypadkowo, nie spowoduje to zmian systemu. Jeśli na przykład skrypt wdrażania jest używany do tworzenia zasobu platformy Azure, przed utworzeniem zasobu sprawdź, czy zasób nie istnieje, aby skrypt zakończył się powodzeniem lub nie utworzysz zasobu ponownie.

## <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

Jako środowiska dewelopera skryptu wdrażania można użyć wstępnie skonfigurowanego obrazu kontenera. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska projektowego dla skryptów wdrażania w szablonach](./deployment-script-template-configure-dev.md).

Po pomyślnym przetestowaniu skryptu można go użyć jako skryptu wdrażania w szablonach.

## <a name="deployment-script-error-codes"></a>Kody błędów skryptu wdrażania

| Kod błędu | Opis |
|------------|-------------|
| DeploymentScriptInvalidOperation | Definicja zasobu skryptu wdrażania w szablonie zawiera nieprawidłowe nazwy właściwości. |
| DeploymentScriptResourceConflict | Nie można usunąć zasobu skryptu wdrożenia, który jest w stanie innym niż terminal, a wykonanie nie przekroczyło 1 godziny. Nie można też ponownie uruchomić tego samego skryptu wdrażania z tym samym identyfikatorem zasobu (ta sama subskrypcja, nazwa grupy zasobów i nazwa zasobu), ale w tym samym czasie innej zawartości treści skryptu. |
| DeploymentScriptOperationFailed | Wewnętrzna operacja skryptu wdrażania nie powiodła się. Skontaktuj się z pomocą techniczną firmy Microsoft. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | Klucz dostępu nie został określony dla istniejącego konta magazynu.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Grupa kontenerów utworzona przez usługę skryptu wdrożenia została zewnętrznie zmodyfikowana i dodano nieprawidłowe kontenery. |
| DeploymentScriptContainerGroupInNonterminalState | Co najmniej dwa zasoby skryptu wdrażania używają tej samej nazwy wystąpienia kontenera platformy Azure w tej samej grupie zasobów, a jeden z nich nie zakończył jeszcze wykonywania. |
| DeploymentScriptStorageAccountInvalidKind | Istniejące konto magazynu typu BlobBlobStorage lub BlobStorage nie obsługuje udziałów plików i nie może być używane. |
| DeploymentScriptStorageAccountInvalidKindAndSku | Istniejące konto magazynu nie obsługuje udziałów plików. Aby uzyskać listę obsługiwanych rodzajów kont magazynu, zobacz [Używanie istniejącego konta magazynu.](#use-existing-storage-account) |
| DeploymentScriptStorageAccountNotFound | Konto magazynu nie istnieje lub zostało usunięte przez zewnętrzny proces lub narzędzie. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | Określone konto magazynu ma punkt końcowy usługi. Konto magazynu z punktem końcowym usługi nie jest obsługiwane. |
| DeploymentScriptStorageAccountInvalidAccessKey | Określono nieprawidłowy klucz dostępu dla istniejącego konta magazynu. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Nieprawidłowy format klucza konta magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu.](../../storage/common/storage-account-keys-manage.md) |
| DeploymentScriptExceededMaxAllowedTime | Czas wykonywania skryptu wdrażania przekroczył wartość limitu czasu określoną w definicji zasobu skryptu wdrażania. |
| DeploymentScriptInvalidOutputs | Dane wyjściowe skryptu wdrażania nie są prawidłowym obiektem JSON. |
| DeploymentScriptContainerInstancesServiceLoginFailure | Tożsamość zarządzana przypisana przez użytkownika nie mogła zalogować się po 10 próbach z 1-minutowym interwałem. |
| DeploymentScriptContainerGroupNotFound | Grupa kontenerów utworzona przez usługę skryptu wdrożenia została usunięta przez zewnętrzne narzędzie lub proces. |
| DeploymentScriptDownloadFailure | Nie można pobrać skryptu obsługi. Zobacz [Używanie skryptu obsługi](#use-supporting-scripts).|
| DeploymentScriptError | Skrypt użytkownika zrzucił błąd. |
| DeploymentScriptBootstrapScriptExecutionFailed | Skrypt uruchamiania zrzucił błąd. Skrypt uruchamiania to skrypt systemowy, który orkiestruje wykonywanie skryptu wdrażania. |
| DeploymentScriptExecutionFailed | Nieznany błąd podczas wykonywania skryptu wdrożenia. |
| DeploymentScriptContainerInstancesServiceUnavailable | Podczas tworzenia wystąpienia kontenera platformy Azure (ACI) usługa ACI zrzuciła błąd niedostępności usługi. |
| DeploymentScriptContainerGroupInNonterminalState | Podczas tworzenia wystąpienia kontenera platformy Azure (ACI) inny skrypt wdrażania używa tej samej nazwy usługi ACI w tym samym zakresie (ta sama subskrypcja, nazwa grupy zasobów i nazwa zasobu). |
| DeploymentScriptContainerGroupNameInvalid | Określona nazwa wystąpienia kontenera platformy Azure (ACI) nie spełnia wymagań usługi ACI. Zobacz [Rozwiązywanie typowych problemów w programie Azure Container Instances](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób używania skryptów wdrażania. Aby przejść przez samouczek skryptu wdrażania:

> [!div class="nextstepaction"]
> [Samouczek: używanie skryptów wdrażania w Azure Resource Manager szablonów](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Moduł Learn: Rozszerzanie szablonów usługi ARM przy użyciu skryptów wdrażania](/learn/modules/extend-resource-manager-template-deployment-scripts/)
