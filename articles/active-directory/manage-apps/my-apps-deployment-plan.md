---
title: Planowanie Azure Active Directory Moje aplikacje konfiguracji
description: Przewodnik planowania dotyczący efektywnego Moje aplikacje w organizacji.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: baselden
ms.openlocfilehash: 777daecc119a158f11d865489e4eb497c3bc7899
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376600"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Planowanie Azure Active Directory Moje aplikacje konfiguracji

> [!NOTE]
> Ten artykuł jest przeznaczony dla specjalistów IT, którzy muszą zaplanować konfigurację portalu Moje aplikacje organizacji. 
>
> **Aby uzyskać dokumentację użytkownika końcowego, zobacz [Logowanie i uruchamianie aplikacji z Moje aplikacje portal.](../user-help/my-apps-portal-end-user-access.md)**

Azure Active Directory (Azure AD) Moje aplikacje to internetowy portal do uruchamiania aplikacji i zarządzania nimi. Strona Moje aplikacje zapewnia użytkownikom jedno miejsce do rozpoczęcia pracy i znalezienia wszystkich aplikacji, do których mają dostęp. Użytkownicy mają dostęp Moje aplikacje na stronie [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Dlaczego warto skonfigurować Moje aplikacje

Portal Moje aplikacje jest domyślnie dostępny dla użytkowników i nie można go wyłączyć. Ważne jest, aby skonfigurować je tak, aby były jak najlepsze, a portal nadal przydatny. 

Każda aplikacja na liście Azure Active Directory aplikacji dla przedsiębiorstw jest wyświetlana, gdy zostaną spełnione oba następujące warunki:

* Właściwość widoczności aplikacji jest ustawiona na wartość true. 

* Aplikacja jest przypisana do dowolnego użytkownika lub grupy. Jest on wyświetlany dla przypisanych użytkowników.

Skonfigurowanie portalu gwarantuje, że odpowiednie osoby mogą łatwo znaleźć odpowiednie aplikacje.

 
### <a name="how-is-the-my-apps-portal-used"></a>Jak jest używany Moje aplikacje portal?

Użytkownicy mają dostęp do Moje aplikacje portal, aby:

* Odnajdywanie i uzyskiwanie dostępu do wszystkich aplikacji połączonych z usługą Azure AD w organizacji, do których mają dostęp.

   * Najlepiej jest upewnić się, że aplikacje są skonfigurowane do logowania jednokrotnego, aby zapewnić użytkownikom najlepsze środowisko.

* Zażądaj dostępu do nowych aplikacji skonfigurowanych do samoobsługi.

* Tworzenie kolekcji osobistych aplikacji.

* Zarządzanie dostępem do aplikacji dla innych osób po przypisaniu roli właściciela grupy lub delegowanego sterowania dla grupy używanej do udzielania dostępu do aplikacji.

Administratorzy mogą konfigurować:

* [Środowisko wyrażania zgody,](../manage-apps/configure-user-consent.md)  w tym warunki użytkowania usługi.

* [Żądania samoobsługowego odnajdywania aplikacji i uzyskiwania do nich dostępu.](../manage-apps/access-panel-manage-self-service-access.md)

* [Kolekcje aplikacji](../manage-apps/access-panel-collections.md).

* Przypisywanie ikon do aplikacji

* Przyjazne dla użytkownika nazwy aplikacji

* Znakowanie firmowe wyświetlane na Moje aplikacje

 

## <a name="plan-consent-configuration"></a>Planowanie konfiguracji zgody

### <a name="user-consent-for-applications"></a>Zgoda użytkownika na aplikacje 

Zanim użytkownik będzie w stanie zalogować się do aplikacji, a aplikacja będzie mieć dostęp do danych organizacji, użytkownik lub administrator musi udzielić uprawnień aplikacji. Można określić, czy zgoda użytkownika jest dozwolona i w jakich warunkach. **Firma Microsoft zaleca zezwalanie na wyrażanie zgody przez użytkownika tylko w przypadku aplikacji od zweryfikowanych wydawców.**

Aby uzyskać więcej informacji, zobacz [Konfigurowanie sposobu wyrażania zgody przez użytkowników końcowych na aplikacje](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Zgoda właściciela grupy dla aplikacji, które mają dostęp do danych

Właściciele grupy i zespołu mogą autoryzować aplikacje, takie jak aplikacje opublikowane przez innych dostawców, do uzyskiwania dostępu do danych organizacji skojarzonych z grupą. Aby dowiedzieć [się więcej, zobacz Zgoda](https://docs.microsoft.com/microsoftteams/resource-specific-consent) specyficzna dla zasobów w aplikacji Microsoft Teams. 

Możesz określić, czy chcesz zezwolić na tę funkcję, czy ją wyłączyć.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie uprawnień do wyrażania zgody przez grupę.](../manage-apps/configure-user-consent-groups.md)

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja ma kluczowe znaczenie dla sukcesu każdej nowej usługi. Proaktywne informowanie użytkowników o tym, jak i kiedy ich środowisko zmieni się oraz jak uzyskać pomoc techniczną w razie potrzeby.

Chociaż Moje aplikacje zwykle nie tworzy problemów z użytkownikiem, należy się przygotować. Przed startem utwórz przewodniki i listę wszystkich zasobów dla pracowników pomocy technicznej.

#### <a name="communications-templates"></a>Szablony komunikacji

Firma Microsoft udostępnia [dostosowywalne szablony wiadomości e-mail i innych](https://aka.ms/APTemplates) wiadomości Moje aplikacje. Te zasoby można dostosować do użycia w innych kanałach komunikacyjnych zgodnie z potrzebami kultury firmowej.

 

## <a name="plan-your-sso-configuration"></a>Planowanie konfiguracji logowania jednokrotnego

Najlepiej, jeśli logowanie jednokrotne jest włączone dla wszystkich aplikacji w portalu Moje aplikacje, aby użytkownicy mieli bezproblemowe środowisko pracy bez konieczności wprowadzania poświadczeń.

Usługa Azure AD obsługuje wiele opcji logowania jednokrotnego. 

* Aby dowiedzieć się więcej, zobacz [Opcje logowania pojedynczego w usłudze Azure AD.](sso-options.md)

* Aby dowiedzieć się więcej na temat używania usługi Azure AD jako dostawcy tożsamości dla aplikacji, zobacz [serię Szybki start na temat zarządzania aplikacją.](../manage-apps/view-applications-portal.md)

### <a name="use-federated-sso-if-possible"></a>Jeśli to możliwe, używaj federowanych logowania jednokrotnego

Aby uzyskać najlepsze środowisko użytkownika na stronie Moje aplikacje, zacznij od integracji aplikacji w chmurze, które są dostępne dla federacyjnych logowania jednokrotnego (OpenID Connect saml). Federacyjne logowanie jednokrotne umożliwia użytkownikom spójne środowisko uruchamiania aplikacji jednym kliknięciem i zapewnia większą niezawodność w zakresie kontroli konfiguracji.

Aby uzyskać więcej informacji na temat sposobu konfigurowania aplikacji typu oprogramowanie jako usługa (SaaS) dla logowania jednokrotnego, zobacz [Plan wdrażania logowania jednokrotnego w modelu SaaS].. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Uwagi dotyczące specjalnych okoliczności logowania jednokrotnego

> [!TIP]
> Aby uzyskać lepsze środowisko użytkownika, użyj federowanych logowania jednokrotnego z usługą Azure AD (OpenID Connect/SAML), gdy aplikacja go obsługuje, zamiast logowania jednokrotnego opartego na hasłach i usług ADFS.

Aby zalogować się do aplikacji logowania jednokrotnego opartych na hasłach lub do aplikacji, do których uzyskuje się dostęp za pośrednictwem usługi Azure AD serwer proxy aplikacji, użytkownicy muszą zainstalować rozszerzenie bezpiecznego logowania Moje aplikacje korzystać z niego. Użytkownicy są monitowane o zainstalowanie rozszerzenia przy pierwszym uruchomieniu aplikacji logowania jednokrotnego lub aplikacji serwer proxy aplikacji hasła. 

![Zrzut ekranu przedstawiający](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Aby uzyskać szczegółowe informacje na temat rozszerzenia, zobacz [Installing Moje aplikacje browser extension (Instalowanie Moje aplikacje rozszerzenia przeglądarki).](../user-help/my-apps-portal-end-user-access.md)

Jeśli musisz zintegrować te aplikacje, musisz zdefiniować mechanizm wdrażania rozszerzenia na dużą skalę z [obsługiwanymi przeglądarkami.](../user-help/my-apps-portal-end-user-access.md) Dostępne są następujące opcje:

* [Sterowane przez użytkownika pobieranie i konfigurowanie przeglądarek Chrome, Firefox, Microsoft Edge lub IE](../user-help/my-apps-portal-end-user-access.md)

* [Menedżer konfiguracji for Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

Rozszerzenie umożliwia użytkownikom uruchamianie dowolnej aplikacji na pasku wyszukiwania, znajdowanie dostępu do ostatnio używanych aplikacji i posiadanie linku do Moje aplikacje aplikacji.

![Zrzut ekranu przedstawiający wyszukiwanie rozszerzeń aplikacji](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Planowanie dostępu dla urządzeń przenośnych

W przypadku aplikacji korzystających z logowania jednokrotnego opartego na hasłach lub dostępnych przy [użyciu Microsoft Azure AD serwer proxy aplikacji](../manage-apps/application-proxy.md)należy użyć aplikacji Microsoft Edge mobilnych. W przypadku innych aplikacji można używać dowolnej przeglądarki dla urządzeń przenośnych. 

### <a name="linked-sso"></a>Połączone logowanie jednokrotne

Aplikacje można dodawać przy użyciu opcji Połączone logowanie jednokrotne. Możesz skonfigurować kafelek aplikacji, który będzie łączył się z adresem URL istniejącej aplikacji internetowej. Połączone logowanie jednokrotne umożliwia rozpoczęcie kierowania użytkowników do witryny Moje aplikacje Portal bez migrowania wszystkich aplikacji do logowania jednokrotnego w usłudze Azure AD. Możesz stopniowo przechodzić do aplikacji skonfigurowanych przy użyciu logowania jednokrotnego usługi Azure AD bez zakłócania pracy użytkowników.

## <a name="plan-the-user-experience"></a>Planowanie obsługi użytkownika

Domyślnie wszystkie aplikacje, do których użytkownik ma dostęp, i wszystkie aplikacje skonfigurowane do samoobsługowego odnajdywania są wyświetlane na panelu Moje aplikacje użytkownika. W przypadku wielu organizacji może to być bardzo duża lista, która może być uciążliwa, jeśli nie jest zorganizowana

### <a name="plan-my-apps-collections"></a>Planowanie Moje aplikacje kolekcji

Każda aplikacja usługi Azure AD, do której użytkownik ma dostęp, będzie wyświetlana Moje aplikacje kolekcji Wszystkie aplikacje. Kolekcje ułatwiają grupowanie powiązanych aplikacji i prezentowanie ich na osobnej karcie. Na przykład kolekcji można użyć do tworzenia logicznych grup aplikacji dla określonych ról zadań, zadań, projektów i tak dalej. 

Użytkownicy końcowi mogą również dostosować swoje środowisko,

* Tworzenie własnych kolekcji aplikacji.

* [Ukrywanie i zmiana kolejności kolekcji aplikacji.](access-panel-collections.md)

![Zrzut ekranu przedstawiający konfigurację samoobsługową](./media/my-apps-deployment-plan/collections.png)

Istnieje możliwość ukrycia aplikacji w portalu Moje aplikacje, jednocześnie zezwalając na dostęp z innych lokalizacji, takich jak Microsoft 365 portal. Dowiedz się więcej: [Ukrywanie aplikacji w interfejsie użytkownika](hide-application-from-user-portal.md)w Azure Active Directory.

> [!IMPORTANT]
> Tylko 950 aplikacji, do których użytkownik ma dostęp, można uzyskać za pośrednictwem Moje aplikacje. Obejmuje to aplikacje ukryte przez użytkownika lub administratora. 

### <a name="plan-self-service-group-management-membership"></a>Planowanie samoobsługowego członkostwa w zarządzaniu grupą

Możesz umożliwić użytkownikom tworzenie własnych grup zabezpieczeń lub grup zabezpieczeń oraz zarządzanie nimi Microsoft 365 w usłudze Azure AD. Właściciel grupy może zatwierdzać lub odrzucać żądania członkostwa i delegować kontrolę nad członkostwem w grupie. Funkcje samoobsługowego zarządzania grupami nie są dostępne dla grup zabezpieczeń lub list dystrybucyjnych z włączoną obsługą poczty.

Aby zaplanować samoobsługowe członkostwo w grupach, określ, czy zezwolisz wszystkim użytkownikom w organizacji na tworzenie grup i zarządzanie nimi, czy tylko na podzestaw użytkowników. Jeśli zezwalasz na podzestaw użytkowników, musisz skonfigurować grupę, do której te osoby są dodawane. 

Aby uzyskać szczegółowe informacje na temat włączania tych [scenariuszy, zobacz Konfigurowanie](../enterprise-users/groups-self-service-management.md) samoobsługowego zarządzania Azure Active Directory w usłudze .

### <a name="plan-self-service-application-access"></a>Planowanie samoobsługowego dostępu do aplikacji

Możesz umożliwić użytkownikom odnajdywanie i żądanie dostępu do aplikacji za pośrednictwem Moje aplikacje aplikacji. Aby to zrobić, należy najpierw 

* włączanie samoobsługowego zarządzania grupą

* włączanie aplikacji dla logowania jednokrotnego

* tworzenie grupy na celu uzyskanie dostępu do aplikacji

![Zrzut ekranu Moje aplikacje konfiguracji samoobsługowej](./media/my-apps-deployment-plan/my-apps-self-service.png)

Gdy użytkownicy żądają dostępu, żądają dostępu do grupy źródłowej, a właściciele grupy mogą delegować uprawnienia do zarządzania członkostwem w grupie, a tym samym dostępu do aplikacji. Przepływy pracy zatwierdzania są dostępne do jawnego zatwierdzania w celu uzyskania dostępu do aplikacji. Użytkownicy, którzy są osobami zatwierdzającym, otrzymają powiadomienia w portalu Moje aplikacje, gdy istnieje oczekujące żądanie dostępu do aplikacji.

## <a name="plan-reporting-and-auditing"></a>Planowanie raportowania i inspekcji

Usługa Azure AD udostępnia [raporty, które oferują szczegółowe informacje techniczne i biznesowe].. /reports-monitoring/overview-reports.md). We współpracy z właścicielami aplikacji biznesowych i technicznych przejmij własność tych raportów i regularnie z nich korzystaj. W poniższej tabeli przedstawiono kilka przykładów typowych scenariuszy raportowania.

| Przykład| Zarządzanie ryzykiem| Zwiększanie produktywności| Zarządzanie i zgodność |
| - | - | - | -|
| Typy raportów| Uprawnienia i użycie aplikacji| Działanie aprowingu konta| Sprawdź, kto uzyskuje dostęp do aplikacji |
| Potencjalne akcje| Inspekcja dostępu; odwoływanie uprawnień| Korygowanie błędów aprowacji| Odwoływanie dostępu |


Usługa Azure AD przechowuje większość danych inspekcji przez 30 dni. Dane są dostępne za pośrednictwem portalu administracyjnego platformy Azure lub interfejsu API, które można pobrać do systemów analizy.

#### <a name="auditing"></a>Inspekcja

Dzienniki inspekcji dotyczące dostępu do aplikacji są dostępne przez 30 dni. Jeśli Twoja organizacja wymaga dłuższego przechowywania, wyeksportuj dzienniki do narzędzia security information event and management (SIEM), takiego jak Splunk lub ArcSight.

W przypadku kopii zapasowych inspekcji, raportowania i odzyskiwania po awarii należy udokumentować wymaganą częstotliwość pobierania, co to jest system docelowy i kto jest odpowiedzialny za zarządzanie każdą kopią zapasową. Tworzenie oddzielnych kopii zapasowych inspekcji i raportowania może nie być konieczne. Kopia zapasowa odzyskiwania po awarii powinna być oddzielną jednostką.

## <a name="validate-your-deployment"></a>Weryfikowanie wdrożenia

Upewnij się Moje aplikacje że wdrożenie aplikacji jest dokładnie przetestowane i że istnieje plan wycofywania.

Przeć kroki poniższych testów zarówno na urządzeniach należących do firmy, jak i na urządzeniach osobistych. Te przypadki testowe powinny również odzwierciedlać przypadki użycia biznesowego. Poniżej przedstawiono kilka przypadków opartych na typowych scenariuszach technicznych. Dodaj inne, które są specyficzne dla Twoich potrzeb.

#### <a name="application-sso-access-test-case-examples"></a>Przykłady przypadków testowych dostępu do logowania jednokrotnego aplikacji:


| Przypadek biznesowy| Oczekiwany wynik |
| - | - |
| Użytkownik musi się do tego Moje aplikacje portalem| Użytkownik może zalogować się i wyświetlić swoje aplikacje |
| Użytkownik uruchamia federacyjną aplikację logowania jednokrotnego| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację z logowaniem jednokrotnym z hasłem po raz pierwszy| Użytkownik musi zainstalować rozszerzenie Moje aplikacje |
| Użytkownik uruchamia aplikację logowania jednokrotnego z hasłem w późniejszym czasie| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację z witryny Microsoft 365 Portal| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację z Managed Browser| Użytkownik jest automatycznie zalogowany do aplikacji |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Przykłady przypadków testowych możliwości samoobsługi aplikacji


| Przypadek biznesowy| Oczekiwany wynik |
| - | - |
| Użytkownik może zarządzać członkostwem w aplikacji| Użytkownik może dodawać/usuwać członków, którzy mają dostęp do aplikacji |
| Użytkownik może edytować aplikację| Użytkownik może edytować opis i poświadczenia aplikacji logowania jednokrotnego przy użyciu hasła |


### <a name="rollback-steps"></a>Kroki wycofywania

Ważne jest, aby zaplanować, co należy zrobić, jeśli wdrożenie nie zostanie zaplanowane. Jeśli konfiguracja logowania jednokrotnego nie powiedzie się podczas wdrażania, musisz zrozumieć, jak rozwiązywać problemy z logowaniem [jednokrotnym](../hybrid/tshoot-connect-sso.md) i zmniejszyć wpływ na użytkowników. W skrajnych okolicznościach może być konieczne [wycofanie logowania jednokrotnego](plan-sso-deployment.md).

## <a name="manage-your-implementation"></a>Zarządzanie implementacją

Użyj roli o najmniejszych uprawnieniach, aby wykonać wymagane zadanie w Azure Active Directory. [Zapoznaj się z różnymi dostępnymi rolami](../roles/permissions-reference.md) i wybierz odpowiednią rolę, aby rozwiązać potrzeby każdej osoby w tej aplikacji. Niektóre role mogą wymagać tymczasowego zastosowania i usunięcia po zakończeniu wdrażania.

| Osoby| Role| Rola usługi Azure AD |
| - | - | - |
| Administrator pomocy technicznej| Obsługa warstwy 1| Brak |
| Administrator tożsamości| Konfigurowanie i debugowanie, gdy problemy wpływają na usługę Azure AD| Administrator globalny |
| Administrator aplikacji| Zaświadczenia użytkownika w aplikacji, konfiguracja dla użytkowników z uprawnieniami| Brak |
| Administratorzy infrastruktury| Właściciel przechowania certyfikatu| Administrator globalny |
| Właściciel firmy/uczestnik projektu| Zaświadczenia użytkownika w aplikacji, konfiguracja dla użytkowników z uprawnieniami| Brak |


Za pomocą [Privileged Identity Management](../privileged-identity-management/pim-configure.md) można zarządzać rolami w celu zapewnienia dodatkowej inspekcji, kontroli i przeglądu dostępu dla użytkowników z uprawnieniami katalogu.

## <a name="next-steps"></a>Następne kroki

[Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Planowanie wdrożenia serwera proxy aplikacji](application-proxy-deployment-plan.md)

