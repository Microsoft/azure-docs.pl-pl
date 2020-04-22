---
title: Wymagania według typu aukcji | Azure
description: W tym artykule opisano kryteria uprawnień i wymagania dotyczące publikowania partnerów próbujących zrozumieć, jak publikować aplikacje w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: dsindona
ms.openlocfilehash: c9936e9c406e262c06d9016b88f8999e46dcb917
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684359"
---
# <a name="requirements-by-listing-type"></a>Wymagania dotyczące poszczególnych typów ofert  
Wymagania dotyczące zawartości technicznej i marketingowej różnią się w zależności od witryny sklepu, typu oferty i typu aukcji. Zapoznaj się z poniższymi specyfikacjami, aby zweryfikować zgodność.  
1. Wymagania sklepu:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Wymagania dotyczące typu aukcji i typu oferty:  
    *   Aby uzyskać więcej informacji na temat typów aukcji i typów ofert, odwiedź stronę Określanie typu aukcji dla rozwiązania znajdującą się w [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Wymagania sklepu: AppSource  
W poniższej tabeli opisano wymagania wstępne dotyczące publikowania w udziale w udziale w udziale aplikacji AppSource.  

| Wymaganie | Szczegóły | Wymagane czy Zalecane |  
|:--- |:--- |:--- |  
| ***Usługa Azure Active Directory (Usługa Azure AD)*** | Aplikacja musi zezwalać na usługę Azure Active Directory federacyjnej logowania jednokrotnego (Azure AD federrated SSO) z włączoną zgodą.<ul> <li>Aby uzyskać więcej informacji na temat włączania usługi Azure AD federacyjnego logowania jednokrotnego, odwiedź konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się na stronie galerii aplikacji usługi Azure Active Directory znajdującej się w [docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Wymagany |   
| ***Integracja z usługami Microsoft Cloud Services*** | Aplikacja powinna być integrana z innymi usługami Microsoft Cloud, takimi jak Microsoft Power BI, Cortana Intelligence lub Microsoft Azure services.<ul> <li>Przykładem usługi Microsoft Cloud jest Internet rzeczy.</li> </ul> | Zalecane |  
| ***Grupy odbiorców*** | Aplikacja musi być dla użytkowników biznesowych i właścicieli firm. | Wymagany | 
| ***Aplikacja Oprogramowanie jako usługa (SaaS) dla firm*** | Aplikacja musi spełniać następujące wymagania.<ul> <li>Aplikacja SaaS dla linii biznesowej</li> <li>Ukierunkowanie na proces biznesowy</li> <li>Skierowane do klientów biznesowych</li> <li>Umożliwianie użytkownikom używania poświadczeń służbowych do logowania się, takich jak nazwa użytkownika i hasło</li> </ul> | Wymagany |  
| ***Bezpłatny okres próbny i doświadczenie próbne*** | Aplikacja musi zawierać jedną z następujących opcji, aby klient mógł bezpłatnie korzystać z aplikacji przez ograniczony czas.<ul> <li>Podaj `try` metodę, aby klienci mogli rozpocząć wersję próbną aplikacji w u źródła usług AppSource</li> <li>Podaj `request trial` opcję w źródle aplikacji, aby klienci mogli zażądać wersji próbnej aplikacji</li> </ul>Bezpłatna wersja próbna, którą podasz, musi oferować klientowi wstępnie ustawiony czas na wypróbowanie aplikacji bez dodatkowych kosztów. | Wymagany |  
| ***Łatwe konfigurowalne, gotowe do użycia rozwiązanie*** | Aplikacja musi być łatwa i szybka w konfiguracji i konfiguracji bez konieczności dostosowywania. | Wymagany |  
| ***Zarządzanie potencjalnymi klientami*** | Włącz crm zaakceptować dane potencjalnych klientów przed otrzymaniem potencjalnych klientów z witryny sklepu.<ul> <li>Przykładami programów CRM są Marketo, Microsoft Dynamics lub Salesforce</li> </ul> | Wymagany |  
| ***Polityka prywatności i warunki użytkowania*** | Aplikacja musi zawierać link do strony polityki prywatności przy użyciu publicznego adresu URL. Warunki użytkowania muszą być podane podczas publikowania jako tekst. | Wymagany |  
| ***Pomoc techniczna*** | Aplikacja musi zawierać łącze do strony obsługi klienta przy użyciu publicznego adresu URL. Jeśli aplikacja jest próbna, należy obsługiwać bez dodatkowych kosztów w okresie próbnym. | Wymagany |  

## <a name="storefront-requirements-azure-marketplace"></a>Wymagania witryny sklepu: witryna Azure Marketplace  
Poniżej przedstawiono wymagania wstępne dotyczące typów aukcji w portalu Azure Marketplace.  

| Wymaganie | Szczegóły | Typ aukcji |  
|:--- |:--- |:--- |  
| ***Zasady uczestnictwa*** | Aplikacja musi być zgodna z zasadami uczestnictwa w portalu Azure Marketplace.<ul> <li>Aby uzyskać więcej informacji na temat zasad uczestnictwa, odwiedź stronę Zasady uczestnictwa w portalu Azure Marketplace znajdującą się pod [adresem azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />Transact<br />trial |  
| ***Integracja z firmą Microsoft*** | Oferta powinna używać lub rozszerzać typy usług platformy Microsoft Azure, takie jak obliczenia, sieci lub magazyn. Oferta powinna być wyrównana do istniejącej kategorii portalu Azure Marketplace, takiej jak bazy danych, zabezpieczenia lub sieci.<ul> <li>Aby uzyskać więcej informacji o ofertach marketplace, odwiedź stronę Aplikacji marketplace znajdującą się pod [adresem azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />Transact<br />trial |  
| ***Grupy odbiorców*** | Oferta musi być dla profesjonalistów IT, deweloperów chmury lub innych ról klientów technicznych. | list<br />Transact<br />trial |  
| ***Zarządzanie potencjalnymi klientami*** | Włącz program CRM (Marketo, Microsoft Dynamics lub Salesforce) w celu zaakceptowania danych potencjalnych klientów przed otrzymaniem potencjalnych klientów od witryny sklepu. | list<br />Transact<br />trial |  
| ***Polityka prywatności i warunki użytkowania*** | Aplikacja musi zawierać link do strony polityki prywatności przy użyciu publicznego adresu URL. Warunki użytkowania muszą być podane podczas publikowania jako tekst. | list<br />Transact<br />trial |  
| ***Pomoc techniczna*** | Oferta musi zawierać link do strony obsługi klienta przy użyciu publicznego adresu URL. Jeśli twoja oferta jest próbna, musisz wspierać bez dodatkowych kosztów w okresie próbnym. | Transact<br />trial |    

## <a name="non-transact-listings"></a>Aukcje nieprzekonacyjne  
W tej sekcji opisano wszystkie typy ofert, które nie używają typu aukcji Transact. 

### <a name="list"></a>List  
Typ listy zawiera następujące typy oferty na stronach sklepowych w portalu marketplace.  

| Typ oferty | Sklepu | Szczegóły |  
|:---        |:---        |:---     |  
| Usługi konsultingowe | AppSource | Wymagania: AppSource: Lista: Usługi konsultingowe |  
| Usługi konsultingowe | Azure Marketplace | Wymagania: Azure Marketplace: Lista: Usługi konsultingowe |  
| Skontaktuj się ze mną | AppSource | [](#) |  
| Skontaktuj się ze mną | Azure Marketplace | Wymagania: AppSource: Lista: Skontaktuj się ze mną |  

#### <a name="requirements-appsource-list-consulting-service"></a>Wymagania: AppSource: Lista: Usługa konsultingowa  

| Wymagania | Szczegóły |  
|:--- |:--- |  
| Charakterystyka oferty serwisowej | Twoja usługa konsultingowa musi spełniać następujące kryteria.<ul> <li>Zapewnij zaangażowanie o stałym zakresie, stałym czasie trwania, stałej cenie (lub bezpłatnej).</li> <li>Orient przede wszystkim do przedsprzedaży.</li> <li>Ograniczenie do jednego klienta.</li> <li>Prowadzenie na miejscu.</li> </ul> |  
| Wymagania partnerów dotyczące usług konsultingowych | Spełniasz kryteria w danym obszarze dla twojej usługi.<table><tr><th>Obszar rozwiązania</th><th>Kryteria</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Posiadaj kompetencję zarządzania relacjami z klientami w chmurze Silver lub Gold.</td></tr><tr><td>Dynamics 365 dla finansów i operacji, wersja Enterprise</td><td>Posiadaj kompetencje Silver lub Gold Enterprise Resource Planning i przychody z operacji w chmurze w ciągu 12 miesięcy 25 000 USD lub więcej.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Wersja biznesowa</td><td>Służyć jako dostawca usług w chmurze (CSP) lub cyfrowy partner rekordu (DPOR) dla jednego lub więcej klientów.</td></tr><tr><td>Power BI</td><td>Spełnij kryteria partnera rozwiązania.</td></tr><tr><td>PowerApps</td><td>Zaprezentuj partnerów.</td></tr></table><ul> <li>Aby uzyskać więcej informacji na temat zarządzania relacjami z klientami, odwiedź stronę Zarządzanie relacjami z klientami w chmurze znajdującą się pod [adresem partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Aby uzyskać więcej informacji na temat planowania zasobów, odwiedź stronę Planowania zasobów organizacji znajdującą się w [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Aby uzyskać więcej informacji na temat dostawcy usług kryptograficznych, odwiedź stronę dostawcy usług w chmurze znajdującą się pod [adresem partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Aby uzyskać więcej informacji na temat DPOR, odwiedź stronę Digital Partner of Record and Partner Association znajdującą się pod [adresem partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Aby uzyskać więcej informacji na temat kryteriów partnerów rozwiązania, [https://partner.microsoft.com/en-us/membership/partner-incentives](https://partner.microsoft.com/en-us/membership/partner-incentives)odwiedź dokument Przegląd i zachęty dla partnerów rozwiązań znajdujący się pod adresem .</li> <li>Aby uzyskać więcej informacji na temat prezentacji partnerów, odwiedź stronę Prezentacja partnera znajdującą się w [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Wymagania: Azure Marketplace: Lista: Usługa konsultingowa  

| Wymagania | Szczegóły |  
|:--- |:--- |  
| Charakterystyka oferty serwisowej | Twoja usługa konsultingowa musi spełniać następujące kryteria.<ul> <li>Zapewnij zaangażowanie o stałym zakresie, stałym czasie trwania, stałej cenie (lub bezpłatnej).</li> <li>Orient przede wszystkim do przedsprzedaży.</li> <li>Ograniczenie do jednego klienta.</li> <li>Prowadzenie na miejscu.</li> </ul> |  
| Wymagania partnerów dotyczące usług konsultingowych | Musisz mieć srebro lub złoto w jednej z następujących kompetencji w danym obszarze dla twojej usługi. <table><tr><th>Obszar rozwiązania</th><th>Kompetencji</th></tr><td>Platforma chmurowa i infrastruktura</td><td>Platforma chmurowa<br />Centrum danych</td><tr><td>Tworzenie aplikacji i isv</td><td>Projektowanie aplikacji<br />Integracja aplikacji<br />DevOps</td></tr><tr><td>Zarządzanie danymi i analiza danych</td><td>Analiza danych<br />Platforma danych</td></tr></table><ul> <li>Aby uzyskać więcej informacji na temat kompetencji, odwiedź stronę Kompetencje za pośrednictwem sieci partnerów firmy Microsoft, znajdującą się pod [adresem partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Aby uzyskać więcej informacji na temat aukcji, odwiedź stronę usług konsultingowych portalu Azure Marketplace, która znajduje się pod [adresem docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Wersja próbna  

| Typ oferty | Sklepu | Szczegóły |  
|:---        |:---        |:---     |  
| Bezpłatna / próba SaaS | AppSource | Wymagania dotyczące typu aukcji: Wersja próbna |  
| Bezpłatna / próba SaaS | Azure Marketplace | Wymagania: Azure Marketplace: Wersja próbna: bezpłatna wersja próbna / wersja próbna SaaS |  
| Interaktywny pokaz | AppSource | Wymagania dotyczące typu aukcji: Wersja próbna |  
| Interaktywny pokaz | Azure Marketplace | [Wymagania: Azure Marketplace: Wersja próbna: interaktywne demo](#requirements-azure-marketplace-trial-interactive-demo) |  
| Jazda próbna | AppSource | Wymagania dotyczące typu aukcji: Wersja próbna |  
| Jazda próbna | Azure Marketplace | [Wymagania: Usługa Azure Marketplace: wersja próbna: dysk testowy](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Wymagania: Azure Marketplace: Wersja próbna  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Bezpłatny okres próbny i doświadczenie próbne | Klient może korzystać z aplikacji bezpłatnie przez ograniczony czas.<br /><br />Klient nie jest zobowiązany do uiszczenia żadnych opłat licencyjnych lub subskrypcyjnych za ofertę lub aplikację. Klient nie musi płacić za podstawowy produkt lub usługę firmy Microsoft. Wszystkie opcje wersji próbnej są wdrażane w ramach subskrypcji platformy Azure. Masz jedyną próbę kontrolną optymalizacji kosztów i zarządzania.<br /><br />Możesz wybrać bezpłatną wersję próbną, interaktywne demo lub jazdę próbną. Bez względu na to, co wybierzesz, bezpłatna wersja próbna musi oferować klientowi wstępnie ustawiony czas na wypróbowanie aplikacji bez dodatkowych kosztów.<ul> <li>Aby rozpocząć proces tworzenia dysku testowego, wyślij [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com)wiadomość e-mail na adres .</li> </ul>Uwaga: Środowisko testowe usługi Azure Marketplace SaaS musi umożliwiać klientom używanie poświadczeń pracy do logowania się.<ul> <li>Aby uzyskać więcej informacji, odwiedź sekcję środowiska próbnego usługi AppSource znajdującą się w [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Łatwe konfigurowalne, gotowe do użycia rozwiązanie | Konfiguracja i konfiguracja aplikacji musi być łatwa i szybka. |  
| Dostępność / czas pracy bez przestojów | Twoja aplikacja lub platforma SaaS musi mieć czas pracy co najmniej 99,9%. |  
| Azure Active Directory | Oferta musi zezwalać na usługę Azure Active Directory (Azure AD) federacyjnej rejestracji jednokrotnej (SSO) (Azure AD federacyjne logowanie jednokrotne) z włączoną zgodą. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Wymagania: Azure Marketplace: Wersja próbna: bezpłatna wersja próbna / wersja próbna SaaS  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientowi wypróbowanie produktu przed zakupem za pomocą zautomatyzowanej metody konwersji na płatne użycie. Umożliwia również potwierdzenie koncepcji dla klienta i wspólne zaangażowanie z zespołami sprzedaży firmy Microsoft. | Rozwiązanie jest maszyną wirtualną lub szablonem rozwiązania.<br /><br />Twoje rozwiązanie to oferta SaaS i oferujesz produkt SaaS wielodostępny.<br /><br />Masz doświadczenie pierwszego uruchomienia, aby szybko uruchomić klienta.<br /><br />Masz jedną dzierżawę, ale dodajesz klientów jako użytkowników-gości. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Wymagania: Azure Marketplace: Wersja próbna: interaktywne demo  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientom zobaczenie rozwiązania w działaniu bez złożoności konfigurowania. | Rozwiązanie wymaga złożonych ustawień, które byłyby trudne do osiągnięcia w okresie próbnym. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Wymagania: Usługa Azure Marketplace: wersja próbna: dysk testowy  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientowi wypróbowanie produktu przed zakupem.<br /><br />Zapewnia środowisko z przewodnikiem rozwiązania przy użyciu wstępnie skonfigurowanych ustawień.<br /><br />Poniżej przedstawiono dodatkowe korzyści podczas korzystania z jazdy próbne.<ul> <li>27% wyszukiwań użytkowników na rynku jest udoskonalanych przez użytkowników, aby wyświetlać tylko oferty z napędami testowymi.</li> <li>Oferty z napędami testowymi generują o 38% więcej potencjalnych klientów niż oferty bez.</li> <li>36% nowych przejęć klientów na rynku pochodzi od klientów, którzy wzięli udział w próbie.</li> <li>Dyski testowe umożliwiają sprzedawcom terenowym firmy Microsoft lepsze zrozumienie produktu pod kątem współpracy ze sprzedażą.</li> </ul> | Rozwiązanie to maszyna wirtualna, szablon rozwiązania lub aplikacja SaaS z jedną dzierżawą lub jest skomplikowana do aprowizowania. <br /><br />Nie masz metody konwersji wersji próbnej na płatną ofertę. |  

---

## <a name="transact-specific-listings"></a>Aukcje specyficzne dla transakcji

### <a name="transact"></a>Transact  

| Typ oferty | Sklepu | Szczegóły |   
|:---        |:---        | :--- |  
| Aplikacje platformy Azure: aplikacja zarządzana | Azure Marketplace | Wymagania: Azure Marketplace: Transact: Aplikacje platformy Azure: aplikacja zarządzana |  
| Aplikacje platformy Azure: szablon rozwiązania | Azure Marketplace | Wymagania: Azure Marketplace: Transact: Aplikacje platformy Azure: Szablon rozwiązania |  
| Containers | Azure Marketplace | [Wymagania: Azure Marketplace: Transact: Container](#requirements-azure-marketplace-transact-container) |  
| Aplikacja SaaS  | Azure Marketplace | [Wymagania: Azure Marketplace: Transact: Aplikacja SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Maszyna wirtualna | Azure Marketplace | [Wymagania: Azure Marketplace: Transact: Maszyna wirtualna](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Wymagania: Azure Marketplace: Transact: Container  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiary | Obsługa bezpłatnego lub byolowego modelu rozliczeniowego. |  
| Obraz oparty na docker | Obraz kontenera musi być oparty na formacie obrazu platformy Docker i musi zostać pobrany z rejestratorów kontenerów platformy Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Wymagania: Azure Marketplace: Transact: Aplikacja SaaS  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiary | Twoja oferta jest wyceniana według miesięcznej stawki ryczałtowej. Ceny oparte na użyciu i opcje *true-up* oparte na użyciu nie są obecnie obsługiwane. |  
| Anulowanie | Twoja oferta jest anulowana przez klienta w dowolnym momencie. |  
| Strona docelowa transakcji | Hostuj stronę docelową transakcji współmarkowej platformy Azure. Strona docelowa umożliwia klientom tworzenie konta usługi SaaS i zarządzanie nim. |  
| Interfejs API subskrypcji usługi SaaS | Zapewnij usługę, która współdziała z subskrypcją SaaS, aby utworzyć, zaktualizować i usunąć konto użytkownika i plan usługi. Wszystkie krytyczne zmiany interfejsu API muszą być obsługiwane w ciągu 24 godzin. Wszelkie niekrytyczne zmiany interfejsu API są okresowo aktualizowane. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Wymagania: Azure Marketplace: Transact: Maszyna wirtualna  

| Wymaganie | Szczegóły |  
|:--- |:--- | 
| Rozliczenia i pomiary | Maszyna wirtualna musi obsługiwać miesięczne rozliczenia według systemu BYOL lub płatności zgodnie z rzeczywistym i wyjazdem. |  
| Wirtualny dysk twardy zgodny z platformą Azure (VHD) | Maszyny wirtualne muszą być zbudowane w systemie Windows lub Linux.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Linux, zobacz [Dystrybucje systemu Linux zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Windows, zobacz [Tworzenie dysku VHD zgodnego z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Następne kroki
*   Odwiedź stronę [Przewodnik po portalu Azure Marketplace i Przewodnik dla wydawców appsource.](./marketplace-publishers-guide.md)  

