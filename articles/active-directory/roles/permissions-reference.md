---
title: Opisy i uprawnienia ról usługi Azure AD — Azure Active Directory | Microsoft Docs
description: Rola administratora może dodawać użytkowników, przypisywać role administracyjne, resetować hasła użytkowników, zarządzać licencjami użytkowników lub zarządzać domenami.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 02/01/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c89bfab6c4914cee26726006e6928eeb14c0686
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538191"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Uprawnienia roli administratora w usłudze Azure Active Directory

Za pomocą Azure Active Directory (Azure AD) można wyznaczyć ograniczonych administratorów do zarządzania zadaniami tożsamości w rolach z niższymi uprawnieniami. Administratorzy mogą być przypisani do takich celów, jak dodawanie lub zmiana użytkowników, przypisywanie ról administracyjnych, resetowanie haseł użytkowników, zarządzanie licencjami użytkowników i zarządzanie nazwami domen. [Domyślne uprawnienia użytkownika](../fundamentals/users-default-permissions.md) można zmienić tylko w ustawieniach użytkownika w usłudze Azure AD.

## <a name="limit-use-of-global-administrator"></a>Ograniczanie użycia administratora globalnego

Użytkownicy przypisani do roli administratora globalnego mogą odczytywać i modyfikować każde ustawienie administracyjne w organizacji usługi Azure AD. Domyślnie, gdy użytkownik loguje się do usługi w chmurze firmy Microsoft, zostaje utworzona dzierżawa usługi Azure AD, a użytkownik jest członkiem roli Administratorzy globalni. Po dodaniu subskrypcji do istniejącej dzierżawy nie jest ona przypisana do roli administratora globalnego. Tylko Administratorzy globalni i Administratorzy ról uprzywilejowanych mogą delegować role administratorów. Aby zmniejszyć ryzyko dla Twojej firmy, zalecamy przypisanie tej roli do najmniejszej możliwej liczby osób w organizacji.

Najlepszym rozwiązaniem jest przypisanie tej roli do mniej niż pięciu osób w organizacji. Jeśli masz więcej niż pięć administratorów przypisanych do roli administratora globalnego w organizacji, Oto kilka sposobów na zmniejszenie jej użycia.

### <a name="find-the-role-you-need"></a>Znajdź potrzebną rolę

Jeśli frustrujące się, aby znaleźć rolę, której potrzebujesz, z listy wielu ról, usługa Azure AD może wyświetlać podzbiory ról na podstawie kategorii ról. Zapoznaj się z naszym nowym filtrem **typu** dla [ról i administratorów usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) , aby wyświetlić tylko role w wybranym typie.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Rola istnieje teraz, która nie istnieje po przypisaniu roli administratora globalnego

Istnieje możliwość, że rola lub role zostały dodane do usługi Azure AD, która zapewnia bardziej szczegółowe uprawnienia, które nie były opcją w przypadku podniesienia poziomu niektórych użytkowników do administratora globalnego. W miarę upływu czasu wprowadzamy dodatkowe role wykonujące zadania, które mogą wykonać tylko rola administratora globalnego. Są one widoczne w poniższych [dostępnych rolach](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Przypisywanie lub usuwanie ról administratorów

Aby dowiedzieć się, jak przypisać role administracyjne do użytkownika w Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratorów w Azure Active Directory](manage-roles-portal.md).

> [!Note]
> Jeśli masz licencję usługi Azure AD Premium P2 i jesteś już użytkownikiem Privileged Identity Management (PIM), wszystkie zadania związane z zarządzaniem rolami są wykonywane w ramach uprawnień do zarządzania tożsamościami, a nie w usłudze Azure AD.
>
> ![Role usługi Azure AD zarządzane w usłudze PIM dla użytkowników korzystających już z usług PIM i z licencją premium P2](./media/permissions-reference/pim-manages-roles-for-p2.png)

## <a name="available-roles"></a>Dostępne role

Dostępne są następujące role administratorów:

### <a name="application-administrator"></a>[Administrator aplikacji](#application-administrator-permissions)

Użytkownicy w tej roli mogą tworzyć wszystkie aspekty aplikacji przedsiębiorstwa, rejestracji aplikacji i ustawień serwera proxy aplikacji oraz zarządzać nimi. Należy pamiętać, że użytkownicy przypisani do tej roli nie są dodawani jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

Ta rola zapewnia również możliwość _wyrażania zgody_ na delegowane uprawnienia i uprawnienia aplikacji, z wyjątkiem uprawnień aplikacji w interfejsie API Microsoft Graph.

> [!IMPORTANT]
> Ten wyjątek oznacza, że nadal można wyrazić zgodę na uprawnienia do _innych_ aplikacji (na przykład aplikacje inne niż firmy Microsoft lub aplikacje, które zostały zarejestrowane), ale nie do uprawnień w usłudze Azure AD. Nadal możesz _poprosić_ o te uprawnienia w ramach rejestracji aplikacji, ale _przyznanie_ (czyli wyrażanie zgody na) te uprawnienia wymaga administratora usługi Azure AD. Oznacza to, że złośliwy użytkownik nie może łatwo podnieść poziomu uprawnień, na przykład przez utworzenie i zgodę na dostęp do aplikacji, która może zapisywać w całym katalogu i za pośrednictwem uprawnień tej aplikacji Podnieś poziom do administratora globalnego.
>
>Ta rola umożliwia zarządzanie poświadczeniami aplikacji. Użytkownicy przypisani do tej roli mogą dodawać poświadczenia do aplikacji i używać tych poświadczeń do personifikacji tożsamości aplikacji. Jeśli tożsamość aplikacji otrzymała dostęp do zasobu, na przykład możliwość tworzenia lub aktualizowania użytkowników lub innych obiektów, wówczas użytkownik przypisany do tej roli może wykonywać te akcje podczas personifikowania aplikacji. Ta możliwość personifikacji tożsamości aplikacji może być podniesieniem uprawnień przez użytkownika za pośrednictwem ich przypisań ról. Ważne jest, aby zrozumieć, że przypisanie użytkownika do roli administratora aplikacji daje im możliwość personifikacji tożsamości aplikacji.

### <a name="application-developer"></a>[Deweloper aplikacji](#application-developer-permissions)

Użytkownicy w tej roli mogą tworzyć rejestracje aplikacji, gdy ustawienie "użytkownicy mogą rejestrować aplikacje" ma wartość nie. Ta rola przyznaje także uprawnienia do wyrażania zgody w imieniu użytkownika, gdy ustawienie "użytkownicy mogą wyrazić zgodę na dostęp do danych firmowych w ich imieniu" jest ustawione na wartość nie. Użytkownicy przypisani do tej roli są dodawani jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

### <a name="authentication-administrator"></a>[Administrator uwierzytelniania](#authentication-administrator-permissions)

Użytkownicy z tą rolą mogą ustawiać lub resetować poświadczenia niebędące hasłami dla niektórych użytkowników i mogą aktualizować hasła dla wszystkich użytkowników. Administratorzy uwierzytelniania mogą wymagać od użytkowników, którzy nie są administratorami lub przypisani do niektórych ról do ponownego zarejestrowania istniejących poświadczeń niezwiązanych z hasłem (na przykład MFA lub FIDO), a także do odwoływania się do **zapamiętania usługi MFA na urządzeniu**, które monituje o usługę MFA przy następnym logowaniu. Czy administrator uwierzytelniania może zresetować hasło użytkownika, zależy od roli przypisanej do użytkownika. Aby uzyskać listę ról, dla których administrator uwierzytelniania może zresetować hasła dla programu, zobacz [uprawnienia do resetowania haseł](#password-reset-permissions).

Uprawnienie [administrator uwierzytelniania uprzywilejowanego](#privileged-authentication-administrator) ma uprawnienia do wymuszenia ponownej rejestracji i uwierzytelniania wieloskładnikowego dla wszystkich użytkowników.

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać poświadczenia dla osób, które mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej wewnątrz i na zewnątrz Azure Active Directory. Zmiana poświadczeń użytkownika może oznaczać, że założono, że tożsamość i uprawnienia tego użytkownika. Na przykład:
>
>* Rejestracja aplikacji i właściciele aplikacji przedsiębiorstwa, którzy mogą zarządzać poświadczeniami aplikacji, których są właścicielami. Te aplikacje mogą mieć uprawnienia uprzywilejowane w usłudze Azure AD, a w innym miejscu nie są przyznawane administratorom uwierzytelniania. Za pomocą tej ścieżki administrator uwierzytelniania może założyć tożsamość właściciela aplikacji, a następnie ponownie założyć tożsamość aplikacji uprzywilejowanej przez zaktualizowanie poświadczeń dla aplikacji.
>* Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej na platformie Azure.
>* Grupa zabezpieczeń i właściciele grup Microsoft 365, którzy mogą zarządzać członkostwem w grupie. Te grupy mogą udzielić dostępu do poufnych lub prywatnych informacji lub konfiguracji krytycznej w usłudze Azure AD i w innym miejscu.
>* Administratorzy w innych usługach poza usługą Azure AD, np. Exchange Online, Centrum zabezpieczeń i zgodności pakietu Office oraz systemy kadr.
>* Użytkownicy niebędący administratorami, w tym członkowie kierownictwa, prawnik prawny i pracownicy działu kadr, którzy mogą mieć dostęp do poufnych lub prywatnych informacji.

### <a name="attack-payload-author"></a>[Autor ładunku ataku](#attack-payload-author-permissions)

Użytkownicy w tej roli mogą tworzyć ładunki ataków, ale nie są faktycznie uruchamiane lub zaplanowali. Ładunki ataków są następnie dostępne dla wszystkich administratorów w dzierżawie, którzy mogą ich używać do tworzenia symulacji.

### <a name="attack-simulation-administrator"></a>[Administrator symulacji ataku](#attack-simulation-administrator-permissions)

Użytkownicy w tej roli mogą tworzyć i zarządzać wszystkimi aspektami tworzenia symulacji ataków, uruchamiać i planować symulację oraz przeglądać wyniki symulacji. Członkowie tej roli mają dostęp do wszystkich symulacji w dzierżawie.

### <a name="azure-devops-administrator"></a>[Administrator usługi Azure DevOps](#azure-devops-administrator-permissions)

Użytkownicy z tą rolą mogą zarządzać zasadami usługi Azure DevOps, aby ograniczyć Tworzenie nowej organizacji usługi Azure DevOps do zestawu konfigurowalnych użytkowników lub grup. Użytkownicy w tej roli mogą zarządzać tymi zasadami za pomocą dowolnej organizacji usługi Azure DevOps, która jest obsługiwana przez organizację usługi Azure AD firmy. Ta rola nie udziela żadnych innych uprawnień specyficznych dla usługi Azure DevOps (na przykład administratorów kolekcji projektów) w ramach żadnej organizacji usługi Azure DevOps objętej organizacją usługi Azure AD firmy.

Wszystkie zasady usługi Azure DevOps dla przedsiębiorstw mogą być zarządzane przez użytkowników w tej roli.

### <a name="azure-information-protection-administrator"></a>[Azure Information Protection administrator](#azure-information-protection-administrator-permissions)

Użytkownicy z tą rolą mają wszystkie uprawnienia w usłudze Azure Information Protection. Ta rola umożliwia konfigurowanie etykiet dla zasad Azure Information Protection, zarządzanie szablonami ochrony i aktywowanie ochrony. Ta rola nie udziela żadnych uprawnień w usłudze Identity Protection Center, Privileged Identity Management, monitor Microsoft 365 Service Health lub Office 365 Security & Centrum zgodności.

### <a name="b2c-ief-keyset-administrator"></a>[Administrator zestawu kluczy B2C IEF](#b2c-ief-keyset-administrator-permissions)

Użytkownik może tworzyć klucze zasad i wpisy tajne oraz zarządzać nimi na potrzeby szyfrowania tokenów, podpisów tokenów oraz szyfrowania i odszyfrowywania roszczeń. Przez dodanie nowych kluczy do istniejących kontenerów kluczy, ten ograniczony administrator może przerzucać wpisy tajne w razie potrzeby bez wpływu na istniejące aplikacje. Ten użytkownik może zobaczyć pełną zawartość tych kluczy tajnych i ich daty wygaśnięcia nawet po ich utworzeniu.

> [!IMPORTANT]
> Jest to poufna rola. Rolę administratora zestawu kluczy należy uważnie poddać inspekcji i przypisać ją do środowiska produkcyjnego.

### <a name="b2c-ief-policy-administrator"></a>[Administrator zasad B2C IEF](#b2c-ief-policy-administrator-permissions)

Użytkownicy w tej roli mogą tworzyć, odczytywać, aktualizować i usuwać wszystkie niestandardowe zasady w Azure AD B2C i dlatego mieć pełną kontrolę nad strukturą środowiska tożsamości w odpowiedniej organizacji Azure AD B2C. Edytując zasady, ten użytkownik może ustanowić bezpośrednią Federacji z zewnętrznymi dostawcami tożsamości, zmienić schemat katalogu, zmienić całą zawartość dostępną dla użytkownika (HTML, CSS, JavaScript), zmienić wymagania w celu ukończenia uwierzytelniania, utworzyć nowych użytkowników, wysłać dane użytkowników do systemów zewnętrznych, w tym pełne migracje i edytować wszystkie informacje o użytkowniku, w tym hasła i numery telefonów. Z drugiej strony ta rola nie może zmienić kluczy szyfrowania ani edytować wpisów tajnych używanych dla Federacji w organizacji.

> [!IMPORTANT]
> IEF zasad jest wysoce poufną rolą, która powinna być przypisana na bardzo ograniczonym poziomie dla organizacji w środowisku produkcyjnym. Działania tych użytkowników powinny być ściśle poddawane inspekcji, szczególnie w przypadku organizacji w środowisku produkcyjnym.

### <a name="billing-administrator"></a>[Administrator rozliczeń](#billing-administrator-permissions)

Dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.

### <a name="cloud-application-administrator"></a>[Administrator aplikacji w chmurze](#cloud-application-administrator-permissions)

Użytkownicy w tej roli mają takie same uprawnienia jak rola administratora aplikacji, z wyłączeniem możliwości zarządzania serwerem proxy aplikacji. Ta rola umożliwia tworzenie wszystkich aspektów aplikacji dla przedsiębiorstw i rejestracji aplikacji oraz zarządzanie nimi. Ta rola umożliwia również zgodę na uprawnienia delegowane i uprawnienia aplikacji z wyłączeniem Microsoft Graph i Azure AD Graph. Użytkownicy przypisani do tej roli nie są dodawani jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

> [!IMPORTANT]
> Ta rola umożliwia zarządzanie poświadczeniami aplikacji. Użytkownicy przypisani do tej roli mogą dodawać poświadczenia do aplikacji i używać tych poświadczeń do personifikacji tożsamości aplikacji. Jeśli tożsamość aplikacji otrzymała dostęp do zasobu, na przykład możliwość tworzenia lub aktualizowania użytkowników lub innych obiektów, wówczas użytkownik przypisany do tej roli może wykonywać te akcje podczas personifikowania aplikacji. Ta możliwość personifikacji tożsamości aplikacji może być podniesieniem uprawnień przez użytkownika za pośrednictwem ich przypisań ról. Ważne jest, aby zrozumieć, że przypisanie użytkownika do roli administratora aplikacji w chmurze daje im możliwość personifikacji tożsamości aplikacji.


### <a name="cloud-device-administrator"></a>[Administrator urządzenia w chmurze](#cloud-device-administrator-permissions)

Użytkownicy w tej roli mogą włączać, wyłączać i usuwać urządzenia w usłudze Azure AD oraz odczytywać klucze funkcji BitLocker systemu Windows 10 (jeśli istnieją) w Azure Portal. Rola nie udziela uprawnień do zarządzania wszystkimi innymi właściwościami na urządzeniu.

### <a name="compliance-administrator"></a>[Administrator zgodności](#compliance-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia do zarządzania funkcjami związanymi z zgodnością w centrum zgodności Microsoft 365, Microsoft 365 centrum administracyjne, Azure i Office 365 Security & Centrum zgodności. Osoby przydzielone mogą również zarządzać wszystkimi funkcjami w centrum administracyjnym programu Exchange i zespołami & centrami administracyjnymi Skype dla firm i tworzyć bilety pomocy technicznej dla platformy Azure i Microsoft 365. Więcej informacji można znaleźć w [tematach Microsoft 365 ról administratorów](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

W | Można wykonać
----- | ----------
[Microsoft 365 Centrum zgodności](https://protection.office.com) | Ochrona danych organizacji i zarządzanie nimi w ramach usług Microsoft 365 Services<br>Zarządzanie alertami zgodności
[Menedżer zgodności](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Śledź, przypisuj i Weryfikuj działania dotyczące zgodności z przepisami organizacji
[Centrum zgodności & zabezpieczeń pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie nadzorem danych<br>Wykonaj badanie prawne i dane<br>Zarządzaj żądaniem podmiotu danych<br><br>Ta rola ma takie same uprawnienia, jak [rola Administrator zgodności](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) w programie Office 365 zabezpieczenia & kontrola dostępu oparta na rolach.
[Intune](/intune/role-based-access-control) | Wyświetl wszystkie dane inspekcji usługi Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami<br>Może tworzyć i modyfikować zasady dotyczące plików i zezwalać na akcje ładu plików<br>Może wyświetlać wszystkie wbudowane raporty w obszarze Zarządzanie danymi

### <a name="compliance-data-administrator"></a>[Administrator danych zgodności](#compliance-data-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia do śledzenia danych w centrum Microsoft 365 zgodności, Microsoft 365 centrum administracyjnego i na platformie Azure. Użytkownicy mogą również śledzić dane zgodności w centrum administracyjnym programu Exchange, w Menedżerze zgodności i zespołach & centrum administracyjnym usługi Skype dla firm i tworzyć bilety pomocy technicznej dla platformy Azure i Microsoft 365. [Ta dokumentacja](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) zawiera szczegółowe informacje dotyczące różnic między administratorami zgodności i administratorami danych zgodności.

W | Można wykonać
----- | ----------
[Microsoft 365 Centrum zgodności](https://protection.office.com) | Monitorowanie zasad związanych z zgodnością w ramach usług Microsoft 365 Services<br>Zarządzanie alertami zgodności
[Menedżer zgodności](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Śledź, przypisuj i Weryfikuj działania dotyczące zgodności z przepisami organizacji
[Centrum zgodności & zabezpieczeń pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie nadzorem danych<br>Wykonaj badanie prawne i dane<br>Zarządzaj żądaniem podmiotu danych<br><br>Ta rola ma takie same uprawnienia jak w przypadku [roli administrator danych zgodności](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) w programie Office 365 zabezpieczenia & kontrola dostępu oparta na rolach.
[Intune](/intune/role-based-access-control) | Wyświetl wszystkie dane inspekcji usługi Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami<br>Może tworzyć i modyfikować zasady dotyczące plików i zezwalać na akcje ładu plików<br>Może wyświetlać wszystkie wbudowane raporty w obszarze Zarządzanie danymi

### <a name="conditional-access-administrator"></a>[Administrator dostępu warunkowego](#conditional-access-administrator-permissions)

Użytkownicy z tą rolą mają możliwość zarządzania ustawieniami dostępu warunkowego Azure Active Directory.

### <a name="customer-lockbox-access-approver"></a>[Osoba zatwierdzająca Skrytka klienta dostępu](#customer-lockbox-access-approver-permissions)

Zarządza [żądaniami skrytka klienta](/office365/admin/manage/customer-lockbox-requests) w organizacji. Odbierają one powiadomienia e-mail dla żądań Skrytka klienta i mogą zatwierdzać i odrzucać żądania z centrum administracyjnego Microsoft 365. Mogą również włączać lub wyłączać funkcję Skrytka klienta. Tylko Administratorzy globalni mogą resetować hasła osób przypisanych do tej roli.

### <a name="desktop-analytics-administrator"></a>[Administrator usługi Desktop Analytics](#desktop-analytics-administrator-permissions)


Użytkownicy w tej roli mogą zarządzać narzędziami do analizy pulpitu i dostosowywania pakietu Office &ymi usługami zasad. W przypadku usługi Desktop Analytics obejmuje to możliwość wyświetlania spisu zasobów, tworzenia planów wdrażania, wyświetlania stanu wdrożenia i kondycji. Ta rola umożliwia użytkownikom zarządzanie zasadami pakietu Office w przypadku dostosowywania pakietu Office & Policy Service.

### <a name="device-administrators"></a>[Administratorzy urządzeń](#device-administrators-permissions)

Ta rola jest dostępna do przypisania tylko jako dodatkowy administrator lokalny w [ustawieniach urządzenia](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Użytkownicy z tą rolą stają się administratorami maszyn lokalnych na wszystkich urządzeniach z systemem Windows 10, które są przyłączone do Azure Active Directory. Nie mają możliwości zarządzania obiektami obiektów w Azure Active Directory.

### <a name="directory-readers"></a>[Czytelnicy katalogów](#directory-readers-permissions)

Użytkownicy w tej roli mogą odczytywać podstawowe informacje o katalogu. Ta rola powinna być używana na potrzeby:

* Przyznanie określonego zestawu Gościom dostępu do odczytu zamiast udzielania go wszystkim użytkownikom-Gościom.
* Przyznanie konkretnego zestawu użytkownikom niebędącym administratorami dostępu do Azure Portal w przypadku wybrania opcji "Ogranicz dostęp do portalu usługi Azure AD tylko do administratorów" ma wartość "tak".
* Przyznanie podmiotom usługi dostępu do katalogu, w którym znajduje się katalog. Read. All nie jest opcją.

### <a name="directory-synchronization-accounts"></a>[Konta synchronizacji katalogów](#directory-synchronization-accounts-permissions)

Nie używaj. Ta rola jest automatycznie przypisana do usługi Azure AD Connect i nie jest przeznaczona do użycia ani nie jest obsługiwana w żadnym innym przypadku.

### <a name="directory-writers"></a>[Autorzy katalogów](#directory-writers-permissions)
Użytkownicy w tej roli mogą odczytywać i aktualizować podstawowe informacje o użytkownikach, grupach i nazwach głównych usług. Przypisz tę rolę tylko do aplikacji, które nie obsługują [struktury zgody](../develop/quickstart-register-app.md). Nie powinien być przypisany do żadnych użytkowników.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Administrator programu Dynamics 365/programu CRM](#crm-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w systemie Microsoft Dynamics 365 online, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji na temat [używania roli administratora usługi do zarządzania organizacją usługi Azure AD](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> W interfejsie API Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi Dynamics 365." Jest to "administrator systemu Dynamics 365" w [Azure Portal](https://portal.azure.com).

### <a name="exchange-administrator"></a>[Administrator programu Exchange](#exchange-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Exchange Online, gdy usługa jest obecna. Program ma także możliwość tworzenia wszystkich grup Microsoft 365 i zarządzania nimi, zarządzania biletami pomocy technicznej oraz monitorowania kondycji usługi. Więcej informacji na [temat Microsoft 365 ról administratorów](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> W interfejsie API Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi Exchange". Jest to "Administrator programu Exchange" w [Azure Portal](https://portal.azure.com). Jest to "administrator usługi Exchange Online" w [centrum administracyjnym programu Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).


### <a name="external-id-user-flow-administrator"></a>[Administrator przepływu użytkownika z IDENTYFIKATORem zewnętrznym](#external-id-user-flow-administrator-permissions)

Użytkownicy z tą rolą mogą tworzyć i zarządzać przepływami użytkowników (nazywanymi również zasadami "wbudowanymi") w Azure Portal. Ci użytkownicy mogą dostosowywać zawartość HTML/CSS/JavaScript, zmieniać wymagania usługi MFA, wybierać oświadczenia w tokenie, zarządzać łącznikami interfejsu API i konfigurować ustawienia sesji dla wszystkich przepływów użytkowników w organizacji usługi Azure AD. Z drugiej strony ta rola nie obejmuje możliwości przeglądania danych użytkownika ani wprowadzania zmian w atrybutach, które są zawarte w schemacie organizacji. Zmiany zasad struktury środowiska tożsamości (znanych także jako zasady niestandardowe) również są poza zakresem tej roli.

### <a name="external-id-user-flow-attribute-administrator"></a>[Administrator atrybutów przepływu użytkownika zewnętrznego ID](#external-id-user-flow-attribute-administrator-permissions)

Użytkownicy z tą rolą dodają lub usuwają atrybuty niestandardowe dostępne dla wszystkich przepływów użytkowników w organizacji usługi Azure AD. W związku z tym użytkownicy z tą rolą mogą zmieniać lub dodawać nowe elementy do schematu użytkownika końcowego i wpływać na zachowanie wszystkich przepływów użytkownika, a także pośrednio wprowadzić zmiany w jakie dane mogą być zadawane przez użytkowników końcowych i ostatecznie wysyłane jako oświadczenia do aplikacji. Ta rola nie może edytować przepływów użytkowników.

### <a name="external-identity-provider-administrator"></a>[Administrator zewnętrznego dostawcy tożsamości](#external-identity-provider-administrator-permissions)

Ten administrator zarządza Federacją między organizacjami usługi Azure AD a zewnętrznymi dostawcami tożsamości. Za pomocą tej roli użytkownicy mogą dodawać nowych dostawców tożsamości i konfigurować wszystkie dostępne ustawienia (np. ścieżkę uwierzytelniania, identyfikator usługi, przypisane kontenery kluczy). Ten użytkownik może włączyć, aby organizacja usługi Azure AD mogła ufać uwierzytelnianiu od zewnętrznych dostawców tożsamości. Wynikowy wpływ na środowisko użytkownika końcowego zależy od typu organizacji:

* Organizacje usługi Azure AD dla pracowników i partnerów: dodanie Federacji (np. z użyciem usługi Gmail) natychmiast wpłynie na wszystkie zaproszenia gościa, które nie zostały jeszcze zrealizowane. Zobacz [Dodawanie usługi Google jako dostawcy tożsamości dla użytkowników-Gości B2B](../external-identities/google-federation.md).
* Azure Active Directory B2C organizacje: dodanie Federacji (na przykład w usłudze Facebook lub innej organizacji usługi Azure AD) nie wpływa natychmiast na przepływy użytkowników końcowych, dopóki dostawca tożsamości nie zostanie dodany jako opcja w przepływie użytkownika (nazywane również zasadami wbudowanymi). Zapoznaj się z przykładem dotyczącym [konfigurowania konto Microsoft jako dostawcy tożsamości](../../active-directory-b2c/identity-provider-microsoft-account.md) . Aby zmienić przepływy użytkowników, wymagana jest ograniczona rola "Administrator przepływu użytkownika B2C".

### <a name="global-administrator"></a>[Administrator globalny](#global-administrator-permissions)

Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w Azure Active Directory, a także usług korzystających z tożsamości Azure Active Directory, takich jak Microsoft 365 Security Center, Microsoft 365 Centrum zgodności, Exchange Online, SharePoint Online i Skype dla firm Online. Ponadto administratorzy globalni mogą [podnieść poziom dostępu](../../role-based-access-control/elevate-access-global-admin.md) do zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania. Dzięki temu administratorzy globalni mogą uzyskać pełny dostęp do wszystkich zasobów platformy Azure przy użyciu odpowiedniej dzierżawy usługi Azure AD. Osoba, która zarejestruje się w usłudze Azure AD, zostaje administratorem globalnym. W Twojej firmie może istnieć więcej niż jeden administrator globalny. Administratorzy globalni mogą zresetować hasło dla dowolnego użytkownika i wszystkich innych administratorów.

### <a name="global-reader"></a>[Czytelnik globalny](#global-reader-permissions)

Użytkownicy w tej roli mogą odczytywać ustawienia i informacje administracyjne w ramach usług Microsoft 365, ale nie mogą podejmować działań związanych z zarządzaniem. Global Reader to odpowiedni dla administratora globalnego tylko do odczytu. Przypisywanie globalnego czytnika zamiast administratora globalnego do planowania, inspekcji lub badań. Używaj globalnego czytnika w połączeniu z innymi ograniczonymi rolami administratora, takimi jak administrator programu Exchange, aby ułatwić wykonywanie zadań bez przypisywania roli administratora globalnego. Global Reader współpracuje z centrum administracyjnym Microsoft 365, centrum administracyjnym programu Exchange, centrum administracyjnym programu SharePoint, centrami administracyjnymi zespołów, Centrum zabezpieczeń, centrum zgodności, centrum administracyjnym usługi Azure AD i centrum administracyjnym zarządzania urządzeniami.

> [!NOTE]
> Globalna rola czytnika ma teraz kilka ograniczeń —
>
>- [Centrum administracyjne usługi OneDrive](https://admin.onedrive.com/) — centrum administracyjne usługi OneDrive nie obsługuje roli czytnika globalnego
>- [Centrum administracyjne M365](https://admin.microsoft.com/Adminportal/Home#/homepage) — globalny czytnik nie może odczytać żądań skrytki klienta. Nie znajdziesz karty **żądania skrytki klienta** w obszarze **Pomoc techniczna** w lewym okienku Centrum administracyjnego M365.
>- [Centrum zgodności & zabezpieczeń pakietu Office](https://sip.protection.office.com/homepage) — czytnik globalny nie może odczytać dzienników inspekcji SCC, przeprowadzić wyszukiwania zawartości lub zobaczyć zabezpieczony wynik.
>- [Centrum administracyjne zespołów](https://admin.teams.microsoft.com) — globalny czytnik nie może odczytywać **zespołów cykl życia**, **Analiza & raporty**, **Zarządzanie urządzeniami telefonicznymi IP** i **wykaz aplikacji**.
>- [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) nie obsługuje roli czytnika globalnego.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) — Global Reader jest obsługiwany tylko [w przypadku raportowania centralnego](/azure/information-protection/reports-aip) , a organizacja usługi Azure AD nie jest na [ujednoliconej platformie etykietowania](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
>
> Te funkcje są obecnie opracowywane.
>

### <a name="groups-administrator"></a>[Administrator grup](#groups-administrator-permissions)

Użytkownicy w tej roli mogą tworzyć grupy i ich ustawienia, takie jak zasady nazewnictwa i wygasania, oraz zarządzać nimi. Ważne jest, aby zrozumieć, że przypisanie użytkownika do tej roli daje im możliwość zarządzania wszystkimi grupami w organizacji między różnymi obciążeniami, takimi jak zespoły, SharePoint, Yammer, oprócz programu Outlook. Ponadto użytkownik będzie mógł zarządzać różnymi ustawieniami grup w różnych portalach administracyjnych, takich jak centrum administracyjne firmy Microsoft, Azure Portal, a także obciążenie specyficzne dla zespołów i centrów administracyjnych programu SharePoint.

### <a name="guest-inviter"></a>[Zapraszający gościa](#guest-inviter-permissions)

Użytkownicy w tej roli mogą zarządzać Azure Active Directory zaproszeniami użytkowników typu B2B, gdy **Członkowie mogą zapraszać** ustawienie użytkownika na wartość nie. Więcej informacji na temat współpracy B2B na [temat współpracy B2B w usłudze Azure AD](../external-identities/what-is-b2b.md). Nie zawiera żadnych innych uprawnień.

### <a name="helpdesk-administrator"></a>[Administrator pomocy technicznej](#helpdesk-administrator-permissions)

Użytkownicy z tą rolą mogą zmieniać hasła, unieważniać tokeny odświeżania, zarządzać żądaniami obsługi oraz monitorować kondycję usługi. Unieważnienie tokenu odświeżania Wymusza ponowne zalogowanie użytkownika. Czy administrator pomocy technicznej może zresetować hasło użytkownika i unieważniać tokeny odświeżania, zależy od roli przypisanej do użytkownika. Aby uzyskać listę ról, które administrator pomocy technicznej może zresetować hasła dla i unieważniać tokeny odświeżania, zobacz [uprawnienia do resetowania haseł](#password-reset-permissions).

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać hasła dla osób, które mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej wewnątrz i na zewnątrz Azure Active Directory. Zmiana hasła użytkownika może oznaczać, że założono, że tożsamość i uprawnienia tego użytkownika. Na przykład:
>
>- Rejestracja aplikacji i właściciele aplikacji przedsiębiorstwa, którzy mogą zarządzać poświadczeniami aplikacji, których są właścicielami. Aplikacje te mogą mieć uprawnienia uprzywilejowane w usłudze Azure AD i nie są przyznawane administratorom pomocy technicznej. Za pomocą tej ścieżki Administrator pomocy technicznej może być w stanie założyć tożsamość właściciela aplikacji, a następnie ponownie założyć tożsamość aplikacji uprzywilejowanej przez zaktualizowanie poświadczeń dla aplikacji.
>- Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej na platformie Azure.
>- Grupa zabezpieczeń i właściciele grup Microsoft 365, którzy mogą zarządzać członkostwem w grupie. Te grupy mogą udzielić dostępu do poufnych lub prywatnych informacji lub konfiguracji krytycznej w usłudze Azure AD i w innym miejscu.
>- Administratorzy w innych usługach poza usługą Azure AD, np. Exchange Online, Centrum zabezpieczeń i zgodności pakietu Office oraz systemy kadr.
>- Użytkownicy niebędący administratorami, w tym członkowie kierownictwa, prawnik prawny i pracownicy działu kadr, którzy mogą mieć dostęp do poufnych lub prywatnych informacji.

Delegowanie uprawnień administracyjnych przez podzbiory użytkowników i stosowanie zasad do podzbioru użytkowników jest możliwe z [jednostkami administracyjnymi](administrative-units.md).

Ta rola była wcześniej nazywana "administratorem haseł" w [Azure Portal](https://portal.azure.com/). Nazwa "Administrator pomocy technicznej" w usłudze Azure AD jest teraz zgodna z nazwą w programie Azure AD PowerShell i interfejsem API Microsoft Graph.

### <a name="hybrid-identity-administrator"></a>[Administrator tożsamości hybrydowej](#hybrid-identity-administrator-permissions)

Użytkownicy w tej roli mogą tworzyć i wdrażać ustawienia konfiguracji aprowizacji z usługi AD w usłudze Azure AD przy użyciu aprowizacji w chmurze, a także zarządzać ustawieniami federacyjnymi. Użytkownicy mogą również rozwiązywać problemy i monitorować dzienniki przy użyciu tej roli.  

### <a name="insights-administrator"></a>[Administrator usługi Insights](#insights-administrator-permissions)
Użytkownicy w tej roli mogą uzyskać dostęp do pełnego zestawu funkcji administracyjnych w [aplikacji M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Ta rola ma możliwość odczytywania informacji o katalogu, monitorowania kondycji usługi, biletów pomocy technicznej dotyczącej plików i uzyskiwania dostępu do aspektów ustawień administratora szczegółowego.

### <a name="insights-business-leader"></a>[Lider biznesowy usługi Insights](#insights-business-leader-permissions)
Użytkownicy w tej roli mogą uzyskiwać dostęp do zestawu pulpitów nawigacyjnych i szczegółowych informacji za pośrednictwem [aplikacji M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Obejmuje to pełny dostęp do wszystkich pulpitów nawigacyjnych oraz przedstawia szczegółowe informacje i funkcje eksploracji danych. Użytkownicy w tej roli nie mają dostępu do ustawień konfiguracji produktu, które są odpowiedzialne za rolę administratora usługi Insights.

### <a name="intune-administrator"></a>[Administrator usługi Intune](#intune-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Intune online, gdy usługa jest obecna. Ponadto ta rola obejmuje możliwość zarządzania użytkownikami i urządzeniami w celu kojarzenia zasad, a także tworzenia grup i zarządzania nimi. Więcej informacji na temat [kontroli administracji opartej na rolach (RBAC) z Microsoft Intune](/intune/role-based-access-control).

Ta rola może tworzyć wszystkie grupy zabezpieczeń i zarządzać nimi. Jednak administrator usługi Intune nie ma uprawnień administratora w grupach pakietu Office. Oznacza to, że administrator nie może zaktualizować właścicieli ani członkostw wszystkich grup pakietu Office w organizacji. Może jednak zarządzać utworzoną przez siebie grupą pakietu Office, która jest częścią swoich uprawnień użytkownika końcowego. W związku z tym wszystkie grupy pakietu Office (nie grupy zabezpieczeń), które tworzy, powinny być wliczane do limitu przydziału 250.

> [!NOTE]
> W interfejsie API Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi Intune". Jest to "administrator usługi Intune" w [Azure Portal](https://portal.azure.com).

### <a name="kaizala-administrator"></a>[Usługi kaizala administrator](#kaizala-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne do zarządzania ustawieniami w programie Microsoft usługi kaizala, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Ponadto użytkownik może uzyskiwać dostęp do raportów związanych z wdrażaniem & użyciu usługi kaizala przez członków organizacji i raportów biznesowych wygenerowanych za pomocą akcji usługi kaizala.

### <a name="license-administrator"></a>[Administrator licencji](#license-administrator-permissions)

Użytkownicy w tej roli mogą dodawać, usuwać i aktualizować przypisania licencji dla użytkowników, grup (przy użyciu licencjonowania opartego na grupach) i zarządzać lokalizacją użycia na użytkownikach. Rola nie pozwala na kupowanie subskrypcji ani zarządzanie nimi, tworzenie grup ani zarządzanie nimi, a także tworzenie użytkowników poza lokalizacją użycia ani zarządzanie nimi. Ta rola nie ma dostępu do wyświetlania biletów pomocy technicznej, tworzenia ich ani zarządzania nimi.

### <a name="message-center-privacy-reader"></a>[Czytnik prywatności centrum wiadomości](#message-center-privacy-reader-permissions)

Użytkownicy w tej roli mogą monitorować wszystkie powiadomienia w centrum wiadomości, w tym komunikaty o ochronie prywatności danych. Czytelnicy ochrony prywatności centrum wiadomości otrzymują powiadomienia e-mail, w tym dotyczące prywatności danych i mogą anulować subskrypcję przy użyciu preferencji centrum wiadomości. Tylko administrator globalny i czytnik zasad ochrony prywatności centrum wiadomości mogą odczytywać wiadomości dotyczące prywatności danych. Ponadto ta rola zawiera możliwość wyświetlania grup, domen i subskrypcji. Ta rola nie ma uprawnień do wyświetlania, tworzenia ani zarządzania żądaniami obsługi.

### <a name="message-center-reader"></a>[Czytelnik centrum wiadomości](#message-center-reader-permissions)

Użytkownicy w tej roli mogą monitorować powiadomienia i aktualizacje kondycji doradców w [centrum wiadomości](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) dla swojej organizacji na skonfigurowanych usługach, takich jak Exchange, Intune i Microsoft Teams. Czytelnicy centrum wiadomości otrzymują cotygodniowe podsumowanie wiadomości e-mail z ogłoszeń, aktualizacji i mogą udostępniać wpisy centrum wiadomości w Microsoft 365. W usłudze Azure AD Użytkownicy przypisani do tej roli będą mieli dostęp tylko do odczytu w ramach usług Azure AD, takich jak użytkownicy i grupy. Ta rola nie ma dostępu do wyświetlania biletów pomocy technicznej, tworzenia ich ani zarządzania nimi.

### <a name="modern-commerce-user"></a>[Nowoczesny użytkownik handlowy](#modern-commerce-user-permissions)

Nie używaj. Ta rola jest automatycznie przypisana z handlu i nie jest przeznaczona do użycia ani nie jest obsługiwana w żadnym innym przypadku. Zobacz szczegóły poniżej.

Rola użytkownika nowoczesnego handlu zapewnia określonym użytkownikom uprawnienia dostępu do centrum administracyjnego Microsoft 365, a następnie wyświetla lewe wpisy nawigacyjne dla **domu**, **rozliczeń** i **pomocy technicznej**. Zawartość dostępna w tych obszarach jest kontrolowana przez [role specyficzne dla handlu](../../cost-management-billing/manage/understand-mca-roles.md) , które są przypisane do użytkowników w celu zarządzania produktami zakupionymi dla siebie lub w organizacji. Mogą to być takie zadania, jak płacisz rachunki lub dostęp do kont rozliczeń i profilów rozliczeń. 

Użytkownicy z rolą użytkownika Modern Commerce zazwyczaj mają uprawnienia administracyjne w innych systemach zakupów firmy Microsoft, ale nie mają ról administratora globalnego lub administratora rozliczeń służących do uzyskiwania dostępu do centrum administracyjnego. 

**Kiedy jest przypisana nowoczesny rola użytkownika Commerce?**

* Samoobsługowe **kupowanie w Microsoft 365 centrum administracyjnego** — zakup samoobsługowy umożliwia użytkownikom wypróbowanie nowych produktów przez ich zakup lub utworzenie. Te produkty są zarządzane w centrum administracyjnym. Użytkownicy, którzy dokonują zakupu samoobsługowego, mają przypisaną rolę w systemie Commerce i nowoczesnej roli użytkownika commerce, aby mogli zarządzać zakupami w centrum administracyjnym. Administratorzy mogą blokować zakupy samoobsługowe (w przypadku Power BI, aplikacji zaawansowanych, automatyzacji) za poorednictwem [programu PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell). Aby uzyskać więcej informacji, zobacz [Zakup samoobsługowy — często zadawane pytania](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).  
* **Zakupy od firmy Microsoft komercyjnej Marketplace**  — podobnie jak w przypadku zakupu samoobsługowego, gdy użytkownik kupuje produkt lub usługę od Microsoft AppSource lub Azure Marketplace, jest przypisywany współczesna rola użytkownika commerce w przypadku, gdy nie ma roli Administrator globalny lub rozliczenia. W niektórych przypadkach użytkownicy mogą mieć zablokowaną możliwość dokonywania tych zakupów. Aby uzyskać więcej informacji, zobacz [Microsoft Commercial Marketplace](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase).
* **Propozycje firmy Microsoft**  — propozycja jest formalną ofertą oferowaną przez firmę Microsoft w organizacji do kupowania produktów i usług firmy Microsoft. Gdy osoba akceptująca ofertę nie ma roli administratora globalnego lub administratora rozliczeń w usłudze Azure AD, są oni przypisani do roli specyficzne dla handlu, aby dokończyć propozycję i współczesną rolę użytkownika handlowego w celu uzyskania dostępu do centrum administracyjnego. Gdy uzyskują dostęp do centrum administracyjnego, mogą korzystać tylko z funkcji, które są autoryzowane przez ich rolę specyficzną dla danego handlu.
* **Role specyficzne dla handlu** — niektórzy użytkownicy mają przypisane role dotyczące handlu. Jeśli użytkownik nie jest administratorem globalnym lub rozliczeniami, uzyska nowoczesnej roli użytkownika commerce, aby mógł uzyskać dostęp do centrum administracyjnego.  

Jeśli nie przypisano do użytkownika nowoczesnej roli użytkownika w języku commerce, utraci on dostęp do centrum administracyjnego Microsoft 365. W przypadku zarządzania produktami przez siebie lub w organizacji nie będą oni mogli nimi zarządzać. Może to dotyczyć przypisywania licencji, zmiany metod płatności, płacenia rachunków lub innych zadań związanych z zarządzaniem subskrypcjami.

### <a name="network-administrator"></a>[Administrator sieci](#network-administrator-permissions)

Użytkownicy w tej roli mogą przeglądać zalecenia dotyczące architektury obwodowej sieci firmy Microsoft, które są oparte na telemetrii sieci z lokalizacji użytkownika. Wydajność sieci dla Microsoft 365 opiera się na starannej architekturze obwodowej sieci klienta, która jest zwykle specyficzna dla lokalizacji użytkownika. Ta rola pozwala edytować odnalezione lokalizacje użytkowników i konfigurację parametrów sieci dla tych lokalizacji, aby ułatwić lepsze pomiary telemetrii i zalecenia dotyczące projektowania
### <a name="office-apps-administrator"></a>[Administrator aplikacji pakietu Office](#office-apps-administrator-permissions)

Użytkownicy w tej roli mogą zarządzać ustawieniami chmury Microsoft 365 aplikacji. Obejmuje to zarządzanie zasadami chmury, samoobsługowym zarządzaniem pobieraniem i możliwością wyświetlania raportu związanego z aplikacjami pakietu Office. Ta rola dodatkowo umożliwia zarządzanie biletami pomocy technicznej i monitorowanie kondycji usługi w głównym centrum administracyjnym. Użytkownicy przypisani do tej roli mogą również zarządzać komunikacją o nowych funkcjach w aplikacjach pakietu Office. 

### <a name="partner-tier1-support"></a>[Obsługa pomoc partnera](#partner-tier1-support-permissions)

Nie używaj. Ta rola jest przestarzała i zostanie usunięta z usługi Azure AD w przyszłości. Ta rola jest przeznaczona do użytku przez małą liczbę partnerów odsprzedaży firmy Microsoft i nie jest przeznaczona do użytku ogólnego.

### <a name="partner-tier2-support"></a>[Obsługa SVR partnera](#partner-tier2-support-permissions)

Nie używaj. Ta rola jest przestarzała i zostanie usunięta z usługi Azure AD w przyszłości. Ta rola jest przeznaczona do użytku przez małą liczbę partnerów odsprzedaży firmy Microsoft i nie jest przeznaczona do użytku ogólnego.

### <a name="password-administrator"></a>[Administrator haseł](#password-administrator-permissions)

Użytkownicy z tą rolą mają ograniczoną możliwość zarządzania hasłami. Ta rola nie przyznaje możliwości zarządzania żądaniami obsługi ani monitorowania kondycji usługi. Określa, czy administrator hasła może zresetować hasło użytkownika zależy od roli przypisanej do użytkownika. Aby uzyskać listę ról, dla których administrator haseł może zresetować hasła dla programu, zobacz [uprawnienia do resetowania haseł](#password-reset-permissions).

### <a name="power-bi-administrator"></a>[Power BI administrator](#power-bi-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w programie Microsoft Power BI, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji na [temat Power BI roli administratora](/power-bi/service-admin-role).

> [!NOTE]
> W interfejsie API Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi Power BI". W [Azure Portal](https://portal.azure.com)jest "Power BI administrator".

### <a name="power-platform-administrator"></a>[Administrator platformy w elektrowni](#power-platform-administrator-permissions)

Użytkownicy w tej roli mogą tworzyć wszystkie aspekty środowisk, usługi PowerApps, przepływów, zasady ochrony przed utratą danych i zarządzać nimi. Ponadto użytkownicy z tą rolą mają możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi.

### <a name="printer-administrator"></a>[Administrator drukarki](#printer-administrator-permissions)

Użytkownicy w tej roli mogą rejestrować drukarki i zarządzać wszystkimi aspektami wszystkich konfiguracji drukarek w uniwersalnym rozwiązaniu drukowania firmy Microsoft, w tym ustawieniami uniwersalnego łącznika wydruku. Mogą oni wyrazić zgodę na wszystkie delegowane żądania uprawnień do drukowania. Administratorzy drukarek mają również dostęp do raportów drukowania.

### <a name="printer-technician"></a>[Technika drukarki](#printer-technician-permissions)

Użytkownicy z tą rolą mogą rejestrować drukarki i zarządzać stanem drukarki w uniwersalnym rozwiązaniu firmy Microsoft. Mogą również odczytywać wszystkie informacje o łączniku. Podstawowe zadanie nie można ustawić uprawnień użytkownika w przypadku drukarek i udostępniania drukarek.

### <a name="privileged-authentication-administrator"></a>[Administrator uprzywilejowanego uwierzytelniania](#privileged-authentication-administrator-permissions)

Użytkownicy z tą rolą mogą ustawiać lub resetować poświadczenia bez hasła dla wszystkich użytkowników, w tym administratorów globalnych, a także aktualizować hasła dla wszystkich użytkowników. Administratorzy uprzywilejowanego uwierzytelniania mogą zmusić użytkowników do ponownego zarejestrowania istniejących poświadczeń niezwiązanych z hasłem (takich jak MFA lub FIDO) i odwołać "Pamiętaj usługę MFA na urządzeniu", monitując o usługę MFA przy następnym logowaniu dla wszystkich użytkowników.

### <a name="privileged-role-administrator"></a>[Administrator ról uprzywilejowanych](#privileged-role-administrator-permissions)

Użytkownicy z tą rolą mogą zarządzać przypisaniami ról w Azure Active Directory, a także w Azure AD Privileged Identity Management. Mogą oni tworzyć i zarządzać grupami, które można przypisać do ról usługi Azure AD. Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami Privileged Identity Management i jednostek administracyjnych.

> [!IMPORTANT]
> Ta rola umożliwia zarządzanie przypisaniami wszystkich ról usługi Azure AD, w tym roli administratora globalnego. Ta rola nie obejmuje żadnych innych uprzywilejowanych możliwości w usłudze Azure AD, takich jak tworzenie i aktualizowanie użytkowników. Jednak użytkownicy przypisani do tej roli mogą udzielić sobie lub innym dodatkowym uprawnieniem, przypisując dodatkowe role.

### <a name="reports-reader"></a>[Czytelnik raportów](#reports-reader-permissions)

Użytkownicy z tą rolą mogą wyświetlać dane raportowania użycia i pulpit nawigacyjny raportów w centrum administracyjnym programu Microsoft 365 i pakietem kontekstu wdrażania w programie Power BI. Ponadto rola zapewnia dostęp do raportów i działań związanych z logowaniem w usłudze Azure AD oraz danymi zwracanymi przez interfejs API raportowania Microsoft Graph. Użytkownik przypisany do roli czytelnik raportów może uzyskać dostęp tylko do odpowiednich metryk użycia i wdrażania. Nie mają uprawnień administratora do konfigurowania ustawień lub uzyskiwania dostępu do centrów administracyjnych specyficznych dla produktu, takich jak program Exchange. Ta rola nie ma dostępu do wyświetlania biletów pomocy technicznej, tworzenia ich ani zarządzania nimi.

### <a name="search-administrator"></a>[Administrator wyszukiwania](#search-administrator-permissions)

Użytkownicy w tej roli mają pełny dostęp do wszystkich funkcji zarządzania wyszukiwaniem firmy Microsoft w centrum administracyjnym Microsoft 365. Ponadto Ci użytkownicy mogą wyświetlać centrum wiadomości, monitorować kondycję usługi i tworzyć żądania obsługi.

### <a name="search-editor"></a>[Edytor wyszukiwania](#search-editor-permissions)

Użytkownicy w tej roli mogą tworzyć i usuwać zawartość usługi Microsoft Search w centrum administracyjnym Microsoft 365, w tym zakładki, pytania Q&jako lokalizacje oraz zarządzać nimi.

### <a name="security-administrator"></a>[Administrator zabezpieczeń](#security-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia do zarządzania funkcjami związanymi z zabezpieczeniami w centrum Microsoft 365 Security Center, Azure Active Directory Identity Protection, Azure Active Directory Authentication, Azure Information Protection i Office 365 Security &. Więcej informacji o uprawnieniach pakietu Office 365 jest dostępnych w obszarze [uprawnienia w centrum zabezpieczeń & zgodności](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

W | Można wykonać
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Monitorowanie zasad związanych z zabezpieczeniami w ramach usług Microsoft 365 Services<br>Zarządzanie zagrożeniami i alertami zabezpieczeń<br>Wyświetlanie raportów
Centrum ochrony tożsamości | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>Ponadto możliwość wykonywania wszystkich operacji usługi Identity Protection Center z wyjątkiem resetowania haseł
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>**Nie można** zarządzać przypisaniami ról lub ustawieniami usługi Azure AD
[Centrum zgodności & zabezpieczeń pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie zasadami zabezpieczeń<br>Wyświetlanie, badanie i reagowanie na zagrożenia bezpieczeństwa<br>Wyświetlanie raportów
Azure Advanced Threat Protection | Monitoruj podejrzane działania zabezpieczeń i odpowiadaj na nie
Windows Defender ATP i EDR | Przypisywanie ról<br>Zarządzanie grupami maszyn<br>Konfigurowanie wykrywania zagrożeń punktów końcowych i zautomatyzowanego korygowania<br>Wyświetlanie alertów, badanie ich i reagowanie na nie
[Intune](/intune/role-based-access-control) | Wyświetla informacje o użytkownikach, urządzeniach, rejestracji, konfiguracji i aplikacji<br>Nie można wprowadzać zmian w usłudze Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Dodawanie administratorów, Dodawanie zasad i ustawień, przekazywanie dzienników i wykonywanie akcji ładu
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Może wyświetlać zasady zabezpieczeń, wyświetlać Stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia, odrzucać alerty i zalecenia
[Kondycja usługi Microsoft 365](/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług Microsoft 365 Services
[Inteligentna blokada](../authentication/howto-password-smart-lockout.md) | Zdefiniuj próg i czas trwania dla blokad w przypadku wystąpienia nieudanych zdarzeń logowania.
[Ochrona hasłem](../authentication/concept-password-ban-bad.md) | Skonfiguruj niestandardową listę haseł zabronionych lub lokalną ochronę hasłem.

### <a name="security-operator"></a>[Operator zabezpieczeń](#security-operator-permissions)

Użytkownicy z tą rolą mogą zarządzać alertami i mieć globalny dostęp tylko do odczytu w funkcjach związanych z zabezpieczeniami, w tym wszystkie informacje w Microsoft 365 Security Center, Azure Active Directory, Identity Protection, Privileged Identity Management i Office 365 Security & Centrum zgodności. Więcej informacji o uprawnieniach pakietu Office 365 jest dostępnych w obszarze [uprawnienia w centrum zabezpieczeń & zgodności](/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

W | Można wykonać
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>Wyświetlanie alertów dotyczących zagrożeń zabezpieczeń, badanie ich i reagowanie na nie
Usługa Azure AD Identity Protection | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>Ponadto możliwość wykonywania wszystkich operacji usługi Identity Protection Center z wyjątkiem resetowania haseł i konfigurowania wiadomości e-mail dotyczących alertów.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Wszystkie uprawnienia roli czytelnik zabezpieczeń
[Centrum zgodności & zabezpieczeń pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>Wyświetlanie alertów zabezpieczeń, badanie ich i reagowanie na nie
Windows Defender ATP i EDR | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>Wyświetlanie alertów zabezpieczeń, badanie ich i reagowanie na nie
[Intune](/intune/role-based-access-control) | Wszystkie uprawnienia roli czytelnik zabezpieczeń
[Cloud App Security](/cloud-app-security/manage-admins) | Wszystkie uprawnienia roli czytelnik zabezpieczeń
[Kondycja usługi Microsoft 365](/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług Microsoft 365 Services

### <a name="security-reader"></a>[Czytelnik zabezpieczeń](#security-reader-permissions)

Użytkownicy z tą rolą mają globalny dostęp tylko do odczytu w funkcji związanych z zabezpieczeniami, w tym wszystkie informacje w Microsoft 365 Security Center, Azure Active Directory, Ochrona tożsamości, Privileged Identity Management, a także możliwość odczytywania Azure Active Directory raportów logowania i dzienników inspekcji oraz w centrum zgodności z pakietem Office 365 Security &. Więcej informacji o uprawnieniach pakietu Office 365 jest dostępnych w obszarze [uprawnienia w centrum zabezpieczeń & zgodności](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

W | Można wykonać
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Wyświetlanie zasad związanych z zabezpieczeniami w ramach usług Microsoft 365 Services<br>Wyświetl zagrożenia i alerty zabezpieczeń<br>Wyświetlanie raportów
Centrum ochrony tożsamości | Przeczytaj wszystkie raporty i ustawienia zabezpieczeń dotyczące funkcji zabezpieczeń<br><ul><li>Antyspam<li>Szyfrowanie<li>Ochrona przed utratą danych<li>Ochrona przed złośliwym oprogramowaniem<li>Zaawansowana ochrona przed zagrożeniami<li>Ochrona przed phishingiem<li>Reguły Mailflow
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Ma dostęp tylko do odczytu do wszystkich informacji podanych w Azure AD Privileged Identity Management: zasady i raporty dotyczące przypisań ról i przeglądów zabezpieczeń usługi Azure AD.<br>**Nie można** zarejestrować się w usłudze Azure AD Privileged Identity Management ani wprowadzać w niej żadnych zmian. W portalu Privileged Identity Management lub za pośrednictwem programu PowerShell ktoś w tej roli może aktywować dodatkowe role (na przykład administrator globalny lub administrator ról uprzywilejowanych), jeśli użytkownik jest uprawniony do korzystania z nich.
[Centrum zgodności & zabezpieczeń pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Wyświetlanie zasad zabezpieczeń<br>Wyświetl i zbadaj zagrożenia bezpieczeństwa<br>Wyświetlanie raportów
Windows Defender ATP i EDR | Wyświetlanie i badanie alertów. Po włączeniu kontroli dostępu opartej na rolach w usłudze Windows Defender ATP użytkownicy z uprawnieniami tylko do odczytu, takimi jak rola czytnika zabezpieczeń usługi Azure AD, tracą dostęp do momentu przypisania ich do roli usługi Windows Defender ATP.
[Intune](/intune/role-based-access-control) | wyświetla informacje o użytkownikach, urządzeniach, rejestracji, konfiguracji i aplikacji. Nie może wprowadzać zmian w usłudze Intune.
[Cloud App Security](/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Może wyświetlać zalecenia i alerty, wyświetlać zasady zabezpieczeń, wyświetlać Stany zabezpieczeń, ale nie mogą wprowadzać zmian
[Kondycja usługi Microsoft 365](/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług Microsoft 365 Services

### <a name="service-support-administrator"></a>[Administrator pomocy technicznej usługi](#service-support-administrator-permissions)

Użytkownicy z tą rolą mogą otwierać żądania pomocy technicznej z firmą Microsoft dla platformy Azure i usługami Microsoft 365 Services oraz przegląda pulpit nawigacyjny usługi i centrum wiadomości w [centrum administracyjnym](https://admin.microsoft.com) [Azure Portal](https://portal.azure.com) i Microsoft 365. Więcej informacji na [temat ról administratorów](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Wcześniej ta rola była nazywana "administratorem usługi" w [Azure Portal](https://portal.azure.com) i [Microsoft 365 centrum administracyjnego](https://admin.microsoft.com). Nazwa została zmieniona na "Administrator pomocy technicznej usługi", aby wyrównać nazwę istniejącego w interfejsie API Microsoft Graph, usłudze Azure AD interfejs API programu Graph i programie Azure AD PowerShell.

### <a name="sharepoint-administrator"></a>[Administrator programu SharePoint](#sharepoint-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft SharePoint Online, gdy usługa jest obecna, a także możliwość tworzenia wszystkich grup Microsoft 365 i zarządzania nimi, zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji na [temat ról administratorów](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> W interfejsie API Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi SharePoint". Jest to "Administrator programu SharePoint" w [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Ta rola przyznaje także uprawnienia w zakresie do interfejsu API Microsoft Graph dla Microsoft Intune, co umożliwia zarządzanie i Konfigurowanie zasad dotyczących zasobów programu SharePoint i usługi OneDrive.

### <a name="skype-for-business--lync-administrator"></a>[Administrator programu Skype dla firm/Lync](#lync-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Skype dla firm, gdy usługa jest obecna, a także do zarządzania atrybutami użytkownika specyficznymi dla programu Skype w Azure Active Directory. Ponadto ta rola zapewnia możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi oraz uzyskiwania dostępu do zespołów i centrum administracyjnego programu Skype dla firm. Konto musi być również licencjonowane dla zespołów lub nie może uruchamiać poleceń cmdlet programu PowerShell dla zespołów. Więcej informacji na [temat roli administratora programu Skype dla firm](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) i zespołów informacje o licencjonowaniu w usłudze [Skype dla firm i licencjonowaniu dodatku Microsoft Teams](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> W interfejsie API Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi Lync". Jest to "Administrator programu Skype dla firm" w [Azure Portal](https://portal.azure.com/).

### <a name="teams-communications-administrator"></a>[Administratorzy zespołu ds. komunikacji](#teams-communications-administrator-permissions)

Użytkownicy w tej roli mogą zarządzać aspektami obciążeń programu Microsoft Teams związanymi z usługą telefon & Voice. Obejmuje to narzędzia do zarządzania do przypisywania numerów telefonów, zasad dotyczących głosu i spotkania oraz pełny dostęp do zestawu narzędzi do analizy wywołań.

### <a name="teams-communications-support-engineer"></a>[Inżynierowie pomocy technicznej dla zespołów](#teams-communications-support-engineer-permissions)

Użytkownicy w tej roli mogą rozwiązywać problemy z komunikacją w usłudze Microsoft Teams & Skype dla firm przy użyciu narzędzi do rozwiązywania problemów z wywołaniem użytkownika w centrum administracyjnym usługi Microsoft Teams & Skype dla firm. Użytkownicy w tej roli mogą wyświetlać pełne informacje o rekordzie wywołania dla wszystkich uczestników. Ta rola nie ma dostępu do wyświetlania biletów pomocy technicznej, tworzenia ich ani zarządzania nimi.

### <a name="teams-communications-support-specialist"></a>[Zespoły ds. pomocy technicznej](#teams-communications-support-specialist-permissions)

Użytkownicy w tej roli mogą rozwiązywać problemy z komunikacją w usłudze Microsoft Teams & Skype dla firm przy użyciu narzędzi do rozwiązywania problemów z wywołaniem użytkownika w centrum administracyjnym usługi Microsoft Teams & Skype dla firm. Użytkownicy w tej roli mogą tylko wyświetlać szczegóły użytkownika w wywołaniu dla określonego użytkownika, który wyszukał. Ta rola nie ma dostępu do wyświetlania biletów pomocy technicznej, tworzenia ich ani zarządzania nimi.

### <a name="teams-devices-administrator"></a>[Administrator urządzeń zespołów](#teams-devices-administrator-permissions)

Użytkownicy z tą rolą mogą zarządzać [urządzeniami certyfikowanymi przez zespoły](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) z poziomu Centrum administracyjnego zespołów. Ta rola umożliwia szybkie wyświetlanie wszystkich urządzeń z możliwością wyszukiwania i filtrowania urządzeń. Użytkownik może sprawdzić szczegóły poszczególnych urządzeń, w tym konta zalogowanego, marki i modelu urządzenia. Użytkownik może zmienić ustawienia na urządzeniu i zaktualizować wersje oprogramowania. Ta rola nie udziela uprawnień do sprawdzania aktywności zespołów i jakości wywołań urządzenia. 

### <a name="teams-service-administrator"></a>[Administrator usługi Teams](#teams-service-administrator-permissions)

Użytkownicy w tej roli mogą zarządzać wszystkimi aspektami obciążeń programu Microsoft Teams za pośrednictwem Centrum administracyjnego Microsoft Teams & Skype dla firm i odpowiednich modułów programu PowerShell. Obejmuje to między innymi wszystkie narzędzia do zarządzania dotyczące telefonii, wiadomości, spotkań i samych zespołów. Ta rola dodatkowo daje możliwość tworzenia wszystkich grup Microsoft 365 i zarządzania nimi, zarządzania biletami pomocy technicznej oraz monitorowania kondycji usługi.

### <a name="usage-summary-reports-reader"></a>[Czytnik raportów podsumowujących użycia](#usage-summary-reports-reader-permissions)

Użytkownicy z tą rolą mogą uzyskać dostęp do zagregowanych danych na poziomie dzierżawy i skojarzonych z nimi szczegółowych informacji w centrum administracyjnym Microsoft 365 w celu użycia i oceny produktywności, ale nie mogą uzyskać dostępu do dowolnych szczegółów na poziomie użytkownika ani szczegółowych danych. W Microsoft 365 centrum administracyjnym dla dwóch raportów rozróżniamy zagregowane dane na poziomie dzierżawy i szczegóły na poziomie użytkownika. Ta rola zapewnia dodatkową warstwę ochrony dla poszczególnych użytkowników, których dane zostały zlecone przez klientów i zespoły prawne. 

### <a name="user-administrator"></a>[Administrator użytkowników](#user-administrator-permissions)

Użytkownicy z tą rolą mogą tworzyć użytkowników i zarządzać wszystkimi aspektami użytkowników z pewnymi ograniczeniami (zobacz tabelę), a także aktualizować zasady wygasania haseł. Ponadto użytkownicy z tą rolą mogą tworzyć wszystkie grupy i zarządzać nimi. Ta rola obejmuje również możliwość tworzenia widoków użytkowników i zarządzania nimi, zarządzania biletami pomocy technicznej oraz monitorowania kondycji usługi. Administratorzy użytkowników nie mają uprawnień do zarządzania niektórymi właściwościami użytkowników w większości ról administratorów. Użytkownik z tą rolą nie ma uprawnień do zarządzania usługą MFA. Role, które są wyjątkami do tego ograniczenia, są wymienione w poniższej tabeli.

| Uprawnienie administratora użytkownika | Uwagi |
| --- | --- |
| Tworzenie użytkowników i grup<br/>Tworzenie widoków użytkowników i zarządzanie nimi<br/>Zarządzanie biletami pomocy technicznej pakietu Office<br/>Aktualizowanie zasad wygasania haseł |  |
| Zarządzanie licencjami<br/>Zarządzanie wszystkimi właściwościami użytkowników z wyjątkiem głównej nazwy użytkownika | Dotyczy wszystkich użytkowników, w tym wszystkich administratorów |
| Usuń i Przywróć<br/>Wyłącz i Włącz<br/>Zarządzanie wszystkimi właściwościami użytkowników, w tym główną nazwą użytkownika<br/>Aktualizuj klucze urządzeń (FIDO) | Dotyczy użytkowników, którzy nie są administratorami ani w żadnej z następujących ról:<ul><li>Administrator pomocy technicznej</li><li>Użytkownik bez roli</li><li>Administrator użytkowników</li></ul> |
| Unieważnianie tokenów odświeżania<br/>Resetowanie hasła | Aby uzyskać listę ról, które administrator użytkowników może resetować hasła dla i unieważniać tokeny odświeżania, zobacz [uprawnienia do resetowania haseł](#password-reset-permissions). |

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać hasła dla osób, które mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej wewnątrz i na zewnątrz Azure Active Directory. Zmiana hasła użytkownika może oznaczać, że założono, że tożsamość i uprawnienia tego użytkownika. Na przykład:
>
>- Rejestracja aplikacji i właściciele aplikacji przedsiębiorstwa, którzy mogą zarządzać poświadczeniami aplikacji, których są właścicielami. Aplikacje te mogą mieć uprawnienia uprzywilejowane w usłudze Azure AD, a w innym miejscu nie są przyznawane administratorom użytkowników. Za pomocą tej ścieżki administrator użytkownika może być w stanie założyć tożsamość właściciela aplikacji, a następnie ponownie założyć tożsamość aplikacji uprzywilejowanej przez zaktualizowanie poświadczeń dla aplikacji.
>- Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej na platformie Azure.
>- Grupa zabezpieczeń i właściciele grup Microsoft 365, którzy mogą zarządzać członkostwem w grupie. Te grupy mogą udzielić dostępu do poufnych lub prywatnych informacji lub konfiguracji krytycznej w usłudze Azure AD i w innym miejscu.
>- Administratorzy w innych usługach poza usługą Azure AD, np. Exchange Online, Centrum zabezpieczeń i zgodności pakietu Office oraz systemy kadr.
>- Użytkownicy niebędący administratorami, w tym członkowie kierownictwa, prawnik prawny i pracownicy działu kadr, którzy mogą mieć dostęp do poufnych lub prywatnych informacji.

## <a name="role-permissions"></a>Uprawnienia roli

W poniższych tabelach opisano określone uprawnienia w Azure Active Directory poszczególnych ról. Niektóre role mogą mieć dodatkowe uprawnienia w usługach firmy Microsoft poza programem Azure Active Directory.

### <a name="application-administrator-permissions"></a>Uprawnienia administratora aplikacji

Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa oraz zarządzać nimi.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Application/appProxyAuthentication/Update | Aktualizowanie właściwości uwierzytelniania serwera proxy aplikacji w jednostkach usługi w Azure Active Directory. |
| Microsoft. Directory/Application/appProxyUrlSettings/Update | Zaktualizuj wewnętrzne i zewnętrzne adresy URL serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/applicationProxy/Read | Zapoznaj się ze wszystkimi właściwościami serwera proxy aplikacji. |
| Microsoft. Directory/Applications/applicationProxy/Update | Zaktualizuj wszystkie właściwości serwera proxy aplikacji. |
| Microsoft. katalog/aplikacje/odbiorcy/aktualizacja | Aktualizowanie właściwości Applications. odbiorców w Azure Active Directory. |
| Microsoft. katalog/aplikacje/uwierzytelnianie/aktualizacja | Aktualizowanie właściwości Applications. Authentication w Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Zaktualizuj podstawowe właściwości aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/Create | Twórz aplikacje w Azure Active Directory. |
| Microsoft. Directory/Applications/Credentials/Update | Zaktualizuj Właściwość Applications. Credentials w Azure Active Directory. |
| Microsoft. katalog/aplikacje/usuwanie | Usuń aplikacje w Azure Active Directory. |
| Microsoft. Directory/Applications/Owners/Update | Aktualizowanie właściwości Applications. Owners w Azure Active Directory. |
| Microsoft. Directory/Applications/Permissions/Update | Aktualizowanie właściwości Applications. Permissions w Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Update | Zaktualizuj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Create | Utwórz appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/odczyt | Odczytaj appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Update | Aktualizacja appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Delete | Usuń appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/connectorGroups/allProperties/odczyt | Odczytaj właściwości grupy łączników serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/connectorGroups/allProperties/Update | Zaktualizuj wszystkie właściwości grupy łączników serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/connectorGroups/Create | Utwórz grupy łączników serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/connectorGroups/Delete | Usuń grupy łączników serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/Connectors/allProperties/Read | Odczytaj wszystkie właściwości łącznika serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/Connectors/Create | Utwórz łączniki serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Read | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Update | Zaktualizuj Właściwość policies. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Create | Utwórz zasady w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Delete | Usuń zasady w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/właściciele/odczyt | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/właściciele/aktualizacja | Zaktualizuj Właściwość policies. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/policyAppliedTo/odczyt | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/odbiorcy/aktualizacja | Aktualizacja właściwości serviceprincipals. odbiorca w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Authentication/Update | Aktualizacja właściwości serviceprincipals. Authentication w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Update | Zaktualizuj podstawowe właściwości obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Create | Tworzenie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Credentials/Update | Aktualizacja właściwości serviceprincipals. Credentials w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Delete | Usuwanie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Update | Aktualizacja właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Permissions/Update | Aktualizacja właściwości serviceprincipals. Permissions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Update | Aktualizacja właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Standard/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="application-developer-permissions"></a>Uprawnienia deweloperów aplikacji

Można utworzyć rejestracje aplikacji niezależne od ustawienia "użytkownicy mogą rejestrować aplikacje".

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Applications/createAsOwner | Twórz aplikacje w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/appRoleAssignments/createAsOwner | Utwórz appRoleAssignments w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/elementu oauth2permissiongrants/createAsOwner | Utwórz elementu oauth2permissiongrants w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/serviceprincipals/createAsOwner | Tworzenie obiektów serviceprincipals w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |

### <a name="authentication-administrator-permissions"></a>Uprawnienia administratora uwierzytelniania

Zezwolenie na wyświetlanie, ustawianie i Resetowanie informacji o metodach uwierzytelniania dla dowolnego użytkownika niebędącego administratorem.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/strongAuthentication/Update | Zaktualizuj właściwości silnego uwierzytelniania, takie jak informacje o poświadczeniach usługi MFA. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w organizacji Microsoft 365. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |

### <a name="attack-payload-author-permissions"></a>Uprawnienia autora ładunku ataku

Może utworzyć ładunki ataków, które mogą zostać później wdrożone przez administratora.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Office 365. protectionCenter/attackSimulator/ładunek/allProperties/allTasks | Twórz i Zarządzaj ładunkiem ataków w symulatorze ataków. |
| Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/Read | Przeczytaj raporty dotyczące symulacji ataku, odpowiedzi i powiązanego szkolenia. |

### <a name="attack-simulation-administrator-permissions"></a>Uprawnienia administratora symulacji ataku

Może tworzyć wszystkie aspekty kampanii związanych z symulacją ataków i zarządzać nimi.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Office 365. protectionCenter/attackSimulator/ładunek/allProperties/allTasks | Twórz i Zarządzaj ładunkiem ataków w symulatorze ataków. |
| Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/Read | Przeczytaj raporty dotyczące symulacji ataku, odpowiedzi i powiązanego szkolenia. |
| Microsoft. Office 365. protectionCenter/attackSimulator/symulacja/allProperties/allTasks | Twórz i Zarządzaj szablonami symulacji ataków w symulatorze ataków. |

### <a name="azure-devops-administrator-permissions"></a>Uprawnienia administratora usługi Azure DevOps

Może zarządzać zasadami i ustawieniami organizacji usługi Azure DevOps.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Opis roli](#azure-devops-administrator) powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. devOps/allEntities/allTasks | Odczytaj i skonfiguruj usługę Azure DevOps. |

### <a name="azure-information-protection-administrator-permissions"></a>Uprawnienia administratora Azure Information Protection

Może zarządzać wszystkimi aspektami usługi Azure Information Protection.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Opis roli](#) powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami Azure Information Protection. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Standard/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>Uprawnienia administratora zestawu kluczy B2C IEF

Zarządzaj wpisami tajnymi Federacji i szyfrowania w strukturze środowiska tożsamości.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. B2C/trustFramework/zestawy danych/allTasks | Odczytywanie i konfigurowanie zestawów kluczy w Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Uprawnienia administratora zasad B2C IEF

Tworzenie zasad zaufania platformy i zarządzanie nimi w strukturze środowiska tożsamości.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. B2C/trustFramework/policies/allTasks | Odczytaj i skonfiguruj zasady niestandardowe w Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Uprawnienia administratora rozliczeń

Może wykonywać typowe zadania związane z rozliczeniami, takie jak aktualizowanie informacji o płatności.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Organization/Basic/Update | Aktualizuj podstawowe właściwości organizacji w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Commerce. rozliczenia/allEntities/allTasks | Zarządzanie wszystkimi aspektami rozliczeń. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="cloud-application-administrator-permissions"></a>Uprawnienia administratora aplikacji w chmurze

Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa oraz zarządzać nimi, z wyjątkiem serwera proxy aplikacji.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. katalog/aplikacje/odbiorcy/aktualizacja | Aktualizowanie właściwości Applications. odbiorców w Azure Active Directory. |
| Microsoft. katalog/aplikacje/uwierzytelnianie/aktualizacja | Aktualizowanie właściwości Applications. Authentication w Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Zaktualizuj podstawowe właściwości aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/Create | Twórz aplikacje w Azure Active Directory. |
| Microsoft. Directory/Applications/Credentials/Update | Zaktualizuj Właściwość Applications. Credentials w Azure Active Directory. |
| Microsoft. katalog/aplikacje/usuwanie | Usuń aplikacje w Azure Active Directory. |
| Microsoft. Directory/Applications/Owners/Update | Aktualizowanie właściwości Applications. Owners w Azure Active Directory. |
| Microsoft. Directory/Applications/Permissions/Update | Aktualizowanie właściwości Applications. Permissions w Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Update | Zaktualizuj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Create | Utwórz appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Update | Aktualizacja appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Delete | Usuń appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Create | Utwórz zasady w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Read | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Update | Zaktualizuj Właściwość policies. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Delete | Usuń zasady w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/właściciele/odczyt | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/właściciele/aktualizacja | Zaktualizuj Właściwość policies. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/policyAppliedTo/odczyt | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/odbiorcy/aktualizacja | Aktualizacja właściwości serviceprincipals. odbiorca w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Authentication/Update | Aktualizacja właściwości serviceprincipals. Authentication w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Update | Zaktualizuj podstawowe właściwości obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Create | Tworzenie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Credentials/Update | Aktualizacja właściwości serviceprincipals. Credentials w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Delete | Usuwanie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Update | Aktualizacja właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Permissions/Update | Aktualizacja właściwości serviceprincipals. Permissions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Update | Aktualizacja właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Standard/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="cloud-device-administrator-permissions"></a>Uprawnienia administratora urządzenia w chmurze

Pełny dostęp do zarządzania urządzeniami w usłudze Azure AD.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/bitlockerKeys/Key/Read | Odczytywanie obiektów i właściwości klucza funkcji BitLocker w Azure Active Directory. |
| Microsoft. katalog/urządzenia/usuwanie | Usuń urządzenia w Azure Active Directory. |
| Microsoft. Directory/Devices/Disable | Wyłącz urządzenia w Azure Active Directory. |
| Microsoft. Directory/devices/Włącz | Włącz urządzenia w Azure Active Directory. |
| Microsoft. Directory/Devices/extensionAttributes/Update | Zaktualizuj wszystkie wartości właściwości Devices. extensionAttributes w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |

### <a name="global-administrator-permissions"></a>Uprawnienia administratora globalnego

Może zarządzać wszystkimi aspektami usługi Azure AD i usługami firmy Microsoft, które korzystają z tożsamości usługi Azure AD.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. cloudAppSecurity/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. AAD. cloudAppSecurity. |
| Microsoft. Directory/administrativeUnits/allProperties/allTasks | Tworzenie i usuwanie administrativeUnits oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/Applications/allProperties/allTasks | Tworzenie i usuwanie aplikacji oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/allProperties/allTasks | Tworzenie i usuwanie appRoleAssignments oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/bitlockerKeys/Key/Read | Odczytywanie obiektów i właściwości klucza funkcji BitLocker w Azure Active Directory. |
| Microsoft. Directory/Contacts/allProperties/allTasks | Tworzenie i usuwanie kontaktów oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/kontrakty/allProperties/allTasks | Tworzenie i usuwanie kontraktów oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/Devices/allProperties/allTasks | Tworzenie i usuwanie urządzeń oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/allProperties/allTasks | Tworzenie i usuwanie directoryRoles oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/directoryRoleTemplates/allProperties/allTasks | Tworzenie i usuwanie directoryRoleTemplates oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/domen/allProperties/allTasks | Tworzenie i usuwanie domen oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/entitlementManagement/allProperties/allTasks | Tworzenie i usuwanie zasobów oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure AD uprawnienia zarządzania. |
| Microsoft. Directory/Groups/allProperties/allTasks | Tworzenie i usuwanie grup oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/groupsAssignableToRoles/allProperties/Update | Aktualizacja grup z właściwością isAssignableToRole ustawioną na wartość true w Azure Active Directory. |
| Microsoft. Directory/groupsAssignableToRoles/Create | Utwórz grupy z właściwością isAssignableToRole ustawioną na wartość true w Azure Active Directory. |
| Microsoft. Directory/groupsAssignableToRoles/Delete | Usuń grupy z właściwością isAssignableToRole ustawioną na wartość true w Azure Active Directory. |
| Microsoft. Directory/groupSettings/allProperties/allTasks | Tworzenie i usuwanie groupSettings oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/allProperties/allTasks | Tworzenie i usuwanie groupSettingTemplates oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/loginTenantBranding/allProperties/allTasks | Tworzenie i usuwanie loginTenantBranding oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/elementu oauth2permissiongrants/allProperties/allTasks | Tworzenie i usuwanie elementu oauth2permissiongrants oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/Organization/allProperties/allTasks | Tworzenie i usuwanie organizacji, a ponadto odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/policies/allProperties/allTasks | Tworzenie i usuwanie zasad oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/roleAssignments/allProperties/allTasks | Tworzenie i usuwanie roleAssignments oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/roleDefinitions/allProperties/allTasks | Tworzenie i usuwanie roleDefinitions oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | Tworzenie i usuwanie scopedRoleMemberships oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/ServiceAction/activateService | Może wykonać akcję usługi Activateservice w Azure Active Directory |
| Microsoft. Directory/ServiceAction/disableDirectoryFeature | Może wykonać akcję usługi Disabledirectoryfeature w Azure Active Directory |
| Microsoft. Directory/ServiceAction/enableDirectoryFeature | Może wykonać akcję usługi Enabledirectoryfeature w Azure Active Directory |
| Microsoft. Directory/ServiceAction/getAvailableExtentionProperties | Może wykonać akcję usługi Getavailableextentionproperties w Azure Active Directory |
| Microsoft. Directory/serviceprincipals/allProperties/allTasks | Tworzenie i usuwanie obiektów serviceprincipals oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Directory/subscribedSkus/allProperties/allTasks | Tworzenie i usuwanie subscribedSkus oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/Users/allProperties/allTasks | Tworzenie i usuwanie użytkowników oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. directorySync/allEntities/allTasks | Wykonaj wszystkie akcje w Azure AD Connect. |
| Microsoft. AAD. identityProtection/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. AAD. identityProtection. |
| Microsoft. AAD. privilegedIdentityManagement/allEntities/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. privilegedIdentityManagement. |
| Microsoft. Azure. advancedThreatProtection/allEntities/odczyt | Odczytaj wszystkie zasoby w Microsoft. Azure. advancedThreatProtection. |
| Microsoft. Azure. informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami Azure Information Protection. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Commerce. rozliczenia/allEntities/allTasks | Zarządzanie wszystkimi aspektami rozliczeń. |
| Microsoft. Intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| Microsoft. Office 365. zgodnośćmanager/allEntities/allTasks | Zarządzanie wszystkimi aspektami programu Office 365 — Menedżer zgodności |
| Microsoft. Office 365. desktopAnalytics/allEntities/allTasks | Zarządzaj wszystkimi aspektami analizy pulpitu. |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| Microsoft. 365. skrytka/allEntities/allTasks | Zarządzaj wszystkimi aspektami pakietu Office 365 Skrytka klienta |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. messageCenter/securityMessages/odczyt | Przeczytaj securityMessages w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. protectionCenter/allEntities/allTasks | Zarządzaj wszystkimi aspektami pakietu Office 365 Protection Center. |
| Microsoft. Office 365. securityComplianceCenter/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. 365. securityComplianceCenter. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office. SharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. Office 365. SharePoint. |
| Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Office 365. usageReports/allEntities/odczyt | Przeczytaj raporty dotyczące użycia pakietu Office 365. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. powerApps. dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami programu Dynamics 365. |
| Microsoft. powerApps. powerBI/allEntities/allTasks | Zarządzaj wszystkimi aspektami Power BI. |
| Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/odczyt | Odczytaj wszystkie zasoby w Microsoft. Windows. defenderAdvancedThreatProtection. |

### <a name="compliance-administrator-permissions"></a>Uprawnienia administratora zgodności

Może odczytywać i zarządzać konfiguracją zgodności i raportami w usłudze Azure AD i Microsoft 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Directory/entitlementManagement/allProperties/odczyt | Odczytaj wszystkie właściwości w temacie Zarządzanie prawami usługi Azure AD. |
| Microsoft. Office 365. zgodnośćmanager/allEntities/allTasks | Zarządzanie wszystkimi aspektami programu Office 365 — Menedżer zgodności |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="compliance-data-administrator-permissions"></a>Uprawnienia administratora danych zgodności

Tworzy i zarządza zawartością zgodności.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory. cloudAppSecurity/allEntities/allTasks | Odczytaj i skonfiguruj Microsoft Cloud App Security. |
| Microsoft. Azure. informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami Azure Information Protection. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Office 365. zgodnośćmanager/allEntities/allTasks | Zarządzanie wszystkimi aspektami programu Office 365 — Menedżer zgodności |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="conditional-access-administrator-permissions"></a>Uprawnienia administratora dostępu warunkowego

Może zarządzać możliwościami dostępu warunkowego.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/policies/conditionalAccess/Basic/Read | Odczytaj Właściwość zasad. conditionalAccess w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/Basic/Update | Zaktualizuj Właściwość policies. conditionalAccess w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/Create | Utwórz zasady w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/Delete | Usuń zasady w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/właściciele/odczyt | Odczytaj Właściwość zasad. conditionalAccess w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/właściciele/aktualizacja | Zaktualizuj Właściwość policies. conditionalAccess w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/policiesAppliedTo/odczyt | Odczytaj Właściwość zasad. conditionalAccess w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/tenantDefault/Update | Zaktualizuj Właściwość policies. conditionalAccess w Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Uprawnienia administratora usługi CRM

Może zarządzać wszystkimi aspektami produktu Dynamics 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. powerApps. dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami programu Dynamics 365. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="customer-lockbox-access-approver-permissions"></a>Uprawnienia osoby zatwierdzającej dostęp skrytki klienta

Może zatwierdzić żądania pomocy technicznej firmy Microsoft w celu uzyskania dostępu do danych organizacji klienta.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. 365. skrytka/allEntities/allTasks | Zarządzaj wszystkimi aspektami pakietu Office 365 Skrytka klienta |

### <a name="desktop-analytics-administrator-permissions"></a>Uprawnienia administratora usługi Desktop Analytics

Może zarządzać narzędziami do analizy pulpitu i dostosowywania pakietu Office &ymi usługami zasad. W przypadku usługi Desktop Analytics obejmuje to możliwość wyświetlania spisu zasobów, tworzenia planów wdrażania, wyświetlania stanu wdrożenia i kondycji. Ta rola umożliwia użytkownikom zarządzanie zasadami pakietu Office w przypadku dostosowywania pakietu Office & Policy Service.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. desktopAnalytics/allEntities/allTasks | Zarządzaj wszystkimi aspektami analizy pulpitu. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="device-administrators-permissions"></a>Uprawnienia administratorów urządzeń

Użytkownicy przypisani do tej roli są dodawani do lokalnej grupy administratorów na urządzeniach dołączonych do usługi Azure AD.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/groupSettings/Basic/Read | Zapoznaj się z podstawowymi właściwościami groupSettings w Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read | Zapoznaj się z podstawowymi właściwościami groupSettingTemplates w Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Uprawnienia czytelnika katalogu
Może odczytywać podstawowe informacje o katalogu. Do udzielania dostępu do aplikacji nieprzeznaczonych dla użytkowników.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/administrativeUnits/Basic/Read | Zapoznaj się z podstawowymi właściwościami administrativeUnits w Azure Active Directory. |
| Microsoft. Directory/administrativeUnits/Members/odczyt | Przeczytaj Właściwość administrativeUnits. Members w Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Read | Zapoznaj się z podstawowymi właściwościami aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/Owners/Read | Odczytywanie właściwości Applications. Owners w Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Read | Odczytaj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Read | Zapoznaj się z podstawowymi właściwościami kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/memberOf/odczyt | Przeczytaj Właściwość Contacts. memberOf w Azure Active Directory. |
| Microsoft. Directory/kontrakts/Basic/Read | Zapoznaj się z podstawowymi właściwościami umów w Azure Active Directory. |
| Microsoft. Directory/Devices/podstawowa/odczytana | Zapoznaj się z podstawowymi właściwościami urządzeń w Azure Active Directory. |
| Microsoft. Directory/Devices/memberOf/odczyt | Odczytaj właściwości Devices. memberOf w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredOwners/Read | Odczytaj Właściwość Devices. registeredOwners w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredUsers/Read | Odczytaj Właściwość Devices. registeredUsers w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Basic/Read | Zapoznaj się z podstawowymi właściwościami directoryRoles w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/eligibleMembers/odczyt | Odczytaj Właściwość directoryRoles. eligibleMembers w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Members/odczyt | Przeczytaj Właściwość directoryRoles. Members w Azure Active Directory. |
| Microsoft. Directory/domen/podstawowa/odczytana | Zapoznaj się z podstawowymi właściwościami domen w Azure Active Directory. |
| Microsoft. Directory/Groups/appRoleAssignments/Read | Odczytaj właściwości groups. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Read | Zapoznaj się z podstawowymi właściwościami grup w Azure Active Directory. |
| Microsoft. Directory/Groups/memberOf/Read | Odczytaj właściwości groups. memberOf w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Read | Odczytywanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Read | Odczytywanie właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Read | Odczytywanie właściwości groups. Settings w Azure Active Directory. |
| Microsoft. Directory/groupSettings/Basic/Read | Zapoznaj się z podstawowymi właściwościami groupSettings w Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read | Zapoznaj się z podstawowymi właściwościami groupSettingTemplates w Azure Active Directory. |
| Microsoft. Directory/elementu oauth2permissiongrants/Basic/Read | Zapoznaj się z podstawowymi właściwościami elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/organizacja/podstawowa/odczytana | Odczytaj podstawowe właściwości organizacji w Azure Active Directory. |
| Microsoft. Directory/Organization/trustedCAsForPasswordlessAuth/Read | Przeczytaj Właściwość Organization. trustedCAsForPasswordlessAuth w Azure Active Directory. |
| Microsoft. Directory/roleAssignments/Basic/Read | Zapoznaj się z podstawowymi właściwościami roleAssignments w Azure Active Directory. |
| Microsoft. Directory/roleDefinitions/Basic/Read | Zapoznaj się z podstawowymi właściwościami roleDefinitions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Read | Odczytywanie właściwości serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Read | Odczytywanie właściwości serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Read | Zapoznaj się z podstawowymi właściwościami obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/memberOf/Read | Odczytywanie właściwości serviceprincipals. memberOf w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/elementu oauth2permissiongrants/Basic/Read | Odczytywanie właściwości serviceprincipals. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/ownedObjects/Read | Odczytywanie właściwości serviceprincipals. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Read | Odczytywanie właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Read | Odczytywanie właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/subscribedSkus/Basic/Read | Zapoznaj się z podstawowymi właściwościami subscribedSkus w Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Read | Odczytywanie właściwości users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Read | Zapoznaj się z podstawowymi właściwościami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/directReports/Read | Odczytywanie właściwości users. directReports w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Odczytaj | Odczytywanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/memberOf/odczyt | Odczytaj właściwości users. memberOf w Azure Active Directory. |
| Microsoft. Directory/Users/elementu oauth2permissiongrants/Basic/Read | Odczytywanie właściwości users. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/Users/ownedDevices/Read | Odczytywanie właściwości users. ownedDevices w Azure Active Directory. |
| Microsoft. Directory/Users/ownedObjects/Read | Odczytywanie właściwości users. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/Users/registeredDevices/Read | Odczytywanie właściwości users. registeredDevices w Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Uprawnienia kont synchronizacji katalogów

Używane przez usługę Azure AD Connect.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Organization/dirSync/Update | Zaktualizuj Właściwość Organization. dirSync w Azure Active Directory. |
| Microsoft. katalog/zasady/tworzenie | Utwórz zasady w Azure Active Directory. |
| Microsoft. Directory/zasady/usuwanie | Usuń zasady w Azure Active Directory. |
| Microsoft. katalog/zasady/podstawowe/odczyt | Zapoznaj się z podstawowymi właściwościami zasad w Azure Active Directory. |
| Microsoft. katalog/zasady/podstawowa/aktualizacja | Zaktualizuj podstawowe właściwości zasad w Azure Active Directory. |
| Microsoft. katalog/zasady/właściciele/odczyt | Odczytywanie właściwości policies. Owners w Azure Active Directory. |
| Microsoft. katalog/zasady/właściciele/aktualizacja | Aktualizowanie właściwości policies. Owners w Azure Active Directory. |
| Microsoft. Directory/policies/policiesAppliedTo/Read | Odczytaj Właściwość zasad. policiesAppliedTo w Azure Active Directory. |
| Microsoft. Directory/policies/tenantDefault/Update | Zaktualizuj Właściwość policies. tenantDefault w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Read | Odczytywanie właściwości serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Read | Odczytywanie właściwości serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/odbiorcy/aktualizacja | Aktualizacja właściwości serviceprincipals. odbiorca w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Authentication/Update | Aktualizacja właściwości serviceprincipals. Authentication w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Read | Zapoznaj się z podstawowymi właściwościami obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Update | Zaktualizuj podstawowe właściwości obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Create | Tworzenie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Credentials/Update | Aktualizacja właściwości serviceprincipals. Credentials w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/memberOf/Read | Odczytywanie właściwości serviceprincipals. memberOf w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/elementu oauth2permissiongrants/Basic/Read | Odczytywanie właściwości serviceprincipals. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Read | Odczytywanie właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Update | Aktualizacja właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/ownedObjects/Read | Odczytywanie właściwości serviceprincipals. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Permissions/Update | Aktualizacja właściwości serviceprincipals. Permissions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Read | Odczytywanie właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Update | Aktualizacja właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. directorySync/allEntities/allTasks | Wykonaj wszystkie akcje w Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Uprawnienia autorów katalogów

Może odczytywać & pisać podstawowe informacje o katalogu. Do udzielania dostępu do aplikacji nieprzeznaczonych dla użytkowników.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Zaktualizuj Właściwość groups. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Groups/assignLicense | Zarządzanie licencjami w grupach w Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Zaktualizuj podstawowe właściwości dla grup w Azure Active Directory.  |
| Microsoft. katalog/grupy/Klasyfikacja/aktualizacja | Zaktualizuj Właściwość klasyfikacji grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Utwórz grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/GroupType/Update | Zaktualizuj Właściwość GroupType grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizacja właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/reprocessLicenseAssignment | Przetwórz ponownie przypisania licencji dla grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/securityEnabled/Update | Zaktualizuj Właściwość secutiryEnabled grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizowanie właściwości groups. Settings w Azure Active Directory. |
| Microsoft. Directory/Groups/Visibility/Update | Zaktualizuj Właściwość widoczności grupy |
| Microsoft. Directory/groupSettings/Basic/Update | Zaktualizuj podstawowe właściwości groupSettings w Azure Active Directory. |
| Microsoft. Directory/groupSettings/Create | Utwórz groupSettings w Azure Active Directory.. |
| Microsoft. Directory/groupSettings/Delete | Usuń groupSettings w Azure Active Directory. |
| Microsoft. Directory/elementu oauth2permissiongrants/Basic/Update | Zaktualizuj podstawowe właściwości elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/elementu oauth2permissiongrants/Create | Utwórz elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/synchronizationCredentials/Manage | Zarządzaj wpisami tajnymi i poświadczeniami aprowizacji aplikacji. |
| Microsoft. Directory/serviceprincipals/synchronizationJobs/Manage | Uruchamianie, ponowne uruchamianie i wstrzymywanie zadań synchronizacji aprowizacji aplikacji. |
| Microsoft. Directory/serviceprincipals/synchronizationSchema/Manage | Twórz i Zarządzaj zadaniami i schematami synchronizacji aprowizacji aplikacji. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Zaktualizuj Właściwość Users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/assignLicense | Zarządzanie licencjami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Update | Zaktualizuj podstawowe właściwości użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Create | Tworzenie użytkowników w usłudze Azure Active Directory. |
| Microsoft. Directory/Users/Disable | Wyłącz konto użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/Enable | Włączanie konta użytkownika w Azure Active Directory |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory, wymaganie od użytkowników ponownego uwierzytelnienia przy następnym logowaniu |
| Microsoft. Directory/Users/Manager/Update | Aktualizowanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/reprocessLicenseAssignment | Przetwórz ponownie przypisania licencji dla użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/userPrincipalName/Update | Zaktualizuj Właściwość Users. userPrincipalName w Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Uprawnienia administratora usługi Exchange

Może zarządzać wszystkimi aspektami produktu Exchange.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Odczytaj ukryte elementy członkowskie grupy |
| Microsoft. Directory/groups. Unified/Basic/Update | Aktualizuj podstawowe właściwości grup Microsoft 365. |
| Microsoft. Directory/groups. Unified/Create | Utwórz grupy Microsoft 365. |
| Microsoft. Directory/groups. Unified/Delete | Usuń grupy Microsoft 365. |
| Microsoft. Directory/groups. Unified/Restore | Przywracanie grup Microsoft 365 |
| Microsoft. Directory/groups. Unified/Members/Update | Aktualizowanie członkostwa w grupach Microsoft 365. |
| Microsoft. Directory/groups. Unified/właściciele/aktualizacja | Aktualizowanie własności grup Microsoft 365. |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| Microsoft. Office 365. Network/Performance/allProperties/Read | Odczytaj strony wydajności sieci w centrum administracyjnym Microsoft 365. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Office 365. usageReports/allEntities/allProperties/Read | Przeczytaj raporty dotyczące użycia pakietu Office 365. |
| Microsoft. 365. webports/allEntities/Standard/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="external-id-user-flow-administrator-permissions"></a>Identyfikator zewnętrzny — uprawnienia administratora przepływu użytkownika

Twórz wszystkie aspekty przepływów użytkowników i zarządzaj nimi.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. B2C/userFlows/allTasks | Odczytywanie i Konfigurowanie przepływów użytkowników w Azure Active Directory B2C. |

### <a name="external-id-user-flow-attribute-administrator-permissions"></a>Identyfikator zewnętrzny — uprawnienia administratora atrybutu przepływu użytkownika

Utwórz schemat atrybutów dostępny dla wszystkich przepływów użytkowników i Zarządzaj nim.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. B2C/userAttributes/allTasks | Odczytywanie i Konfigurowanie atrybutów użytkownika w Azure Active Directory B2C. |

### <a name="external-identity-provider-administrator-permissions"></a>Uprawnienia administratora zewnętrznego dostawcy tożsamości

Skonfiguruj dostawców tożsamości do użycia w Federacji bezpośredniej.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. B2C/skojarzeni/allTasks | Odczytaj i skonfiguruj dostawców tożsamości w Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Globalne uprawnienia czytelnika
Może odczytywać wszystko, co Administrator globalny może, ale nie edytować niczego.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Opis roli](#global-reader) powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Commerce. rozliczenia/allEntities/odczyt    | Przeczytaj wszystkie aspekty rozliczeń. |
| Microsoft. Directory/administrativeUnits/Basic/Read    | Zapoznaj się z podstawowymi właściwościami administrativeUnits w Azure Active Directory. |
| Microsoft. Directory/administrativeUnits/Members/odczyt    | Przeczytaj Właściwość administrativeUnits. Members w Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Read    | Zapoznaj się z podstawowymi właściwościami aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/Owners/Read    | Odczytywanie właściwości Applications. Owners w Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Read    | Odczytaj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/bitlockerKeys/Key/Read | Odczytywanie obiektów i właściwości klucza funkcji BitLocker w Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Read    | Zapoznaj się z podstawowymi właściwościami kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/memberOf/odczyt    | Przeczytaj Właściwość Contacts. memberOf w Azure Active Directory. |
| Microsoft. Directory/kontrakts/Basic/Read    | Zapoznaj się z podstawowymi właściwościami umów w Azure Active Directory. |
| Microsoft. Directory/Devices/podstawowa/odczytana    | Zapoznaj się z podstawowymi właściwościami urządzeń w Azure Active Directory. |
| Microsoft. Directory/Devices/memberOf/odczyt    | Odczytaj właściwości Devices. memberOf w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredOwners/Read    | Odczytaj Właściwość Devices. registeredOwners w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredUsers/Read    | Odczytaj Właściwość Devices. registeredUsers w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Basic/Read    | Zapoznaj się z podstawowymi właściwościami directoryRoles w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/eligibleMembers/odczyt    | Odczytaj Właściwość directoryRoles. eligibleMembers w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Members/odczyt    | Przeczytaj Właściwość directoryRoles. Members w Azure Active Directory. |
| Microsoft. Directory/domen/podstawowa/odczytana    | Zapoznaj się z podstawowymi właściwościami domen w Azure Active Directory. |
| Microsoft. Directory/entitlementManagement/allProperties/odczyt | Odczytaj wszystkie właściwości w temacie Zarządzanie prawami usługi Azure AD. |
| Microsoft. Directory/Groups/appRoleAssignments/Read    | Odczytaj właściwości groups. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Read    | Zapoznaj się z podstawowymi właściwościami grup w Azure Active Directory. |
| Microsoft. Directory/Groups/hiddenMembers/Read    | Odczytaj właściwości groups. hiddenMembers w Azure Active Directory. |
| Microsoft. Directory/Groups/memberOf/Read    | Odczytaj właściwości groups. memberOf w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Read    | Odczytywanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Read    | Odczytywanie właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Read    | Odczytywanie właściwości groups. Settings w Azure Active Directory. |
| Microsoft. Directory/groupSettings/Basic/Read    | Zapoznaj się z podstawowymi właściwościami groupSettings w Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read    | Zapoznaj się z podstawowymi właściwościami groupSettingTemplates w Azure Active Directory. |
| Microsoft. Directory/elementu oauth2permissiongrants/Basic/Read    | Zapoznaj się z podstawowymi właściwościami elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/organizacja/podstawowa/odczytana    | Odczytaj podstawowe właściwości organizacji w Azure Active Directory. |
| Microsoft. Directory/Organization/trustedCAsForPasswordlessAuth/Read    | Przeczytaj Właściwość Organization. trustedCAsForPasswordlessAuth w Azure Active Directory. |
| Microsoft. katalog/zasady/standardowe/odczyt    | Odczytaj standardowe zasady w Azure Active Directory. |
| Microsoft. Directory/roleAssignments/Basic/Read    | Zapoznaj się z podstawowymi właściwościami roleAssignments w Azure Active Directory. |
| Microsoft. Directory/roleDefinitions/Basic/Read    | Zapoznaj się z podstawowymi właściwościami roleDefinitions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Read    | Odczytywanie właściwości serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Read    | Odczytywanie właściwości serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Read    | Zapoznaj się z podstawowymi właściwościami obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/memberOf/Read    | Odczytywanie właściwości serviceprincipals. memberOf w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/elementu oauth2permissiongrants/Basic/Read    | Odczytywanie właściwości serviceprincipals. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/ownedObjects/Read    | Odczytywanie właściwości serviceprincipals. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Read    | Odczytywanie właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Read    | Odczytywanie właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt    | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Directory/subscribedSkus/Basic/Read    | Zapoznaj się z podstawowymi właściwościami subscribedSkus w Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Read    | Odczytywanie właściwości users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Read    | Zapoznaj się z podstawowymi właściwościami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/directReports/Read    | Odczytywanie właściwości users. directReports w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Odczytaj    | Odczytywanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/memberOf/odczyt    | Odczytaj właściwości users. memberOf w Azure Active Directory. |
| Microsoft. Directory/Users/elementu oauth2permissiongrants/Basic/Read    | Odczytywanie właściwości users. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/Users/ownedDevices/Read    | Odczytywanie właściwości users. ownedDevices w Azure Active Directory. |
| Microsoft. Directory/Users/ownedObjects/Read    | Odczytywanie właściwości users. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/Users/registeredDevices/Read    | Odczytywanie właściwości users. registeredDevices w Azure Active Directory. |
| Microsoft. Directory/Users/strongAuthentication/Read    | Odczytywanie właściwości silnego uwierzytelniania, takich jak informacje o poświadczeniach usługi MFA. |
| Microsoft. Office 365. Exchange/allEntities/Read    | Przeczytaj wszystkie aspekty usługi Exchange Online. |
| Microsoft. Office 365. messageCenter/messages/Read    | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. messageCenter/securityMessages/odczyt    | Przeczytaj securityMessages w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. Network/Performance/allProperties/Read | Odczytaj strony wydajności sieci w centrum administracyjnym Microsoft 365. |
| Microsoft. Office 365. protectionCenter/allEntities/odczyt    | Przeczytaj wszystkie aspekty Centrum ochrony pakietu Office 365. |
| Microsoft. Office 365. securityComplianceCenter/allEntities/odczyt    | Odczytaj wszystkie standardowe właściwości w Microsoft. 365. securityComplianceCenter. |
| Microsoft. Office 365. usageReports/allEntities/odczyt    | Przeczytaj raporty dotyczące użycia pakietu Office 365. |
| Microsoft. 365. webports/allEntities/Standard/Read    | Odczytywanie właściwości standardowych wszystkich zasobów w Microsoft. 365. webport. |

### <a name="groups-administrator-permissions"></a>Uprawnienia administratora grup
Może zarządzać wszystkimi aspektami grup i ustawień grup, takimi jak zasady nazewnictwa i wygasania.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Groups/Basic/Read | Odczytywanie właściwości standardowych grup w Azure Active Directory.  |
| Microsoft. Directory/Groups/Basic/Update | Zaktualizuj podstawowe właściwości dla grup w Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Utwórz grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/createAsOwner | Utwórz grupy w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/Groups/Delete | Usuń grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Odczytaj właściwości groups. hiddenMembers w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizacja właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/Restore | Przywróć grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizowanie właściwości groups. Settings w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="guest-inviter-permissions"></a>Uprawnienia osoby zapraszające gościa
Może zapraszać użytkowników-Gości niezależnie od ustawienia "członkowie mogą zapraszać Gości".

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Users/appRoleAssignments/Read | Odczytywanie właściwości users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Read | Zapoznaj się z podstawowymi właściwościami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/directReports/Read | Odczytywanie właściwości users. directReports w Azure Active Directory. |
| Microsoft. Directory/Users/inviteGuest | Zapraszanie użytkowników-Gości w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Odczytaj | Odczytywanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/memberOf/odczyt | Odczytaj właściwości users. memberOf w Azure Active Directory. |
| Microsoft. Directory/Users/elementu oauth2permissiongrants/Basic/Read | Odczytywanie właściwości users. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/Users/ownedDevices/Read | Odczytywanie właściwości users. ownedDevices w Azure Active Directory. |
| Microsoft. Directory/Users/ownedObjects/Read | Odczytywanie właściwości users. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/Users/registeredDevices/Read | Odczytywanie właściwości users. registeredDevices w Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Uprawnienia administratora pomocy technicznej

Można resetować hasła dla administratorów nie będących administratorami i pomocą techniczną.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Odczytaj Właściwość Devices. bitLockerRecoveryKeys w Azure Active Directory. |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="hybrid-identity-administrator-permissions"></a>Uprawnienia administratora tożsamości hybrydowej

Może zarządzać usługą AD w usłudze Azure AD — Inicjowanie obsługi administracyjnej i ustawienia federacyjne. 

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. katalog/aplikacje/odbiorcy/aktualizacja  | Aktualizowanie właściwości Applications. odbiorców w Azure Active Directory. |
| Microsoft. katalog/aplikacje/uwierzytelnianie/aktualizacja | Aktualizowanie właściwości Applications. Authentication w Azure Active Directory.  |
| Microsoft. Directory/Applications/Basic/Update | Zaktualizuj podstawowe właściwości aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/Create | Twórz aplikacje w Azure Active Directory. |
| Microsoft. Directory/Applications/Credentials/Update | Zaktualizuj Właściwość Applications. Credentials w Azure Active Directory. |
| Microsoft. katalog/aplikacje/usuwanie | Usuń aplikacje w Azure Active Directory. |
| Microsoft. Directory/Applications/Owners/Update | Aktualizowanie właściwości Applications. Owners w Azure Active Directory. |
| Microsoft. Directory/Applications/Permissions/Update | Aktualizowanie właściwości Applications. Permissions w Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Update | Zaktualizuj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/applicationTemplates/wystąpienie | Tworzenie wystąpienia aplikacji galerii z szablonów aplikacji. |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/cloudProvisioning/allProperties/allTasks | Odczytaj i skonfiguruj wszystkie właściwości usługi Azure AD Cloud Provisioning. |
| Microsoft. Directory/domen/allProperties/odczyt | Odczytaj wszystkie właściwości domen. |
| Microsoft. katalog/domeny/Federacja/aktualizacja | Zaktualizuj Właściwość Federacji domen. |
| Microsoft. Directory/Organization/dirSync/Update | Zaktualizuj Właściwość Organization. dirSync w Azure Active Directory. |
| Microsoft. Directory/provisioningLogs/allProperties/odczyt | Odczytaj wszystkie właściwości dzienników aprowizacji. |
| Microsoft. Directory/serviceprincipals/odbiorcy/aktualizacja | Aktualizacja właściwości serviceprincipals. odbiorca w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Authentication/Update | Aktualizacja właściwości serviceprincipals. Authentication w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Update | Zaktualizuj podstawowe właściwości obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Create | Tworzenie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Credentials/Update | Aktualizacja właściwości serviceprincipals. Credentials w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Delete | Usuwanie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Update | Aktualizacja właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Permissions/Update | Aktualizacja właściwości serviceprincipals. Permissions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Update | Aktualizacja właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/synchronizationJobs/Manage | Zarządzanie wszystkimi aspektami zadań synchronizacji w usłudze Azure AD. |
| Microsoft. Directory/serviceprincipals/synchronizationSchema/Manage | Zarządzaj wszystkimi aspektami schematu synchronizacji w usłudze Azure AD. |
| Microsoft. Directory/serviceprincipals/synchronizationCredentials/Manage | Zarządzanie wszystkimi aspektami poświadczeń synchronizacji w usłudze Azure AD. |
| Microsoft. Directory/serviceprincipals/tag/Update | Aktualizacja właściwości serviceprincipals. tag w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="insights-administrator-permissions"></a>Uprawnienia administratora usługi Insights

Ma dostęp administracyjny w aplikacji Microsoft 365 Insights. 

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Insights/allEntities/allTasks | Zarządzaj wszystkimi aspektami szczegółowych informacji. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="insights-business-leader-permissions"></a>Uprawnienia lidera biznesowego usługi Insights

Umożliwia wyświetlanie i udostępnianie pulpitów nawigacyjnych i szczegółowych informacji za pośrednictwem aplikacji M365 Insights.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Insights/Reports/Read | Wyświetlanie raportów i pulpitów nawigacyjnych w aplikacji usługi Insights. |
| Microsoft. Insights/programy/Update | Wdrażaj programy i zarządzaj nimi w aplikacji usługi Insights. |

### <a name="intune-service-administrator-permissions"></a>Uprawnienia administratora usługi Intune

Może zarządzać wszystkimi aspektami produktu usługi Intune.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/bitlockerKeys/Key/Read | Odczytywanie obiektów i właściwości klucza funkcji BitLocker w Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizowanie podstawowych właściwości kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Utwórz kontakty w Azure Active Directory. |
| Microsoft. Directory/kontakty/usuwanie | Usuń kontakty w Azure Active Directory. |
| Microsoft. Directory/Devices/podstawowa/aktualizacja | Zaktualizuj podstawowe właściwości na urządzeniach w Azure Active Directory. |
| Microsoft. Directory/Devices/Create | Utwórz urządzenia w Azure Active Directory. |
| Microsoft. katalog/urządzenia/usuwanie | Usuń urządzenia w Azure Active Directory. |
| Microsoft. Directory/Devices/Disable | Wyłącz urządzenia w Azure Active Directory. |
| Microsoft. Directory/devices/Włącz | Włącz urządzenia w Azure Active Directory. |
| Microsoft. Directory/Devices/extensionAttributes/Update | Zaktualizuj wszystkie wartości właściwości Devices. extensionAttributes w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredOwners/Update | Zaktualizuj Właściwość Devices. registeredOwners w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredUsers/Update | Zaktualizuj Właściwość Devices. registeredUsers w Azure Active Directory. |
| Microsoft. Directory/deviceManagementPolicies/Standard/Read | Odczytywanie właściwości standardowych zasad aplikacji dotyczących zarządzania urządzeniami |
| Microsoft. Directory/deviceRegistrationPolicy/Standard/Read | Odczytywanie właściwości standardowych zasad rejestracji urządzeń |
| Microsoft. Directory/Groups/hiddenMembers/Read | Odczytaj właściwości groups. hiddenMembers w Azure Active Directory. |
| Microsoft. Directory/groups. Security/Basic/Update | Zaktualizuj podstawowe właściwości dla grup w Azure Active Directory. |
| Microsoft. Directory/groups. Security/klasyfikacyjn/Update | Zaktualizuj Właściwość klasyfikacji grup zabezpieczeń z wykluczeniem grup do przypisania ról |
| Microsoft. Directory/groups. Security/Create | Utwórz grupy w Azure Active Directory. |
| Microsoft. Directory/groups. Security/Delete | Usuń grupy w Azure Active Directory. |
| Microsoft. Directory/groups. Security/dynamicMembershipRule/Update | Zaktualizuj Właściwość dynamicMembershipRule grup zabezpieczeń z wykluczeniem grup do przypisania ról |
| Microsoft. Directory/groups. Security/GroupType/Update | Aktualizowanie właściwości typu grupy grup zabezpieczeń z wykluczeniem grup z możliwością przypisywania ról |
| Microsoft. Directory/groups. Security/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/groups. Security/Owners/Update | Aktualizacja właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/groups. Security/Visibility/Update | Aktualizacja właściwości widoczności grup zabezpieczeń z wykluczeniem grup z możliwością przypisania |
| Microsoft. Directory/Users/Basic/Update | Zaktualizuj podstawowe właściwości użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Update | Aktualizowanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Standard/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="kaizala-administrator-permissions"></a>Uprawnienia administratora usługi kaizala

Może zarządzać ustawieniami programu Microsoft usługi kaizala.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Odczytaj Microsoft 365 centrum administracyjnego. |

### <a name="license-administrator-permissions"></a>Uprawnienia administratora licencji

Może zarządzać licencjami produktów dla użytkowników i grup.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Users/assignLicense | Zarządzanie licencjami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/usageLocation/Update | Zaktualizuj Właściwość Users. usageLocation w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |

### <a name="lync-service-administrator-permissions"></a>Uprawnienia administratora usługi Lync

Może zarządzać wszystkimi aspektami produktu Skype dla firm.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Office 365. usageReports/allEntities/odczyt    | Przeczytaj raporty dotyczące użycia pakietu Office 365. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |


### <a name="message-center-privacy-reader-permissions"></a>Uprawnienia czytelnika do ochrony prywatności centrum wiadomości

Może odczytywać wpisy centrum wiadomości, komunikaty dotyczące prywatności danych, grupy, domeny i subskrypcje.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. messageCenter/securityMessages/odczyt | Przeczytaj securityMessages w Microsoft. 365. messageCenter. |

### <a name="message-center-reader-permissions"></a>Uprawnienia czytelnika centrum wiadomości
Może odczytywać wiadomości i aktualizacje dla swojej organizacji tylko w centrum wiadomości. 

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |

### <a name="modern-commerce-user-permissions"></a>Nowoczesne uprawnienia użytkownika handlowego
Może zarządzać zakupami komercyjnymi dla firmy, działu lub zespołu. 

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Commerce. rozliczenia/partnerzy/odczyt | Odczytaj Właściwość partnera Microsoft 365 rozliczeń. |
| Microsoft. Commerce. volumeLicenseServiceCenter/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Volume Licensing Service Center. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz i wyświetlaj własne bilety pomocy technicznej pakietu Office 365. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |


### <a name="network-administrator-permissions"></a>Uprawnienia administratora sieci
Może zarządzać lokalizacjami sieci i przeglądać szczegółowe informacje o projekcie sieci przedsiębiorstwa dla Microsoft 365 oprogramowania jako aplikacji usługi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Office 365. Network/Performance/allProperties/Read | Odczytaj strony wydajności sieci w centrum administracyjnym M365.  |
| Microsoft. Office 365. Network/Locations/allProperties/allTasks | Odczytaj i skonfiguruj właściwości lokalizacji sieciowych dla każdej lokalizacji. |

### <a name="office-apps-administrator-permissions"></a>Uprawnienia administratora aplikacji pakietu Office
Usługa umożliwia zarządzanie aplikacjami pakietu Office w chmurze, w tym zarządzaniem zasadami i ustawieniami, a także pozwala na wybór, usuwanie i publikowanie zawartości funkcji "co nowego" na urządzeniach użytkowników końcowych.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Office 365. userCommunication/allEntities/allTasks | Odczytuj i Aktualizuj informacje o nowościach. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="partner-tier1-support-permissions"></a>Uprawnienia do obsługi pomoc partnerów

Nie używaj — nie jest przeznaczony do użytku ogólnego.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Applications/appRoles/Update | Zarządzaj rolami aplikacji i Żądaj delegowanych uprawnień dla aplikacji. |
| Microsoft. katalog/aplikacje/odbiorcy/aktualizacja | Aktualizowanie odbiorców dla wszystkich typów aplikacji. |
| Microsoft. katalog/aplikacje/uwierzytelnianie/aktualizacja | Zaktualizuj uwierzytelnianie dla wszystkich typów aplikacji. |
| Microsoft. Directory/Applications/Basic/Update | Zaktualizuj podstawowe właściwości wszystkich typów aplikacji. |
| Microsoft. Directory/Applications/Credentials/Update | Zaktualizuj poświadczenia dla wszystkich typów aplikacji. |
| Microsoft. Directory/Applications/Owners/Update | Aktualizowanie właścicieli wszystkich typów aplikacji. |
| Microsoft. Directory/Applications/Permissions/Update | Aktualizuj uwidocznione uprawnienia i wymagane uprawnienia dla wszystkich typów aplikacji. |
| Microsoft. Directory/Applications/policies/Update | Zaktualizuj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizowanie podstawowych właściwości kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Utwórz kontakty w Azure Active Directory. |
| Microsoft. Directory/kontakty/usuwanie | Usuń kontakty w Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Utwórz grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Delete | Usuwanie grup, z wyłączeniem grupy przypisanej do roli |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizacja właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/Restore | Przywracanie usuniętych grup |
| Microsoft. Directory/elementu oauth2permissiongrants/allProperties/allTasks | Tworzenie i usuwanie dotacji do uwierzytelniania OAuth 2,0 oraz odczytywanie i aktualizowanie wszystkich właściwości |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update | Aktualizowanie przypisań ról głównych usługi |
| Microsoft. Directory/Users/assignLicense | Zarządzanie licencjami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Update | Zaktualizuj podstawowe właściwości użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Create | Dodawanie użytkowników |
| Microsoft. Directory/Users/Delete | Usuń użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Disable | Wyłącz użytkowników |
| Microsoft. Directory/Users/Enable | Włącz użytkowników |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Update | Aktualizowanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft. Directory/Users/Restore | Przywróć usuniętych użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/userPrincipalName/Update | Zaktualizuj Właściwość Users. userPrincipalName w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Standard/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="partner-tier2-support-permissions"></a>Uprawnienia do obsługi SVR partnerów

Nie używaj — nie jest przeznaczony do użytku ogólnego.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Applications/appRoles/Update | Zarządzaj rolami aplikacji i Żądaj delegowanych uprawnień dla aplikacji. |
| Microsoft. katalog/aplikacje/odbiorcy/aktualizacja | Aktualizowanie odbiorców dla wszystkich typów aplikacji. |
| Microsoft. katalog/aplikacje/uwierzytelnianie/aktualizacja | Zaktualizuj uwierzytelnianie dla wszystkich typów aplikacji. |
| Microsoft. Directory/Applications/Basic/Update | Zaktualizuj podstawowe właściwości wszystkich typów aplikacji. |
| Microsoft. Directory/Applications/Credentials/Update | Zaktualizuj poświadczenia dla wszystkich typów aplikacji. |
| Microsoft. Directory/Applications/Owners/Update | Aktualizowanie właścicieli wszystkich typów aplikacji. |
| Microsoft. Directory/Applications/Permissions/Update | Aktualizuj uwidocznione uprawnienia i wymagane uprawnienia dla wszystkich typów aplikacji. |
| Microsoft. Directory/Applications/policies/Update | Zaktualizuj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizowanie podstawowych właściwości kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Utwórz kontakty w Azure Active Directory. |
| Microsoft. Directory/kontakty/usuwanie | Usuń kontakty w Azure Active Directory. |
| Microsoft. Directory/domen/Basic/allTasks | Tworzenie i usuwanie domen oraz odczytywanie i aktualizowanie standardowych właściwości w Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Utwórz grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Delete | Usuń grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizowanie właścicieli grup, z wyłączeniem grup przypisanych do ról |
| Microsoft. Directory/Groups/Restore | Przywróć grupy w Azure Active Directory. |
| Microsoft. Directory/elementu oauth2permissiongrants/allProperties/allTasks | Tworzenie i usuwanie dotacji do uwierzytelniania OAuth 2,0 oraz odczytywanie i aktualizowanie wszystkich właściwości |
| Microsoft. Directory/Organization/Basic/Update | Aktualizuj podstawowe właściwości organizacji w Azure Active Directory. |
| Microsoft. Directory/roleAssignments/allProperties/allTasks | Tworzenie i usuwanie przypisań ról oraz odczytywanie i aktualizowanie wszystkich właściwości przypisywania ról |
| Microsoft. Directory/roleDefinitions/allProperties/allTasks | Tworzenie i usuwanie definicji ról oraz odczytywanie i aktualizowanie wszystkich właściwości |
| Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | Tworzenie i usuwanie scopedRoleMemberships oraz odczytywanie i aktualizowanie wszystkich właściwości |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update | Aktualizowanie przypisań ról głównych usługi |
| Microsoft. Directory/subscribedSkus/Standard/Read | Odczytywanie podstawowych właściwości subskrypcji |
| Microsoft. Directory/Users/assignLicense | Zarządzanie licencjami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Update | Zaktualizuj podstawowe właściwości użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Create | Dodawanie użytkowników |
| Microsoft. Directory/Users/Delete | Usuń użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Disable | Wyłącz użytkowników |
| Microsoft. Directory/Users/Enable | Włącz użytkowników |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Update | Aktualizowanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft. Directory/Users/Restore | Przywróć usuniętych użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/userPrincipalName/Update | Zaktualizuj Właściwość Users. userPrincipalName w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Standard/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="password-administrator-permissions"></a>Uprawnienia administratora hasła

Można resetować hasła dla administratorów nie będących administratorami i haseł.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="power-bi-service-administrator-permissions"></a>Uprawnienia administratora usługi Power BI

Może zarządzać wszystkimi aspektami produktu Power BI.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>
| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. powerApps. powerBI/allEntities/allTasks | Zarządzaj wszystkimi aspektami Power BI. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |


### <a name="power-platform-administrator-permissions"></a>Uprawnienia administratora platformy na platformie

Może tworzyć wszystkie aspekty usług Microsoft Dynamics 365, PowerApps i Power Automatyzuj oraz zarządzać nimi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>
| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami programu Dynamics 365. |
| Microsoft. Flow/allEntities/allTasks | Zarządzaj wszystkimi aspektami automatyzacji. |
| Microsoft. powerApps/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi PowerApps. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="printer-administrator-permissions"></a>Uprawnienia administratora drukarki

Może zarządzać wszystkimi aspektami drukarek i łączników drukarek.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>
| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. Print/allEntities/allProperties/allTasks | Tworzenie i usuwanie drukarek i łączników oraz odczytywanie i aktualizowanie wszystkich właściwości w programie Microsoft Print. |

### <a name="printer-technician-permissions"></a>Uprawnienia technika drukarki

Może rejestrować i wyrejestrować drukarki oraz aktualizować stan drukarki.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>
| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. Print/Connectors/allProperties/Read | Odczytaj wszystkie właściwości łączników w programie Microsoft Print. |
| Microsoft. Azure. Print/Prints/allProperties/Read | Odczytaj wszystkie właściwości drukarek w programie Microsoft Print. |
| Microsoft. Azure. Print/Prints/Basic/Update | Aktualizuj podstawowe właściwości drukarek w programie Microsoft Print. |
| Microsoft. Azure. Print/drukarki/Register | Zarejestruj drukarki w programie Microsoft Print. |
| Microsoft. Azure. Print/drukarki/Wyrejestruj | Wyrejestruj drukarki w programie Microsoft Print. |

### <a name="privileged-authentication-administrator-permissions"></a>Uprawnienia administratora uwierzytelniania uprzywilejowanego

Zezwolenie na wyświetlanie, ustawianie i Resetowanie informacji o metodach uwierzytelniania dla dowolnego użytkownika (administratora lub nie administratora).

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/strongAuthentication/Update | Zaktualizuj właściwości silnego uwierzytelniania, takie jak informacje o poświadczeniach usługi MFA. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w organizacji Microsoft 365. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |

### <a name="privileged-role-administrator-permissions"></a>Uprawnienia administratora ról uprzywilejowanych

Może zarządzać przypisaniami ról w usłudze Azure AD i wszystkimi aspektami Privileged Identity Management.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/groupsAssignableToRoles/allProperties/Update | Aktualizacja grup z właściwością isAssignableToRole ustawioną na wartość true w Azure Active Directory. |
| Microsoft. Directory/groupsAssignableToRoles/Create | Utwórz grupy z właściwością isAssignableToRole ustawioną na wartość true w Azure Active Directory. |
| Microsoft. Directory/groupsAssignableToRoles/Delete | Usuń grupy z właściwością isAssignableToRole ustawioną na wartość true w Azure Active Directory. |
| Microsoft. Directory/privilegedIdentityManagement/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. AAD. privilegedIdentityManagement. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/allTasks | Odczytaj i skonfiguruj Właściwość serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/elementu oauth2permissiongrants/allTasks | Odczytaj i skonfiguruj Właściwość serviceprincipals. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/administrativeUnits/allProperties/allTasks | Tworzenie jednostek administracyjnych (w tym członków) i zarządzanie nimi |
| Microsoft. Directory/roleAssignments/allProperties/allTasks | Tworzenie przypisań ról i zarządzanie nimi. |
| Microsoft. Directory/roleDefinitions/allProperties/allTasks | Utwórz definicje ról i zarządzaj nimi. |

### <a name="reports-reader-permissions"></a>Uprawnienia czytelnika raportów

Może odczytywać raporty logowania i inspekcji.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Office 365. usageReports/allEntities/odczyt | Przeczytaj raporty dotyczące użycia pakietu Office 365. |

### <a name="search-administrator-permissions"></a>Wyszukaj uprawnienia administratora

Może tworzyć wszystkie aspekty ustawień wyszukiwania firmy Microsoft i zarządzać nimi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. Search/allEntities/allProperties/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie wszystkich właściwości w Microsoft. Office 365. Search. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="search-editor-permissions"></a>Uprawnienia edytora wyszukiwania

Może tworzyć i zarządzać zawartością redakcyjną, taką jak zakładki, Q i AS, lokalizacje, floorplan.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. 365. Search/Content/allProperties/allTasks | Tworzenie i usuwanie zawartości oraz odczytywanie i aktualizowanie wszystkich właściwości w Microsoft. Office 365. Search. |

### <a name="security-administrator-permissions"></a>Uprawnienia administratora zabezpieczeń

Może odczytywać informacje o zabezpieczeniach i raporty oraz zarządzać konfiguracją w usłudze Azure AD i Microsoft 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Directory/Applications/policies/Update | Zaktualizuj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/bitlockerKeys/Key/Read | Odczytywanie obiektów i właściwości klucza funkcji BitLocker w Azure Active Directory. |
| Microsoft. Directory/entitlementManagement/allProperties/odczyt | Odczytaj wszystkie właściwości w temacie Zarządzanie prawami usługi Azure AD. |
| Microsoft. Directory/identityProtection/allProperties/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. identityProtection. |
| Microsoft. Directory/identityProtection/allProperties/Update | Zaktualizuj wszystkie zasoby w usłudze Microsoft. AAD. identityProtection. |
| Microsoft. katalog/zasady/podstawowa/aktualizacja | Zaktualizuj podstawowe właściwości zasad w Azure Active Directory. |
| Microsoft. katalog/zasady/tworzenie | Utwórz zasady w Azure Active Directory. |
| Microsoft. Directory/zasady/usuwanie | Usuń zasady w Azure Active Directory. |
| Microsoft. katalog/zasady/właściciele/aktualizacja | Aktualizowanie właściwości policies. Owners w Azure Active Directory. |
| Microsoft. Directory/policies/tenantDefault/Update | Zaktualizuj Właściwość policies. tenantDefault w Azure Active Directory. |
| Microsoft. Directory/privilegedIdentityManagement/allProperties/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. privilegedIdentityManagement. |
| Microsoft. Directory/serviceprincipals/policies/Update | Aktualizacja właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Office 365. protectionCenter/allEntities/odczyt | Przeczytaj wszystkie aspekty Centrum ochrony pakietu Office 365. |
| Microsoft. Office 365. protectionCenter/allEntities/Update | Zaktualizuj wszystkie zasoby w Microsoft. 365. protectionCenter. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="security-operator-permissions"></a>Uprawnienia operatora zabezpieczeń

Tworzy i zarządza zdarzeniami zabezpieczeń.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. advancedThreatProtection/allEntities/odczyt | Przeczytaj i skonfiguruj zaawansowaną ochronę przed zagrożeniami w usłudze Azure AD. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Odczytaj i skonfiguruj Microsoft Cloud App Security. |
| Microsoft. Directory/identityProtection/allProperties/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. identityProtection. |
| Microsoft. Directory/privilegedIdentityManagement/allProperties/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. privilegedIdentityManagement. |
| Microsoft. Directory/provisioningLogs/allProperties/odczyt | Odczytaj wszystkie właściwości dzienników aprowizacji. |
| Microsoft. Intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| Microsoft. Office 365. securityComplianceCenter/allEntities/allTasks | Przeczytaj i skonfiguruj Centrum zabezpieczeń & zgodności. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/odczyt | Odczytywanie i Konfigurowanie zaawansowanej ochrony przed zagrożeniami w usłudze Windows Defender. |


### <a name="security-reader-permissions"></a>Uprawnienia czytelnika zabezpieczeń

Może odczytywać informacje o zabezpieczeniach i raporty w usłudze Azure AD i Microsoft 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/bitlockerKeys/Key/Read | Odczytywanie obiektów i właściwości klucza funkcji BitLocker w Azure Active Directory. |
| Microsoft. Directory/entitlementManagement/allProperties/odczyt | Odczytaj wszystkie właściwości w temacie Zarządzanie prawami usługi Azure AD. |
| Microsoft. Directory/policies/conditionalAccess/Basic/Read | Odczytaj Właściwość zasad. conditionalAccess w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. AAD. identityProtection/allEntities/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. identityProtection. |
| Microsoft. AAD. privilegedIdentityManagement/allEntities/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. privilegedIdentityManagement. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. protectionCenter/allEntities/odczyt | Przeczytaj wszystkie aspekty Centrum ochrony pakietu Office 365. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |

### <a name="service-support-administrator-permissions"></a>Uprawnienia administratora usługi Service Support

Może odczytywać informacje o kondycji usługi i zarządzać biletami pomocy technicznej.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="sharepoint-service-administrator-permissions"></a>Uprawnienia administratora usługi programu SharePoint

Może zarządzać wszystkimi aspektami usługi programu SharePoint.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Directory/groups. Unified/Basic/Update | Aktualizuj podstawowe właściwości grup Microsoft 365. |
| Microsoft. Directory/groups. Unified/Create | Utwórz grupy Microsoft 365. |
| Microsoft. Directory/groups. Unified/Delete | Usuń grupy Microsoft 365. |
| Microsoft. Directory/groups. Unified/Members/Update | Aktualizowanie członkostwa w grupach Microsoft 365. |
| Microsoft. Directory/groups. Unified/właściciele/aktualizacja | Aktualizowanie własności grup Microsoft 365. |
| Microsoft. Directory/groups. Unified/Restore | Przywracanie grup Microsoft 365 |
| Microsoft. Office 365. Network/Performance/allProperties/Read | Odczytaj strony wydajności sieci w centrum administracyjnym M365. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office. SharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. Office 365. SharePoint. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Office 365. usageReports/allEntities/allProperties/Read | Przeczytaj raporty dotyczące użycia pakietu Office 365. |
| Microsoft. 365. webports/allEntities/Standard/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="teams-communications-administrator-permissions"></a>Zespoły — uprawnienia administratora

Może zarządzać funkcjami wywoływania i spotkań w usłudze Microsoft Teams.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Office 365. usageReports/allEntities/odczyt | Przeczytaj raporty dotyczące użycia pakietu Office 365. |
| Microsoft. Teams/zebranis/allProperties/allTasks | Zarządzanie spotkaniami, w tym zasadami spotkań, konfiguracjami i mostkami konferencji. |
| Microsoft. Teams/Voice/allProperties/allTasks | Zarządzanie głosami, w tym zasadami wywoływania i spisem numerów telefonów oraz przypisaniem. |
| Microsoft. Teams/callQuality/allProperties/Read | Odczytaj wszystkie dane w pulpicie nawigacyjnym jakości wywołań (CQD). |

### <a name="teams-communications-support-engineer-permissions"></a>Zespoły — uprawnienia inżynierów pomocy technicznej

Program może rozwiązywać problemy z komunikacją w zespołach przy użyciu zaawansowanych narzędzi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Teams/callQuality/allProperties/Read | Odczytaj wszystkie dane w pulpicie nawigacyjnym jakości wywołań (CQD). |

### <a name="teams-communications-support-specialist-permissions"></a>Zespoły komunikacyjne obsługują uprawnienia specjalistyczne

Program może rozwiązywać problemy z komunikacją w zespołach przy użyciu podstawowych narzędzi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Teams/callQuality/Basic/Read | Odczytaj podstawowe dane na pulpicie nawigacyjnym jakości wywołań (CQD). |

### <a name="teams-devices-administrator-permissions"></a>Zespoły urządzeń — uprawnienia administratora

Może wykonywać zadania związane z zarządzaniem na urządzeniach certyfikowanych przez zespoły.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Teams/Devices/podstawowa/odczytana | Zarządzanie wszystkimi aspektami urządzeń certyfikowanych przez zespoły, w tym zasad konfiguracji. |

### <a name="teams-service-administrator-permissions"></a>Uprawnienia administratora usługi Teams

Może zarządzać usługą Microsoft Teams.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Odczytaj właściwości groups. hiddenMembers w Azure Active Directory. |
| Microsoft. Directory/Groups/Unified/appRoleAssignments/Update | Aktualizacja właściwości groups. Unified w Azure Active Directory. |
| Microsoft. Directory/groups. Unified/Basic/Update | Aktualizuj podstawowe właściwości grup Microsoft 365. |
| Microsoft. Directory/groups. Unified/Create | Utwórz grupy Microsoft 365. |
| Microsoft. Directory/groups. Unified/Delete | Usuń grupy Microsoft 365. |
| Microsoft. Directory/groups. Unified/Members/Update | Aktualizowanie członkostwa w grupach Microsoft 365. |
| Microsoft. Directory/groups. Unified/właściciele/aktualizacja | Aktualizowanie własności grup Microsoft 365. |
| Microsoft. Directory/groups. Unified/Restore | Przywracanie grup Microsoft 365 |
| Microsoft. Directory/serviceprincipals/managePermissionGrantsForGroup. Microsoft-All-Application-Permissions | Przyznaj zgodę na delegowane uprawnienia w imieniu grupy |
| Microsoft. Office 365. Network/Performance/allProperties/Read | Odczytaj strony wydajności sieci w centrum administracyjnym M365. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Zarządzanie wszystkimi aspektami usługi Skype dla firm Online |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Office 365. usageReports/allEntities/allProperties/Read | Przeczytaj raporty dotyczące użycia pakietu Office 365. |
| Microsoft. 365. webports/allEntities/Standard/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Teams/allEntities/allProperties/allTasks | Zarządzanie wszystkimi zasobami w zespołach. |

### <a name="usage-summary-reports-reader-permissions"></a>Uprawnienia czytelnika do raportów podsumowania użycia
Może wyświetlać tylko agregacje poziomu dzierżawy w analizie użycia M365 i ocenę wydajności.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Office 365. usageReports/allEntities/Standard/Read | Odczytaj zagregowane raporty użycia pakietu Office 365 na poziomie dzierżawy. |
| Microsoft. 365. webports/allEntities/Standard/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport.|

### <a name="user-administrator-permissions"></a>Uprawnienia administratora użytkownika
Może zarządzać wszystkimi aspektami użytkowników i grup, w tym resetowania haseł dla ograniczonych administratorów.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/appRoleAssignments/Create | Utwórz appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Delete | Usuń appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Update | Aktualizacja appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizowanie podstawowych właściwości kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Utwórz kontakty w Azure Active Directory. |
| Microsoft. Directory/kontakty/usuwanie | Usuń kontakty w Azure Active Directory. |
| Microsoft. Directory/entitlementManagement/allProperties/allTasks | Tworzenie i usuwanie zasobów oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure AD uprawnienia zarządzania. |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Zaktualizuj Właściwość groups. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Zaktualizuj podstawowe właściwości dla grup w Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Utwórz grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/createAsOwner | Utwórz grupy w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/Groups/Delete | Usuń grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Odczytaj właściwości groups. hiddenMembers w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizacja właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/Restore | Przywróć grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizowanie właściwości groups. Settings w Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Zaktualizuj Właściwość Users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/assignLicense | Zarządzanie licencjami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Update | Zaktualizuj podstawowe właściwości użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Create | Tworzenie użytkowników w usłudze Azure Active Directory. |
| Microsoft. Directory/Users/Delete | Usuń użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Update | Aktualizowanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft. Directory/Users/Restore | Przywróć usuniętych użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/userPrincipalName/Update | Zaktualizuj Właściwość Users. userPrincipalName w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi dla usług na poziomie katalogu. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health Microsoft 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

## <a name="role-template-ids"></a>Identyfikatory szablonu roli

Identyfikatory szablonów ról są używane głównie przez interfejs API Microsoft Graph lub użytkowników programu PowerShell.

Nazwa wyświetlana wykresu | Nazwa wyświetlana Azure Portal | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrator aplikacji | Administrator aplikacji | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Deweloper aplikacji | Deweloper aplikacji | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrator uwierzytelniania | Administrator uwierzytelniania | c4e39bd9-1100-46d3-8c65-fb160da0071f
Autor ładunku ataku | Autor ładunku ataku | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f
Administrator symulacji ataku | Administrator symulacji ataku | c430b396-e693-46cc-96f3-db01bf8bb62a
Lokalny administrator urządzenia przyłączonego do usługi Azure AD | Lokalny administrator urządzenia przyłączonego do usługi Azure AD | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Administrator usługi Azure DevOps | Administrator usługi Azure DevOps | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure Information Protection administrator | Azure Information Protection administrator | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administrator zestawu kluczy B2C IEF | Administrator zestawu kluczy B2C IEF | aaf43236-0c0d-4d5f-883a-6955382ac081
Administrator zasad B2C IEF | Administrator zasad B2C IEF | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrator rozliczeń | Administrator rozliczeń | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrator aplikacji w chmurze | Administrator aplikacji w chmurze | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrator urządzenia w chmurze | Administrator urządzenia w chmurze | 7698a772-787b-4ac8-901f-60d6b08affd2
Administrator zgodności | Administrator zgodności | 17315797-102d-40b4-93e0-432062caca18
Administrator danych zgodności | Administrator danych zgodności | e6d1a23a-da11-4be4-9570-befc86d067a7
Administrator dostępu warunkowego | Administrator dostępu warunkowego | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Osoba zatwierdzająca dostęp do skrytki klienta | Osoba zatwierdzająca Skrytka klienta dostępu | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administrator usługi Desktop Analytics | Administrator usługi Desktop Analytics | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Przyłączanie urządzenia | Przestarzałe | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Menedżerowie urządzeń | Przestarzałe | 2b499bcd-da44-4968-8aec-78e1674fa64d
Użytkownicy urządzeń | Przestarzałe | d405c6df-0af8-4e3b-95e4-4d06e542189e
Czytelnicy katalogów | Czytelnicy katalogów | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Konta synchronizacji katalogów | Niewyświetlane, ponieważ nie powinno być używane | d29b2b05-8046-44ba-8758-1e26182fcf32
Autorzy katalogów | Autorzy katalogów | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrator systemu Dynamics 365 | Administrator systemu Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Administrator programu Exchange | Administrator programu Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrator przepływu użytkownika z identyfikatorem zewnętrznym | Administrator przepływu użytkownika z identyfikatorem zewnętrznym | 6e591065-9bad-43ed-90f3-e9424366d2f0
Administrator atrybutów przepływu użytkownika zewnętrznego ID | Administrator atrybutów przepływu użytkownika zewnętrznego ID | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Administrator zewnętrznego dostawcy tożsamości | Administrator zewnętrznego dostawcy tożsamości | be2f45a1-457d-42af-a067-6ec1fa63bc45
Administrator globalny | Administrator globalny | 62e90394-69f5-4237-9190-012177145e10
Czytelnik globalny | Czytnik globalny | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Administrator grup | Administrator grup | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Zapraszający gościa | Zapraszający gościa | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrator pomocy technicznej | Administrator pomocy technicznej | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrator tożsamości hybrydowej | Administrator tożsamości hybrydowej | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Administrator usługi Insights | Administrator usługi Insights | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c
Lider biznesowy usługi Insights | Lider biznesowy usługi Insights | 31e939ad-9672-4796-9c2e-873181342d2d
Administrator usługi Intune | Administrator usługi Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Usługi kaizala administrator | Usługi kaizala administrator | 74ef975b-6605-40af-a5d2-b9539d836353
Administrator licencji | Administrator licencji | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Czytnik prywatności centrum wiadomości | Czytnik prywatności centrum wiadomości | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Czytelnik centrum wiadomości | Czytelnik centrum wiadomości | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Nowoczesny użytkownik handlowy | Nowoczesny użytkownik handlowy | d24aef57-1500-4070-84db-2666f29cf966
Administrator sieci | Administrator sieci | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Administrator aplikacji pakietu Office | Administrator aplikacji pakietu Office | 2b745bdf-0803-4d80-aa65-822c4493daac
Obsługa pomoc partnera | Niewyświetlane, ponieważ nie powinno być używane | 4ba39ca4-527c-499a-b93d-d9b492c50246
Obsługa SVR partnera | Niewyświetlane, ponieważ nie powinno być używane | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrator haseł | Administrator haseł | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI administrator | Power BI administrator | a9ea8996-122f-4c74-9520-8edcd192826c
Administrator platformy w elektrowni | platforma Power administrator | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Administrator drukarki | Administrator drukarki | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
Technika drukarki | Technika drukarki | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
Administrator uprzywilejowanego uwierzytelniania | Administrator uprzywilejowanego uwierzytelniania | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrator ról uprzywilejowanych | Administrator ról uprzywilejowanych | e8611ab8-c189-46e8-94e1-60213ab1f814
Czytelnik raportów | Czytelnik raportów | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrator wyszukiwania | Administrator wyszukiwania | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Edytor wyszukiwania | Edytor wyszukiwania | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Administrator zabezpieczeń | Administrator zabezpieczeń | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operator zabezpieczeń | Operator zabezpieczeń | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Czytelnik zabezpieczeń | Czytelnik zabezpieczeń | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrator pomocy technicznej usługi | Administrator pomocy technicznej usługi | f023fd81-a637-4b56-95fd-791ac0226033
Administrator programu SharePoint | Administrator programu SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Administrator programu Skype dla firm | Administrator programu Skype dla firm | 75941009-915A-4869-abe7-691bff18279e
Administratorzy zespołu ds. komunikacji | Administratorzy zespołu ds. komunikacji | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Inżynierowie pomocy technicznej dla zespołów | Inżynierowie pomocy technicznej dla zespołów | f70938a0-fc10-4177-9e90-2178f8765737
Zespoły ds. pomocy technicznej | Zespoły ds. pomocy technicznej | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrator urządzeń zespołów | Administrator urządzeń zespołów | 3d762c5a-1b6c-493f-843e-55a3b42923d4
Administrator zespołów | Administrator zespołów | 69091246-20e8-4a56-aa4d-066075b2a7a8
Czytnik raportów podsumowujących użycia | Czytnik raportów podsumowujących użycia | 75934031-6c7e-415a-99d7-48dbd49e875e
Użytkownik | Niepokazywany, ponieważ nie można go użyć | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrator użytkowników | Administrator użytkowników | fe930be7-5e62-47db-91af-98c3a49a38b1
Dołączanie urządzenia w miejscu pracy | Przestarzałe | c34f683f-4d5a-4403-AFFD-6615e00e3a7f

## <a name="deprecated-roles"></a>Przestarzałe role

Nie należy używać następujących ról. Są one przestarzałe i zostaną usunięte z usługi Azure AD w przyszłości.

* Administrator licencji ad hoc
* Przyłączanie urządzenia
* Menedżerowie urządzeń
* Użytkownicy urządzeń
* Kreator zweryfikowanych użytkowników e-mail
* Administrator skrzynek pocztowych
* Dołączanie urządzenia w miejscu pracy

## <a name="roles-not-shown-in-the-portal"></a>Role nie są wyświetlane w portalu

Nie każda rola zwrócona przez program PowerShell lub MS interfejs API programu Graph jest widoczna w Azure Portal. W poniższej tabeli przedstawiono te różnice.

Nazwa interfejsu API | Nazwa Azure Portal | Uwagi
-------- | ------------------- | -------------
Przyłączanie urządzenia | Przestarzałe | [Dokumentacja przestarzałych ról](permissions-reference.md#deprecated-roles)
Menedżerowie urządzeń | Przestarzałe | [Dokumentacja przestarzałych ról](permissions-reference.md#deprecated-roles)
Użytkownicy urządzeń | Przestarzałe | [Dokumentacja przestarzałych ról](permissions-reference.md#deprecated-roles)
Konta synchronizacji katalogów | Niewyświetlane, ponieważ nie powinno być używane | [Dokumentacja kont synchronizacji katalogów](permissions-reference.md#directory-synchronization-accounts)
Użytkownik-gość | Niepokazywany, ponieważ nie można go użyć  | NA
Obsługa warstwy 1 dla partnerów | Niewyświetlane, ponieważ nie powinno być używane | [Dokumentacja dotycząca pomocy technicznej pomoc partnera](permissions-reference.md#partner-tier1-support)
Obsługa warstwy 2 partnera | Niewyświetlane, ponieważ nie powinno być używane | [Dokumentacja dotycząca pomocy technicznej SVR partnera](permissions-reference.md#partner-tier2-support)
Ograniczony użytkownik-Gość | Niepokazywany, ponieważ nie można go użyć | NA
Użytkownik | Niepokazywany, ponieważ nie można go użyć | NA
Dołączanie urządzenia w miejscu pracy | Przestarzałe | [Dokumentacja przestarzałych ról](permissions-reference.md#deprecated-roles)

## <a name="password-reset-permissions"></a>Uprawnienia do resetowania hasła

Nagłówki kolumn reprezentują role, które mogą resetować hasła. Wiersze tabeli zawierają role, dla których można zresetować swoje hasła.

Hasło może być resetowane | Administrator haseł | Administrator pomocy technicznej | Administrator uwierzytelniania | Administrator użytkownika | Administrator uwierzytelniania uprzywilejowanego | Administrator globalny
------ | ------ | ------ | ------ | ------ | ------ | ------
Administrator uwierzytelniania | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Czytelnicy katalogów | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrator globalny | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Administrator grup | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Gość | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Zapraszający gościa | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrator pomocy technicznej | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Czytelnik centrum wiadomości | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrator haseł | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrator uwierzytelniania uprzywilejowanego | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Administrator ról uprzywilejowanych | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Czytelnik raportów | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Gość z ograniczeniami | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Użytkownik (bez roli administratora) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrator użytkownika | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Czytnik raportów podsumowujących użycia | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* Administrator globalny nie może usunąć własnego przypisania administratora globalnego. Dzieje się tak, aby zapobiec sytuacji, w której organizacja ma 0 administratorów globalnych.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o sposobie przypisywania użytkownika jako administratora subskrypcji platformy Azure, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)
* Aby dowiedzieć się więcej o sposobach kontroli dostępu do zasobów w Microsoft Azure, zobacz [Omówienie różnych ról](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Aby uzyskać szczegółowe informacje na temat relacji między subskrypcjami a dzierżawą usługi Azure AD lub instrukcje dotyczące kojarzenia lub dodawania subskrypcji, zobacz [kojarzenie lub Dodawanie subskrypcji platformy Azure do dzierżawy usługi Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
