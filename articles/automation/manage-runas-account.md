---
title: Zarządzanie Azure Automation kontem Uruchom jako
description: W tym artykule opisano, jak zarządzać kontem Uruchom jako przy użyciu programu PowerShell lub z poziomu Azure Portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 06/26/2020
ms.topic: conceptual
ms.openlocfilehash: 84f2ef1fc318e53914b1510870d0399cfbfa87e9
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185283"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Zarządzanie Azure Automation kontem Uruchom jako

Konta Uruchom jako w Azure Automation umożliwiają uwierzytelnianie w celu zarządzania zasobami na platformie Azure przy użyciu poleceń cmdlet platformy Azure. Podczas tworzenia konta Uruchom jako jest tworzony nowy użytkownik głównej nazwy usługi w Azure Active Directory (AD) i przypisuje rolę współautor do tego użytkownika na poziomie subskrypcji.

## <a name="types-of-run-as-accounts"></a>Typy kont Uruchom jako

Azure Automation używa dwóch typów kont Uruchom jako:

* Konto Uruchom jako platformy Azure
* Klasyczne konto Uruchom jako platformy Azure

>[!NOTE]
>Subskrypcje dostawcy rozwiązań w chmurze (CSP) platformy Azure obsługują tylko model Azure Resource Manager. Usługi inne niż Azure Resource Manager nie są dostępne w programie. W przypadku korzystania z subskrypcji programu CSP klasyczne konto Uruchom jako platformy Azure nie jest tworzone, ale zostało utworzone konto Uruchom jako platformy Azure. Aby dowiedzieć się więcej o subskrypcjach dostawcy usług kryptograficznych, zobacz [dostępne usługi w subskrypcjach programu CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Nazwa główna usługi dla konta Uruchom jako nie ma uprawnień do domyślnego odczytywania usługi Azure AD. Jeśli chcesz dodać uprawnienia do odczytu lub zarządzania usługą Azure AD, musisz udzielić uprawnień do nazwy głównej usługi w obszarze **uprawnienia interfejsu API**. Aby dowiedzieć się więcej, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>Konto Uruchom jako

Konto Uruchom jako służy do zarządzania zasobami [modelu wdrażania Menedżer zasobów](../azure-resource-manager/management/deployment-models.md) . Wykonuje następujące zadania.

* Tworzy aplikację usługi Azure AD z certyfikatem z podpisem własnym, tworzy konto jednostki usługi dla aplikacji w usłudze Azure AD i przypisuje rolę Współautor dla konta w bieżącej subskrypcji. Można zmienić ustawienie certyfikatu na właściciela lub inną rolę. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).
  
* Tworzy zasób certyfikatu usługi Automation o nazwie `AzureRunAsCertificate` w określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu, którego używa aplikacja usługi Azure AD.
  
* Tworzy zasób połączenia usługi Automation o nazwie `AzureRunAsConnection` w określonym koncie usługi Automation. Zasób połączenia zawiera identyfikator aplikacji, identyfikator dzierżawy, Identyfikator subskrypcji i odcisk palca certyfikatu.

### <a name="azure-classic-run-as-account"></a>Klasyczne konto Uruchom jako platformy Azure

Klasyczne konto Uruchom jako platformy Azure zarządza zasobami [klasycznego modelu wdrażania](../azure-resource-manager/management/deployment-models.md) . Aby utworzyć lub odnowić ten typ konta, musisz być współadministratorem w ramach subskrypcji.

Klasyczne konto Uruchom jako platformy Azure wykonuje następujące zadania.

  * Tworzy certyfikat zarządzania w subskrypcji.

  * Tworzy zasób certyfikatu usługi Automation o nazwie `AzureClassicRunAsCertificate` w określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez certyfikat zarządzania.

  * Tworzy zasób połączenia usługi Automation o nazwie `AzureClassicRunAsConnection` w określonym koncie usługi Automation. Zasób połączenia zawiera nazwę subskrypcji, Identyfikator subskrypcji i nazwę zasobu certyfikatu.

>[!NOTE]
>Klasyczne konto Uruchom jako platformy Azure nie jest domyślnie tworzone w momencie tworzenia konta usługi Automation. To konto jest tworzone osobno zgodnie z krokami opisanymi w dalszej części tego artykułu.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Uzyskaj uprawnienia konta Uruchom jako

Ta sekcja definiuje uprawnienia zarówno do zwykłych kont Uruchom jako, jak i klasycznych kont Uruchom jako.

### <a name="get-permissions-to-configure-run-as-accounts"></a>Uzyskaj uprawnienia do konfigurowania kont Uruchom jako

W celu utworzenia lub zaktualizowania konta Uruchom jako wymagane są określone uprawnienia. Administrator aplikacji w Azure Active Directory i właściciel subskrypcji może ukończyć wszystkie zadania. W przypadku rozdzielenia obowiązków w poniższej tabeli przedstawiono listę zadań, równoważne polecenie cmdlet i potrzebne uprawnienia:

|Zadanie|Polecenie cmdlet  |Minimalne uprawnienia  |Ustawianie uprawnień|
|---|---------|---------|---|
|Tworzenie aplikacji usługi Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Rola dewelopera aplikacji<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Rejestracja aplikacji w domu > usługi Azure AD > |
|Dodaj poświadczenie do aplikacji.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Administrator aplikacji lub Administrator globalny<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Rejestracja aplikacji w domu > usługi Azure AD >|
|Tworzenie i pobieranie jednostki usługi Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Administrator aplikacji lub Administrator globalny<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Rejestracja aplikacji w domu > usługi Azure AD >|
|Przypisz lub uzyskaj rolę RBAC dla określonego podmiotu zabezpieczeń|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrator lub właściciel dostępu użytkowników albo mają następujące uprawnienia:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Subskrypcja](../role-based-access-control/role-assignments-portal.md)</br>Subskrypcje > domowej > \<subscription name\> — Access Control (IAM)|
|Utwórz lub Usuń certyfikat usługi Automation|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Współautor w grupie zasobów         |Grupa zasobów konta usługi Automation|
|Tworzenie lub usuwanie połączenia automatyzacji|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Współautor w grupie zasobów |Grupa zasobów konta usługi Automation|

<sup>1</sup> użytkownicy inni niż administratorzy w dzierżawie usługi Azure AD mogą [rejestrować aplikacje usługi AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) , jeśli opcja Użytkownicy dzierżawy usługi Azure AD **może rejestrować aplikacje** na stronie Ustawienia użytkownika jest ustawiona na **wartość tak**. Jeśli ustawienie rejestracji aplikacji ma wartość **nie**, użytkownik wykonujący tę akcję musi być zdefiniowany w tej tabeli.

Jeśli nie jesteś członkiem wystąpienia Active Directory subskrypcji przed dodaniem do roli administratora globalnego subskrypcji, zostanie on dodany jako gość. W takiej sytuacji pojawi się `You do not have permissions to create…` ostrzeżenie na stronie **Dodawanie konta usługi Automation** .

Jeśli jesteś członkiem wystąpienia Active Directory subskrypcji po przypisaniu roli administratora globalnego, możesz również otrzymać `You do not have permissions to create…` ostrzeżenie na stronie **Dodawanie konta usługi Automation** . W takim przypadku można zażądać usunięcia z wystąpienia Active Directory subskrypcji, a następnie zażądać ponownego dodania, aby stał się pełnym użytkownikiem w Active Directory.

Aby sprawdzić, czy problem z wygenerowaniem komunikatu o błędzie został usunięty:

1. W okienku Azure Active Directory w Azure Portal wybierz pozycję **Użytkownicy i grupy**.
2. Wybierz pozycję **Wszyscy użytkownicy**.
3. Wybierz swoją nazwę, a następnie wybierz pozycję **profil**. 
4. Upewnij się, że wartość atrybutu **typu użytkownika** w profilu użytkownika nie jest ustawiona na **gość**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Uzyskaj uprawnienia do konfigurowania klasycznych kont Uruchom jako

Aby skonfigurować lub odnowić klasyczne konta Uruchom jako, musisz mieć rolę współadministrator na poziomie subskrypcji. Aby dowiedzieć się więcej o uprawnieniach klasycznych subskrypcji, zobacz [klasycznej subskrypcji platformy Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Utwórz konto Uruchom jako w Azure Portal

Wykonaj następujące kroki, aby zaktualizować konto Azure Automation w Azure Portal. Utwórz pojedynczo konta Uruchom jako i klasycznej jako. Jeśli nie trzeba zarządzać zasobami klasycznymi, można po prostu utworzyć konto Uruchom jako platformy Azure.

1. Zaloguj się do portalu Azure przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.

2. Wyszukaj i wybierz pozycję **konta usługi Automation**.

3. Na stronie konta usługi Automation wybierz z listy konto usługi Automation.

4. W okienku po lewej stronie wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.

5. W zależności od tego, które konto jest wymagane, wybierz pozycję **Konto Uruchom jako platformy Azure** lub **Klasyczne konto Uruchom jako platformy Azure**. 

6. W zależności od konta zainteresowania użyj okienka Dodawanie konta Uruchom **jako platformy Azure** lub **Dodaj klasyczne konto Uruchom jako platformy Azure** . Po przejrzeniu informacji przeglądowych kliknij przycisk **Utwórz**.

7. W trakcie tworzenia konta Uruchom jako na platformie Azure postęp można śledzić po wybraniu z menu opcji **Powiadomienia**. Zostanie również wyświetlony transparent informujący o utworzeniu konta. Proces może potrwać kilka minut.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Usuwanie konta Uruchom jako lub klasycznego konta Uruchom jako

W tej sekcji opisano, jak usunąć konto Uruchom jako lub klasyczne. Konto usługi Automation jest zachowywane podczas wykonywania tej akcji. Po usunięciu konta możesz je ponownie utworzyć w Azure Portal.

1. W witrynie Azure Portal otwórz konto usługi Automation.

2. W okienku po lewej stronie wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.

3. Na stronie właściwości Konta Uruchom jako wybierz konto Uruchom jako albo klasyczne konto Uruchom jako, które chcesz usunąć. 

4. W okienku właściwości wybranego konta kliknij pozycję **Usuń**.

   ![Usuwanie konta Uruchom jako](media/manage-runas-account/automation-account-delete-runas.png)

5. W trakcie usuwania konta postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

6. Po usunięciu konta możesz je ponownie utworzyć na stronie właściwości Konta Uruchom jako, wybierając opcję tworzenia **Konto Uruchom jako platformy Azure**.

   ![Ponowne tworzenie konta Uruchom jako usługi Automation](media/manage-runas-account/automation-account-create-runas.png)

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

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Można również określić definicję roli używaną przez konta Uruchom jako dla wielu subskrypcji lub kont usługi Automation. W tym celu należy użyć skryptu [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) w Galeria programu PowerShell.

### <a name="add-permissions-to-key-vault"></a>Dodawanie uprawnień do Key Vault

Możesz zezwolić Azure Automation, aby sprawdzić, czy Key Vault i nazwa główna usługi konta Uruchom jako używają niestandardowej definicji roli. Należy:

* Udziel uprawnień do Key Vault.
* Ustaw zasady dostępu.

Możesz użyć skryptu [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) w Galeria programu PowerShell, aby nadać kontu Uruchom jako uprawnienia do Key Vault. Aby uzyskać więcej informacji na temat ustawiania uprawnień dla Key Vault [, zobacz Udzielanie aplikacjom dostępu do magazynu kluczy](../key-vault/general/group-permissions-for-apps.md) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Rozwiązywanie problemów z błędami konfiguracji dla kont Uruchom jako

Niektóre elementy konfiguracji niezbędne do uruchomienia jako lub klasycznego konta Uruchom jako mogły zostać usunięte lub utworzone nieprawidłowo podczas konfiguracji początkowej. Możliwe wystąpienia niepozostałej konfiguracji obejmują:

* Zasób certyfikatu
* Zasób połączenia
* Konto Uruchom jako zostało usunięte z roli współautor
* Nazwa główna usługi lub aplikacji w usłudze Azure AD

W przypadku takich błędnych wystąpień konfiguracji konto usługi Automation wykrywa zmiany i wyświetla stan *niekompletne* w okienku właściwości konta Uruchom jako dla konta.

![Stan Niekompletne dla konfiguracji konta Uruchom jako](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Po wybraniu konta Uruchom jako w okienku właściwości konta zostanie wyświetlony następujący komunikat o błędzie:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Te problemy związane z kontem Uruchom jako można szybko rozwiązać, usuwając konto i tworząc je ponownie.

## <a name="next-steps"></a>Następne kroki

* [Obiekty aplikacji i obiekty główne usługi](../active-directory/develop/app-objects-and-service-principals.md).
* [Omówienie certyfikatów dla Cloud Services platformy Azure](../cloud-services/cloud-services-certs-create.md).
