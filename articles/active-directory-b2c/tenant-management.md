---
title: Zarządzanie Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: Dowiedz się, jak zarządzać dzierżawą Azure Active Directory B2C dzierżawy. Dowiedz się, które funkcje usługi Azure AD są obsługiwane w usłudze Azure AD B2C, jak zarządzać zasobami przy użyciu ról administratora oraz jak dodawać konta służbowe i użytkowników-gości do Azure AD B2C dzierżawy.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3ea17a4f6dc2fb5134c6ceb1ae37d25e0881365
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715519"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Zarządzanie dzierżawą Azure Active Directory B2C dzierżawy

W Azure Active Directory B2C (Azure AD B2C) dzierżawa reprezentuje katalog użytkowników indywidualnych. Każda Azure AD B2C dzierżawy jest odrębna i oddzielona od innych Azure AD B2C dzierżawy. Dzierżawa Azure AD B2C jest inna niż Azure Active Directory dzierżawy, która być może już istnieje. Z tego artykułu dowiesz się, jak zarządzać dzierżawą Azure AD B2C dzierżawy.

## <a name="supported-azure-ad-features"></a>Obsługiwane funkcje usługi Azure AD

Azure AD B2C korzysta z platformy Usługi Azure AD. Następujące funkcje usługi Azure AD mogą być używane w Azure AD B2C dzierżawie.

|Cecha  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Grupy](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Grupy mogą służyć do zarządzania kontami administracyjnymi i kontami użytkowników.| Grupy mogą służyć do zarządzania kontami administracyjnymi. [Konta konsumentów](user-overview.md#consumer-user) nie obsługują grup. |
| [Zapraszanie External Identities gości](../active-directory//external-identities/add-users-administrator.md)| Możesz zaprosić użytkowników-gości i skonfigurować External Identities, takie jak federacja i logowanie przy użyciu kont Facebook i Google. | Jako gościa dzierżawy usługi Azure AD możesz zaprosić tylko użytkownika usługi konto Microsoft lub użytkownika usługi Azure AD do uzyskiwania dostępu do aplikacji lub zarządzania dzierżawami. W [przypadku kont](user-overview.md#consumer-user)konsumentów przepływy użytkowników Azure AD B2C zasady niestandardowe są Azure AD B2C do zarządzania użytkownikami oraz do rejestracji lub logowania się u zewnętrznych dostawców tożsamości, takich jak Google lub Facebook. |
| [Role i administratorzy](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| W pełni obsługiwane w przypadku kont administracyjnych i kont użytkowników. | Role nie są obsługiwane w przypadku [kont użytkowników.](user-overview.md#consumer-user) Konta konsumentów nie mają dostępu do żadnych zasobów platformy Azure.|
| [Niestandardowe nazwy domen](../active-directory/roles/permissions-reference.md#) |  Domen niestandardowych usługi Azure AD można używać tylko dla kont administracyjnych. | [Konta konsumentów](user-overview.md#consumer-user) mogą logować się przy użyciu nazwy użytkownika, numeru telefonu lub dowolnego adresu e-mail. W adresach [URL przekierowania można](custom-domain.md) używać domen niestandardowych.|
| [Dostęp warunkowy](../active-directory/roles/permissions-reference.md#) | W pełni obsługiwane w przypadku kont administracyjnych i kont użytkowników. | Podzbiór funkcji dostępu warunkowego usługi Azure AD jest obsługiwany w przypadku kont [konsumentów.](user-overview.md#consumer-user) Informacje na temat konfigurowania Azure AD B2C [domeny niestandardowej.](conditional-access-user-flow.md)|

## <a name="other-azure-resources-in-your-tenant"></a>Inne zasoby platformy Azure w dzierżawie

W dzierżawie Azure AD B2C nie można aprowizowania innych zasobów platformy Azure, takich jak maszyny wirtualne, aplikacje internetowe platformy Azure lub funkcje platformy Azure. Te zasoby należy utworzyć w dzierżawie usługi Azure AD.

## <a name="azure-ad-b2c-accounts-overview"></a>omówienie Azure AD B2C kont

Następujące typy kont można utworzyć w dzierżawie Azure AD B2C dzierżawy:

W dzierżawie Azure AD B2C istnieje kilka typów kont, które można utworzyć zgodnie z opisem w artykule Omówienie kont użytkowników w [Azure Active Directory B2C](user-overview.md) usługi.

- **Konto służbowe** — konto służbowe może uzyskać dostęp do zasobów w dzierżawie, a z rolą administratora może zarządzać dzierżawami.
- **Konto gościa** — konto gościa może być tylko kontem konto Microsoft lub Azure Active Directory, który może służyć do uzyskiwania dostępu do aplikacji lub zarządzania dzierżawami.
- **Konto konsumenta** — konto użytkownika jest używane przez użytkownika aplikacji zarejestrowanych w Azure AD B2C.

Aby uzyskać szczegółowe informacje o tych typach kont, [zobacz Overview of user accounts in Azure Active Directory B2C (Omówienie kont użytkowników w Azure Active Directory B2C](user-overview.md). Każdy użytkownik przypisany do zarządzania dzierżawą usługi Azure AD B2C musi mieć konto użytkownika usługi Azure AD, aby mieć dostęp do usług związanych z platformą Azure. Takiego użytkownika można dodać, tworząc konto [(konto](#add-an-administrator-work-account) służbowe) w dzierżawie [](#invite-an-administrator-guest-account) usługi Azure AD B2C lub zapraszając go do dzierżawy Azure AD B2C jako użytkownika-gościa.

## <a name="use-roles-to-control-resource-access"></a>Kontrolowanie dostępu do zasobów za pomocą ról

Podczas planowania strategii kontroli dostępu najlepiej jest przypisać użytkownikom najmniejszą rolę uprzywilejowana wymaganą do uzyskiwania dostępu do zasobów. W poniższej tabeli opisano podstawowe zasoby w dzierżawie Azure AD B2C i najbardziej odpowiednie role administracyjne dla użytkowników, którzy nimi zarządzają.

|Zasób  |Opis  |Rola  |
|---------|---------|---------|
|[Rejestracje aplikacji](tutorial-register-applications.md) | Twórz wszystkie aspekty rejestracji aplikacji internetowych, mobilnych i natywnych oraz zarządzaj nimi w ramach Azure AD B2C.|[Administrator aplikacji](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Dostawcy tożsamości](add-identity-provider.md)| Skonfiguruj [lokalnego dostawcę tożsamości i](identity-provider-local.md) zewnętrznych dostawców tożsamości społecznościowych lub firmowych. | [Administrator zewnętrznego dostawcy tożsamości](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[Łączniki interfejsu API](add-api-connector.md)| Integruj przepływy użytkownika z internetowymi interfejsami API, aby dostosować środowisko użytkownika i zintegrować je z systemami zewnętrznymi.|[Administrator atrybutów przepływu użytkownika zewnętrznego identyfikatora](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Znakowanie firmowe](customize-ui.md#configure-company-branding)| Dostosowywanie stron przepływu użytkownika.| [Administrator globalny](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Atrybuty użytkownika](user-flow-custom-attributes.md)| Dodawanie lub usuwanie atrybutów niestandardowych dostępnych dla wszystkich przepływów użytkownika.| [Administrator atrybutów przepływu użytkownika zewnętrznego identyfikatora](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Zarządzanie użytkownikami| Zarządzanie [kontami konsumentów](manage-users-portal.md) i kontami administracyjnymi zgodnie z opisem w tym artykule.| [Administrator użytkowników](../active-directory/roles/permissions-reference.md#user-administrator)|
|Role i administratorzy| Zarządzanie przypisaniami ról w Azure AD B2C katalogu. Tworzenie grup, które można przypisać do ról Azure AD B2C zarządzanie nimi. |[Administrator globalny,](../active-directory/roles/permissions-reference.md#global-administrator) [administrator ról uprzywilejowanych](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Przepływy użytkownika](user-flow-overview.md)|Szybka konfiguracja i włączanie typowych zadań związanych z tożsamościami, takich jak rejestracja, logowanie i edytowanie profilu.| [Administrator atrybutów przepływu użytkownika zewnętrznego identyfikatora](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Zasady niestandardowe](user-flow-overview.md)| Tworzenie, odczytywanie, aktualizowanie i usuwanie wszystkich zasad niestandardowych w Azure AD B2C.| [Administrator zasad IEF B2C](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Klucze zasad](policy-keys-overview.md)|Dodawanie kluczy szyfrowania do podpisywania i sprawdzania poprawności tokenów, wpisów tajnych klienta, certyfikatów i haseł używanych w zasadach niestandardowych oraz zarządzanie nimi.|[Administrator zestawu kluczy IEF B2C](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>Dodawanie administratora (konto służbowe)

Aby utworzyć nowe konto administracyjne, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) z uprawnieniami administratora globalnego lub administratora ról uprzywilejowanych.
1. Wybierz filtr **Katalog i subskrypcja** w górnym menu, a następnie wybierz katalog, który zawiera Azure AD B2C dzierżawy.
1. W **obszarze Usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Możesz też użyć pola wyszukiwania, aby znaleźć i wybrać **Azure AD B2C**.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik**.
1. Na stronie **Użytkownik** wprowadź informacje dla tego użytkownika:

   - **Nazwa**. Wymagane. Imię i nazwisko nowego użytkownika. Na przykład *Mary Parker*.
   - **Nazwa użytkownika**. Wymagane. Nazwa użytkownika nowego użytkownika. Na przykład `mary@contoso.com`.
     Część domeny nazwy użytkownika musi używać początkowej domyślnej nazwy domeny , *\<yourdomainname> .onmicrosoft.com*.
   - **Grupy**. Opcjonalnie możesz dodać użytkownika do co najmniej jednej istniejącej grupy. Można również dodać użytkownika do grup w późniejszym czasie. 
   - **Rola katalogu:** jeśli potrzebujesz uprawnień administracyjnych usługi Azure AD dla użytkownika, możesz dodać je do roli usługi Azure AD. Możesz przypisać użytkownika do roli administrator globalny lub co najmniej jednej ograniczonej roli administratora w usłudze Azure AD. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Używanie ról do kontrolowania dostępu do zasobów.](#use-roles-to-control-resource-access)
   - **Informacje o** zadaniu: tutaj możesz dodać więcej informacji o użytkowniku lub zrobić to później. 

1. Skopiuj automatycznie wygenerowane hasło podane w **polu** Hasło. Musisz podać to hasło użytkownikowi, aby zalogował się po raz pierwszy.
1. Wybierz przycisk **Utwórz**.

Użytkownik zostanie utworzony i dodany do dzierżawy Azure AD B2C dzierżawy. Preferowane jest, aby mieć co najmniej jedno konto służbowe natywne dla dzierżawy Azure AD B2C przypisaną rolę administratora globalnego. To konto można traktować jako konto typu "break-glass".

## <a name="invite-an-administrator-guest-account"></a>Zapraszanie administratora (konto gościa)

Możesz również zaprosić nowego użytkownika-gościa do zarządzania dzierżawą. Konto gościa jest preferowaną opcją, gdy organizacja ma również usługę Azure AD, ponieważ cykl życia tej tożsamości można zarządzać zewnętrznie. 

Aby zaprosić użytkownika, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) uprawnieniami administratora globalnego lub administratora ról uprzywilejowanych.
1. Wybierz filtr **Katalog i subskrypcja** w górnym menu, a następnie wybierz katalog zawierający Azure AD B2C dzierżawy.
1. W **obszarze Usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Możesz też użyć pola wyszukiwania, aby znaleźć i wybrać **Azure AD B2C**.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
1. Wybierz **pozycję Nowe konto gościa.**
1. Na stronie **Użytkownik** wprowadź informacje dotyczące tego użytkownika:

   - **Nazwa**. Wymagane. Imię i nazwisko nowego użytkownika. Na przykład *Mary Parker*.
   - **Adres e-mail**. Wymagane. Adres e-mail użytkownika, który chcesz zaprosić. Na przykład `mary@contoso.com`.   
   - **Wiadomość osobista:** dodasz osobistą wiadomość, która zostanie uwzględniona w wiadomości e-mail z zaproszeniem.
   - **Grupy**. Opcjonalnie możesz dodać użytkownika do co najmniej jednej istniejącej grupy. Można również dodać użytkownika do grup w późniejszym czasie.
   - **Rola katalogu:** jeśli potrzebujesz uprawnień administracyjnych usługi Azure AD dla użytkownika, możesz dodać je do roli usługi Azure AD. Możesz przypisać użytkownika jako rolę administrator globalny lub co najmniej jedną ograniczoną rolę administratora w usłudze Azure AD. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Używanie ról do kontrolowania dostępu do zasobów.](#use-roles-to-control-resource-access)
   - **Informacje o** zadaniu: tutaj możesz dodać więcej informacji o użytkowniku lub zrobić to później.

1. Wybierz przycisk **Utwórz**.

Wiadomość e-mail z zaproszeniem jest wysyłana do użytkownika. Użytkownik musi zaakceptować zaproszenie, aby móc się zalogować.

### <a name="resend-the-invitation-email"></a>Wyślij ponownie wiadomość e-mail z zaproszeniem

Jeśli gość nie otrzymał wiadomości e-mail z zaproszeniem lub zaproszenie wygasło, możesz ponownie wysłać zaproszenie. Zamiast wiadomości e-mail z zaproszeniem możesz podać gościowi bezpośredni link do zaakceptowania zaproszenia. Aby ponownie wysłać zaproszenie i uzyskać bezpośredni link:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **Katalog i subskrypcja** w górnym menu, a następnie wybierz katalog, który zawiera Azure AD B2C dzierżawy.
1. W **obszarze Usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Możesz też użyć pola wyszukiwania, aby znaleźć i **wybrać Azure AD B2C**.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
1. Wyszukaj i wybierz użytkownika, do którego chcesz ponownie wysłać zaproszenie.
1. W **jęz. | Na stronie** profilu w obszarze **Tożsamość** wybierz **pozycję (Zarządzaj).**
    
    ![Zrzut ekranu przedstawia sposób ponownego rozsyłania wiadomości e-mail z zaproszeniem do konta gościa.](./media/tenant-management/guest-account-resend-invite.png)

1. W **przypadku opcji Wyślij ponownie zaproszenie?** wybierz pozycję **Tak.** Gdy **pojawi się komunikat Czy na pewno chcesz ponownie wysłać zaproszenie?** wybierz pozycję **Tak.**
1. Azure AD B2C wysyła zaproszenie. Możesz również skopiować adres URL zaproszenia i podać go bezpośrednio gościowi.
    
    ![Zrzut ekranu przedstawia sposób uzyskania adresu URL zaproszenia.](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

Rolę można przypisać podczas tworzenia [użytkownika lub](#add-an-administrator-work-account) [zapraszania użytkownika-gościa.](#invite-an-administrator-guest-account) Możesz dodać rolę, zmienić ją lub usunąć dla użytkownika:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) uprawnieniami administratora globalnego lub administratora ról uprzywilejowanych.
1. Wybierz filtr **Katalog i subskrypcja** w górnym menu, a następnie wybierz katalog zawierający Azure AD B2C dzierżawy.
1. W **obszarze Usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Możesz też użyć pola wyszukiwania, aby znaleźć i wybrać **Azure AD B2C**.
1. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
1. Wybierz użytkownika, dla którego chcesz zmienić role. Następnie wybierz **pozycję Przypisane role.**
1. Wybierz **pozycję Dodaj przypisania,** wybierz rolę do przypisania (na przykład administrator aplikacji ), *a* następnie wybierz pozycję **Dodaj**.

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

Jeśli musisz usunąć przypisanie roli od użytkownika, wykonaj następujące kroki:

1. Wybierz **Azure AD B2C**, wybierz **pozycję Użytkownicy,** a następnie wyszukaj i wybierz użytkownika.
1. Wybierz **pozycję Przypisane role.** Wybierz rolę, którą chcesz usunąć, na przykład *administrator aplikacji*, a następnie wybierz **pozycję Usuń przypisanie.**

## <a name="review-administrator-account-role-assignments"></a>Przeglądanie przypisań ról konta administratora

W ramach procesu inspekcji zwykle sprawdza się, którzy użytkownicy są przypisani do określonych ról w Azure AD B2C katalogu. Aby wykonać inspekcję użytkowników, którym obecnie przypisano role uprzywilejowane, należy wykonać poniższe kroki.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) z uprawnieniami administratora globalnego lub administratora ról uprzywilejowanych.
1. Wybierz filtr **Katalog i subskrypcja** w górnym menu, a następnie wybierz katalog, który zawiera Azure AD B2C dzierżawy.
1. W **obszarze Usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Możesz też użyć pola wyszukiwania, aby znaleźć i wybrać **Azure AD B2C**.
1. W **obszarze Zarządzanie** wybierz pozycję Role i **administratorzy.**
1. Wybierz rolę, taką jak **administrator globalny**. Rola **| Strona Przypisania** zawiera listę użytkowników z tej roli.

## <a name="delete-an-administrator-account"></a>Usuwanie konta administratora

Aby usunąć istniejącego użytkownika, musisz mieć przypisanie *administrator globalny* roli. Administratorzy globalni mogą usuwać wszystkich użytkowników, w tym innych administratorów. *Administratorzy* użytkowników mogą usuwać użytkowników niebędących administratorami.

1. W katalogu Azure AD B2C wybierz pozycję **Użytkownicy,** a następnie wybierz użytkownika, którego chcesz usunąć.
1. Wybierz **pozycję Usuń,** a następnie pozycję **Tak,** aby potwierdzić usunięcie.

Użytkownik zostanie usunięty i nie będzie już wyświetlany na **stronie Użytkownicy — wszyscy użytkownicy.** Użytkownik może być widoczny na stronie **Usunięci użytkownicy** przez następne 30 dni i może zostać przywrócony w tym czasie. Aby uzyskać więcej informacji na temat przywracania użytkownika, zobacz Przywracanie lub usuwanie [ostatnio usuniętego użytkownika przy użyciu Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="protect-administrative-accounts"></a>Ochrona kont administracyjnych

W celu większej liczby zabezpieczeń zaleca się ochronę wszystkich kont administratorów przy użyciu uwierzytelniania wieloskładnikowego (MFA). Uwierzytelniania wieloskładnikowego to proces weryfikacji tożsamości podczas logowania, który monituje użytkownika o bardziej postać identyfikacji, taką jak kod weryfikacyjny na urządzeniu przenośnym lub żądanie w Microsoft Authenticator aplikacji.

![Metody uwierzytelniania w użyciu na zrzucie ekranu logowania](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

Możesz włączyć wartości [domyślne zabezpieczeń usługi Azure AD,](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) aby wymusić na wszystkich kontach administracyjnych korzystanie z uwierzytelniania wieloskładnikowego.



## <a name="next-steps"></a>Następne kroki

- [Tworzenie dzierżawy usługi Azure Active Directory B2C w witrynie Azure Portal](tutorial-create-tenant.md)

