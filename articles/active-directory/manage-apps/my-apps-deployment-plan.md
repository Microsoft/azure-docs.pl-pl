---
title: Planowanie Azure Active Directory konfiguracji aplikacji
description: Przewodnik planowania, aby efektywnie korzystać z aplikacji w organizacji.
services: active-directory
author: barbaraselden
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: f63a8fd05e1a6ed5e41eeb64aa852ff01db295af
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645471"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Planowanie Azure Active Directory konfiguracji aplikacji

> [!NOTE]
> Ten artykuł jest przeznaczony dla specjalistów IT, którzy muszą zaplanować konfigurację portalu Moje aplikacje w organizacji. 
>
> **Aby uzyskać dokumentację dotyczącą użytkowników końcowych, zobacz artykuł [Logowanie i uruchamianie aplikacji z poziomu portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md)**.

Azure Active Directory (Azure AD) moje aplikacje to portal oparty na sieci Web służący do uruchamiania aplikacji i zarządzania nimi. Strona Moje aplikacje umożliwia użytkownikom pojedyncze miejsce na rozpoczęcie pracy i znalezienie wszystkich aplikacji, do których mają dostęp. Użytkownicy uzyskują dostęp do moich aplikacji w witrynie [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Dlaczego należy skonfigurować moje aplikacje

Portal moje aplikacje jest domyślnie dostępny dla użytkowników i nie można go wyłączyć. Ważne jest, aby skonfigurować je tak, aby były one najlepszym możliwym doświadczeniem, a Portal pozostaje użyteczny. 

Wszystkie aplikacje na liście Azure Active Directory Enterprise Applications są wyświetlane po spełnieniu obu następujących warunków:

* Właściwość widoczności dla aplikacji jest ustawiona na wartość true. 

* Aplikacja zostanie przypisana do dowolnego użytkownika lub grupy. Pojawia się dla przypisanych użytkowników.

Skonfigurowanie portalu zapewnia, że odpowiednie osoby mogą łatwo znaleźć odpowiednie aplikacje.

 
### <a name="how-is-the-my-apps-portal-used"></a>Jak jest używany Portal moje aplikacje?

Użytkownicy uzyskują dostęp do portalu Moje aplikacje, aby:

* Odkryj i uzyskaj dostęp do wszystkich aplikacji połączonych z usługą Azure AD, do których mają dostęp.

   * Najlepiej, aby aplikacje były skonfigurowane pod kątem logowania jednokrotnego (SSO) w celu zapewnienia użytkownikom najlepszego środowiska.

* Zażądaj dostępu do nowych aplikacji, które są skonfigurowane do samoobsługi.

* Tworzenie osobistych kolekcji aplikacji.

* Zarządzanie dostępem do aplikacji dla innych osób po przypisaniu roli właściciela grupy lub kontroli delegowanej dla grupy używanej do udzielania dostępu do aplikacji.

Administratorzy mogą konfigurować:

* [Funkcje wyrażania zgody](../manage-apps/configure-user-consent.md)  , w tym warunki korzystania z usługi.

* [Samoobsługowe odnajdywanie aplikacji i żądania dostępu](../manage-apps/access-panel-manage-self-service-access.md).

* [Kolekcje aplikacji](../manage-apps/access-panel-collections.md).

* Przypisanie ikon do aplikacji

* Przyjazne dla użytkownika nazwy aplikacji

* Znakowanie firmowe wyświetlane w moich aplikacjach

 

## <a name="plan-consent-configuration"></a>Zaplanuj konfigurację zgody

Istnieją dwa typy wyrazów zgody: użytkownik wyraża zgodę i zgodę na aplikacje uzyskujące dostęp do danych.

![Zrzut ekranu przedstawiający konfigurację zgody](./media/my-apps-deployment-plan/my-apps-consent.png)

### <a name="user-consent-for-applications"></a>Użytkownik wyraża zgodę na aplikacje 

Użytkownicy lub Administratorzy muszą wyrazić zgodę na warunki użytkowania aplikacji oraz zasady zachowania poufności informacji. Należy zdecydować, czy użytkownicy lub tylko Administratorzy mogą wyrazić zgodę na aplikacje. **Firma Microsoft zaleca, aby jeśli reguły biznesowe umożliwiały korzystanie z programu, użytkownik wyraża zgodę na zachowanie kontroli nad aplikacjami w dzierżawie**.

Aby korzystać z zgody administratora, musisz być administratorem globalnym organizacji, a aplikacje muszą mieć jedną z tych opcji:

* Zarejestrowane w organizacji.

* Zarejestrowane w innej organizacji usługi Azure AD, które zostały wcześniej wysłane do co najmniej jednego użytkownika.

Jeśli chcesz zezwolić użytkownikom na wyrażanie zgody, musisz zdecydować, czy chcesz je wyrazić zgodę na dowolną aplikację, czy tylko w określonych okolicznościach.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikację w Azure Active Directory.](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Wyrażanie zgody właściciela grupy na aplikacje uzyskujące dostęp do danych

Ustal, czy właściciele grup zabezpieczeń usługi Azure AD lub grup M365 są w stanie wyrazić zgodę na dostęp do danych dla należących do nich grup. Możesz nie zezwalać na wszystkie właściciele grup lub zezwalać tylko na podzbiór właścicieli grup.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie uprawnień do zgody na grupy](../manage-apps/configure-user-consent-groups.md).

Następnie skonfiguruj [Ustawienia zgody użytkownika i właściciela grupy](https://portal.azure.com/) w Azure Portal.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Proaktywnie Informuj użytkowników, jak i kiedy ich środowisko zmiany się zmieni i jak uzyskać pomoc techniczną, jeśli jest to konieczne.

Mimo że moje aplikacje zazwyczaj nie tworzą problemów z użytkownikami, ważne jest przygotowanie. Przed rozpoczęciem Utwórz przewodniki i listę wszystkich zasobów dla personelu pomocy technicznej.

#### <a name="communications-templates"></a>Szablony komunikacji

Firma Microsoft udostępnia [dostosowywalne szablony dla wiadomości e-mail i innych komunikacji](https://aka.ms/APTemplates) dla moich aplikacji. Można dostosować te zasoby do użycia w innych kanałach komunikacyjnych, zgodnie z potrzebami kultury firmowej.

 

## <a name="plan-your-sso-configuration"></a>Planowanie konfiguracji logowania jednokrotnego

Najlepszym rozwiązaniem jest włączenie logowania jednokrotnego dla wszystkich aplikacji w portalu My Apps, dzięki czemu użytkownicy będą mogli bezproblemowo korzystać bez konieczności wprowadzania poświadczeń.

Usługa Azure AD obsługuje wiele opcji logowania jednokrotnego. 

* Aby dowiedzieć się więcej, zobacz Opcje logowania jednokrotnego [w usłudze Azure AD](sso-options.md).

* Aby dowiedzieć się więcej o korzystaniu z usługi Azure AD jako dostawcy tożsamości dla aplikacji, zobacz [serie szybkiego startu w zarządzaniu aplikacjami](../manage-apps/view-applications-portal.md).

### <a name="use-federated-sso-if-possible"></a>Jeśli to możliwe, użyj federacyjnego logowania jednokrotnego

Aby zapewnić najlepsze środowisko użytkownika na stronie Moje aplikacje, Zacznij od integracji aplikacji w chmurze, które są dostępne dla federacyjnego logowania jednokrotnego (OpenID Connect Connect lub SAML). Federacyjnego Logowanie jednokrotne umożliwia użytkownikom spójne środowisko pojedynczego kliknięcia na stronach uruchamiania aplikacji i sprawia, że jest bardziej niezawodna w kontroli konfiguracji.

Aby uzyskać więcej informacji na temat sposobu konfigurowania aplikacji SaaS (Software as a Service) na potrzeby logowania jednokrotnego, zobacz [SaaS-SSO Deployment plan wdrażania].. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Uwagi dotyczące specjalnych okoliczności logowania jednokrotnego

> [!TIP]
> Aby lepiej wykorzystać środowisko użytkownika, użyj federacyjnego logowania jednokrotnego za pomocą usługi Azure AD (OpenID Connect Connect/SAML), gdy aplikacja ją obsługuje, zamiast logowania jednokrotnego i usług ADFS opartych na hasłach.

Aby zalogować się do aplikacji logowania jednokrotnego opartego na hasłach lub do aplikacji, do których dostęp odbywa się za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD, użytkownicy muszą zainstalować i używać rozszerzenia moje aplikacje bezpieczne logowanie. Użytkownicy są monitowani o zainstalowanie rozszerzenia podczas pierwszego uruchomienia aplikacji logowania jednokrotnego lub serwera proxy aplikacji opartego na hasłach. 

![Zrzut ekranu przedstawiający](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Aby uzyskać szczegółowe informacje na temat rozszerzenia, zobacz [Installing my Apps Extension Browser](../user-help/my-apps-portal-end-user-access.md).

Jeśli trzeba zintegrować te aplikacje, należy zdefiniować mechanizm wdrażania rozszerzenia na dużą skalę z [obsługiwanymi przeglądarkami](../user-help/my-apps-portal-end-user-access.md). Dostępne są następujące opcje:

* [Pobieranie i Konfigurowanie sterowane przez użytkownika dla programu Chrome, Firefox, Microsoft Edge lub IE](../user-help/my-apps-portal-end-user-access.md)

* [Configuration Manager programu Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

Rozszerzenie umożliwia użytkownikom uruchamianie dowolnych aplikacji z paska wyszukiwania, znajdowanie dostępu do ostatnio używanych aplikacji i posiadanie linku do strony Moje aplikacje.

![Zrzut ekranu przedstawiający wyszukiwanie rozszerzenia my Apps](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Planowanie dostępu mobilnego

W przypadku aplikacji korzystających z logowania jednokrotnego opartego na hasłach lub dostępu przy użyciu [Microsoft Azure AD serwera proxy aplikacji](../manage-apps/application-proxy.md)należy użyć programu Microsoft Edge Mobile. W przypadku innych aplikacji można użyć dowolnej przeglądarki mobilnej. 

### <a name="linked-sso"></a>Połączone Logowanie jednokrotne

Aplikacje można dodawać przy użyciu opcji połączone Logowanie jednokrotne. Można skonfigurować kafelek aplikacji, który łączy się z adresem URL istniejącej aplikacji sieci Web. Połączone Logowanie jednokrotne pozwala rozpocząć kierowanie użytkowników do portalu My Apps bez migrowania wszystkich aplikacji do usługi Azure AD SSO. Możesz stopniowo przechodzić do aplikacji skonfigurowanych na potrzeby rejestracji jednokrotnej usługi Azure AD bez zakłócania pracy użytkowników.

## <a name="plan-the-user-experience"></a>Planowanie środowiska użytkownika

Domyślnie wszystkie aplikacje, do których użytkownik ma dostęp, oraz wszystkie aplikacje skonfigurowane na potrzeby odnajdywania samoobsługowego są wyświetlane w panelu Moje aplikacje użytkownika. W przypadku wielu organizacji może to być bardzo duża lista, która może stać się uciążliwa, jeśli nie jest zorganizowana

### <a name="plan-my-apps-collections"></a>Planowanie kolekcji Moje aplikacje

Każda aplikacja usługi Azure AD, do której użytkownik ma dostęp, będzie widoczna w obszarze Moje aplikacje w kolekcji wszystkie aplikacje. Za pomocą kolekcji można grupować powiązane aplikacje i przedstawić je na osobnej karcie, co ułatwia ich znalezienie. Można na przykład użyć kolekcji do tworzenia logicznych grup aplikacji dla określonych ról zadań, zadań, projektów i tak dalej. 

Użytkownicy końcowi mogą również dostosować ich środowisko przez

* Tworzenie własnych kolekcji aplikacji.

* [Ukrywanie i zmiana kolejności kolekcji aplikacji](access-panel-collections.md).

![Zrzut ekranu konfiguracji samoobsługowej](./media/my-apps-deployment-plan/collections.png)

Istnieje możliwość ukrycia aplikacji w portalu My Apps, pozostawiając jednocześnie dostęp z innych lokalizacji, takich jak portal Microsoft 365. Dowiedz się więcej: [Ukryj aplikację ze środowiska użytkownika w Azure Active Directory](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Tylko aplikacje 950, do których użytkownik ma dostęp, są dostępne za pomocą aplikacji Moje aplikacje. Obejmuje to aplikacje ukryte przez użytkownika lub administratora. 

### <a name="plan-self-service-group-management-membership"></a>Planowanie przynależności do samoobsługowego zarządzania grupami

Można umożliwić użytkownikom tworzenie własnych grup zabezpieczeń lub grup Microsoft 365 w usłudze Azure AD oraz zarządzanie nimi. Właściciel grupy może zatwierdzać lub odrzucać żądania członkostwa oraz delegować kontrolę nad członkostwem w grupie. Funkcje samoobsługowego zarządzania grupami nie są dostępne dla grup zabezpieczeń z włączoną obsługą poczty lub list dystrybucyjnych.

Aby zaplanować członkostwo w grupach samoobsługi, ustal, czy zezwolisz wszystkim użytkownikom w organizacji na tworzenie grup i zarządzanie nimi. Jeśli zezwolisz na podzbiór użytkowników, musisz skonfigurować grupę, do której zostaną dodani te osoby. 

Zobacz [Konfigurowanie samoobsługowego zarządzania grupami w Azure Active Directory](../enterprise-users/groups-self-service-management.md) , aby uzyskać szczegółowe informacje na temat włączania tych scenariuszy.

### <a name="plan-self-service-application-access"></a>Planowanie dostępu do aplikacji samoobsługowej

Możesz umożliwić użytkownikom odnajdywanie i zażądać dostępu do aplikacji za pośrednictwem panelu Moje aplikacje. Aby to zrobić, należy najpierw 

* Włącz Samoobsługowe zarządzanie grupami

* Włącz aplikację dla logowania jednokrotnego

* Tworzenie grupy na potrzeby dostępu do aplikacji

![Zrzut ekranu przedstawiający konfigurację samoobsługowej aplikacji](./media/my-apps-deployment-plan/my-apps-self-service.png)

Gdy użytkownicy zażądają dostępu, żądają dostępu do grupy źródłowej, a właściciele grupy mogą mieć uprawnienia delegowania do zarządzania członkostwem w grupie, a tym samym dostęp do aplikacji. Przepływy pracy zatwierdzania są dostępne dla jawnej zgody na dostęp do aplikacji. Użytkownicy, którzy są osobami zatwierdzającymi, otrzymają powiadomienia w portalu Moje aplikacje, gdy istnieją oczekujące żądania dostępu do aplikacji.

## <a name="plan-reporting-and-auditing"></a>Planowanie raportowania i inspekcji

Usługa Azure AD udostępnia [raporty oferujące informacje techniczne i biznesowe].. /reports-monitoring/overview-reports.md). Skontaktuj się z właścicielami aplikacji firmowych i technicznych, aby założyć własność tych raportów i regularnie korzystać z nich. W poniższej tabeli przedstawiono kilka przykładów typowych scenariuszy raportowania.

| Przykład| Zarządzanie ryzykiem| Zwiększ produktywność| Zarządzanie i zgodność |
| - | - | - | -|
| Typy raportów| Uprawnienia i użycie aplikacji| Działanie aprowizacji konta| Sprawdź, kto uzyskuje dostęp do aplikacji |
| Potencjalni akcje| Inspekcja dostępu; Odwołaj uprawnienia| Koryguj wszelkie błędy aprowizacji| Odwołaj dostęp |


Usługa Azure AD przechowuje większość danych inspekcji przez 30 dni. Dane są dostępne za pośrednictwem portalu administracyjnego platformy Azure lub interfejsu API, aby można było pobrać je do systemów analizy.

#### <a name="auditing"></a>Inspekcja

Dzienniki inspekcji dostępu do aplikacji są dostępne przez 30 dni. Jeśli Twoja organizacja wymaga dłuższego przechowywania, wyeksportuj dzienniki do narzędzia zdarzenia i zarządzania informacjami o zabezpieczeniach (SIEM), np. Splunk lub ArcSight.

W przypadku kopii zapasowych inspekcji, raportowania i odzyskiwania po awarii należy udokumentować wymaganą częstotliwość pobierania, co to jest system docelowy i kto jest odpowiedzialny za zarządzanie każdą kopią zapasową. Może nie być konieczne oddzielne inspekcje i raportowanie kopii zapasowych. Kopia zapasowa odzyskiwania po awarii powinna być oddzielną jednostką.

## <a name="validate-your-deployment"></a>Weryfikowanie wdrożenia

Upewnij się, że wdrożenie moje aplikacje jest dokładnie przetestowane i że plan wycofania jest gotowy.

Przeprowadź następujące testy zarówno dla urządzeń należących do firmy, jak i urządzeń osobistych. Te przypadki testowe powinny również odzwierciedlać Twoje przypadki użycia biznesowego. Poniżej przedstawiono kilka przypadków w oparciu o typowe scenariusze techniczne. Dodaj inne osoby specyficzne dla Twoich potrzeb.

#### <a name="application-sso-access-test-case-examples"></a>Przykłady przypadku testowego dostępu SSO aplikacji:


| Przypadek biznesowy| Oczekiwany wynik |
| - | - |
| Użytkownik loguje się do portalu My Apps| Użytkownik może zalogować się i zobaczyć swoje aplikacje |
| Użytkownik uruchamia federacyjną aplikację SSO| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację Logowanie jednokrotne po raz pierwszy| Użytkownik musi zainstalować rozszerzenie moje aplikacje |
| Użytkownik uruchamia aplikację Logowanie jednokrotne przy użyciu hasła| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację z portalu Microsoft 365| Użytkownik jest automatycznie zalogowany do aplikacji |
| Użytkownik uruchamia aplikację z Managed Browser| Użytkownik jest automatycznie zalogowany do aplikacji |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Przykłady przypadków testowych funkcji samoobsługi aplikacji


| Przypadek biznesowy| Oczekiwany wynik |
| - | - |
| Użytkownik może zarządzać członkostwem w aplikacji| Użytkownik może dodawać/usuwać członków, którzy mają dostęp do aplikacji |
| Użytkownik może edytować aplikację| Użytkownik może edytować opis i poświadczenia aplikacji dla aplikacji SSO logowania jednokrotnego |


### <a name="rollback-steps"></a>Etapy wycofywania

Ważne jest, aby zaplanować czynności, które należy wykonać, jeśli wdrożenie nie zostanie zamierzone. Jeśli konfiguracja logowania jednokrotnego nie powiedzie się podczas wdrażania, należy zrozumieć, jak [rozwiązywać problemy z logowaniem JEDNOkrotnym](../hybrid/tshoot-connect-sso.md) i ograniczyć wpływ na użytkowników. W skrajnych przypadkach może być konieczne [wycofanie rejestracji jednokrotnej](plan-sso-deployment.md).

## <a name="manage-your-implementation"></a>Zarządzanie implementacją

Użyj najmniejszej roli uprzywilejowanej, aby wykonać wymagane zadanie w Azure Active Directory. [Zapoznaj się z różnymi rolami, które są dostępne](../roles/permissions-reference.md) , a następnie wybierz jedną z nich, aby rozwiązać potrzeby każdej osoby dla tej aplikacji. Niektóre role mogą wymagać tymczasowego zastosowania i usunąć po zakończeniu wdrażania.

| Osoby| Role| Rola usługi Azure AD |
| - | - | - |
| Administrator pomocy technicznej| Obsługa warstwy 1| Brak |
| Administrator tożsamości| Konfigurowanie i debugowanie w przypadku problemów mających wpływ na usługę Azure AD| Administrator globalny |
| Administrator aplikacji| Zaświadczanie użytkownika w aplikacji, konfiguracja dla użytkowników z uprawnieniami| Brak |
| Administratorzy infrastruktury| Właściciel przerzucania certyfikatu| Administrator globalny |
| Właściciel firmy/uczestnik projektu| Zaświadczanie użytkownika w aplikacji, konfiguracja dla użytkowników z uprawnieniami| Brak |


Za pomocą [Privileged Identity Management](../privileged-identity-management/pim-configure.md) można zarządzać rolami w celu zapewnienia dodatkowej inspekcji, kontroli i przeglądu dostępu użytkownikom z uprawnieniami do katalogów.

## <a name="next-steps"></a>Następne kroki

[Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Planowanie wdrożenia serwera proxy aplikacji](application-proxy-deployment-plan.md)

