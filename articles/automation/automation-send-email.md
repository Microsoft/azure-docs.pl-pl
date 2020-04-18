---
title: Wysyłanie wiadomości e-mail z podręcznika azure automation
description: Dowiedz się, jak używać sendgrid do wysyłania wiadomości e-mail z poziomu życiówek.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: d4b35458c76da82b33dfcb530cfdc71ee3da3bb6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604781"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Samouczek: Wysyłanie wiadomości e-mail z uruchomieniu usługi Azure Automation

Możesz wysłać wiadomość e-mail z życiówek z [SendGrid](https://sendgrid.com/solutions) przy użyciu programu PowerShell. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz usługę Azure Key Vault.
> * Przechowuj `SendGrid` klucz interfejsu API w magazynie kluczy.
> * Utwórz element runbook wielokrotnego użycia, który pobiera klucz interfejsu API i wysyła wiadomość e-mail przy użyciu klucza interfejsu API przechowywanego w [usłudze Azure Key Vault](/azure/key-vault/).

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są wymagane następujące elementy:

* Subskrypcja platformy Azure: jeśli jeszcze jej nie masz, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub założyć [bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Utwórz konto SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Konto automatyzacji](automation-offering-get-started.md) z modułami **Az** i [Uruchom jako połączenie](automation-create-runas-account.md), aby przechowywać i wykonywać runbook.

## <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

Usługę Azure Key Vault można utworzyć przy użyciu następującego skryptu programu PowerShell. Zastąp wartości zmiennych wartościami specyficznymi dla danego środowiska. Użyj osadzonej usługi Azure Cloud Shell za pomocą przycisku **Wypróbuj,** znajdującego się w prawym górnym rogu bloku kodu. Można również skopiować i uruchomić kod lokalnie, jeśli masz [moduł programu Azure PowerShell](/powershell/azure/install-az-ps) zainstalowany na komputerze lokalnym.

> [!NOTE]
> Aby pobrać klucz interfejsu API, należy wykonać czynności opisane w funkcji [Znajdź klucz interfejsu API SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

Aby uzyskać inne sposoby tworzenia usługi Azure Key Vault i przechowywania klucza tajnego, zobacz [Szybki start usługi Key Vault](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importowanie wymaganych modułów do konta automatyzacji

Aby korzystać z usługi Azure Key Vault w ramach systemu runbook, twoje konto automatyzacji wymaga następujących modułów:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Kliknij **pozycję Wdrażanie w usłudze Azure Automation** na karcie Automatyzacja platformy Azure w obszarze Opcje **instalacji**. Ta akcja otwiera witrynę Azure portal. Na stronie Importowanie wybierz konto automatyzacji i kliknij przycisk **OK**.

Aby uzyskać dodatkowe metody dodawania wymaganych modułów, zobacz [Importowanie modułów](/azure/automation/shared-resources/modules#importing-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Tworzenie egobooka w celu wysłania wiadomości e-mail

Po utworzeniu magazynu kluczy `SendGrid` i zapisaniu klucza interfejsu API nadszedł czas, aby utworzyć element runbook, który pobiera klucz interfejsu API i wysyła wiadomość e-mail.

Ten element runbook używa `AzureRunAsConnection` jako uruchom jako [konto](automation-create-runas-account.md) do uwierzytelniania za pomocą platformy Azure, aby pobrać klucz tajny z usługi Azure Key Vault.

W tym przykładzie można utworzyć projekt runbook o nazwie **Send-GridMailMessage**. Można zmodyfikować skrypt programu PowerShell i użyć go ponownie w różnych scenariuszach.

1. Przejdź do swojego konta usługi Azure Automation.
2. W obszarze **Automatyzacja procesów**wybierz pozycję **Runbooks**.
3. U góry listy śmięty wybierz **pozycję + Utwórz program runbook**.
4. Na stronie **Dodawanie ekwikro** wprowadź pozycję **Wyślij wiadomość e-mail dla** nazwy podręcznika. Dla typu eks-u wybierz pozycję **PowerShell**. Następnie wybierz przycisk **Create** (Utwórz).
   ![Tworzenie eks-u](./media/automation-send-email/automation-send-email-runbook.png)
5. Element runbook zostanie utworzony i zostanie otworzona strona **Edytuj element runbook programu PowerShell**.
   ![Edytowanie eksmisji](./media/automation-send-email/automation-send-email-edit.png)
6. Skopiuj następujący przykład programu PowerShell na stronę **Edycji.** Upewnij się, że `$VaultName` jest to nazwa określona podczas tworzenia magazynu kluczy.

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
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
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

7. Wybierz **pozycję Publikuj,** aby zapisać i opublikować projekt runbook.

Aby sprawdzić, czy projekt runbook jest wykonywany pomyślnie, można wykonać kroki w obszarze [Testowanie żyła lub](manage-runbooks.md#testing-a-runbook) [Uruchamianie żyła .](start-runbooks.md)
Jeśli początkowo nie widzisz testowej poczty e-mail, sprawdź foldery **Wiadomości-śmieci** i **Spam.**

## <a name="clean-up"></a>Czyszczenie

Gdy element runbook nie będzie już potrzebny, usuń go. Aby to zrobić, zaznacz element runbook na liście i kliknij pozycję **Usuń**.

Usuń magazyn kluczy za pomocą polecenia cmdlet [Remove-AzKeyVault.](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0)

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać problemy z tworzeniem lub uruchamianiem wiązki uruchomieniu, zobacz [Rozwiązywanie problemów z błędami w systemach runbook](./troubleshoot/runbooks.md).
* Aby zaktualizować moduły na koncie automatyzacji, zobacz [Jak zaktualizować moduły usługi Azure PowerShell w usłudze Azure Automation].](automation-update-azure-modules.md)
* Aby monitorować wykonywanie uruchomieniu, zobacz [Przekazywanie stanu zadania i strumieni zadań z automatyzacji do dzienników usługi Azure Monitor.](automation-manage-send-joblogs-log-analytics.md)
* Aby wyzwolić element runbook przy użyciu alertu, zobacz [Wyzwalanie uruchomieniu systemu Azure Automation za pomocą alertu.](automation-create-alert-triggered-runbook.md)
