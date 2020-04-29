---
title: Wymagania według typu listy | Azure
description: W tym artykule opisano kryteria kwalifikacyjne i wymagania dotyczące publikowania partnerów próbujących zrozumieć sposób publikowania aplikacji w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: dsindona
ms.openlocfilehash: c9936e9c406e262c06d9016b88f8999e46dcb917
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684359"
---
# <a name="requirements-by-listing-type"></a>Wymagania dotyczące poszczególnych typów ofert  
Wymagania dotyczące zawartości technicznej i marketingowej różnią się w zależności od sklepu, typu oferty i typu listy. Zapoznaj się z poniższymi specyfikacjami, aby zweryfikować zgodność.  
1. Wymagania dotyczące witryny sklepu:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Typ listy i wymagania dotyczące typu oferty:  
    *   Aby uzyskać więcej informacji na temat typów list i typów ofert, odwiedź stronę Określanie typu listy dla Twojego rozwiązania znajdującego się pod adresem [docs.Microsoft.com/Azure/Marketplace/determine-Your-Listing-Type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Wymagania dotyczące witryny sklepu: AppSource  
W poniższej tabeli opisano wymagania wstępne dotyczące publikowania w programie AppSource.  

| Wymaganie | Szczegóły | Wymagane czy Zalecane |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Aplikacja musi zezwalać Azure Active Directory federacyjnym logowaniem jednokrotnym (Azure AD Federation SSO) z włączoną zgodą.<ul> <li>Więcej informacji na temat włączania federacyjnego logowania jednokrotnego usługi Azure AD można znaleźć w temacie Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się na stronie galerii aplikacji Azure Active Directory znajdującej się w [docs.Microsoft.com/Azure/Active-Directory/Active-Directory-SaaS-Custom-Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Wymagany |   
| ***Integracja z usługami Microsoft Cloud Services*** | Aplikacja powinna być zintegrowana z innymi usługami Microsoft Cloud, takimi jak Microsoft Power BI, Cortana Intelligence lub Microsoft Azure.<ul> <li>Przykładem usługi Microsoft Cloud jest Internet rzeczy.</li> </ul> | Zalecane |  
| ***Grupy odbiorców*** | Twoja aplikacja musi być dla użytkowników biznesowych i właścicieli firmy. | Wymagany | 
| ***Aplikacja SaaS (Software as a Service) dla firm*** | Aplikacja musi spełniać poniższe wymagania.<ul> <li>Aplikacja biznesowa SaaS</li> <li>Biznes — proces ukierunkowany</li> <li>Przeznaczony dla klientów biznesowych</li> <li>Zezwól użytkownikom na używanie poświadczeń służbowych do logowania, takich jak nazwa użytkownika i hasło</li> </ul> | Wymagany |  
| ***Bezpłatny okres próbny i środowisko próbne*** | Aplikacja musi zawierać jedną z następujących opcji, aby klient mógł bezpłatnie korzystać z aplikacji przez ograniczony czas.<ul> <li>Podaj `try` metodę, aby klienci mogli rozpocząć okres próbny aplikacji w AppSource</li> <li>Podaj `request trial` opcję w AppSource, aby klienci mogli zażądać wersji próbnej aplikacji</li> </ul>Bezpłatna wersja próbna musi oferować klientowi wstępnie ustawioną ilość czasu na wypróbowanie aplikacji bez dodatkowych kosztów. | Wymagany |  
| ***Łatwe do skonfigurowania rozwiązanie gotowe do użycia*** | Twoja aplikacja musi być prosta i szybka konfiguracja i konfiguracja bez konieczności dostosowywania. | Wymagany |  
| ***Zarządzanie potencjalnymi klientami*** | Zezwól programowi CRM na akceptowanie danych potencjalnych klientów przed uzyskaniem potencjalnych klientów z witryny sklepu.<ul> <li>Przykłady CRMs to Marketo, Microsoft Dynamics lub Salesforce</li> </ul> | Wymagany |  
| ***Zasady ochrony prywatności i warunki użytkowania*** | Aplikacja musi udostępnić link do strony zasad zachowania poufności informacji przy użyciu publicznego adresu URL. Warunki użytkowania muszą zostać podane podczas publikowania jako tekst. | Wymagany |  
| ***Pomoc techniczna*** | Twoja aplikacja musi udostępnić link do strony obsługi klienta przy użyciu publicznego adresu URL. Jeśli Twoja aplikacja jest wersją próbną, musisz obsługiwać bez dodatkowych kosztów w okresie próbnym. | Wymagany |  

## <a name="storefront-requirements-azure-marketplace"></a>Wymagania dotyczące witryny sklepu: Azure Marketplace  
Poniżej przedstawiono wymagania wstępne dotyczące typów list w portalu Azure Marketplace.  

| Wymaganie | Szczegóły | Typ listy |  
|:--- |:--- |:--- |  
| ***Zasady uczestnictwa*** | Aplikacja musi być zgodna z zasadami uczestnictwa w portalu Azure Marketplace.<ul> <li>Aby uzyskać więcej informacji na temat zasad uczestnictwa, odwiedź stronę zasad uczestnictwa w portalu Azure Marketplace znajdującą się w witrynie [Azure.Microsoft.com/support/legal/Marketplace/Participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />Transact<br />trial |  
| ***Integracja z firmą Microsoft*** | Oferta powinna używać lub zwiększać Microsoft Azure typów usług, takich jak obliczenia, sieci lub magazyn. Twoja oferta powinna być wyrównana do istniejącej kategorii portalu Azure Marketplace, na przykład baz danych, zabezpieczeń lub sieci.<ul> <li>Aby uzyskać więcej informacji na temat ofert z witryny Marketplace, odwiedź stronę Aplikacje Marketplace znajdującą się pod adresem [azuremarketplace.Microsoft.com/Marketplace/Apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />Transact<br />trial |  
| ***Grupy odbiorców*** | Oferta musi dotyczyć informatyków, deweloperów rozwiązań w chmurze lub innych technicznych ról klientów. | list<br />Transact<br />trial |  
| ***Zarządzanie potencjalnymi klientami*** | Włącz funkcję CRM (Marketo, Microsoft Dynamics lub Salesforce), aby akceptować dane potencjalnych klientów przed uzyskaniem potencjalnych klientów z witryny sklepu. | list<br />Transact<br />trial |  
| ***Zasady ochrony prywatności i warunki użytkowania*** | Aplikacja musi udostępnić link do strony zasad zachowania poufności informacji przy użyciu publicznego adresu URL. Warunki użytkowania muszą zostać podane podczas publikowania jako tekst. | list<br />Transact<br />trial |  
| ***Pomoc techniczna*** | Twoja oferta musi dostarczyć link do strony pomocy technicznej klienta przy użyciu publicznego adresu URL. Jeśli Twoja oferta jest wersją próbną, musisz obsługiwać bez dodatkowych kosztów w okresie próbnym. | Transact<br />trial |    

## <a name="non-transact-listings"></a>Aukcje inne niż Transact  
W tej sekcji opisano wszystkie typy ofert, które nie korzystają z typu listy Transact. 

### <a name="list"></a>List  
Typ listy zawiera następujące typy ofert w sklepie Marketplace.  

| Typ oferty | Musisz | Szczegóły |  
|:---        |:---        |:---     |  
| Usługi konsultingowe | AppSource | Wymagania: AppSource: list: usługi doradcze |  
| Usługi konsultingowe | Azure Marketplace | Wymagania: Azure Marketplace: lista: usługi doradcze |  
| Skontaktuj się z nami | AppSource | [](#) |  
| Skontaktuj się z nami | Azure Marketplace | Wymagania: AppSource: list: kontakt ze mną |  

#### <a name="requirements-appsource-list-consulting-service"></a>Wymagania: AppSource: list: usługa konsultingowa  

| Wymagania | Szczegóły |  
|:--- |:--- |  
| Charakterystyka oferty usługi | Twoja usługa doradcza musi spełniać następujące kryteria.<ul> <li>Dostarczaj ustalony zakres, ustalony czas trwania, stała cena (lub bezpłatna).</li> <li>Orientacja głównie dla przedsprzedaży.</li> <li>Ogranicz do pojedynczego klienta.</li> <li>Przeprowadzenie w witrynie.</li> </ul> |  
| Wymagania partnerskie dla usług doradczych | Spełniasz kryteria w odpowiednim obszarze dla usługi.<table><tr><th>Obszar rozwiązania</th><th>Kryteria</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Posiadanie kompetencji do zarządzania relacjami z klientami w chmurze Silver lub Gold.</td></tr><tr><td>Dynamics 365 dla finansów i operacji, wersja Enterprise</td><td>Zaplanuj i Zaplanuj zasoby firmy Silver lub Gold w ramach operacji związanych z chmurą w ciągu 12 miesięcy z $25 000 lub więcej.</td></tr><tr><td>Dynamics 365 dla finansów i operacji, wersja Business</td><td>ObCloud Services dostawcę (CSP) lub Digital Partnering rekordu (DPOR) dla jednego lub kilku klientów.</td></tr><tr><td>Power BI</td><td>Spełnij kryteria partnera rozwiązań.</td></tr><tr><td>PowerApps</td><td>Zaprezentuj Rozwiązanie partnera.</td></tr></table><ul> <li>Aby uzyskać więcej informacji na temat zarządzania relacjami z klientami, odwiedź stronę Zarządzanie relacjami z klientami w chmurze znajdującą się w witrynie [partner.Microsoft.com/membership/Cloud-Customer-Relationship-Management-Competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Aby uzyskać więcej informacji o planowaniu zasobów, odwiedź stronę planowanie zasobów przedsiębiorstwa znajdującą się w witrynie [partner.Microsoft.com/membership/Enterprise-Resource-Planning-Competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Aby uzyskać więcej informacji na temat dostawcy usług kryptograficznych, odwiedź stronę dostawcy Cloud Services znajdującą się pod adresem [partner.Microsoft.com/Cloud-Solution-Provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Aby uzyskać więcej informacji na temat DPOR, odwiedź stronę partnera cyfrowego rekordu i powiązania partnera znajdującego się w witrynie [partner.Microsoft.com/membership/Digital-Partner-of-Record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Aby uzyskać więcej informacji na temat kryteriów partnera rozwiązań, przejdź do omówienia partnera rozwiązań i dokumentu zachęty [https://partner.microsoft.com/en-us/membership/partner-incentives](https://partner.microsoft.com/en-us/membership/partner-incentives)znajdującego się w temacie.</li> <li>Aby uzyskać więcej informacji na temat pokazu partnerów, odwiedź stronę pokazu partnera znajdującą się w witrynie [powerapps.Microsoft.com/partner-Showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Wymagania: Azure Marketplace: lista: usługa konsultingowa  

| Wymagania | Szczegóły |  
|:--- |:--- |  
| Charakterystyka oferty usługi | Twoja usługa doradcza musi spełniać następujące kryteria.<ul> <li>Dostarczaj ustalony zakres, ustalony czas trwania, stała cena (lub bezpłatna).</li> <li>Orientacja głównie dla przedsprzedaży.</li> <li>Ogranicz do pojedynczego klienta.</li> <li>Przeprowadzenie w witrynie.</li> </ul> |  
| Wymagania partnerskie dla usług doradczych | Musisz mieć Silver lub Gold w jednej z następujących kompetencji w odpowiednim obszarze dla Twojej usługi. <table><tr><th>Obszar rozwiązania</th><th>Kompetencją</th></tr><td>Platforma i infrastruktura w chmurze</td><td>Platforma w chmurze<br />Centrum danych</td><tr><td>Tworzenie aplikacji i niezależne dostawcy oprogramowania</td><td>Projektowanie aplikacji<br />Integracja aplikacji<br />DevOps</td></tr><tr><td>Zarządzanie danymi i analiza</td><td>Analiza danych<br />Platforma danych</td></tr></table><ul> <li>Aby uzyskać więcej informacji na temat kompetencji, odwiedź stronę z kompetencjami Microsoft Partner Network w witrynie [partner.Microsoft.com/membership/Competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Aby uzyskać więcej informacji na temat oferty, odwiedź stronę usług konsultingowych portalu Azure Marketplace znajdującą się pod adresem [docs.Microsoft.com/Azure/Marketplace/Consulting-Services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Wersja próbna  

| Typ oferty | Musisz | Szczegóły |  
|:---        |:---        |:---     |  
| Bezpłatna/SaaS wersja próbna | AppSource | Wymagania dotyczące typów list: wersja próbna |  
| Bezpłatna/SaaS wersja próbna | Azure Marketplace | Wymagania: Azure Marketplace: wersja próbna: bezpłatna wersja próbna/wersja próbna SaaS |  
| Interaktywny pokaz | AppSource | Wymagania dotyczące typów list: wersja próbna |  
| Interaktywny pokaz | Azure Marketplace | [Wymagania: Azure Marketplace: wersja próbna: pokaz interaktywny](#requirements-azure-marketplace-trial-interactive-demo) |  
| Wersja testowa | AppSource | Wymagania dotyczące typów list: wersja próbna |  
| Wersja testowa | Azure Marketplace | [Wymagania: Azure Marketplace: wersja próbna: dysk testowy](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Wymagania: Azure Marketplace: wersja próbna  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Bezpłatny okres próbny i środowisko próbne | Klient może bezpłatnie korzystać z aplikacji przez ograniczony czas.<br /><br />Klient nie musi uiszczać żadnych opłat licencyjnych ani subskrypcji związanych z ofertą lub aplikacją. Klient nie jest zobowiązany do płacenia za dany produkt lub usługę pierwszej firmy Microsoft. Wszystkie opcje wersji próbnej są wdrażane w ramach subskrypcji platformy Azure. Użytkownik ma jedyną kontrolę nad optymalizacją kosztów i zarządzaniem.<br /><br />Możesz wybrać bezpłatną wersję próbną, interaktywną prezentację lub dysk testowy. Niezależnie od tego, co zostało wybrane, bezpłatna wersja próbna musi oferować klientowi wstępnie ustawiony czas na wypróbowanie aplikacji bez dodatkowych kosztów.<ul> <li>Aby rozpocząć proces tworzenia dysku testowego, Wyślij wiadomość e-mail na [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com)adres.</li> </ul>Uwaga: w przypadku korzystania z wersji próbnej usługi Azure Marketplace SaaS należy umożliwić klientom logowanie się przy użyciu poświadczeń służbowych.<ul> <li>Aby uzyskać więcej informacji, zapoznaj się z sekcją usługi AppSource Trial w witrynie [docs.Microsoft.com/Azure/Active-Directory/Develop/Active-Directory-devhowto-AppSource-Certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Łatwe do skonfigurowania rozwiązanie gotowe do użycia | Twoja aplikacja musi być prosta i szybka konfiguracja i konfiguracja. |  
| Dostępność/czas pracy | Twoja aplikacja SaaS lub platforma musi mieć czas przestoju wynoszącą co najmniej 99,9% czasu. |  
| Azure Active Directory | Twoja oferta musi zezwalać na Azure Active Directory (Azure AD) federacyjne Logowanie jednokrotne (SSO) (Logowanie jednokrotne usługi Azure AD) z włączoną opcją zgody. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Wymagania: Azure Marketplace: wersja próbna: bezpłatna wersja próbna/wersja próbna SaaS  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientowi wypróbowanie produktu przed zakupieniem metody zautomatyzowanej do konwersji na płatne użycie. Umożliwia również weryfikację koncepcji dla klienta i wspólne zaangażowanie z zespołami sprzedaży firmy Microsoft. | Twoje rozwiązanie jest maszyną wirtualną lub szablonem rozwiązania.<br /><br />Twoje rozwiązanie jest ofertą SaaS i oferuje wielodostępny produkt SaaS.<br /><br />Masz pierwsze środowisko uruchomieniowe, aby szybko rozpocząć pracę z klientem.<br /><br />Masz jedną dzierżawę, ale dodasz klientów jako użytkowników-Gości. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Wymagania: Azure Marketplace: wersja próbna: pokaz interaktywny  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Pozwala klientom zobaczyć Twoje rozwiązanie w działaniu bez złożoności konfigurowania. | Twoje rozwiązanie wymaga złożonych ustawień, które byłyby trudne do osiągnięcia w okresie próbnym. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Wymagania: Azure Marketplace: wersja próbna: dysk testowy  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientowi wypróbowanie produktu przed zakupieniem.<br /><br />Zapewnia środowisko z przewodnikiem dotyczące rozwiązania przy użyciu wstępnie skonfigurowanych ustawień.<br /><br />W przypadku korzystania z dysku testowego obowiązują następujące dodatkowe korzyści.<ul> <li>27% wyszukiwań użytkowników w portalu Marketplace są udoskonalane przez użytkowników, aby pokazywały tylko oferty z dyskami testowymi.</li> <li>Oferty z dyskami testowymi generują 38% więcej potencjalnych klientów niż oferty bez.</li> <li>36% nowych nabyć klientów w witrynie Marketplace pochodzi od klientów, którzy przebrali test na dysk.</li> <li>Dyski testowe umożliwiają sprzedawcaom pól firmy Microsoft lepsze zrozumienie Twojego produktu w celu zwiększenia nakładu pracy.</li> </ul> | Twoje rozwiązanie to maszyna wirtualna, szablon rozwiązania lub aplikacja SaaS z jedną dzierżawą lub skomplikowane do aprowizacji. <br /><br />Nie masz metody konwersji wersji próbnej na płatną ofertę. |  

---

## <a name="transact-specific-listings"></a>Aukcje specyficzne dla języka Transact

### <a name="transact"></a>Transact  

| Typ oferty | Musisz | Szczegóły |   
|:---        |:---        | :--- |  
| Aplikacje platformy Azure: zarządzana aplikacja | Azure Marketplace | Wymagania: Azure Marketplace: Transact: Azure Apps: zarządzana aplikacja |  
| Aplikacje platformy Azure: szablon rozwiązania | Azure Marketplace | Wymagania: Azure Marketplace: Transact: Azure Apps: szablon rozwiązania |  
| Containers | Azure Marketplace | [Wymagania: Azure Marketplace: Transact: kontener](#requirements-azure-marketplace-transact-container) |  
| Aplikacja SaaS  | Azure Marketplace | [Wymagania: Azure Marketplace: aplikacja Transact: SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Maszyna wirtualna | Azure Marketplace | [Wymagania: Azure Marketplace: Transact: maszyna wirtualna](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Wymagania: Azure Marketplace: Transact: kontener  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiary | Obsługuj model rozliczeń bezpłatny lub BYOL. |  
| Obraz oparty na platformie Docker | Obraz kontenera musi być oparty na formacie obrazu platformy Docker i musi zostać pobrany z rejestrów kontenerów Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Wymagania: Azure Marketplace: aplikacja Transact: SaaS  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiary | Twoja oferta jest naliczana według stawki miesięcznej. Cennik oparty na użyciu i opcje *prawdziwe* oparte na użyciu nie są teraz obsługiwane. |  
| Anulowanie | Twoja oferta jest anulowana przez klienta w dowolnym momencie. |  
| Strona docelowa transakcji | Hostowanie strony docelowej transakcji z platformą Azure. Strona docelowa pozwala klientom na tworzenie i zarządzanie kontem usługi SaaS. |  
| Interfejs API subskrypcji SaaS | Podaj usługę, która współdziała z subskrypcją SaaS, aby utworzyć, zaktualizować i usunąć konto użytkownika i plan usług. Wszystkie krytyczne zmiany interfejsu API muszą być obsługiwane w ciągu 24 godzin. Wszelkie niekrytyczne zmiany interfejsu API są okresowo aktualizowane. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Wymagania: Azure Marketplace: Transact: maszyna wirtualna  

| Wymaganie | Szczegóły |  
|:--- |:--- | 
| Rozliczenia i pomiary | Maszyna wirtualna musi obsługiwać miesięczne rozliczenie BYOL lub płatność zgodnie z rzeczywistym użyciem. |  
| Wirtualny dysk twardy (VHD) zgodny z platformą Azure | Maszyny wirtualne muszą być wbudowane w system Windows lub Linux.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego z systemem Linux, zobacz [dystrybucje systemu Linux zatwierdzone na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego systemu Windows, zobacz [Tworzenie dysku VHD zgodnego z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Następne kroki
*   Odwiedź stronę [przewodnika po wydawcy portalu Azure Marketplace i AppSource](./marketplace-publishers-guide.md) .  

