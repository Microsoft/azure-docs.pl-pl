---
title: Omówienie uwierzytelniania konta Azure Automation
description: Ten artykuł zawiera omówienie uwierzytelniania konta Azure Automation.
keywords: zabezpieczenia usługi automation, bezpieczna usługa automation, uwierzytelnianie usługi automation
services: automation
ms.subservice: process-automation
ms.date: 02/26/2021
ms.topic: conceptual
ms.openlocfilehash: c559a81b17b92f48b2d51b7c2d26325d6a1b1cca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708904"
---
# <a name="automation-account-authentication-overview"></a>Omówienie uwierzytelniania konta usługi Automation

Usługa Azure Automation pozwala na zautomatyzowanie zadań w odniesieniu do zasobów platformy Azure, a także zasobów lokalnych oraz pochodzących od innych dostawców chmury, takich jak usługa Amazon Web Services (AWS). Za pomocą elementów Runbook można zautomatyzować zadania lub hybrydowy proces roboczy elementu Runbook, jeśli masz procesy biznesowe lub operacyjne do zarządzania poza platformą Azure. Praca w jednym z tych środowisk wymaga uprawnień do bezpiecznego dostępu do zasobów z wymaganymi minimalnymi prawami.

W tym artykule opisano scenariusze uwierzytelniania obsługiwane przez Azure Automation i zawarto informacje na temat rozpoczynania pracy w oparciu o środowisko lub środowiska, którymi należy zarządzać.

## <a name="automation-account"></a>Konto usługi Automation

Kiedy uruchamiasz usługę Azure Automation po raz pierwszy, musisz utworzyć co najmniej jedno konto usługi Automation. Konta usługi Automation umożliwiają izolowanie zasobów usługi Automation, elementów Runbook, zasobów i konfiguracji z zasobów innych kont. Kont usługi Automation można użyć do oddzielenia zasobów do oddzielnych środowisk logicznych lub delegowanych obowiązków. Na przykład jednego konta można użyć dla środowiska rozwojowego, innego dla produkcyjnego, a jeszcze innego dla lokalnego. Można też przypisać konto usługi Automation do zarządzania aktualizacjami systemu operacyjnego na wszystkich maszynach przy użyciu [Update Management](update-management/overview.md). 

Konto usługi Azure Automation różni się od konta Microsoft lub kont utworzonych w ramach subskrypcji platformy Azure. Aby zapoznać się z wprowadzeniem do tworzenia konta usługi Automation, zobacz [Tworzenie konta usługi Automation](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Zasoby automatyzacji

Zasoby usługi Automation dla każdego konta usługi Automation są skojarzone z pojedynczym regionem świadczenia usługi Azure, ale konto może zarządzać wszystkimi zasobami w ramach subskrypcji platformy Azure. Głównym powodem tworzenia kont usługi Automation w różnych regionach jest to, że zasady, które wymagają, aby dane i zasoby były izolowane w określonym regionie.

Wszystkie zadania utworzone przy użyciu Azure Resource Manager i poleceń cmdlet programu PowerShell w programie Azure Automation muszą uwierzytelniać się na platformie Azure przy użyciu uwierzytelniania opartego na poświadczeniach tożsamości organizacji w usłudze Azure Active Directory (Azure AD).

## <a name="run-as-accounts"></a>Konta Uruchom jako

Konta Uruchom jako w Azure Automation umożliwiają uwierzytelnianie w celu zarządzania zasobami Azure Resource Manager lub zasobów wdrożonych w klasycznym modelu wdrażania. W Azure Automation istnieją dwa typy kont Uruchom jako:

* Konto Uruchom jako platformy Azure: umożliwia zarządzanie zasobami platformy Azure na podstawie Azure Resource Manager wdrażania i zarządzania na platformie Azure.
* Klasyczne konto Uruchom jako platformy Azure: umożliwia zarządzanie klasycznymi zasobami platformy Azure na podstawie klasycznego modelu wdrażania.

Aby dowiedzieć się więcej na temat Azure Resource Manager i klasycznych modeli wdrażania, zobacz [wdrażanie Menedżer zasobów i klasycznych](../azure-resource-manager/management/deployment-models.md).

>[!NOTE]
>Subskrypcje dostawcy rozwiązań w chmurze (CSP) platformy Azure obsługują tylko model Azure Resource Manager. Usługi inne niż Azure Resource Manager nie są dostępne w programie. W przypadku korzystania z subskrypcji programu CSP klasyczne konto Uruchom jako platformy Azure nie jest tworzone, ale zostało utworzone konto Uruchom jako platformy Azure. Aby dowiedzieć się więcej o subskrypcjach dostawcy usług kryptograficznych, zobacz [dostępne usługi w subskrypcjach programu CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Podczas tworzenia konta usługi Automation konto Uruchom jako jest domyślnie tworzone w tym samym czasie. Jeśli nie utworzysz go wraz z kontem usługi Automation, można je utworzyć indywidualnie w późniejszym czasie. Klasyczne konto Uruchom jako platformy Azure jest opcjonalne i jest tworzone oddzielnie, jeśli konieczne jest zarządzanie zasobami klasycznymi.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>Konto Uruchom jako

Podczas tworzenia konta Uruchom jako wykonywane są następujące zadania:

* Tworzy aplikację usługi Azure AD z certyfikatem z podpisem własnym, tworzy konto nazwy głównej usługi dla aplikacji w usłudze Azure AD i przypisuje rolę [współautor](../role-based-access-control/built-in-roles.md#contributor) dla konta w bieżącej subskrypcji. Można zmienić ustawienie certyfikatu na [Reader](../role-based-access-control/built-in-roles.md#reader) lub dowolną inną rolę. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).

* Tworzy zasób certyfikatu usługi Automation o nazwie `AzureRunAsCertificate` w określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu, którego używa aplikacja usługi Azure AD.

* Tworzy zasób połączenia usługi Automation o nazwie `AzureRunAsConnection` w określonym koncie usługi Automation. Zasób połączenia zawiera identyfikator aplikacji, identyfikator dzierżawy, Identyfikator subskrypcji i odcisk palca certyfikatu.

### <a name="azure-classic-run-as-account"></a>Klasyczne konto Uruchom jako platformy Azure

Podczas tworzenia klasycznego konta Uruchom jako platformy Azure wykonywane są następujące zadania:

> [!NOTE]
> Aby utworzyć lub odnowić ten typ konta Uruchom jako, musisz być współadministratorem w ramach subskrypcji.

* Tworzy certyfikat zarządzania w subskrypcji.

* Tworzy zasób certyfikatu usługi Automation o nazwie `AzureClassicRunAsCertificate` w określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez certyfikat zarządzania.

* Tworzy zasób połączenia usługi Automation o nazwie `AzureClassicRunAsConnection` w określonym koncie usługi Automation. Zasób połączenia zawiera nazwę subskrypcji, Identyfikator subskrypcji i nazwę zasobu certyfikatu.

## <a name="service-principal-for-run-as-account"></a>Nazwa główna usługi dla konta Uruchom jako

Nazwa główna usługi dla konta Uruchom jako nie ma uprawnień do domyślnego odczytywania usługi Azure AD. Jeśli chcesz dodać uprawnienia do odczytu lub zarządzania usługą Azure AD, musisz udzielić uprawnień do jednostki usługi w obszarze **uprawnienia interfejsu API**. Aby dowiedzieć się więcej, zobacz [Dodawanie uprawnień w celu uzyskania dostępu do internetowego interfejsu API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Uprawnienia konta Uruchom jako

Ta sekcja definiuje uprawnienia zarówno do zwykłych kont Uruchom jako, jak i klasycznych kont Uruchom jako.

* Aby utworzyć lub zaktualizować konto Uruchom jako, administrator aplikacji w Azure Active Directory i właściciel subskrypcji może ukończyć wszystkie zadania.
* Aby skonfigurować lub odnowić klasyczne konta Uruchom jako, musisz mieć rolę współadministrator na poziomie subskrypcji. Aby dowiedzieć się więcej o uprawnieniach klasycznych subskrypcji, zobacz [klasycznej subskrypcji platformy Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

W przypadku rozdzielenia obowiązków w poniższej tabeli przedstawiono listę zadań, równoważne polecenie cmdlet i potrzebne uprawnienia:

|Zadanie|Polecenie cmdlet  |Minimalne uprawnienia  |Ustawianie uprawnień|
|---|---------|---------|---|
|Tworzenie aplikacji usługi Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Rola dewelopera aplikacji<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Rejestracja aplikacji w domu > usługi Azure AD > |
|Dodaj poświadczenie do aplikacji.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Administrator aplikacji lub Administrator globalny<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Rejestracja aplikacji w domu > usługi Azure AD >|
|Tworzenie i pobieranie jednostki usługi Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Administrator aplikacji lub Administrator globalny<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Rejestracja aplikacji w domu > usługi Azure AD >|
|Przypisz lub uzyskaj rolę platformy Azure dla określonego podmiotu zabezpieczeń|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrator lub właściciel dostępu użytkowników albo mają następujące uprawnienia:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Subskrypcja](../role-based-access-control/role-assignments-portal.md)</br>Subskrypcje > domowej > \<subscription name\> — Access Control (IAM)|
|Utwórz lub Usuń certyfikat usługi Automation|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Współautor w grupie zasobów         |Grupa zasobów konta usługi Automation|
|Tworzenie lub usuwanie połączenia automatyzacji|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Współautor w grupie zasobów |Grupa zasobów konta usługi Automation|

<sup>1</sup> użytkownicy inni niż administratorzy w dzierżawie usługi Azure AD mogą [rejestrować aplikacje usługi AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) , jeśli opcja Użytkownicy dzierżawy usługi Azure AD **może rejestrować aplikacje** na stronie **Ustawienia użytkownika** jest ustawiona na **wartość tak**. Jeśli ustawienie rejestracji aplikacji ma wartość **nie**, użytkownik wykonujący tę akcję musi być zdefiniowany w tej tabeli.

Jeśli nie jesteś członkiem wystąpienia Active Directory subskrypcji przed dodaniem do roli administratora globalnego subskrypcji, zostanie on dodany jako gość. W takiej sytuacji pojawi się `You do not have permissions to create…` ostrzeżenie na stronie **Dodawanie konta usługi Automation** .

Aby sprawdzić, czy problem z wygenerowaniem komunikatu o błędzie został usunięty:

1. W okienku Azure Active Directory w Azure Portal wybierz pozycję **Użytkownicy i grupy**.
2. Wybierz pozycję **Wszyscy użytkownicy**.
3. Wybierz swoją nazwę, a następnie wybierz pozycję **profil**.
4. Upewnij się, że wartość atrybutu **typu użytkownika** w profilu użytkownika nie jest ustawiona na **gość**.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Kontrola dostępu oparta na rolach jest dostępna w Azure Resource Manager, aby udzielić dozwolonych akcji do konta użytkownika usługi Azure AD i konta Uruchom jako, a także uwierzytelnić jednostkę usługi. Dalsze informacje pomagające w tworzeniu modelu zarządzania uprawnieniami w usłudze Automation można znaleźć w artykule [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).

Jeśli masz ścisłe kontrole zabezpieczeń dla przypisania uprawnień w grupach zasobów, musisz przypisać członkostwo konta Uruchom jako do roli **współautor** w grupie zasobów.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Uwierzytelnianie elementu Runbook za pomocą hybrydowego procesu roboczego elementu Runbook

Elementy Runbook działające w ramach hybrydowego procesu roboczego elementu Runbook w centrum danych lub w przypadku usług obliczeniowych w innych środowiskach chmurowych, takich jak AWS, nie mogą korzystać z tej samej metody, która jest zwykle używana w przypadku elementów Runbook uwierzytelnianych w zasobach platformy Azure. Jest to spowodowane faktem, że te zasoby działają poza platformą Azure i dlatego wymagają własnych poświadczeń zabezpieczeń zdefiniowanych w usłudze Automation do uwierzytelniania w zasobach, z których korzystają lokalnie. Aby uzyskać więcej informacji na temat uwierzytelniania elementów Runbook za pomocą procesów roboczych elementów Runbook, zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).

W przypadku elementów Runbook, które używają hybrydowych procesów roboczych elementów Runbook na maszynach wirtualnych platformy Azure, można użyć [uwierzytelniania elementu Runbook z tożsamościami zarządzanymi](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) zamiast kont Uruchom jako do uwierzytelniania w zasobach platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Aby utworzyć konto usługi Automation na podstawie Azure Portal, zobacz [Tworzenie autonomicznego konta Azure Automation](automation-create-standalone-account.md).
* Jeśli wolisz utworzyć konto przy użyciu szablonu, zobacz [Tworzenie konta usługi Automation przy użyciu szablonu Azure Resource Manager](quickstart-create-automation-account-template.md).
* Aby uzyskać informacje na temat uwierzytelniania przy użyciu Amazon Web Services, zobacz temat [uwierzytelnianie elementów Runbook przy użyciu Amazon Web Services](automation-config-aws-account.md).