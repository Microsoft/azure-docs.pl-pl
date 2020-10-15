---
title: Zarządzanie Azure Automation kontem Uruchom jako
description: W tym artykule opisano, jak zarządzać kontem Uruchom jako przy użyciu programu PowerShell lub z poziomu Azure Portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0849eb0c421883ecb0510451ff81b604538c9cc3
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92069895"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Zarządzanie Azure Automation kontem Uruchom jako

Konta Uruchom jako w Azure Automation umożliwiają uwierzytelnianie w celu zarządzania zasobami w ramach Azure Resource Manager lub klasycznego modelu wdrażania platformy Azure przy użyciu elementów Runbook usługi Automation i innych funkcji automatyzacji. Ten artykuł zawiera wskazówki dotyczące zarządzania kontem Uruchom jako lub klasycznego.

Aby dowiedzieć się więcej na temat Azure Automation uwierzytelniania i wskazówek dotyczących procesu automatyzacji procesów, zobacz [Omówienie uwierzytelniania konta usługi Automation](automation-security-overview.md).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Uprawnienia konta Uruchom jako

Ta sekcja definiuje uprawnienia zarówno do zwykłych kont Uruchom jako, jak i klasycznych kont Uruchom jako.

W celu utworzenia lub zaktualizowania konta Uruchom jako wymagane są określone uprawnienia. Administrator aplikacji w Azure Active Directory i właściciel subskrypcji może ukończyć wszystkie zadania. W przypadku rozdzielenia obowiązków w poniższej tabeli przedstawiono listę zadań, równoważne polecenie cmdlet i potrzebne uprawnienia:

|Zadanie|Polecenie cmdlet  |Minimalne uprawnienia  |Ustawianie uprawnień|
|---|---------|---------|---|
|Tworzenie aplikacji usługi Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Rola dewelopera aplikacji<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Rejestracja aplikacji w domu > usługi Azure AD > |
|Dodaj poświadczenie do aplikacji.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Administrator aplikacji lub Administrator globalny<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Rejestracja aplikacji w domu > usługi Azure AD >|
|Tworzenie i pobieranie jednostki usługi Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Administrator aplikacji lub Administrator globalny<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Rejestracja aplikacji w domu > usługi Azure AD >|
|Przypisz lub uzyskaj rolę platformy Azure dla określonego podmiotu zabezpieczeń|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrator lub właściciel dostępu użytkowników albo mają następujące uprawnienia:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Subskrypcja](../role-based-access-control/role-assignments-portal.md)</br>Subskrypcje > domowej > \<subscription name\> — Access Control (IAM)|
|Utwórz lub Usuń certyfikat usługi Automation|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Współautor w grupie zasobów         |Grupa zasobów konta usługi Automation|
|Tworzenie lub usuwanie połączenia automatyzacji|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Współautor w grupie zasobów |Grupa zasobów konta usługi Automation|

<sup>1</sup> użytkownicy inni niż administratorzy w dzierżawie usługi Azure AD mogą [rejestrować aplikacje usługi AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) , jeśli opcja Użytkownicy dzierżawy usługi Azure AD **może rejestrować aplikacje** na stronie Ustawienia użytkownika jest ustawiona na **wartość tak**. Jeśli ustawienie rejestracji aplikacji ma wartość **nie**, użytkownik wykonujący tę akcję musi być zdefiniowany w tej tabeli.

Jeśli nie jesteś członkiem wystąpienia Active Directory subskrypcji przed dodaniem do roli administratora globalnego subskrypcji, zostanie on dodany jako gość. W takiej sytuacji pojawi się `You do not have permissions to create…` ostrzeżenie na stronie **Dodawanie konta usługi Automation** .

Jeśli jesteś członkiem wystąpienia Active Directory subskrypcji, do którego przypisano rolę administratora globalnego, możesz również otrzymać `You do not have permissions to create…` ostrzeżenie na stronie **Dodawanie konta usługi Automation** . W takim przypadku można zażądać usunięcia z wystąpienia Active Directory subskrypcji, a następnie zażądać ponownego dodania, aby stał się pełnym użytkownikiem w Active Directory.

Aby sprawdzić, czy problem z wygenerowaniem komunikatu o błędzie został usunięty:

1. W okienku Azure Active Directory w Azure Portal wybierz pozycję **Użytkownicy i grupy**.
2. Wybierz pozycję **Wszyscy użytkownicy**.
3. Wybierz swoją nazwę, a następnie wybierz pozycję **profil**.
4. Upewnij się, że wartość atrybutu **typu użytkownika** w profilu użytkownika nie jest ustawiona na **gość**.

### <a name="permissions-required-to-create-or-manage-classic-run-as-accounts"></a><a name="permissions-classic"></a>Uprawnienia wymagane do tworzenia klasycznych kont Uruchom jako lub zarządzania nimi

Aby skonfigurować lub odnowić klasyczne konta Uruchom jako, musisz mieć rolę współadministrator na poziomie subskrypcji. Aby dowiedzieć się więcej o uprawnieniach klasycznych subskrypcji, zobacz [klasycznej subskrypcji platformy Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Utwórz konto Uruchom jako w Azure Portal

Wykonaj następujące kroki, aby zaktualizować konto Azure Automation w Azure Portal. Utwórz pojedynczo konta Uruchom jako i klasycznej jako. Jeśli nie trzeba zarządzać zasobami klasycznymi, można po prostu utworzyć konto Uruchom jako platformy Azure.

1. Zaloguj się do portalu Azure przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.

2. Wyszukaj i wybierz pozycję **konta usługi Automation**.

3. Na stronie konta usługi Automation wybierz z listy konto usługi Automation.

4. W okienku po lewej stronie wybierz pozycję **konta Uruchom jako** w sekcji **Ustawienia konta** .

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Wybierz opcję konta Uruchom jako.":::

5. W zależności od wymaganego konta Użyj **konta Uruchom jako platformy Azure** lub okienka **klasycznego konta Uruchom jako platformy Azure** . Po przejrzeniu informacji przeglądowych kliknij przycisk **Utwórz**.

    :::image type="content" source="media/manage-runas-account/automation-account-create-runas.png" alt-text="Wybierz opcję konta Uruchom jako.":::

6. W trakcie tworzenia konta Uruchom jako na platformie Azure postęp można śledzić po wybraniu z menu opcji **Powiadomienia**. Zostanie również wyświetlony transparent informujący o utworzeniu konta. Proces może potrwać kilka minut.

## <a name="create-a-run-as-account-using-powershell"></a>Tworzenie konta Uruchom jako przy użyciu programu PowerShell

Poniższa lista zawiera wymagania dotyczące tworzenia konta Uruchom jako w programie PowerShell przy użyciu dostarczonego skryptu. Te wymagania dotyczą obu typów kont Uruchom jako.

* System Windows 10 lub Windows Server 2016 z Azure Resource Manager modules i nowszymi. Skrypt programu PowerShell nie obsługuje wcześniejszych wersji systemu Windows.
* Azure PowerShell program PowerShell 6.2.4 lub nowszy. Aby uzyskać więcej informacji, zobacz [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps).
* Konto usługi Automation, do którego odwołuje się wartość `AutomationAccountName` `ApplicationDisplayName` parametrów i.
* Uprawnienia równoważne z tymi wymienionymi w [wymaganych uprawnieniach do konfigurowania kont Uruchom jako](#permissions).

Aby uzyskać wartości dla `AutomationAccountName` , `SubscriptionId` i `ResourceGroupName` , które są wymagane parametry skryptu programu PowerShell, wykonaj poniższe kroki.

1. W Azure Portal wybierz pozycję **konta usługi Automation**.

1. Na stronie konta usługi Automation wybierz swoje konto usługi Automation.

1. W sekcji Ustawienia konta wybierz pozycję **Właściwości**.

1. Zwróć uwagę na wartości **nazwy**, **identyfikatora subskrypcji**i **grupy zasobów** na stronie **Właściwości** .

   ![Strona właściwości konta usługi Automation](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Skrypt programu PowerShell służący do tworzenia konta Uruchom jako

Skrypt programu PowerShell zawiera obsługę kilku konfiguracji.

* Tworzenie konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze usługi Azure Government.

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

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako

W tej sekcji opisano, jak usunąć konto Uruchom jako lub klasyczne. Konto usługi Automation jest zachowywane podczas wykonywania tej akcji. Po usunięciu konta Uruchom jako możesz je ponownie utworzyć w Azure Portal lub przy użyciu dostarczonego skryptu programu PowerShell.

1. W witrynie Azure Portal otwórz konto usługi Automation.

2. W okienku po lewej stronie wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.

3. Na stronie właściwości Konta Uruchom jako wybierz konto Uruchom jako albo klasyczne konto Uruchom jako, które chcesz usunąć.

4. W okienku właściwości wybranego konta kliknij pozycję **Usuń**.

   ![Usuwanie konta Uruchom jako](media/manage-runas-account/automation-account-delete-runas.png)

5. W trakcie usuwania konta postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Odnów certyfikat z podpisem własnym

Certyfikat z podpisem własnym utworzony dla konta Uruchom jako wygasa w jednym roku od daty utworzenia. W pewnym momencie przed wygaśnięciem konta Uruchom jako należy odnowić certyfikat. Można je odnowić w dowolnym momencie przed wygaśnięciem.

W przypadku odnowienia certyfikatu z podpisem własnym bieżący prawidłowy certyfikat jest zachowywany, aby upewnić się, że wszystkie elementy Runbook, które zostały dodane do kolejki lub aktywnie uruchomione, i które uwierzytelniają się przy użyciu konta Uruchom jako, nie mają negatywnego wpływu. Certyfikat pozostanie ważny aż do daty wygaśnięcia.

>[!NOTE]
>Jeśli uważasz, że zabezpieczenia konta Uruchom jako zostały naruszone, możesz usunąć i ponownie utworzyć certyfikat z podpisem własnym.

>[!NOTE]
>Jeśli konto Uruchom jako zostało skonfigurowane do użycia certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa i używasz opcji odnawiania certyfikatu z podpisem własnym, certyfikat przedsiębiorstwa zostanie zastąpiony certyfikatem z podpisem własnym.

Aby odnowić certyfikat z podpisem własnym, wykonaj następujące czynności.

1. W witrynie Azure Portal otwórz konto usługi Automation.

1. Wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.

    ![Okienko właściwości konta usługi Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Na stronie właściwości konta Uruchom jako wybierz konto Uruchom jako lub klasyczne konto Uruchom jako, dla którego chcesz odnowić certyfikat.

1. W okienku właściwości wybranego konta kliknij pozycję **odnów certyfikat**.

    ![Odnawianie certyfikatu konta Uruchom jako](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. W trakcie odnawiania certyfikatu postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

## <a name="limit-run-as-account-permissions"></a>Ogranicz uprawnienia konta Uruchom jako

Aby kontrolować Określanie wartości docelowej automatyzacji względem zasobów na platformie Azure, można uruchomić skrypt [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) . Ten skrypt zmienia istniejącą nazwę główną usługi konta Uruchom jako, aby utworzyć niestandardową definicję roli i korzystać z niej. Rola ma uprawnienia do wszystkich zasobów, z wyjątkiem [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Po uruchomieniu skryptu **Update-AutomationRunAsAccountRoleAssignments.ps1** elementy Runbook, które uzyskują dostęp do Key Vault za pomocą kont Uruchom jako, nie będą już działać. Przed uruchomieniem skryptu należy przejrzeć elementy Runbook na koncie w celu wywołania Azure Key Vault. Aby umożliwić dostęp do Key Vault z Azure Automation elementów Runbook, należy [dodać konto Uruchom jako do uprawnień Key Vault](#add-permissions-to-key-vault).

Jeśli trzeba będzie ograniczyć, jak może to zrobić jednostka usługi Uruchom jako, możesz dodać inne typy zasobów do `NotActions` elementu definicji roli niestandardowej. Poniższy przykład ogranicza dostęp do programu `Microsoft.Compute/*` . W przypadku dodania tego typu zasobu do `NotActions` definicji roli nie będzie można uzyskać dostępu do żadnego zasobu obliczeniowego. Aby dowiedzieć się więcej na temat definicji ról, zobacz [Omówienie definicji ról dla zasobów platformy Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Można określić, czy nazwa główna usługi używana przez konto Uruchom jako jest w definicji roli współautor, czy też niestandardowa.

1. Przejdź do konta usługi Automation i wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.
2. Wybierz pozycję **konto Uruchom jako platformy Azure**.
3. Wybierz **rolę** , aby zlokalizować używaną definicję roli.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Wybierz opcję konta Uruchom jako." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Można również określić definicję roli używaną przez konta Uruchom jako dla wielu subskrypcji lub kont usługi Automation. W tym celu należy użyć skryptu [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) w Galeria programu PowerShell.

### <a name="add-permissions-to-key-vault"></a>Dodawanie uprawnień do Key Vault

Możesz zezwolić Azure Automation, aby sprawdzić, czy Key Vault i nazwa główna usługi konta Uruchom jako używają niestandardowej definicji roli. Należy:

* Udziel uprawnień do Key Vault.
* Ustaw zasady dostępu.

Możesz użyć skryptu [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) w Galeria programu PowerShell, aby nadać kontu Uruchom jako uprawnienia do Key Vault. Aby uzyskać więcej informacji na temat ustawiania uprawnień Key Vault, zobacz temat [przypisywanie zasad dostępu Key Vault](../key-vault/general/assign-access-policy-powershell.md) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Rozwiązywanie problemów z błędami konfiguracji dla kont Uruchom jako

Niektóre elementy konfiguracji niezbędne do uruchomienia jako lub klasycznego konta Uruchom jako mogły zostać usunięte lub utworzone nieprawidłowo podczas konfiguracji początkowej. Możliwe wystąpienia niepozostałej konfiguracji obejmują:

* Zasób certyfikatu
* Zasób połączenia
* Konto Uruchom jako zostało usunięte z roli współautor
* Nazwa główna usługi lub aplikacji w usłudze Azure AD

W przypadku takich błędnych wystąpień konfiguracji konto usługi Automation wykrywa zmiany i wyświetla stan *niekompletne* w okienku właściwości konta Uruchom jako dla konta.

![Stan Niekompletne dla konfiguracji konta Uruchom jako](media/manage-runas-account/automation-account-runas-config-incomplete.png)

Po wybraniu konta Uruchom jako w okienku właściwości konta zostanie wyświetlony następujący komunikat o błędzie:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Możesz szybko rozwiązać te problemy dotyczące konta Uruchom jako, usuwając i tworząc je.

## <a name="next-steps"></a>Następne kroki

* [Obiekty aplikacji i obiekty główne usługi](../active-directory/develop/app-objects-and-service-principals.md).
* [Omówienie certyfikatów dla Cloud Services platformy Azure](../cloud-services/cloud-services-certs-create.md).