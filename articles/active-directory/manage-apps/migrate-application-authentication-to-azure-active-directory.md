---
title: Migrowanie uwierzytelniania aplikacji do Azure Active Directory
description: Ten oficjalny dokument zawiera szczegółowe informacje na temat planowania i korzystania z migracji aplikacji do usługi Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/05/2021
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e05a7af3f0b95470432b4fb9602e1b41da9f72f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952970"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Migrowanie uwierzytelniania aplikacji do Azure Active Directory

## <a name="about-this-paper"></a>Informacje o tym dokumencie

Ten oficjalny dokument zawiera szczegółowe informacje na temat planowania i korzystania z migracji aplikacji do usługi Azure AD. Jest ona przeznaczona dla administratorów platformy Azure i specjalistów ds. tożsamości.

Rozdzielenie tego procesu na cztery etapy, z których każdy ma szczegółowe kryteria planowania i wyjścia, jest przeznaczone do zaplanowania strategii migracji i zrozumienia, jak uwierzytelnianie usługi Azure AD obsługuje cele organizacji.

## <a name="introduction"></a>Wprowadzenie

Obecnie organizacja wymaga, aby użytkownicy mogli korzystać z aplikacji (aplikacji). W każdym dniu możesz nadal dodawać, opracowywać i wycofać aplikacje. Użytkownicy uzyskują dostęp do tych aplikacji z szerokiej gamy urządzeń firmowych i osobistych oraz lokalizacji. Aplikacje są otwierane na wiele sposobów, w tym:

- za pomocą strony głównej lub portalu firmy

- tworzenie zakładek w swoich przeglądarkach

- za pośrednictwem adresu URL dostawcy dla aplikacji oprogramowanie jako usługa (SaaS)

- linki wypychane bezpośrednio do komputerów stacjonarnych lub urządzeń przenośnych za pośrednictwem rozwiązania do zarządzania urządzeniami przenośnymi i aplikacjami (MDM/MAM)

Aplikacje mogą korzystać z następujących typów uwierzytelniania:

- Lokalne rozwiązania federacyjne (takie jak Active Directory Federation Services (ADFS) i polecenie ping

- Active Directory (na przykład uwierzytelnianie Kerberos i uwierzytelnianie Windows-Integrated)

- Inne rozwiązania do zarządzania tożsamościami i dostępem w chmurze (na przykład usługi okta lub Oracle)

- Lokalna infrastruktura sieci Web (np. usługi IIS i Apache)

- Infrastruktura hostowana w chmurze (na przykład Azure i AWS)

**Aby mieć pewność, że użytkownicy będą mogli łatwo i bezpiecznie uzyskiwać dostęp do aplikacji, celem jest posiadanie jednego zestawu kontroli dostępu i zasad w środowiskach lokalnych i w chmurze.**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) oferuje uniwersalną platformę do obsługi tożsamości, która umożliwia osobom, partnerom i klientom pojedynczą tożsamość dostęp do potrzebnych aplikacji oraz współpracę z dowolnego platformy i urządzenia.

![Diagram Azure Active Directory łączności](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Usługa Azure AD ma [pełen pakiet funkcji zarządzania tożsamościami](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad). Ujednolicenie uwierzytelniania aplikacji i autoryzacji do usługi Azure AD pozwala uzyskać korzyści oferowane przez te funkcje.

Więcej zasobów migracji można znaleźć pod adresem [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Zalety migrowania uwierzytelniania aplikacji do usługi Azure AD

Przeniesienie uwierzytelniania aplikacji do usługi Azure AD pomoże Ci zarządzać ryzykiem i kosztem, zwiększyć produktywność i spełnić wymagania dotyczące zgodności i zarządzania.

### <a name="manage-risk"></a>Zarządzanie ryzykiem

Ochrona aplikacji wymaga pełnego wglądu w wszystkie czynniki ryzyka. Migrowanie aplikacji do usługi Azure AD konsoliduje rozwiązania zabezpieczeń. Dzięki niej można:

- Ulepszaj bezpieczny dostęp użytkowników do aplikacji i skojarzonych danych firmowych przy użyciu [zasad dostępu warunkowego](../conditional-access/overview.md), [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)i technologii [ochrony tożsamości](../identity-protection/overview-identity-protection.md) opartej na ryzyku w czasie rzeczywistym.

- Ochrona dostępu uprzywilejowanego użytkownika do środowiska za pomocą dostępu administratora [just in Time](../../azure-resource-manager/managed-applications/request-just-in-time-access.md) .

- Korzystaj z [wielodostępnego, geograficznie rozproszonego projektu usługi Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)w celu zapewnienia najbardziej krytycznych potrzeb firmy.

- Chroń starsze aplikacje, korzystając z jednej z naszych [bezpiecznych integracji partnera dostępu hybrydowego](https://aka.ms/secure-hybrid-access) , które mogły już zostać wdrożone.

### <a name="manage-cost"></a>Zarządzanie kosztami

Organizacja może mieć wiele rozwiązań do zarządzania dostępem do tożsamości (IAM). Migrowanie do jednej infrastruktury usługi Azure AD umożliwia zredukowanie zależności od licencji na usługę IAM (lokalnie lub w chmurze) oraz kosztów infrastruktury. W przypadkach, gdy można już zapłacić za usługę Azure AD za pośrednictwem licencji Microsoft 365, nie ma powodów, aby płacić za dodatkowe rozwiązanie IAM.

**Za pomocą usługi Azure AD możesz zmniejszyć koszty infrastruktury, wykonując następujące polecenie:**

- Zapewnianie bezpiecznego dostępu zdalnego do aplikacji lokalnych przy użyciu [usługi Azure serwer proxy aplikacji usługi Azure AD](./application-proxy.md).

- Oddzielanie aplikacji od metody Premium Credential w dzierżawie przez [skonfigurowanie usługi Azure AD jako zaufanego dostawcy tożsamości uniwersalnej](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization).

### <a name="increase-productivity"></a>Zwiększ produktywność

Korzyści związane z ekonomią i zabezpieczeniami umożliwiają wdrażanie usługi Azure AD, ale pełne wdrożenie i zgodność są bardziej korzystne, jeśli korzyści użytkowników. Za pomocą usługi Azure AD można:

- Ulepszaj interfejs użytkownika końcowego [Sign-On (SSO)](./what-is-single-sign-on.md) dzięki bezproblemowemu i bezpiecznym dostępowi do dowolnej aplikacji z dowolnego urządzenia i lokalizacji.

- Korzystaj z samoobsługowych funkcji usługi IAM, takich jak [resetowanie haseł](../authentication/concept-sspr-howitworks.md) samoobsługowych i [Zarządzanie grupami samoobsługowy](../enterprise-users/groups-self-service-management.md).

- Zmniejszenie kosztów administracyjnych przez zarządzanie tylko jedną tożsamością dla każdego użytkownika w środowiskach w chmurze i lokalnych:

  - [Automatyzowanie aprowizacji](../app-provisioning/user-provisioning.md) kont użytkowników (w [galerii usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)) w oparciu o tożsamości usługi Azure AD
  - Dostęp do wszystkich aplikacji z poziomu panelu Moje aplikacje w [Azure Portal ](https://portal.azure.com/)

- Umożliwienie deweloperom zabezpieczania dostępu do aplikacji i ulepszania środowiska użytkownika końcowego przy użyciu [platformy tożsamości firmy Microsoft](../develop/v2-overview.md) z biblioteką uwierzytelniania firmy Microsoft (MSAL).

- Zapewnij swoim partnerom dostęp do zasobów w chmurze przy użyciu [funkcji współpracy B2B usługi Azure AD](../external-identities/what-is-b2b.md). Zasoby w chmurze usuwają obciążenie dotyczące konfigurowania Federacji punkt-punkt z partnerami.

### <a name="address-compliance-and-governance"></a>Zgodność i zarządzanie adresami

Zapewnienie zgodności z wymaganiami prawnymi przez wymuszenie zasad dostępu do zasobów firmy i monitorowanie dostępu użytkowników do aplikacji i skojarzonych z nimi danych za pomocą zintegrowanych narzędzi i interfejsów API inspekcji. Za pomocą usługi Azure AD można monitorować logowanie do aplikacji za pomocą raportów, które używają [narzędzi zdarzenia zabezpieczeń i monitorowanie zdarzeń (Siem)](../reports-monitoring/plan-monitoring-and-reporting.md). Możesz uzyskać dostęp do raportów z portalu lub interfejsów API i programowo przeprowadzić inspekcję, kto ma dostęp do aplikacji, i usunąć dostęp dla nieaktywnych użytkowników za pośrednictwem przeglądów dostępu.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Planowanie faz migracji i strategii projektu

Gdy projekty technologii kończą się niepowodzeniem, często wynikają z niedopasowanych oczekiwań, odpowiednich uczestników nie zaangażowanych lub braku komunikacji. Zadbaj o pomyślne zaplanowanie projektu.

### <a name="the-phases-of-migration"></a>Fazy migracji

Przed rozpoczęciem pracy z narzędziami należy zrozumieć, jak należy wziąć pod uwagę proces migracji. Za pomocą kilku bezpośrednich warsztatów zalecamy użycie następujących czterech etapów:

![Diagram faz migracji](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Złóż zespół projektu

Migracja aplikacji jest wysiłkem zespołu i należy upewnić się, że wszystkie wymagane pozycje zostały wypełnione. Pomoc techniczna z wyższych liderów firmy jest ważna. Upewnij się, że powiążesz właściwy zestaw sponsorów wykonawczych, podejmowania decyzji służbowych i ekspertów z dziedziny (msp).

W projekcie migracji jedna osoba może spełnić wiele ról lub wiele osób spełnia każdą rolę, w zależności od rozmiaru i struktury organizacji. Możesz również mieć zależność od innych zespołów, które odgrywają kluczową rolę w poziomie zabezpieczeń.

Poniższa tabela zawiera podstawowe role i ich wkłady:

| Rola          | Contributions                                              |
| ------------- | ---------------------------------------------------------- |
| **Project Manager** | Autokar projektu dla identyfikatora GUID projektu, w tym:<br /> — Uzyskaj pomoc techniczną<br /> — Przenieś na uczestników projektu<br /> — Zarządzanie harmonogramami, dokumentacją i komunikacją |
| **Administrator architekta tożsamości/aplikacja usługi Azure AD** | Są one odpowiedzialne za następujące:<br /> — Zaprojektuj rozwiązanie we współpracy z uczestnikami projektu<br /> -udokumentowanie projektu rozwiązania i procedur operacyjnych do przekazania do zespołu operacyjnego<br /> — Zarządzanie środowiskami przedprodukcyjnymi i produkcyjnymi |
| **Lokalny zespół operacyjny usługi AD** | Organizacja, która zarządza różnymi lokalnymi źródłami tożsamości, takimi jak lasy usługi AD, katalogi LDAP, systemy kadr itp.<br /> -Wykonaj wszelkie zadania zaradcze potrzebne przed synchronizacją<br /> — Podaj konta usługi wymagane do synchronizacji<br /> -zapewnianie dostępu do konfigurowania Federacji w usłudze Azure AD |
| **Menedżer pomocy technicznej IT** | Przedstawiciel firmy z działu pomocy technicznej IT, który może dostarczyć dane wejściowe dotyczące możliwości obsługi tej zmiany w perspektywie pomocy technicznej. |
| **Właściciel zabezpieczeń**  | Przedstawiciel zespołu ds. zabezpieczeń, który może zapewnić spełnienie wymagań w zakresie bezpieczeństwa organizacji. |
| **Właściciele techniczni aplikacji** | Obejmuje technicznych właścicieli aplikacji i usług, które będą integrowane z usługą Azure AD. Zapewniają one atrybuty tożsamości aplikacji, które powinny obejmować proces synchronizacji. Zwykle mają one relację z przedstawicielami woluminu CSV. |
| **Właściciele firmy aplikacji** | Reprezentatywnych współpracowników, którzy mogą dostarczać dane wejściowe dotyczące środowiska użytkownika i użyteczności tej zmiany z perspektywy użytkownika i są własnością ogólnego aspektu biznesowego aplikacji, co może obejmować zarządzanie dostępem. |
| **Grupa pilotażowa użytkowników** | Użytkownicy, którzy będą testować się w ramach codziennej pracy, doświadczenia pilotażowego i przekazywania informacji zwrotnych dotyczących reszty wdrożeń. |

### <a name="plan-communications"></a>Planowanie komunikacji

Skuteczna praca i komunikacja jest kluczem do sukcesu. Ważne jest, aby umożliwić uczestnikom projektu i użytkownikom końcowym dostęp do informacji i informować o zaplanowanych aktualizacjach. Zapoznaj się z informacjami na temat migracji, oczekiwanych osi czasu oraz Zaplanuj ewentualne tymczasowe zakłócenia biznesowe. Używaj wielu ścieżek, takich jak sesje krótkie, wiadomości e-mail, spotkania jedno-do-jednego, transparenty i townhalls.

Na podstawie strategii komunikacji, która została wybrana dla aplikacji, możesz chcieć przypominać użytkowników o zbliżającym się przestoju. Należy również upewnić się, że nie ma żadnych ostatnich zmian lub wpływów na działalność biznesową, które wymagają odłożenia wdrożenia.

W poniższej tabeli znajdziesz minimalną sugerowaną komunikację, która będzie informować zainteresowanych stron:

**Etapy planu i strategia projektu**:

| Komunikacja      | Grupy odbiorców                                          |
| ------------------ | ------------------------------------------------- |
| Świadomość i biznes/wartość techniczna projektu | Wszystkie z wyjątkiem użytkowników końcowych |
| Żądanie dotyczące aplikacji pilotażowych | — Właściciele firmy aplikacji<br />— Właściciele techniczna aplikacji<br />-Architektzy i zespół ds. tożsamości |

**Faza 1 — odnajdywanie i zakres**:

| Komunikacja      | Grupy odbiorców                                          |
| ------------------ | ------------------------------------------------- |
| -Żądanie dotyczące informacji o aplikacji<br />— Wynik realizacji zakresu | — Właściciele techniczna aplikacji<br />— Właściciele firmy aplikacji |

**Faza 2 — klasyfikowanie aplikacji i planów pilotażowych**:

| Komunikacja      | Grupy odbiorców                                          |
| ------------------ | ------------------------------------------------- |
| -Wyniki klasyfikacji i znaczenie dla harmonogramu migracji<br />— Harmonogram migracji wstępnej | — Właściciele techniczna aplikacji<br /> — Właściciele firmy aplikacji |

**Faza 3 — Planowanie migracji i testowania**:

| Komunikacja      | Grupy odbiorców                                          |
| ------------------ | ------------------------------------------------- |
| -Wyniki testowania migracji aplikacji | — Właściciele techniczna aplikacji<br />— Właściciele firmy aplikacji |
| — Powiadomienie o tym, że migracja jest dostępna i wyjaśni wynikowe środowisko użytkownika końcowego.<br />— Przestoje związane z przesyłaniem i pełną łącznością, w tym informacje o tym, co powinno teraz robić, opinie i jak uzyskać pomoc | -Użytkownicy końcowi (i wszystkie inne) |

**Faza 4 — zarządzanie i uzyskiwanie szczegółowych** informacji:

| Komunikacja      | Grupy odbiorców                                          |
| ------------------ | ------------------------------------------------- |
| Dostępna analiza i sposób uzyskiwania dostępu | — Właściciele techniczna aplikacji<br />— Właściciele firmy aplikacji |

### <a name="migration-states-communication-dashboard"></a>Pulpit nawigacyjny komunikacji Stanów migracji

Przekazywanie informacji o ogólnym stanie projektu migracji ma kluczowe znaczenie, ponieważ pokazuje postęp i pomaga właścicielom aplikacji, których aplikacje są dostępne do migracji w celu przygotowania do przeniesienia. Możesz połączyć prosty pulpit nawigacyjny za pomocą Power BI lub innych narzędzi do raportowania, aby zapewnić widoczność stanu aplikacji podczas migracji.

Można rozważyć następujące stany migracji:

| Stany migracji       | Plan działania                                   |
| ---------------------- | --------------------------------------------- |
| **Początkowe żądanie** | Znajdź aplikację i skontaktuj się z właścicielem, aby uzyskać więcej informacji |
| **Ocena zakończona** | Właściciel aplikacji szacuje wymagania aplikacji i zwraca kwestionariusz aplikacji</td>
| **Konfiguracja w toku** | Opracowywanie zmian niezbędnych do zarządzania uwierzytelnianiem w usłudze Azure AD |
| **Konfiguracja testu powiodła się** | Oceń zmiany i Uwierzytelnij aplikację w odniesieniu do testowej dzierżawy usługi Azure AD w środowisku testowym |
| **Konfiguracja produkcyjna zakończona pomyślnie** | Zmień konfiguracje, aby współpracowały z produkcyjną dzierżawą usługi AD i ocenić uwierzytelnianie aplikacji w środowisku testowym |
| **Zakończ/Wyloguj** | Wdróż zmiany dla aplikacji w środowisku produkcyjnym i wykonaj względem produkcyjnej dzierżawy usługi Azure AD |

Dzięki temu właściciele aplikacji będą wiedzieli, czym jest harmonogram migracji i testowania aplikacji, gdy ich aplikacje są do migracji, a wyniki pochodzą z innych aplikacji, które zostały już zmigrowane. Możesz również zastanowić się nad dostarczeniem linków do bazy danych śledzenia usterek dla właścicieli, aby można było plików i wyświetlać problemy dotyczące migrowanych aplikacji.

### <a name="best-practices"></a>Najlepsze rozwiązania

Poniżej przedstawiono scenariusze sukcesów klientów i partnerów oraz sugerowane najlepsze rozwiązania:

- [Pięć wskazówek dotyczących ulepszania procesu migracji do Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) przez konsultacje patriotą, który jest członkiem naszej sieci partnerskiej, która koncentruje się na ułatwianiu klientom wdrażania rozwiązań w chmurze firmy Microsoft.

- [Utwórz strategię zarządzania ryzykiem dla migracji aplikacji usługi Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) przez Edgile, czyli partnera, który koncentruje się na rozwiązaniach do zarządzania w usłudze IAM i czynnikami ryzyka.

## <a name="phase-1-discover-and-scope-apps"></a>Faza 1: odnajdywanie i zakres aplikacji

**Odnajdywanie i analiza aplikacji to podstawowe ćwiczenie zapewniające dobry początek.** Możesz nie wiedzieć wszystko, co jest gotowe do uwzględnienia nieznanych aplikacji.

### <a name="find-your-apps"></a>Znajdowanie aplikacji

Pierwszy punkt decyzyjny migracji aplikacji to aplikacje, które mają zostać poddane migracji, które w razie potrzeby powinny pozostać i które aplikacje są przestarzałe. Zawsze istnieje możliwość wycofania aplikacji, które nie będą używane w Twojej organizacji. Istnieje kilka sposobów znajdowania aplikacji w organizacji. **Podczas odnajdywania aplikacji upewnij się, że zawarto w nim aplikacje deweloperskie i planowane. Użyj usługi Azure AD do uwierzytelniania we wszystkich przyszłych aplikacjach.**

**Używanie Active Directory Federation Services (AD FS) do zbierania prawidłowego spisu aplikacji:**

- **Używanie programu Azure AD Connect Health.** Jeśli masz licencję na Azure AD — wersja Premium, zalecamy wdrożenie [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) w celu przeanalizowania użycia aplikacji w środowisku lokalnym. [Raport aplikacji ADFS](./migrate-adfs-application-activity.md) (wersja zapoznawcza) umożliwia odnajdywanie aplikacji usług ADFS, które można migrować i ocenę gotowości aplikacji do migracji. Po zakończeniu migracji Wdróż [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) , który umożliwia ciągłe monitorowanie w tle w organizacji po zakończeniu korzystania z chmury.

- **Analiza dzienników AD FS**. Jeśli nie masz licencji Azure AD — wersja Premium, zalecamy użycie usług AD FS do migracji aplikacji usługi Azure AD opartych na programie [PowerShell.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration) Zapoznaj się z [przewodnikiem po rozwiązaniu](./migrate-adfs-apps-to-azure.md):

[Migrowanie aplikacji z Active Directory Federation Services (AD FS) do usługi Azure AD.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Korzystanie z innych dostawców tożsamości (dostawców tożsamości)

W przypadku innych dostawców tożsamości (takich jak usługi okta lub ping) do eksportowania spisu aplikacji można użyć ich narzędzi. Możesz rozważyć przeglądanie zasad usługi zarejestrowanych w Active Directory, które odpowiadają aplikacjom sieci Web w organizacji.

### <a name="using-cloud-discovery-tools"></a>Korzystanie z narzędzi usługi Cloud Discovery

W środowisku chmury trzeba mieć rozbudowaną widoczność, kontrolę nad przejazdami danych oraz zaawansowaną analizę, aby znaleźć i zwalczać zagrożenia cybernetycznymi w ramach wszystkich usług w chmurze. Spis aplikacji w chmurze można zebrać przy użyciu następujących narzędzi:

- Usługa **Cloud Access Security Broker (CASB**) — [CASB](/cloud-app-security/) zazwyczaj działa wraz z zaporą w celu zapewnienia wglądu w użycie aplikacji w chmurze pracowników i pomaga chronić dane firmowe przed zagrożeniami cyberbezpieczeństwa. Raport CASB może pomóc w ustaleniu najczęściej używanych aplikacji w organizacji oraz wczesnych celach migracji do usługi Azure AD.

- **Cloud Discovery** — przez skonfigurowanie [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)można uzyskać wgląd w użycie aplikacji w chmurze i odkryć niezaakceptowane lub zasłonięte aplikacje IT.

- **Interfejsy API** — dla aplikacji podłączonych do infrastruktury chmurowej można użyć interfejsów API i narzędzi w tych systemach, aby zacząć korzystać ze spisu hostowanych aplikacji. W środowisku platformy Azure:

  - Użyj polecenia cmdlet [Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) , aby uzyskać informacje o usłudze Azure Websites.

  - Użyj polecenia cmdlet [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) , aby uzyskać informacje na temat Web Apps platformy Azure.
D
  - Wszystkie aplikacje działające w usługach IIS firmy Microsoft można znaleźć w wierszu polecenia systemu Windows przy użyciu [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Użyj [aplikacji](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) i nazw [głównych usługi](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) , aby uzyskać informacje o aplikacji i wystąpieniu aplikacji w katalogu w usłudze Azure AD.

### <a name="using-manual-processes"></a>Korzystanie z procesów ręcznych

Po wykonaniu opisanych powyżej metod automatycznych będziesz mieć dobre dojście do aplikacji. Można jednak wziąć pod uwagę następujące kwestie, aby mieć pewność, że masz dobre pokrycie we wszystkich obszarach dostępu użytkowników:

- Skontaktuj się z różnymi właścicielami firmy w organizacji, aby znaleźć aplikacje używane w organizacji.

- Uruchom narzędzie do inspekcji HTTP na serwerze proxy lub Analizuj dzienniki proxy, aby zobaczyć, gdzie ruch jest często kierowany.

- Przejrzyj dzienniki Weblogs z popularnych witryn portalu firmy, aby zobaczyć, jakie linki użytkownicy uzyskują do nich dostęp.

- Skontaktuj się z dyrektorami lub innymi kluczowymi członkami biznesowymi, aby upewnić się, że aplikacje o znaczeniu krytycznym zostały omówione w firmie.

### <a name="type-of-apps-to-migrate"></a>Typ aplikacji do migracji

Po znalezieniu aplikacji możesz zidentyfikować następujące typy aplikacji w organizacji:

- Aplikacje korzystające już z nowoczesnych protokołów uwierzytelniania

- Aplikacje korzystające z starszych protokołów uwierzytelniania wybranych do modernizacji

- Aplikacje korzystające z starszych protokołów uwierzytelniania, które nie zostały wybrane do modernizacji

- Nowe aplikacje biznesowe (LoB)

### <a name="apps-that-use-modern-authentication-already"></a>Aplikacje korzystające już z nowoczesnego uwierzytelniania

Już nowoczesne aplikacje są najprawdopodobniej przeniesione do usługi Azure AD. Te aplikacje używają już nowoczesnych protokołów uwierzytelniania (takich jak SAML lub OpenID Connect Connect) i można je ponownie skonfigurować do uwierzytelniania w usłudze Azure AD.

Oprócz opcji dostępnych w [galerii aplikacji usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) mogą to być aplikacje, które już istnieją w organizacji, lub wszelkie aplikacje innych firm od dostawcy, który nie jest częścią galerii usługi Azure AD ([aplikacje spoza galerii)](./add-application-portal.md).

Starsze aplikacje wybrane do modernizacji

W przypadku starszych aplikacji, które chcesz przeprowadzić modernizację, przejście do usługi Azure AD na potrzeby uwierzytelniania podstawowego i autoryzacja powoduje odblokowanie wszystkich możliwości i bogactwa danych, które [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) i [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) mają być oferowane.

Zalecamy **zaktualizowanie kodu stosu uwierzytelniania** dla tych aplikacji ze starszego protokołu (takiego jak uwierzytelnianie Windows-Integrated, ograniczone delegowanie protokołu Kerberos, uwierzytelnianie na podstawie nagłówków HTTP) do nowoczesnego protokołu (na przykład SAML lub OpenID Connect Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Starsze aplikacje, które nie zostały wybrane do modernizacji

W przypadku niektórych aplikacji korzystających ze starszych protokołów uwierzytelniania czasami modernizowanie ich uwierzytelniania nie jest właściwe w przypadku przyczyn związanych z biznesem. Należą do nich następujące typy aplikacji:

- Aplikacje przechowywane lokalnie w celu zapewnienia zgodności lub kontroli.

- Aplikacje połączone z tożsamością lokalną lub dostawcą federacyjnym, których nie chcesz zmienić.

- Aplikacje opracowane przy użyciu lokalnych standardów uwierzytelniania, które nie są planowane do przeniesienia

Usługa Azure AD może przynieść doskonałe korzyści dla tych starszych aplikacji, ponieważ można włączyć nowoczesne funkcje zabezpieczeń i zarządzania usługą Azure AD, takie jak [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), [dostęp warunkowy](../conditional-access/overview.md), [Ochrona tożsamości](../identity-protection/index.yml), [dostęp do aplikacji delegowanych](./access-panel-manage-self-service-access.md)i [przeglądy dostępu do](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) tych aplikacji bez konieczności dotykania aplikacji.

Zacznij od **rozszerzenia tych aplikacji do chmury** za pomocą [serwera proxy aplikacji](./application-proxy-configure-single-sign-on-password-vaulting.md) usługi Azure AD przy użyciu prostego sposobu uwierzytelniania (takiego jak przechowywanie haseł), aby szybko przeprowadzić migrację użytkowników lub z naszych [integracji partnerów](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) z kontrolerami dostarczania aplikacji, które zostały już wdrożone.

### <a name="new-line-of-business-lob-apps"></a>Nowe aplikacje biznesowe (LoB)

Zwykle opracowujesz aplikacje LoB do użytku wewnętrznego w organizacji. Jeśli w potoku masz nowe aplikacje, zalecamy użycie [platformy tożsamości firmy Microsoft](../develop/v2-overview.md) w celu zaimplementowania OpenID Connect Connect.

### <a name="apps-to-deprecate"></a>Aplikacje do wycofania

Aplikacje bez wyraźnych właścicieli i jasne konserwacje i monitorowanie powodują zagrożenie bezpieczeństwa dla organizacji. Rozważ przestarzałe aplikacje w przypadku:

- ich **funkcjonalność jest wysoce nadmiarowa** w innych systemach • **nie ma właściciela firmy**

- nie ma jasnego **użycia**.

Zalecamy **, aby nie zaniechać wysokiego wpływu na aplikacje o znaczeniu krytycznym**. W takich przypadkach współpraca z właścicielami firmy w celu ustalenia odpowiedniej strategii.

### <a name="exit-criteria"></a>Kryteria wyjścia

W tej fazie zakończono pomyślnie:

- Dobre zrozumienie systemów w zakresie migracji (które można wycofać po przeniesieniu do usługi Azure AD)

- Lista aplikacji, które obejmują:

  - Jakie systemy są połączone z tymi aplikacjami
  - Z lokalizacji i na urządzeniach, do których użytkownicy uzyskują do nich dostęp
  - Czy zostaną zmigrowane, zaniechane lub połączone z [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

> [!NOTE]
> Możesz pobrać [Arkusz odnajdowania aplikacji](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) , aby zarejestrować aplikacje, które mają zostać zmigrowane do uwierzytelniania w usłudze Azure AD, a także te, które chcesz opuścić przy użyciu [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Faza 2: klasyfikowanie aplikacji i planowanie pilotażu

Klasyfikowanie migracji aplikacji jest ważnym zadaniem. Nie wszystkie aplikacje muszą być migrowane i przenoszone w tym samym czasie. Po zebraniu informacji o każdej z nich można usprawnić, które aplikacje powinny być migrowane w pierwszej kolejności, co może zająć trochę czasu.

### <a name="classify-in-scope-apps"></a>Klasyfikowanie aplikacji w zakresie

Jednym z nich jest to, że jest to na przykład zależność od stopnia zagrożenia firmy, użycia i cykl życia, z których każda jest zależna od wielu czynników.

### <a name="business-criticality"></a>Znaczenie biznesowe

Krytyczne znaczenie biznesowe będzie miało różne wymiary dla każdej firmy, ale dwie miary, które należy wziąć pod uwagę, to **funkcje i funkcje** oraz **Profile użytkowników**. Przypisywanie aplikacji z unikatowymi funkcjami wyższego poziomu niż te z funkcją nadmiarową lub przestarzałą.

![Diagram spektrum funkcji & funkcje i profile użytkowników](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Użycie

Aplikacje o **wysokich numerach użycia** powinny otrzymać wyższą wartość niż aplikacje o niskim zużyciu. Przypisz wyższą wartość do aplikacji z użytkownikami zespołu zewnętrznego, kierowniczego lub zabezpieczeń. Wykonaj te oceny dla każdej aplikacji w portfolio migracji.

![Diagram spektrum wielkości woluminu użytkownika i użytkownika](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Po ustaleniu wartości dla celów krytycznych i użycia w firmie można określić **aplikację cykl życia** i utworzyć macierz priorytetów. Zobacz jedną z poniższych macierzy:

![Trójkątny diagram przedstawiający relacje między użyciem, oczekiwany cykl życia i krytyczność firmy](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Określanie priorytetów aplikacji do migracji

Możesz rozpocząć migrację aplikacji przy użyciu aplikacji o najniższym priorytecie lub aplikacji o najwyższym priorytecie w zależności od potrzeb organizacji.

W scenariuszu, w którym możesz nie korzystać z usług Azure AD i Identity Services, rozważ najpierw przeniesienie **aplikacji o najniższym priorytecie** do usługi Azure AD. Pozwoli to zminimalizować wpływ na firmę i można utworzyć chwilę. Po pomyślnym przeniesieniu tych aplikacji i uzyskaniu zaufania uczestnika projektu można kontynuować migrację innych aplikacji.

Jeśli nie ma żadnych jasnych priorytetów, należy rozważyć przeniesienie aplikacji znajdujących się w [galerii usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) najpierw i obsłudze wielu dostawców tożsamości (AD FS lub usługi okta), ponieważ są one łatwiejsze do integracji. Najprawdopodobniej te aplikacje są aplikacjami o **najwyższym priorytecie** w organizacji. Aby ułatwić integrację aplikacji SaaS z usługą Azure AD, opracowano kolekcję [samouczków](../saas-apps/tutorial-list.md) , które przeprowadzą Cię przez proces konfiguracji.

Po upływie ostatecznego terminu migracji aplikacji ten zasobnik aplikacji o najwyższym priorytecie zajmie się dużym obciążeniem. Możesz ostatecznie wybrać aplikacje o niższym priorytecie, ponieważ nie zmienią one kosztu nawet po przeniesieniu terminu ostatecznego. Nawet jeśli musisz odnowić licencję, będzie to mała kwota.

Oprócz tej klasyfikacji i w zależności od pilności migracji można również rozważyć zastosowanie **harmonogramu migracji** , w ramach którego właściciele aplikacji muszą mieć możliwość migrowania aplikacji. Po zakończeniu tego procesu powinna istnieć lista wszystkich aplikacji w zasobnikach z priorytetami na potrzeby migracji.

### <a name="document-your-apps"></a>Dokumentowanie aplikacji

Najpierw Zacznij od zebrania najważniejszych informacji dotyczących aplikacji. [Arkusz odnajdowania aplikacji](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)pomoże Ci w szybkim podejmowaniu decyzji dotyczących migracji i w ogóle uzyskać zalecenie dotyczące Twojej grupy biznesowej.

Informacje ważne do podejmowania decyzji dotyczących migracji obejmują:

- **Nazwa aplikacji** — ta aplikacja jest znana dla firmy?

- **Typ aplikacji** — czy jest to aplikacja SaaS innej firmy? Niestandardowa aplikacja sieci Web biznesowa? Interfejs API?

- **Krytyczne znaczenie biznesowe** ? Małą? Lub gdzieś między nimi?

- **Wolumin dostępu użytkownika** — czy wszyscy uzyskują dostęp do tej aplikacji, czy tylko kilku osób?

- **Planowane cykl życia** — jak długo będzie mieć tę aplikację? Mniej niż sześć miesięcy? Ponad dwa lata?

- **Bieżący dostawca tożsamości** — co to jest podstawowy dostawcy tożsamości dla tej aplikacji? Czy jest ona zależna od lokalnego magazynu?

- **Metoda uwierzytelniania** — czy aplikacja jest uwierzytelniana przy użyciu otwartych standardów?

- **Czy planujesz zaktualizować kod aplikacji** — czy jest to aplikacja zaplanowana czy aktywna?

- **Czy planujesz zachowanie aplikacji** w środowisku lokalnym — czy chcesz zachować swoją aplikację w Twoim centrum danych długoterminowym?

- **Niezależnie od tego, czy aplikacja jest zależna od innych aplikacji czy interfejsów API** — czy aplikacja aktualnie wywołuje inne aplikacje lub interfejsy API?

- **Czy aplikacja znajduje się w galerii usługi Azure AD — czy** aplikacja jest już zintegrowana z [galerią usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)?

Inne dane, które pomogą później, ale nie trzeba podejmować decyzji o natychmiastowej migracji:

- **Adres URL aplikacji** — gdzie użytkownicy przejdą w celu uzyskania dostępu do aplikacji?

- **Opis aplikacji** — Krótki opis działania aplikacji?

- **Właściciel aplikacji** — kto w firmie jest główną ZK dla aplikacji?

- **Ogólne komentarze lub uwagi** — inne ogólne informacje dotyczące aplikacji lub własności biznesowej

Po sklasyfikowaniu aplikacji i udokumentowaniu szczegółowych informacji, należy się upewnić, że wszyscy właściciele firmy kupią do planowanej strategii migracji.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Aplikacje wybrane dla pilotażu powinny reprezentować kluczowe wymagania dotyczące tożsamości i zabezpieczeń organizacji, a użytkownik musi mieć wyraźny zakup od właścicieli aplikacji. Pilotaży są zwykle uruchamiane w osobnym środowisku testowym. Zapoznaj się z [najlepszymi rozwiązaniami dla pilotażów](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) na stronie plany wdrożenia.

**Nie zapomnij partnerów zewnętrznych.** Upewnij się, że biorą udział w harmonogramie i testowaniu migracji. Na koniec upewnij się, że masz dostęp do pomocy technicznej, jeśli wystąpiły problemy z przerwaniem.

### <a name="plan-for-limitations"></a>Planowanie ograniczeń

Chociaż niektóre aplikacje są łatwe do migracji, inne mogą trwać dłużej ze względu na wiele serwerów lub wystąpień. Na przykład migracja programu SharePoint może trwać dłużej ze względu na niestandardową stronę logowania.

Wielu dostawców aplikacji SaaS jest opłatą za zmianę połączenia SSO. Sprawdź je i Zaplanuj.

Usługa Azure AD ma także [limity i ograniczenia usługi](../enterprise-users/directory-service-limits-restrictions.md) .

### <a name="app-owner-sign-off"></a>Wylogowywanie właściciela aplikacji

Aplikacje o znaczeniu krytycznym dla firmy i powszechnie używane mogą potrzebować grupy użytkowników pilotażowych do testowania aplikacji na etapie pilotażu. Po przetestowaniu aplikacji w środowisku przedprodukcyjnym lub pilotażowym upewnij się, że właściciele firmy aplikacji mogą się wylogować z wydajności przed migracją aplikacji i wszyscy użytkownicy korzystający z usługi Azure AD na potrzeby uwierzytelniania.

### <a name="plan-the-security-posture"></a>Planowanie stan zabezpieczeń

Przed rozpoczęciem procesu migracji należy w pełni rozważyć stan zabezpieczeń, które mają być opracowane dla firmowego systemu tożsamości. Jest to oparte na zbieraniu tych cennych zestawów informacji: **tożsamości, urządzeń i lokalizacji, które uzyskują dostęp do danych.**

### <a name="identities-and-data"></a>Tożsamości i dane

Większość organizacji ma określone wymagania dotyczące tożsamości i ochrony danych, które różnią się w zależności od segmentów branżowych i funkcji zadań w organizacjach. Zapoznaj się z [konfiguracją konfiguracji i dostępu do urządzenia](/microsoft-365/enterprise/microsoft-365-policies-configurations) , aby poznać nasze zalecenia, w tym określony zestaw [zasad dostępu warunkowego](../conditional-access/overview.md) i powiązane funkcje.

Te informacje służą do ochrony dostępu do wszystkich usług zintegrowanych z usługą Azure AD. Te zalecenia są wyrównane z bezpiecznym wynikiem firmy Microsoft i [oceną tożsamości w usłudze Azure AD](../fundamentals/identity-secure-score.md). Ten wskaźnik ułatwi Ci:

- Obiektywne mierzenie poziomu bezpieczeństwa tożsamości

- Planowanie poprawy bezpieczeństwa tożsamości

- Sprawdzanie powodzenia wprowadzonych udoskonaleń

Pomoże to również zaimplementować [pięć kroków w celu zabezpieczenia infrastruktury tożsamości](../../security/fundamentals/steps-secure-identity.md). Skorzystaj ze wskazówek jako punktu wyjścia dla organizacji i Dostosuj zasady, aby spełniały określone wymagania organizacji.

### <a name="who-is-accessing-your-data"></a>Kto uzyskuje dostęp do danych?

Istnieją dwie główne kategorie użytkowników aplikacji i zasobów obsługiwanych przez usługę Azure AD:

- **Wewnętrzny:** Pracownicy, wykonawcy i dostawcy, którzy mają konta w ramach dostawcy tożsamości. Może to wymagać dalszej tabeli przestawnej z różnymi regułami dla menedżerów lub liderów, a nie innych pracowników.

- **Zewnętrzny:** Dostawcy, dostawcy, dystrybutorzy lub inni partnerzy biznesowi, którzy współpracują z Twoją organizacją w regularnych działaniach dotyczących [współpracy B2B usługi Azure AD.](../external-identities/what-is-b2b.md)

Można definiować grupy dla tych użytkowników i wypełniać te grupy na różne sposoby. Możesz zdecydować, że administrator musi ręcznie dodać członków do grupy lub włączyć członkostwo w grupie samoobsługowy. Można ustalić reguły, które automatycznie dodają członków do grup na podstawie określonych kryteriów przy użyciu [grup dynamicznych](../enterprise-users/groups-dynamic-membership.md).

Użytkownicy zewnętrzni mogą również odnosić się do klientów. [Azure AD B2C](../../active-directory-b2c/overview.md), oddzielny produkt obsługuje uwierzytelnianie klienta. Jednak jest to poza zakresem tego dokumentu.

### <a name="devicelocation-used-to-access-data"></a>Urządzenie/lokalizacja służąca do uzyskiwania dostępu do danych

Ważne jest również urządzenie i lokalizacja, za pomocą których użytkownik uzyskuje dostęp do aplikacji. Urządzenia fizycznie połączone z siecią firmową są bezpieczniejsze. Połączenia spoza sieci za pośrednictwem sieci VPN mogą wymagać przeprowadzenia kontroli.

![Diagram przedstawiający relację między lokalizacją użytkownika i dostępem do danych](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Korzystając z tych aspektów zasobów, użytkowników i urządzeń, możesz korzystać z możliwości [dostępu warunkowego usługi Azure AD](../conditional-access/overview.md) . Dostęp warunkowy wykracza poza uprawnienia użytkownika: jest on oparty na kombinacji czynników, takich jak tożsamość użytkownika lub grupy, Sieć, z którą jest połączony użytkownik, używane urządzenie i aplikacja oraz typ danych, do których próbują uzyskać dostęp. Dostęp udzielony użytkownikowi dostosowuje się do tego szerszego zestawu warunków.

### <a name="exit-criteria"></a>Kryteria wyjścia

W tej fazie zakończono pomyślnie:

- Znajomość aplikacji
  - W pełni udokumentowane aplikacje, które zamierzasz zmigrować
  - Aplikacje z priorytetami na podstawie stopnia ważności firmy, woluminu użycia i cykl życia

- Wybierz aplikacje, które reprezentują wymagania dla pilotażu

- Biznes — kupowanie w ramach priorytetyzacji i strategii

- Zapoznaj się z wymaganiami dotyczącymi stan zabezpieczeń i sposobami ich implementacji

## <a name="phase-3-plan-migration-and-testing"></a>Faza 3: Planowanie migracji i testowania

Po uzyskaniu zakupów w firmie następnym krokiem jest rozpoczęcie migrowania tych aplikacji do uwierzytelniania usługi Azure AD.

### <a name="migration-tools-and-guidance"></a>Narzędzia i wskazówki dotyczące migracji

Skorzystaj z poniższych narzędzi i wskazówek, aby wykonać dokładne kroki wymagane do migrowania aplikacji do usługi Azure AD:

- **Ogólne wskazówki dotyczące migracji** — korzystanie z kwestionariusza, narzędzi, szablonów poczty e-mail i aplikacji w ramach [zestawu narzędzi do migracji aplikacji usługi Azure AD](./migration-resources.md) w celu odnajdywania, klasyfikowania i migrowania aplikacji.

- **Aplikacje SaaS** — zapoznaj się z naszą listą [setek samouczków aplikacji SaaS](../saas-apps/tutorial-list.md) i kompletnym [PLANem wdrażania rejestracji JEDNOkrotnej usługi Azure AD](https://aka.ms/ssodeploymentplan) , aby zapoznać się z kompleksowym procesem.

- **Aplikacje działające lokalnie** — Dowiedz się więcej [o usłudze Azure serwer proxy aplikacji usługi Azure AD](./application-proxy.md) i Skorzystaj z kompletnego [planu wdrażania serwer proxy aplikacji usługi Azure AD platformy Azure](https://aka.ms/AppProxyDPDownload) , aby szybko rozpocząć pracę.

- **Aplikacje, które** tworzysz — zapoznaj się z naszymi krok po kroku [integrację](../develop/quickstart-register-app.md) i wskazówki dotyczące [rejestracji](../develop/quickstart-register-app.md) .

Po migracji można wysłać komunikat informujący użytkowników o pomyślnym wdrożeniu i przypomnieniu o wszelkich nowych krokach, które muszą zostać wykonane.

### <a name="plan-testing"></a>Testowanie planu

Podczas procesu migracji aplikacja może już mieć środowisko testowe używane podczas zwykłych wdrożeń. Można nadal używać tego środowiska do testowania migracji. Jeśli środowisko testowe nie jest obecnie dostępne, można je skonfigurować za pomocą Azure App Service lub Virtual Machines platformy Azure, w zależności od architektury aplikacji. Możesz skonfigurować oddzielną dzierżawę usługi Azure AD, która będzie używana podczas tworzenia konfiguracji aplikacji. Ta dzierżawa zostanie uruchomiona w stanie czystym i nie zostanie skonfigurowana do synchronizacji z żadnym systemem.

Możesz testować każdą aplikację, logując się przy użyciu użytkownika testowego i upewniając się, że wszystkie funkcje są takie same jak przed migracją. W przypadku określenia podczas testowania, że użytkownicy będą musieli zaktualizować ustawienia usługi [MFA](/azure/active-directory/authentication/howto-mfa-userstates) lub [SSPR](../authentication/tutorial-enable-sspr.md)lub dodać tę funkcję podczas migracji, pamiętaj o dodaniu jej do planu komunikacji z użytkownikami końcowymi. Zobacz szablony komunikacji usługi [MFA](https://aka.ms/mfatemplates) i [SSPR](https://aka.ms/ssprtemplates) dla użytkowników końcowych.

Po przeprowadzeniu migracji aplikacji przejdź do [Azure Portal](https://aad.portal.azure.com/) , aby sprawdzić, czy migracja zakończyła się pomyślnie. Postępuj zgodnie z poniższymi instrukcjami:

- Wybierz pozycję **aplikacje dla przedsiębiorstw &gt; wszystkie aplikacje** i Znajdź aplikację z listy.

- Wybierz pozycję **Zarządzaj &gt; użytkownikami i grupami** , aby przypisać do aplikacji co najmniej jednego użytkownika lub grupę.

- Wybierz pozycję **Zarządzaj &gt; dostępem warunkowym**. Przejrzyj listę zasad i upewnij się, że nie blokujesz dostępu do aplikacji za pomocą [zasad dostępu warunkowego](../conditional-access/overview.md).

W zależności od sposobu skonfigurowania aplikacji Sprawdź, czy logowanie jednokrotne działa prawidłowo.

| Typ uwierzytelniania      | Testowanie                                             |
| ------------------------ | --------------------------------------------------- |
| **Połączenie OAuth/OpenID Connect** | Wybierz **&gt; uprawnienia aplikacje dla przedsiębiorstw** i upewnij się, że masz zgodę na użycie aplikacji w organizacji w ustawieniach użytkownika aplikacji. |
| **Logowanie jednokrotne oparte na języku SAML** | Użyj przycisku [Testuj ustawienia SAML](./debug-saml-sso-issues.md) w obszarze **Logowanie jednokrotne.** |
| **Logowanie jednokrotne oparte na hasłach** | Pobierz i zainstaluj [rozszerzenie moje aplikacje bezpieczne logowanie](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). To rozszerzenie ułatwia rozpoczęcie pracy z aplikacjami w chmurze w organizacji, które wymagają korzystania z procesu rejestracji jednokrotnej. |

| **[Serwer proxy aplikacji](./application-proxy.md)** | Upewnij się, że łącznik jest uruchomiony i przypisany do aplikacji. Więcej informacji można znaleźć w [przewodniku rozwiązywania problemów z serwerem proxy aplikacji](./application-proxy-troubleshoot.md) . |

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli występują problemy, zapoznaj się z [przewodnikiem rozwiązywania problemów z aplikacjami](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) , aby uzyskać pomoc. Możesz również zapoznać się z artykułami dotyczącymi rozwiązywania problemów, zobacz temat [problemy z logowaniem do aplikacji logowania](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)jednokrotnego opartego na protokole SAML.

### <a name="plan-rollback"></a>Planowanie wycofywania

Jeśli migracja nie powiedzie się, najlepszą strategią jest wycofanie i przetestowanie. Poniżej przedstawiono kroki, które można wykonać, aby wyeliminować problemy z migracją:

- **Wykonaj zrzuty ekranu** istniejącej konfiguracji aplikacji. Po ponownym skonfigurowaniu aplikacji ponownie możesz ją wyszukać.

- Możesz również rozważyć **dostarczenie linków do starszego uwierzytelniania**, jeśli wystąpią problemy z uwierzytelnianiem w chmurze.

- Przed ukończeniem migracji nie należy **zmieniać istniejącej konfiguracji** przy użyciu wcześniejszego dostawcy tożsamości.

- Zacznij od migrowania **aplikacji, które obsługują wiele dostawców tożsamości**. Jeśli coś się nie dzieje, zawsze możesz zmienić konfigurację preferowanych dostawcy tożsamości.

- Upewnij się, że środowisko aplikacji zawiera **przycisk opinii** lub wskaźniki do problemów z działem **pomocy technicznej** .

### <a name="exit-criteria"></a>Kryteria wyjścia

W tej fazie kończy się powodzeniem, gdy masz:

- Określa sposób migrowania poszczególnych aplikacji

- Przegląd narzędzi migracji

- Planowane testowanie, w tym środowiska testowe i grupy

- Planowane wycofywanie

## <a name="phase-4-plan-management-and-insights"></a>Faza 4: Planowanie zarządzania i szczegółowych informacji

Po przeprowadzeniu migracji aplikacji należy upewnić się, że:

- Użytkownicy mogą bezpiecznie uzyskać dostęp do programu i zarządzać nim

- Możesz uzyskać odpowiednie szczegółowe informacje dotyczące użycia i kondycji aplikacji

Zalecamy wykonanie poniższych czynności zgodnie z potrzebami organizacji.

### <a name="manage-your-users-app-access"></a>Zarządzanie dostępem do aplikacji użytkowników

Po przeprowadzeniu migracji aplikacji można wzbogacić środowisko użytkownika na wiele sposobów

**Umożliwianie odnajdywania aplikacji**

**Wskaż użytkownika** w środowisku portalu Moje [aplikacje](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). W tym miejscu użytkownicy mogą uzyskiwać dostęp do wszystkich aplikacji opartych na chmurze, aplikacji udostępnianych przy użyciu [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)i aplikacji korzystających z [serwera proxy aplikacji](./application-proxy.md) , pod warunkiem, że mają uprawnienia dostępu do tych aplikacji.


Możesz poprowadzić użytkowników na temat odnajdywania swoich aplikacji:

- Korzystanie z [istniejącej funkcji logowania](./view-applications-portal.md) jednokrotnego w celu **łączenia użytkowników z dowolnymi aplikacjami**


- Włączanie [samoobsługowego dostępu](./manage-self-service-access.md)do aplikacji i **Zezwalanie użytkownikom na dodawanie aplikacji, które są nadzorowane**

- [Ukryj aplikacje od użytkowników końcowych](./hide-application-from-user-portal.md) (domyślne aplikacje firmy Microsoft lub inne aplikacje) **, aby aplikacje, których potrzebują, były bardziej wykrywalne**

### <a name="make-apps-accessible"></a>Udostępnianie aplikacji

**Zezwól użytkownikom na dostęp do aplikacji z urządzeń przenośnych**. Użytkownicy mogą uzyskiwać dostęp do portalu aplikacji za pomocą programu Intune Managed Browser na urządzeniach z [systemem iOS](./hide-application-from-user-portal.md) 7,0 lub nowszym lub [Android](./hide-application-from-user-portal.md) .

Użytkownicy mogą pobrać **przeglądarkę zarządzaną przez usługę Intune**:

- W **przypadku urządzeń z systemem Android** w [sklepie Google Play](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- W **przypadku urządzeń firmy Apple** w [sklepie Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) można pobrać [aplikację mobilną moje aplikacje dla systemu iOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Zezwól użytkownikom na otwieranie aplikacji z poziomu rozszerzenia przeglądarki.**

Użytkownicy mogą [pobrać rozszerzenie "bezpieczne logowanie](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) do usługi Moje aplikacje" w przeglądarce [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) lub [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) i mogą uruchamiać aplikacje bezpośrednio z poziomu paska przeglądarki, aby:

- **Wyszukaj swoje aplikacje i Wyświetl ich ostatnio używane aplikacje**

- **Automatyczne konwertowanie wewnętrznych adresów URL** , które zostały skonfigurowane w [serwerze proxy aplikacji](./application-proxy.md) , do odpowiednich zewnętrznych adresów URL. Użytkownicy mogą teraz korzystać z linków, które są znane, niezależnie od tego, gdzie się znajdują.

**Zezwól użytkownikom na otwieranie aplikacji z usługi Office.com.**

Użytkownicy mogą przejść do [Office.com](https://www.office.com/) , aby **wyszukać swoje aplikacje, a ich najczęściej używane aplikacje pojawiają się** w tym miejscu, w którym pracują.

### <a name="secure-app-access"></a>Bezpieczny dostęp do aplikacji

Usługa Azure AD zapewnia scentralizowaną lokalizację dostępu do zarządzania migrowanymi aplikacjami. Przejdź do [Azure Portal](https://portal.azure.com/) i Włącz następujące możliwości:

- **Zabezpiecz dostęp użytkowników do aplikacji.** Włącz [zasady dostępu warunkowego](../conditional-access/overview.md)lub [ochronę tożsamości](../identity-protection/overview-identity-protection.md), aby zabezpieczyć dostęp użytkowników do aplikacji na podstawie stanu urządzenia, lokalizacji i innych.

- **Automatyczne Inicjowanie obsługi.** Skonfiguruj [Automatyczne Inicjowanie obsługi użytkowników](../app-provisioning/user-provisioning.md) przy użyciu różnych aplikacji SaaS innych firm, do których użytkownicy muszą uzyskać dostęp. Oprócz tworzenia tożsamości użytkowników, obejmuje to konserwację i usuwanie tożsamości użytkowników jako zmiany stanu lub ról.

- **Delegowanie zarządzania dostępem użytkowników** . W razie potrzeby Włącz samoobsługowy dostęp do aplikacji i *Przypisz osobę zatwierdzającą w firmie, aby zatwierdzić dostęp do tych* aplikacji. Korzystanie z [samoobsługowego zarządzania grupami](../enterprise-users/groups-self-service-management.md)dla grup przypisanych do kolekcji aplikacji.

- **Delegowanie dostępu administratora.** Użycie **roli katalogu** do przypisania roli administratora (takiej jak administrator aplikacji, administrator aplikacji w chmurze lub Deweloper aplikacji) do użytkownika.

### <a name="audit-and-gain-insights-of-your-apps"></a>Inspekcja i uzyskiwanie wglądu w dane aplikacji

Możesz również użyć [Azure Portal](https://portal.azure.com/) do inspekcji wszystkich aplikacji z centralnej lokalizacji,

- **Przeprowadź inspekcję aplikacji** przy użyciu funkcji * * aplikacje dla przedsiębiorstw, Przeprowadź inspekcję lub uzyskaj dostęp do tych samych informacji z [interfejsu API raportowania usługi Azure AD](../reports-monitoring/concept-reporting-api.md) w celu zintegrowania z ulubionymi narzędziami

- **Wyświetlanie uprawnień aplikacji** przy użyciu aplikacji dla **przedsiębiorstw, uprawnień** do aplikacji przy użyciu protokołu OAuth/OpenID Connect Connect.

- **Uzyskaj wgląd w szczegółowe dane** za pomocą **aplikacji dla przedsiębiorstw, zaloguj się**. Uzyskaj dostęp do tych samych informacji z [interfejsu API raportowania usługi Azure AD.](../reports-monitoring/concept-reporting-api.md)

- **Wizualizowanie użycia aplikacji** za pomocą [pakietu zawartości usługi Azure AD Power BI](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Kryteria wyjścia

W tej fazie zakończono pomyślnie:

- Zapewnianie użytkownikom bezpiecznego dostępu do aplikacji

- Zarządzaj inspekcją i zyskaj wgląd w zmigrowane aplikacje

### <a name="do-even-more-with-deployment-plans"></a>Kontynuuj jeszcze więcej dzięki planom wdrażania

Plany wdrożenia przeprowadzą Cię przez wartość biznesową, planowanie, etapy implementacji i zarządzanie rozwiązaniami usługi Azure AD, w tym scenariusze migracji aplikacji. Łączą wszystko, co jest potrzebne do rozpoczęcia wdrażania i pobierania wartości z funkcji usługi Azure AD. Przewodniki wdrażania obejmują takie elementy, jak zalecane przez firmę Microsoft najlepsze rozwiązania, komunikacja użytkowników końcowych, przewodniki dotyczące planowania, kroki implementacji, przypadki testowe i inne.

Wiele [planów wdrażania](../fundamentals/active-directory-deployment-plans.md) jest dostępnych do użytku, a zawsze wszystko!

### <a name="contact-support"></a>Kontakt z pomocą techniczną

Odwiedź następujące linki pomocy technicznej, aby utworzyć lub śledzić bilet pomocy technicznej oraz monitorować kondycję.

- **Pomoc techniczna platformy Azure:** Możesz wywołać [Pomoc techniczna firmy Microsoft](https://azure.microsoft.com/support) i otworzyć bilet dla dowolnego systemu Azure

Problem z wdrożeniem tożsamości w zależności od Enterprise Agreement firmy Microsoft.

- **FastTrack**.: Jeśli zakupiono licencje Enterprise Mobility and Security (EMS) lub Azure AD — wersja Premium, masz prawo do otrzymywania pomocy dotyczącej wdrażania z [programu FastTrack.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- **Zaangażuj zespół inżynierów produktu:** Jeśli pracujesz nad głównym wdrożeniem klienta za pomocą milionów użytkowników, masz prawo do pomocy technicznej z zespołu konto Microsoft lub architekta rozwiązań w chmurze. Na podstawie złożoności wdrożenia projektu można bezpośrednio współpracować z [zespołem inżynierów ds. tożsamości platformy Azure.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Blog dotyczący tożsamości usługi Azure AD:** Zasubskrybuj Blog dotyczący [tożsamości usługi Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) , aby uzyskać aktualność, korzystając ze wszystkich najnowszych ogłoszeń o produkcie, omówieniach szczegółowych informacji i przewodników udostępnianych bezpośrednio przez zespół inżynierów ds. tożsamości.
