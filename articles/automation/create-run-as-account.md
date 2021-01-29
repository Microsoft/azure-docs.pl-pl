---
title: Tworzenie Azure Automation konta Uruchom jako
description: W tym artykule opisano sposób tworzenia konta Uruchom jako w programie PowerShell lub z poziomu Azure Portal.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: d6c8e96c325cfa6422ceb4efc55ef0c5cc149ae3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055897"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Jak utworzyć Azure Automation konto Uruchom jako

Konta Uruchom jako w Azure Automation umożliwiają uwierzytelnianie w celu zarządzania zasobami w ramach Azure Resource Manager lub klasycznego modelu wdrażania platformy Azure przy użyciu elementów Runbook usługi Automation i innych funkcji automatyzacji. W tym artykule opisano sposób tworzenia klasycznego konta Uruchom jako lub z poziomu Azure Portal lub Azure PowerShell.

## <a name="create-account-in-azure-portal"></a>Utwórz konto w Azure Portal

Wykonaj następujące kroki, aby zaktualizować konto Azure Automation w Azure Portal. Konta Uruchom jako i klasyczne są tworzone osobno. Jeśli nie trzeba zarządzać zasobami klasycznymi, można po prostu utworzyć konto Uruchom jako platformy Azure.

1. Zaloguj się do witryny Azure Portal przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.

2. Wyszukaj i wybierz pozycję **konta usługi Automation**.

3. Na stronie **konta usługi Automation** wybierz z listy konto usługi Automation.

4. W okienku po lewej stronie wybierz pozycję **konta Uruchom jako** w sekcji **Ustawienia konta** .

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="Wybierz opcję konta Uruchom jako.":::

5. W zależności od wymaganego konta Użyj **konta Uruchom jako platformy Azure** lub okienka **klasycznego konta Uruchom jako platformy Azure** . Po przejrzeniu informacji przeglądowych kliknij przycisk **Utwórz**.

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="Wybierz opcję tworzenia konta Uruchom jako":::

6. W trakcie tworzenia konta Uruchom jako na platformie Azure postęp można śledzić po wybraniu z menu opcji **Powiadomienia**. Zostanie również wyświetlony transparent informujący o utworzeniu konta. Proces może potrwać kilka minut.

## <a name="create-account-using-powershell"></a>Tworzenie konta przy użyciu programu PowerShell

Poniższa lista zawiera wymagania dotyczące tworzenia konta Uruchom jako w programie PowerShell przy użyciu dostarczonego skryptu. Te wymagania dotyczą obu typów kont Uruchom jako.

* System Windows 10 lub Windows Server 2016 z Azure Resource Manager modules i nowszymi. Skrypt programu PowerShell nie obsługuje wcześniejszych wersji systemu Windows.
* Azure PowerShell program PowerShell 6.2.4 lub nowszy. Aby uzyskać więcej informacji, zobacz [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps).
* Konto usługi Automation, do którego odwołuje się wartość `AutomationAccountName` `ApplicationDisplayName` parametrów i.
* Uprawnienia równoważne z tymi wymienionymi w [wymaganych uprawnieniach do konfigurowania kont Uruchom jako](automation-security-overview.md#permissions).

Aby uzyskać wartości dla `AutomationAccountName` , `SubscriptionId` i `ResourceGroupName` , które są wymagane parametry skryptu programu PowerShell, wykonaj poniższe kroki.

1. Zaloguj się do witryny Azure Portal.

1. Wyszukaj i wybierz pozycję **konta usługi Automation**.

1. Na stronie konta usługi Automation wybierz z listy konto usługi Automation.

1. W lewym okienku wybierz pozycję **Właściwości**.

1. Zwróć uwagę na wartości **nazwy**, **identyfikatora subskrypcji** i **grupy zasobów** na stronie **Właściwości** .

   ![Strona właściwości konta usługi Automation](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Skrypt programu PowerShell służący do tworzenia konta Uruchom jako

Skrypt programu PowerShell zawiera obsługę kilku konfiguracji.

* Tworzenie konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Utwórz konto Uruchom jako i/lub klasyczne konto Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Utwórz konto Uruchom jako i/lub klasyczne konto Uruchom jako przy użyciu certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa.
* Utwórz konto Uruchom jako i/lub klasyczne konto Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze Azure Government.

1. Pobierz i Zapisz skrypt do folderu lokalnego przy użyciu następującego polecenia.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Uruchom program PowerShell z podwyższonym poziomem uprawnień użytkownika i przejdź do folderu, który zawiera skrypt.

3. Uruchom jedno z następujących poleceń, aby utworzyć konto Uruchom jako i/lub klasyczne, zgodnie z wymaganiami.

    * Utwórz konto Uruchom jako przy użyciu certyfikatu z podpisem własnym.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu przedsiębiorstwa.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Jeśli utworzono klasyczne konto Uruchom jako z certyfikatem publicznym przedsiębiorstwa (plik. cer), użyj tego certyfikatu. Skrypt tworzy i zapisuje go w folderze plików tymczasowych na komputerze w profilu użytkownika `%USERPROFILE%\AppData\Local\Temp` użytym do wykonania sesji programu PowerShell. Zobacz [przekazywanie certyfikatu interfejsu API zarządzania do Azure Portal](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze usługi Azure Government

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Po wykonaniu skryptu zostanie wyświetlony monit o uwierzytelnienie na platformie Azure. Zaloguj się przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji. Jeśli tworzysz klasyczne konto Uruchom jako, Twoje konto musi być współadministratorem subskrypcji.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tworzeniu graficznym, zobacz [Tworzenie graficznych elementów Runbook w programie Azure Automation](automation-graphical-authoring-intro.md).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz [Samouczek: Tworzenie elementu Runbook programu PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Aby rozpocząć pracę z elementem Runbook języka Python 3, zobacz [Samouczek: Tworzenie elementu Runbook języka Python 3](learn/automation-tutorial-runbook-textual-python-3.md).