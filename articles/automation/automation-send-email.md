---
title: Wyślij wiadomość e-mail z elementu Runbook Azure Automation
description: W tym artykule opisano sposób wysyłania wiadomości e-mail z elementu Runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: conceptual
ms.openlocfilehash: a81ae680a5f04eca0a6cc01ee24b474cc5daabea
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005209"
---
# <a name="send-an-email-from-a-runbook"></a>Wysyłanie wiadomości e-mail z poziomu elementu runbook

Możesz wysłać wiadomość e-mail z elementu Runbook za pomocą usługi [SendGrid](https://sendgrid.com/solutions) przy użyciu programu PowerShell. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli jeszcze jej nie masz, możesz  [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account).
* [Konto usługi Automation](./index.yml) za pomocą **AZ** modules.
* [Konto Uruchom jako](./manage-runas-account.md) do przechowywania i wykonywania elementu Runbook.

## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

Azure Key Vault można utworzyć przy użyciu poniższego skryptu programu PowerShell. Zamień wartości zmiennej na wartości specyficzne dla danego środowiska. Użyj osadzonego Azure Cloud Shell za pomocą przycisku **Wypróbuj** , znajdującego się w prawym górnym rogu bloku kodu. Możesz również kopiować i uruchamiać kod lokalnie, jeśli [masz zainstalowane na](/powershell/azure/install-az-ps) komputerze lokalnym.

> [!NOTE]
> Aby pobrać klucz interfejsu API, wykonaj kroki opisane w sekcji [Znajdowanie klucza interfejsu API SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key).

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

Aby zapoznać się z innymi sposobami tworzenia Azure Key Vault i przechowywania wpisu tajnego, zobacz [Key Vault przewodników szybki start](../key-vault/index.yml).

## <a name="import-required-modules-into-your-automation-account"></a>Importowanie wymaganych modułów do konta usługi Automation

Aby użyć Azure Key Vault w elemencie Runbook, należy zaimportować następujące moduły do konta usługi Automation:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Aby uzyskać instrukcje, zobacz [Importowanie AZ modules](shared-resources/modules.md#import-az-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Tworzenie elementu Runbook w celu wysłania wiadomości e-mail

Po utworzeniu Key Vault i zapisaniu `SendGrid` klucza interfejsu API czas tworzenia elementu Runbook, który pobiera klucz interfejsu API i wysyła wiadomość e-mail. Użyjmy elementu Runbook, który jest używany `AzureRunAsConnection` jako [konto Uruchom jako](./manage-runas-account.md) do uwierzytelniania na platformie Azure w celu pobrania klucza tajnego z Azure Key Vault. Wywołamy element Runbook **send-GridMailMessage**. Można modyfikować skrypt programu PowerShell używany do przykładu i ponownie używać go w różnych scenariuszach.

1. Przejdź do swojego konta Azure Automation.
2. W obszarze **Automatyzacja procesów** wybierz pozycję **elementy Runbook**.
3. W górnej części listy elementów Runbook wybierz pozycję **+ Utwórz element Runbook**.
4. Na stronie Dodawanie elementu Runbook wprowadź polecenie **send-GridMailMessage** dla nazwy elementu Runbook. W polu Typ elementu Runbook wybierz pozycję **PowerShell**. Następnie wybierz przycisk **Utwórz**.
   ![Utwórz element Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Element runbook zostanie utworzony i zostanie otworzona strona Edytuj element runbook programu PowerShell.
   ![Edytowanie elementu Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Skopiuj poniższy przykład programu PowerShell do strony Edycja. Upewnij się, że `VaultName` określono nazwę wybraną dla Key Vault.

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
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValue
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

7. Wybierz pozycję **Publikuj** , aby zapisać i opublikować element Runbook.

Aby sprawdzić, czy element Runbook został wykonany pomyślnie, możesz wykonać czynności opisane w sekcji [testowanie elementu Runbook](manage-runbooks.md#test-a-runbook) lub [Uruchamianie elementu Runbook](start-runbooks.md).

Jeśli nie widzisz początkowo testową pocztą e-mail, Sprawdź foldery **wiadomości-śmieci** i **spamu** .

## <a name="clean-up-resources-after-the-email-operation"></a>Wyczyść zasoby po operacji poczty e-mail

1. Gdy element Runbook nie jest już wymagany, wybierz go na liście elementów Runbook, a następnie kliknij przycisk **Usuń**.

2. Usuń Key Vault za pomocą polecenia cmdlet [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0) .

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki

* Aby wysłać dane zadania elementu Runbook do obszaru roboczego Log Analytics, zobacz [przesyłanie dalej Azure Automation danych zadania do dzienników Azure monitor](automation-manage-send-joblogs-log-analytics.md).
* Aby monitorować metryki i dzienniki na poziomie podstawowym, zobacz temat [Używanie alertu do wyzwalania Azure Automation elementu Runbook](automation-create-alert-triggered-runbook.md).
* Aby rozwiązać problemy związane z operacjami elementu Runbook, zobacz [Rozwiązywanie problemów z elementem Runbook](./troubleshoot/runbooks.md).
