---
title: Cztery kroki do silnej wersji programu Identity Foundation — Azure AD
description: W tym temacie opisano cztery kroki, które klienci mogą wykonać w celu utworzenia silnej wersji programu Identity Foundation.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 795f5ede382e561ee810e54e1f8897c5d806e8b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94412378"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Cztery kroki do silnej wersji programu Identity Foundation z Azure Active Directory

Zarządzanie dostępem do aplikacji i danych nie może już polegać na tradycyjnych strategiach granicznych zabezpieczeń sieci, takich jak sieci obwodowe i zapory, ze względu na szybkie przenoszenie aplikacji do chmury. Teraz organizacje muszą ufać swojemu rozwiązaniu tożsamości w celu kontrolowania, kto i co ma dostęp do aplikacji i danych organizacji. Więcej organizacji umożliwia pracownikom przyłączanie własnych urządzeń do pracy i korzystanie z ich urządzeń z dowolnego miejsca, w którym mogą łączyć się z Internetem. Upewnienie się, że te urządzenia są zgodne i bezpieczne, jest ważnym zagadnieniem w rozwiązaniu tożsamości, które ma zostać wdrożone przez organizację. W dzisiejszym, cyfrowym miejscu pracy [tożsamość jest podstawową płaszczyzną kontroli](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) każdej organizacji przenoszonej do chmury.

W przypadku przyjęcia rozwiązania do obsługi tożsamości hybrydowej w usłudze Azure Active Directory (Azure AD) organizacje uzyskują dostęp do funkcji w warstwie Premium, które odblokują produktywność dzięki możliwościom automatyzacji, delegowania, samoobsługowego i logowania jednokrotnego. Umożliwia ona pracownikom dostęp do zasobów firmy z dowolnego miejsca, w którym są one potrzebne do pracy, jednocześnie umożliwiając zespołowi IT zarządzanie tym dostępem, upewniając się, że odpowiednie osoby mają właściwy dostęp do odpowiednich zasobów, aby stworzyć bezpieczną produktywność.

Na podstawie naszych informacji lista kontrolna najlepszych rozwiązań pomaga szybko wdrożyć zalecane akcje w celu utworzenia *silnej* wersji programu Identity Foundation w organizacji:

* Łatwe łączenie z aplikacjami
* Ustanów jedną tożsamość dla każdego użytkownika automatycznie
* Bezpieczne zapewnianie użytkownikom
* Operacjonalizować wgląd w dane

## <a name="step-1---connect-to-apps-easily"></a>Krok 1. łatwe łączenie z aplikacjami

Łącząc aplikacje z usługą Azure AD, możesz zwiększyć produktywność i bezpieczeństwo użytkowników końcowych, włączając Logowanie jednokrotne (SSO) i wykonując Inicjowanie obsługi użytkowników. Przez Zarządzanie aplikacjami w jednym miejscu, w usłudze Azure AD, możesz zminimalizować koszty administracyjne i uzyskać jeden punkt kontroli dla zasad zabezpieczeń i zgodności.

W tej sekcji omówiono opcje zarządzania dostępem użytkowników do aplikacji, Włączanie bezpiecznego dostępu zdalnego do aplikacji wewnętrznych oraz zalety migrowania aplikacji do usługi Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Bezproblemowo udostępniaj aplikacje użytkownikom

Usługa Azure AD umożliwia administratorom [Dodawanie aplikacji](../manage-apps/add-application-portal.md) do galerii aplikacji dla przedsiębiorstw w [Azure Portal](https://portal.azure.com/). Dodawanie aplikacji do galerii aplikacji dla przedsiębiorstw ułatwia konfigurowanie aplikacji do korzystania z usługi Azure AD jako dostawcy tożsamości. Umożliwia również zarządzanie dostępem użytkowników do aplikacji przy użyciu zasad dostępu warunkowego oraz Konfigurowanie logowania jednokrotnego (SSO) w aplikacjach, dzięki czemu użytkownicy nie muszą wielokrotnie wprowadzać haseł i są automatycznie zalogowani do aplikacji lokalnych i opartych na chmurze.

Po dodaniu aplikacji do galerii usługi Azure AD użytkownicy będą mogli wyświetlać przypisane do nich aplikacje oraz wyszukiwać i żądać innych aplikacji zgodnie z wymaganiami. Usługa Azure AD udostępnia [kilka metod](../manage-apps/end-user-experiences.md) uzyskiwania dostępu do aplikacji przez użytkowników:

* Panel dostępu/moje aplikacje
* Uruchamianie aplikacji usługi Microsoft 365
* Bezpośrednie logowanie do aplikacji federacyjnych
* Linki bezpośrednie logowania

Aby dowiedzieć się więcej o dostępie użytkowników do aplikacji, zobacz **krok 3 —** umożliwienie użytkownikom w tym artykule.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrowanie aplikacji z Active Directory Federation Services do usługi Azure AD

Migrowanie konfiguracji logowania jednokrotnego z Active Directory Federation Services (AD FS) do usługi Azure AD umożliwia korzystanie z dodatkowych funkcji zabezpieczeń, bardziej spójnego zarządzania i współpracy. Aby uzyskać optymalne wyniki, zalecamy Migrowanie aplikacji z AD FS do usługi Azure AD. Zastosowanie uwierzytelniania aplikacji i autoryzacji w usłudze Azure AD zapewnia następujące korzyści:

* Zarządzanie kosztami
* Zarządzanie ryzykiem
* Zwiększenie produktywności
* Rozwiązywanie zgodności i zarządzania

Aby dowiedzieć się więcej, zobacz temat [Migrowanie aplikacji do Azure Active Directory](https://aka.ms/migrateapps/whitepaper) oficjalny dokument.

### <a name="enable-secure-remote-access-to-apps"></a>Włączanie bezpiecznego dostępu zdalnego do aplikacji

[Usługa Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/what-is-application-proxy.md) zapewnia organizacjom proste rozwiązanie do publikowania aplikacji lokalnych w chmurze dla użytkowników zdalnych, którzy potrzebują bezpiecznego dostępu do wewnętrznych aplikacji. Po jednokrotnym zalogowaniu się do usługi Azure AD użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze i lokalnych przy użyciu zewnętrznych adresów URL lub wewnętrznego portalu aplikacji.

Usługa Azure serwer proxy aplikacji usługi Azure AD oferuje następujące korzyści:

* Rozszerzanie usługi Azure AD do zasobów lokalnych
  * Zabezpieczenia i ochrona w skali chmury
  * Funkcje, takie jak dostęp warunkowy i Multi-Factor Authentication, które można łatwo włączyć
* Brak składników w sieci obwodowej, takich jak sieci VPN i tradycyjne zwrotne rozwiązania serwera proxy
* Nie są wymagane żadne połączenia przychodzące
* Logowanie jednokrotne (SSO) między urządzeniami, zasobami i aplikacjami w chmurze i lokalnie
* Umożliwia użytkownikom końcowym pracę w dowolnym czasie i w dowolnym miejscu

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Odnajdź cień przy użyciu Microsoft Cloud App Security

W nowoczesnych przedsiębiorstwach działy IT często nie wiedzą o wszystkich aplikacjach w chmurze, które są używane przez użytkowników do wykonywania swoich zadań. Gdy Administratorzy IT są proszeni o liczbę aplikacji w chmurze, których używają pracownicy, średnio 30 lub 40. W rzeczywistości średnia jest ponad 1 000 oddzielnych aplikacji używanych przez pracowników w organizacji. 80% pracowników używa niezatwierdzonych aplikacji, które nie zostały zrecenzowane i mogą nie być zgodne z zasadami zabezpieczeń i zgodności.

[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) może pomóc w zidentyfikowaniu użytecznych aplikacji, które są popularne dla użytkowników, którzy mogą zatwierdzono i dodać do galerii aplikacji dla przedsiębiorstw, aby użytkownicy mogli korzystać z takich funkcji, jak logowanie jednokrotne i dostęp warunkowy.

<em>"**Cloud App Security** pomaga nam upewnić się, że nasze osoby prawidłowo korzystają z naszych aplikacji w chmurze i SaaS w sposób, który obsługuje podstawowe zasady zabezpieczeń, które pomagają chronić Accenture".</em> --- [John Blasi, dyrektor naczelny, bezpieczeństwo informacji, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Oprócz wykrywania cieniowania IT MCAS może również określać poziom ryzyka aplikacji, zapobiegać nieautoryzowanemu dostępowi do danych firmowych, możliwego wycieku danych i innym zagrożeniom związanym z bezpieczeństwem w aplikacjach.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Krok 2 — ustanawianie jednej tożsamości dla każdego użytkownika automatycznie

Umieszczenie lokalnych i opartych na chmurze katalogów w rozwiązaniu hybrydowej tożsamości usługi Azure AD umożliwi ponowne użycie istniejących lokalnych Active Directory inwestycji przez inicjowanie obsługi istniejących tożsamości w chmurze. Rozwiązanie synchronizuje tożsamości lokalne z usługą Azure AD, podczas gdy utrzymuje on lokalne Active Directory działające z wszelkimi istniejącymi rozwiązaniami do zarządzania jako podstawowym źródłem prawdy dla tożsamości. Rozwiązanie do obsługi tożsamości hybrydowej usługi Azure AD firmy Microsoft obejmuje możliwości lokalne i oparte na chmurze, tworząc wspólną tożsamość użytkownika na potrzeby uwierzytelniania i autoryzacji do wszystkich zasobów, niezależnie od ich lokalizacji.

Integrowanie katalogów lokalnych z usługą Azure AD zwiększa produktywność użytkowników i uniemożliwia użytkownikom korzystanie z wielu kont w aplikacjach i usługach, zapewniając wspólną tożsamość do uzyskiwania dostępu do zasobów w chmurze i lokalnych. Używanie wielu kont to ból, który jest przeznaczony dla użytkowników końcowych i podobne. Z punktu widzenia użytkowników końcowych z wieloma kontami trzeba pamiętać o wielu hasłach. Aby tego uniknąć, wielu użytkowników ponownie używa tego samego hasła dla każdego konta, które jest niewłaściwe w perspektywie zabezpieczeń. W perspektywie IT ponowne użycie często prowadzi do większej liczby operacji resetowania haseł i kosztów pomocy technicznej wraz z skargami użytkowników końcowych.

Azure AD Connect jest narzędziem używanym do synchronizowania tożsamości lokalnych z usługą Azure AD, która może być następnie używana do uzyskiwania dostępu do aplikacji w chmurze. Po otrzymaniu tożsamości w usłudze Azure AD mogą one SaaS aplikacje, takie jak Salesforce lub Concur.

W tej sekcji wymieniono zalecenia dotyczące zapewniania wysokiej dostępności, nowoczesnego uwierzytelniania w chmurze i zmniejszania lokalnego zasięgu.

> [!NOTE]
> Jeśli chcesz dowiedzieć się więcej na temat Azure AD Connect, zobacz [co to jest Azure AD Connect Sync?](./how-to-connect-sync-whatis.md)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Konfigurowanie serwera przejściowego na potrzeby Azure AD Connect i aktualizowanie go

Azure AD Connect odgrywa kluczową rolę w procesie aprowizacji. Jeśli serwer synchronizacji przejdzie w tryb offline z dowolnego powodu, zmiany w środowisku lokalnym nie będą aktualizowane w chmurze i będą powodować problemy z dostępem do użytkowników. Należy zdefiniować strategię pracy awaryjnej, która umożliwia administratorom szybkie wznowienie synchronizacji po przejściu serwera synchronizacji do trybu offline.

Aby zapewnić wysoką dostępność w przypadku, gdy podstawowy serwer Azure AD Connect przejdzie w tryb offline, zalecamy wdrożenie oddzielnego [serwera tymczasowego](./how-to-connect-sync-staging-server.md) dla Azure AD Connect. Wdrożenie serwera pozwala administratorowi na podwyższenie poziomu serwera przejściowego do środowiska produkcyjnego za pomocą prostego przełącznika konfiguracji. Serwer zapasowy skonfigurowany w trybie przejściowym umożliwia również testowanie i wdrażanie nowych zmian konfiguracji i wprowadzanie nowego serwera w przypadku likwidowania Starego.

> [!TIP]
> Azure AD Connect jest regularnie aktualizowana. Dlatego zdecydowanie zaleca się utrzymywanie aktualnego serwera przemieszczania w celu wykorzystania ulepszeń wydajności, poprawek błędów i nowych funkcji dostępnych w każdej nowej wersji.

### <a name="enable-cloud-authentication"></a>Włącz uwierzytelnianie w chmurze

Organizacje z Active Directoryami lokalnymi powinny rozciągnąć swój katalog do usługi Azure AD przy użyciu Azure AD Connect i skonfigurować odpowiednią metodę uwierzytelniania. [Wybór odpowiedniej metody uwierzytelniania](./choose-ad-authn.md) dla organizacji to pierwszy krok w trakcie przechodzenia aplikacji do chmury. Jest to składnik krytyczny, ponieważ kontroluje dostęp do wszystkich danych i zasobów w chmurze.

Najprostszą i zalecaną metodą włączenia uwierzytelniania w chmurze dla obiektów katalogu lokalnego w usłudze Azure AD jest włączenie [synchronizacji skrótów haseł](./how-to-connect-password-hash-synchronization.md) (PHS). Alternatywnie niektóre organizacje mogą rozważyć włączenie [uwierzytelniania przekazywanego](./how-to-connect-pta-quick-start.md) (PTA).

Bez względu na to, czy wybierzesz PHS, czy PTA, nie zapomnij włączyć [bezproblemowego logowania jednokrotnego](./how-to-connect-sso.md) , aby umożliwić użytkownikom uzyskiwanie dostępu do aplikacji w chmurze, a w sieci firmowej przy użyciu urządzeń z systemami Windows 7 i 8. Bez logowania jednokrotnego użytkownicy muszą pamiętać hasła specyficzne dla aplikacji i zalogować się do każdej aplikacji. Podobnie pracownicy działu IT muszą utworzyć i zaktualizować konta użytkowników dla każdej aplikacji, takiej jak Microsoft 365, Box i Salesforce. Użytkownicy muszą pamiętać swoje hasła, a także poświęcać czas na zalogowanie się do każdej aplikacji. Zapewnienie standardowego mechanizmu logowania jednokrotnego do całego przedsiębiorstwa ma kluczowe znaczenie dla najlepszego środowiska użytkownika, zmniejszenia ryzyka, możliwości zgłaszania i zarządzania.

W przypadku organizacji, które już używają AD FS lub innego dostawcy uwierzytelniania lokalnego, przeniesienie do usługi Azure AD, ponieważ dostawca tożsamości może zmniejszyć złożoność i zwiększyć dostępność. Jeśli nie masz określonych przypadków użycia dla Federacji, zalecamy Migrowanie z uwierzytelniania federacyjnego do PHS i bezproblemowego logowania jednokrotnego lub PTA i bezproblemowe logowanie jednokrotne, aby korzystać z zalet zredukowanego miejsca lokalnego oraz elastyczności chmury dzięki udoskonalonym środowiskom użytkownika. Aby uzyskać więcej informacji, zobacz [Migrowanie z usługi federacyjnej do synchronizacji skrótów haseł dla Azure Active Directory](./plan-migrate-adfs-password-hash-sync.md).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Włącz automatyczne cofanie aprowizacji kont

Włączenie zautomatyzowanej obsługi i anulowania aprowizacji aplikacji jest najlepszą strategią dla zarządzania cyklem życia tożsamości w wielu systemach. Usługa Azure AD obsługuje [zautomatyzowane, oparte na zasadach Inicjowanie obsługi i](../app-provisioning/configure-automatic-user-provisioning-portal.md) cofanie aprowizacji kont użytkowników w różnych popularnych aplikacjach SaaS, takich jak usługi ServiceNow i Salesforce, oraz innych, które implementują [Protokół Standard scim 2,0](../app-provisioning/use-scim-to-provision-users-and-groups.md). W przeciwieństwie do tradycyjnych rozwiązań aprowizacji, które wymagają niestandardowego kodu lub ręcznego przekazywania plików CSV, usługa aprowizacji jest hostowana w chmurze i oferuje wstępnie zintegrowane łączniki, które można skonfigurować i zarządzać nimi za pomocą Azure Portal. Kluczową zaletą automatycznego anulowania aprowizacji jest ułatwienie bezpieczeństwa organizacji przez natychmiastowe usunięcie tożsamości użytkowników z usługi Key SaaS, gdy opuszczają one organizację.

Aby dowiedzieć się więcej o automatycznym inicjowaniu obsługi kont użytkowników i sposobie jej działania, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="step-3---empower-your-users-securely"></a>Krok 3 — bezpieczne zapewnianie użytkownikom

W dzisiejszym, cyfrowym miejscu pracy należy zrównoważyć bezpieczeństwo i zwiększyć wydajność. Jednak użytkownicy końcowi często wypychają środki zabezpieczające, które spowalniają swoją produktywność i dostęp do aplikacji w chmurze. Aby pomóc w rozświadczeniu tego rozwiązania, usługa Azure AD udostępnia funkcje samoobsługowe, które umożliwiają użytkownikom zwiększenie produktywności, jednocześnie minimalizując koszty administracyjne.

W tej sekcji przedstawiono zalecenia dotyczące usuwania problemów z organizacji przez umożliwienie użytkownikom pozostałej czujność.

### <a name="enable-self-service-password-reset-for-all-users"></a>Włącz Resetowanie hasła Self-Service dla wszystkich użytkowników

Funkcja samoobsługowego [resetowania haseł](../authentication/tutorial-enable-sspr.md) (SSPR) na platformie Azure zapewnia administratorom IT prosty sposób, aby użytkownicy mogli resetować i odblokowywać hasła lub konta bez interwencji administratora. System obejmuje szczegółowe raporty pozwalające śledzić, kiedy użytkownicy korzystają z systemu, oraz powiadomienia ostrzegające o jego nieprawidłowym użyciu lub nadużyciach związanych z zabezpieczeniami.

Domyślnie usługa Azure AD odblokowuje konta, gdy wykonuje Resetowanie hasła. Jednak po włączeniu [integracji Azure AD Connect lokalnie](../authentication/concept-sspr-howitworks.md#on-premises-integration)można także oddzielić te dwie operacje, które umożliwią użytkownikom odblokowanie konta bez konieczności resetowania hasła.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Upewnij się, że wszyscy użytkownicy są zarejestrowani do usługi MFA i SSPR

Platforma Azure udostępnia raporty, które mogą być używane przez Ciebie i Twoją organizację w celu zapewnienia, że użytkownicy są zarejestrowani do usługi MFA i SSPR. Użytkownicy, którzy nie zostali zarejestrowani, mogą wymagać przeprowadzenie procesu.

[Raport logowania](../authentication/howto-mfa-reporting.md) usługi MFA zawiera informacje na temat użycia usługi MFA i zapewnia wgląd w sposób działania usługi MFA w organizacji. Posiadanie dostępu do działań związanych z logowaniem (oraz inspekcjami i wykrywaniem ryzyka) dla usługi Azure AD ma kluczowe znaczenie dla rozwiązywania problemów, analizy użycia i badań dowodowych.

Podobnie można użyć raportu samoobsługowego [zarządzania hasłami](../authentication/howto-sspr-reporting.md) , aby określić, kto ma (lub nie) zarejestrowany dla SSPR.

### <a name="self-service-app-management"></a>Samoobsługowe zarządzanie aplikacjami

Zanim użytkownicy będą mogli automatycznie wykrywać aplikacje z poziomu ich panelu dostępu, musisz włączyć [dostęp do aplikacji samoobsługowej](../manage-apps/access-panel-manage-self-service-access.md) do wszystkich aplikacji, które mają pozwolić użytkownikom na samodzielne odnajdowanie i żądać dostępu do programu. Samoobsługowy dostęp do aplikacji to doskonały sposób na umożliwienie użytkownikom samodzielnego odnajdywania aplikacji i opcjonalnie Zezwalanie grupie biznesowej na zatwierdzanie dostępu do tych aplikacji. Możesz zezwolić grupie biznesowej na zarządzanie poświadczeniami przypisanymi do tych użytkowników, aby [hasła Single-Sign w aplikacjach](../manage-apps/troubleshoot-password-based-sso.md#automatically-capture-sign-in-fields-for-an-app) bezpośrednio z poziomu panelu dostępu.

### <a name="self-service-group-management"></a>Samoobsługowe zarządzanie grupami

Przypisywanie użytkowników do aplikacji jest najlepszym mapowaniem w przypadku korzystania z grup, ponieważ umożliwiają one doskonałą elastyczność i możliwość zarządzania w odpowiedniej skali:

* Oparte na atrybutach z przynależnością do grupy dynamicznej
* Delegowanie do właścicieli aplikacji

Usługa Azure AD umożliwia zarządzanie dostępem do zasobów przy użyciu grup zabezpieczeń i grup Microsoft 365. Tymi grupami można zarządzać za pomocą właściciela grupy, który może zatwierdzać lub odrzucać żądania członkostwa oraz delegować kontrolę członkostwa w grupie. Ta funkcja jest określana jako [Samoobsługowe zarządzanie grupami](../enterprise-users/groups-self-service-management.md), dzięki czemu właściciele grup nie mają przypisanej roli administracyjnej do tworzenia grup i zarządzania nimi bez konieczności polegania na administratorach do obsługi ich żądań.

## <a name="step-4---operationalize-your-insights"></a>Krok 4 — operacjonalizować się ze szczegółowymi informacjami

Inspekcja i rejestrowanie zdarzeń związanych z zabezpieczeniami oraz powiązanych alertów to podstawowe składniki wydajnej strategii, aby zapewnić, że użytkownicy będą pracować wydajnie i Twoja organizacja jest bezpieczna. Dzienniki zabezpieczeń i raporty mogą pomóc w odpowiedzi na pytania, takie jak:

* Czy korzystasz z tego, co płacisz?
* Czy w mojej dzierżawie występuje podejrzenie lub złośliwe działanie?
* Kto miał wpływ na zdarzenie związane z bezpieczeństwem?

Dzienniki zabezpieczeń i raporty zapewniają elektroniczny rejestr podejrzanych działań i pomagają wykryć wzorce, które mogą wskazywać na próbę lub pomyślne przechodzenie do sieci, a także ataki wewnętrzne. Inspekcji można używać do monitorowania aktywności użytkowników, dokumentowania zgodności z przepisami, analizy śledczej i innych. Alerty udostępniają powiadomienia o zdarzeniach zabezpieczeń.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Przypisywanie ról administratora najniższych uprawnień dla operacji

Ponieważ myślisz o podejściu do operacji, istnieje kilka poziomów administrowania, które należy wziąć pod uwagę. Pierwszy poziom polega na obciążeniu administrowania administratorami globalnymi. Zawsze, gdy jest używana rola administratora globalnego, może być odpowiednia w przypadku mniejszych firm. Jednak w przypadku większych organizacji z personelem działu pomocy technicznej i administratorami odpowiedzialnymi za określone zadania, przypisywanie roli administratora globalnego może stanowić zagrożenie bezpieczeństwa, ponieważ zapewnia to osobom fizycznym możliwość zarządzania zadaniami, które znajdują się powyżej, co powinno być możliwe do wykonania.

W takim przypadku należy wziąć pod uwagę następny poziom administrowania. Korzystając z usługi Azure AD, można wyznaczyć użytkowników końcowych jako "ograniczonych administratorów", którzy mogą zarządzać zadaniami w rolach z niższymi uprawnieniami. Na przykład możesz przypisać personelowi pomocy technicznej rolę [czytnika zabezpieczeń](../roles/permissions-reference.md#security-reader) , aby zapewnić im możliwość zarządzania funkcjami związanymi z zabezpieczeniami z dostępem tylko do odczytu. Lub być może warto przypisać rolę [administrator uwierzytelniania](../roles/permissions-reference.md#authentication-administrator) do osób indywidualnych, aby umożliwić im Resetowanie poświadczeń bez hasła lub odczytywanie i Konfigurowanie Azure Service Health.

Aby dowiedzieć się więcej, zobacz [uprawnienia roli administrator w Azure Active Directory](../roles/permissions-reference.md).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitoruj składniki hybrydowe (Azure AD Connect Sync AD FS) przy użyciu Azure AD Connect Health

Azure AD Connect i AD FS są podstawowymi składnikami, które mogą potencjalnie przerwać zarządzanie cyklem życia oraz uwierzytelnianie i ostatecznie prowadzić do awarii. W związku z tym należy wdrożyć Azure AD Connect Health na potrzeby monitorowania i raportowania tych składników.

Aby dowiedzieć się więcej, przejdź do pozycji Przeczytaj [Monitor AD FS przy użyciu Azure AD Connect Health](./how-to-connect-health-adfs.md).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Zbieranie dzienników danych na potrzeby analizy przy użyciu Azure Monitor

[Azure monitor](../../azure-monitor/overview.md) to ujednolicony Portal monitorowania dla wszystkich dzienników usługi Azure AD, który zapewnia szczegółowe informacje, zaawansowaną analizę i inteligentne Uczenie maszynowe. Dzięki Azure Monitor można korzystać z metryk i dzienników w portalu oraz za pośrednictwem interfejsów API, aby zwiększyć widoczność stanu i wydajności zasobów. Umożliwia to pojedyncze okienko środowiska w portalu przy jednoczesnym umożliwieniu szerokiej integracji produktów za pośrednictwem interfejsów API i opcji eksportu danych, które obsługują tradycyjne systemy SIEM innych firm. Azure Monitor zapewnia także możliwość konfigurowania reguł alertów w celu uzyskania powiadomień lub podjęcia zautomatyzowanych akcji dotyczących problemów wpływających na zasoby.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Utwórz niestandardowe pulpity nawigacyjne dla lidera i swój dzień na dzień

Organizacje, które nie mają rozwiązania SIEM, mogą pobrać [pakiet zawartości Power BI](../reports-monitoring/howto-use-azure-monitor-workbooks.md) dla usługi Azure AD. Pakiet zawartości Power BI zawiera wstępnie skompilowane raporty, które ułatwiają zrozumienie sposobu, w jaki użytkownicy przyjmują i korzystają z funkcji usługi Azure AD, co pozwala uzyskiwać wgląd we wszystkie działania w katalogu. Możesz również utworzyć własny [niestandardowy pulpit nawigacyjny](/power-bi/service-dashboards) i udostępnić go zespołowi kierownictwa, aby zgłosić codzienne działania. Pulpity nawigacyjne to doskonały sposób na monitorowanie firmy i szybkie wyświetlanie wszystkich najważniejszych metryk. Wizualizacje na pulpicie nawigacyjnym mogą pochodzić z jednego bazowego zestawu danych lub raportu albo wielu zestawów danych lub raportów. Pulpit nawigacyjny łączy dane lokalne i przechowywane w chmurze, zapewniają skonsolidowany widok bez względu na to, gdzie znajdują się dane.

![Niestandardowy pulpit nawigacyjny Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Zrozumienie sterowników wywołań pomocy technicznej

W przypadku zaimplementowania hybrydowego rozwiązania do obsługi tożsamości, które zostało opisane w tym artykule, należy ostatecznie zauważyć zmniejszenie liczby zgłoszeń do pomocy technicznej. Typowe problemy, takie jak zapomniane hasła i blokady kont, są korygowane przez zaimplementowanie funkcji samoobsługowego resetowania haseł przez platformę Azure, a włączenie samoobsługowego dostępu do aplikacji pozwala użytkownikom na samodzielne wykrywanie i zażądać dostępu do aplikacji bez konieczności korzystania z działu IT.

Jeśli nie obserwujesz zmniejszenia liczby zgłoszeń do pomocy technicznej, Zalecamy przeanalizowanie sterowników wywołań pomocy technicznej w celu potwierdzenia, czy SSPR lub samoobsługowy dostęp do aplikacji został prawidłowo skonfigurowany lub czy istnieją inne nowe problemy, które można systematycznie rozwiązać.

*"W naszej podróży cyfrowej musimy mieć niezawodnego dostawcę zarządzania tożsamościami i dostępem, aby ułatwić bezproblemowe i bezpieczne integrację między Stanami USA, partnerami i dostawcami usług w chmurze w skutecznym ekosystemie; Usługa Azure AD była najlepszą opcją oferującą nam potrzeby i widoczność, która umożliwiła nam wykrywanie zagrożeń i reagowanie na nie.* --- [Yazan Almasri, dyrektor ds. zabezpieczeń informacji globalnych, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitoruj użycie aplikacji, aby uzyskać wgląd w dane

Oprócz odnajdywania w tle, monitorowania użycia aplikacji w całej organizacji przy użyciu [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) może pomóc organizacji podczas przenoszenia, aby w pełni wykorzystać możliwości aplikacji w chmurze. Ułatwia ona kontrolę nad zasobami dzięki ulepszonemu wglądowi w działanie i zwiększa ochronę kluczowych danych w aplikacjach w chmurze. Monitorowanie użycia aplikacji w organizacji przy użyciu usługi MCAS może pomóc w udzieleniu odpowiedzi na następujące pytania:

* Jakie aplikacje niezaakceptowane oficjalnie są używane do przechowywania danych?
* Gdzie i kiedy dane poufne są przechowywane w chmurze?
* Kto uzyskuje dostęp do poufnych danych w chmurze?

*"Dzięki Cloud App Security możemy szybko wykasować anomalie i podejmować działania".* --- [Eric LePenske, kierownik ds. ochrony informacji, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Podsumowanie

Istnieje wiele aspektów implementowania hybrydowego rozwiązania do obsługi tożsamości, ale ta lista kontrolna z czterema krokami pomoże Ci w szybkim przeniesieniu infrastruktury tożsamości, która pozwoli użytkownikom zwiększyć produktywność i bezpieczeństwo.

* Łatwe łączenie z aplikacjami
* Ustanów jedną tożsamość dla każdego użytkownika automatycznie
* Bezpieczne zapewnianie użytkownikom
* Operacjonalizować wgląd w dane

Mamy nadzieję, że ten dokument jest przydatny do ustanowienia silnej tożsamości dla organizacji.

## <a name="identity-checklist"></a>Lista kontrolna tożsamości

Zaleca się wydrukowanie poniższej listy kontrolnej w celu uzyskania odniesienia podczas podróży do bardziej stałego programu Identity Foundation w organizacji.

### <a name="today"></a>Today

|Gotowe?|Element|
|:-|:-|
||Samoobsługowe resetowanie hasła (SSPR) dla grupy|
||Monitoruj składniki hybrydowe za pomocą Azure AD Connect Health|
||Przypisywanie ról administratora najniższych uprawnień dla operacji|
||Odnajdź cień przy użyciu Microsoft Cloud App Security|
||Zbieranie dzienników danych do analizy za pomocą Azure Monitor|

### <a name="next-two-weeks"></a>Następne dwa tygodnie

|Gotowe?|Element|
|:-|:-|
||Udostępnianie aplikacji użytkownikom|
||Pilotażowa obsługa usługi Azure AD dla wybranej aplikacji SaaS|
||Konfigurowanie serwera przejściowego na potrzeby Azure AD Connect i aktualizowanie go|
||Rozpocznij Migrowanie aplikacji z usług AD FS do usługi Azure AD|
||Utwórz niestandardowe pulpity nawigacyjne dla lidera i swój dzień na dzień|

### <a name="next-month"></a>Następny miesiąc

|Gotowe?|Element|
|:-|:-|
||Monitoruj użycie aplikacji, aby uzyskać wgląd w dane|
||Pilotażowy bezpieczny dostęp zdalny do aplikacji|
||Upewnij się, że wszyscy użytkownicy są zarejestrowani do usługi MFA i SSPR|
||Włącz uwierzytelnianie w chmurze|

### <a name="next-three-months"></a>Następne trzy miesiące

|Gotowe?|Element|
|:-|:-|
||Włączanie funkcji samoobsługowego zarządzania aplikacjami|
||Włącz Samoobsługowe zarządzanie grupami|
||Monitoruj użycie aplikacji, aby uzyskać wgląd w dane|
||Zrozumienie sterowników wywołań pomocy technicznej|

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zwiększyć bezpieczeństwo stan przy użyciu możliwości Azure Active Directory i z 5-etapową listą kontrolną [do zabezpieczenia infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md).

Dowiedz się, jak funkcje tożsamości w usłudze Azure AD mogą pomóc przyspieszyć przejście do zarządzania przez chmurę, dostarczając rozwiązania i możliwości, które umożliwiają organizacjom szybkie wdrażanie i przenoszenie większej ilości zarządzania tożsamościami z tradycyjnych systemów lokalnych do usługi Azure AD — w [jaki sposób usługa Azure AD zapewnia zarządzanie zarządzaną chmurą dla obciążeń lokalnych](./cloud-governed-management-for-on-premises.md).