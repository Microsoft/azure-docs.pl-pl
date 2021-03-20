---
title: Przewodnik dotyczący operacji zarządzania tożsamościami i dostępem Azure Active Directory
description: W tym przewodniku odwołuje się opis operacji sprawdzania i działań, które należy wykonać w celu zabezpieczenia operacji zarządzania tożsamościami i dostępem
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 66bce573be5a31641bdff809b8e9a79b617a703a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371005"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Przewodnik dotyczący operacji zarządzania tożsamościami i dostępem Azure Active Directory

W tej części [przewodnika dotyczącego odwołań usługi Azure AD](active-directory-ops-guide-intro.md) opisano testy i akcje, które należy wziąć pod uwagę w celu zabezpieczenia i zarządzania cyklem życia tożsamości i ich przypisań.

> [!NOTE]
> Te zalecenia są aktualne w dniu opublikowania, ale mogą ulec zmianie w czasie. Organizacje powinny stale sprawdzać swoją tożsamość jako produkty i usługi firmy Microsoft, które są roznoszone wraz z upływem czasu.

## <a name="key-operational-processes"></a>Najważniejsze procesy operacyjne

### <a name="assign-owners-to-key-tasks"></a>Przypisywanie właścicieli do kluczowych zadań

Zarządzanie Azure Active Directory wymaga ciągłego wykonywania kluczowych zadań operacyjnych i procesów, które mogą nie być częścią projektu wdrożenia. Nadal ważne jest, aby skonfigurować te zadania w celu utrzymania środowiska. Najważniejsze zadania i ich zalecani właściciele obejmują:

| Zadanie | Właściciel |
| :- | :- |
| Definiowanie procesu tworzenia subskrypcji platformy Azure | Różni się w zależności od organizacji |
| Zdecyduj, kto otrzymuje Enterprise Mobility + Security licencje | Zespół operacji IAM |
| Zdecyduj, kto otrzymuje Microsoft 365 licencje | Zespół produktywności |
| Zdecyduj, kto otrzymuje inne licencje, na przykład Dynamics, Visual Studio Codespaces | Właściciel aplikacji |
| Przypisywanie licencji | Zespół operacji IAM |
| Rozwiązywanie problemów i korygowanie błędów przypisywania licencji | Zespół operacji IAM |
| Inicjowanie obsługi tożsamości dla aplikacji w usłudze Azure AD | Zespół operacji IAM |

Podczas przeglądania listy może być konieczne przypisanie właściciela do zadań, w których brakuje właściciela, lub dostosować własność do zadań z właścicielami, które nie są wyrównane do powyższych zaleceń.

#### <a name="assigning-owners-recommended-reading"></a>Przypisywanie zalecanych właścicieli

- [Przypisywanie ról administratorów w usłudze Azure Active Directory](../roles/permissions-reference.md)
- [Nadzór na platformie Azure](../../governance/index.yml)

## <a name="on-premises-identity-synchronization"></a>Lokalna synchronizacja tożsamości

### <a name="identify-and-resolve-synchronization-issues"></a>Identyfikowanie i rozwiązywanie problemów z synchronizacją

Firma Microsoft zaleca, aby mieć dobrą podstawę i zrozumienie problemów w środowisku lokalnym, co może spowodować problemy z synchronizacją w chmurze. Ze względu na to, że zautomatyzowane narzędzia, takie jak [IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) i [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#why-use-azure-ad-connect-health) mogą generować duże liczby fałszywych wartości dodatnich, zalecamy zidentyfikowanie błędów synchronizacji, które zostały pozostawione jako nieadresowane przez ponad 100 dni przez oczyszczenie tych obiektów w błąd. Długotrwałe nierozwiązane błędy synchronizacji mogą generować zdarzenia pomocy technicznej. [Rozwiązywanie problemów z błędami podczas synchronizacji](../hybrid/tshoot-connect-sync-errors.md) zawiera omówienie różnych typów błędów synchronizacji, niektóre możliwe scenariusze, które powodują te błędy, oraz potencjalne sposoby rozwiązania błędów.

### <a name="azure-ad-connect-sync-configuration"></a>Konfiguracja synchronizacji Azure AD Connect

Aby włączyć wszystkie środowiska hybrydowe, stan zabezpieczeń opartych na urządzeniach i integrację z usługą Azure AD, należy zsynchronizować konta użytkowników, których pracownicy używają do logowania się do swoich pulpitów.

Jeśli użytkownik nie zsynchronizuje się z logowaniem użytkowników lasu, należy zmienić synchronizację, aby pochodzić z odpowiedniego lasu.

#### <a name="synchronization-scope-and-object-filtering"></a>Zakres synchronizacji i filtrowanie obiektów

Usunięcie znanych zasobników obiektów, które nie są wymagane do synchronizacji, ma następujące zalety operacyjne:

- Mniej źródeł błędów synchronizacji
- Szybsze cykle synchronizacji
- Mniej "elementów bezużytecznych" do przeprowadzenia w środowisku lokalnym, na przykład, zanieczyszczenie globalnej listy adresów dla kont usług lokalnych, które nie są istotne w chmurze

> [!NOTE]
> Jeśli okaże się, że importujesz wiele obiektów, które nie są eksportowane do chmury, należy filtrować według jednostki organizacyjnej lub określonych atrybutów.

Przykłady obiektów do wykluczenia:

- Konta usług, które nie są używane przez aplikacje w chmurze
- Grupy, które nie są przeznaczone do użycia w scenariuszach w chmurze, takich jak te używane do udzielania dostępu do zasobów
- Użytkownicy lub kontakty, które są tożsamościami zewnętrznymi przeznaczonymi do współpracy z usługą Azure AD B2B
- Konta komputerów, w których pracownicy nie mają dostępu do aplikacji w chmurze, na przykład serwery

> [!NOTE]
> Jeśli pojedyncza tożsamość ludzka ma wiele kont, które są obsługiwane od takich jak migracja domeny starszej, scalanie lub pozyskiwanie, należy synchronizować tylko konto używane przez użytkownika w oparciu o codzienne dane, na przykład ich użycie do logowania się na komputerze.

Najlepszym rozwiązaniem jest osiągnięcie równowagi między zmniejszeniem liczby obiektów do zsynchronizowania i złożoności reguł. Ogólnie rzecz biorąc połączenie między jednostką organizacyjną a [filtrowaniem](../hybrid/how-to-connect-sync-configure-filtering.md) kontenerów oraz prostym mapowaniem atrybutu do atrybutu cloudFiltered jest efektywną kombinacją filtrowania.

> [!IMPORTANT]
> Jeśli używasz filtrowania grup w środowisku produkcyjnym, należy przejść do innego podejścia filtrowania.

#### <a name="sync-failover-or-disaster-recovery"></a>Synchronizacja trybu failover lub odzyskiwania po awarii

Azure AD Connect odgrywa kluczową rolę w procesie aprowizacji. Jeśli serwer synchronizacji przejdzie w tryb offline z dowolnego powodu, zmiany w środowisku lokalnym nie mogą zostać zaktualizowane w chmurze i mogą powodować problemy z dostępem użytkowników. W związku z tym ważne jest zdefiniowanie strategii trybu failover, która umożliwia administratorom szybkie wznowienie synchronizacji po przejściu serwera synchronizacji do trybu offline. Takie strategie mogą przypadać na następujące kategorie:

- **Wdróż Azure AD Connect serwery w trybie przejściowym** — umożliwia administratorowi podwyższenie poziomu serwera przemieszczania do środowiska produkcyjnego za pomocą prostego przełącznika konfiguracji.
- **Korzystanie z wirtualizacji** — Jeśli program Azure AD Connect jest wdrożony na maszynie wirtualnej (VM), Administratorzy mogą wykorzystać swój stos wirtualizacji do migracji na żywo lub szybko ponownie wdrożyć maszynę wirtualną, a tym samym wznowić synchronizację.

Jeśli w organizacji nie ma żadnej strategii odzyskiwania po awarii i trybu failover w celu synchronizacji, nie należy niechętnie zezwalają wdrożyć Azure AD Connect w trybie przejściowym. Podobnie, jeśli istnieje niezgodność między konfiguracją produkcyjną i przejściową, należy zmienić Azure AD Connect tryb przejściowy, aby dopasować konfigurację produkcyjną, w tym wersje oprogramowania i konfiguracje.

![Zrzut ekranu przedstawiający konfigurację trybu przejściowego Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Najnowsze informacje

Aktualizacje firmy Microsoft są regularnie Azure AD Connect. Bądź na bieżąco, aby wykorzystać ulepszenia wydajności, poprawki błędów i nowe funkcje udostępniane przez każdą nową wersję.

Jeśli wersja Azure AD Connect ma więcej niż sześć miesięcy, należy przeprowadzić uaktualnienie do najnowszej wersji.

#### <a name="source-anchor"></a>Zakotwiczenie źródła

Użycie **MS-ds-consistencyguid** jako [kotwicy źródłowej](../hybrid/plan-connect-design-concepts.md) umożliwia łatwiejsze Migrowanie obiektów w lasach i domenach, które są wspólne w przypadku konsolidacji/oczyszczania domeny usługi Active Directory, fuzji, przejęcia i divestitures.

Jeśli obecnie używasz **objectGUID** jako kotwicy źródłowej, zalecamy przełączenie do korzystania z **MS-ds-ConsistencyGuid**.

#### <a name="custom-rules"></a>Reguły niestandardowe

Azure AD Connect reguły niestandardowe zapewniają możliwość sterowania przepływem atrybutów między obiektami lokalnymi i obiektami w chmurze. Niemniej jednak użycie lub nieużycie niestandardowych reguł może spowodować następujące zagrożenia:

- Złożoność rozwiązywania problemów
- Spadek wydajności podczas wykonywania złożonych operacji dla obiektów
- Większe prawdopodobieństwo rozbieżności konfiguracji między serwerem produkcyjnym a serwerem przejściowym
- Dodatkowe obciążenie podczas uaktualniania Azure AD Connect, jeśli reguły niestandardowe są tworzone w ramach pierwszeństwa większego niż 100 (używane przez reguły wbudowane)

Jeśli używasz zbyt złożonej reguły, należy zbadać przyczyny złożoności i znaleźć szanse dla uproszczenia. Podobnie, jeśli utworzono reguły niestandardowe z wartością pierwszeństwa ponad 100, należy poprawić reguły, aby nie były one zagrożone lub powodowały konflikt z domyślnym zestawem.

Przykłady nieużywanych reguł niestandardowych obejmują:

- **Kompensowanie danych w katalogu** — w tym przypadku zaleca się współpracę z właścicielami zespołu usługi AD i wyczyszczenie danych w katalogu jako zadania korygowania i dostosowanie procesów w celu uniknięcia ponownego wprowadzenia nieprawidłowych danych.
- **Jednorazowe korygowanie poszczególnych użytkowników** — często istnieje możliwość znalezienia reguł, które różnią się wielkością liter, zazwyczaj z powodu problemu z określonym użytkownikiem.
- **Nadskomplikowane "CloudFiltering"** — podczas zmniejszania liczby obiektów jest dobrym warunkiem, że istnieje ryzyko tworzenia i nieskomplikowanego zakresu synchronizacji przy użyciu wielu reguł synchronizacji. Jeśli istnieje złożona logika do dołączania/wykluczania obiektów wykraczających poza filtrowanie jednostek organizacyjnych, zaleca się rozpatrzenie tej logiki poza synchronizacją i etykietowanie obiektów z prostym atrybutem "cloudFiltered", który może być przepływem przy użyciu prostej reguły synchronizacji.

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD Connect dokument konfiguracji

[Azure AD Connect configurationer](https://github.com/Microsoft/AADConnectConfigDocumenter) to narzędzie, którego można użyć do wygenerowania dokumentacji Azure AD Connect instalacji, aby umożliwić lepsze zrozumienie konfiguracji synchronizacji, przyczynić się do uzyskania pewności i dowiedzieć się, co zostało zmienione w przypadku zastosowania nowej kompilacji lub konfiguracji Azure AD Connect lub dodanych lub zaktualizowanych niestandardowych reguł synchronizacji. Bieżące możliwości narzędzia obejmują:

- Dokumentacja kompletnej konfiguracji Azure AD Connect synchronizacji.
- Dokumentacja wszelkich zmian w konfiguracji dwóch Azure AD Connect serwerów synchronizacji lub zmian z danej linii bazowej konfiguracji.
- Generowanie skryptu wdrożenia programu PowerShell w celu przeprowadzenia migracji różnic lub dostosowań reguły synchronizacji z jednego serwera na inny.

## <a name="assignment-to-apps-and-resources"></a>Przypisanie do aplikacji i zasobów

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Licencjonowanie oparte na grupach dla usług w chmurze firmy Microsoft

Azure Active Directory usprawnia zarządzanie licencjami za pośrednictwem [licencjonowania opartego na grupach](./active-directory-licensing-whatis-azure-portal.md) dla usług w chmurze firmy Microsoft. W ten sposób IAM udostępnia infrastrukturę grupową i delegowane zarządzanie tymi grupami do właściwych zespołów w organizacji. Istnieje wiele sposobów konfigurowania członkostwa w grupach w usłudze Azure AD, w tym:

- **Zsynchronizowane z grup lokalnych** mogą pochodzić z katalogów lokalnych, co może być przydatne w przypadku organizacji, które ustanowiły procesy zarządzania grupami, które można rozszerzyć w celu przypisywania licencji w Microsoft 365.

- Grupy **oparte na atrybutach i dynamiczne** można tworzyć w chmurze na podstawie wyrażenia na podstawie atrybutów użytkownika, na przykład działem równym "Sales". Usługa Azure AD utrzymuje członków grupy, zachowując spójność ze zdefiniowanym wyrażeniem. Użycie tego rodzaju grupy do przypisania licencji umożliwia przypisanie licencji oparte na atrybutach, co jest dobrym założenia w przypadku organizacji, które mają wysoką jakość danych w swoich katalogach.

- **Delegowana własność** — grupy można tworzyć w chmurze i mogą być wyznaczeni właściciele. W ten sposób można przygrupować właścicieli biznesowych, na przykład zespół współpracy lub zespół analizy biznesowej, aby zdefiniować, kto powinien mieć dostęp.

Jeśli obecnie używasz procesu ręcznego do przypisywania licencji i składników użytkownikom, zalecamy wdrożenie licencjonowania opartego na grupach. Jeśli bieżący proces nie monitoruje błędów licencjonowania lub co jest przypisane, należy zdefiniować ulepszenia procesu, aby rozwiązać problemy z licencjonowaniem i monitorować przypisanie licencjonowania.

Innym aspektem zarządzania licencjami jest definicja planów usług (składników licencji), które powinny być włączone na podstawie funkcji zadań w organizacji. Przyznanie dostępu do planów usług, które nie są konieczne, może spowodować, że użytkownicy zobaczą narzędzia w portalu usługi Office, dla których nie przeszkolone lub nie będą używane. Może on obsługiwać dodatkowy wolumin działu pomocy technicznej, niepotrzebne Inicjowanie obsługi oraz zapewnić zgodność i nadzór na ryzyko, na przykład podczas aprowizacji usługi OneDrive dla firm osobom, które mogą nie mieć uprawnień do udostępniania zawartości.

Poniższe wskazówki ułatwiają Definiowanie planów usługi dla użytkowników:

- Administratorzy powinni definiować "pakiety" planów usług, które mają być oferowane użytkownikom na podstawie ich roli, na przykład międzypierścieniowy proces roboczy, a pracownik piętra.
- Utwórz grupy według klastra i Przypisz licencję za pomocą planu usługi.
- Opcjonalnie można zdefiniować atrybut do przechowywania pakietów dla użytkowników.

> [!IMPORTANT]
> Licencjonowanie oparte na grupach w usłudze Azure AD wprowadza koncepcję użytkowników w stanie błąd licencjonowania. Jeśli zauważysz, że wystąpiły błędy licencjonowania, należy natychmiast [zidentyfikować i rozwiązać](../enterprise-users/licensing-groups-resolve-problems.md) wszelkie problemy z przypisaniem licencji.

![Zrzut ekranu przedstawiający opis ekranu komputera wygenerowany automatycznie](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Zarządzanie cyklem życia

Jeśli używasz obecnie narzędzia, takiego jak [Microsoft Identity Manager](/microsoft-identity-manager/) lub systemu innej firmy, który jest oparty na infrastrukturze lokalnej, zalecamy odciążenie od istniejącego narzędzia, implementację licencjonowania opartego na grupach i zdefiniowanie zarządzania cyklem życia grupy w oparciu o [grupy](../enterprise-users/licensing-group-advanced.md#use-group-based-licensing-with-dynamic-groups). Podobnie, jeśli istniejący proces nie uwzględnia nowych pracowników ani pracowników, którzy opuszczają organizację, należy wdrożyć Licencjonowanie oparte na grupach na podstawie grup dynamicznych i zdefiniować cykl życia członkostwa w grupie. Na koniec w przypadku wdrożenia licencjonowania opartego na grupach w grupach lokalnych, które nie mają zarządzania cyklem życia, należy rozważyć użycie grup w chmurze w celu włączenia takich funkcji, jak delegowane prawa własności lub członkostwo dynamiczne oparte na atrybutach.

### <a name="assignment-of-apps-with-all-users-group"></a>Przypisywanie aplikacji z grupą "Wszyscy użytkownicy"

Właściciele zasobów mogą uznać, że grupa **Wszyscy użytkownicy** zawiera tylko **pracowników przedsiębiorstwa** , którzy w rzeczywistości mogą zawierać zarówno **pracowników przedsiębiorstwa** , jak i **Gości**. W związku z tym należy zachować szczególną ostrożność w przypadku używania grupy **Wszyscy użytkownicy** do przypisywania aplikacji i udzielania dostępu do zasobów, takich jak zawartość lub aplikacje programu SharePoint.

> [!IMPORTANT]
> Jeśli grupa **Wszyscy użytkownicy** jest włączona i użyta na potrzeby zasad dostępu warunkowego, aplikacji lub przydziału zasobów, należy [zabezpieczyć grupę](../external-identities/use-dynamic-groups.md) , jeśli nie chcesz uwzględniać użytkowników-Gości. Ponadto należy rozwiązać przydziały licencjonowania, tworząc i przypisując do grup, które zawierają tylko **pracowników przedsiębiorstwa** . Z drugiej strony, jeśli okaże się, że grupa **Wszyscy użytkownicy** jest włączona, ale nie jest używana do udzielania dostępu do zasobów, upewnij się, że wskazówki operacyjne w organizacji zacelowo używają tej grupy (obejmującej **pracowników** i **Gości** przedsiębiorstwa).

### <a name="automated-user-provisioning-to-apps"></a>Automatyczne Inicjowanie obsługi użytkowników w aplikacjach

[Automatyczne Inicjowanie obsługi użytkowników](../app-provisioning/user-provisioning.md) w aplikacjach jest najlepszym sposobem na utworzenie spójnej obsługi, anulowania aprowizacji i cyklu życia tożsamości w wielu systemach.

Jeśli obecnie udostępniasz aplikacje w trybie ad hoc lub używasz takich elementów jak pliki CSV, JIT lub lokalne rozwiązanie, które nie jest związane z zarządzaniem cyklem życia, zalecamy [wdrożenie aprowizacji aplikacji](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) z usługą Azure AD dla obsługiwanych aplikacji i zdefiniowanie spójnego wzorca dla aplikacji, które nie są jeszcze obsługiwane przez usługę Azure AD.

![Usługa Azure AD Provisioning](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect bazowy cykl synchronizacji różnicowej

Ważne jest, aby zrozumieć ilość zmian w organizacji i upewnić się, że nie trwa zbyt długo, aby mieć przewidywalny czas synchronizacji.

[Domyślna częstotliwość synchronizacji różnicowej](../hybrid/how-to-connect-sync-feature-scheduler.md) to 30 minut. Jeśli synchronizacja różnicowa trwa dłużej niż 30 minut lub występuje znaczne rozbieżność między wydajnością synchronizacji różnicowej i produkcji, należy zbadać i sprawdzić [czynniki wpływające na wydajność Azure AD Connect](../hybrid/plan-connect-performance-factors.md).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect Rozwiązywanie problemów zalecanych

- [Przygotowanie atrybutów katalogu do synchronizacji z Microsoft 365 przy użyciu narzędzia IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: Rozwiązywanie problemów z błędami podczas synchronizacji](../hybrid/tshoot-connect-sync-errors.md)

## <a name="summary"></a>Podsumowanie

Istnieje pięć aspektów bezpiecznej infrastruktury tożsamości. Ta lista ułatwi szybkie znalezienie i podjęcie niezbędnych działań w celu zabezpieczenia i zarządzania cyklem życia tożsamości oraz ich uprawnień w organizacji.

- Przypisywanie właścicieli do kluczowych zadań.
- Znajdowanie i rozwiązywanie problemów z synchronizacją.
- Zdefiniuj strategię pracy awaryjnej na potrzeby odzyskiwania po awarii.
- Usprawnij zarządzanie licencjami i przypisaniem aplikacji.
- Automatyzowanie aprowizacji użytkowników do aplikacji.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [kontrolami i akcjami zarządzania uwierzytelnianiem](active-directory-ops-guide-auth.md).