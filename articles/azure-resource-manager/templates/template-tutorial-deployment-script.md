---
title: Używanie skryptów wdrażania szablonów | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać skryptów wdrażania w szablonach usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/07/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f369eb54dc92a29ba122a8a645262dc085b1ed36
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "80930047"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Samouczek: Tworzenie certyfikatu z podpisem własnym za pomocą skryptów wdrażania (wersja zapoznawcza)

Dowiedz się, jak używać skryptów wdrażania w szablonach zarządzania zasobami platformy Azure (ARM). Skrypty wdrażania mogą służyć do wykonywania niestandardowych kroków, których nie można wykonać za pomocą szablonów ARM. Na przykład tworzenie certyfikatu z podpisem własnym.  W tym samouczku utworzysz szablon, aby wdrożyć `Microsoft.Resources/deploymentScripts` magazyn kluczy platformy Azure, a następnie użyj zasobu w tym samym szablonie, aby utworzyć certyfikat, a następnie dodać certyfikat do magazynu kluczy. Aby dowiedzieć się więcej o skrypcie wdrażania, zobacz [Używanie skryptów wdrażania w szablonach ARM](./deployment-script-template.md).

> [!IMPORTANT]
> Dwa zasoby skryptu wdrażania, konto magazynu i wystąpienie kontenera, są tworzone w tej samej grupie zasobów do wykonywania skryptów i rozwiązywania problemów. Te zasoby są zwykle usuwane przez usługę skryptu, gdy wykonanie skryptu zostanie w stanie terminala. Naliczane są naliczane za zasoby, dopóki zasoby nie zostaną usunięte. Aby dowiedzieć się więcej, zobacz [Czyszczenie zasobów skryptu wdrażania](./deployment-script-template.md#clean-up-deployment-script-resources).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Edytowanie szablonu
> * Wdrożenie szablonu
> * Debugowanie skryptu, który nie powiódł się
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* ** [Kod programu Visual Studio](https://code.visualstudio.com/) z rozszerzeniem Narzędzia Menedżera zasobów**. Zobacz [Tworzenie szablonów ARM za pomocą programu Visual Studio](./use-vs-code-to-create-template.md).

* **Tożsamość zarządzana przypisana przez użytkownika z rolą współautora na poziomie subskrypcji**. Ta tożsamość jest używana do wykonywania skryptów wdrażania. Aby go utworzyć, zobacz [Tożsamość zarządzana przypisana przez użytkownika](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity). Identyfikator tożsamości jest potrzebny podczas wdrażania szablonu. Format tożsamości jest:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Użyj następującego skryptu interfejsu wiersza polecenia, aby uzyskać identyfikator, podając nazwę grupy zasobów i nazwę tożsamości.

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  az identity list -g $resourceGroupName
  ```

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Zamiast tworzyć szablon od podstaw, otwórz szablon z obszaru [Azure Quickstart Templates (Szablony szybkiego startu platformy Azure)](https://azure.microsoft.com/resources/templates/). Szablony szybki start platformy Azure to repozytorium szablonów ARM.

Szablon używany w tym przewodniku Szybki start nosi nazwę [Tworzenie usługi Azure Key Vault i klucz tajny.](https://azure.microsoft.com/resources/templates/101-key-vault-create/) Szablon tworzy magazyn kluczy, a następnie dodaje klucz tajny do magazynu kluczy.

1. W programie Visual Studio Code wybierz pozycję **Plik**>**otwórz plik**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz **opcję Zapisz plik,**>**Save As** aby zapisać plik jako **azuredeploy.json** na komputerze lokalnym.

## <a name="edit-the-template"></a>Edytowanie szablonu

Wykonuj następujące zmiany w szablonie:

### <a name="clean-up-the-template-optional"></a>Czyszczenie szablonu (opcjonalnie)

Oryginalny szablon dodaje klucz tajny do magazynu kluczy.  Aby uprościć samouczek, usuń następujący zasób:

* **Microsoft.KeyVault/vaults/secrets**

Usuń następujące dwie definicje parametrów:

* **nazwa secretName**
* **wartość tajna**

Jeśli nie chcesz usuwać tych definicji, należy określić wartości parametrów podczas wdrażania.

### <a name="configure-the-key-vault-access-policies"></a>Konfigurowanie zasad dostępu do magazynu kluczy

Skrypt wdrażania dodaje certyfikat do magazynu kluczy. Skonfiguruj zasady dostępu do magazynu kluczy, aby nadać uprawnienia do tożsamości zarządzanej:

1. Dodaj parametr, aby uzyskać identyfikator tożsamości zarządzanej:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Rozszerzenie szablonu Menedżera zasobów programu Visual Studio Code nie jest jeszcze w stanie sformatować skryptów wdrażania. Nie używaj [SHIFT]+[ALT]+F do formatowania zasobów deploymentScripts, takich jak następujące.

1. Dodaj parametr do konfigurowania zasad dostępu do magazynu kluczy, aby tożsamość zarządzana mogła dodawać certyfikaty do magazynu kluczy.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Zaktualizuj istniejące zasady dostępu do magazynu kluczy do:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Istnieją dwie zasady zdefiniowane, jeden dla zalogowany użytkownik, a drugi jest dla tożsamości zarządzanej.  Zalogowany użytkownik potrzebuje tylko uprawnienia *listy,* aby zweryfikować wdrożenie.  Aby uprościć samouczek, ten sam certyfikat jest przypisany zarówno do tożsamości zarządzanej, jak i do zalogowanych użytkowników.

### <a name="add-the-deployment-script"></a>Dodawanie skryptu wdrażania

1. Dodaj trzy parametry, które są używane przez skrypt wdrażania.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Dodawanie zasobu w językach deploymentScripts:

    > [!NOTE]
    > Ponieważ skrypty wdrażania wbudowanego są ujęte w cudzysłowy podwójne, ciągi wewnątrz skryptów wdrażania muszą być ujęte w pojedyncze cudzysłowy. Znak ucieczki programu PowerShell jest **&#92;**.

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    Zasób `deploymentScripts` zależy od zasobu magazynu kluczy i zasobu przydziału roli.  Ma następujące właściwości:

    * **tożsamość:** Skrypt wdrażania używa tożsamości zarządzanej przypisanej przez użytkownika do wykonania skryptów.
    * **rodzaj**: Określ typ skryptu. Obecnie obsługuje tylko skrypt programu PowerShell.
    * **forceUpdateTag**: Określ, czy skrypt wdrażania ma być wykonywany, nawet jeśli źródło skryptu nie uległo zmianie. Może to być bieżąca sygnatura czasowa lub identyfikator GUID. Aby dowiedzieć się więcej, zobacz [Uruchamianie skryptu więcej niż raz](./deployment-script-template.md#run-script-more-than-once).
    * **azPowerShellVersion**: Określa wersję modułu programu Azure PowerShell, która ma być używana. Obecnie skrypt wdrażania obsługuje wersje 2.7.0, 2.8.0 i 3.0.0.
    * **limit czasu:** Określ maksymalny dozwolony czas wykonywania skryptu określony w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Wartością domyślną jest **P1D**.
    * **argumenty**: Określ wartości parametrów. Wartości są oddzielone spacjami.
    * **scriptContent**: Określ zawartość skryptu. Aby uruchomić skrypt zewnętrzny, należy użyć **podstawowego identyfikatoraScriptURI.** Aby uzyskać więcej informacji, zobacz [Używanie skryptu zewnętrznego](./deployment-script-template.md#use-external-scripts).
        Deklarowanie **$DeploymentScriptOutputs** jest wymagane tylko podczas testowania skryptu na komputerze lokalnym. Deklarowanie zmiennej umożliwia uruchamianie skryptu na komputerze lokalnym i w zasobie deploymentScript bez konieczności wprowadzania zmian. Wartość przypisana do $DeploymentScriptOutputs jest dostępna jako dane wyjściowe we wdrożeniach. Aby uzyskać więcej informacji, zobacz [Praca z wyjściami ze skryptów wdrażania programu PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) lub [Praca z wyjściami ze skryptów wdrażania interfejsu wiersza polecenia](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * **cleanupPreference**: Określ preferencję, kiedy usunąć zasoby skryptu wdrażania.  Wartością domyślną jest **Zawsze**, co oznacza, że zasoby skryptu wdrażania są usuwane pomimo stanu terminala (Powodem, Niepowodzenie, Anulowano). W tym **samouczku OnSuccess** jest używany, dzięki czemu masz szansę wyświetlić wyniki wykonywania skryptu.
    * **retentionInterval**: Określ interwał, dla którego usługa zachowuje zasoby skryptu po osiągnięciu stanu terminala. Zasoby zostaną usunięte po upływie tego czasu. Czas trwania jest oparty na wzorze ISO 8601. Ten samouczek używa P1D, co oznacza jeden dzień.  Ta właściwość jest używana, gdy **cleanupPreference** jest ustawiona **na OnExpiration**. Ta właściwość nie jest obecnie włączona.

    Skrypt wdrażania przyjmuje trzy parametry: nazwę magazynu kluczy, nazwę certyfikatu i nazwę podmiotu.  Tworzy certyfikat, a następnie dodaje certyfikat do magazynu kluczy.

    **$DeploymentScriptOutputs** służy do przechowywania wartości wyjściowej.  Aby dowiedzieć się więcej, zobacz [Praca z wyjściami ze skryptów wdrażania programu PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) lub [Praca z wyjściami ze skryptów wdrażania interfejsu wiersza polecenia](./deployment-script-template.md#work-with-outputs-from-cli-script).

    Wypełniony szablon można znaleźć [tutaj](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

1. Aby wyświetlić proces debugowania, umieść błąd w kodzie, dodając następujący wiersz do skryptu wdrażania:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    Prawidłowe polecenie to **Write-Output** zamiast **Write-Output1**.

1. Wybierz pozycję**Zapisz** **plik,**>aby zapisać plik.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Zapoznaj się [z sekcją Wdrażanie szablonu](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template) w przewodniku Szybki start programu Visual Studio Kod, aby otworzyć powłokę chmury i przekazać plik szablonu do powłoki. Następnie uruchom następujący skrypt programu PowerShell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

Usługa skryptu wdrażania musi utworzyć dodatkowe zasoby skryptu wdrażania do wykonania skryptu. Przygotowanie i proces oczyszczania może potrwać do jednej minuty, aby zakończyć oprócz rzeczywistego czasu wykonywania skryptu.

Wdrożenie nie powiodło się z powodu nieprawidłowego **polecenia, Write-Output1** jest używany w skrypcie. Zostanie wyświetlony błąd mówiący:

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

Wynik wykonania skryptu wdrażania jest przechowywany w zasobach skryptu wdrażania w celu rozwiązywania problemów.

## <a name="debug-the-failed-script"></a>Debugowanie skryptu, który nie powiódł się

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Otwórz grupę zasobów. Jest to nazwa projektu z **rg** dołączone. Zobaczysz dwa dodatkowe zasoby w grupie zasobów. Zasoby te są określane jako *zasoby skryptu wdrażania*.

    ![Zasoby skryptu wdrażania szablonów Menedżera zasobów](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Oba pliki mają **sufiks azscripts.** Jednym z nich jest konto magazynu, a drugi jest wystąpienie kontenera.

    Wybierz **pozycję Pokaż ukryte typy,** aby wyświetlić listę zasobu deploymentScripts.

1. Wybierz konto magazynu z **sufiksem azscripts.**
1. Wybierz kafelek **Udziały plików.** Zobaczysz folder **azscripts.**  Folder zawiera pliki wykonania skryptu wdrażania.
1. Wybierz **azscripts**. Zobaczysz dwa **folderyL azscriptinput** i **azscriptoutput**.  Folder wejściowy zawiera systemowy plik skryptu programu PowerShell i pliki skryptów wdrażania użytkownika. Folder wyjściowy zawiera **plik executionresult.json** i plik wyjściowy skryptu. Komunikat o błędzie można zobaczyć w **pliku executionresult.json**. Plik wyjściowy nie istnieje, ponieważ wykonanie nie powiodło się.

Usuń wiersz **Write-Output1** i ponownie wdrożyć szablon.

Po pomyślnym uruchomieniu drugiego wdrożenia zasoby skryptu wdrażania zostaną usunięte przez usługę skryptu, ponieważ właściwość **cleanupPreference** jest **ustawiona na OnSuccess**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano, jak używać skryptu wdrażania w szablonach ARM. Aby dowiedzieć się, jak wdrażać zasoby platformy Azure na podstawie warunków, zobacz:

> [!div class="nextstepaction"]
> [Używanie warunków](./template-tutorial-use-conditions.md)
