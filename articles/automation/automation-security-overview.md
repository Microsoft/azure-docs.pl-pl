---
title: Azure Automation omówienie uwierzytelniania konta
description: Ten artykuł zawiera omówienie uwierzytelniania Azure Automation konta.
keywords: zabezpieczenia usługi automation, bezpieczna usługa automation, uwierzytelnianie usługi automation
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 546407ce7286cebc04d3c86422f6242051d1dbf3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830434"
---
# <a name="azure-automation-account-authentication-overview"></a>Azure Automation omówienie uwierzytelniania konta

Usługa Azure Automation pozwala na zautomatyzowanie zadań w odniesieniu do zasobów platformy Azure, a także zasobów lokalnych oraz pochodzących od innych dostawców chmury, takich jak usługa Amazon Web Services (AWS). Możesz użyć runbook do automatyzacji zadań lub hybrydowego procesu roboczego runbook, jeśli masz procesy biznesowe lub operacyjne do zarządzania poza platformą Azure. Praca w dowolnym z tych środowisk wymaga uprawnień do bezpiecznego uzyskiwania dostępu do zasobów przy minimalnych wymaganych prawach.

W tym artykule opisano scenariusze uwierzytelniania Azure Automation i opisano sposób rozpoczynania pracy na podstawie środowiska lub środowisk, które należy zarządzać.

## <a name="automation-account"></a>Konto usługi Automation

Kiedy uruchamiasz usługę Azure Automation po raz pierwszy, musisz utworzyć co najmniej jedno konto usługi Automation. Konta usługi Automation umożliwiają izolowanie zasobów usługi Automation, runbook, zasobów i konfiguracji od zasobów innych kont. Za pomocą kont usługi Automation można rozdzielić zasoby na oddzielne środowiska logiczne lub delegowane obowiązki. Na przykład jednego konta można użyć dla środowiska rozwojowego, innego dla produkcyjnego, a jeszcze innego dla lokalnego. Możesz też dedykować konto usługi Automation do zarządzania aktualizacjami systemu operacyjnego na wszystkich maszynach przy użyciu [Update Management](update-management/overview.md). 

Konto usługi Azure Automation różni się od konta Microsoft lub kont utworzonych w ramach subskrypcji platformy Azure. Aby uzyskać wprowadzenie do tworzenia konta usługi Automation, zobacz [Tworzenie konta usługi Automation.](automation-quickstart-create-account.md)

## <a name="automation-resources"></a>Zasoby automatyzacji

Zasoby usługi Automation dla każdego konta usługi Automation są skojarzone z pojedynczym regionem świadczenia usługi Azure, ale konto może zarządzać wszystkimi zasobami w ramach subskrypcji platformy Azure. Głównym powodem tworzenia kont usługi Automation w różnych regionach jest to, że masz zasady, które wymagają odizolowanie danych i zasobów do określonego regionu.

Wszystkie zadania, które tworzysz względem zasobów przy użyciu programu Azure Resource Manager i poleceń cmdlet programu PowerShell w programie Azure Automation, muszą być uwierzytelniane na platformie Azure przy użyciu uwierzytelniania opartego na poświadczeniach tożsamości organizacyjnej usługi Azure Active Directory (Azure AD).

## <a name="managed-identities-preview"></a>Tożsamości zarządzane (wersja zapoznawcza)

Tożsamość zarządzana z usługi Azure Active Directory (Azure AD) umożliwia użytkownikowi runbook łatwy dostęp do innych zasobów chronionych przez usługę Azure AD. Tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowizować ani obracać żadnych wpisów tajnych. Aby uzyskać więcej informacji na temat tożsamości zarządzanych w usłudze Azure AD, zobacz [Tożsamości zarządzane dla zasobów platformy Azure.](/azure/active-directory/managed-identities-azure-resources/overview)

Oto niektóre zalety korzystania z tożsamości zarządzanych:

- Tożsamości zarządzanych można używać do uwierzytelniania w dowolnej usłudze platformy Azure, która obsługuje uwierzytelnianie usługi Azure AD. Mogą być używane do zadań w chmurze i zadań hybrydowych. Zadania hybrydowe mogą używać tożsamości zarządzanych, gdy są uruchamiane w hybrydowym procesu roboczego runbook uruchomionym na maszynie wirtualnej platformy Azure lub spoza platformy Azure.

- Tożsamości zarządzane mogą być używane bez żadnych dodatkowych kosztów.

- Nie trzeba odnawiać certyfikatu używanego przez konto Uruchom jako usługi Automation.

- Nie trzeba określać obiektu połączenia Uruchom jako w kodzie runbook. Możesz uzyskać dostęp do zasobów przy użyciu tożsamości zarządzanej konta usługi Automation z podręcznika Runbook bez tworzenia certyfikatów, połączeń, kont Uruchom jako itp.

Kontu usługi Automation można przyznać dwa typy tożsamości:

- Tożsamość przypisana przez system jest powiązana z aplikacją i jest usuwana w przypadku usunięcia aplikacji. Aplikacja może mieć tylko jedną tożsamość przypisaną przez system.

- Tożsamość przypisana przez użytkownika to autonomiczny zasób platformy Azure, który można przypisać do aplikacji. Aplikacja może mieć wiele tożsamości przypisanych przez użytkownika.

>[!NOTE]
> Tożsamości przypisane przez użytkownika nie są jeszcze obsługiwane.

Aby uzyskać szczegółowe informacje na temat korzystania z tożsamości zarządzanych, zobacz Włączanie tożsamości [zarządzanej Azure Automation (wersja zapoznawcza).](enable-managed-identity-for-automation.md)

## <a name="run-as-accounts"></a>Konta Uruchom jako

Konta Uruchom jako w Azure Automation zapewniają uwierzytelnianie na Azure Resource Manager zasobów lub zasobów wdrożonych w klasycznym modelu wdrażania. Istnieją dwa typy kont Uruchom jako w Azure Automation:

* Konto Uruchom jako platformy Azure: umożliwia zarządzanie zasobami platformy Azure na podstawie Azure Resource Manager wdrażania i zarządzania dla platformy Azure.
* Klasyczne konto Uruchom jako platformy Azure: umożliwia zarządzanie klasycznymi zasobami platformy Azure w oparciu o klasyczny model wdrażania.

Aby dowiedzieć się więcej na temat Azure Resource Manager i klasycznego wdrażania, zobacz [Resource Manager wdrażania klasycznego.](../azure-resource-manager/management/deployment-models.md)

>[!NOTE]
>Azure Cloud Solution Provider (CSP) obsługują tylko Azure Resource Manager modelu. Usługi inne Azure Resource Manager nie są dostępne w programie. Jeśli używasz subskrypcji CSP, klasyczne konto Uruchom jako platformy Azure nie zostanie utworzone, ale zostanie utworzone konto Uruchom jako platformy Azure. Aby dowiedzieć się więcej na temat subskrypcji CSP, zobacz [Dostępne usługi w subskrypcjach CSP.](/azure/cloud-solution-provider/overview/azure-csp-available-services)

Podczas tworzenia konta usługi Automation konto Uruchom jako jest tworzone domyślnie w tym samym czasie. Jeśli nie chcesz go tworzyć razem z kontem usługi Automation, można je utworzyć osobno w późniejszym czasie. Klasyczne konto Uruchom jako platformy Azure jest opcjonalne i jest tworzone oddzielnie, jeśli musisz zarządzać zasobami klasycznymi.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>Konto Uruchom jako

Podczas tworzenia konta Uruchom jako wykonywane są następujące zadania:

* Tworzy aplikację usługi Azure AD z certyfikatem z podpisem własnym, tworzy konto jednostki usługi dla aplikacji w usłudze Azure AD i przypisuje rolę [Współautor](../role-based-access-control/built-in-roles.md#contributor) dla konta w bieżącej subskrypcji. Ustawienie certyfikatu można zmienić na [Czytelnik](../role-based-access-control/built-in-roles.md#reader) lub dowolną inną rolę. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).

* Tworzy zasób certyfikatu usługi Automation `AzureRunAsCertificate` o nazwie na określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez aplikację usługi Azure AD.

* Tworzy zasób połączenia usługi Automation o `AzureRunAsConnection` nazwie na określonym koncie usługi Automation. Zasób połączenia zawiera identyfikator aplikacji, identyfikator dzierżawy, identyfikator subskrypcji i odcisk palca certyfikatu.

### <a name="azure-classic-run-as-account"></a>Klasyczne konto Uruchom jako platformy Azure

Podczas tworzenia klasycznego konta Uruchom jako platformy Azure wykonywane są następujące zadania:

> [!NOTE]
> Aby utworzyć lub odnowić konto Uruchom jako tego typu, musisz być współwłascą subskrypcji.

* Tworzy certyfikat zarządzania w subskrypcji.

* Tworzy zasób certyfikatu usługi Automation `AzureClassicRunAsCertificate` o nazwie na określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez certyfikat zarządzania.

* Tworzy zasób połączenia usługi Automation o `AzureClassicRunAsConnection` nazwie na określonym koncie usługi Automation. Zasób połączenia zawiera nazwę subskrypcji, identyfikator subskrypcji i nazwę zasobu certyfikatu.

## <a name="service-principal-for-run-as-account"></a>Jednostkę usługi dla konta Uruchom jako

Jednostki usługi dla konta Uruchom jako nie ma uprawnień do odczytu usługi Azure AD domyślnie. Jeśli chcesz dodać uprawnienia do odczytu lub zarządzania usługą Azure AD, musisz udzielić uprawnień do jednostki usługi w obszarze Uprawnienia **interfejsu API.** Aby dowiedzieć się więcej, zobacz [Dodawanie uprawnień dostępu do internetowego interfejsu API.](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Uprawnienia konta Uruchom jako

W tej sekcji zdefiniowano uprawnienia zarówno dla zwykłych kont Uruchom jako, jak i klasycznych kont Uruchom jako.

* Aby utworzyć lub zaktualizować konto Uruchom jako, administrator aplikacji w Azure Active Directory i właściciel subskrypcji mogą wykonać wszystkie zadania.
* Aby skonfigurować lub odnowić klasyczne konta Uruchom jako, musisz mieć rolę współwłascy na poziomie subskrypcji. Aby dowiedzieć się więcej na temat klasycznych uprawnień subskrypcji, zobacz [Klasyczny administrator subskrypcji platformy Azure.](../role-based-access-control/classic-administrators.md#add-a-co-administrator)

W sytuacji, gdy masz separację obowiązków, w poniższej tabeli przedstawiono listę zadań, równoważnego polecenia cmdlet i wymaganych uprawnień:

|Zadanie|Polecenie cmdlet  |Minimalne uprawnienia  |Miejsce ustawienia uprawnień|
|---|---------|---------|---|
|Tworzenie aplikacji usługi Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Rola dewelopera<sup>aplikacji 1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Strona > rejestracji aplikacji > Azure AD |
|Dodaj poświadczenia do aplikacji.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Administrator aplikacji lub administrator globalny<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Strona > usługi Azure AD > rejestracje aplikacji|
|Tworzenie i uzyskiwanie jednostki usługi Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Administrator aplikacji lub administrator globalny<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Strona > usługi Azure AD > rejestracje aplikacji|
|Przypisywanie lub uzyskiwanie roli platformy Azure dla określonego podmiotu zabezpieczeń|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrator dostępu użytkowników lub właściciel albo mają następujące uprawnienia:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Subskrypcja](../role-based-access-control/role-assignments-portal.md)</br>Strona główna > subskrypcje > \<subscription name\> — Access Control (IAM)|
|Tworzenie lub usuwanie certyfikatu automatyzacji|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Współautor w grupie zasobów         |Grupa zasobów konta usługi Automation|
|Tworzenie lub usuwanie połączenia usługi Automation|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Współautor w grupie zasobów |Grupa zasobów konta usługi Automation|

<sup>1</sup> Użytkownicy niebędący administratorami w dzierżawie usługi Azure AD mogą  rejestrować aplikacje usługi  [AD,](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) jeśli opcja Użytkownicy mogą rejestrować aplikacje w dzierżawie usługi Azure AD na stronie Ustawienia użytkownika ma wartość **Tak.** Jeśli ustawienie rejestracji aplikacji ma ustawienie **Nie,** użytkownik wykonujący tę akcję musi być zdefiniowany w tej tabeli.

Jeśli nie jesteś członkiem wystąpienia usługi Active Directory subskrypcji przed dodaniem Cię do roli administratora globalnego subskrypcji, zostaniesz dodany jako gość. W takiej sytuacji na stronie Dodawanie konta usługi Automation zostanie `You do not have permissions to create…` **wyświetlony komunikat ostrzegawczy.**

Aby sprawdzić, czy sytuacja tworząca komunikat o błędzie została usunięta:

1. W okienku Azure Active Directory na stronie Azure Portal wybierz **pozycję Użytkownicy i grupy.**
2. Wybierz **pozycję Wszyscy użytkownicy.**
3. Wybierz swoją nazwę, a następnie wybierz pozycję **Profil.**
4. Upewnij się, że wartość atrybutu **Typ użytkownika** w profilu użytkownika nie jest ustawiona na wartość **Gość.**

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Kontrola dostępu oparta na rolach jest dostępna w usłudze Azure Resource Manager na potrzeby udzielania dozwolonych akcji kontu użytkownika usługi Azure AD i kontu Uruchom jako oraz uwierzytelniania jednostki usługi. Dalsze informacje pomagające w tworzeniu modelu zarządzania uprawnieniami w usłudze Automation można znaleźć w artykule [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).

Jeśli masz ścisłe mechanizmy kontroli zabezpieczeń przypisywania uprawnień w grupach zasobów, musisz przypisać członkostwo konta Uruchom jako do roli **Współautor** w grupie zasobów.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Uwierzytelnianie runbook za pomocą hybrydowego procesu roboczego runbook

W przypadku zasobów Runbook uruchomionych w hybrydowym procesach roboczych runbook w centrum danych lub w usługach obliczeniowych w innych środowiskach chmury, takich jak AWS, nie można użyć tej samej metody, która jest zwykle używana do uwierzytelniania w zasobach platformy Azure przy użyciu tych samych zasobów. Jest to spowodowane faktem, że te zasoby działają poza platformą Azure i dlatego wymagają własnych poświadczeń zabezpieczeń zdefiniowanych w usłudze Automation do uwierzytelniania w zasobach, z których korzystają lokalnie. Aby uzyskać więcej informacji na temat uwierzytelniania runbook za pomocą procesów roboczych runbook, zobacz Runbook on a Hybrid Runbook Worker (Uruchamianie [runbook w hybrydowym procesach roboczych runbook).](automation-hrw-run-runbooks.md)

W przypadku zasobów Runbook, które używają hybrydowych pracowników [runbook](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) na maszyn wirtualnych platformy Azure, do uwierzytelniania w zasobach platformy Azure można użyć uwierzytelniania za pomocą tożsamości zarządzanych zamiast kont Uruchom jako.

## <a name="next-steps"></a>Następne kroki

* Aby utworzyć konto usługi Automation na Azure Portal, zobacz [Tworzenie autonomicznego Azure Automation usługi](automation-create-standalone-account.md).
* Jeśli wolisz utworzyć konto przy użyciu szablonu, zobacz Create an Automation account using an Azure Resource Manager template (Tworzenie konta usługi Automation przy [użyciu Azure Resource Manager szablonu).](quickstart-create-automation-account-template.md)
* Aby uzyskać informacje na temat uwierzytelniania Amazon Web Services, zobacz [Authenticate runbook with Amazon Web Services (Uwierzytelnianie Amazon Web Services).](automation-config-aws-account.md)
* Listę usług platformy Azure, które obsługują funkcję tożsamości zarządzanych dla zasobów platformy Azure, można znaleźć w temacie [Services that support managed identities for Azure resources (Usługi, które obsługują tożsamości zarządzane dla platformy Azure)](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).
