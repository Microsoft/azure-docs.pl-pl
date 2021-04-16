---
title: Migrowanie uwierzytelniania aplikacji do Azure Active Directory
description: W tym oficjalnych oficjalnych planach i korzyściach związanych z migracją uwierzytelniania aplikacji do usługi Azure AD.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/05/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3458f358c12ef33a337e50066e83b6e59273ccf1
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376753"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Migrowanie uwierzytelniania aplikacji do Azure Active Directory

## <a name="about-this-paper"></a>Informacje o tym dokumencie

W tym oficjalnych oficjalnych planach i korzyściach związanych z migracją uwierzytelniania aplikacji do usługi Azure AD. Jest przeznaczona dla administratorów platformy Azure i specjalistów ds. tożsamości.

Podział procesu na cztery fazy, z których każda zawiera szczegółowe kryteria planowania i zakończenia, ma na celu pomoc w zaplanowaniu strategii migracji i zrozumieniu, w jaki sposób uwierzytelnianie usługi Azure AD obsługuje cele organizacji.

## <a name="introduction"></a>Wprowadzenie

Obecnie Twoja organizacja wymaga wielu aplikacji (aplikacji) dla użytkowników, aby wykonać pracę. Prawdopodobnie będziesz codziennie dodawać, opracowywać lub wycofywać aplikacje. Użytkownicy mają dostęp do tych aplikacji z szerokiej gamy firmowych i osobistych urządzeń oraz lokalizacji. Otwierają aplikacje na wiele sposobów, w tym:

- za pośrednictwem strony głównej firmy lub portalu

- przez dodawanie zakładek w przeglądarkach

- za pośrednictwem adresu URL dostawcy dla aplikacji SaaS (oprogramowanie jako usługa)

- linki wypychane bezpośrednio do komputerów stacjonarnych lub urządzeń przenośnych użytkownika za pośrednictwem rozwiązania do zarządzania urządzeniami przenośnymi/aplikacjami (MDM/MAM)

Twoje aplikacje prawdopodobnie będą używać następujących typów uwierzytelniania:

- Lokalne rozwiązania federowe (takie jak Active Directory Federation Services (ADFS) i Ping

- Usługa Active Directory (na przykład uwierzytelnianie Kerberos i Windows-Integrated uwierzytelnianie)

- Inne rozwiązania do zarządzania tożsamościami i dostępem w chmurze (IAM, cloud-based identity and access management) (takie jak Okta lub Oracle)

- Lokalna infrastruktura sieci Web (taka jak usługi IIS i Apache)

- Infrastruktura hostowana w chmurze (taka jak platforma Azure i platforma AWS)

**Aby zapewnić użytkownikom łatwy i bezpieczny dostęp do aplikacji, twoim celem jest zastosowanie jednego zestawu kontroli dostępu i zasad w środowiskach lokalnych i w chmurze.**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) oferuje uniwersalną platformę tożsamości, która zapewnia osobom, partnerom i klientom jedną tożsamość na potrzeby dostępu do aplikacji, których chcą i które współpracują z dowolnej platformy i urządzenia.

![Diagram przedstawiający łączność Azure Active Directory sieci](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Usługa Azure AD oferuje [pełny zestaw funkcji zarządzania tożsamościami.](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad) Standaryzacja uwierzytelniania aplikacji i autoryzacji w usłudze Azure AD umożliwia uzyskanie korzyści zapewnianych przez te możliwości.

Więcej zasobów migracji można znaleźć na stronie [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Korzyści wynikające z migracji uwierzytelniania aplikacji do usługi Azure AD

Przeniesienie uwierzytelniania aplikacji do usługi Azure AD pomoże Ci zarządzać ryzykiem i kosztami, zwiększyć produktywność oraz spełnić wymagania dotyczące zgodności i ładu.

### <a name="manage-risk"></a>Zarządzanie ryzykiem

Ochrona aplikacji wymaga pełnego widoku wszystkich czynników ryzyka. Migracja aplikacji do usługi Azure AD konsoliduje rozwiązania w zakresie zabezpieczeń. Za jego pomocą można:

- Zwiększ bezpieczeństwo dostępu użytkowników do aplikacji [](../conditional-access/overview.md)i skojarzonych danych firmowych przy użyciu zasad dostępu warunkowego, [usługi Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)i technologii [ochrony](../identity-protection/overview-identity-protection.md) tożsamości opartej na ryzyku w czasie rzeczywistym.

- Ochrona dostępu uprzywilejowanych użytkowników do środowiska przy użyciu dostępu administratora just [in time.](../../azure-resource-manager/managed-applications/request-just-in-time-access.md)

- Użyj [wielodostępnej, rozproszonej geograficznie i wysokiej](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)dostępności projektu usługi Azure AD na potrzeby najważniejszych potrzeb biznesowych.

- Chroń starsze aplikacje za pomocą jednej z naszych bezpiecznych integracji z [partnerami](https://aka.ms/secure-hybrid-access) dostępu hybrydowego, które być może zostały już wdrożone.

### <a name="manage-cost"></a>Zarządzanie kosztami

Organizacja może mieć wiele rozwiązań do zarządzania dostępem do tożsamości (IAM). Migracja do jednej infrastruktury usługi Azure AD jest okazją do zmniejszenia zależności od licencji IAM (lokalnie lub w chmurze) i kosztów infrastruktury. W przypadkach, w których usługa Azure AD była już opłacana za pośrednictwem licencji usługi Microsoft 365, nie ma powodu, aby płacić dodatkowy koszt innego rozwiązania IAM.

**Usługa Azure AD umożliwia obniżenie kosztów infrastruktury przez:**

- Zapewnianie bezpiecznego dostępu zdalnego do aplikacji lokalnych przy użyciu [usługi Azure AD serwer proxy aplikacji.](./application-proxy.md)

- Oddzielenie aplikacji od lokalnego podejścia do poświadczeń w dzierżawie przez skonfigurowanie usługi [Azure AD jako zaufanego](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)uniwersalnego dostawcy tożsamości.

### <a name="increase-productivity"></a>Zwiększanie produktywności

Korzyści ekonomiczne i związane z zabezpieczeniami wpływają na to, że organizacje mogą przyjąć usługę Azure AD, ale pełne wdrożenie i zgodność również będą korzystne dla użytkowników. Usługa Azure AD umożliwia:

- Ulepszanie obsługi logowania jednokrotnego Sign-On [(SSO)](./what-is-single-sign-on.md) przez bezproblemowy i bezpieczny dostęp do dowolnej aplikacji z dowolnego urządzenia i z dowolnej lokalizacji.

- Korzystaj z funkcji samoobsługowego zarządzania zarządzaniem hasłami, takich jak samoobsługowe resetowanie [haseł](../authentication/concept-sspr-howitworks.md) i [samoobsługowe zarządzanie grupą.](../enterprise-users/groups-self-service-management.md)

- Zmniejsz koszty administracyjne, zarządzając tylko jedną tożsamością dla każdego użytkownika w środowisku chmurowym i lokalnym:

  - [Automatyzowanie aprowizowania](../app-provisioning/user-provisioning.md) kont użytkowników (w [galerii usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)) na podstawie tożsamości usługi Azure AD
  - Uzyskiwanie dostępu do wszystkich aplikacji z panelu MyApps w [Azure Portal ](https://portal.azure.com/)

- Umożliwienie deweloperom bezpiecznego dostępu do swoich aplikacji i ulepszanie obsługi użytkowników końcowych przy użyciu platformy tożsamości firmy [Microsoft](../develop/v2-overview.md) z biblioteką Microsoft Authentication Library (MSAL).

- Zapewnij partnerom dostęp do zasobów w chmurze przy użyciu [funkcji współpracy B2B w](../external-identities/what-is-b2b.md)usłudze Azure AD. Zasoby w chmurze usuwają obciążenie związane z konfigurowaniem federacji punkt-punkt z partnerami.

### <a name="address-compliance-and-governance"></a>Adresowanie zgodności i ładu

Zapewnij zgodność z wymaganiami prawnymi, wymuszając zasady dostępu firmowego i monitorując dostęp użytkowników do aplikacji i skojarzonych danych przy użyciu zintegrowanych narzędzi inspekcji i interfejsów API. Za pomocą usługi Azure AD można monitorować logowania aplikacji za pomocą raportów, które korzystają z narzędzi do monitorowania zdarzeń i zdarzeń zabezpieczeń [(SIEM).](../reports-monitoring/plan-monitoring-and-reporting.md) Dostęp do raportów można uzyskać z portalu lub interfejsów API, a także programowo sprawdzać, kto ma dostęp do aplikacji, i usuwać dostęp nieaktywnych użytkowników za pośrednictwem przeglądów dostępu.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Planowanie faz migracji i strategii projektu

Gdy projekty technologiczne nie powiodą się, często wynika to z niezgodności oczekiwań, braku zaangażowania odpowiednich uczestników projektu lub braku komunikacji. Zapewnij powodzenie, planując sam projekt.

### <a name="the-phases-of-migration"></a>Fazy migracji

Zanim dojdą do narzędzi, należy zrozumieć, jak myśleć o procesie migracji. W ramach kilku warsztatów bezpośrednich dla klientów zalecamy następujące cztery fazy:

![Diagram faz migracji](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Składanie zespołu projektu

Migracja aplikacji jest wysiłkiem zespołu i należy upewnić się, że wszystkie ważne pozycje zostały wypełnione. Ważne jest wsparcie starszych liderów biznesowych. Upewnij się, że uczestniczysz w odpowiednim zestawie sponsorów wykonawczych, osób decyzyjnych biznesowych i ekspertów w tej dziedzinie (MSP).

W trakcie projektu migracji jedna osoba może pełnić wiele ról lub wiele osób pełni każdą rolę, w zależności od rozmiaru i struktury organizacji. Możesz również mieć zależność od innych zespołów, które odgrywają kluczową rolę w twoim krajobrazie zabezpieczeń.

W poniższej tabeli przedstawiono najważniejsze role i ich wkład:

| Rola          | Contributions                                              |
| ------------- | ---------------------------------------------------------- |
| **Project Manager** | Kierownik projektu, który jest odpowiedzialna za prowadzenie projektu, w tym:<br /> — zyskaj wsparcie kadry kierowniczej<br /> — przyniesienie uczestników projektu<br /> — zarządzanie harmonogramami, dokumentacją i komunikacją |
| **Architekt tożsamości/ aplikacja usługi Azure AD administratora** | Są oni odpowiedzialni za następujące czynności:<br /> — projektowanie rozwiązania we współpracy z uczestnikami projektu<br /> — udokumentowanie projektu rozwiązania i procedur operacyjnych w celu ich reprezentacji zespołowi operacyjnemu<br /> — zarządzanie środowiskami przedprodukcyjną i produkcyjną |
| **Zespół operacyjny lokalnej usługi AD** | Organizacja, która zarządza różnymi lokalnymi źródłami tożsamości, takimi jak lasy usługi AD, katalogi LDAP, systemy hr itp.<br /> — wykonywanie wszelkich wymaganych zadań korygowania przed synchronizacją<br /> - Podaj konta usług wymagane do synchronizacji<br /> — zapewnianie dostępu do konfigurowania federacji w usłudze Azure AD |
| **Menedżer pomocy technicznej IT** | Przedstawiciel organizacji pomocy technicznej IT, który może udzielić opinii na temat możliwości obsługi tej zmiany z perspektywy działu pomocy technicznej. |
| **Właściciel zabezpieczeń**  | Przedstawiciel zespołu ds. zabezpieczeń, który może zagwarantować, że plan będzie spełniał wymagania organizacji dotyczące zabezpieczeń. |
| **Właściciele techniczna aplikacji** | Obejmuje właścicieli technicznych aplikacji i usług, które zostaną zintegrowane z usługą Azure AD. Zapewniają one atrybuty tożsamości aplikacji, które powinny zostać uwzględnić w procesie synchronizacji. Zazwyczaj mają one relację z przedstawicielami woluminów CSV. |
| **Właściciele firm aplikacji** | Reprezentatywni współpracownicy, którzy mogą dostarczyć opinii na temat środowiska użytkownika i przydatności tej zmiany z perspektywy użytkownika i są właścicielami ogólnego aspektu biznesowego aplikacji, który może obejmować zarządzanie dostępem. |
| **Grupa pilotażowa użytkowników** | Użytkownicy, którzy będą testować w ramach codziennej pracy, doświadczenia pilotażowego i przekazać opinię, aby kierować resztą wdrożeń. |

### <a name="plan-communications"></a>Planowanie komunikacji

Skuteczne zaangażowanie biznesowe i komunikacja są kluczem do sukcesu. Ważne jest, aby zapewnić uczestnikom projektu i użytkownikom końcowych drogi do uzyskania informacji i na bieżąco z harmonogramem aktualizacji. Edukować wszystkich na temat wartości migracji, oczekiwanych harmonogramów oraz sposobu planowania tymczasowych zakłóceń w działalności biznesowej. Korzystanie z wielu sposobów, takich jak sesje z krótkich okresów, wiadomości e-mail, spotkania "jeden do jednego", banery i miejscowości.

Na podstawie strategii komunikacji wybranej dla aplikacji możesz przypominać użytkownikom o oczekującym przestoju. Należy również sprawdzić, czy nie ma najnowszych zmian ani wpływu na działalność biznesową, które wymagałyby odroczenia wdrożenia.

W poniższej tabeli znajdziesz minimalną sugerowaną komunikację, aby informować uczestników projektu:

**Fazy planu i strategia projektu:**

| Komunikacja      | Grupy odbiorców                                          |
| ------------------ | ------------------------------------------------- |
| Świadomość i wartość biznesowa/techniczna projektu | Wszyscy z wyjątkiem użytkowników końcowych |
| Nagabędanie do aplikacji pilotażowych | — Właściciele firm aplikacji<br />— Właściciele techniczna aplikacji<br />— Architekci i zespół tożsamości |

**Faza 1 — odnajdywanie i zakres:**

| Komunikacja      | Grupy odbiorców                                          |
| ------------------ | ------------------------------------------------- |
| — Nagabędanie o informacje o aplikacji<br />— Wynik ćwiczenia z zakresu | — Właściciele techniczna aplikacji<br />— Właściciele firm aplikacji |

**Faza 2 — klasyfikowanie aplikacji i planowanie pilotażu:**

| Komunikacja      | Grupy odbiorców                                          |
| ------------------ | ------------------------------------------------- |
| - Wynik klasyfikacji i co to oznacza dla harmonogramu migracji<br />— Wstępny harmonogram migracji | — Właściciele techniczna aplikacji<br /> — Właściciele firm aplikacji |

**Faza 3 — planowanie migracji i testowania:**

| Komunikacja      | Grupy odbiorców                                          |
| ------------------ | ------------------------------------------------- |
| — Wynik testowania migracji aplikacji | — Właściciele techniczna aplikacji<br />— Właściciele firm aplikacji |
| — Powiadomienie o zbliżających się migracjach i wyjaśnienie wynikowych doświadczeń użytkowników końcowych.<br />— Nadchodzące przestoje i pełne komunikaty, w tym informacje o tym, co powinni teraz zrobić, opinie i jak uzyskać pomoc | — Użytkownicy końcowi (i wszystkie inne) |

**Faza 4 — zarządzanie i uzyskanie szczegółowych informacji:**

| Komunikacja      | Grupy odbiorców                                          |
| ------------------ | ------------------------------------------------- |
| Dostępna analiza i sposób uzyskiwania dostępu | — Właściciele techniczna aplikacji<br />— Właściciele firm aplikacji |

### <a name="migration-states-communication-dashboard"></a>Pulpit nawigacyjny komunikacji stanów migracji

Informowanie o ogólnym stanie projektu migracji ma kluczowe znaczenie, ponieważ pokazuje postęp, i pomaga właścicielom aplikacji, których aplikacje nadchodzą do migracji, w przygotowaniu się do przeniesienia. Możesz utworzyć prosty pulpit nawigacyjny przy użyciu Power BI lub innych narzędzi do raportowania, aby zapewnić wgląd w stan aplikacji podczas migracji.

Stany migracji, których można użyć, są następujące:

| Stany migracji       | Plan działania                                   |
| ---------------------- | --------------------------------------------- |
| **Żądanie początkowe** | Znajdź aplikację i skontaktuj się z właścicielem, aby uzyskać więcej informacji |
| **Ocena ukończona** | Właściciel aplikacji ocenia wymagania aplikacji i zwraca ankiety aplikacji</td>
| **Konfiguracja w toku** | Opracowywanie zmian niezbędnych do zarządzania uwierzytelnianiem w usłudze Azure AD |
| **Konfiguracja testu powiodła się** | Ocena zmian i uwierzytelnianie aplikacji względem testowej dzierżawy usługi Azure AD w środowisku testowym |
| **Konfiguracja produkcyjna powiodła się** | Zmień konfiguracje, aby działały z produkcyjną dzierżawą usługi AD i oceniały uwierzytelnianie aplikacji w środowisku testowym |
| **Ukończenie/wylogowanie** | Wdrażanie zmian aplikacji w środowisku produkcyjnym i wykonywanie polecenia względem produkcyjnej dzierżawy usługi Azure AD |

Dzięki temu właściciele aplikacji będą wiedzieć, jaki jest harmonogram migracji i testowania aplikacji, gdy ich aplikacje są w stanie migracji, oraz jakie są wyniki innych aplikacji, które zostały już zmigrowane. Możesz również rozważyć udostępnienie właścicielom linków do bazy danych śledzenia usterek, aby właściciele mogli plikować i wyświetlać problemy dotyczące migrowanych aplikacji.

### <a name="best-practices"></a>Najlepsze rozwiązania

Poniżej przedstawiono historie sukcesu naszych klientów i partnerów oraz sugerowane najlepsze rozwiązania:

- [Pięć porad dotyczących usprawnienia procesu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) migracji w celu Azure Active Directory firmy Patriot Consulting, członka naszej sieci partnerskiej, która koncentruje się na pomaganiu klientom w bezpiecznym wdrażaniu rozwiązań w chmurze firmy Microsoft.

- [Opracuj strategię zarządzania ryzykiem dla migracji](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) aplikacji usługi Azure AD, firmy Edgile, partnera, który koncentruje się na rozwiązaniach do zarządzania dostępem i dostępem i ryzykiem.

## <a name="phase-1-discover-and-scope-apps"></a>Faza 1. Odnajdywanie aplikacji i ich zakres

**Odnajdywanie i analiza aplikacji to podstawowe ćwiczenie, które daje dobry początek.** Być może nie znasz wszystkiego, więc przygotuj się do uwzględnienia nieznanych aplikacji.

### <a name="find-your-apps"></a>Znajdowanie aplikacji

Pierwszym punktem decyzyjnym migracji aplikacji jest to, które aplikacje mają zostać zmigrowane, które powinny pozostać i które aplikacje mają zostać wycofane. Zawsze istnieje możliwość wycofania aplikacji, których nie będziesz używać w organizacji. Istnieje kilka sposobów na znalezienie aplikacji w organizacji. **Podczas odnajdywania aplikacji upewnij się, że aplikacje są w trakcie opracowywania i planowane. Użyj usługi Azure AD do uwierzytelniania we wszystkich przyszłych aplikacjach.**

**Przy Active Directory Federation Services (AD FS) W celu zebrania prawidłowego spisu aplikacji:**

- **Używanie programu Azure AD Connect Health.** Jeśli masz licencję usługi Azure AD — wersja Premium, zalecamy wdrożenie usługi [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) analizowania użycia aplikacji w środowisku lokalnym. Raport aplikacji usług [AD FS](./migrate-adfs-application-activity.md) (wersja zapoznawcza) umożliwia odnajdywanie aplikacji usług AD FS, które można migrować, i ocenianie gotowości aplikacji do migracji. Po zakończeniu migracji należy wdrożyć [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) który umożliwia ciągłe monitorowanie rozwiązań w tle IT w organizacji po pracy w chmurze.

- **AD FS analizowania dzienników.** Jeśli nie masz licencji usługi Azure AD — wersja Premium, zalecamy użycie narzędzi migracji aplikacji usług AD FS do usługi Azure AD w oparciu o [program PowerShell.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration). Zapoznaj się z [przewodnikiem po rozwiązaniu:](./migrate-adfs-apps-to-azure.md)

[Migrowanie aplikacji z Active Directory Federation Services (AD FS) do usługi Azure AD.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Korzystanie z innych dostawców tożsamości

W przypadku innych dostawców tożsamości (takich jak Okta lub Ping) można użyć ich narzędzi do wyeksportowania spisu aplikacji. Warto rozważyć przeglądanie zasad usług zarejestrowanych w usłudze Active Directory, które odpowiadają aplikacjom internetowym w organizacji.

### <a name="using-cloud-discovery-tools"></a>Korzystanie z narzędzi do odnajdywania w chmurze

W środowisku chmury potrzebujesz rozbudowanego wglądu, kontroli nad podróżami danych oraz zaawansowanej analizy, aby znaleźć i znoczyć zagrożenia cybernetyczne we wszystkich usługach w chmurze. Spis aplikacji w chmurze można zebrać przy użyciu następujących narzędzi:

- **Cloud Access Security Broker (CASB)**— system [CASB](/cloud-app-security/) zazwyczaj współpracuje z zaporą w celu zapewnienia wglądu w użycie aplikacji w chmurze pracowników i pomaga chronić dane firmowe przed zagrożeniami cyberbezpieczeństwa. Raport CASB może pomóc w ustaleniu najczęściej używanych aplikacji w organizacji oraz wczesnych celów migracji do usługi Azure AD.

- **Cloud Discovery** — konfigurując aplikacje [Cloud Discovery,](/cloud-app-security/set-up-cloud-discovery)zyskujesz wgląd w użycie aplikacji w chmurze i możesz odnajdywać niezaakcjonowane lub niezaakcjonowane aplikacje IT.

- **Interfejsy API** — w przypadku aplikacji połączonych z infrastrukturą chmury możesz użyć interfejsów API i narzędzi w tych systemach, aby rozpocząć spis hostowanych aplikacji. W środowisku platformy Azure:

  - Użyj polecenia cmdlet [Get-AzureWebsite,](/powershell/module/servicemanagement/azure.service/get-azurewebsite) aby uzyskać informacje o witrynach internetowych platformy Azure.

  - Użyj polecenia cmdlet [Get-AzureRMWebApp,](/powershell/module/azurerm.websites/get-azurermwebapp) aby uzyskać informacje o usłudze Azure Web Apps.
D
  - Wszystkie aplikacje działające w usługach Microsoft IIS można znaleźć w wierszu polecenia systemu Windows przy [ użyciuAppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Użyj [aplikacji](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) i [jednostki usługi,](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) aby uzyskać informacje o aplikacji i wystąpieniu aplikacji w katalogu w usłudze Azure AD.

### <a name="using-manual-processes"></a>Używanie procesów ręcznych

Po użyciu zautomatyzowanych metod opisanych powyżej będziesz mieć dobrą obsługę aplikacji. Można jednak rozważyć wykonanie następujących czynności, aby zapewnić dobre pokrycie we wszystkich obszarach dostępu użytkowników:

- Skontaktuj się z różnymi właścicielami biznesowymi w twojej organizacji, aby znaleźć aplikacje, które są w użyciu w Twojej organizacji.

- Uruchom narzędzie inspekcji HTTP na serwerze proxy lub przeanalizuj dzienniki serwera proxy, aby sprawdzić, gdzie ruch jest często przekierowyny.

- Przejrzyj blogi z popularnych witryn portalu firmy, aby zobaczyć, do jakich linków użytkownicy mają najwięcej dostępu.

- Skons out to executives or other key business members to ensure you have covered the business-critical apps.

### <a name="type-of-apps-to-migrate"></a>Typ aplikacji do migracji

Po odnalezieniu aplikacji rozpoznasz następujące typy aplikacji w organizacji:

- Aplikacje, które już używają nowoczesnych protokołów uwierzytelniania

- Aplikacje, które korzystają ze starszych protokołów uwierzytelniania, które chcesz zmodernizować

- Aplikacje, które używają starszych protokołów uwierzytelniania, których nie chcesz modernizować

- Nowe aplikacje biznesowe

### <a name="apps-that-use-modern-authentication-already"></a>Aplikacje, które już używają nowoczesnego uwierzytelniania

Już zmodernizowane aplikacje najprawdopodobniej zostaną przeniesione do usługi Azure AD. Te aplikacje już używają nowoczesnych protokołów uwierzytelniania (takich jak SAML lub OpenID Connect) i można je ponownie skonfigurować do uwierzytelniania w usłudze Azure AD.

Oprócz opcji dostępnych w galerii aplikacji usługi Azure AD mogą to być aplikacje, które już istnieją w [organizacji,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) lub aplikacje innych firm od dostawcy, który nie należy do galerii usługi Azure AD (aplikacje spoza galerii).[](./add-application-portal.md)

Starsze aplikacje, które chcesz zmodernizować

W przypadku starszych aplikacji, które chcesz zmodernizować, przejście do usługi Azure AD w celu uwierzytelniania [](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) podstawowego i [](https://www.microsoft.com/security/operations/intelligence?rtc=1) autoryzacji odblokowuje wszystkie możliwości i możliwości danych, które Microsoft Graph i Intelligent Security Graph mają do zaoferowania.

Zalecamy **zaktualizowanie** kodu stosu uwierzytelniania dla tych aplikacji ze starszego protokołu (takiego jak uwierzytelnianie Windows-Integrated, ograniczone delegowanie Kerberos, uwierzytelnianie oparte na nagłówkach HTTP) do nowoczesnego protokołu (takiego jak SAML lub OpenID Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Starsze aplikacje, których nie chcesz modernizować

W przypadku niektórych aplikacji korzystających ze starszych protokołów uwierzytelniania czasami modernizacja ich uwierzytelniania nie jest właściwym zastosowaniem ze względów biznesowych. Należą do nich następujące typy aplikacji:

- Aplikacje przechowywane lokalnie ze względu na zgodność lub kontrolę.

- Aplikacje połączone z lokalną tożsamością lub dostawcą federacyjną, których nie chcesz zmieniać.

- Aplikacje opracowane przy użyciu lokalnych standardów uwierzytelniania, których nie planujesz przenosić

Usługa Azure AD może przynieść ogromne korzyści tym starszym aplikacjom, ponieważ można włączyć nowoczesne funkcje zabezpieczeń i ładu usługi [](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) Azure AD, takie jak [Multi-Factor Authentication,](../authentication/concept-mfa-howitworks.md) [dostęp](../conditional-access/overview.md)warunkowy, [](../identity-protection/index.yml)ochrona [tożsamości,](./access-panel-manage-self-service-access.md)delegowany dostęp do aplikacji i przeglądy dostępu dla tych aplikacji bez w ogóle dotykania aplikacji.

Zacznij  od rozszerzenia tych aplikacji do chmury za pomocą usługi Azure AD [serwer proxy aplikacji](./application-proxy-configure-single-sign-on-password-vaulting.md) przy użyciu prostych metod uwierzytelniania [](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) (takich jak usługa Magazynu haseł), aby szybko przeprowadzić migrację użytkowników, lub za pośrednictwem integracji naszych partnerów z kontrolerami dostarczania aplikacji, które mogły już zostać wdrożone.

### <a name="new-line-of-business-lob-apps"></a>Nowe aplikacje biznesowe

Zazwyczaj opracowywać aplikacje typu LoB do użytku w firmie. Jeśli masz nowe aplikacje w potoku, zalecamy użycie platformy [tożsamości firmy Microsoft](../develop/v2-overview.md) w celu zaimplementowania OpenID Connect.

### <a name="apps-to-deprecate"></a>Aplikacje do wycofania

Aplikacje bez jasnych właścicieli oraz przejrzystej konserwacji i monitorowania stanowić zagrożenie bezpieczeństwa dla organizacji. Rozważ wycofanie aplikacji, gdy:

- Ich **funkcje są wysoce nadmiarowe** w innych systemach • nie ma właściciela **firmy**

- nie ma wyraźnie **żadnego użycia**.

Zalecamy, aby nie oznaczać aplikacji o dużym znaczeniu krytycznym dla działalności firmy jako **przestarzałych.** W takich przypadkach należy współpracować z właścicielami biznesowymi, aby określić właściwą strategię.

### <a name="exit-criteria"></a>Kryteria zakończenia

W tej fazie udało Ci się pomyślnie:

- Dobre zrozumienie systemów w zakresie migracji (które można wycofać po przejściu do usługi Azure AD)

- Lista aplikacji, które obejmują:

  - Z jakimi systemami łączą się te aplikacje
  - Skąd i na jakich urządzeniach użytkownicy mają do nich dostęp
  - Określa, czy będą one migrowane, przestarzałe, czy połączone z [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

> [!NOTE]
> Arkusz [odnajdywania](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) aplikacji można pobrać, aby zarejestrować aplikacje, które mają zostać zmigrowane do uwierzytelniania usługi Azure AD, oraz te, które chcesz opuścić, ale zarządzać nimi przy użyciu Azure AD Connect [.](../hybrid/whatis-azure-ad-connect.md)

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Faza 2: Klasyfikowanie aplikacji i planowanie pilotażu

Klasyfikowanie migracji aplikacji jest ważnym ćwiczeniem. Nie każda aplikacja musi zostać zmigrowana i przeniesiona w tym samym czasie. Po zebrania informacji o poszczególnych aplikacjach można zracjonalizować, które aplikacje powinny zostać zmigrowane jako pierwsze, a które mogą zająć dodatkowy czas.

### <a name="classify-in-scope-apps"></a>Klasyfikowanie aplikacji w zakresie

Jednym ze sposobów myślenia o tym jest wzdłuż osi krytycznego dla działania firmy, użycia i cyklu życia, z których każda jest zależna od wielu czynników.

### <a name="business-criticality"></a>Znaczenie biznesowe

Krytyczne znaczenie biznesowe będzie mieć różne wymiary dla każdej firmy, ale dwie miary, które należy wziąć pod uwagę, to funkcje, **funkcje** i **profile użytkowników.** Przypisz aplikacjom o unikatowej funkcjonalności wyższą wartość punktu niż aplikacje z nadmiarową lub przestarzałą funkcjonalnością.

![Diagram przedstawiający spektrum funkcji & funkcjonalności i profilów użytkowników](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Użycie

Aplikacje o **wysokim użyciu powinny** otrzymać wyższą wartość niż aplikacje o niskim użyciu. Przypisz wyższą wartość do aplikacji z użytkownikami zewnętrznymi, wykonawczymi lub z zespołu ds. zabezpieczeń. Dla każdej aplikacji w portfolio migracji wykonaj te oceny.

![Diagram spektrum woluminu użytkownika i szerokości użytkownika](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Po określeniu wartości krytycznych dla działania firmy i użycia można określić cykl życia aplikacji i utworzyć macierz priorytetów. Zobacz jedną z takich macierzy poniżej:

![Diagram trójkąta przedstawiający relacje między użyciem, oczekiwanym cyklem życia i krytycznością działania firmy](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Określanie priorytetów aplikacji do migracji

Możesz rozpocząć migrację aplikacji od aplikacji o najniższym priorytecie lub aplikacji o najwyższym priorytecie w zależności od potrzeb organizacji.

W scenariuszu, w którym możesz nie mieć doświadczenia w używaniu usług Azure AD i Identity, rozważ najpierw przeniesienie aplikacji o najniższym priorytecie **do** usługi Azure AD. Spowoduje to zminimalizowanie wpływu na działalność biznesową i pozwoli na rozwój. Po pomyślnym przenoszeniu tych aplikacji i zyskaniu pewności uczestnika projektu można kontynuować migrację innych aplikacji.

Jeśli nie ma wyraźnego priorytetu, należy najpierw rozważyć przeniesienie aplikacji, które znajdują się w galerii usługi [Azure AD,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) i obsługę wielu dostawców tożsamości (ADFS lub Okta), ponieważ są one łatwiejsze do zintegrowania. Prawdopodobnie są to aplikacje o **najwyższym priorytecie** w organizacji. Aby ułatwić integrację aplikacji SaaS z usługą Azure AD, opracowaliśmy kolekcję [samouczków,](../saas-apps/tutorial-list.md) które ułatwiają konfigurowanie.

Po upływie ostatecznego terminu migracji aplikacji te zasobniki o najwyższym priorytecie przejmą główne obciążenie. Możesz ostatecznie wybrać aplikacje o niższym priorytecie, ponieważ nie zmienią one kosztu, nawet jeśli termin ostateczny został przeniesiony. Nawet jeśli musisz odnowić licencję, będzie ona za niewielką kwotę.

Oprócz tej klasyfikacji i w zależności od pilności migracji można również  rozważyć zaplanowanie migracji, w ramach którego właściciele aplikacji muszą zaangażować się w migrację swoich aplikacji. Po zakończeniu tego procesu powinna zostać wyświetlona lista wszystkich aplikacji w zasobnikach z priorytetami migracji.

### <a name="document-your-apps"></a>Dokumentować aplikacje

Najpierw należy rozpocząć od zebrania kluczowych informacji o aplikacjach. Arkusz [odnajdywania aplikacji](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)pomoże Ci szybko podjąć decyzje dotyczące migracji i uzyskać zalecenie do grupy biznesowej w żadnym momencie.

Informacje, które są ważne podczas podejmowania decyzji dotyczącej migracji, obejmują:

- **Nazwa aplikacji** — jak ta aplikacja jest znana firmie?

- **Typ aplikacji** — czy jest to aplikacja SaaS innej firmy? Niestandardowa aplikacja internetowa biznesowa? Interfejs API?

- **Krytyczne znaczenie dla działania** firmy — czy ma ona wysoki poziom krytyczności? Niskie? Czy gdzieś pomiędzy nimi?

- **Wolumin dostępu użytkowników** — czy wszyscy mają dostęp do tej aplikacji, czy tylko kilka osób?

- **Planowany cykl** życia — jak długo będzie się ona czekać? Mniej niż sześć miesięcy? Więcej niż dwa lata?

- **Bieżący dostawca tożsamości** — jaki jest podstawowy dostawca tożsamości dla tej aplikacji? Czy może korzysta z magazynu lokalnego?

- **Metoda uwierzytelniania** — czy aplikacja jest uwierzytelniana przy użyciu otwartych standardów?

- **Czy zamierzasz zaktualizować kod aplikacji** — czy aplikacja jest planowana, czy aktywna?

- **Czy planujesz zachować aplikację** lokalnie — czy chcesz przechowywać aplikację w centrum danych w dłuższym okresie?

- **Czy aplikacja jest zależna od innych aplikacji, czy** interfejsów API — czy aplikacja aktualnie jest wywoływana do innych aplikacji lub interfejsów API?

- **Czy aplikacja znajduje się w galerii usługi Azure AD** — czy aplikacja jest już zintegrowana z [galerią usługi Azure AD?](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)

Inne dane, które będą pomocne później, ale nie trzeba podjąć natychmiastowej decyzji dotyczącej migracji, obejmują:

- **Adres URL aplikacji** — gdzie użytkownicy mogą uzyskać dostęp do aplikacji?

- **Opis aplikacji** — jaki jest krótki opis tego, co robi aplikacja?

- **Właściciel aplikacji** — kto w firmie jest głównym centrum pochyłu aplikacji?

- **Ogólne komentarze lub uwagi** — wszelkie inne ogólne informacje o aplikacji lub własności firmy

Po sklasyfikowania aplikacji i udokumentowaniu szczegółów pamiętaj o wykupienie właściciela firmy w ramach strategii planowanej migracji.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Aplikacje wybrane na potrzeby pilotażu powinny reprezentować kluczowe wymagania organizacji dotyczące tożsamości i zabezpieczeń, a właściciel aplikacji musi wyraźnie wykupować aplikację. Pilotaż zwykle działa w osobnym środowisku testowym. Zobacz [najlepsze rozwiązania dotyczące pilotażu](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) na stronie planów wdrażania.

**Nie zapomnij o partnerach zewnętrznych.** Upewnij się, że uczestniczą one w harmonogramach migracji i testach. Na koniec upewnij się, że mają one sposób uzyskiwania dostępu do pomocy technicznej, jeśli występują istotne problemy.

### <a name="plan-for-limitations"></a>Planowanie ograniczeń

Chociaż migracja niektórych aplikacji jest łatwa, inne mogą trwać dłużej ze względu na wiele serwerów lub wystąpień. Na przykład migracja programu SharePoint może trwać dłużej ze względu na niestandardowe strony logowania.

Wielu dostawców aplikacji SaaS nalicza opłaty za zmianę połączenia logowania jednokrotnego. Skontaktuj się z nimi i zaplanuj to.

Usługa Azure AD ma [również limity i ograniczenia usług,](../enterprise-users/directory-service-limits-restrictions.md) o których należy pamiętać.

### <a name="app-owner-sign-off"></a>Rejestracja właściciela aplikacji

Aplikacje krytyczne dla działania firmy i powszechnie używane mogą wymagać grupy użytkowników pilotażowych, aby przetestować aplikację na etapie pilotażu. Po przetestowania aplikacji w środowisku przedprodukcyjnym lub pilotażowym upewnij się, że właściciele firmy aplikacji wylogują się z wydajności przed migracją aplikacji i wszyscy użytkownicy do produkcyjnego użycia usługi Azure AD na potrzeby uwierzytelniania.

### <a name="plan-the-security-posture"></a>Planowanie postawy zabezpieczeń

Zanim zainicjujesz proces migracji, pomiń trochę czasu, aby w pełni rozważyć poziom zabezpieczeń, który chcesz opracować dla firmowego systemu tożsamości. Jest to oparte na zbieraniu cennych zestawów informacji: tożsamości, urządzeń i lokalizacji, które **mają dostęp do danych.**

### <a name="identities-and-data"></a>Tożsamości i dane

Większość organizacji ma określone wymagania dotyczące tożsamości i ochrony danych, które różnią się w zależności od segmentu branży i funkcji zadań w organizacji. Zapoznaj się [z konfiguracjami tożsamości i dostępu do](/microsoft-365/enterprise/microsoft-365-policies-configurations) urządzeń, aby uzyskać rekomendacje, w tym określony zestaw zasad dostępu [warunkowego i](../conditional-access/overview.md) powiązane możliwości.

Tych informacji można użyć do ochrony dostępu do wszystkich usług zintegrowanych z usługą Azure AD. Te zalecenia są zgodne z wynikiem bezpieczeństwa firmy Microsoft i wynikiem [tożsamości w usłudze Azure AD.](../fundamentals/identity-secure-score.md) Ten wskaźnik ułatwi Ci:

- Obiektywne mierzenie poziomu bezpieczeństwa tożsamości

- Planowanie poprawy bezpieczeństwa tożsamości

- Sprawdzanie powodzenia wprowadzonych udoskonaleń

Pomoże to również zaimplementować pięć [kroków zabezpieczania infrastruktury tożsamości.](../../security/fundamentals/steps-secure-identity.md) Skorzystaj ze wskazówek jako punktu wyjścia dla swojej organizacji i dostosuj zasady, aby spełnić określone wymagania organizacji.

### <a name="who-is-accessing-your-data"></a>Kto uzyskuje dostęp do danych?

Istnieją dwie główne kategorie użytkowników aplikacji i zasobów, które obsługuje usługa Azure AD:

- **Wewnętrzne:** Pracownicy, wykonawcy i dostawcy, którzy mają konta w ramach dostawcy tożsamości. Może to wymagać dalszych przestawnych zasad dla menedżerów lub kierownictwa w porównaniu z innymi pracownikami.

- **Zewnętrzne:** Dostawcy, dostawcy, dystrybutorzy lub inni partnerzy biznesowi, którzy w regularnych odstępach czasu wchodzą w interakcje z Twoją organizacją dzięki współpracy [B2B](../external-identities/what-is-b2b.md) w usłudze Azure AD.

Grupy dla tych użytkowników można definiować i wypełniać je na różne sposoby. Możesz zdecydować, że administrator musi ręcznie dodać członków do grupy lub włączyć samoobsługowe członkostwo w grupie. Można określić reguły, które automatycznie dodają członków do grup na podstawie określonych kryteriów przy użyciu [grup dynamicznych.](../enterprise-users/groups-dynamic-membership.md)

Użytkownicy zewnętrzni mogą również odwoływać się do klientów. [Azure AD B2C](../../active-directory-b2c/overview.md), oddzielny produkt obsługuje uwierzytelnianie klientów. Jednak jest on poza zakresem tego dokumentu.

### <a name="devicelocation-used-to-access-data"></a>Urządzenie/lokalizacja używane do uzyskiwania dostępu do danych

Ważne jest również urządzenie i lokalizacja, których użytkownik używa do uzyskiwania dostępu do aplikacji. Urządzenia fizycznie połączone z siecią firmową są bezpieczniejsze. Połączenia spoza sieci za pośrednictwem sieci VPN mogą wymagać kontroli.

![Diagram przedstawiający relację między lokalizacją użytkownika i dostępem do danych](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Mając na uwadze te aspekty zasobów, użytkowników i urządzeń, możesz użyć funkcji [dostępu warunkowego usługi Azure AD.](../conditional-access/overview.md) Dostęp warunkowy wykracza poza uprawnienia użytkownika: opiera się na kombinacji czynników, takich jak tożsamość użytkownika lub grupy, sieć, z która użytkownik jest połączony, urządzenie i aplikacja, z których korzysta, oraz typ danych, do których próbuje uzyskać dostęp. Dostęp udzielany użytkownikowi dostosowuje się do tego szerszego zestawu warunków.

### <a name="exit-criteria"></a>Kryteria zakończenia

Ta faza jest pomyślna, jeśli:

- Poznasz swoje aplikacje
  - W pełni udokumentowano aplikacje, które mają być migrowane
  - Określanie priorytetów aplikacji na podstawie krytyczności dla działania firmy, ilości użycia i cyklu życia

- Wybrano aplikacje, które reprezentują twoje wymagania dotyczące pilotażu

- Włoź właściciela firmy do priorytetów i strategii

- Zrozumienie potrzeb w zakresie bezpieczeństwa i sposobu ich implementowanie

## <a name="phase-3-plan-migration-and-testing"></a>Faza 3: Planowanie migracji i testowania

Po zakupieniu konta biznesowego następnym krokiem jest rozpoczęcie migracji tych aplikacji do uwierzytelniania w usłudze Azure AD.

### <a name="migration-tools-and-guidance"></a>Narzędzia i wskazówki dotyczące migracji

Użyj poniższych narzędzi i wskazówek, aby wykonać dokładne kroki wymagane do zmigrowania aplikacji do usługi Azure AD:

- **Ogólne wskazówki dotyczące** migracji — skorzystaj z oficjalnych dokumentów, narzędzi, szablonów wiadomości e-mail i aplikacji w zestawie narzędzi do migracji aplikacji usługi [Azure AD,](./migration-resources.md) aby odnajdywać, klasyfikować i migrować aplikacje.

- **Aplikacje SaaS** — zapoznaj się z naszą listą setek samouczków aplikacji [SaaS](../saas-apps/tutorial-list.md) i kompletnym planem wdrożenia logowania jednokrotnego usługi [Azure AD,](https://aka.ms/ssodeploymentplan) aby przejść przez cały proces.

- **Aplikacje działające lokalnie —** dowiedz się wszystkiego o usłudze [Azure AD serwer proxy aplikacji](./application-proxy.md) i użyj pełnego planu wdrożenia usługi Azure AD [serwer proxy aplikacji,](https://aka.ms/AppProxyDPDownload) aby szybko rozpocząć.

- **Aplikacje, które opracowujesz** — przeczytaj nasze szczegółowe wskazówki dotyczące integracji [i](../develop/quickstart-register-app.md) [rejestracji.](../develop/quickstart-register-app.md)

Po migracji można wysłać wiadomość z informacjami o pomyślnym wdrożeniu i przypomnieć im o wszelkich nowych krokach, które należy wykonać.

### <a name="plan-testing"></a>Planowanie testowania

Podczas migracji aplikacja może już mieć środowisko testowe używane podczas regularnych wdrożeń. Możesz nadal używać tego środowiska do testowania migracji. Jeśli środowisko testowe nie jest obecnie dostępne, może być możliwe skonfigurowanie go przy użyciu programu Azure App Service lub usługi Azure Virtual Machines, w zależności od architektury aplikacji. Możesz skonfigurować oddzielną dzierżawę testów usługi Azure AD do użycia podczas opracowywania konfiguracji aplikacji. Ta dzierżawa rozpocznie się w stanie czystym i nie zostanie skonfigurowana do synchronizacji z żadnym systemem.

Każdą aplikację można przetestować, logując się przy użyciu użytkownika testowego i upewniać się, że wszystkie funkcje są takie same jak przed migracją. Jeśli podczas testowania określisz, że użytkownicy będą musieli zaktualizować ustawienia usługi [MFA](/azure/active-directory/authentication/howto-mfa-userstates) lub [funkcji SSPR](../authentication/tutorial-enable-sspr.md)lub dodajesz tę funkcję podczas migracji, pamiętaj o dodaniu tej funkcji do planu komunikacji użytkownika końcowego. Zobacz [MfA](https://aka.ms/mfatemplates) and SSPR end-user communication templates (Szablony komunikacji użytkownika końcowego usługi MFA i funkcji [SSPR).](https://aka.ms/ssprtemplates)

Po zmigrowanych aplikacjach przejdź [](https://aad.portal.azure.com/) do Azure Portal, aby sprawdzić, czy migracja się udało. Postępuj zgodnie z poniższymi instrukcjami:

- Wybierz **pozycję Aplikacje dla przedsiębiorstw &gt; Wszystkie** aplikacje i znajdź swoją aplikację na liście.

- Wybierz **pozycję &gt; Zarządzaj użytkownikami i grupami,** aby przypisać do aplikacji co najmniej jednego użytkownika lub grupę.

- Wybierz **pozycję Zarządzaj &gt; dostępem warunkowym.** Przejrzyj listę zasad i upewnij się, że nie blokujesz dostępu do aplikacji przy użyciu [zasad dostępu warunkowego.](../conditional-access/overview.md)

W zależności od sposobu konfigurowania aplikacji sprawdź, czy logowanie jednokrotne działa prawidłowo.

| Typ uwierzytelniania      | Testowanie                                             |
| ------------------------ | --------------------------------------------------- |
| **Uwierzytelnianie OAuth/OpenID Connect** | Wybierz **pozycję &gt; Aplikacje dla** przedsiębiorstw Uprawnienia i upewnij się, że aplikacja ma być używana w organizacji w ustawieniach użytkownika aplikacji. |
| **Logowanie jednokrotne oparte na języku SAML** | Użyj [przycisku Testuj ustawienia saml](./debug-saml-sso-issues.md) w obszarze **Logowanie pojedynczej.** |
| **Logowanie jednokrotne oparte na hasłach** | Pobierz i zainstaluj rozszerzenie bezpiecznego logowania [myApps.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) To rozszerzenie ułatwia uruchamianie dowolnych aplikacji w chmurze organizacji, które wymagają użycia procesu logowania jednokrotnego. |

| **[serwer proxy aplikacji](./application-proxy.md)** | Upewnij się, że łącznik jest uruchomiony i przypisany do aplikacji. Odwiedź stronę [serwer proxy aplikacji rozwiązywania problemów, aby](./application-proxy-troubleshoot.md) uzyskać dalszą pomoc. |

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli występują problemy, zapoznaj się z naszym przewodnikiem rozwiązywania problemów z [aplikacjami,](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) aby uzyskać pomoc. Możesz również zapoznaj się z naszymi artykułami na temat rozwiązywania problemów. Zobacz Problemy z logowaniem do aplikacji skonfigurowanych do logowania się za pomocą [logowania pojedynczego opartego na saml.](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)

### <a name="plan-rollback"></a>Wycofywanie planu

Jeśli migracja zakończy się niepowodzeniem, najlepszą strategią jest wycofywanie i testowanie. Poniżej podano kroki, które można wykonać, aby rozwiązać problemy z migracją:

- **Zrób zrzuty** ekranu z istniejącą konfiguracją aplikacji. Możesz zajrzeć wstecz, jeśli musisz ponownie skonfigurować aplikację.

- Możesz również rozważyć udostępnienie **linków do starszego uwierzytelniania,** jeśli występują problemy z uwierzytelnianiem w chmurze.

- Przed ukończeniem migracji nie **zmieniaj istniejącej konfiguracji za** pomocą wcześniejszego dostawcy tożsamości.

- Rozpocznij od migracji **aplikacji, które obsługują wielu idPs.** Jeśli coś pójdzie nie tak, zawsze możesz przejść do konfiguracji preferowanego identyfikatora.

- Upewnij się, że środowisko aplikacji ma **przycisk Opinii** lub wskaźniki do problemów z **pomocą techniczną.**

### <a name="exit-criteria"></a>Kryteria zakończenia

Ta faza jest pomyślna, jeśli masz:

- Określono sposób migrowania poszczególnych aplikacji

- Przegląd narzędzi migracji

- Zaplanowano testowanie, w tym środowiska testowe i grupy

- Planowane wycofywanie

## <a name="phase-4-plan-management-and-insights"></a>Faza 4. Planowanie zarządzania i szczegółowe informacje

Po migracji aplikacji należy upewnić się, że:

- Użytkownicy mogą bezpiecznie uzyskać dostęp do aplikacji i zarządzać nimi

- Możesz uzyskać odpowiednie szczegółowe informacje na temat użycia i kondycji aplikacji

Zalecamy podjęcie następujących działań zgodnie z potrzebami Twojej organizacji.

### <a name="manage-your-users-app-access"></a>Zarządzanie dostępem do aplikacji użytkowników

Po zmigrowanych aplikacjach możesz na wiele sposobów wzbogacić środowisko użytkownika

**Spraw, aby aplikacje można było odnajdywać**

**Wskaż użytkownikowi** środowisko [portalu MyApps.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) W tym miejscu mogą uzyskać dostęp do wszystkich aplikacji opartych na chmurze, aplikacji dostępnych przy użyciu usługi [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)i aplikacji przy użyciu usługi [serwer proxy aplikacji](./application-proxy.md) pod warunkiem, że mają uprawnienia dostępu do tych aplikacji.


Możesz pokierować użytkowników, jak odnajdywać ich aplikacje:

- Użyj [istniejącej funkcji logowania pojedynczego,](./view-applications-portal.md) aby **połączyć użytkowników z dowolną aplikacją**


- Włączanie [dostępu do aplikacji samoobsługowej](./manage-self-service-access.md)dla aplikacji i **umożliwianie użytkownikom dodawania aplikacji, które są przez Ciebie wytwarzane**

- [Ukryj aplikacje przed użytkownikami końcowi](./hide-application-from-user-portal.md) (domyślne aplikacje firmy Microsoft lub inne aplikacje), aby **aplikacje, których potrzebują, były bardziej wykrywalne**

### <a name="make-apps-accessible"></a>Udostępnij aplikacje

**Umożliwia użytkownikom dostęp do aplikacji z urządzeń przenośnych.** Użytkownicy mogą uzyskać dostęp do portalu MyApps za pomocą przeglądarki zarządzanej przez usługę Intune na urządzeniach z systemem [iOS](./hide-application-from-user-portal.md) 7.0 lub nowszym lub [Android.](./hide-application-from-user-portal.md)

Użytkownicy mogą pobrać przeglądarkę **zarządzaną przez usługę Intune:**

- **W przypadku urządzeń z systemem Android** ze [sklepu Google Play](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- **W przypadku urządzeń** [](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) firmy Apple można pobrać App Store apple lub pobrać Moje aplikacje aplikację [mobilną dla systemu iOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Umożliwia użytkownikom otwieranie aplikacji z rozszerzenia przeglądarki.**

Użytkownicy mogą pobrać rozszerzenie bezpiecznego logowania [MyApps](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) w przeglądarce [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [FireFox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) [lub Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) uruchamiać aplikacje bezpośrednio na pasku przeglądarki, aby:

- **Wyszukiwanie aplikacji i pojawianie się ostatnio używanych aplikacji**

- **Automatyczne konwertowanie wewnętrznych adresów URL** skonfigurowanych w [serwer proxy aplikacji](./application-proxy.md) na odpowiednie zewnętrzne adresy URL. Użytkownicy mogą teraz korzystać z linków, które znają, niezależnie od tego, gdzie się znajdują.

**Umożliwianie użytkownikom otwierania aplikacji z Office.com.**

Użytkownicy mogą przejść do  [Office.com,](https://www.office.com/) aby wyszukać swoje aplikacje, a ich ostatnio używane aplikacje będą dla nich wyświetlane bezpośrednio z miejsca, w którym pracują.

### <a name="secure-app-access"></a>Bezpieczny dostęp do aplikacji

Usługa Azure AD zapewnia scentralizowaną lokalizację dostępu do zarządzania zmigrowanych aplikacji. Przejdź do [Azure Portal](https://portal.azure.com/) i włącz następujące możliwości:

- **Zabezpieczanie dostępu użytkowników do aplikacji.** Włącz [zasady dostępu warunkowego lub](../conditional-access/overview.md) [usługę Identity Protection,](../identity-protection/overview-identity-protection.md)aby zabezpieczyć dostęp użytkowników do aplikacji na podstawie stanu urządzenia, lokalizacji i nie tylko.

- **Automatyczna aprowizowanie.** Skonfiguruj [automatyczną aprowizowanie użytkowników za](../app-provisioning/user-provisioning.md) pomocą różnych aplikacji SaaS innych firm, do których użytkownicy muszą uzyskać dostęp. Oprócz tworzenia tożsamości użytkowników obejmuje to obsługę i usuwanie tożsamości użytkowników w przypadku zmiany stanu lub ról.

- **Delegowanie zarządzania dostępem** **użytkowników.** W razie potrzeby włącz dostęp aplikacji samoobsługowej do aplikacji i przypisz do nich osoby zatwierdzającej w firmie, aby *zatwierdzić dostęp do tych aplikacji.* Samoobsługowe [zarządzanie grupami jest przeznaczone](../enterprise-users/groups-self-service-management.md)dla grup przypisanych do kolekcji aplikacji.

- **Delegowanie dostępu administratora.** **przypisywanie** użytkownikowi roli administratora (takiej jak administrator aplikacji, Cloud administrator aplikacji lub deweloper aplikacji).

### <a name="audit-and-gain-insights-of-your-apps"></a>Inspekcja i szczegółowe informacje o aplikacjach

Możesz również użyć tej [Azure Portal](https://portal.azure.com/) do inspekcji wszystkich aplikacji ze scentralizowanej lokalizacji.

- **Przejmij inspekcję** aplikacji przy użyciu funkcji **Aplikacje dla przedsiębiorstw, Inspekcja lub uzyskaj dostęp do tych samych informacji z interfejsu API raportowania usługi [Azure AD,](../reports-monitoring/concept-reporting-api.md) aby zintegrować ją z ulubionymi narzędziami.

- **Wyświetl uprawnienia dla aplikacji przy użyciu aplikacji** dla **przedsiębiorstw,** uprawnienia dla aplikacji korzystających z protokołu OAuth/OpenID Connect.

- **Uzyskaj szczegółowe informacje dotyczące logowania przy** użyciu aplikacji dla przedsiębiorstw i **logowania.** Uzyskaj dostęp do tych samych informacji z interfejsu [API raportowania usługi Azure AD.](../reports-monitoring/concept-reporting-api.md)

- **Wizualizowanie użycia aplikacji z** pakietu [zawartości usługi Azure AD Power BI](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Kryteria zakończenia

Ta faza jest pomyślna w przypadku:

- Zapewnianie użytkownikom bezpiecznego dostępu do aplikacji

- Zarządzanie inspekcją i uzyskaniem szczegółowych informacji o zmigrowanych aplikacjach

### <a name="do-even-more-with-deployment-plans"></a>Jeszcze więcej działań dzięki planom wdrażania

Plany wdrażania przejdą przez proces wartości biznesowej, planowania, kroków implementacji i zarządzania rozwiązaniami usługi Azure AD, w tym scenariuszami migracji aplikacji. Łączy on wszystkie funkcje potrzebne do rozpoczęcia wdrażania i uzyskiwania korzyści z możliwości usługi Azure AD. Przewodniki wdrażania obejmują zawartość, taką jak zalecane przez firmę Microsoft najlepsze rozwiązania, komunikacja z użytkownikami końcowi, przewodniki planowania, kroki implementacji, przypadki testowe i inne.

Wiele [planów wdrażania](../fundamentals/active-directory-deployment-plans.md) jest dostępnych do użycia, a my zawsze staramy się jeszcze więcej.

### <a name="contact-support"></a>Kontakt z pomocą techniczną

Odwiedź następujące linki do pomocy technicznej, aby utworzyć lub śledzić bilet pomocy technicznej i monitorować kondycję.

- **pomoc techniczna platformy Azure:** Możesz wywołać tę [Pomoc techniczna Microsoft](https://azure.microsoft.com/support) i otworzyć bilet dla dowolnej platformy Azure

Problem z wdrażaniem tożsamości w zależności od Enterprise Agreement z firmą Microsoft.

- **FastTrack:** Jeśli zakupiono pakiet Enterprise Mobility and Security (EMS) lub Azure AD — wersja Premium licencji, masz uprawnienia do otrzymania pomocy dotyczącej wdrażania z [programu FastTrack.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- **Sangażuj zespół inżynierów produktu:** Jeśli pracujesz nad dużym wdrożeniem klientów z milionami użytkowników, masz prawo do pomocy technicznej od zespołu konto Microsoft lub zespołu architekci rozwiązań. W zależności od złożoności wdrożenia projektu możesz pracować bezpośrednio z zespołem inżynierów produktu [tożsamości platformy Azure.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Blog tożsamości usługi Azure AD:** Zasubskrybuj blog dotyczący tożsamości usługi [Azure AD,](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) aby być na bieżąco ze wszystkimi najnowszymi ogłoszeniami o produktach, szczegółowe informacje i informacje o planach udostępniane bezpośrednio przez zespół inżynierów ds. tożsamości.
