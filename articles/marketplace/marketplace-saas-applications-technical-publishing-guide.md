---
title: Przewodnik publikowania aplikacji saaS w portalu Azure Marketplace
description: Przewodnik krok po kroku i listy kontrolne publikowania aplikacji SaaS w portalu Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 1bc2feb4d8f3f103802bd823c66cd3f54ac5d74a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687559"
---
# <a name="saas-applications-offer-publishing-guide"></a>Aplikacje SaaS Przewodnik po publikacjach

Aplikacje SaaS mogą być publikowane na rynku z trzema różnymi wezwaniami do działania: "Skontaktuj się ze mną", "Wypróbuj teraz" i "Pobierz teraz". W tym przewodniku opisano te trzy opcje, w tym wymagania dla każdego z nich. 

## <a name="offer-overview"></a>Przegląd oferty  

Aplikacje SaaS są dostępne w obu witrynach sklepu Azure W poniższej tabeli opisano bieżące dostępne opcje:

| Opcja witryny sklepu | Aukcji | Próba/transak |  
| --- | --- | --- |  
| AppSource | Tak | Tak |
| Rynek platformy Azure | Nie | Tak |   

**Lista:**  Opcja publikowania aukcji składa się z typu oferty Kontakt me i jest używana, gdy udział na poziomie wersji próbnej lub transakcji nie jest możliwy. Zaletą tego podejścia jest to, że umożliwia wydawcom z rozwiązaniem na rynku, aby natychmiast rozpocząć odbieranie potencjalnych klientów, które mogą być przekształcone w oferty, aby zwiększyć swoją działalność.  
**Wersja próbna/transakcja:**  Klient ma możliwość bezpośredniego zakupu lub żądania wersji próbnej dla rozwiązania. Dostarczanie wersji próbnej zwiększa poziom zaangażowania oferowanych klientom i umożliwia klientom odkrywanie rozwiązania przed zakupem. Dzięki doświadczeniu próbnego będziesz miał większe szanse na promocję w witrynach sklepowych i powinieneś spodziewać się coraz bogatszych potencjalnych klientów z zaangażowania klientów. Wersje próbne muszą obejmować bezpłatne wsparcie przynajmniej na czas trwania okresu próbnego.  

| Oferta aplikacji SaaS | Wymagania biznesowe | Wymagania techniczne |  
| --- | --- | --- |  
| **Skontaktuj się z nami** | Tak | Nie |  
| **PowerBI / Dynamics** | Tak | Tak (integracja usługi Azure AD) |  
| **Aplikacje SaaS**| Tak | Tak (integracja usługi Azure AD) |     

## <a name="saas-list"></a>Lista SaaS

Wezwanie do działania dla aukcji SaaS bez wersji próbnej i bez funkcji rozliczeń to "Skontaktuj się ze mną". 

Nie trzeba konfigurować usługi Azure Active Directory do listy aplikacji SaaS. 

|Wymagania  |Szczegóły  |
|---------|---------|
|Twoja aplikacja to oferta SaaS  |   Twoje rozwiązanie to oferta SaaS i oferujesz produkt SaaS wielodostępny.      |


## <a name="saas-trial"></a>Próba SaaS

Udostępniasz rozwiązanie lub aplikację przy użyciu bezpłatnej wersji próbnej opartej na oprogramowaniu jako usłudze (SaaS). Bezpłatne oferty próbne mogą być prezentowane jako konto próbne o ograniczonym lub ograniczonym czasie trwania. 


|Wymagania  |Szczegóły  |
|---------|---------|
|Twoja aplikacja to oferta SaaS  |   Twoje rozwiązanie to oferta SaaS i oferujesz produkt SaaS wielodostępny.      |
|Twoja aplikacja jest włączona jako AAD     |   Klient zostanie przekierowany do Twojej domeny i będziesz dokonywać transakcji bezpośrednio z klientem       |


## <a name="saas-trial-technical-requirements"></a>SaaS Trial Wymagania techniczne

Wymagania techniczne dotyczące aplikacji SaaS są proste. Wydawcy muszą być zintegrowani tylko z usługą Azure Active Directory (Azure AD) do opublikowania. Integracja usługi Azure AD z aplikacjami jest dobrze udokumentowana, a firma Microsoft udostępnia wiele zestawów SDK i zasobów, aby to osiągnąć.  

Aby rozpocząć, zaleca się, że masz subskrypcję dedykowaną do publikowania w portalu Azure Marketplace, co pozwala na izolowanie pracy od innych inicjatyw. Po wykonaniu tej pracy można rozpocząć wdrażanie aplikacji SaaS w tej subskrypcji, aby rozpocząć pracę dewelopera.  

Najlepsza dokumentacja, przykłady i wskazówki usługi Azure Active Directory znajdują się w następujących witrynach: 

* [Przewodnik dla deweloperów usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integracja z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Mapa drogowa platformy Azure — zabezpieczenia i tożsamość](https://azure.microsoft.com/roadmap/?category=security-identity)

W przypadku samouczków wideo zapoznaj się z następującymi tematami:

* [Uwierzytelnianie usługi Azure Active Directory z usługą Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Briefing techniczny tożsamości usługi Azure Active Directory — część 1 z 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Briefing techniczny tożsamości usługi Azure Active Directory — część 2 z 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Tworzenie aplikacji za pomocą usługi Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Filmy wideo platformy Microsoft Azure poświęcone usłudze Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Bezpłatne szkolenie usługi Azure Active Directory jest dostępne pod adresem  
* [Seria zawartości Microsoft Azure for IT Pros: Usługa Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Ponadto usługa Azure Active Directory udostępnia witrynę do sprawdzania dostępności aktualizacji usługi   
* [Aktualizacje usługi Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Włączanie wersji próbnych za pomocą usługi Azure Active Directory  

Firma Microsoft uwierzytelnia wszystkich użytkowników portalu Marketplace za pomocą usługi Azure AD, więc gdy uwierzytelniony użytkownik kliknie za pośrednictwem listy próbnej w portalu Marketplace i zostanie przekierowany do środowiska próbnego, można aprowizować użytkownika bezpośrednio do wersji próbnej bez konieczności dodatkowego kroku logowania. Token, który aplikacja otrzymuje z usługi Azure AD podczas uwierzytelniania zawiera cenne informacje o użytkowniku, których można użyć do utworzenia konta użytkownika w aplikacji, co umożliwia automatyzację obsługi administracyjnej i zwiększyć prawdopodobieństwo konwersji. Aby uzyskać więcej informacji na temat tokenu, zobacz [Przykładowe tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Korzystanie z usługi Azure AD w celu włączenia uwierzytelniania jednym kliknięciem w aplikacji lub wersji próbnej wykonuje następujące czynności:  
* Usprawnia obsługę klienta z Marketplace do wersji próbnej.  
* Zachowuje poczucie "doświadczenia w produkcie", nawet gdy użytkownik jest przekierowywał z Marketplace do domeny lub środowiska próbnego.  
* Zmniejsza prawdopodobieństwo porzucenia na przekierowanie, ponieważ nie ma dodatkowego kroku logowania.  
* Zmniejsza bariery wdrażania dla dużej populacji użytkowników usługi Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certyfikacja integracji usługi Azure AD dla portalu Marketplace  

Certyfikuj integrację usługi Azure AD na kilka różnych sposobów, w zależności od tego, czy aplikacja jest pojedynczą dzierżawą, czy wieloma dzierżawcami i czy jesteś nowy w usłudze Azure AD federacyjnego logowania jednokrotnego (SSO), czy już ją obsługujesz.  

**W przypadku aplikacji wielodostępnych:**  

Jeśli już obsługujesz usługę Azure AD, wykonaj następujące czynności:
1.    Rejestrowanie aplikacji w witrynie Azure portal
2.    Włącz funkcję obsługi wielu dzierżaw w usłudze Azure AD, aby uzyskać wersję próbną "jednym kliknięciem". Bardziej szczegółowe informacje można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Jeśli jesteś nowym użytkownikiem usługi Azure AD Federated SSO, wykonaj następujące czynności: 
1.  Rejestrowanie aplikacji w witrynie Azure portal
2.  Tworzenie jedno i tak z usługą Azure AD przy użyciu [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) lub [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Włącz funkcję obsługi wielu dzierżaw w UAD, aby uzyskać wersję próbną "jednym kliknięciem", Więcej szczegółowych informacji można znaleźć [tutaj.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)  

**W przypadku aplikacji z jedną dzierżawą należy użyć dowolnej z następujących opcji:**  
* Dodawanie użytkowników do katalogu jako użytkowników-gości przy użyciu [usługi Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Ręczne udostępnianie wersji próbnych klientom przy użyciu funkcji "Skontaktuj się ze mną"
* Opracowanie "jazdy testowej" dla klienta
* Tworzenie przykładowej aplikacji demonstracyjnej z wieloma dzierżawcami za pomocą aplikacji SSO

## <a name="saas-subscriptions"></a>Subskrypcje SaaS

Użyj typu oferty aplikacji SaaS, aby umożliwić klientowi zakup rozwiązania technicznego opartego na SaaS jako subskrypcji. W aplikacji SaaS muszą być spełnione następujące wymagania:
- Cena i rozliczenie usługi w mieszkaniu (miesięcznym lub rocznym) lub według stawki za użytkownika.
- Podaj metodę uaktualnienia lub anulowania usługi w dowolnym momencie.
Firma Microsoft obsługuje transakcję handlową. Firma Microsoft rozlicza klienta w Twoim imieniu. Aby oferować aplikację SaaS jako subskrypcję, należy zintegrować się z interfejsami API realizacji usługi SaaS.  Usługa musi obsługiwać inicjowanie obsługi administracyjnej, uaktualnianie i anulowanie.

| Wymaganie | Szczegóły |  
|:--- |:--- |  
|Rozliczenia i pomiary | Twoja oferta jest wyceniana na podstawie wybranego modelu cenowego przed opublikowaniem (stawka ryczałtowa lub na użytkownika).  W przypadku korzystania z modelu ryczałtowego można opcjonalnie uwzględnić dodatkowe wymiary używane do pobierania opłat od odbiorców za użycie, które nie są uwzględnione w stawce ryczałtowej. |  
|Anulowanie | Twoja oferta jest anulowana przez klienta w dowolnym momencie. |  
|Strona docelowa transakcji | Hostujesz stronę docelową transakcji współmarkowej platformy Azure, na której użytkownicy mogą tworzyć konto usługi SaaS i zarządzać nim. |   
| Interfejs API subskrypcji | Udostępniasz usługę, która może wchodzić w interakcje z subskrypcją SaaS, aby utworzyć, zaktualizować i usunąć konto użytkownika i plan usługi. Krytyczne zmiany interfejsu API muszą być obsługiwane w ciągu 24 godzin. Niekrytyczne zmiany interfejsu API będą publikowane okresowo. |  

>[!Note]
>Usługa wyboru kanału partnerskiego dostawców rozwiązań w chmurze (CSP) jest już dostępna.  Zobacz [dostawców rozwiązań w chmurze, aby](./cloud-solution-providers.md) uzyskać więcej informacji na temat marketingu oferty za pośrednictwem kanałów partnerów CSP firmy Microsoft.

## <a name="next-steps"></a>Następne kroki
Jeśli jeszcze tego nie zrobiłeś,

* [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o rynku.

Aby zarejestrować się w Centrum partnerów, rozpocznij tworzenie nowej oferty lub pracę nad istniejącą:

* [Zaloguj się do Centrum partnerów,](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) aby utworzyć lub uzupełnić ofertę.
* Zobacz [tworzenie oferty aplikacji SaaS,](./partner-center-portal/create-new-saas-offer.md) aby uzyskać więcej informacji.
