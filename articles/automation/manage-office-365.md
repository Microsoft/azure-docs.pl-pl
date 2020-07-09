---
title: Zarządzanie usługami pakietu Office 365 przy użyciu usługi Azure Automation
description: W tym artykule opisano, jak za pomocą Azure Automation zarządzać usługami subskrypcji pakietu Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 322e2a3679ed29ab9ecc4cdc3c6e1fe4d0f20276
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83831175"
---
# <a name="manage-office-365-services"></a>Zarządzanie usługami Office 365

Za pomocą Azure Automation do zarządzania usługami subskrypcji pakietu Office 365 w przypadku produktów takich jak Microsoft Word i Microsoft Outlook. Interakcje z pakietem Office 365 są włączane przez [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Aby [uwierzytelnić się na platformie Azure, zobacz temat Korzystanie z usługi Azure AD w Azure Automation](automation-use-azure-ad.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby zarządzać usługami subskrypcji pakietu Office 365 w programie Azure Automation, należy wykonać następujące czynności.

* Subskrypcja platformy Azure. Zobacz [Przewodnik po decyzji](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)dotyczącej subskrypcji.
* Obiekt automatyzacji na platformie Azure do przechowywania poświadczeń konta użytkownika i elementów Runbook. Zapoznaj [się z wprowadzeniem do Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro).
* Usługa Azure AD. Aby [uwierzytelnić się na platformie Azure, zobacz temat Korzystanie z usługi Azure AD w Azure Automation](automation-use-azure-ad.md).
* Dzierżawa pakietu Office 365 z kontem. Zobacz [Konfigurowanie dzierżawy pakietu Office 365](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="install-the-msonline-and-msonlineext-modules"></a>Instalowanie modułów MSOnline i MSOnlineExt

Korzystanie z pakietu Office 365 w ramach Azure Automation wymaga Microsoft Azure Active Directory dla środowiska Windows PowerShell ( `MSOnline` moduł). Wymagany jest również moduł [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35) , który upraszcza zarządzanie usługą Azure AD w środowiskach z jedną i wieloma dzierżawcami. Zainstaluj moduły zgodnie z opisem w temacie [Korzystanie z usługi Azure AD w Azure Automation do uwierzytelniania na platformie Azure](automation-use-azure-ad.md).

>[!NOTE]
>Aby korzystać z programu MSOnline PowerShell, musisz być członkiem usługi Azure AD. Użytkownicy-Goście nie mogą używać modułu.

## <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

Aby wykonać kroki opisane w tym artykule, musisz mieć konto w Azure Automation. Zobacz [Tworzenie konta Azure Automation](automation-quickstart-create-account.md).
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>Dodawanie MSOnline i MSOnlineExt jako elementów zawartości

Teraz Dodaj zainstalowane moduły MSOnline i MSOnlineExt, aby umożliwić korzystanie z funkcji pakietu Office 365. Zapoznaj się z tematem [Zarządzanie modułami w Azure Automation](shared-resources/modules.md).

1. W Azure Portal wybierz pozycję **konta usługi Automation**.
2. Wybierz konto usługi Automation.
3. Wybierz pozycję **Galeria modułów** w obszarze **zasoby udostępnione**.
4. Wyszukaj MSOnline.
5. Wybierz `MSOnline` moduł PowerShell, a następnie kliknij przycisk **Importuj** , aby zaimportować moduł jako element zawartości.
6. Powtórz kroki 4 i 5, aby zlokalizować i zaimportować `MSOnlineExt` moduł. 

## <a name="create-a-credential-asset-optional"></a>Utwórz zasób poświadczeń (opcjonalnie)

Jest to opcjonalne, aby utworzyć zasób poświadczeń dla użytkownika administracyjnego pakietu Office 365, który ma uprawnienia do uruchamiania skryptu. Może to pomóc, ale zachować możliwość ujawniania nazw użytkowników i haseł wewnątrz skryptów programu PowerShell. Aby uzyskać instrukcje, zobacz [Tworzenie zasobu poświadczeń](automation-use-azure-ad.md#create-a-credential-asset).

## <a name="create-an-office-365-service-account"></a>Utwórz konto usługi Office 365

Aby móc uruchamiać usługi subskrypcji pakietu Office 365, musisz mieć konto usługi Office 365 z uprawnieniami do wykonywania tych czynności. Można użyć jednego konta administratora globalnego, jednego konta na usługę lub jednej funkcji lub skryptu do wykonania. W każdym przypadku konto usługi wymaga złożonego i bezpiecznego hasła. Zobacz [Konfigurowanie pakietu Office 365 dla firm](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connect-to-the-azure-ad-online-service"></a>Nawiązywanie połączenia z usługą online usługi Azure AD

>[!NOTE]
>Aby użyć poleceń cmdlet modułu MSOnline, należy uruchomić je z programu Windows PowerShell. Program PowerShell Core nie obsługuje tych poleceń cmdlet.

Możesz użyć modułu MSOnline, aby nawiązać połączenie z usługą Azure AD w ramach subskrypcji pakietu Office 365. Połączenie używa nazwy użytkownika i hasła pakietu Office 365 lub używa uwierzytelniania wieloskładnikowego (MFA). Można nawiązać połączenie za pomocą Azure Portal lub wiersza polecenia programu Windows PowerShell (nie trzeba mieć podwyższonego poziomu uprawnień).

Poniżej przedstawiono przykład programu PowerShell. Polecenie cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) wyświetli komunikat z prośbą o poświadczenia i przechowuje je w `Msolcred` zmiennej. Następnie polecenie cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) używa poświadczeń do nawiązania połączenia z usługą online usługi Azure Directory. Jeśli chcesz nawiązać połączenie z określonym środowiskiem platformy Azure, użyj `AzureEnvironment` parametru.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Jeśli nie otrzymasz żadnych błędów, nawiązanie połączenia powiodło się. Szybki test polega na uruchomieniu polecenia cmdlet pakietu Office 365, na przykład, `Get-MsolUser` i wyświetleniu wyników. Jeśli wystąpią błędy, należy pamiętać, że typowy problem jest nieprawidłowym hasłem.

>[!NOTE]
>Możesz również użyć modułu AzureRM lub AZ module, aby nawiązać połączenie z usługą Azure AD w ramach subskrypcji pakietu Office 365. Główne polecenie cmdlet połączenia to [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). To polecenie cmdlet obsługuje `AzureEnvironmentName` parametr dla konkretnych środowisk pakietu Office 365.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>Tworzenie elementu Runbook programu PowerShell na podstawie istniejącego skryptu

Dostęp do funkcjonalności pakietu Office 365 z poziomu skryptu programu PowerShell. Oto przykład skryptu dla poświadczenia o nazwie `Office-Credentials` z nazwą użytkownika `admin@TenantOne.com` . Używa `Get-AutomationPSCredential` do zaimportowania poświadczeń pakietu Office 365.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="run-the-script-in-a-runbook"></a>Uruchamianie skryptu w elemencie Runbook

Możesz użyć skryptu w Azure Automation elemencie Runbook. Na przykład użyjemy typu elementu Runbook programu PowerShell.

1. Utwórz nowy element Runbook programu PowerShell. Zapoznaj się z tematem [tworzenie Azure Automation elementu Runbook](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Z poziomu konta usługi Automation wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.
3. Wybierz nowy element Runbook, a następnie kliknij przycisk **Edytuj**.
4. Skopiuj skrypt i wklej go do edytora tekstu dla elementu Runbook.
5. Wybierz pozycję **zasoby**, a następnie rozwiń pozycję **poświadczenia** i sprawdź, czy jest tam poświadczenie pakietu Office 365.
6. Kliknij pozycję **Zapisz**.
7. Wybierz pozycję **okienko testowania**, a następnie kliknij przycisk **Rozpocznij** , aby rozpocząć testowanie elementu Runbook. Zobacz [Zarządzanie elementami Runbook w Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. Po zakończeniu testowania Wyjdź z okienka testowania.

## <a name="publish-and-schedule-the-runbook"></a>Publikowanie i planowanie elementu Runbook

Aby opublikować i zaplanować element Runbook, zobacz [Zarządzanie elementami Runbook w Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje o używaniu poświadczeń, zobacz [Zarządzanie poświadczeniami w Azure Automation](shared-resources/credentials.md).
* Aby uzyskać informacje o modułach, zobacz [Zarządzanie modułami w Azure Automation](shared-resources/modules.md).
* Jeśli musisz uruchomić element Runbook, zobacz temat [Uruchamianie elementu Runbook w Azure Automation](start-runbooks.md).
* Aby uzyskać szczegółowe informacje dotyczące programu PowerShell, zobacz dokumentację [programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).