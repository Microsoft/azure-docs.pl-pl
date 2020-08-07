---
title: Lista kontrolna tworzenia oferty SaaS w portalu komercyjnym firmy Microsoft
description: Szczegóły, jakie można podać w procesie tworzenia oferty SaaS w centrum partnerskim.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: e17edacf45583283d53c5484417f6a2dd7c6e012
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799926"
---
# <a name="saas-offer-creation-checklist-in-partner-center"></a>Lista kontrolna tworzenia oferty SaaS w centrum partnerskim

Proces tworzenia oferty SaaS przeprowadzi Cię przez wiele stron.  W tym artykule opisano szczegóły, które można podać na każdej stronie, z linkami do dodatkowych informacji na temat poszczególnych elementów.

> [!NOTE]
> W przypadku tworzenia oferty SaaS z funkcją transacting upewnij się, że wdrożono integrację z [interfejsami API realizacji SaaS](./pc-saas-fulfillment-apis.md).  Integracja z interfejsami API jest jedynym sposobem zapewnienia poprawnego działania transactu w portalu Marketplace. Należy również upewnić się, że aplikacja używa uwierzytelniania usługi Azure AD z logowaniem jednokrotnym (SSO). Zobacz [oferty usługi Azure AD i transacting SaaS w komercyjnej witrynie Marketplace](../azure-ad-saas.md).

Elementy wymagane do podania lub określenia są wymienione poniżej.  Niektóre obszary są opcjonalne lub mają podane wartości domyślne, które można zmienić zgodnie z potrzebami.  Nie musisz już korzystać z tych sekcji w podanej kolejności.

| **Element**    | **Przeznaczenie**  |
| :---------- | :-------------------|
| [**Nowa oferta — modalne**](#new-offer-modal) | Zbiera informacje o tożsamości oferty.  |
| [Strona konfiguracji oferty](#offer-setup-page) | Umożliwia korzystanie z kluczowych funkcji i wybór sposobu sprzedaży oferty przez firmę Microsoft.  |
| [Strona właściwości](#properties-page) | Zdefiniuj kategorie i branże używane do grupowania oferty na rynkach Marketplace, umowy prawne wspierające Twoją ofertę i wersję aplikacji. |
| [Strona z listą ofert](#offer-listing-page) | Zdefiniuj szczegóły oferty do wyświetlania w portalu Marketplace, w tym opisy oferty i zasobów marketingowych.|
| [Strona podglądu](#preview-page) | Zdefiniuj ograniczonego odbiorcę w wersji zapoznawczej, aby wypróbować ofertę przed opublikowaniem oferty w szerszym gronie odbiorców w portalu Marketplace.|
| [Strona konfiguracji technicznej](#technical-configuration-page)  |  Dostępne tylko w przypadku wybrania sprzedaży oferty przez firmę Microsoft.  Zdefiniuj szczegóły techniczne (adres URL strony docelowej, adres URL połączenia elementu webhook, identyfikator dzierżawy usługi Azure AD i identyfikator aplikacji usługi Azure AD), które są używane przez witrynę Marketplace do łączenia się z ofertą.  Te parametry są wymagane do poprawnego zintegrowania z realizacją SaaS oraz z użyciem mierzonych interfejsów API rozliczeń w portalu Marketplace.|
| [**Nowy modalny plan**](#plan-identity-modal) | Zbiera informacje o tożsamości planu.  |
| [Strona aukcji planu](#plan-listing-page)  | Dostępne tylko w przypadku wybrania sprzedaży oferty przez firmę Microsoft. Zdefiniuj szczegóły używane do wyświetlania planu w portalu Marketplace.  |
| [Zaplanuj stronę dostępności & cennika](#plan-pricing--availability-page)  | Dostępne tylko w przypadku wybrania sprzedaży oferty przez firmę Microsoft.  Gromadzi informacje o cechach biznesowej (modelu cen), odbiorcach i dostępności dla każdego planu (wersji) oferty.  |
| [Strona listy dysków testowych](#test-drive-listing-page)  | Dostępne tylko wtedy, gdy wybrano opcję zaoferowania dysku testowego dla oferty. Zdefiniuj szczegóły używane do wyświetlania na liście dysku testowego w portalu Marketplace.  |
| Strona konfiguracji technicznej na dysku testowym  | Dostępne tylko wtedy, gdy wybrano opcję zaoferowania dysku testowego dla oferty. Zdefiniuj szczegóły techniczne dla demonstracji (lub "Test Drive"), która umożliwi klientom wypróbowanie oferty przed zatwierdzeniem jej zakupu.  |
| [Przeglądanie i publikowanie strony](#review-and-publish-page)  | Wybierz zmiany, które chcesz opublikować, zobacz stan poszczególnych stron i podaj uwagi dotyczące zespołu certyfikacji.  |

## <a name="new-offer-modal"></a>Nowa oferta — modalne

Pierwsze fragmenty informacji, które należy podać, są IDENTYFIKATORem i aliasem oferty.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Identyfikator oferty  | Wymagane, nie można zmienić po utworzeniu. Maks. 50 znaków i musi zawierać tylko małe litery, znaki alfanumeryczne, łączniki i podkreślenia. |
| Alias oferty  | Wymagany. |

## <a name="offer-setup-page"></a>Strona konfiguracji oferty

Na stronie Konfiguracja oferty możesz wybrać różne kanały i sprzedawać ruchy, a także zadeklarować korzystanie z najważniejszych funkcji, takich jak Stacja testowa i klient. 

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------|  
| Czy chcesz sprzedawać w firmie Microsoft?  | Wymagany. Ustawienie domyślne: Tak |
| Jak chcesz, aby potencjalni klienci mogli współdziałać z listą ofert? (Wywołanie do akcji)  | Wymagane, jeśli nie sprzedajesz przez firmę Microsoft. Domyślnie: bezpłatna wersja próbna, opcje: "Pobierz teraz", "bezpłatna wersja próbna", "kontakt ze mną". |
| Adres URL wersji próbnej  | Wymagany, jeśli wybrano opcję "bezpłatna wersja próbna", ponieważ sposób, w jaki klienci powinni korzystać z listy ofert. |
| Adres URL oferty  | Wymagane, jeśli wybrano opcję "Pobierz teraz", ponieważ sposób, w jaki klienci powinni korzystać z listy ofert |
| Kanały  | Opcjonalny. Wartość domyślna: nie została wybrana w kanale CSP (odsprzedawcy).  |
| Wersja testowa | Opcjonalny. Wartość domyślna: brak włączonego dysku testowego.  |
| Typ dysku testowego | Wymagane, jeśli włączono dysk testowy. Wartość domyślna: Brak zaznaczenia. Opcje: Azure Resource Manager, Dynamics 365 dla firm Central, Dynamics 365 do zaangażowania klienta, Dynamics 365 for Operations, Logic App Power BI.  |
| Potencjalni klienci — łączenie z systemem CRM | Wymagane, jeśli sprzedajesz przez firmę Microsoft lub jeśli lista zawiera oferty "kontakt ze mną". Wartość domyślna: brak połączonego systemu CRM. Opcje programu CRM: tabela Azure, obiekt blob platformy Azure, Dynamics CRM Online, HTTPs, punkt końcowy, Marketo, Salesforce  |

## <a name="properties-page"></a>Strona właściwości

Na stronie właściwości można zdefiniować kategorie i branże używane do grupowania oferty na rynkach Marketplace, umowy prawne wspierające Twoją ofertę i wersję aplikacji. Upewnij się, że podajesz pełne i dokładne szczegółowe informacje o ofercie na tej stronie, aby była ona wyświetlana odpowiednio i oferowana przez właściwy zestaw klientów. 

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------|  
| Kategoria i Podkategoria | Wymagane wartości 1 i maks. 3. Wartość domyślna: Brak zaznaczenia. |
| Branże i podbranże | Opcjonalny. maksymalnie 2 branże L1 i 2 podbranże w ramach każdej branży L1, domyślnie: brak wybranych |
| Wersja aplikacji  | Opcjonalny. Wartość domyślna: Brak. |
| Użyj kontraktu standardowego  | Opcjonalny. Domyślnie: nie wybrano.  | |
| Warunki użytkowania  | Wymagane, jeśli nie wybrano kontraktu standardowego.  |

## <a name="offer-listing-page"></a>Strona z listą ofert

Na stronie lista znajduje się tekst i obrazy widoczne dla klientów podczas wyświetlania listy ofert w portalu Marketplace. 

| **Nazwa pola**    | **Uwagi**   |
| :---------------- | :-----------|
| Nazwa  | Wymagane, maks. 50 znaków. |
| Podsumowanie  | Wymagane, maks. 100 znaków. |
| Opis  | Wymagane, maks. 3000 znaków. |
| Instrukcje Wprowadzenie  | Wymagane, maks. 3000 znaków. |
| Instrukcje Wprowadzenie  | Wymagane, maks. 3000 znaków. |
| Słowa kluczowe wyszukiwania  | Opcjonalne, zalecane, maksymalnie 3 słowa kluczowe. |
| Adres URL zasad ochrony prywatności  | Wymagany. |
| Adres URL materiałów marketingowych programu CSP  | Opcjonalny. |
| Przydatne linki tytuł + adres URL  | Opcjonalny. |
| Dokument pomocniczy tytuł + plik  | Wymagane, minimum 1 i 3. Musi być formatem pliku PDF. |
| Zrzuty ekranu  | Wymagany, minimalny 1 zrzut ekranu i maksymalnie 5; co najmniej cztery zalecane. Musi mieć 1280 X 720 w formacie PNG. |
| Logo Sklepu (mały, średni, duży)  | Jest wymagane duże logo (216 x 216). Centrum partnerskie będzie używać tego do tworzenia małych (48 x 48 pikseli) i średniego rozmiaru logo (90 x 90 pikseli). Opcjonalnie można zastąpić je różnymi obrazami. Logo musi być w formacie PNG. |
| Nazwa wideo + URL + miniatura  | Opcjonalne, zalecane, maksymalnie 4 wideo. Miniatura musi mieć 1280 x 720 w formacie PNG. Wideo musi być hostowane w serwisie YouTube lub Vimeo. |
| Kontakty (program CSP, inżynieria, pomoc techniczna)  | Wymagana osoba kontaktu inżynieryjnego i pomocy technicznej (nazwisko, adres e-mail i numer telefonu); Program CSP kontaktuje się z opcjonalnym, ale zalecanym. |
| Adres URL pomocy technicznej  | Wymagany. |

## <a name="preview-page"></a>Strona podglądu

Na stronie wersji zapoznawczej można określić odbiorców w celu uzyskania dostępu do wersji zapoznawczej oferty, aby upewnić się, że oferta spełnia wszystkie wymagania, zanim będzie ona aktywna. 

| **Nazwa pola**    | **Uwagi**   |
| :---------------- | :-----------|
| Wiadomości e-mail w usłudze AAD/MSA i opis | Wymagane, minimum 1 i maks. 10, jeśli zostało wprowadzone ręcznie, lub do 20 w przypadku przekazywania pliku CSV. |

## <a name="technical-configuration-page"></a>Strona konfiguracji technicznej

Na stronie Konfiguracja techniczna można określić szczegóły techniczne używane przez firmę Microsoft do nawiązania połączenia z ofertą. Ta strona nie jest widoczna dla Ciebie, jeśli zdecydujesz się nie sprzedawać w firmie Microsoft.

> [!NOTE]
> W przypadku ofert transakcyjnych należy utworzyć stronę docelową, a aplikacja musi używać uwierzytelniania usługi Azure AD z logowaniem jednokrotnym (SSO). Aby uzyskać więcej informacji, zobacz [oferty usługi Azure AD i transacting SaaS w portalu komercyjnym](../azure-ad-saas.md).

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Adres URL strony docelowej | Wymagane, jeśli sprzedajesz przez firmę Microsoft. |
| Element webhook połączenia | Wymagane, jeśli sprzedajesz przez firmę Microsoft. |
| Identyfikator dzierżawy usługi Azure AD | Wymagane, jeśli sprzedajesz przez firmę Microsoft. |
| Identyfikator aplikacji usługi Azure AD | Wymagane, jeśli sprzedajesz przez firmę Microsoft. |

## <a name="plan-identity-modal"></a>Zaplanuj modalną tożsamość

Pierwsze informacje, które należy podać, to nazwa i identyfikator planu. Ta strona nie jest widoczna dla Ciebie, jeśli użytkownik zdecydował się nie sprzedawać w firmie Microsoft.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Identyfikator planu  | Wymagane, jeśli sprzedajesz przez firmę Microsoft. Nie można go zmienić po utworzeniu. Maks. 50 znaków i musi zawierać tylko małe litery, znaki alfanumeryczne, łączniki i podkreślenia. |
| Plan Name  | Wymagane, jeśli sprzedajesz przez firmę Microsoft. Musi być unikatowa we wszystkich planach oferty. Maks. 50 znaków. |

## <a name="plan-listing-page"></a>Strona aukcji planu

Na stronie z listą planu można podać tekst, który mają być widoczne dla klientów podczas wyświetlania planu w portalu Marketplace. Ta strona nie jest widoczna dla Ciebie, jeśli zdecydujesz się nie sprzedawać w firmie Microsoft.

| **Nazwa pola**    | **Uwagi**   |  
| :---------------- | :-----------| 
| Opis planu   | Wymagane, jeśli sprzedajesz przez firmę Microsoft. Maks. 500 znaków. | |

## <a name="plan-pricing--availability-page"></a>Zaplanuj stronę dostępności & cennika

Na stronie planowanie cen i dostępności można zdefiniować charakterystykę biznesową, odbiorców i dostępność rynkową dla każdego planu (wersji) oferty. Ta strona nie jest widoczna dla Ciebie, jeśli zdecydujesz się nie sprzedawać w firmie Microsoft.

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Dostępność na rynku  | Wymagane, minimum 1 i maks. 141. |
| Model cen  | Wymagany. Wartość domyślna: stawka płaska. Opcje: stawka ryczałtowa na użytkownika. |
| Minimalna i Maksymalna liczba stanowisk  | Opcjonalne, dostępne tylko wtedy, gdy wybrano model cenowy oparty na miejscu. |
| Okres rozliczeniowy  | Wymagany. Wartość domyślna: co miesiąc. Opcje: co miesiąc, roczna. |
| Cena  | Wymagany USD miesięcznie, w przypadku wybrania miesięcznego okresu rozliczeniowego; lub USD na rok, jeśli wybrano roczny okres rozliczeniowy. |
| Planowanie odbiorców  | Opcjonalny. Domyślne: plan publiczny. Opcje: publiczne, prywatne według identyfikatora dzierżawy |
| Odbiorcy planu z ograniczeniami (identyfikator dzierżawy + opis)  | Wymagane, jeśli wybrano plan prywatny. Minimalna 1 i maksymalna 10 identyfikatorów dzierżawy, jeśli wprowadzono ją ręcznie. Max 20000, jeśli Importuj plik CSV. |

## <a name="test-drive-listing-page"></a>Strona listy dysków testowych

Dostępne tylko wtedy, gdy wybrano opcję zaoferowania dysku testowego dla oferty. Zdefiniuj szczegóły używane do wyświetlania na liście dysku testowego w portalu Marketplace.

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Opis  | Wymagany. |
| Ręczna nazwa użytkownika + plik  | Wymagany, maksymalny 1 dokument doc. musi być w formacie PDF. |
| Nazwa wideo, adres URL i miniatura  | Opcjonalne, zalecane. Miniatura musi być 533 x 324 w formacie JPGP lub PNG. Wideo musi być hostowane w serwisie YouTube lub Vimeo. |

## <a name="review-and-publish-page"></a>Przeglądanie i publikowanie strony

| **Nazwa pola**    | **Uwagi**   | 
| :---------------- | :-----------| 
| Uwagi dotyczące certyfikacji  | Opcjonalny. |

## <a name="next-steps"></a>Następne kroki

- [Tworzenie nowej oferty SaaS](./create-new-saas-offer.md)
