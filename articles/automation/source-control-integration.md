---
title: Korzystanie z integracji kontroli źródła w Azure Automation
description: W tym artykule opisano sposób synchronizowania Azure Automation kontroli źródła z innymi repozytoriami.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d94da9792d40a389e3981163e565d85d82a9cdc9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831244"
---
# <a name="use-source-control-integration"></a>Korzystanie z integracji kontroli źródła

 Integracja kontroli źródła w Azure Automation obsługuje synchronizację jednokierunkową z repozytorium kontroli źródła. Kontrola źródła umożliwia zapewnianie, że twoje podręczniki Runbook na koncie usługi Automation są na bieżąco ze skryptami w usłudze GitHub lub Azure Repos repozytorium kontroli źródła. Ta funkcja ułatwia podszycie się pod kątem kodu, który został przetestowany w środowisku projektowym, do produkcyjnego konta usługi Automation.

 Integracja kontroli źródła pozwala łatwo współpracować z zespołem, śledzić zmiany i wycofywała wcześniejsze wersje swoich runbook. Na przykład kontrola źródła umożliwia synchronizowanie różnych gałęzi w kontroli źródła z kontami dewelopera, testowania i produkcji usługi Automation.

## <a name="source-control-types"></a>Typy kontroli źródła

Azure Automation obsługuje trzy typy kontroli źródła:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Wymagania wstępne

* Repozytorium kontroli źródła (GitHub lub Azure Repos)
* Konto [Uruchom jako](automation-security-overview.md#run-as-accounts)
* Moduł [ `AzureRM.Profile` należy](/powershell/module/azurerm.profile/) zaimportować na konto usługi Automation. Należy pamiętać, że równoważny moduł Az ( `Az.Accounts` ) nie będzie działać z kontrolą źródła usługi Automation.

> [!NOTE]
> Zadania synchronizacji kontroli źródła są uruchamiane na koncie usługi Automation użytkownika i są rozliczane z taką samą szybkością jak inne zadania automatyzacji.

## <a name="configure-source-control"></a>Konfigurowanie kontroli źródła

W tej sekcji opisano sposób konfigurowania kontroli źródła dla konta usługi Automation. Możesz użyć interfejsu Azure Portal programu PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Konfigurowanie kontroli źródła w programie Azure Portal

Użyj tej procedury, aby skonfigurować kontrolę źródła przy użyciu Azure Portal.

1. Na koncie usługi Automation wybierz pozycję **Source Control (Kontrola źródła)** i kliknij pozycję **Add (Dodaj).**

    ![Wybieranie kontroli źródła](./media/source-control-integration/select-source-control.png)

2. Wybierz **typ kontroli źródła,** a następnie kliknij pozycję **Uwierzytelnij.**

3. Zostanie otwarte okno przeglądarki z monitem o zalogowanie. Postępuj zgodnie z monitami, aby ukończyć uwierzytelnianie.

4. Na stronie Podsumowanie kontroli kodu źródłowego wypełnij zdefiniowane poniżej właściwości kontroli źródła przy użyciu pól. Po **zakończeniu kliknij** przycisk Zapisz.

    |Właściwość  |Opis  |
    |---------|---------|
    |Nazwa kontroli źródła     | Przyjazna nazwa kontroli źródła. Ta nazwa musi zawierać tylko litery i cyfry.        |
    |Typ kontroli źródła     | Typ mechanizmu kontroli źródła. Dostępne opcje:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Repozytorium     | Nazwa repozytorium lub projektu. Pobieranych jest pierwszych 200 repozytoriów. Aby wyszukać repozytorium, wpisz nazwę w polu i kliknij pozycję **Wyszukaj w usłudze GitHub.**|
    |Odgałęzienie     | Gałąź, z której mają być ściągane pliki źródłowe. Kierowanie gałęzi nie jest dostępne dla typu kontroli źródła KONTROLI wersji serwera Team Team Team.          |
    |Ścieżka folderu     | Folder zawierający elementy Runbook do zsynchronizowania, na przykład **/Runbook.** Synchronizowane są tylko podręczniki Runbook w określonym folderze. Rekursja nie jest obsługiwana.        |
    |Automatyczna<sup>synchronizacja 1</sup>     | Ustawienie, które włącza lub wyłącza automatyczną synchronizację po zatwierdzeniu w repozytorium kontroli źródła.        |
    |Publikowanie runbook     | Ustawienie Wł., jeśli pliki Runbook są automatycznie publikowane po synchronizacji z kontroli źródła, i Wyłączone w przeciwnym razie.           |
    |Opis     | Tekst określający dodatkowe szczegóły dotyczące kontroli źródła.        |

    <sup>1</sup> Aby włączyć automatyczną synchronizację podczas konfigurowania integracji kontroli źródła z Azure Repos, musisz być administratorem projektu.

   ![Podsumowanie kontroli źródła](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Identyfikator logowania do repozytorium kontroli źródła może się różnić od logowania dla Azure Portal. Upewnij się, że podczas konfigurowania kontroli źródła zalogowano się przy użyciu odpowiedniego konta dla repozytorium kontroli źródła. Jeśli masz wątpliwości, otwórz nową kartę w przeglądarce, wyloguj się z usługi **dev.azure.com,** **visualstudio.com** lub **github.com** i spróbuj ponownie połączyć się z kontrolą źródła.

### <a name="configure-source-control-in-powershell"></a>Konfigurowanie kontroli źródła w programie PowerShell

Możesz również użyć programu PowerShell, aby skonfigurować kontrolę źródła w Azure Automation. Aby użyć poleceń cmdlet programu PowerShell dla tej operacji, potrzebny jest osobisty token dostępu. Użyj polecenia cmdlet [New-AzAutomationSourceControl,](/powershell/module/az.automation/new-azautomationsourcecontrol) aby utworzyć połączenie kontroli źródła. To polecenie cmdlet wymaga bezpiecznego ciągu dla patu dostępu. Aby dowiedzieć się, jak utworzyć bezpieczny ciąg, zobacz [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring).

Poniższe podsekcji ilustrują tworzenie połączenia kontroli źródła w programie PowerShell dla usług GitHub, Azure Repos (Git) i Azure Repos (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Tworzenie połączenia kontroli źródła dla usługi GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Tworzenie połączenia kontroli źródła dla Azure Repos (Git)

> [!NOTE]
> Azure Repos (Git) używa adresu URL, który uzyskuje dostęp do dev.azure.com **zamiast** visualstudio.com **,** używanego we wcześniejszych formatach. Starszy format adresu URL `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` jest przestarzały, ale nadal obsługiwany. Nowy format jest preferowany.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Tworzenie połączenia kontroli źródła dla Azure Repos (TFVC)

> [!NOTE]
> Azure Repos (TFVC) używa adresu URL,  który uzyskuje dostęp do dev.azure.com zamiast visualstudio.com **,** używany we wcześniejszych formatach. Starszy format adresu URL `https://<accountname>.visualstudio.com/<projectname>/_versionControl` jest przestarzały, ale nadal jest obsługiwany. Nowy format jest preferowany.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Uprawnienia osobistego tokenu dostępu

Kontrola źródła wymaga pewnych minimalnych uprawnień dla ts. Poniższe podsekcji zawierają minimalne uprawnienia wymagane dla usługi GitHub i Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Minimalne uprawnienia do paty dostępu dla usługi GitHub

W poniższej tabeli zdefiniowano minimalne uprawnienia patu dostępu wymagane dla usługi GitHub. Aby uzyskać więcej informacji na temat tworzenia osobistego tokenu dostępu w usłudze GitHub, zobacz [Tworzenie osobistego tokenu dostępu dla wiersza polecenia](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Zakres  |Opis  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Stan zatwierdzenia dostępu         |
|`repo_deployment`      | Stan wdrożenia dostępu         |
|`public_repo`     | Uzyskiwanie dostępu do repozytoriów publicznych         |
|`repo:invite` | Zaproszenia do repozytorium dostępu |
|`security_events` | Odczytywanie i zapis zdarzeń zabezpieczeń |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Wpięcie do repozytorium zapisu         |
|`read:repo_hook`|Odczytywanie wpięć repozytorium|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minimalne uprawnienia do Azure Repos

Na poniższej liście zdefiniowano minimalne uprawnienia patu dostępu wymagane do Azure Repos. Aby uzyskać więcej informacji na temat tworzenia osobistego tokenu dostępu w Azure Repos, zobacz [Uwierzytelnianie dostępu za pomocą osobistych tokenów dostępu.](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)

| Zakres  |  Typ dostępu  |
|---------| ----------|
| `Code`      | Odczyt  |
| `Project and team` | Odczyt |
| `Identity` | Odczyt     |
| `User profile` | Odczyt     |
| `Work items` | Odczyt    |
| `Service connections` | Odczyt, wykonywanie zapytań, zarządzanie<sup>1</sup>    |

<sup>1</sup> Uprawnienie `Service connections` jest wymagane tylko wtedy, gdy włączono automatyczną synchronizację.

## <a name="synchronize-with-source-control"></a>Synchronizowanie z kontrolą źródła

Wykonaj następujące kroki, aby zsynchronizować z kontrolą źródła.

1. Wybierz źródło z tabeli na stronie Kontrola źródła.

2. Kliknij **pozycję Rozpocznij synchronizację,** aby rozpocząć proces synchronizacji.

3. Aby wyświetlić stan bieżącego lub poprzedniego zadania synchronizacji, kliknij **kartę Zadania synchronizacji.**

4. Z menu **rozwijanego Source Control** (Kontrola źródła) wybierz mechanizm kontroli źródła.

    ![Stan synchronizacji](./media/source-control-integration/sync-status.png)

5. Kliknięcie zadania umożliwia wyświetlenie danych wyjściowych zadania. Poniższy przykład to dane wyjściowe zadania synchronizacji kontroli źródła.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. Dodatkowe rejestrowanie jest dostępne po wybraniu opcji **Wszystkie dzienniki** na stronie Podsumowanie zadania synchronizacji kontroli kodu źródłowego. Te dodatkowe wpisy dziennika mogą pomóc w rozwiązywaniu problemów, które mogą wystąpić podczas korzystania z kontroli źródła.

## <a name="disconnect-source-control"></a>Rozłączanie kontroli źródła

Aby rozłączyć się z repozytorium kontroli źródła:

1. Kontrola **typu open source** w obszarze Ustawienia **konta** na koncie usługi Automation.

2. Wybierz mechanizm kontroli źródła do usunięcia.

3. Na stronie Podsumowanie kontroli kodu źródłowego kliknij pozycję **Usuń.**

## <a name="handle-encoding-issues"></a>Obsługa problemów z kodowaniem

Jeśli wiele osób edytuje podręczniki Runbook w repozytorium kontroli źródła przy użyciu różnych edytorów, mogą wystąpić problemy z kodowaniem. Aby dowiedzieć się więcej na temat tej sytuacji, zobacz [Typowe przyczyny problemów z kodowaniem](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="update-the-pat"></a>Aktualizowanie pata

Obecnie nie można użyć tego Azure Portal, aby zaktualizować pat dostępu w kontroli źródła. Po wygaśnięciu lub odwołaniu tokenu dostępu można zaktualizować kontrolę źródła przy użyciu nowego tokenu dostępu w jeden z tych sposobów:

* Użyj interfejsu [API REST](/rest/api/automation/sourcecontrol/update).
* Użyj polecenia cmdlet [Update-AzAutomationSourceControl.](/powershell/module/az.automation/update-azautomationsourcecontrol)

## <a name="next-steps"></a>Następne kroki

* Aby zintegrować kontrolę źródła w programie Azure Automation, [zobacz Azure Automation: Source Control Integration in Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Aby zintegrować kontrolę źródła runbook z programem Visual Studio Codespaces, zobacz [Azure Automation: Integrating Runbook Source Control using Visual Studio Codespaces](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)(Integracja kontroli źródła runbook przy użyciu Visual Studio Codespaces).
