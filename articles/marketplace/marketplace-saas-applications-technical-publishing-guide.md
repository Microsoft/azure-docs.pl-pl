---
title: Przewodnik publikowania aplikacji SaaS — Microsoft Commercial Marketplace
description: Wymagania i zasoby dotyczące publikowania publikacji SaaS oferty aplikacji Microsoft AppSource w witrynie Azure Marketplace.
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9f488675e4f2437982e2426da73b4db1a713905d
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607296"
---
# <a name="saas-applications-offer-publishing-guide"></a>Przewodnik publikowania ofert w aplikacjach SaaS

Możesz publikować aplikacje SaaS na komercyjnej witrynie Marketplace z trzema różnymi wywołaniami akcji: "Contact Me", "Wypróbuj teraz" i "Pobierz teraz". W tym artykule opisano te trzy opcje, w tym wymagania dotyczące każdego z nich. 

## <a name="offer-overview"></a>Przegląd oferty  

Aplikacje SaaS są dostępne w Microsoft AppSource i w witrynie Azure Marketplace.  Magazyny online obsługują listę, wersję próbną i oferty Transact.

**Lista:**  Opcja publikowania listy składa się z typu oferty kontakt ze mną i jest używana, gdy nie jest możliwe uczestnictwo w wersji próbnej lub na poziomie transakcji. Zaletą tego podejścia jest to, że umożliwia ona wydawcom korzystanie z rozwiązania na rynku do natychmiastowego rozpoczęcia otrzymywania potencjalnych klientów, które mogą zostać przekształcone w celu zwiększenia firmy.  
**Wersja próbna/transakcja:**  Klient ma możliwość bezpośredniego zakupu lub żądania wersji próbnej rozwiązania. Udostępnienie wersji próbnej spowoduje zwiększenie poziomu zaangażowania oferowanego klientom i umożliwienie klientom eksplorowania Twojego rozwiązania przed zakupem. Korzystając z wersji próbnej, będziesz lepiej korzystać z promocji w sklepach online i powinna oczekiwać więcej i bogatszych potencjalnych klientów od zaangażowania klientów. Wersje próbne muszą obejmować bezpłatną pomoc techniczną co najmniej przez okres próbny.  

| Oferta aplikacji SaaS | Wymagania biznesowe | Wymagania techniczne |  
| --- | --- | --- |  
| **Skontaktuj się z nami** | Tak | Nie |  
| **Power BI/Dynamics** | Tak | Tak (Integracja z usługą Azure AD) |  
| **Aplikacje SaaS**| Tak | Tak (Integracja z usługą Azure AD) |     

## <a name="saas-list"></a>Lista SaaS

Wywołanie akcji dla SaaS aukcji bez wersji próbnej i brak funkcji rozliczeń to "kontakt ze mną". 

Nie musisz konfigurować Azure Active Directory, aby wyświetlić listę aplikacji SaaS. 

|Wymagania  |Szczegóły  |
|---------|---------|
|Twoja aplikacja jest ofertą SaaS  |   Twoje rozwiązanie to SaaS oferta i oferuje wielodostępny produkt SaaS.      |


## <a name="saas-trial"></a>Wersja próbna SaaS

Możesz podać rozwiązanie lub aplikację, korzystając z bezpłatnej wersji próbnej opartej na oprogramowaniu jako usługa (SaaS). Oferty bezpłatnej wersji próbnej mogą być prezentowane jako konto w ramach ograniczonego użycia lub ograniczonego okresu próbnego. 


|Wymagania  |Szczegóły  |
|---------|---------|
|Twoja aplikacja jest ofertą SaaS  |   Twoje rozwiązanie to SaaS oferta i oferuje wielodostępny produkt SaaS.      |
|Twoja aplikacja jest włączona w usłudze AAD     |   Klient zostanie przekierowany do domeny, a użytkownik będzie bezpośrednio transakcyjny       |


## <a name="saas-trial-technical-requirements"></a>Wymagania techniczne dotyczące wersji próbnej SaaS

Wymagania techniczne dotyczące aplikacji SaaS są proste. Aby można było opublikować wydawców, należy jedynie przeprowadzić integrację z usługą Azure Active Directory (Azure AD). Integracja usługi Azure AD z aplikacjami jest dobrze udokumentowana, a firma Microsoft udostępnia wiele zestawów SDK i zasobów w celu osiągnięcia tego celu.  

Aby rozpocząć, zalecamy korzystanie z subskrypcji przeznaczonej do publikacji w portalu Azure Marketplace, co pozwala odizolować pracę od innych inicjatyw. Gdy to zrobisz, możesz rozpocząć wdrażanie aplikacji SaaS w ramach tej subskrypcji, aby rozpocząć pracę programistyczną.  

Najlepsza Azure Active Directory dokumentacja, przykłady i wskazówki znajdują się w następujących lokacjach: 

* [Przewodnik dewelopera Azure Active Directory](../active-directory/develop/index.yml)

* [Integracja z usługą Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md)

* [Plan Azure — zabezpieczenia i tożsamość](https://azure.microsoft.com/roadmap/?category=security-identity)

W przypadku samouczków wideo zapoznaj się z następującymi tematami:

* [Uwierzytelnianie Azure Active Directory za pomocą Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Opis techniczny Azure Active Directory tożsamości — część 1 z 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Opis techniczny Azure Active Directory tożsamości — część 2 z 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Tworzenie aplikacji za pomocą Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure wideo skoncentrowane na Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Bezpłatne szkolenia Azure Active Directory są dostępne pod adresem  
* [Microsoft Azure dla specjalistów IT — seria zawartości: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Ponadto Azure Active Directory udostępnia lokację do sprawdzenia dostępności aktualizacji usługi   
* [Aktualizacje usługi Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Używanie Azure Active Directory do włączania prób  

Firma Microsoft uwierzytelnia wszystkich użytkowników portalu Marketplace przy użyciu usługi Azure AD, więc gdy uwierzytelniony użytkownik kliknie listę próbną w witrynie Marketplace i zostanie przekierowany do środowiska próbnego, można udostępnić użytkownikowi bezpośrednio w wersji próbnej bez konieczności dodatkowego kroku logowania. Token, który aplikacja otrzymuje z usługi Azure AD podczas uwierzytelniania, zawiera cenne informacje o użytkowniku, których można użyć do utworzenia konta użytkownika w aplikacji, co pozwala zautomatyzować środowisko aprowizacji i zwiększyć prawdopodobieństwo konwersji. Aby uzyskać więcej informacji o tokenie, zobacz [przykładowe tokeny](../active-directory/develop/active-directory-token-and-claims.md) .

Za pomocą usługi Azure AD Włącz uwierzytelnianie jednokrotne w aplikacji lub wersji próbnej, wykonując następujące czynności:  
* Usprawnia obsługę klienta z witryny Marketplace w wersji próbnej.  
* Utrzymuje działanie środowiska w środowisku produkcyjnym, nawet jeśli użytkownik jest przekierowany z witryny Marketplace do Twojej domeny lub środowiska w wersji próbnej.  
* Zmniejsza prawdopodobieństwo odrzucenia przy przekierowaniu, ponieważ nie istnieje dodatkowy krok logowania.  
* Zmniejsza bariery wdrażania dla dużej populacji użytkowników usługi Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certyfikowanie integracji usługi Azure AD w witrynie Marketplace  

Zatwierdź integrację z usługą Azure AD na kilka różnych sposobów, w zależności od tego, czy aplikacja jest pojedynczą dzierżawą, czy z wieloma dzierżawcami, oraz czy jesteś nowym elementem federacyjnym logowania jednokrotnego (SSO) usługi Azure AD lub już ją obsługuje.  

**W przypadku aplikacji wielodostępnych:**  

Jeśli już obsługujesz usługę Azure AD, wykonaj następujące czynności:
1.    Zarejestruj swoją aplikację w Azure Portal
2.    Włącz funkcję obsługi wielu dzierżawców w usłudze Azure AD, aby pobrać wersję próbną jednego kliknięcia. Bardziej szczegółowe informacje można znaleźć [tutaj](../active-directory/develop/active-directory-integrating-applications.md).  

Jeśli jesteś nowym w przypadku federacyjnego logowania jednokrotnego usługi Azure AD, wykonaj następujące czynności: 
1.  Zarejestruj swoją aplikację w Azure Portal
2.  Utwórz Logowanie jednokrotne za pomocą usługi Azure AD przy użyciu [OpenID Connect Connect](../active-directory/develop/active-directory-protocols-openid-connect-code.md) lub [OAuth 2,0](../active-directory/develop/active-directory-protocols-oauth-code.md).
3.  Włącz funkcję obsługi wielu dzierżawców w usłudze AAD, aby uzyskać więcej informacji na temat środowiska próbnego "jednym kliknięciem". bardziej szczegółowe informacje można znaleźć [tutaj](../active-directory/develop/active-directory-devhowto-appsource-certified.md).  

**W przypadku aplikacji z jedną dzierżawą Użyj jednej z następujących opcji:**  
* Dodawanie użytkowników do katalogu jako użytkowników-Gości korzystających z [platformy Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)
* Ręczne inicjowanie obsługi wersji próbnych dla klientów za pomocą polecenia "kontakt ze mną"
* Opracowywanie dysku testowego dla poszczególnych klientów
* Tworzenie przykładowej aplikacji demonstracyjnej z wieloma dzierżawcami przy użyciu logowania jednokrotnego

## <a name="saas-subscriptions"></a>Subskrypcje SaaS

Użyj typu oferty aplikacji SaaS, aby umożliwić klientowi kupowanie rozwiązań technicznych opartych na SaaS, w ramach subskrypcji. Dla aplikacji SaaS muszą zostać spełnione następujące wymagania:
- Cena i rozliczanie usługi za pomocą stawki ryczałtowej (co miesiąc lub co rok) lub według stawki za użytkownika.
- Podaj metodę uaktualnienia lub anulowania usługi w dowolnym momencie.
Firma Microsoft hostuje transakcję handlową. Firma Microsoft rozlicza klienta w Twoim imieniu. Aby zaoferować aplikację SaaS jako subskrypcję, musisz przeprowadzić integrację z interfejsami API realizacji SaaS.  Usługa musi obsługiwać Inicjowanie obsługi administracyjnej, uaktualnianie i anulowanie.

| Wymaganie | Szczegóły |  
|:--- |:--- |  
|Rozliczenia i pomiary | Cena oferty jest naliczana na podstawie modelu cen wybranych przed opublikowaniem (stawka ryczałtowa lub dla użytkownika).  W przypadku korzystania z modelu stawki Flat można opcjonalnie uwzględnić dodatkowe wymiary używane do naliczania opłat klientom za użycie nieuwzględnione w stałej stawce. |  
|Anulowanie | Twoja oferta jest anulowana przez klienta w dowolnym momencie. |  
|Strona docelowa transakcji | Możesz obsługiwać stronę docelową transakcji z platformą Azure, w której użytkownicy mogą tworzyć konta usługi SaaS i zarządzać nimi. |   
| Interfejs API subskrypcji | Uwidaczniasz usługę, która może współistnieć z subskrypcją SaaS, aby utworzyć, zaktualizować i usunąć konto użytkownika i plan usług. Krytyczne zmiany interfejsu API muszą być obsługiwane w ciągu 24 godzin. Niekrytyczne zmiany interfejsu API będą okresowo publikowane. |  

>[!Note]
>Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna.  Zobacz [dostawcy rozwiązań w chmurze](./cloud-solution-providers.md) , aby uzyskać więcej informacji na temat marketingu oferty przez kanały partnerskie programu Microsoft CSP.

## <a name="next-steps"></a>Następne kroki
Jeśli jeszcze tego nie zrobiono,

* [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o portalu Marketplace.

Aby zarejestrować się w centrum partnerskim, zacznij tworzyć nową ofertę lub pracować na istniejącym:

* [Zaloguj się do Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.
* Aby uzyskać więcej informacji, zobacz temat [Tworzenie oferty aplikacji SaaS](./partner-center-portal/create-new-saas-offer.md) .
