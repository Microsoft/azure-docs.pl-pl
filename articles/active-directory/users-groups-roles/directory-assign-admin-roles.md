---
title: Opisy ról administratora i uprawnienia — Usługa Azure AD | Dokumenty firmy Microsoft
description: Rola administratora może dodawać użytkowników, przypisywać role administracyjne, resetować hasła użytkowników, zarządzać licencjami użytkowników lub zarządzać domenami.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac49209fb1debca604a6aeb8ad3993ff898c331
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083006"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Uprawnienia ról administratorów w usłudze Azure Active Directory

Korzystając z usługi Azure Active Directory (Azure AD), można wyznaczyć ograniczonych administratorów do zarządzania zadaniami tożsamości w rolach o mniej uprzywilejowanych. Administratorzy mogą być przypisywani do takich celów, jak dodawanie lub zmienianie użytkowników, przypisywanie ról administracyjnych, resetowanie haseł użytkowników, zarządzanie licencjami użytkowników i zarządzanie nazwami domen. [Domyślne uprawnienia użytkownika](../fundamentals/users-default-permissions.md) można zmienić tylko w ustawieniach użytkownika w usłudze Azure AD.

## <a name="limit-use-of-global-administrator"></a>Ogranicz użycie administratora globalnego

Użytkownicy przypisani do roli administratora globalnego mogą odczytywać i modyfikować każde ustawienie administracyjne w organizacji usługi Azure AD. Domyślnie osobie, która zarejestruje się w ramach subskrypcji platformy Azure, jest przypisywana rola administratora globalnego dla organizacji usługi Azure AD. Tylko administratorzy globalni i administratorzy ról uprzywilejowanych mogą delegować role administratora. Aby zmniejszyć ryzyko dla firmy, zalecamy przypisanie tej roli do jak najmniejszej liczby osób w organizacji.

Najlepszym rozwiązaniem jest przypisanie tej roli do mniej niż pięciu osób w organizacji. Jeśli do roli administratora globalnego w organizacji przypisano więcej niż pięciu administratorów, oto kilka sposobów na ograniczenie jej użycia.

### <a name="find-the-role-you-need"></a>Znajdź potrzebną rolę

Jeśli jest to frustrujące, aby znaleźć rolę, której potrzebujesz z listy wielu ról, usługa Azure AD może pokazać podzbiory ról na podstawie kategorii ról. Zapoznaj się z naszym nowym **filtrem typ** dla [ról usługi Azure AD i administratorów,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) aby wyświetlić tylko role w wybranym typie.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Rola istnieje teraz, która nie istniała po przypisaniu roli administratora globalnego

Jest możliwe, że rola lub role zostały dodane do usługi Azure AD, które zapewniają bardziej szczegółowe uprawnienia, które nie były opcją, gdy podwyższone niektórych użytkowników do administratora globalnego. Z biegiem czasu wdrażamy dodatkowe role, które wykonują zadania, które wcześniej mogły wykonywać tylko rola administratora globalnego. Można je zobaczyć w następujących [dostępnych rolach](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Przypisywanie lub usuwanie ról administratora

Aby dowiedzieć się, jak przypisać role administracyjne do użytkownika w usłudze Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratora w usłudze Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Dostępne role

Dostępne są następujące role administratora:

### <a name="application-administrator"></a>[Administrator aplikacji](#application-administrator-permissions)

Użytkownicy w tej roli mogą tworzyć i zarządzać wszystkimi aspektami aplikacji dla przedsiębiorstw, rejestracji aplikacji i ustawień serwera proxy aplikacji. Należy zauważyć, że użytkownicy przypisani do tej roli nie są dodawane jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji przedsiębiorstwa.

Administratorzy aplikacji mogą zarządzać poświadczeniami aplikacji, które umożliwiają im personifikacji aplikacji. Tak więc użytkownicy przypisani do tej roli mogą zarządzać poświadczeniami aplikacji tylko tych aplikacji, które nie są przypisane do żadnych ról usługi Azure AD lub tych przypisanych tylko do następujących ról administratora:

* Administrator aplikacji
* Deweloper aplikacji
* Administrator aplikacji w chmurze
* Czytniki katalogów

Jeśli aplikacja jest przypisana do innej roli, które nie są wymienione powyżej, administrator aplikacji nie może zarządzać poświadczenia tej aplikacji.

Ta rola daje również możliwość _wyrażenia zgody_ na delegowane uprawnienia i uprawnienia aplikacji, z wyjątkiem uprawnień do interfejsu API programu Microsoft Graph.

> [!IMPORTANT]
> Ten wyjątek oznacza, że nadal można wyrazić zgodę na uprawnienia dla _innych_ aplikacji (na przykład aplikacji lub aplikacji innych firm, które zostały zarejestrowane), ale nie do uprawnień do samej usługi Azure AD. Nadal można _zażądać_ tych uprawnień w ramach rejestracji aplikacji, ale _przyznanie_ (czyli wyrażenia zgody) tych uprawnień wymaga administratora usługi Azure AD. Oznacza to, że złośliwy użytkownik nie może łatwo podnieść swoje uprawnienia, na przykład przez utworzenie i wyrażenie zgody na aplikację, która może pisać do całego katalogu i za pośrednictwem uprawnień tej aplikacji podnieść się stać administratorem globalnym.

### <a name="application-developer"></a>[Deweloper aplikacji](#application-developer-permissions)

Użytkownicy w tej roli mogą tworzyć rejestracje aplikacji, gdy ustawienie "Użytkownicy mogą rejestrować aplikacje" jest ustawione na Nie. Ta rola udziela również zgody na wyrażenie zgody we własnym imieniu, gdy ustawienie "Użytkownicy mogą wyrazić zgodę na dostęp do danych firmy w ich imieniu" jest ustawione na Nie. Użytkownicy przypisani do tej roli są dodawane jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji przedsiębiorstwa.

### <a name="authentication-administrator"></a>[Administrator uwierzytelniania](#authentication-administrator-permissions)

Użytkownicy z tą rolą mogą ustawiać lub resetować poświadczenia niebędące hasłami dla niektórych użytkowników i mogą aktualizować hasła dla wszystkich użytkowników. Administratorzy uwierzytelniania mogą wymagać od użytkowników, którzy nie są administratorami lub przypisani do niektórych ról, ponownej rejestracji z istniejącymi poświadczeniami niebędącymi hasłami (na przykład uwierzytelnianie wieloskładnikowe lub FIDO), a także mogą odwołać **zapamiętywanie usługi MFA na urządzeniu,** co powoduje monitowanie o uwierzytelnianie wieloskładnikowe przy następnym loguch. Te akcje dotyczą tylko użytkowników, którzy nie są administratorami lub którym przypisano jedną lub więcej z następujących ról:

* Administrator uwierzytelniania
* Czytniki katalogów
* Zaproszony gość
* Czytnik centrum wiadomości
* Czytnik raportów

Rola [administratora uwierzytelniania uprzywilejowanego](#privileged-authentication-administrator) ma uprawnienia, które mogą wymusić ponowną rejestrację i uwierzytelnianie wieloskładnikowe dla wszystkich użytkowników.

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać poświadczenia dla osób, które mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej wewnątrz i na zewnątrz usługi Azure Active Directory. Zmiana poświadczeń użytkownika może oznaczać możliwość przyjęcia tożsamości i uprawnień tego użytkownika. Przykład:
>
>- Rejestracja aplikacji i właściciele aplikacji korporacyjnych, którzy mogą zarządzać poświadczeniami aplikacji, których są właścicielami. Te aplikacje mogą mieć uprawnienia uprzywilejowane w usłudze Azure AD i w innym miejscu nie są przyznawane administratorom uwierzytelniania. Za pośrednictwem tej ścieżki administrator uwierzytelniania może być w stanie przyjąć tożsamość właściciela aplikacji, a następnie dalej przyjąć tożsamość aplikacji uprzywilejowanej, aktualizując poświadczenia dla aplikacji.
>- Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do poufnych lub prywatnych informacji lub krytycznej konfiguracji na platformie Azure.
>- Grupy zabezpieczeń i właścicieli grup usługi Office 365, którzy mogą zarządzać członkostwem w grupie. Te grupy mogą udzielić dostępu do poufnych lub prywatnych informacji lub konfiguracji krytycznej w usłudze Azure AD i w innych miejscach.
>- Administratorzy w innych usługach poza usługą Azure AD, takich jak Exchange Online, Office Security and Compliance Center oraz systemy zasobów ludzkich.
>- Osoby niebędące administratorami, takie jak kadra kierownicza, radca prawny i pracownicy działu zasobów ludzkich, którzy mogą mieć dostęp do poufnych lub prywatnych informacji.

### <a name="azure-devops-administrator"></a>[Azure DevOps Administrator](#azure-devops-administrator-permissions)

Użytkownicy z tą rolą mogą zarządzać zasadami azure devops, aby ograniczyć nowe tworzenie organizacji usługi Azure DevOps do zestawu konfigurowalnych użytkowników lub grup. Użytkownicy w tej roli mogą zarządzać tą zasadą za pośrednictwem dowolnej organizacji Azure DevOps, która jest wspierana przez organizację usługi Azure AD firmy.

Wszystkie zasady deweloperów platformy Azure w przedsiębiorstwie mogą być zarządzane przez użytkowników w tej roli.

### <a name="azure-information-protection-administrator"></a>[Azure Information Protection Administrator](#azure-information-protection-administrator-permissions)

Użytkownicy z tą rolą mają wszystkie uprawnienia w usłudze Azure Information Protection. Ta rola umożliwia konfigurowanie etykiet dla zasad usługi Azure Information Protection, zarządzanie szablonami ochrony i aktywowanie ochrony. Ta rola nie udziela żadnych uprawnień w Centrum ochrony tożsamości, zarządzania tożsamościami uprzywilejowanymi, monitorowaniu kondycji usługi Office 365 ani Centrum zgodności zabezpieczeń & usługi Office 365.

### <a name="b2c-user-flow-administrator"></a>[Administrator przepływu użytkownika B2C](#b2c-user-flow-administrator-permissions)

Użytkownicy z tej roli można tworzyć i zarządzać przepływów użytkowników B2C (nazywanych również "wbudowane" zasady) w witrynie Azure portal.Tworząc lub edytując przepływy użytkowników, ci użytkownicy mogą zmieniać zawartość html/CSS/javascript środowiska użytkownika, zmieniać wymagania usługi MFA na przepływ użytkownika, zmieniać oświadczenia w tokenie i dostosowywać ustawienia sesji dla wszystkich zasad w dzierżawie. Z drugiej strony ta rola nie obejmuje możliwość przeglądania danych użytkownika lub wprowadzać zmiany w atrybutach, które są zawarte w schemacie dzierżawy.Zmiany w ramach środowiska tożsamości (znany również jako niestandardowe) zasady jest również poza zakresem tej roli.

### <a name="b2c-user-flow-attribute-administrator"></a>[Administrator atrybutów przepływu użytkownika B2C](#b2c-user-flow-attribute-administrator-permissions)

Użytkownicy z tą rolą dodają lub usuwają atrybuty niestandardowe dostępne dla wszystkich przepływów użytkowników w dzierżawie.W związku z tym użytkownicy z tej roli można zmienić lub dodać nowe elementy do schematu użytkownika końcowego i wpływ na zachowanie wszystkich przepływów użytkowników i pośrednio spowodować zmiany, jakie dane mogą być zadawane użytkowników końcowych i ostatecznie wysyłane jako oświadczenia do aplikacji.Ta rola nie może edytować przepływów użytkownika.

### <a name="b2c-ief-keyset-administrator"></a>[Administrator zestawu kluczy IEF B2C](#b2c-ief-keyset-administrator-permissions)

Użytkownik może tworzyć klucze zasad i wpisy tajne i zarządzać nimi w zakresie szyfrowania tokenów, podpisów tokenów i szyfrowania/odszyfrowywania oświadczeń.Dodając nowe klucze do istniejących kontenerów kluczy, ten ograniczony administrator może przerzućć wpisy tajne w razie potrzeby bez wpływu na istniejące aplikacje.Ten użytkownik może zobaczyć pełną zawartość tych wpisów tajnych i ich daty wygaśnięcia nawet po ich utworzeniu.

> [!IMPORTANT]
> Jest to rola wrażliwa.Rola administratora zestawu kluczy powinna być starannie kontrolowana i przypisywana z ostrożnością podczas przedprodukcji i produkcji.

### <a name="b2c-ief-policy-administrator"></a>[Administrator zasad IEF B2C](#b2c-ief-policy-administrator-permissions)

Użytkownicy w tej roli mają możliwość tworzenia, odczytywania, aktualizowania i usuwania wszystkich zasad niestandardowych w usłudze Azure AD B2C i dlatego mają pełną kontrolę nad platformą środowiska tożsamości w odpowiedniej dzierżawie usługi Azure AD B2C. Edytując zasady, ten użytkownik może ustanowić bezpośrednią federację z zewnętrznymi dostawcami tożsamości, zmienić schemat katalogu, zmienić całą zawartość skierowaną do użytkownika (HTML, CSS, JavaScript), zmienić wymagania, aby zakończyć uwierzytelnianie, utworzyć nowych użytkowników, wysłać dane użytkownika do systemów zewnętrznych, w tym pełne migracje, i edytować wszystkie informacje o użytkowniku, w tym poufne pola, takie jak hasła i numery telefonów. Z drugiej strony ta rola nie może zmienić kluczy szyfrowania ani edytować wpisów tajnych używanych dla federacji w dzierżawie.

> [!IMPORTANT]
> Administrator zasad IEF B2 jest bardzo wrażliwą rolą, która powinna być przypisana w bardzo ograniczonym stopniu dla dzierżawców w produkcji.Działania tych użytkowników powinny być ściśle kontrolowane, szczególnie dla dzierżawców w produkcji.

### <a name="billing-administrator"></a>[Administrator rozliczeń](#billing-administrator-permissions)

Dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.

### <a name="cloud-application-administrator"></a>[Administrator aplikacji w chmurze](#cloud-application-administrator-permissions)

Użytkownicy w tej roli mają takie same uprawnienia jak rola Administrator aplikacji, z wyłączeniem możliwości zarządzania serwerem proxy aplikacji. Ta rola umożliwia tworzenie i zarządzanie wszystkimi aspektami aplikacji dla przedsiębiorstw i rejestracji aplikacji. Ta rola daje również możliwość wyrażenia zgody na uprawnienia delegowane i uprawnienia aplikacji z wyłączeniem interfejsu API programu Microsoft Graph. Użytkownicy przypisani do tej roli nie są dodawane jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji przedsiębiorstwa.

Administratorzy aplikacji w chmurze mogą zarządzać poświadczeniami aplikacji, które umożliwiają im personifikacji aplikacji. Tak więc użytkownicy przypisani do tej roli mogą zarządzać poświadczeniami aplikacji tylko tych aplikacji, które nie są przypisane do żadnych ról usługi Azure AD lub tych przypisanych tylko do następujących ról administratora:

* Deweloper aplikacji
* Administrator aplikacji w chmurze
* Czytniki katalogów

Jeśli aplikacja jest przypisana do innej roli, które nie są wymienione powyżej, a następnie administrator aplikacji w chmurze nie może zarządzać poświadczenia tej aplikacji.

### <a name="cloud-device-administrator"></a>[Administrator urządzeń w chmurze](#cloud-device-administrator-permissions)

Użytkownicy w tej roli mogą włączać, wyłączać i usuwać urządzenia w usłudze Azure AD i odczytywać klucze funkcji BitLocker systemu Windows 10 (jeśli są obecne) w witrynie Azure portal. Rola nie udziela uprawnień do zarządzania innymi właściwościami na urządzeniu.

### <a name="compliance-administrator"></a>[Administrator zgodności](#compliance-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia do zarządzania funkcjami związanymi ze zgodnością w Centrum zgodności usługi Microsoft 365, centrum administracyjnym usługi Microsoft 365, platformie Azure i Centrum zgodności zabezpieczeń & usługi Office 365. Cesjonariusze mogą również zarządzać wszystkimi funkcjami w centrum administracyjnym programu Exchange i usługach Teams & centrach administracyjnych programu Skype dla firm oraz tworzyć bilety pomocy technicznej dla platformy Azure i usługi Microsoft 365. Więcej informacji można znaleźć na [stronie Informacje o rolach administratorów usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

W | Może to zrobić
----- | ----------
[Centrum zgodności usługi Microsoft 365](https://protection.office.com) | Ochrona danych organizacji i zarządzanie nimi w usługach Microsoft 365<br>Zarządzanie alertami zgodności
[Menedżer zgodności](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Śledzenie, przypisywanie i weryfikowanie działań organizacji w zakresie zgodności z przepisami
[Centrum zgodności & zabezpieczeń usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie zarządzaniem danymi<br>Przeprowadzenie dochodzenia prawnego i badania danych<br>Zarządzanie żądaniem osoby, której dane dotyczą<br><br>Ta rola ma takie same uprawnienia jak grupa [ról administratora zgodności](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) w kontroli dostępu opartej na rolach Centrum zabezpieczeń & office 365.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Wyświetlanie wszystkich danych inspekcji usługi Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami<br>Może tworzyć i modyfikować zasady plików oraz zezwalać na akcje nadzoru plików<br>Może wyświetlać wszystkie wbudowane raporty w obszarze Zarządzanie danymi

### <a name="compliance-data-administrator"></a>[Administrator danych zgodności](#compliance-data-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia do śledzenia danych w centrum zgodności usługi Microsoft 365, centrum administracyjnym usługi Microsoft 365 i platformie Azure. Użytkownicy mogą również śledzić dane zgodności w centrum administracyjnym programu Exchange, Menedżerze zgodności i & centrum administracyjnym programu Skype dla firm oraz tworzyć bilety pomocy technicznej dla platformy Azure i usługi Microsoft 365.

W | Może to zrobić
----- | ----------
[Centrum zgodności usługi Microsoft 365](https://protection.office.com) | Monitorowanie zasad związanych ze zgodnością w usługach Microsoft 365<br>Zarządzanie alertami zgodności
[Menedżer zgodności](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Śledzenie, przypisywanie i weryfikowanie działań organizacji w zakresie zgodności z przepisami
[Centrum zgodności & zabezpieczeń usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie zarządzaniem danymi<br>Przeprowadzenie dochodzenia prawnego i badania danych<br>Zarządzanie żądaniem osoby, której dane dotyczą<br><br>Ta rola ma takie same uprawnienia jak [grupa role administratora danych zgodności](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) w kontroli dostępu opartej na rolach Centrum zabezpieczeń & office 365.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Wyświetlanie wszystkich danych inspekcji usługi Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami<br>Może tworzyć i modyfikować zasady plików oraz zezwalać na akcje nadzoru plików<br>Może wyświetlać wszystkie wbudowane raporty w obszarze Zarządzanie danymi

### <a name="conditional-access-administrator"></a>[Administrator dostępu warunkowego](#conditional-access-administrator-permissions)

Użytkownicy z tą rolą mają możliwość zarządzania ustawieniami dostępu warunkowego usługi Azure Active Directory.
> [!NOTE]
> Aby wdrożyć zasady programu Exchange ActiveSync Dostęp warunkowy na platformie Azure, użytkownik musi być również administratorem globalnym.

### <a name="customer-lockbox-access-approver"></a>[Osoba zatwierdzająca dostęp do skrytki klienta](#customer-lockbox-access-approver-permissions)

Zarządza [żądaniami skrytki klienta](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) w organizacji. Otrzymują powiadomienia e-mail dla żądań skrytki klienta i mogą zatwierdzać i odrzucać żądania z centrum administracyjnego usługi Microsoft 365. Mogą również włączać lub wyłączać funkcję skrytki klienta. Tylko administratorzy globalni mogą resetować hasła osób przypisanych do tej roli.

### <a name="desktop-analytics-administrator"></a>[Administrator analityki komputerowej](#desktop-analytics-administrator-permissions)


Użytkownicy w tej roli mogą zarządzać usługami analizy pulpitu i dostosowania pakietu Office & zasad. W przypadku analizy komputerowej obejmuje to możliwość wyświetlania zasobów, tworzenia planów wdrażania, wyświetlania wdrożenia i stanu kondycji. W przypadku usługi Dostosowania pakietu Office & zasady ta rola umożliwia użytkownikom zarządzanie zasadami pakietu Office.

### <a name="device-administrator"></a>[Administrator urządzenia](#device-administrators-permissions)

Ta rola jest dostępna do przypisania tylko jako dodatkowy administrator lokalny w [ustawieniach urządzenia](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Użytkownicy z tą rolą stają się administratorami komputerów lokalnych na wszystkich urządzeniach z systemem Windows 10, które są przyłączone do usługi Azure Active Directory. Nie mają możliwości zarządzania obiektami urządzeń w usłudze Azure Active Directory.

### <a name="directory-readers"></a>[Czytniki katalogów](#directory-readers-permissions)

Użytkownicy w tej roli mogą odczytywać podstawowe informacje katalogowe. Ta rola powinna być używana dla:
* Przyznawanie określonego zestawu użytkowników-gości odczytywanych zamiast udzielania go wszystkim użytkownikom-gościom.
* Przyznawanie określonego zestawu użytkowników niebędących administratorami dostępu do witryny Azure portal, gdy "Ogranicz dostęp do portalu usługi Azure AD tylko do administratorów" jest ustawiona na "Tak".
* Przyznanie jednostkom usługi dostępu do katalogu, w którym Directory.Read.All nie jest opcją.

### <a name="directory-synchronization-accounts"></a>[Konta synchronizacji katalogów](#directory-synchronization-accounts-permissions)

Nie używaj. Ta rola jest automatycznie przypisywana do usługi Azure AD Connect i nie jest przeznaczona ani obsługiwana do innego użytku.

### <a name="directory-writers"></a>[Autorzy katalogów](#directory-writers-permissions)

Jest to starsza rola, która ma być przypisana do aplikacji, które nie obsługują [struktury zgody](../develop/quickstart-register-app.md). Nie należy go przypisywać żadnym użytkownikom.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Dynamics 365 administrator / CRM Administrator](#crm-service-administrator-permissions)

Użytkownicy z tą rolą mają globalne uprawnienia w usłudze Microsoft Dynamics 365 Online, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji na temat [Zarządzanie dzierżawą za pomocą roli administratora usługi](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> W interfejsie API programu Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "Administrator usługi Dynamics 365". Jest to "Administrator Dynamics 365" w [witrynie Azure portal](https://portal.azure.com).

### <a name="exchange-administrator"></a>[Exchange Administrator](#exchange-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Exchange Online, gdy usługa jest obecna. Ma również możliwość tworzenia i zarządzania wszystkimi grupami usługi Office 365, zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji na [temat ról administratora usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)— informacje .

> [!NOTE]
> W interfejsie API programu Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "Administrator usługi programu Exchange". Jest to "Administrator programu Exchange" w [witrynie Azure portal](https://portal.azure.com). Jest to "Administrator usługi Exchange Online" w [centrum administracyjnym programu Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).

### <a name="external-identity-provider-administrator"></a>[Administrator zewnętrznego dostawcy tożsamości](#external-identity-provider-administrator-permissions)

Ten administrator zarządza federacją między dzierżawami usługi Azure Active Directory a zewnętrznymi dostawcami tożsamości.Dzięki tej roli użytkownicy mogą dodawać nowych dostawców tożsamości i konfigurować wszystkie dostępne ustawienia (np. ścieżkę uwierzytelniania, identyfikator usługi, przypisane kontenery kluczy).Ten użytkownik może włączyć dzierżawy do zaufania uwierzytelniania od zewnętrznych dostawców tożsamości.Wynikowy wpływ na środowisko użytkownika końcowego zależy od typu dzierżawy:

* Dzierżawy usługi Azure Active Directory dla pracowników i partnerów: dodanie federacji (np. w Gmailu) będzie miało natychmiastowy wpływ na wszystkie zaproszenia gości, które nie zostały jeszcze zrealizowane. Zobacz [Dodawanie Google jako dostawcy tożsamości dla użytkowników-gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
* Dzierżawy usługi Azure Active Directory B2C: dodanie federacji (na przykład z Facebookiem lub inną organizacją usługi Azure AD) nie wpływa natychmiast na przepływy użytkowników końcowych, dopóki dostawca tożsamości nie zostanie dodany jako opcja w przepływie użytkownika (nazywany również wbudowanymi zasadami). Zobacz [konfigurowanie konta Microsoft jako dostawcy tożsamości](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) na przykład.Aby zmienić przepływy użytkowników, wymagana jest ograniczona rola "Administrator przepływu użytkownika B2C".

### <a name="global-administrator--company-administrator"></a>[Administrator globalny / Administrator firmy](#company-administrator-permissions)

Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure Active Directory, a także do usług korzystających z tożsamości usługi Azure Active Directory, takich jak centrum zabezpieczeń usługi Microsoft 365, centrum zgodności usługi Microsoft 365, usługa Exchange Online, usługa SharePoint Online i usługa Skype dla firm Online. Osoba, która zarejestruje się w dzierżawie usługi Azure Active Directory, staje się administratorem globalnym. W Twojej firmie może znajdować się więcej niż jeden administrator globalny. Administratorzy globalni mogą resetować hasła dla wszystkich użytkowników oraz wszystkich pozostałych administratorów.

> [!NOTE]
> W interfejsie API programu Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "Administrator firmy". Jest to "Administrator globalny" w [witrynie Azure portal](https://portal.azure.com).
>
>

### <a name="global-reader"></a>[Czytelnik globalny](#global-reader-permissions)

Użytkownicy w tej roli mogą odczytywać ustawienia i informacje administracyjne w usługach Microsoft 365, ale nie mogą podejmować działań zarządzania. Czytnik globalny jest tylko do odczytu odpowiednikiem administratora globalnego. Przypisz czytnik globalny zamiast administratora globalnego do planowania, inspekcji lub dochodzeń. Użyj czytnika globalnego w połączeniu z innymi ograniczonymi rolami administratora, takimi jak Administrator programu Exchange, aby ułatwić pracę bez przypisywania roli administratora globalnego. Czytnik globalny współpracuje z centrum administracyjnym usługi Microsoft 365, centrum administracyjne programu Exchange, centrum administracyjne programu SharePoint, centrum administracyjne teams, centrum zabezpieczeń, centrum zgodności, centrum administracyjne usługi Azure AD i centrum administracyjne zarządzania urządzeniami.

> [!NOTE]
> Globalna rola czytelnika ma teraz kilka ograniczeń -
>
>- [Centrum administracyjne usługi OneDrive](https://admin.onedrive.com/) — centrum administracyjne usługi OneDrive nie obsługuje roli Czytnik globalny.
>- [Portal usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) — czytnik globalny nie może odczytać trybu inicjowania obsługi administracyjnej aplikacji przedsiębiorstwa.
>- [Centrum administracyjne M365](https://admin.microsoft.com/Adminportal/Home#/homepage) — czytnik globalny nie może odczytać żądań skrytki klienta. Karta **Żądania skrytki klienta** nie zostanie ujęte w obszarze **Obsługa w** lewym okienku Centrum administracyjnego M365.
>- [Centrum zabezpieczeń M365](https://security.microsoft.com/homepage) — czytnik globalny nie może odczytać etykiet czułości i przechowywania. W lewym okienku centrum zabezpieczeń M365 nie będą znajdować **etykiety czułości,** **etykiety przechowywania**i karty **Analizy etykiet.**
>- [Centrum zgodności & zabezpieczeń pakietu Office](https://sip.protection.office.com/homepage) — czytnik globalny nie może odczytywać dzienników inspekcji SCC, wyszukiwać zawartości ani wyświetlać się w programie Secure Score.
>- [Centrum administracyjne zespołów](https://admin.teams.microsoft.com) — czytnik globalny nie może odczytać **cyklu życia zespołów,** **raportów & analizy,** **zarządzania urządzeniami telefonu IP** i **katalogu aplikacji**.
>- [Zarządzanie dostępem uprzywilejowanym (PAM)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) nie obsługuje roli czytnika globalnego.
>- [Usługa Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) — czytnik globalny jest obsługiwany tylko [w przypadku raportowania centralnego,](https://docs.microsoft.com/azure/information-protection/reports-aip) a organizacja usługi Azure AD nie znajduje się na [ujednoliconej platformie etykietowania.](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
>
> Te funkcje są obecnie w fazie rozwoju.
>

### <a name="groups-administrator"></a>[Administrator grup](#groups-administrator-permissions)

Użytkownicy w tej roli mogą tworzyć grupy i jej ustawienia, takie jak zasady nazewnictwa i wygasania. Ważne jest, aby zrozumieć, że przypisanie użytkownika do tej roli daje im możliwość zarządzania wszystkimi grupami w dzierżawie w różnych obciążeniach, takich jak zespoły, SharePoint, Yammer oprócz programu Outlook. Ponadto użytkownik będzie mógł zarządzać różnymi ustawieniami grup w różnych portalach administracyjnych, takich jak Microsoft Admin Center, Azure Portal, a także ustawieniami specyficznymi dla obciążenia, takimi jak zespoły i centra administracyjne programu SharePoint.

### <a name="guest-inviter"></a>[Zaproszony gość](#guest-inviter-permissions)

Użytkownicy w tej roli mogą zarządzać zaproszeniami użytkowników-użytkowników-użytkowników-użytkowników usługi Azure Active Directory B2B, gdy ustawienie użytkowników **może zapraszać** użytkowników jest ustawione na Nie. Więcej informacji na temat współpracy B2B na [temat współpracy usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nie zawiera żadnych innych uprawnień.

### <a name="helpdesk-administrator"></a>[Administrator działu pomocy technicznej](#helpdesk-administrator-permissions)

Użytkownicy z tą rolą mogą zmieniać hasła, unieważniać tokeny odświeżania, zarządzać żądaniami usług i monitorować kondycję usługi. Unieważnienie tokenu odświeżania zmusza użytkownika do ponownego zalogowania się. Administratorzy pomocy technicznej mogą resetować hasła i unieważniać tokeny odświeżania innych użytkowników, którzy nie są administratorami lub przypisują tylko następujące role:

* Czytniki katalogów
* Zaproszony gość
* Administrator działu pomocy technicznej
* Czytnik centrum wiadomości
* Czytnik raportów

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać hasła dla osób, które mogą mieć dostęp do poufnych lub prywatnych informacji lub krytycznej konfiguracji wewnątrz i na zewnątrz usługi Azure Active Directory. Zmiana hasła użytkownika może oznaczać możliwość przyjęcia tożsamości i uprawnień tego użytkownika. Przykład:
>
>- Rejestracja aplikacji i właściciele aplikacji korporacyjnych, którzy mogą zarządzać poświadczeniami aplikacji, których są właścicielami. Te aplikacje mogą mieć uprawnienia uprzywilejowane w usłudze Azure AD i w innym miejscu, które nie są przyznawane administratorom pomocy technicznej. Za pomocą tej ścieżki administrator pomocy technicznej może być w stanie przyjąć tożsamość właściciela aplikacji, a następnie dalej przyjąć tożsamość aplikacji uprzywilejowanej, aktualizując poświadczenia dla aplikacji.
>- Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do poufnych lub prywatnych informacji lub krytycznej konfiguracji na platformie Azure.
>- Grupy zabezpieczeń i właścicieli grup usługi Office 365, którzy mogą zarządzać członkostwem w grupie. Te grupy mogą udzielić dostępu do poufnych lub prywatnych informacji lub konfiguracji krytycznej w usłudze Azure AD i w innych miejscach.
>- Administratorzy w innych usługach poza usługą Azure AD, takich jak Exchange Online, Office Security and Compliance Center oraz systemy zasobów ludzkich.
>- Osoby niebędące administratorami, takie jak kadra kierownicza, radca prawny i pracownicy działu zasobów ludzkich, którzy mogą mieć dostęp do poufnych lub prywatnych informacji.

Delegowanie uprawnień administracyjnych nad podzbiorami użytkowników i stosowanie zasad do podzbioru użytkowników jest możliwe w [przypadku jednostek administracyjnych (teraz w publicznej wersji zapoznawczej).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)

Ta rola była wcześniej nazywana "Administratorem haseł" w [witrynie Azure portal](https://portal.azure.com/). Nazwa "Administrator pomocy technicznej" w usłudze Azure AD jest teraz zgodna z jej nazwą w programie Azure AD PowerShell i interfejsie API programu Microsoft Graph.

### <a name="hybrid-identity-administrator"></a>[Administrator tożsamości hybrydowej](#hybrid-identity-administrator-permissions)

Użytkownicy w tej roli mogą włączać, konfigurować i zarządzać usługami i ustawieniami związanymi z włączaniem tożsamości hybrydowej w usłudze Azure AD. Ta rola umożliwia skonfigurowanie usługi Azure AD do jednej z trzech obsługiwanych metod uwierzytelniania, synchronizacja skrótów haseł (PHS), uwierzytelnianie przekazywane (PTA) lub federacja (AD FS lub dostawca federacji innych firm) oraz wdrożenie powiązanej infrastruktury lokalnej w celu ich umożliwienia. Infrastruktura przedwzmysłowa obejmuje aprowizacji i agentów PTA. Ta rola umożliwia włączenie bezproblemowego logowania jednokrotnego (S-Log-SSO) w celu umożliwienia bezproblemowego uwierzytelniania na urządzeniach innych niż Windows 10 lub komputerach z systemem windows server 2016. Ponadto ta rola daje możliwość zobacz dzienniki logowania i dostęp do kondycji i analizy do celów monitorowania i rozwiązywania problemów. 

### <a name="intune-administrator"></a>[Intune Administrator](#intune-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Intune Online, gdy usługa jest obecna. Ponadto ta rola zawiera możliwość zarządzania użytkownikami i urządzeniami w celu skojarzenia zasad, a także tworzenia grup i zarządzania nimi. Więcej informacji na temat [kontroli administracji opartej na rolach (RBAC) w usłudze Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control).

Ta rola może tworzyć wszystkie grupy zabezpieczeń i zarządzać nimi. Administrator usługi Intune nie ma jednak praw administratora w grupach pakietu Office. Oznacza to, że administrator nie może zaktualizować właścicieli lub członkostwa wszystkich grup pakietu Office w dzierżawie. Może jednak zarządzać grupą pakietu Office, którą tworzy, która jest częścią jego uprawnień użytkownika końcowego. Tak więc każda grupa pakietu Office (nie grupa zabezpieczeń), którą tworzy, powinna być wliczona do jego przydziału 250.

> [!NOTE]
> W interfejsie API programu Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "Administrator usługi usługi intune". Jest to "Administrator usługi Intune" w [witrynie Azure portal](https://portal.azure.com).

### <a name="kaizala-administrator"></a>[Kaizala Administrator](#kaizala-administrator-permissions)

Użytkownicy z tej roli mają globalne uprawnienia do zarządzania ustawieniami w ramach firmy Microsoft Kaizala, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Ponadto użytkownik może uzyskać dostęp do raportów związanych z przyjęciem & użyciem Kaizala przez członków organizacji i raporty biznesowe generowane za pomocą akcji Kaizala.

### <a name="license-administrator"></a>[Administrator licencji](#license-administrator-permissions)

Użytkownicy w tej roli mogą dodawać, usuwać i aktualizować przypisania licencji dla użytkowników, grup (przy użyciu licencjonowania opartego na grupach) i zarządzać lokalizacją użycia użytkowników. Rola nie daje możliwości zakupu subskrypcji, tworzenia grup ani zarządzania nimi, tworzenia ani zarządzania nimi, tworzenia użytkowników poza lokalizacją użycia ani zarządzania nimi. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

### <a name="message-center-privacy-reader"></a>[Czytnik prywatności centrum wiadomości](#message-center-privacy-reader-permissions)

Użytkownicy w tej roli mogą monitorować wszystkie powiadomienia w Centrum wiadomości, w tym wiadomości o prywatności danych. Czytniki prywatności Centrum wiadomości otrzymują powiadomienia e-mail, w tym te związane z prywatnością danych i mogą zrezygnować z subskrypcji za pomocą preferencji Centrum wiadomości. Tylko administrator globalny i czytnik prywatności centrum wiadomości mogą odczytywać wiadomości o ochronie danych. Ponadto ta rola zawiera możliwość wyświetlania grup, domen i subskrypcji. Ta rola nie ma uprawnień do wyświetlania, tworzenia lub zarządzania żądaniami usługi.

### <a name="message-center-reader"></a>[Czytnik centrum wiadomości](#message-center-reader-permissions)

Użytkownicy w tej roli mogą monitorować powiadomienia i aktualizacje kondycji poradnika w [Centrum wiadomości usługi Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) dla swojej organizacji w skonfigurowanych usługach, takich jak Exchange, Intune i Microsoft Teams. Czytniki Centrum wiadomości otrzymują cotygodniowe wiadomości e-mail z postami, aktualizacjami i mogą udostępniać posty centrum wiadomości w usłudze Office 365. W usłudze Azure AD użytkownicy przypisani do tej roli będą mieli dostęp tylko do odczytu w usługach Usługi Azure AD, takich jak użytkownicy i grupy. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

### <a name="network-administrator"></a>[Administrator sieci](#network-administrator-permissions)

Użytkownicy w tej roli można przejrzeć zalecenia architektury obwodowej sieci firmy Microsoft, które są oparte na danych telemetrycznych sieci z ich lokalizacji użytkownika. Wydajność sieci dla usługi Office 365 zależy od ostrożnej architektury obwodowej sieci klientów korporacyjnych, która jest zazwyczaj specyficzna dla lokalizacji użytkownika. Ta rola umożliwia edycję odnalezionych lokalizacji użytkowników i konfigurację parametrów sieci dla tych lokalizacji w celu ułatwienia ulepszonych pomiarów telemetrycznych i zaleceń projektowych. 

### <a name="office-apps-administrator"></a>[Administrator aplikacji pakietu Office](#office-apps-administrator-permissions)

Użytkownicy w tej roli mogą zarządzać ustawieniami chmury aplikacji usługi Office 365. Obejmuje to zarządzanie zasadami chmury, samoobsługowym zarządzaniem pobieraniem i możliwością wyświetlania raportu związanego z aplikacjami pakietu Office. Ta rola dodatkowo daje możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi w głównym centrum administracyjnym. Użytkownicy przypisani do tej roli mogą również zarządzać komunikacją nowych funkcji w aplikacjach pakietu Office. 

### <a name="partner-tier1-support"></a>[Pomoc techniczna poziomu 1 partnera](#partner-tier1-support-permissions)

Nie używaj. Ta rola została przestarzała i zostanie usunięta z usługi Azure AD w przyszłości. Ta rola jest przeznaczona do użytku przez niewielką liczbę partnerów odsprzedaży firmy Microsoft i nie jest przeznaczona do użytku ogólnego.

### <a name="partner-tier2-support"></a>[Obsługa partnerów Tier2](#partner-tier2-support-permissions)

Nie używaj. Ta rola została przestarzała i zostanie usunięta z usługi Azure AD w przyszłości. Ta rola jest przeznaczona do użytku przez niewielką liczbę partnerów odsprzedaży firmy Microsoft i nie jest przeznaczona do użytku ogólnego.

### <a name="password-administrator"></a>[Administrator haseł](#password-administrator-permissions)

Użytkownicy z tą rolą mają ograniczoną możliwość zarządzania hasłami. Ta rola nie daje możliwość zarządzania żądaniami usługi lub monitorowania kondycji usługi. Administratorzy haseł mogą resetować hasła innych użytkowników, którzy nie są administratorami lub tylko członkami następujących ról:

* Czytniki katalogów
* Zaproszony gość
* Administrator haseł

### <a name="power-bi-administrator"></a>[Power BI Administrator](#power-bi-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Power BI, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji można znaleźć w [opisie roli administratora usługi Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

> [!NOTE]
> W interfejsie API programu Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "Administrator usługi Power BI ". Jest to "Administrator usługi Power BI" w [portalu Azure](https://portal.azure.com).

### <a name="power-platform-administrator"></a>[Administrator platformy zasilania](#power-platform-administrator-permissions)

Użytkownicy w tej roli mogą tworzyć i zarządzać wszystkimi aspektami środowisk, usługi PowerApps, przepływów, zasad zapobiegania utracie danych. Ponadto użytkownicy z tej roli mają możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi.

### <a name="printer-administrator"></a>[Administrator drukarki](#printer-administrator-permissions)

Użytkownicy w tej roli mogą rejestrować drukarki i zarządzać wszystkimi aspektami wszystkich konfiguracji drukarek w rozwiązaniu Microsoft Universal Print, w tym ustawienia uniwersalnego łącznika drukowania. Mogą wyrazić zgodę na wszystkie delegowane żądania uprawnień drukowania. Administratorzy drukarek mają również dostęp do raportów drukowania.

### <a name="printer-technician"></a>[Technik drukarki](#printer-technician-permissions)

Użytkownicy z tą rolą mogą rejestrować drukarki i zarządzać stanem drukarki w rozwiązaniu Microsoft Universal Print. Mogą również odczytać wszystkie informacje o łączniku. Kluczowe zadanie, którego nie może wykonać technik drukarki, to ustawić uprawnienia użytkownika do drukarek i drukarek udostępnianych.

### <a name="privileged-authentication-administrator"></a>[Administrator uwierzytelniania uprzywilejowanego](#privileged-authentication-administrator-permissions)

Użytkownicy z tą rolą mogą ustawiać lub resetować poświadczenia niebędące hasłami dla wszystkich użytkowników, w tym administratorów globalnych, i mogą aktualizować hasła dla wszystkich użytkowników. Administratorzy uwierzytelniania uprzywilejowanego mogą wymusić na użytkownikach ponowne zarejestrowanie się przy użyciu istniejących poświadczeń niebędących hasłami (takich jak uwierzytelnianie wieloskładnikowe lub FIDO) i odwołać funkcję "zapamiętaj uwierzytelnianie wieloskładnikowe na urządzeniu", monitując o uwierzytelnianie w sprawie następnego logowania wszystkich użytkowników. Rola [administratora uwierzytelniania](#authentication-administrator) może wymusić ponowną rejestrację i uwierzytelnianie wieloskładnikowe tylko dla użytkowników niebędących administratorami i użytkownikami przypisanymi do następujących ról usługi Azure AD:

* Administrator uwierzytelniania
* Czytniki katalogów
* Zaproszony gość
* Czytnik centrum wiadomości
* Czytnik raportów

### <a name="privileged-role-administrator"></a>[Administrator ról uprzywilejowanych](#privileged-role-administrator-permissions)

Użytkownicy z tą rolą mogą zarządzać przypisaniami ról w usłudze Azure Active Directory, a także w ramach zarządzania tożsamościami uprzywilejowanymi usługami Azure AD. Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami zarządzania tożsamością uprzywilejowaną i jednostek administracyjnych.

> [!IMPORTANT]
> Ta rola umożliwia zarządzanie przydziałami dla wszystkich ról usługi Azure AD, w tym roli administratora globalnego. Ta rola nie obejmuje żadnych innych uprzywilejowanych zdolności w usłudze Azure AD, takich jak tworzenie lub aktualizowanie użytkowników. Jednak użytkownicy przypisani do tej roli mogą przyznać sobie lub innym dodatkowym uprawnieniom, przypisując dodatkowe role.

### <a name="reports-reader"></a>[Czytnik raportów](#reports-reader-permissions)

Użytkownicy z tą rolą mogą wyświetlać dane raportowania użycia i pulpit nawigacyjny raportów w centrum administracyjnym usługi Microsoft 365 oraz pakiet kontekstu przyjęcia w usłudze Power BI. Ponadto rola zapewnia dostęp do raportów logowania i działania w usłudze Azure AD i danych zwracanych przez interfejs API raportowania programu Microsoft Graph. Użytkownik przypisany do roli czytnika raportów może uzyskać dostęp tylko do odpowiednich metryk użycia i wdrażania. Nie mają żadnych uprawnień administratora do konfigurowania ustawień ani uzyskiwania dostępu do centrów administracyjnych specyficznych dla produktu, takich jak Exchange. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

### <a name="search-administrator"></a>[Administrator wyszukiwania](#search-administrator-permissions)

Użytkownicy w tej roli mają pełny dostęp do wszystkich funkcji zarządzania wyszukiwaniem w centrum administracyjnym usługi Microsoft 365. Administratorzy wyszukiwania mogą delegować role Administratorzy wyszukiwania i Edytor wyszukiwania do użytkowników oraz tworzyć zawartość, taką jak zakładki, Q&As i lokalizacje, i zarządzać nimi. Ponadto ci użytkownicy mogą wyświetlać centrum komunikatów, monitorować kondycję usługi i tworzyć żądania usługi.

### <a name="search-editor"></a>[Edytor wyszukiwania](#search-editor-permissions)

Użytkownicy w tej roli mogą tworzyć, zarządzać i usuwać zawartość dla usługi Microsoft Search w centrum administracyjnym usługi Microsoft 365, w tym zakładki, Q&As i lokalizacje.

### <a name="security-administrator"></a>[Administrator zabezpieczeń](#security-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia do zarządzania funkcjami związanymi z zabezpieczeniami w centrum zabezpieczeń usługi Microsoft 365, usłudze Azure Active Directory Identity Protection, usłudze Azure Information Protection i Office 365 Security & Compliance Center. Więcej informacji o uprawnieniach usługi Office 365 można znaleźć w sekcji [Uprawnienia w Centrum zgodności & zabezpieczeń usługi Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

W | Może to zrobić
--- | ---
[Centrum zabezpieczeń usługi Microsoft 365](https://protection.office.com) | Monitorowanie zasad dotyczących zabezpieczeń w usługach Microsoft 365<br>Zarządzanie zagrożeniami i alertami bezpieczeństwa<br>Wyświetlanie raportów
Centrum ochrony tożsamości | Wszystkie uprawnienia roli Czytnik zabezpieczeń<br>Ponadto możliwość wykonywania wszystkich operacji Centrum ochrony tożsamości z wyjątkiem resetowania haseł
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Wszystkie uprawnienia roli Czytnik zabezpieczeń<br>**Nie można** zarządzać przypisaniami lub ustawieniami ról usługi Azure AD
[Centrum zgodności & zabezpieczeń usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie zasadami zabezpieczeń<br>Wyświetlanie, badanie i reagowanie na zagrożenia bezpieczeństwa<br>Wyświetlanie raportów
Azure Advanced Threat Protection | Monitorowanie podejrzanych działań związanych z zabezpieczeniami i reagowanie na nie
Windows Defender ATP i EDR | Przypisywanie ról<br>Zarządzanie grupami maszyn<br>Konfigurowanie wykrywania zagrożeń w punktach końcowych i automatycznego korygowania<br>Wyświetlanie, badanie i reagowanie na alerty
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Wyświetla informacje o użytkowniku, urządzeniu, rejestracji, konfiguracji i aplikacji<br>Nie można wprowadzać zmian w usłudze Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Dodawanie administratorów, dodawanie zasad i ustawień, przekazywanie dzienników i wykonywanie akcji nadzoru
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Może wyświetlać zasady zabezpieczeń, wyświetlać stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia, odrzucać alerty i zalecenia
[Kondycja usługi Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług usługi Office 365

### <a name="security-operator"></a>[Operator zabezpieczeń](#security-operator-permissions)

Użytkownicy z tą rolą mogą zarządzać alertami i mieć globalny dostęp tylko do odczytu w funkcjach związanych z zabezpieczeniami, w tym wszystkie informacje w centrum zabezpieczeń usługi Microsoft 365, usłudze Azure Active Directory, ochronie tożsamości, zarządzaniu tożsamościami uprzywilejowanymi i Centrum zgodności zabezpieczeń & usługi Office 365. Więcej informacji o uprawnieniach usługi Office 365 można znaleźć w sekcji [Uprawnienia w Centrum zgodności & zabezpieczeń usługi Office 365](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

W | Może to zrobić
--- | ---
[Centrum zabezpieczeń usługi Microsoft 365](https://protection.office.com) | Wszystkie uprawnienia roli Czytnik zabezpieczeń<br>Wyświetlanie, badanie i reagowanie na alerty o zagrożeniach bezpieczeństwa
Centrum ochrony tożsamości | Wszystkie uprawnienia roli Czytnik zabezpieczeń<br>Ponadto możliwość wykonywania wszystkich operacji Centrum ochrony tożsamości z wyjątkiem resetowania haseł
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Wszystkie uprawnienia roli Czytnik zabezpieczeń
[Centrum zgodności & zabezpieczeń usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Wszystkie uprawnienia roli Czytnik zabezpieczeń<br>Wyświetlanie, badanie i reagowanie na alerty zabezpieczeń
Windows Defender ATP i EDR | Wszystkie uprawnienia roli Czytnik zabezpieczeń<br>Wyświetlanie, badanie i reagowanie na alerty zabezpieczeń
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Wszystkie uprawnienia roli Czytnik zabezpieczeń
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Wszystkie uprawnienia roli Czytnik zabezpieczeń
[Kondycja usługi Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług usługi Office 365

### <a name="security-reader"></a>[Czytnik zabezpieczeń](#security-reader-permissions)

Użytkownicy korzystający z tej roli mają globalny dostęp tylko do odczytu w funkcji związanej z zabezpieczeniami, w tym wszystkie informacje w centrum zabezpieczeń usługi Microsoft 365, usłudze Azure Active Directory, ochronie tożsamości, zarządzania tożsamościami uprzywilejowanymi, a także możliwość odczytywania raportów logowania usługi Azure Active Directory i dzienników inspekcji oraz w Centrum zgodności zabezpieczeń usługi Office 365 Security &. Więcej informacji o uprawnieniach usługi Office 365 można znaleźć w sekcji [Uprawnienia w Centrum zgodności & zabezpieczeń usługi Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

W | Może to zrobić
--- | ---
[Centrum zabezpieczeń usługi Microsoft 365](https://protection.office.com) | Wyświetlanie zasad dotyczących zabezpieczeń w usługach Microsoft 365<br>Wyświetlanie zagrożeń bezpieczeństwa i alertów<br>Wyświetlanie raportów
Centrum ochrony tożsamości | Przeczytaj wszystkie raporty zabezpieczeń i informacje o ustawieniach dla funkcji zabezpieczeń<br><ul><li>Antyspam<li>Szyfrowanie<li>Zapobieganie utracie danych<li>Ochrona przed złośliwym oprogramowaniem<li>Zaawansowana ochrona przed zagrożeniami<li>Ochrona przed wyłudzaniem informacji<li>Reguły przepływu poczty
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Ma dostęp tylko do odczytu do wszystkich informacji dostępnych w usłudze Azure AD Privileged Identity Management: Zasady i raporty dotyczące przypisań ról usługi Azure AD i przeglądów zabezpieczeń.<br>**Nie można** zarejestrować się w usłudze Azure AD Privileged Identity Management ani wprowadzać w nim żadnych zmian. W portalu Zarządzania tożsamościami uprzywilejowanymi lub za pośrednictwem programu PowerShell osoba w tej roli może aktywować dodatkowe role (na przykład administratora globalnego lub administratora ról uprzywilejowanych), jeśli użytkownik kwalifikuje się do nich.
[Centrum zgodności & zabezpieczeń usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Wyświetlanie zasad zabezpieczeń<br>Wyświetlanie i badanie zagrożeń bezpieczeństwa<br>Wyświetlanie raportów
Windows Defender ATP i EDR | Wyświetlanie i badanie alertów. Po włączeniu kontroli dostępu opartej na rolach w usłudze Windows Defender ATP użytkownicy z uprawnieniami tylko do odczytu, takimi jak rola czytnika zabezpieczeń usługi Azure AD, tracą dostęp, dopóki nie zostaną przypisani do roli narzędzia ATP usługi Windows Defender.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | wyświetla informacje o użytkownikach, urządzeniach, rejestracji, konfiguracji i aplikacji. Nie może wprowadzać zmian w usłudze Intune.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Może wyświetlać zalecenia i alerty, wyświetlać zasady zabezpieczeń, wyświetlać stany zabezpieczeń, ale nie mogą wprowadzać zmian
[Kondycja usługi Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług usługi Office 365

### <a name="service-support-administrator"></a>[Administrator pomocy technicznej](#service-support-administrator-permissions)

Użytkownicy z tą rolą mogą otwierać żądania pomocy technicznej za pomocą usług Microsoft dla platformy Microsoft dla platformy Azure i usługi Office 365 oraz wyświetlać pulpit nawigacyjny usługi i centrum komunikatów w [witrynie Azure portal](https://portal.azure.com) i [centrum administracyjnym usługi Microsoft 365.](https://admin.microsoft.com) Więcej informacji na [temat ról administratora — informacje](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Wcześniej ta rola była nazywana "Administratorem usługi" w [witrynie Azure portal](https://portal.azure.com) i [centrum administracyjnym usługi Microsoft 365](https://admin.microsoft.com). Zmieniliśmy nazwę na "Service Support Administrator", aby wyrównać z nazwą exsiting w interfejsie API programu Microsoft Graph, interfejsie API programu Azure AD Graph i programie Azure AD PowerShell.

### <a name="sharepoint-administrator"></a>[SharePoint Administrator](#sharepoint-service-administrator-permissions)

Użytkownicy z tą rolą mają globalne uprawnienia w usłudze Microsoft SharePoint Online, gdy usługa jest obecna, a także możliwość tworzenia i zarządzania wszystkimi grupami usługi Office 365, zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji na [temat ról administratora — informacje](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> W interfejsie API programu Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "Administrator usługi programu SharePoint". Jest to "Administrator programu SharePoint" w [witrynie Azure portal](https://portal.azure.com).

### <a name="skype-for-business--lync-administrator"></a>[Administrator programu Skype dla firm / Lync](#lync-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w programie Microsoft Skype dla firm, gdy usługa jest obecna, a także zarządzają atrybutami użytkowników specyficznymi dla programu Skype w usłudze Azure Active Directory. Ponadto ta rola umożliwia zarządzanie biletami pomocy technicznej i monitorowanie kondycji usługi oraz dostęp do zespołów i Centrum administracyjnego programu Skype dla firm. Konto musi być również licencjonowane dla zespołów lub nie może uruchamiać poleceń cmdlet programu Teams PowerShell. Więcej informacji na [temat roli administratora programu Skype dla firm](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) i informacji o licencjonowaniu usługi Teams w usłudze [licencjonowania dodatków Skype dla firm i usługi Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> W interfejsie API programu Microsoft Graph i programie Azure AD PowerShell ta rola jest identyfikowana jako "Administrator usługi programu Lync". Jest to "Administrator programu Skype dla firm" w [witrynie Azure portal](https://portal.azure.com/).

### <a name="teams-communications-administrator"></a>[Administrator komunikacji zespołów](#teams-communications-administrator-permissions)

Użytkownicy w tej roli mogą zarządzać aspektami obciążenia usługi Microsoft Teams związanymi z telefonią & głosową. Obejmuje to narzędzia do zarządzania przypisywania numerów telefonów, zasady połączeń głosowych i spełniających oraz pełny dostęp do zestawu narzędzi do analizy połączeń.

### <a name="teams-communications-support-engineer"></a>[Inżynier wsparcia komunikacji zespołowej](#teams-communications-support-engineer-permissions)

Użytkownicy w tej roli mogą rozwiązywać problemy z komunikacją w usłudze Microsoft Teams & programie Skype dla firm za pomocą narzędzi do rozwiązywania problemów z połączeniami użytkownika w centrum administracyjnym programu Microsoft Teams & Skype dla firm. Użytkownicy w tej roli mogą wyświetlać informacje o rekordzie pełnego połączenia dla wszystkich zaangażowanych uczestników. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

### <a name="teams-communications-support-specialist"></a>[Specjalista ds. Wsparcia Komunikacji Zespołowej](#teams-communications-support-specialist-permissions)

Użytkownicy w tej roli mogą rozwiązywać problemy z komunikacją w usłudze Microsoft Teams & programie Skype dla firm za pomocą narzędzi do rozwiązywania problemów z połączeniami użytkownika w centrum administracyjnym programu Microsoft Teams & Skype dla firm. Użytkownicy w tej roli mogą wyświetlać tylko szczegóły użytkownika w wywołaniu dla określonego użytkownika, który wyszukali. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

### <a name="teams-service-administrator"></a>[Administrator usługi teams](#teams-service-administrator-permissions)

Użytkownicy w tej roli mogą zarządzać wszystkimi aspektami obciążenia usługi Microsoft Teams za pośrednictwem usługi Microsoft Teams & centrum administracyjnego programu Skype dla firm i odpowiednich modułów programu PowerShell. Obejmuje to, między innymi, wszystkie narzędzia do zarządzania związane z telefonią, wiadomościami, spotkaniami i samymi zespołami. Ta rola dodatkowo daje możliwość tworzenia i zarządzania wszystkimi grupami usługi Office 365, zarządzania biletami pomocy technicznej i monitorowania kondycji usługi.

### <a name="user-administrator"></a>[Administrator użytkowników](#user-administrator-permissions)

Użytkownicy z tą rolą mogą tworzyć użytkowników i zarządzać wszystkimi aspektami użytkowników z pewnymi ograniczeniami (patrz poniżej) i mogą aktualizować zasady wygasania haseł. Ponadto użytkownicy z tą rolą mogą tworzyć wszystkie grupy i zarządzać nimi. Ta rola obejmuje również możliwość tworzenia i zarządzania widokami użytkowników, zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Administratorzy użytkowników nie mają uprawnień do zarządzania niektórymi właściwościami użytkowników dla użytkowników w większości ról administratora. Użytkownik z tą rolą nie ma przechyłów do zarządzania uwierzytelniać wieloskładnikowej usługi. Role, które są wyjątkami od tego ograniczenia są wymienione w poniższej tabeli.

| | |
| --- | --- |
|Uprawnienia ogólne|<p>Tworzenie użytkowników i grup</p><p>Tworzenie widoków użytkowników i zarządzanie nimi</p><p>Zarządzanie biletami pomocy technicznej pakietu Office<p>Aktualizowanie zasad wygasania haseł|
|<p>Na wszystkich użytkownikach, w tym wszystkich administratorach</p>|<p>Zarządzanie licencjami</p><p>Zarządzanie wszystkimi właściwościami użytkownika z wyjątkiem głównej nazwy użytkownika</p>
|Tylko na użytkownikach, którzy nie są administratorami lub w dowolnej z następujących ról administratora ograniczonego:<ul><li>Czytniki katalogów<li>Zaproszony gość<li>Administrator działu pomocy technicznej<li>Czytnik centrum wiadomości<li>Czytnik raportów<li>Administrator użytkowników|<p>Usuwanie i przywracanie</p><p>Wyłączanie i włączanie</p><p>Unieważnij tokeny odświeżania</p><p>Zarządzanie wszystkimi właściwościami użytkownika, w tym nazwą główną użytkownika</p><p>Resetowanie hasła</p><p>Przyciski urządzenia aktualizacji (FIDO)</p>|

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać hasła dla osób, które mogą mieć dostęp do poufnych lub prywatnych informacji lub krytycznej konfiguracji wewnątrz i na zewnątrz usługi Azure Active Directory. Zmiana hasła użytkownika może oznaczać możliwość przyjęcia tożsamości i uprawnień tego użytkownika. Przykład:
>
>- Rejestracja aplikacji i właściciele aplikacji korporacyjnych, którzy mogą zarządzać poświadczeniami aplikacji, których są właścicielami. Te aplikacje mogą mieć uprawnienia uprzywilejowane w usłudze Azure AD i w innym miejscu nie są przyznawane administratorom użytkowników. Za pomocą tej ścieżki administrator użytkownika może być w stanie przyjąć tożsamość właściciela aplikacji, a następnie dalej przyjąć tożsamość aplikacji uprzywilejowanej, aktualizując poświadczenia dla aplikacji.
>- Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do poufnych lub prywatnych informacji lub krytycznej konfiguracji na platformie Azure.
>- Grupy zabezpieczeń i właścicieli grup usługi Office 365, którzy mogą zarządzać członkostwem w grupie. Te grupy mogą udzielić dostępu do poufnych lub prywatnych informacji lub konfiguracji krytycznej w usłudze Azure AD i w innych miejscach.
>- Administratorzy w innych usługach poza usługą Azure AD, takich jak Exchange Online, Office Security and Compliance Center oraz systemy zasobów ludzkich.
>- Osoby niebędące administratorami, takie jak kadra kierownicza, radca prawny i pracownicy działu zasobów ludzkich, którzy mogą mieć dostęp do poufnych lub prywatnych informacji.

## <a name="role-permissions"></a>Uprawnienia roli

W poniższych tabelach opisano określone uprawnienia w usłudze Azure Active Directory przyznane każdej roli. Niektóre role mogą mieć dodatkowe uprawnienia w usługach firmy Microsoft poza usługą Azure Active Directory.

### <a name="application-administrator-permissions"></a>Uprawnienia administratora aplikacji

Może tworzyć i zarządzać wszystkimi aspektami rejestracji aplikacji i aplikacji dla przedsiębiorstw.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/Application/appProxyAuthentication/update | Aktualizowanie właściwości uwierzytelniania serwera proxy aplikacji w jednostkach usługi w usłudze Azure Active Directory. |
| microsoft.directory/Application/appProxyUrlSettings/update | Aktualizowanie wewnętrznych i zewnętrznych adresów URL serwera proxy aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/applicationProxy/read | Przeczytaj wszystkie właściwości serwera proxy aplikacji. |
| microsoft.directory/applications/applicationProxy/update | Zaktualizuj wszystkie właściwości serwera proxy aplikacji. |
| microsoft.directory/applications/audience/update | Aktualizuj właściwość applications.audience w usłudze Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aktualizuj właściwość applications.authentication w usłudze Azure Active Directory. |
| microsoft.directory/applications/basic/update | Aktualizowanie podstawowych właściwości aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/create | Tworzenie aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Aktualizuj właściwość applications.credentials w usłudze Azure Active Directory. |
| microsoft.directory/applications/delete | Usuwanie aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/owners/update | Aktualizuj właściwość applications.owners w usłudze Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Aktualizuj właściwość applications.permissions w usłudze Azure Active Directory. |
| microsoft.directory/applications/policies/update | Aktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Tworzenie appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/appRoleAssignments/read | Odczytuj przypisania aplikacjiRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Aktualizowanie appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Usuń appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w auditLogs w usłudze Azure Active Directory. |
| microsoft.directory/connectorGroups/everything/read | Odczytywanie właściwości grupy łączników serwera proxy aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/connectorGroups/everything/update | Zaktualizuj wszystkie właściwości grupy łączników serwera proxy aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/connectorGroups/create | Tworzenie grup łączników serwera proxy aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/connectorGrupy/usuń | Usuwanie grup łączników serwera proxy aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/connectors/everything/read | Przeczytaj wszystkie właściwości łącznika serwera proxy aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/łączniki/tworzenie | Tworzenie łączników serwera proxy aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Przeczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Aktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Przeczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Aktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Przeczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizacja właściwości servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Aktualizuj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Zaktualizuj właściwość servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Zaktualizuj właściwość servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aktualizowanie podstawowych właściwości w usługachPrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | Tworzenie servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Zaktualizuj właściwość servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Usuń servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Zaktualizuj właściwość servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="application-developer-permissions"></a>Uprawnienia dewelopera aplikacji

Można tworzyć rejestracje aplikacji niezależnie od ustawienia "Użytkownicy mogą rejestrować aplikacje".

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | Tworzenie aplikacji w usłudze Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a utworzony obiekt liczy się z przydziałem 250 utworzonych obiektów twórcy. |
| microsoft.directory/appRoleAssignments/createAsOwner | Tworzenie appRoleAssignments w usłudze Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a utworzony obiekt liczy się z przydziałem 250 utworzonych obiektów twórcy. |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | Tworzenie oAuth2PermissionGrants w usłudze Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a utworzony obiekt liczy się z przydziałem 250 utworzonych obiektów twórcy. |
| microsoft.directory/servicePrincipals/createAsOwner | Tworzenie servicePrincipals w usłudze Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a utworzony obiekt liczy się z przydziałem 250 utworzonych obiektów twórcy. |

### <a name="authentication-administrator-permissions"></a>Uprawnienia administratora uwierzytelniania

Dozwolone wyświetlanie, ustawianie i resetowanie informacji o metodzie uwierzytelniania dla każdego użytkownika niebędącego administratorem.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Unieważnij wszystkie tokeny odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Aktualizowanie właściwości silnego uwierzytelniania, takich jak informacje o poświadczeniach usługi MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.directory/users/password/update | Aktualizowanie haseł dla wszystkich użytkowników w organizacji usługi Office 365. Więcej informacji można znaleźć w dokumentacji online. |

### <a name="azure-devops-administrator-permissions"></a>Uprawnienia administratora programu Azure DevOps

Może zarządzać zasadami i ustawieniami organizacji usługi Azure DevOps.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz [opis roli](#azure-devops-administrator) powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Odczytuj i konfiguruj usługi Azure DevOps. |

### <a name="azure-information-protection-administrator-permissions"></a>Uprawnienia administratora usługi Azure Information Protection

Może zarządzać wszystkimi aspektami usługi Azure Information Protection.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz [opis roli](#) powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="b2c-user-flow-administrator-permissions"></a>Uprawnienia administratora przepływu użytkowników B2C

Tworzenie i zarządzanie wszystkimi aspektami przepływów użytkowników.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Odczytywanie i konfigurowanie przepływów użytkowników w usłudze Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>Uprawnienia administratora atrybutów przepływu użytkownika B2C

Tworzenie schematu atrybutu dostępnego dla wszystkich przepływów użytkownika i zarządzanie nim.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Odczytywanie i konfigurowanie atrybutów użytkowników w usłudze Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>Uprawnienia administratora zestawu kluczy B2C IEF

Zarządzanie wpisami tajnymi dla federacji i szyfrowania w ramach środowiska tożsamości.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Odczytywanie i konfigurowanie zestawów kluczy w usłudze Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Uprawnienia administratora zasad IEF B2C

Tworzenie zasad struktury zaufania i zarządzanie nimi w ramach środowiska tożsamości.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Odczytuj i konfiguruj zasady niestandardowe w usłudze Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Uprawnienia administratora rozliczeń

Może wykonywać typowe zadania związane z rozliczeniami, takie jak aktualizowanie informacji o płatności.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/organization/basic/update | Aktualizowanie podstawowych właściwości organizacji w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.commerce.billing/allEntities/allTasks | Zarządzaj wszystkimi aspektami rozliczeń usługi Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="cloud-application-administrator-permissions"></a>Uprawnienia administratora aplikacji w chmurze

Może tworzyć i zarządzać wszystkimi aspektami rejestracji aplikacji i aplikacji dla przedsiębiorstw z wyjątkiem serwera proxy aplikacji.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/applications/audience/update | Aktualizuj właściwość applications.audience w usłudze Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aktualizuj właściwość applications.authentication w usłudze Azure Active Directory. |
| microsoft.directory/applications/basic/update | Aktualizowanie podstawowych właściwości aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/create | Tworzenie aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Aktualizuj właściwość applications.credentials w usłudze Azure Active Directory. |
| microsoft.directory/applications/delete | Usuwanie aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/owners/update | Aktualizuj właściwość applications.owners w usłudze Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Aktualizuj właściwość applications.permissions w usłudze Azure Active Directory. |
| microsoft.directory/applications/policies/update | Aktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Tworzenie appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Aktualizowanie appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Usuń appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w auditLogs w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Przeczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Aktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Przeczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Aktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Przeczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizacja właściwości servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Aktualizuj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Zaktualizuj właściwość servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Zaktualizuj właściwość servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aktualizowanie podstawowych właściwości w usługachPrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | Tworzenie servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Zaktualizuj właściwość servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Usuń servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Zaktualizuj właściwość servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="cloud-device-administrator-permissions"></a>Uprawnienia administratora urządzeń w chmurze

Pełny dostęp do zarządzania urządzeniami w usłudze Azure AD.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w auditLogs w usłudze Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Odczytuj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.directory/devices/delete | Usuwanie urządzeń w usłudze Azure Active Directory. |
| microsoft.directory/devices/disable | Wyłączanie urządzeń w usłudze Azure Active Directory. |
| microsoft.directory/devices/enable | Włącz urządzenia w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |

### <a name="company-administrator-permissions"></a>Uprawnienia administratora firmy

Może zarządzać wszystkimi aspektami usług Usługi Azure AD i microsoft, które używają tożsamości usługi Azure AD. Ta rola jest również znana jako rola administratora globalnego. 

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w witrynie microsoft.aad.cloudAppSecurity. |
| microsoft.directory/administrativeJednosty/wszystkieWłaściwości/wszystkieZadowonia | Tworzenie i usuwanie jednostek administracyjnych oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/applications/allProperties/allTasks | Tworzenie i usuwanie aplikacji oraz odczytywanie i aktualizowanie wszystkich właściwości usługi Azure Active Directory. |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | Tworzenie i usuwanie appRoleAssignments oraz odczytu i aktualizacji wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w auditLogs w usłudze Azure Active Directory. |
| microsoft.directory/contacts/allProperties/allTasks | Tworzenie i usuwanie kontaktów oraz odczytywanie i aktualizowanie wszystkich właściwości usługi Azure Active Directory. |
| microsoft.directory/contracts/allProperties/allTasks | Tworzenie i usuwanie kontraktów oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/devices/allProperties/allTasks | Tworzenie i usuwanie urządzeń oraz odczytywanie i aktualizowanie wszystkich właściwości usługi Azure Active Directory. |
| microsoft.directory/directoryRoles/allProperties/allTasks | Tworzenie i usuwanie funkcji katalogowychrole oraz odczytywanie i aktualizowanie wszystkich właściwości usługi Azure Active Directory. |
| microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Tworzenie i usuwanie tablic katalogowychRoleTemplates oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/domains/allProperties/allTasks | Tworzenie i usuwanie domen oraz odczytywanie i aktualizowanie wszystkich właściwości usługi Azure Active Directory. |
| microsoft.directory/groups/allProperties/allTasks | Tworzenie i usuwanie grup oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/groupSettings/allProperties/allTasks | Tworzenie i usuwanie grupStawienia oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/allProperties/allTasks | Tworzenie i usuwanie groupSettingTemplates oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | Tworzenie i usuwanie loginTenantBranding i odczytu i aktualizacji wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Tworzenie i usuwanie oAuth2PermissionGrants i odczytu i aktualizacji wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/organization/allProperties/allTasks | Tworzenie i usuwanie organizacji oraz odczytywanie i aktualizowanie wszystkich właściwości usługi Azure Active Directory. |
| microsoft.directory/policies/allProperties/allTasks | Tworzenie i usuwanie zasad oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/roleAssignments/allProperties/allTasks | Tworzenie i usuwanie rólAsypisy oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Tworzenie i usuwanie funkcjiDefinitions oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Tworzenie i usuwanie scopedRoleMemberships oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/serviceAkcja/aktywowanie Usługi | Można wykonać akcję Aktywowanie usługi usługi w usłudze Azure Active Directory |
| microsoft.directory/serviceAction/disableDirectoryFeature | Można wykonać akcję usługi Wyłączanie katalogów w usłudze Azure Active Directory |
| microsoft.directory/serviceAction/enableDirectoryFeature | Można wykonać akcję usługi Włącz katalogi w usłudze Azure Active Directory |
| microsoft.directory/serviceAkcja/getDostępneWywłaściwośćprotencjonalna | Można wykonać akcję usługi Getavailableextentionproperties w usłudze Azure Active Directory |
| microsoft.directory/servicePrincipals/allProperties/allTasks | Tworzenie i usuwanie servicePrincipals i odczytu i aktualizacji wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w signInReports w usłudze Azure Active Directory. |
| microsoft.directory/subscribedSkus/allProperties/allTasks | Tworzenie i usuwanie subskrybowanychSkus i odczytu i aktualizacji wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.directory/users/allProperties/allTasks | Tworzenie i usuwanie użytkowników oraz odczytywanie i aktualizowanie wszystkich właściwości usługi Azure Active Directory. |
| microsoft.directorySync/allEntities/allTasks | Wykonaj wszystkie akcje w usłudze Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w witrynie microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Przeczytaj wszystkie zasoby w pliku microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Przeczytaj wszystkie zasoby w witrynie microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.commerce.billing/allEntities/allTasks | Zarządzaj wszystkimi aspektami rozliczeń usługi Office 365. |
| microsoft.intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Zarządzanie wszystkimi aspektami Menedżera zgodności usługi Office 365 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Zarządzaj wszystkimi aspektami analizy pulpitu. |
| microsoft.office365.exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| microsoft.office365.skrytka/wszystkieEntities/allTasks | Zarządzanie wszystkimi aspektami skrytki klienta usługi Office 365 |
| microsoft.office365.messageCenter/messages/read | Odczytywanie wiadomości w witrynie microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Odczyt zabezpieczeńWiadomie w microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Zarządzaj wszystkimi aspektami Centrum ochrony usługi Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w witrynie microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w programie microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.office365.usageRaporty/wszystkieEnty/odczyt | Przeczytaj raporty użycia usługi Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Przeczytaj wszystkie zasoby w witrynie microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator-permissions"></a>Uprawnienia administratora zgodności

Może odczytywać konfigurację i raporty zgodności i zarządzać nimi w usłudze Azure AD i usłudze Office 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Zarządzanie wszystkimi aspektami Menedżera zgodności usługi Office 365 |
| microsoft.office365.exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w programie microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="compliance-data-administrator-permissions"></a>Uprawnienia administratora danych zgodności

Tworzy zawartość zgodności i zarządza nią.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Odczytuj i skonfiguruj usługę Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Zarządzanie wszystkimi aspektami Menedżera zgodności usługi Office 365 |
| microsoft.office365.exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w programie microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="conditional-access-administrator-permissions"></a>Uprawnienia administratora dostępu warunkowego

Może zarządzać możliwościami dostępu warunkowego.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | Odczyt właściwości policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/update | Aktualizuj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/read | Odczyt właściwości policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/update | Aktualizuj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | Odczyt właściwości policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | Aktualizuj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Uprawnienia administratora usługi CRM

Może zarządzać wszystkimi aspektami produktu Dynamics 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="customer-lockbox-access-approver-permissions"></a>Uprawnienia osoby zatwierdzającej dostępu do blokady klienta

Może zatwierdzać żądania pomocy technicznej firmy Microsoft w celu uzyskania dostępu do danych organizacji klienta.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.skrytka/wszystkieEntities/allTasks | Zarządzanie wszystkimi aspektami skrytki klienta usługi Office 365 |

### <a name="desktop-analytics-administrator-permissions"></a>Uprawnienia administratora analizy na pulpicie

Może zarządzać usługami analizy pulpitu i dostosowywania pakietu Office & zasady. W przypadku analizy komputerowej obejmuje to możliwość wyświetlania zasobów, tworzenia planów wdrażania, wyświetlania wdrożenia i stanu kondycji. W przypadku usługi Dostosowania pakietu Office & zasady ta rola umożliwia użytkownikom zarządzanie zasadami pakietu Office.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Zarządzaj wszystkimi aspektami analizy pulpitu. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="device-administrators-permissions"></a>Uprawnienia administratorzy urządzeń

Użytkownicy przypisani do tej roli są dodawane do grupy administratorów lokalnych na urządzeniach przyłączonych do usługi Azure AD.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/groupSettings/basic/read | Odczytuj podstawowe właściwości w witrynie groupSettings w usłudze Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Odczytuj podstawowe właściwości w platformie GroupSettingTemplates w usłudze Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Uprawnienia czytników katalogów
Może odczytywać podstawowe informacje katalogowe. Do udzielania dostępu do aplikacji, nie przeznaczonych dla użytkowników.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/administrativeJednojniejne/podstawowe/odczyt | Odczytuj podstawowe właściwości jednostek administracyjnych w usłudze Azure Active Directory. |
| microsoft.directory/administrativeJednojniejs/członkowie/odczyt | Odczyt właściwości administrativeUnits.members w usłudze Azure Active Directory. |
| microsoft.directory/applications/basic/read | Odczytuj podstawowe właściwości aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/owners/read | Odczytuj właściwość applications.owners w usłudze Azure Active Directory. |
| microsoft.directory/applications/policies/read | Odczytuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.directory/contacts/basic/read | Odczytuj podstawowe właściwości kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read | Odczyt właściwości contacts.memberOf w usłudze Azure Active Directory. |
| microsoft.directory/contracts/basic/read | Odczytuj podstawowe właściwości kontraktów w usłudze Azure Active Directory. |
| microsoft.directory/devices/basic/read | Odczytuj podstawowe właściwości na urządzeniach w usłudze Azure Active Directory. |
| microsoft.directory/devices/memberOf/read | Odczyt właściwości devices.memberOf w usłudze Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read | Odczytuj właściwość devices.registeredOwners w usłudze Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/read | Odczytuj właściwość devices.registeredUsers w usłudze Azure Active Directory. |
| microsoft.directory/directoryRoles/basic/read | Odczytuj podstawowe właściwości w kataloguRole w usłudze Azure Active Directory. |
| microsoft.directory/directoryRoles/eligibleMembers/read | Odczytu directoryRoles.eligibleWłaściwość w usłudze Azure Active Directory. |
| microsoft.directory/directoryRoles/members/read | Odczytu właściwość DirectoryRoles.members w usłudze Azure Active Directory. |
| microsoft.directory/domains/basic/read | Odczytuj podstawowe właściwości w domenach w usłudze Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/read | Odczyt właściwości groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/groups/basic/read | Odczytanie podstawowych właściwości w grupach w usłudze Azure Active Directory. |
| microsoft.directory/groups/memberOf/read | Odczyt właściwości groups.memberOf w usłudze Azure Active Directory. |
| microsoft.directory/groups/members/read | Odczyt właściwości groups.members w usłudze Azure Active Directory. |
| microsoft.directory/groups/owners/read | Odczytywanie właściwości groups.owners w usłudze Azure Active Directory. |
| microsoft.directory/groups/settings/read | Odczyt właściwości groups.settings w usłudze Azure Active Directory. |
| microsoft.directory/groupSettings/basic/read | Odczytuj podstawowe właściwości w witrynie groupSettings w usłudze Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Odczytuj podstawowe właściwości w platformie GroupSettingTemplates w usłudze Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read | Odczytuj podstawowe właściwości na oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.directory/organization/basic/read | Przeczytaj podstawowe właściwości organizacji w usłudze Azure Active Directory. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Przeczytaj właściwość organization.trustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.directory/roleAssignments/basic/read | Odczytuj podstawowe właściwości dla rólAsypisy w usłudze Azure Active Directory. |
| microsoft.directory/roleDefinitions/basic/read | Odczytuj podstawowe właściwości funkcjiDefinitions w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Odczyt servicePrincipals.appRoleAssignedTo właściwość w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Odczyt servicePrincipals.appRoleAssignments właściwości w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read | Odczytuj podstawowe właściwości w serwisiePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read | Odczyt servicePrincipals.memberOf właściwości w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Odczyt servicePrincipals.oAuth2PermissionGrants właściwości w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Odczyt servicePrincipals.ownedObjects właściwość w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read | Przeczytaj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read | Przeczytaj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read | Odczytuj podstawowe właściwości subskrybentówSkus w usłudze Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read | Odczyt właściwości users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/users/basic/read | Odczytuj podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/directReports/read | Odczyt właściwości users.directReports w usłudze Azure Active Directory. |
| microsoft.directory/users/manager/read | Odczyt właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.directory/users/memberOf/read | Odczyt właściwości users.memberOf w usłudze Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Odczyt właściwości users.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Odczyt właściwości users.ownedDevices w usłudze Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read | Odczyt właściwości users.ownedObjects w usłudze Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read | Odczyt właściwości users.registeredDevices w usłudze Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Uprawnienia konta synchronizacji katalogów

Używane tylko przez usługę Azure AD Connect.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/organization/dirSync/update | Zaktualizuj właściwość organization.dirSync w usłudze Azure Active Directory. |
| microsoft.directory/policies/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/basic/read | Przeczytaj podstawowe właściwości zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/basic/update | Aktualizowanie podstawowych właściwości zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/owners/read | Przeczytaj właściwość policies.owners w usłudze Azure Active Directory. |
| microsoft.directory/policies/owners/update | Aktualizuj właściwość policies.owners w usłudze Azure Active Directory. |
| microsoft.directory/policies/policiesAppliedTo/read | Przeczytaj właściwość policies.policiesAppliedTo w usłudze Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Aktualizuj właściwość policies.tenantDefault w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Odczyt servicePrincipals.appRoleAssignedTo właściwość w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizacja właściwości servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Odczyt servicePrincipals.appRoleAssignments właściwości w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Aktualizuj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Zaktualizuj właściwość servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Zaktualizuj właściwość servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read | Odczytuj podstawowe właściwości w serwisiePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aktualizowanie podstawowych właściwości w usługachPrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | Tworzenie servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Zaktualizuj właściwość servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read | Odczyt servicePrincipals.memberOf właściwości w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Odczyt servicePrincipals.oAuth2PermissionGrants właściwości w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read | Przeczytaj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Odczyt servicePrincipals.ownedObjects właściwość w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Zaktualizuj właściwość servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read | Przeczytaj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.directorySync/allEntities/allTasks | Wykonaj wszystkie akcje w usłudze Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Uprawnienia autorów katalogów

Może odczytywać & zapisywać podstawowe informacje katalogowe. Do udzielania dostępu do aplikacji, nie przeznaczonych dla użytkowników.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/groups/create microsoft.directory/groups/create microsoft.directory/groups/create microsoft. | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a utworzony obiekt liczy się z przydziałem 250 utworzonych obiektów twórcy. |
| microsoft.directory/groups/appRoleAssignments/update | Aktualizuj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aktualizowanie podstawowych właściwości w grupach w usłudze Azure Active Directory. |
| microsoft.directory/groups/members/update | Aktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aktualizuj właściwość groups.settings w usłudze Azure Active Directory. |
| microsoft.directory/groupSettings/basic/update | Aktualizowanie podstawowych właściwości w witrynie groupSettings w usłudze Azure Active Directory. |
| microsoft.directory/groupSettings/create | Tworzenie grupStawienia w usłudze Azure Active Directory. |
| microsoft.directory/groupSettings/delete | Usuń ustawienia grup w usłudze Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/users/assignLicense | Zarządzanie licencjami dla użytkowników usługi Azure Active Directory. |
| microsoft.directory/users/basic/update | Aktualizowanie podstawowych właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Unieważnij wszystkie tokeny odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/manager/update | Aktualizowanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Uprawnienia administratora usługi programu Exchange

Może zarządzać wszystkimi aspektami produktu Exchange.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aktualizuj właściwość groups.unified w usłudze Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aktualizowanie podstawowych właściwości grup usługi Office 365. |
| microsoft.directory/groups/unified/create | Tworzenie grup usługi Office 365. |
| microsoft.directory/groups/unified/delete | Usuwanie grup usługi Office 365. |
| microsoft.directory/groups/unified/members/update | Aktualizowanie członkostwa w grupach usługi Office 365. |
| microsoft.directory/groups/unified/owners/update | Aktualizowanie własności grup usługi Office 365. |
| microsoft.office365.exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| microsoft.office365.network/performance/allProperties/read | Odczytywanie stron wydajności sieci w Centrum administracyjnym M365. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.office365.usageRaporty/wszystkieEnty/odczyt | Przeczytaj raporty użycia usługi Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |

### <a name="external-identity-provider-administrator-permissions"></a>Uprawnienia administratora zewnętrznego dostawcy tożsamości

Konfigurowanie dostawców tożsamości do użytku w bezpośredniej federacji.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Odczytuj i konfiguruj dostawców tożsamości w usłudze Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Uprawnienia czytnika globalnego
Może czytać wszystko, co administrator globalny może, ale nie edytować niczego.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz [opis roli](#global-reader) powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | Przeczytaj wszystkie aspekty rozliczeń usługi Office 365. |
| microsoft.directory/administrativeJednojniejne/podstawowe/odczyt    | Odczytuj podstawowe właściwości jednostek administracyjnych w usłudze Azure Active Directory. |
| microsoft.directory/administrativeJednojniejs/członkowie/odczyt    | Odczyt właściwości administrativeUnits.members w usłudze Azure Active Directory. |
| microsoft.directory/applications/basic/read    | Odczytuj podstawowe właściwości aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/owners/read    | Odczytuj właściwość applications.owners w usłudze Azure Active Directory. |
| microsoft.directory/applications/policies/read    | Odczytuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.directory/contacts/basic/read    | Odczytuj podstawowe właściwości kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read    | Odczyt właściwości contacts.memberOf w usłudze Azure Active Directory. |
| microsoft.directory/contracts/basic/read    | Odczytuj podstawowe właściwości kontraktów w usłudze Azure Active Directory. |
| microsoft.directory/devices/basic/read    | Odczytuj podstawowe właściwości na urządzeniach w usłudze Azure Active Directory. |
| microsoft.directory/devices/memberOf/read    | Odczyt właściwości devices.memberOf w usłudze Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read    | Odczytuj właściwość devices.registeredOwners w usłudze Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/read    | Odczytuj właściwość devices.registeredUsers w usłudze Azure Active Directory. |
| microsoft.directory/directoryRoles/basic/read    | Odczytuj podstawowe właściwości w kataloguRole w usłudze Azure Active Directory. |
| microsoft.directory/directoryRoles/eligibleMembers/read    | Odczytu directoryRoles.eligibleWłaściwość w usłudze Azure Active Directory. |
| microsoft.directory/directoryRoles/members/read    | Odczytu właściwość DirectoryRoles.members w usłudze Azure Active Directory. |
| microsoft.directory/domains/basic/read    | Odczytuj podstawowe właściwości w domenach w usłudze Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/read    | Odczyt właściwości groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/groups/basic/read    | Odczytanie podstawowych właściwości w grupach w usłudze Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read    | Odczytaj właściwość groups.hiddenMembers w usłudze Azure Active Directory. |
| microsoft.directory/groups/memberOf/read    | Odczyt właściwości groups.memberOf w usłudze Azure Active Directory. |
| microsoft.directory/groups/members/read    | Odczyt właściwości groups.members w usłudze Azure Active Directory. |
| microsoft.directory/groups/owners/read    | Odczytywanie właściwości groups.owners w usłudze Azure Active Directory. |
| microsoft.directory/groups/settings/read    | Odczyt właściwości groups.settings w usłudze Azure Active Directory. |
| microsoft.directory/groupSettings/basic/read    | Odczytuj podstawowe właściwości w witrynie groupSettings w usłudze Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read    | Odczytuj podstawowe właściwości w platformie GroupSettingTemplates w usłudze Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read    | Odczytuj podstawowe właściwości na oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.directory/organization/basic/read    | Przeczytaj podstawowe właściwości organizacji w usłudze Azure Active Directory. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read    | Przeczytaj właściwość organization.trustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.directory/policies/standard/read    | Przeczytaj standardowe zasady w usłudze Azure Active Directory. |
| microsoft.directory/roleAssignments/basic/read    | Odczytuj podstawowe właściwości dla rólAsypisy w usłudze Azure Active Directory. |
| microsoft.directory/roleDefinitions/basic/read    | Odczytuj podstawowe właściwości funkcjiDefinitions w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read    | Odczyt servicePrincipals.appRoleAssignedTo właściwość w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read    | Odczyt servicePrincipals.appRoleAssignments właściwości w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read    | Odczytuj podstawowe właściwości w serwisiePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read    | Odczyt servicePrincipals.memberOf właściwości w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read    | Odczyt servicePrincipals.oAuth2PermissionGrants właściwości w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read    | Odczyt servicePrincipals.ownedObjects właściwość w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read    | Przeczytaj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read    | Przeczytaj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read    | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w signInReports w usłudze Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read    | Odczytuj podstawowe właściwości subskrybentówSkus w usłudze Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read    | Odczyt właściwości users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/users/basic/read    | Odczytuj podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/directReports/read    | Odczyt właściwości users.directReports w usłudze Azure Active Directory. |
| microsoft.directory/users/manager/read    | Odczyt właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.directory/users/memberOf/read    | Odczyt właściwości users.memberOf w usłudze Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read    | Odczyt właściwości users.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read    | Odczyt właściwości users.ownedDevices w usłudze Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read    | Odczyt właściwości users.ownedObjects w usłudze Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read    | Odczyt właściwości users.registeredDevices w usłudze Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/read    | Odczytuj silne właściwości uwierzytelniania, takie jak informacje o poświadczeniach usługi MFA. |
| microsoft.office365.exchange/allEntities/read    | Przeczytaj wszystkie aspekty usługi Exchange Online. |
| microsoft.office365.messageCenter/messages/read    | Odczytywanie wiadomości w witrynie microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read    | Odczyt zabezpieczeńWiadomie w microsoft.office365.messageCenter. |
| microsoft.office365.network/performance/allProperties/read | Odczytywanie stron wydajności sieci w Centrum administracyjnym M365. |
| microsoft.office365.protectionCenter/allEntities/read    | Przeczytaj wszystkie aspekty Centrum ochrony usługi Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/read    | Przeczytaj wszystkie standardowe właściwości w witrynie microsoft.office365.securityComplianceCenter. |
| microsoft.office365.usageRaporty/wszystkieEnty/odczyt    | Przeczytaj raporty użycia usługi Office 365. |
| microsoft.office365.webPortal/allEntities/standard/read    | Odczytuj standardowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |

### <a name="groups-administrator-permissions"></a>Uprawnienia administratora grup
Może zarządzać wszystkimi aspektami grup i ustawieniami grupy, takimi jak zasady nazewnictwa i wygasania.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/groups/basic/read | Odczytanie standardowych właściwości w grupach w usłudze Azure Active Directory.  |
| microsoft.directory/groups/basic/update | Aktualizowanie podstawowych właściwości w grupach w usłudze Azure Active Directory. |
| microsoft.directory/groups/create microsoft.directory/groups/create microsoft.directory/groups/create microsoft. | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a utworzony obiekt liczy się z przydziałem 250 utworzonych obiektów twórcy. |
| microsoft.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Odczytaj właściwość groups.hiddenMembers w usłudze Azure Active Directory. |
| microsoft.directory/groups/members/update | Aktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.directory/groups/restore | Przywracanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aktualizuj właściwość groups.settings w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.messageCenter/messages/read | Odczytywanie wiadomości w witrynie microsoft.office365.messageCenter. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="guest-inviter-permissions"></a>Uprawnienia osoby zapraszania gościa
Może zapraszać gości niezależnych od ustawienia "członkowie mogą zapraszać gości".

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read | Odczyt właściwości users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/users/basic/read | Odczytuj podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/directReports/read | Odczyt właściwości users.directReports w usłudze Azure Active Directory. |
| microsoft.directory/users/inviteGuest | Zapraszanie użytkowników-gości w usłudze Azure Active Directory. |
| microsoft.directory/users/manager/read | Odczyt właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.directory/users/memberOf/read | Odczyt właściwości users.memberOf w usłudze Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Odczyt właściwości users.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Odczyt właściwości users.ownedDevices w usłudze Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read | Odczyt właściwości users.ownedObjects w usłudze Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read | Odczyt właściwości users.registeredDevices w usłudze Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Uprawnienia administratora pomocy technicznej

Można resetować hasła dla osób niebędących administratorami i administratorami pomocy technicznej.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Odczytuj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Unieważnij wszystkie tokeny odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/password/update | Aktualizowanie haseł dla wszystkich użytkowników usługi Azure Active Directory. Więcej informacji można znaleźć w dokumentacji online. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="hybrid-identity-administrator-permissions"></a>Uprawnienia administratora tożsamości hybrydowej

Włączanie, wdrażanie, konfigurowanie, zarządzanie, monitorowanie i rozwiązywanie problemów z usługami inicjowania obsługi administracyjnej i uwierzytelniania w chmurze. 

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.directory/applications/audience/update  | Aktualizuj właściwość applications.audience w usłudze Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aktualizuj właściwość applications.authentication w usłudze Azure Active Directory.  |
| microsoft.directory/applications/basic/update | Aktualizowanie podstawowych właściwości aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/create | Tworzenie aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Aktualizuj właściwość applications.credentials w usłudze Azure Active Directory. |
| microsoft.directory/applications/delete | Usuwanie aplikacji w usłudze Azure Active Directory. |
| microsoft.directory/applications/owners/update | Aktualizuj właściwość applications.owners w usłudze Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Aktualizuj właściwość applications.permissions w usłudze Azure Active Directory. |
| microsoft.directory/applications/policies/update | Aktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.directory/applicationTemplates/instantiate | Tworzenie wystąpienia aplikacji galerii z szablonów aplikacji. |
| microsoft.directory/auditLogs/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w auditLogs w usłudze Azure Active Directory. |
| microsoft.directory/cloudProvisioning/allProperties/allTasks | Odczytuj i skonfiguruj wszystkie właściwości usługi azure ad cloud inicjowania obsługi administracyjnej. |
| microsoft.directory/federatedAuthentication/allProperties/allTasks | Zarządzaj wszystkimi aspektami usług Federacyjnych Usługi Federacyjne usługi Active Directory (ADFS) lub zewnętrznego dostawcy federacji w usłudze Azure AD. |
| microsoft.directory/organization/dirSync/update | Zaktualizuj właściwość organization.dirSync w usłudze Azure Active Directory. |
| microsoft.directory/passwordHashSync/allProperties/allTasks | Zarządzaj wszystkimi aspektami synchronizacji skrótów haseł (PHS) w usłudze Azure AD. |
| microsoft.directory/passThroughAuthentication/allProperties/allTasks | Zarządzaj wszystkimi aspektami uwierzytelniania przekazywanego (PTA) w usłudze Azure AD. |
| microsoft.directory/seamlessSSO/allProperties/allTasks | Zarządzaj wszystkimi aspektami bezproblemowego logowania jednokrotnego (Logowanie jednokrotne) w usłudze Azure AD. |
| microsoft.directory/servicePrincipals/audience/update | Zaktualizuj właściwość servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Zaktualizuj właściwość servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aktualizowanie podstawowych właściwości w usługachPrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | Tworzenie servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Zaktualizuj właściwość servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Usuń servicePrincipals w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Zaktualizuj właściwość servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/synchronizationJobs/manage | Zarządzaj wszystkimi aspektami zadań synchronizacji w usłudze Azure AD. |
| microsoft.directory/servicePrincipals/synchronizationSchema/manage | Zarządzanie wszystkimi aspektami schematu synchronizacji w usłudze Azure AD. |
| microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Zarządzanie wszystkimi aspektami poświadczeń synchronizacji w usłudze Azure AD. |
| microsoft.directory/servicePrincipals/tag/update | Zaktualizuj właściwość servicePrincipals.tag w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w signInReports w usłudze Azure Active Directory. |
| microsoft.office365.messageCenter/messages/read | Odczytywanie wiadomości w witrynie microsoft.office365.messageCenter. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |


### <a name="intune-service-administrator-permissions"></a>Intune Service Administrator permissions

Może zarządzać wszystkimi aspektami produktu usługi Intune.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Aktualizowanie podstawowych właściwości kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/contacts/create | Tworzenie kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/contacts/delete | Usuwanie kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/devices/basic/update | Aktualizowanie podstawowych właściwości na urządzeniach w usłudze Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Odczytuj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.directory/devices/create microsoft.directory/devices/create microsoft.directory/devices/create | Tworzenie urządzeń w usłudze Azure Active Directory. |
| microsoft.directory/devices/delete | Usuwanie urządzeń w usłudze Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/update | Aktualizuj właściwość devices.registeredOwners w usłudze Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/update | Aktualizuj właściwość devices.registeredUsers w usłudze Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Aktualizuj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aktualizowanie podstawowych właściwości w grupach w usłudze Azure Active Directory. |
| microsoft.directory/groups/create microsoft.directory/groups/create microsoft.directory/groups/create microsoft. | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a utworzony obiekt liczy się z przydziałem 250 utworzonych obiektów twórcy. |
| microsoft.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Odczytaj właściwość groups.hiddenMembers w usłudze Azure Active Directory. |
| microsoft.directory/groups/members/update | Aktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.directory/groups/restore | Przywracanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aktualizuj właściwość groups.settings w usłudze Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/users/basic/update | Aktualizowanie podstawowych właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/manager/update | Aktualizowanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |

### <a name="kaizala-administrator-permissions"></a>Uprawnienia administratora Kaizala

Może zarządzać ustawieniami dla Microsoft Kaizala.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj centrum administracyjne usługi Office 365. |

### <a name="license-administrator-permissions"></a>Uprawnienia administratora licencji

Może zarządzać licencjami produktów dla użytkowników i grup.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/users/assignLicense | Zarządzanie licencjami dla użytkowników usługi Azure Active Directory. |
| microsoft.directory/users/usageLokalizacja/aktualizacja | Zaktualizuj właściwość users.usageLocation w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |

### <a name="lync-service-administrator-permissions"></a>Uprawnienia administratora usługi programu Lync

Może zarządzać wszystkimi aspektami produktu Skype dla firm.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.office365.usageRaporty/wszystkieEnty/odczyt    | Przeczytaj raporty użycia usługi Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |


### <a name="message-center-privacy-reader-permissions"></a>Uprawnienia czytnika prywatności centrum wiadomości

Może czytać posty Centrum wiadomości, wiadomości o prywatności danych, grupy, domeny i subskrypcje.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Odczytywanie wiadomości w witrynie microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Odczyt zabezpieczeńWiadomie w microsoft.office365.messageCenter. |

### <a name="message-center-reader-permissions"></a>Uprawnienia czytnika Centrum wiadomości
Może odczytywać wiadomości i aktualizacje dla swojej organizacji tylko w Centrum wiadomości usługi Office 365. 

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Odczytywanie wiadomości w witrynie microsoft.office365.messageCenter. |

### <a name="network-administrator-permissions"></a>Uprawnienia administratora sieci
Może zarządzać lokalizacjami sieciowymi i przeglądać szczegółowe informacje dotyczące projektowania sieci przedsiębiorstw dla aplikacji Microsoft 365 Software as a Service.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.network/performance/allProperties/read | Odczytywanie stron wydajności sieci w Centrum administracyjnym M365.  |
| microsoft.office365.network/locations/allProperties/allTasks | Odczytuj i skonfiguruj właściwości lokalizacji sieciowych dla każdej lokalizacji. |

### <a name="office-apps-administrator-permissions"></a>Uprawnienia Administratora aplikacji pakietu Office
Może zarządzać usługami w chmurze aplikacji pakietu Office, w tym zarządzaniem zasadami i ustawieniami, oraz zarządzać możliwością wybierania, odznaczania i publikowania zawartości funkcji "co nowego" na urządzeniach użytkowników końcowych.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.messageCenter/messages/read | Odczytywanie wiadomości w witrynie microsoft.office365.messageCenter. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.office365.usageRaporty/wszystkieEnty/odczyt | Przeczytaj raporty użycia usługi Office 365. |
| microsoft.office365.userKomunika/wszystkieEnty/wszystkieZadania | Widoczność i aktualizowanie widoczność wiadomości Co nowego. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |

### <a name="partner-tier1-support-permissions"></a>Uprawnienia do pomocy technicznej warstwy 1 partnera

Nie używać - nie przeznaczone do ogólnego użytku.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Aktualizowanie podstawowych właściwości kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/contacts/create | Tworzenie kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/contacts/delete | Usuwanie kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/groups/create microsoft.directory/groups/create microsoft.directory/groups/create microsoft. | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a utworzony obiekt liczy się z przydziałem 250 utworzonych obiektów twórcy. |
| microsoft.directory/groups/members/update | Aktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/users/assignLicense | Zarządzanie licencjami dla użytkowników usługi Azure Active Directory. |
| microsoft.directory/users/basic/update | Aktualizowanie podstawowych właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Unieważnij wszystkie tokeny odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/manager/update | Aktualizowanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.directory/users/password/update | Aktualizowanie haseł dla wszystkich użytkowników usługi Azure Active Directory. Więcej informacji można znaleźć w dokumentacji online. |
| microsoft.directory/users/restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="partner-tier2-support-permissions"></a>Uprawnienia do pomocy technicznej warstwy partnerów tier2

Nie używać - nie przeznaczone do ogólnego użytku.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Aktualizowanie podstawowych właściwości kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/contacts/create | Tworzenie kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/contacts/delete | Usuwanie kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/domains/allTasks | Tworzenie i usuwanie domen oraz odczytywanie i aktualizowanie standardowych właściwości w usłudze Azure Active Directory. |
| microsoft.directory/groups/create microsoft.directory/groups/create microsoft.directory/groups/create microsoft. | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/members/update | Aktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.directory/groups/restore | Przywracanie grup w usłudze Azure Active Directory. |
| microsoft.directory/organization/basic/update | Aktualizowanie podstawowych właściwości organizacji w usłudze Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/users/assignLicense | Zarządzanie licencjami dla użytkowników usługi Azure Active Directory. |
| microsoft.directory/users/basic/update | Aktualizowanie podstawowych właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Unieważnij wszystkie tokeny odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/manager/update | Aktualizowanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.directory/users/password/update | Aktualizowanie haseł dla wszystkich użytkowników usługi Azure Active Directory. Więcej informacji można znaleźć w dokumentacji online. |
| microsoft.directory/users/restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="password-administrator-permissions"></a>Uprawnienia administratora haseł

Można resetować hasła dla administratorów niebędących administratorami i administratorami haseł.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/users/password/update | Aktualizowanie haseł dla wszystkich użytkowników usługi Azure Active Directory. Więcej informacji można znaleźć w dokumentacji online. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |

### <a name="power-bi-service-administrator-permissions"></a>Uprawnienia administratora usługi Power BI

Może zarządzać wszystkimi aspektami produktu Usługi Power BI.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>
| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |


### <a name="power-platform-administrator-permissions"></a>Uprawnienia administratora platformy Power Platform

Może tworzyć i zarządzać wszystkimi aspektami Microsoft Dynamics 365, PowerApps i Microsoft Flow. 

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>
| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami dynamics 365. |
| microsoft.flow/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Microsoft Flow. |
| microsoft.powerApps/allEntities/allTasks | Zarządzaj wszystkimi aspektami aplikacji PowerApps. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="printer-administrator-permissions"></a>Uprawnienia administratora drukarki

Może zarządzać wszystkimi aspektami drukarek i złączy drukarek.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>
| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.print/allEntities/allProperties/allTasks | Tworzenie i usuwanie drukarek i łączników oraz odczytywanie i aktualizowanie wszystkich właściwości w programie Microsoft Print. |

### <a name="printer-technician-permissions"></a>Uprawnienia technika drukarki

Może rejestrować i wyrymazyować drukarki oraz aktualizować stan drukarki.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>
| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.print/connectors/allProperties/read | Przeczytaj wszystkie właściwości łączników w programie Microsoft Print. |
| microsoft.azure.print/printers/allProperties/read | Przeczytaj wszystkie właściwości drukarek w programie Microsoft Print. |
| microsoft.azure.print/printers/basic/update | Aktualizowanie podstawowych właściwości drukarek w programie Microsoft Print. |
| microsoft.azure.print/printers/register | Rejestrowanie drukarek w programie Microsoft Print. |
| microsoft.azure.print/printers/wyrejestrowy | Wyrejestruj drukarki w programie Microsoft Print. |

### <a name="privileged-authentication-administrator-permissions"></a>Uprawnienia administratora uwierzytelniania uprzywilejowanego

Dozwolone wyświetlanie, ustawianie i resetowanie informacji o metodzie uwierzytelniania dla każdego użytkownika (administratora lub osoby niebędącej administratorem).

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Unieważnij wszystkie tokeny odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Aktualizowanie właściwości silnego uwierzytelniania, takich jak informacje o poświadczeniach usługi MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.directory/users/password/update | Aktualizowanie haseł dla wszystkich użytkowników w organizacji usługi Office 365. Więcej informacji można znaleźć w dokumentacji online. |

### <a name="privileged-role-administrator-permissions"></a>Uprawnienia administratora ról uprzywilejowanych

Może zarządzać przypisaniami ról w usłudze Azure AD i wszystkimi aspektami zarządzania tożsamościami uprzywilejowanymi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w witrynie microsoft.aad.privilegedIdentityManagement. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks | Odczytuj i konfiguruj właściwość servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Odczytuj i konfiguruj właściwość servicePrincipals.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.directory/administrativeJednosty/wszystkieWłaściwości/wszystkieZadowonia | Tworzenie jednostek administracyjnych (w tym członków) i zarządzanie nimi |
| microsoft.directory/roleAssignments/allProperties/allTasks | Tworzenie przypisań ról i zarządzanie nimi. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Tworzenie definicji ról i zarządzanie nimi. |

### <a name="reports-reader-permissions"></a>Uprawnienia czytnika raportów

Może odczytywać raporty logowania i inspekcji.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w auditLogs w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.office365.usageRaporty/wszystkieEnty/odczyt | Przeczytaj raporty użycia usługi Office 365. |

### <a name="search-administrator-permissions"></a>Uprawnienia administratora wyszukiwania

Może tworzyć i zarządzać wszystkimi aspektami ustawień usługi Microsoft Search.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Odczytywanie wiadomości w witrynie microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie wszystkich właściwości w witrynie microsoft.office365.search. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |

### <a name="search-editor-permissions"></a>Uprawnienia Edytora wyszukiwania

Może tworzyć treści redakcyjne, takie jak zakładki, Q i As, lokalizacje, floorplan i zarządzać nimi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Odczytywanie wiadomości w witrynie microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Tworzenie i usuwanie zawartości oraz odczytywanie i aktualizowanie wszystkich właściwości w witrynie microsoft.office365.search. |

### <a name="security-administrator-permissions"></a>Uprawnienia administratora zabezpieczeń

Może odczytywać informacje o zabezpieczeniach i raporty oraz zarządzać konfiguracją w usłudze Azure AD i usłudze Office 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/applications/policies/update | Aktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w auditLogs w usłudze Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Odczytuj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.directory/policies/basic/update | Aktualizowanie podstawowych właściwości zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.directory/policies/owners/update | Aktualizuj właściwość policies.owners w usłudze Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Aktualizuj właściwość policies.tenantDefault w usłudze Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w signInReports w usłudze Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Przeczytaj wszystkie zasoby w pliku microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Zaktualizuj wszystkie zasoby w pliku microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Przeczytaj wszystkie zasoby w pliku microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Przeczytaj wszystkie aspekty Centrum ochrony usługi Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Zaktualizuj wszystkie zasoby w witrynie microsoft.office365.protectionCenter. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |

### <a name="security-operator-permissions"></a>Uprawnienia operatora zabezpieczeń

Tworzy zdarzenia zabezpieczeń i zarządza nimi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Odczytuj i skonfiguruj usługę Microsoft Cloud App Security. |
| microsoft.aad.identityProtection/allEntities/read | Przeczytaj wszystkie zasoby w pliku microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Przeczytaj wszystkie zasoby w pliku microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Odczytuj i skonfiguruj zaawansowaną ochronę przed zagrożeniami usługi Azure AD. |
| microsoft.intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Umożliwia odczyt i konfigurowanie Centrum zgodności & zabezpieczeń. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Odczytuj i skonfiguruj zaawansowaną ochronę przed zagrożeniami w usłudze Windows Defender. |

### <a name="security-reader-permissions"></a>Uprawnienia czytnika zabezpieczeń

Może odczytywać informacje o zabezpieczeniach i raporty w usłudze Azure AD i usłudze Office 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w auditLogs w usłudze Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Odczytuj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/read | Odczyt właściwości policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Odczytuj wszystkie właściwości (w tym właściwości uprzywilejowane) w signInReports w usłudze Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Przeczytaj wszystkie zasoby w pliku microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Przeczytaj wszystkie zasoby w pliku microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Przeczytaj wszystkie aspekty Centrum ochrony usługi Office 365. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |

### <a name="service-support-administrator-permissions"></a>Uprawnienia administratora pomocy technicznej

Może odczytywać informacje o kondycji usługi i zarządzać biletami pomocy technicznej.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

### <a name="sharepoint-service-administrator-permissions"></a>Uprawnienia administratora usługi programu SharePoint

Może zarządzać wszystkimi aspektami usługi SharePoint.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aktualizuj właściwość groups.unified w usłudze Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aktualizowanie podstawowych właściwości grup usługi Office 365. |
| microsoft.directory/groups/unified/create | Tworzenie grup usługi Office 365. |
| microsoft.directory/groups/unified/delete | Usuwanie grup usługi Office 365. |
| microsoft.directory/groups/unified/members/update | Aktualizowanie członkostwa w grupach usługi Office 365. |
| microsoft.directory/groups/unified/owners/update | Aktualizowanie własności grup usługi Office 365. |
| microsoft.office365.network/performance/allProperties/read | Odczytywanie stron wydajności sieci w Centrum administracyjnym M365. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w programie microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.office365.usageRaporty/wszystkieEnty/odczyt    | Przeczytaj raporty użycia usługi Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |

### <a name="teams-communications-administrator-permissions"></a>Uprawnienia administratora komunikacji zespołów

Może zarządzać funkcjami połączeń i spotkań w ramach usługi Microsoft Teams.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.office365.usageRaporty/wszystkieEnty/odczyt | Przeczytaj raporty użycia usługi Office 365. |

### <a name="teams-communications-support-engineer-permissions"></a>Uprawnienia inżyniera pomocy technicznej teams communications

Może rozwiązywać problemy z komunikacją w ramach usługi Teams za pomocą zaawansowanych narzędzi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |

### <a name="teams-communications-support-specialist-permissions"></a>Uprawnienia specjalisty do obsługi komunikacji zespołów

Może rozwiązywać problemy z komunikacją w ramach zespołów przy użyciu podstawowych narzędzi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |

### <a name="teams-service-administrator-permissions"></a>Uprawnienia administratora usługi teams

Może zarządzać usługą Microsoft Teams.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.directory/groups/hiddenMembers/read | Odczytaj właściwość groups.hiddenMembers w usłudze Azure Active Directory. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aktualizuj właściwość groups.unified w usłudze Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aktualizowanie podstawowych właściwości grup usługi Office 365. |
| microsoft.directory/groups/unified/create | Tworzenie grup usługi Office 365. |
| microsoft.directory/groups/unified/delete | Usuwanie grup usługi Office 365. |
| microsoft.directory/groups/unified/members/update | Aktualizowanie członkostwa w grupach usługi Office 365. |
| microsoft.directory/groups/unified/owners/update | Aktualizowanie własności grup usługi Office 365. |
| microsoft.office365.network/performance/allProperties/read | Odczytywanie stron wydajności sieci w Centrum administracyjnym M365. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |
| microsoft.office365.usageRaporty/wszystkieEnty/odczyt | Przeczytaj raporty użycia usługi Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |

### <a name="user-administrator-permissions"></a>Uprawnienia administratora użytkownika
Może zarządzać wszystkimi aspektami użytkowników i grup, w tym resetowaniem haseł dla ograniczonych administratorów.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | Tworzenie appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Usuń appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Aktualizowanie appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/contacts/basic/update | Aktualizowanie podstawowych właściwości kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/contacts/create | Tworzenie kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/contacts/delete | Usuwanie kontaktów w usłudze Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Aktualizuj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aktualizowanie podstawowych właściwości w grupach w usłudze Azure Active Directory. |
| microsoft.directory/groups/create microsoft.directory/groups/create microsoft.directory/groups/create microsoft. | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a utworzony obiekt liczy się z przydziałem 250 utworzonych obiektów twórcy. |
| microsoft.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Odczytaj właściwość groups.hiddenMembers w usłudze Azure Active Directory. |
| microsoft.directory/groups/members/update | Aktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.directory/groups/restore | Przywracanie grup w usłudze Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aktualizuj właściwość groups.settings w usłudze Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.directory/users/assignLicense | Zarządzanie licencjami dla użytkowników usługi Azure Active Directory. |
| microsoft.directory/users/basic/update | Aktualizowanie podstawowych właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/create microsoft.directory/users/create | Tworzenie użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Unieważnij wszystkie tokeny odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/manager/update | Aktualizowanie właściwości users.manager w usłudze Azure Active Directory. |
| microsoft.directory/users/password/update | Aktualizowanie haseł dla wszystkich użytkowników usługi Azure Active Directory. Więcej informacji można znaleźć w dokumentacji online. |
| microsoft.directory/users/restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Odczytuj i skonfiguruj kondycję usługi Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej platformy Azure i zarządzanie nimi. |
| microsoft.office365.webPortal/allEntities/basic/read | Odczytuj podstawowe właściwości wszystkich zasobów w witrynie microsoft.office365.webPortal. |
| microsoft.office365.serviceOdwzdrowie/wszystkieEntności/wszystkieZadłużenia | Odczytuj i skonfiguruj kondycję usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie biletów pomocy technicznej usługi Office 365 i zarządzanie nimi. |

## <a name="role-template-ids"></a>Identyfikatory szablonów ról

Identyfikatory szablonów ról są używane głównie przez użytkowników interfejsu API programu Microsoft Graph lub programu PowerShell.

Nazwa wyświetlania wykresu | Nazwa wyświetlana portalu platformy Azure | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrator aplikacji | Administrator aplikacji | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Deweloper aplikacji | Deweloper aplikacji | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrator uwierzytelniania | Administrator uwierzytelniania | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps Administrator | Administrator programu DevOps platformy Azure | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure Information Protection Administrator | Administrator usługi Azure Information Protection | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administrator przepływu użytkownika B2C | Administrator przepływu użytkownika B2C | 6e591065-9bad-43ed-90f3-e9424366d2f0
Administrator atrybutów przepływu użytkownika B2C | Administrator atrybutów przepływu użytkownika B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Administrator zestawu kluczy IEF B2C | Administrator zestawu kluczy IEF B2C | aaf43236-0c0d-4d5f-883a-6955382ac081
Administrator zasad IEF B2C | Administrator zasad IEF B2C | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrator rozliczeń | Administrator rozliczeń | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrator aplikacji w chmurze | Administrator aplikacji w chmurze | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrator urządzeń w chmurze | Administrator urządzeń w chmurze | 7698a772-787b-4ac8-901f-60d6b08affd2
Administrator firmy | Administrator globalny | 62e90394-69f5-4237-9190-012177145e10
Administrator zgodności | Administrator zgodności | 17315797-102d-40b4-93e0-432062caca18
Administrator danych zgodności | Administrator danych zgodności | e6d1a23a-da11-4be4-9570-befc86d067a7
Administrator dostępu warunkowego | Administrator dostępu warunkowego | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administrator usługi CRM | Administrator dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Osoba zatwierdzająca dostęp do skrytki klienta | Osoba zatwierdzająca dostęp do skrytki klienta | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administrator analityki komputerowej | Administrator analityki komputerowej | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Administratorzy urządzeń | Administratorzy urządzeń | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Dołącz do urządzenia | Sprzężenie urządzenia | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Menedżery urządzeń | Menedżery urządzeń | 2b499bcd-da44-4968-8aec-78e1674fa64d
Użytkownicy urządzeń | Użytkownicy urządzeń | d405c6df-0af8-4e3b-95e4-4d06e542189e
Czytniki katalogów | Czytniki katalogów | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Konta synchronizacji katalogów | Konta synchronizacji katalogów | d29b2b05-8046-44ba-8758-1e26182fcf32
Autorzy katalogów | Autorzy katalogów | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrator usługi programu Exchange | Administrator programu Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrator zewnętrznego dostawcy tożsamości | Administrator zewnętrznego dostawcy tożsamości | be2f45a1-457d-42af-a067-6ec1fa63bc45
Czytelnik globalny | Czytnik globalny | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Administrator grup | Administrator grup | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Zaproszony gość | Zaproszony gość | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrator działu pomocy technicznej | Administrator działu pomocy technicznej | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrator tożsamości hybrydowej | Administrator tożsamości hybrydowej | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Administrator usługi Intune | Administrator usługi Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala Administrator | Administrator Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Administrator licencji | Administrator licencji | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administrator usługi programu Lync | Administrator programu Skype dla firm | 75941009-915a-4869-abe7-691bff18279e
Czytnik prywatności centrum wiadomości | Czytnik prywatności centrum wiadomości | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Czytnik centrum wiadomości | Czytnik centrum wiadomości | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Administrator sieci | Administrator sieci | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Administrator aplikacji pakietu Office | Administrator aplikacji pakietu Office | 2b745bdf-0803-4d80-aa65-822c4493daac
Pomoc techniczna poziomu 1 partnera | Pomoc techniczna poziomu 1 partnera | 4ba39ca4-527c-499a-b93d-d9b492c50246
Obsługa partnerów Tier2 | Pomoc techniczna poziomu 2 dla partnerów | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrator haseł | Administrator haseł | 966707d0-3269-4727-9be2-8c3a10f19b9d
Administrator usługi Power BI | Administrator usługi Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Administrator platformy zasilania | Administrator platformy zasilania | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Administrator drukarki | Administrator drukarki | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
Technik drukarki | Technik drukarek | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
Administrator uwierzytelniania uprzywilejowanego | Administrator uwierzytelniania uprzywilejowanego | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrator ról uprzywilejowanych | Administrator ról uprzywilejowanych | e8611ab8-c189-46e8-94e1-60213ab1f814
Czytnik raportów | Czytnik raportów | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrator wyszukiwania | Administrator wyszukiwania | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Edytor wyszukiwania | Edytor wyszukiwania | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Administrator zabezpieczeń | Administrator zabezpieczeń | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operator zabezpieczeń | Operator zabezpieczeń | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Czytelnik zabezpieczeń | Czytelnik zabezpieczeń | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrator pomocy technicznej | Administrator pomocy technicznej usługi | f023fd81-a637-4b56-95fd-791ac0226033
Administrator usługi programu SharePoint | Administrator programu SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Administrator komunikacji zespołów | Administrator komunikacji zespołów | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Inżynier wsparcia komunikacji zespołowej | Inżynier wsparcia komunikacji zespołowej | f70938a0-fc10-4177-9e90-2178f8765737
Specjalista ds. Wsparcia Komunikacji Zespołowej | Specjalista ds. Wsparcia Komunikacji Zespołowej | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrator usługi teams | Administrator usługi teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Użytkownik | Użytkownik | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrator konta użytkownika | Administrator użytkownika | fe930be7-5e62-47db-91af-98c3a49a38b1
Dołączanie do urządzenia w miejscu pracy | Dołączanie do urządzenia w miejscu pracy | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Przestarzałe role

Następujące role nie powinny być używane. Zostały one przestarzałe i zostaną usunięte z usługi Azure AD w przyszłości.

* Administrator licencji AdHoc
* Dołącz do urządzenia
* Menedżery urządzeń
* Użytkownicy urządzeń
* Twórca zweryfikowanego użytkownika pocztą e-mail
* Administrator skrzynki pocztowej
* Dołączanie do urządzenia w miejscu pracy

## <a name="roles-not-shown-in-the-portal"></a>Role nie są wyświetlane w portalu

Nie każda rola zwrócona przez interfejs API programu PowerShell lub MS Graph jest widoczna w witrynie Azure portal. W poniższej tabeli przedstawiono te różnice.

Nazwa interfejsu API | Nazwa portalu platformy Azure | Uwagi
-------- | ------------------- | -------------
Administrator firmy | Administrator globalny | [Zmieniono nazwę dla lepszej jasności](directory-assign-admin-roles.md#role-template-ids)
Administrator usługi CRM | Administrator dynamics 365 | [Odzwierciedla bieżącą markę produktu](directory-assign-admin-roles.md#role-template-ids)
Dołącz do urządzenia | Przestarzałe | [Przestarzała dokumentacja ról](directory-assign-admin-roles.md#deprecated-roles)
Menedżery urządzeń | Przestarzałe | [Przestarzała dokumentacja ról](directory-assign-admin-roles.md#deprecated-roles)
Użytkownicy urządzeń | Przestarzałe | [Przestarzała dokumentacja ról](directory-assign-admin-roles.md#deprecated-roles)
Konta synchronizacji katalogów | Nie pokazano, ponieważ nie powinien być używany | [Dokumentacja kont synchronizacji katalogów](directory-assign-admin-roles.md#directory-synchronization-accounts)
Autorzy katalogów | Nie pokazano, ponieważ nie powinien być używany | [Dokumentacja modułów zapisu katalogów](directory-assign-admin-roles.md#directory-writers)
Użytkownik-gość | Nie pokazano, ponieważ nie można go używać  | Nie dotyczy
Administrator usługi programu Lync | Administrator programu Skype dla firm | [Odzwierciedla bieżącą markę produktu](directory-assign-admin-roles.md#role-template-ids)
Wsparcie dla partnerów tier 1 | Nie pokazano, ponieważ nie powinien być używany | [Dokumentacja pomocy technicznej tier1 partnera](directory-assign-admin-roles.md#partner-tier1-support)
Wsparcie dla partnerów tier 2 | Nie pokazano, ponieważ nie powinien być używany | [Dokumentacja pomocy technicznej warstwy partnerów Tier2](directory-assign-admin-roles.md#partner-tier2-support)
Administrator drukarki | Work in progress | Work in progress
Technik drukarki | Work in progress | Work in progress
Ograniczony użytkownik-gość | Nie pokazano, ponieważ nie można go używać | Nie dotyczy
Użytkownik | Nie pokazano, ponieważ nie można go używać | Nie dotyczy
Dołączanie do urządzenia w miejscu pracy | Przestarzałe | [Przestarzała dokumentacja ról](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przypisywaniu użytkownika jako administratora subskrypcji platformy Azure, zobacz [Zarządzanie dostępem przy użyciu ról platformy Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)
* Aby dowiedzieć się więcej o tym, jak dostęp do zasobów jest kontrolowany na platformie Microsoft Azure, zobacz [Opis różnych ról](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
