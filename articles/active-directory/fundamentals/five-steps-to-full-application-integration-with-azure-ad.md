---
title: Pięć kroków związanych z integracją wszystkich aplikacji z usługą Azure AD
description: W tym przewodniku wyjaśniono, jak zintegrować wszystkie aplikacje z usługą Azure AD. W każdym kroku wyjaśnimy wartość i udostępniamy linki do zasobów, które objaśniają szczegóły techniczne.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: 7fd9e504448d55b4a2ef8c10b4ba1176cb2e3402
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172639"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Pięć kroków związanych z integracją wszystkich aplikacji z usługą Azure AD

Azure Active Directory (Azure AD) to usługa zarządzania tożsamościami i dostępem w chmurze firmy Microsoft. Usługa Azure AD zapewnia bezpieczne rozwiązania do uwierzytelniania i autoryzacji, dzięki czemu klienci, partnerzy i pracownicy mogą uzyskiwać dostęp do potrzebnych aplikacji. Za pomocą usługi Azure AD, [dostępu warunkowego](../conditional-access/overview.md), [uwierzytelniania wieloskładnikowego](../authentication/concept-mfa-howitworks.md), [logowania](../hybrid/how-to-connect-sso.md)jednokrotnego i [automatycznej aprowizacji użytkowników](../app-provisioning/user-provisioning.md) zapewniaj łatwe i bezpieczne zarządzanie tożsamościami i dostępem.

Jeśli Twoja firma ma Microsoft 365 subskrypcję, najkorzystniej [już korzystasz](/office365/enterprise/about-office-365-identity) z usługi Azure AD. Usługa Azure AD może być jednak używana dla wszystkich aplikacji i [scentralizowanym zarządzaniem aplikacjami](../manage-apps/common-scenarios.md) , dzięki którym można korzystać z tych samych funkcji zarządzania tożsamościami, narzędzi i zasad w całym portfolio aplikacji. Dzięki temu będzie można ujednolicone rozwiązanie, które zwiększa bezpieczeństwo, zmniejsza koszty, zwiększa produktywność i umożliwia zapewnienie zgodności. I uzyskasz dostęp zdalny do aplikacji lokalnych.

W tym przewodniku wyjaśniono, jak zintegrować wszystkie aplikacje z usługą Azure AD. W każdym kroku wyjaśnimy wartość i udostępniamy linki do zasobów, które objaśniają szczegóły techniczne. Firma Microsoft udostępnia te kroki w kolejności, w której zalecamy. Można jednak przejść do dowolnej części procesu, aby rozpocząć pracę z dowolną wartością.

Inne zasoby w tym temacie, w tym szczegółowe oficjalne dokumenty dotyczące procesów, które można znaleźć w naszych [zasobach na potrzeby migrowania aplikacji do Azure Active Directory](../manage-apps/migration-resources.md) stronie.

## <a name="1-use-azure-ad-for-new-applications"></a>1. Użyj usługi Azure AD w przypadku nowych aplikacji

Najpierw należy skoncentrować się na nowo uzyskanych aplikacjach. Gdy firma zacznie korzystać z nowej aplikacji, należy od razu [dodać do dzierżawy usługi Azure AD](../manage-apps/add-application-portal.md) . Skonfiguruj zasady firmy, dzięki czemu Dodawanie nowych aplikacji do usługi Azure AD jest standardową procedurą w organizacji. Jest to w minimalnym stopniu zakłócone istniejącym procesom biznesowym i pozwala zbadać i udowodnić wartość uzyskaną z integracji aplikacji bez konieczności zmiany sposobu, w jaki ludzie korzystają z tego środowiska.

Azure Active Directory (Azure AD) zawiera galerię zawierającą tysiące wstępnie zintegrowanych aplikacji, które ułatwiają rozpoczęcie pracy. Możesz [dodać aplikację galerii do swojej organizacji usługi Azure AD](../manage-apps/add-application-portal.md) , korzystając z [samouczków](../saas-apps/tutorial-list.md) krok po kroku w celu integracji z popularnymi aplikacjami, takimi jak:

- [ServiceNow](../saas-apps/servicenow-tutorial.md)
- [Workday](../saas-apps/workday-tutorial.md)
- [Salesforce](../saas-apps/salesforce-tutorial.md)
- [AWS](../saas-apps/amazon-web-service-tutorial.md)
- [Slack](../saas-apps/slack-tutorial.md)

Ponadto można [zintegrować aplikacje spoza galerii](../manage-apps/view-applications-portal.md), w tym wszystkie aplikacje, które już istnieją w organizacji, lub dowolną aplikację innej firmy od dostawcy, który nie jest jeszcze częścią galerii usługi Azure AD. Możesz również [dodać aplikację do galerii,](../develop/v2-howto-app-gallery-listing.md) Jeśli nie jest tam.

Na koniec możesz także zintegrować aplikacje opracowywane w firmie. Zostało to omówione w kroku 5 tego przewodnika.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. Ustal istniejące użycie aplikacji i ustaw priorytet pracy

Następnie odkryj często używane aplikacje, a następnie określ priorytet swojej pracy, aby zintegrować je z usługą Azure AD.

Możesz zacząć od używania [narzędzi do wykrywania w chmurze](/cloud-app-security/tutorial-shadow-it) Microsoft Cloud App Security, aby odnaleźć "cień" w sieci (czyli aplikacje niezarządzane przez dział IT i zarządzać nim). Możesz [użyć zaawansowanej ochrony przed zagrożeniami (ATP) w usłudze Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) , aby uprościć i zwiększyć proces odnajdywania.

Ponadto można użyć [raportu AD FS działania aplikacji](../manage-apps/migrate-adfs-application-activity.md) w Azure Portal, aby odnaleźć wszystkie AD FS aplikacje w organizacji, liczbę unikatowych użytkowników, którzy zalogowali się do nich i zgodność z integracją z usługą Azure AD.

Po znalezieniu istniejących krajobrazów należy [utworzyć plan](../manage-apps/migration-resources.md) i określić priorytety aplikacji o najwyższym priorytecie do integracji. Oto przykładowe pytania, na które można zadać ten proces:

- Które aplikacje są najczęściej używane?
- Co to jest riskiest?
- Które aplikacje zostaną zlikwidowane w przyszłości, co sprawia, że niepotrzebne przeniesienie?
- Które aplikacje muszą pozostać w środowisku lokalnym i nie można ich przenieść do chmury?

Jeśli wszystkie Twoje aplikacje zostaną zintegrowane, zobaczysz największe korzyści i oszczędności, a nie będziesz już korzystać z wielu rozwiązań do tworzenia tożsamości. Można jednak ułatwić zarządzanie tożsamościami i zwiększyć poziom bezpieczeństwa podczas przechodzenia do tego celu. Chcesz użyć tej godziny, aby określić priorytet pracy i zdecydować, co ma sens w danej sytuacji.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. Zintegruj aplikacje zależne od innych dostawców tożsamości

Podczas procesu odnajdywania można znaleźć aplikacje, które nie są śledzone przez dział IT, co pozostawia dane i zasoby. Mogą również istnieć aplikacje korzystające z alternatywnych rozwiązań tożsamości, w tym Active Directory Federation Services (ADFS) lub innych dostawców tożsamości. Zastanów się nad tym, jak można skonsolidować Zarządzanie tożsamościami i dostępem, aby zaoszczędzić pieniądze i zwiększyć bezpieczeństwo. Zmniejszenie liczby posiadanych rozwiązań tożsamości:

- Oszczędzaj pieniądze, eliminując konieczność lokalnego aprowizacji i uwierzytelniania użytkowników oraz opłaty licencyjne płacone innym dostawcom tożsamości w chmurze dla tej samej usługi.
- Zmniejsz koszty administracyjne i zapewnij ściślejsze zabezpieczenia z mniejszą liczbą nadmiarowości w procesie zarządzania tożsamościami i dostępem.
- Umożliwiaj pracownikom uzyskanie bezpiecznego logowania jednokrotnego do wszystkich aplikacji, których potrzebują za pośrednictwem [portalu aplikacji](../manage-apps/access-panel-collections.md).
- Zwiększ możliwości analizy usług związanych z [ochroną tożsamości](../identity-protection/overview-identity-protection.md) usługi Azure AD, takich jak dostęp warunkowy, zwiększając ilość danych pobieranych z użycia aplikacji i zwiększ korzyści dla nowo dodanych aplikacji.

Opublikowano wskazówki dotyczące zarządzania procesem biznesowym integracji aplikacji z usługą Azure AD, w tym [plakatem](https://aka.ms/AppOnePager) i [prezentacją](https://aka.ms/AppGuideline) , za pomocą których można się dowiedzieć, czy właściciele firmy i aplikacji wiedzą. Możesz modyfikować te przykłady przy użyciu własnych oznaczeń i publikować je w swojej organizacji za pomocą portalu firmy, biuletynu lub innego nośnika, jak w przypadku kończenia tego procesu.

Dobrym miejscem do rozpoczęcia jest Ocena użycia Active Directory Federation Services (AD FS). Wiele organizacji korzysta z usług AD FS do uwierzytelniania za pomocą aplikacji SaaS, niestandardowych aplikacji biznesowych Microsoft 365 i aplikacji opartych na usłudze Azure AD:

![Diagram przedstawia aplikacje lokalne, aplikacje biznesowe, aplikacje SaaS oraz za pośrednictwem usługi Azure AD, pakiet Office 365 wszystkie łączące się z kropkowanymi liniami w Active Directory i AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Tę konfigurację można uaktualnić przez [zastąpienie usług AD FS usługą Azure AD jako centrum](../manage-apps/migrate-adfs-apps-to-azure.md) rozwiązania do zarządzania tożsamościami. Pozwoli to na logowanie się dla każdej aplikacji, do której pracownicy chcą uzyskać dostęp, i ułatwia pracownikom znalezienie dowolnych aplikacji firmowych, których potrzebują za pośrednictwem [portalu aplikacji](../user-help/my-apps-portal-end-user-access.md), oprócz innych korzyści wymienionych powyżej.

![Diagram przedstawia aplikacje lokalne za pośrednictwem Active Directory i AD FS, aplikacji biznesowych, aplikacji SaaS i pakietu Office 365 wszystkie połączenia z kropkowanymi liniami w Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Gdy usługa Azure AD stanie się centralnym dostawcą tożsamości, może być możliwe przełączenie z usług ADFS w całości, a nie za pomocą rozwiązania federacyjnego. Aplikacje, które wcześniej korzystały z usług AD FS do uwierzytelniania, mogą teraz korzystać wyłącznie z usługi Azure AD.

![Na diagramie są wyświetlane lokalne, aplikacje biznesowe, aplikacje SaaS i pakiet Office 365 wszystkie łączące się z kropkowanymi liniami w Azure Active Directory. Nie ma Active Directory i AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

Możesz również migrować aplikacje korzystające z innego dostawcy tożsamości opartego na chmurze do usługi Azure AD. Organizacja może mieć wiele rozwiązań do zarządzania dostępem do tożsamości (IAM). Migrowanie do jednej infrastruktury usługi Azure AD umożliwia zredukowanie zależności od licencji na usługę IAM (lokalnie lub w chmurze) oraz kosztów infrastruktury. W przypadkach, gdy można już zapłacić za usługę Azure AD za pośrednictwem licencji M365, nie ma żadnego powodu, aby płacić za inne rozwiązanie IAM.

## <a name="4-integrate-on-premises-applications"></a>4. Integracja aplikacji lokalnych

Tradycyjnie aplikacje były zabezpieczone przez umożliwienie dostępu tylko podczas połączenia z siecią firmową. Jednak w coraz większym świecie chcemy zezwolić na dostęp do aplikacji klientom, partnerom i/lub pracownikom, niezależnie od tego, gdzie znajdują się na świecie. [Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/what-is-application-proxy.md) (AppProxy) to funkcja usługi Azure AD, która łączy istniejące aplikacje lokalne z usługą Azure AD i nie wymaga zarządzania serwerami brzegowymi ani inną dodatkową infrastrukturą.

![Diagram przedstawia usługę serwera proxy aplikacji w działaniu. Użytkownik uzyskuje dostęp do " https://sales.contoso.com " i ich żądanie jest przekierowywane przez " https://sales-contoso.msappproxy.net " w Azure Active Directory do adresu lokalnego " http://sales "](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Możesz użyć [samouczka: Dodaj aplikację lokalną dla dostępu zdalnego za pomocą serwera proxy aplikacji w Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md) , aby włączyć serwer proxy aplikacji i dodać aplikację lokalną do dzierżawy usługi Azure AD.

Ponadto można zintegrować kontrolery dostarczania aplikacji, takie jak F5 Big-IP APM lub rozwiązania Zscaler prywatny dostęp. Integrując je z usługą Azure AD, uzyskasz nowoczesne uwierzytelnianie i zarządzanie tożsamościami usługi Azure AD wraz z funkcjami zarządzania ruchem i zabezpieczeniami w produkcie partnerskim. Nazywamy to rozwiązanie [bezpieczny dostęp hybrydowy](../manage-apps/secure-hybrid-access.md). Jeśli dzisiaj używasz dowolnego z następujących usług, mamy samouczki, które przeprowadzi Cię przez proces integracji z usługą Azure AD.

- [Akamai dostęp do aplikacji (EAA)](../saas-apps/akamai-tutorial.md)
- [Kontroler dostarczania aplikacji Citrix](../saas-apps/citrix-netscaler-tutorial.md) (znany wcześniej jako Citrix
- [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)
- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)

## <a name="5-integrate-apps-your-developers-build"></a>5. Integruj aplikacje, które są kompilowane przez deweloperów

W przypadku aplikacji, które są wbudowane w firmę, deweloperzy mogą używać [platformy tożsamości firmy Microsoft](../develop/index.yml) do implementowania uwierzytelniania i autoryzacji. Aplikacje zintegrowane z platformą mają być [zarejestrowane w usłudze Azure AD](../develop/quickstart-register-app.md) i zarządzane tak samo jak w przypadku dowolnej innej aplikacji w portfolio.

Deweloperzy mogą używać platformy dla aplikacji wewnętrznych i aplikacji przeznaczonych dla klientów, a istnieją inne korzyści, które są dostępne w przypadku korzystania z platformy. [Biblioteki uwierzytelniania firmy Microsoft (MSAL)](../develop/msal-overview.md), które są częścią platformy, umożliwiają deweloperom tworzenie nowoczesnych środowisk, takich jak uwierzytelnianie wieloskładnikowe i korzystanie z kluczy zabezpieczeń do uzyskiwania dostępu do aplikacji bez konieczności ich wdrażania. Ponadto aplikacje zintegrowane z platformą tożsamości firmy Microsoft mogą uzyskać dostęp do [Microsoft Graph](../develop/microsoft-graph-intro.md) — ujednolicony punkt końcowy interfejsu API, który zapewnia Microsoft 365 dane, które opisują wzorce produktywności, tożsamości i zabezpieczeń w organizacji. Deweloperzy mogą używać tych informacji do implementowania funkcji, które zwiększają produktywność użytkowników. Na przykład, identyfikując osoby, z których użytkownik był ostatnio pracowali, i umieszcza je w interfejsie użytkownika aplikacji.

Mamy [serię filmów wideo](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) , która zapewnia kompleksowy wprowadzenie do platformy, a także [wiele przykładów kodu](../develop/sample-v2-code.md) w obsługiwanych językach i platformach.

## <a name="next-steps"></a>Następne kroki

- [Zasoby pomocne przy migrowaniu aplikacji do usługi Azure Active Directory](../manage-apps/migration-resources.md)