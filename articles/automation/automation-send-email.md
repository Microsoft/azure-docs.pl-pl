---
title: Wysyłanie wiadomości e-mail z Azure Automation Runbook
description: W tym artykule opisano sposób wysyłania wiadomości e-mail z poziomu runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2650e3a9ce58b611c1aff1a569cc1e8f0980fd4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833494"
---
# <a name="send-an-email-from-a-runbook"></a>Wysyłanie wiadomości e-mail z poziomu elementu runbook

Możesz wysłać wiadomość e-mail z element runbook za pomocą [usługi SendGrid przy](https://sendgrid.com/solutions) użyciu programu PowerShell. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz aktywować korzyści dla subskrybentów [MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Konto SendGrid.](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account)
* [Konto usługi Automation z](./index.yml) **modułami Az.**
* [Konto Uruchom jako](./automation-security-overview.md#run-as-accounts) do przechowywania i wykonywania runbook.

## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

Możesz utworzyć nową Azure Key Vault za pomocą następującego skryptu programu PowerShell. Zastąp wartości zmiennych wartościami specyficznymi dla danego środowiska. Użyj osadzonego Azure Cloud Shell za pomocą **przycisku Wypróbuj** znajdującego się w prawym górnym rogu bloku kodu. Możesz również skopiować i uruchomić kod lokalnie, jeśli masz moduły [Az](/powershell/azure/install-az-ps) zainstalowane na maszynie lokalnej.

> [!NOTE]
> Aby pobrać klucz interfejsu API, skorzystaj z procedury opisanej w tece Znajdowanie klucza interfejsu [API usługi SendGrid.](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key)

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Aby uzyskać inne sposoby tworzenia Azure Key Vault i przechowywania tajnego, zobacz Key Vault [Szybki start.](../key-vault/index.yml)

## <a name="import-required-modules-into-your-automation-account"></a>Importowanie wymaganych modułów na konto usługi Automation

Aby używać Azure Key Vault w ramach runbook, należy zaimportować następujące moduły na konto usługi Automation:

* [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Aby uzyskać instrukcje, zobacz [Import Az modules (Importowanie modułów Az).](shared-resources/modules.md#import-az-modules)

## <a name="create-the-runbook-to-send-an-email"></a>Tworzenie runbook w celu wysłania wiadomości e-mail

Po utworzeniu klucza Key Vault i zapisaniu klucza interfejsu API należy utworzyć element Runbook, który pobiera klucz interfejsu API i `SendGrid` wysyła wiadomość e-mail. Użyjmy runbook, który używa konta Uruchom jako do uwierzytelniania na platformie Azure w celu pobrania tajnego `AzureRunAsConnection` Azure Key Vault. [](./automation-security-overview.md#run-as-accounts) Wywołamy runbook **Send-GridMailMessage**. Możesz zmodyfikować skrypt programu PowerShell używany do celów przykładowych i użyć go ponownie w różnych scenariuszach.

1. Przejdź do swojego Azure Automation konta.
2. W **obszarze Automatyzacja procesów** wybierz pozycję **Runbook.**
3. W górnej części listy elementów Runbook wybierz pozycję **+ Utwórz runbook.**
4. Na stronie Dodawanie runbook wprowadź **wartość Send-GridMailMessage** jako nazwę runbook. Jako typ runbook wybierz program **PowerShell.** Następnie wybierz przycisk **Utwórz**.
   ![Tworzenie runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Element runbook zostanie utworzony i zostanie otworzona strona Edytuj element runbook programu PowerShell.
   ![Edytowanie runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Skopiuj poniższy przykład z programu PowerShell na stronę Edycja. Upewnij `VaultName` się, że wartość określa nazwę wybraną dla Key Vault.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>&quot;
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name &quot;SendGridAPIKey").SecretValue
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Wybierz **pozycję Publikuj,** aby zapisać i opublikować runbook.

Aby sprawdzić, czy runbook jest wykonywany pomyślnie, [](manage-runbooks.md#test-a-runbook) możesz wykonać kroki opisane w teście lub [uruchomieniu.](start-runbooks.md)

Jeśli początkowo nie widzisz testowej wiadomości e-mail, sprawdź **foldery wiadomości-śmieci** **i spamu.**

## <a name="clean-up-resources-after-the-email-operation"></a>Czyszczenie zasobów po operacji poczty e-mail

1. Gdy nie będzie już potrzebny, wybierz go na liście elementów Runbook, a następnie kliknij pozycję **Usuń.**

2. Usuń Key Vault za pomocą polecenia cmdlet [Remove-AzKeyVault.](/powershell/module/az.keyvault/remove-azkeyvault)

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki

* Aby wysłać dane zadania runbook do obszaru roboczego usługi Log Analytics, zobacz Forward Azure Automation job data to Azure Monitor logs (Przekazywanie danych zadania do [Azure Monitor dziennikach).](automation-manage-send-joblogs-log-analytics.md)
* Aby monitorować metryki i dzienniki na poziomie bazowym, zobacz Używanie alertu do wyzwalania Azure Automation [Runbook.](automation-create-alert-triggered-runbook.md)
* Aby rozwiązać problemy pojawiające się podczas operacji na runbookach, zobacz [Rozwiązywanie problemów z runbookami.](./troubleshoot/runbooks.md)
