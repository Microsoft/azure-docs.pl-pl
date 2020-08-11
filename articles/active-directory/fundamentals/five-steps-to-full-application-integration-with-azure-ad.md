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
ms.openlocfilehash: c75d04faf9ac59f21aad1dcd88dfe83699a11941
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057257"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Pięć kroków związanych z integracją wszystkich aplikacji z usługą Azure AD

Azure Active Directory (Azure AD) to usługa zarządzania tożsamościami i dostępem w chmurze firmy Microsoft. Usługa Azure AD zapewnia bezpieczne rozwiązania do uwierzytelniania i autoryzacji, dzięki czemu klienci, partnerzy i pracownicy mogą uzyskiwać dostęp do potrzebnych aplikacji. Za pomocą usługi Azure AD, [dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview), [uwierzytelniania wieloskładnikowego](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks), [logowania](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)jednokrotnego i [automatycznej aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) zapewniaj łatwe i bezpieczne zarządzanie tożsamościami i dostępem.

Jeśli Twoja firma ma Microsoft 365 subskrypcję, najkorzystniej [już korzystasz](https://docs.microsoft.com/office365/enterprise/about-office-365-identity) z usługi Azure AD. Usługa Azure AD może być jednak używana dla wszystkich aplikacji i [scentralizowanym zarządzaniem aplikacjami](https://docs.microsoft.com/azure/active-directory/manage-apps/common-scenarios) , dzięki którym można korzystać z tych samych funkcji zarządzania tożsamościami, narzędzi i zasad w całym portfolio aplikacji. Dzięki temu będzie można ujednolicone rozwiązanie, które zwiększa bezpieczeństwo, zmniejsza koszty, zwiększa produktywność i umożliwia zapewnienie zgodności. I uzyskasz dostęp zdalny do aplikacji lokalnych.

W tym przewodniku wyjaśniono, jak zintegrować wszystkie aplikacje z usługą Azure AD. W każdym kroku wyjaśnimy wartość i udostępniamy linki do zasobów, które objaśniają szczegóły techniczne. Firma Microsoft udostępnia te kroki w kolejności, w której zalecamy. Można jednak przejść do dowolnej części procesu, aby rozpocząć pracę z dowolną wartością.

Inne zasoby w tym temacie, w tym szczegółowe oficjalne dokumenty dotyczące procesów, które można znaleźć w naszych [zasobach na potrzeby migrowania aplikacji do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) stronie.

## <a name="1-use-azure-ad-for-new-applications"></a>1. Użyj usługi Azure AD w przypadku nowych aplikacji

Najpierw należy skoncentrować się na nowo uzyskanych aplikacjach. Gdy firma zacznie korzystać z nowej aplikacji, należy od razu [dodać do dzierżawy usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) . Skonfiguruj zasady firmy, dzięki czemu Dodawanie nowych aplikacji do usługi Azure AD jest standardową procedurą w organizacji. Jest to w minimalnym stopniu zakłócone istniejącym procesom biznesowym i pozwala zbadać i udowodnić wartość uzyskaną z integracji aplikacji bez konieczności zmiany sposobu, w jaki ludzie korzystają z tego środowiska.

Azure Active Directory (Azure AD) zawiera galerię zawierającą tysiące wstępnie zintegrowanych aplikacji, które ułatwiają rozpoczęcie pracy. Możesz [dodać aplikację galerii do swojej organizacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) , korzystając z [samouczków](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) krok po kroku w celu integracji z popularnymi aplikacjami, takimi jak:

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-tutorial)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)
- [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial)
- [AWS](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)
- [Slack](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)

Ponadto można [zintegrować aplikacje spoza galerii](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app), w tym wszystkie aplikacje, które już istnieją w organizacji, lub dowolną aplikację innej firmy od dostawcy, który nie jest jeszcze częścią galerii usługi Azure AD. Możesz również [dodać aplikację do galerii,](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-app-gallery-listing) Jeśli nie jest tam.

Na koniec możesz także zintegrować aplikacje opracowywane w firmie. Zostało to omówione w kroku 5 tego przewodnika.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. Ustal istniejące użycie aplikacji i ustaw priorytet pracy

Następnie odkryj często używane aplikacje, a następnie określ priorytet swojej pracy, aby zintegrować je z usługą Azure AD.

Możesz zacząć od używania narzędzi Microsoft Cloud App Security&#39;s [Cloud Discovery Tools](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) , aby odnajdywać i zarządzać nimi &quot; &quot; w sieci (czyli aplikacjami, które nie są zarządzane przez dział IT). Możesz [użyć zaawansowanej ochrony przed zagrożeniami (ATP) w usłudze Microsoft Defender](https://docs.microsoft.com/cloud-app-security/wdatp-integration) , aby uprościć i zwiększyć proces odnajdywania.

Ponadto można użyć [raportu AD FS działania aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) w Azure Portal, aby odnaleźć wszystkie AD FS aplikacje w organizacji, liczbę unikatowych użytkowników, którzy zalogowali się do nich i zgodność z integracją z usługą Azure AD.

Po znalezieniu istniejących krajobrazów należy [utworzyć plan](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) i określić priorytety aplikacji o najwyższym priorytecie do integracji. Oto przykładowe pytania, na które można zadać ten proces:

- Które aplikacje są najczęściej używane?
- Co to jest riskiest?
- Które aplikacje zostaną zlikwidowane w przyszłości, co sprawia, że niepotrzebne przeniesienie?
- Które aplikacje muszą pozostać w środowisku lokalnym i nie można ich przenieść do chmury?

Jeśli wszystkie Twoje aplikacje zostaną zintegrowane, zobaczysz największe korzyści i oszczędności, a nie będziesz już korzystać z wielu rozwiązań do tworzenia tożsamości. Można jednak ułatwić zarządzanie tożsamościami i zwiększyć poziom bezpieczeństwa podczas przechodzenia do tego celu. Chcesz użyć tej godziny, aby określić priorytet pracy i zdecydować, co ma sens w danej sytuacji.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. Zintegruj aplikacje zależne od innych dostawców tożsamości

Podczas procesu odnajdywania można znaleźć aplikacje, które nie są śledzone przez dział IT, co pozostawia dane i zasoby. Mogą również istnieć aplikacje korzystające z alternatywnych rozwiązań tożsamości, w tym Active Directory Federation Services (ADFS) lub innych dostawców tożsamości. Zastanów się nad tym, jak można skonsolidować Zarządzanie tożsamościami i dostępem, aby zaoszczędzić pieniądze i zwiększyć bezpieczeństwo. Zmniejszenie liczby posiadanych rozwiązań tożsamości:

- Oszczędzaj pieniądze, eliminując konieczność lokalnego aprowizacji i uwierzytelniania użytkowników oraz opłaty licencyjne płacone innym dostawcom tożsamości w chmurze dla tej samej usługi.
- Zmniejsz koszty administracyjne i zapewnij ściślejsze zabezpieczenia z mniejszą liczbą nadmiarowości w procesie zarządzania tożsamościami i dostępem.
- Umożliwiaj pracownikom uzyskanie bezpiecznego logowania jednokrotnego do wszystkich aplikacji, których potrzebują za pośrednictwem [portalu aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).
- Ulepszanie analizy usług Azure AD&#39;s usługi związane z [ochroną tożsamości](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) , takich jak dostęp warunkowy, zwiększając ilość danych pobieranych z użycia aplikacji i rozszerzając korzyści dla nowo dodanych aplikacji.

Opublikowano wskazówki dotyczące zarządzania procesem biznesowym integracji aplikacji z usługą Azure AD, w tym [plakatem](https://aka.ms/AppOnePager) i [prezentacją](https://aka.ms/AppGuideline) , za pomocą których można się dowiedzieć, czy właściciele firmy i aplikacji wiedzą. Możesz modyfikować te przykłady przy użyciu własnych oznaczeń i publikować je w swojej organizacji za pomocą portalu firmy, biuletynu lub innego nośnika, jak w przypadku kończenia tego procesu.

Dobrym miejscem do rozpoczęcia jest Ocena użycia Active Directory Federation Services (AD FS). Wiele organizacji korzysta z usług AD FS do uwierzytelniania za pomocą aplikacji SaaS, niestandardowych aplikacji biznesowych i aplikacji opartych na usłudze Office 365 i Azure AD:

![Diagram przedstawia aplikacje lokalne, aplikacje biznesowe, aplikacje SaaS oraz za pośrednictwem usługi Azure AD, pakiet Office 365 wszystkie łączące się z kropkowanymi liniami w Active Directory i AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Tę konfigurację można uaktualnić przez [zastąpienie usług AD FS usługą Azure AD jako centrum](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) rozwiązania do zarządzania tożsamościami. Pozwoli to na logowanie się dla każdej aplikacji, do której pracownicy chcą uzyskać dostęp, i ułatwia pracownikom znalezienie dowolnych aplikacji firmowych, których potrzebują za pośrednictwem [portalu aplikacji](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access), oprócz innych korzyści wymienionych powyżej.

![Diagram przedstawia aplikacje lokalne za pośrednictwem Active Directory i AD FS, aplikacji biznesowych, aplikacji SaaS i pakietu Office 365 wszystkie połączenia z kropkowanymi liniami w Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Gdy usługa Azure AD stanie się centralnym dostawcą tożsamości, może być możliwe przełączenie z usług ADFS w całości, a nie za pomocą rozwiązania federacyjnego. Aplikacje, które wcześniej korzystały z usług AD FS do uwierzytelniania, mogą teraz korzystać wyłącznie z usługi Azure AD.

![Na diagramie są wyświetlane lokalne, aplikacje biznesowe, aplikacje SaaS i pakiet Office 365 wszystkie łączące się z kropkowanymi liniami w Azure Active Directory. Nie ma Active Directory i AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

Możesz również migrować aplikacje korzystające z innego dostawcy tożsamości opartego na chmurze do usługi Azure AD. Organizacja może mieć wiele rozwiązań do zarządzania dostępem do tożsamości (IAM). Migrowanie do jednej infrastruktury usługi Azure AD umożliwia zredukowanie zależności od licencji na usługę IAM (lokalnie lub w chmurze) oraz kosztów infrastruktury. W przypadkach, gdy można już zapłacić za usługę Azure AD za pośrednictwem licencji M365, nie ma żadnego powodu, aby płacić za inne rozwiązanie IAM.

## <a name="4-integrate-on-premises-applications"></a>4. Integracja aplikacji lokalnych

Tradycyjnie aplikacje były zabezpieczone przez umożliwienie dostępu tylko podczas połączenia z siecią firmową. Jednak w coraz większym świecie chcemy zezwolić na dostęp do aplikacji klientom, partnerom i/lub pracownikom, niezależnie od tego, gdzie znajdują się na świecie. [Azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) (AppProxy) to funkcja usługi Azure AD, która łączy istniejące aplikacje lokalne z usługą Azure AD i nie wymaga zarządzania serwerami brzegowymi ani inną dodatkową infrastrukturą.

![Diagram przedstawia usługę serwera proxy aplikacji w działaniu. Użytkownik uzyskuje dostęp do " https://sales.contoso.com " i ich żądanie jest przekierowywane przez " https://sales-contoso.msappproxy.net " w Azure Active Directory do adresu lokalnego " http://sales "](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Możesz użyć [samouczka: Dodaj aplikację lokalną dla dostępu zdalnego za pomocą serwera proxy aplikacji w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) , aby włączyć serwer proxy aplikacji i dodać aplikację lokalną do dzierżawy usługi Azure AD.

Ponadto można zintegrować kontrolery dostarczania aplikacji, takie jak F5 Big-IP APM lub rozwiązania Zscaler prywatny dostęp. Integrując je z usługą Azure AD, uzyskasz nowoczesne uwierzytelnianie i zarządzanie tożsamościami usługi Azure AD wraz z funkcjami zarządzania ruchem i zabezpieczeniami w produkcie partnerskim. Nazywamy to rozwiązanie [bezpieczny dostęp hybrydowy](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). Jeśli dzisiaj używasz dowolnego z następujących usług, mamy samouczki, które przeprowadzi Cię przez proces integracji z usługą Azure AD.

- [Akamai dostęp do aplikacji (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)
- [Kontroler dostarczania aplikacji Citrix](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) (znany wcześniej jako Citrix
- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)
- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)

## <a name="5-integrate-apps-your-developers-build"></a>5. Integruj aplikacje, które są kompilowane przez deweloperów

W przypadku aplikacji, które są wbudowane w firmę, deweloperzy mogą używać [platformy tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) do implementowania uwierzytelniania i autoryzacji. Aplikacje zintegrowane z platformą mają być [zarejestrowane w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) i zarządzane tak samo jak w przypadku dowolnej innej aplikacji w portfolio.

Deweloperzy mogą używać platformy dla aplikacji wewnętrznych i aplikacji przeznaczonych dla klientów, a istnieją inne korzyści, które są dostępne w przypadku korzystania z platformy. [Biblioteki uwierzytelniania firmy Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview), które są częścią platformy, umożliwiają deweloperom tworzenie nowoczesnych środowisk, takich jak uwierzytelnianie wieloskładnikowe i korzystanie z kluczy zabezpieczeń do uzyskiwania dostępu do aplikacji bez konieczności ich wdrażania. Ponadto aplikacje zintegrowane z platformą tożsamości firmy Microsoft mogą uzyskać dostęp do [Microsoft Graph](https://docs.microsoft.com/azure/active-directory/develop/microsoft-graph-intro) — ujednolicony punkt końcowy interfejsu API, który zapewnia Microsoft 365 dane, które opisują wzorce produktywności, tożsamości i zabezpieczeń w organizacji. Deweloperzy mogą używać tych informacji do implementowania funkcji, które zwiększają produktywność użytkowników. Na przykład, identyfikując osoby, z których użytkownik był ostatnio pracowali, i obłączy je w interfejsie użytkownika&#39;s aplikacji.

Mamy [serię filmów wideo](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) , która zapewnia kompleksowy wprowadzenie do platformy, a także [wiele przykładów kodu](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) w obsługiwanych językach i platformach.

## <a name="next-steps"></a>Następne kroki

- [Zasoby do migrowania aplikacji do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)
