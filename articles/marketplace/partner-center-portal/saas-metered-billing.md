---
title: Naliczanie opłat za oferty SaaS z wykorzystaniem komercyjnej usługi pomiarowej Marketplace firmy Microsoft
description: Dowiedz się więcej na temat elastycznych modeli rozliczeń dla ofert SaaS przy użyciu komercyjnej usługi pomiarowej Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: d232f7ba1304d76a0ba2db62b9f0f9b4229513f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378608"
---
# <a name="metered-billing-for-saas-using-the-commercial-marketplace-metering-service"></a>Naliczanie opłat za SaaS przy użyciu komercyjnej usługi pomiarowej Marketplace

Za pomocą komercyjnej usługi pomiarowej Marketplace można utworzyć oferty oprogramowania jako usługi (SaaS), które są naliczone zgodnie z jednostkami niestandardowymi. Przed opublikowaniem oferty SaaS w komercyjnej witrynie Marketplace należy zdefiniować wymiary rozliczeń, takie jak przepustowość, bilety lub przetworzone wiadomości e-mail.  Następnie klienci uiszczają opłaty zgodnie z ich użyciem, a system informuje firmę Microsoft za pośrednictwem komercyjnego interfejsu API usługi pomiarowej Marketplace dla zdarzeń rozliczanych w miarę ich występowania.  

## <a name="prerequisites-for-metered-billing"></a>Wymagania wstępne dotyczące rozliczeń naliczanych

Aby oferta SaaS mogła korzystać z naliczanych opłat, należy najpierw:

- Zapoznaj się ze wszystkimi wymaganiami oferty dotyczącymi [sprzedaży za pomocą oferty firmy Microsoft](../plan-saas-offer.md#listing-options) , jak opisano w temacie [Tworzenie oferty SaaS w komercyjnej witrynie Marketplace](../create-new-saas-offer.md).
- Integruj się z [interfejsami API realizacji SaaS](./pc-saas-fulfillment-api-v2.md) , aby klienci mogli inicjować udostępnianie oferty i łączyć się z nią.  
- Należy skonfigurować model cen dla **stawki ryczałtowej** w przypadku ładowania klientów do usługi.  Wymiary są opcjonalnym rozszerzeniem modelu cen ryczałtowych. 

Następnie oferta SaaS może być zintegrowana z [komercyjnymi interfejsami API usługi pomiaru portalu Marketplace](./marketplace-metering-service-apis.md) , aby poinformować firmę Microsoft o zdarzeniach rozliczanych.

>[!Note]
>Usługa pomiaru Marketplace jest dostępna tylko w modelu rozliczania stawki ryczałtowej i nie dotyczy modelu rozliczania na użytkownika.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Jak naliczane są rozliczenia taryfowe przy użyciu cen

Zrozumienie hierarchii oferty jest ważne, gdy chodzi o Definiowanie oferty wraz z jej modelami cenowymi.

- Każda oferta SaaS jest konfigurowana do sprzedaży przez firmę Microsoft.  Po opublikowaniu oferty nie można zmienić tej opcji.
- Każda oferta SaaS, skonfigurowana do sprzedaży przez firmę Microsoft, może mieć jeden lub więcej planów.  Użytkownik subskrybuje ofertę SaaS, ale jest zakupiony przez firmę Microsoft w ramach planu.
- Z każdym planem jest skojarzony model cen: **stawka ryczałtowa** lub **za użytkownika**. Wszystkie plany w ofercie muszą być skojarzone z tym samym modelem cen. Na przykład nie może istnieć oferta mająca plany dotyczące modelu cen ryczałtowych i innego modelu cen poszczególnych użytkowników.
- W ramach każdego planu skonfigurowanego dla modelu rozliczania stawki ryczałtowej jest uwzględniana co najmniej jedna opłata cykliczna (która może być $0):
    - Cykliczna opłata **miesięczna** : zryczałtowany miesięczna opłata, która jest przedpłata za Miesięczny cykl w przypadku zakupu planu.
    - Cykliczna opłata **roczna** : ryczałtowa opłata roczna, która jest przedpłata za cykl roczny, gdy użytkownik zakupił plan.
- Oprócz opłat cyklicznych plan zryczałtowanej stawki może również zawierać opcjonalne wymiary niestandardowe używane do naliczania opłat za użycie nadwyżkowe, które nie są uwzględnione w stałej stawce.  Każdy wymiar reprezentuje jednostkę rozliczaną, którą usługa będzie komunikować się z firmą Microsoft przy użyciu [interfejsu API komercyjnej usługi pomiaru w portalu Marketplace](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Przykładowa oferta

Przykładowo firma Contoso jest wydawcą z usługą SaaS o nazwie contoso Notification Services (CNS). CNS umożliwia klientom wysyłanie powiadomień za pośrednictwem poczty e-mail lub tekstu. Firma Contoso jest zarejestrowana jako Wydawca w centrum partnerskim do komercyjnego programu Marketplace do publikowania ofert SaaS oferowanych klientom platformy Azure.  Istnieją dwa plany skojarzone z CNS, podane poniżej:

- Plan Basic
    - Wysyłaj 10000 wiadomości e-mail i 1000 teksty dla $0 miesięcznie (stawka miesięczna)
    - W przypadku wiadomości e-mail z 10000, płatność $1 dla każdej 100 wiadomości e-mail
    - Oprócz 1000 tekstów, płatność $0,02 dla każdego tekstu

    [![Cennik planu Basic](./media/saas-basic-pricing.png "Kliknij, aby wyświetlić widok powiększony")](./media/saas-basic-pricing.png)

- Plan Premium
    - Wysyłaj 50000 wiadomości e-mail i 10000 teksty dla $350/miesiąc lub 5 M wiadomości e-mail oraz 1M tekstów dla $3500 rocznie
    - W przypadku wiadomości e-mail z 50000, płatność $0,5 dla każdej 100 wiadomości e-mail
    - Oprócz 10000 tekstów, płatność $0,01 dla każdego tekstu

    [![Cennik planu Premium](./media/saas-premium-pricing.png "Kliknij, aby wyświetlić widok powiększony")](./media/saas-premium-pricing.png)

- Plan przedsiębiorstwa
    - Wysyłaj nieograniczoną liczbę wiadomości e-mail i 50000 tekstów przez $400 miesięcznie
    - Poza 50000 tekst płacisz $0,005 dla każdego txt

    [![Cennik planu przedsiębiorstwa](./media/saas-enterprise-pricing.png "Kliknij, aby wyświetlić widok powiększony")](./media/saas-enterprise-pricing.png)

Zgodnie z wybranym planem subskrypcja zakupu na platformie Azure dla klientów z rabatem SaaS będzie mogła wysyłać uwzględnioną ilość tekstu i wiadomości e-mail na okres subskrypcji (miesiąc lub rok, jak w obszarze szczegóły subskrypcji — startDate i endDate).  Firma Contoso zlicza użycie do dołączonej ilości w bazie podstawowej bez wysyłania żadnych zdarzeń użycia do firmy Microsoft. Gdy klienci zużywają więcej niż uwzględnioną ilość, nie muszą zmieniać planów ani wykonywać żadnych innych czynności.  Firma Contoso mierzy nadwyżkę ponad uwzględnioną ilość i rozpocznie emitowanie zdarzeń użycia do firmy Microsoft w celu naliczania użycia nadwyżkowego za pomocą [interfejsu API komercyjnej usługi pomiarów w portalu Marketplace](./marketplace-metering-service-apis.md).  Firma Microsoft z kolei naliczy klientowi opłaty za użycie nadwyżkowe określone przez wydawcę w wymiarach niestandardowych. Naliczanie opłat za użycie nadwyżkowe odbywa się w następnym cyklu rozliczeniowym (co miesiąc, ale może być kwartalne lub wczesne dla niektórych klientów).  W przypadku planu miesięcznej stawki za użycie nadwyżkowe będą naliczane za każdy miesiąc, w którym nastąpiło nadwyżkowe użycie.  W przypadku rocznego planu taryfowego, gdy ilość uwzględniona w podstawowej stawce jest zużyta, wszystkie dodatkowe użycie wyemitowane przez licznik niestandardowy będzie rozliczane jako nadwyżkowe w każdym cyklu rozliczeniowym (miesięcznie) aż do końca okresu obowiązywania subskrypcji.

## <a name="billing-dimensions"></a>Wymiary rozliczeń

Każdy wymiar rozliczeń definiuje jednostkę niestandardową, za pomocą której niezależnego dostawcy oprogramowania mogą emitować zdarzenia użycia.  Wymiary rozliczeń są również używane do komunikowania się z klientem przy użyciu oprogramowania.  Są one zdefiniowane w następujący sposób:

- **ID**: odwołuje się do niezmiennego identyfikatora wymiaru podczas emitowania zdarzeń użycia.
- **Nazwa wyświetlana**: Nazwa wyświetlana skojarzona z wymiarem, na przykład "wysłane wiadomości SMS".
- **Jednostka miary**: Opis jednostki rozliczeniowej, na przykład "na wiadomość SMS" lub "na 100 wiadomości e-mail".
- **Cena za jednostkę w USD**: Cena jednej jednostki wymiaru.  Może to być 0. 
- **Miesięczna ilość uwzględniona w podstawowej**: liczba wymiarów uwzględnionych miesięcznie w przypadku klientów korzystających z cyklicznej opłaty miesięcznej musi być liczbą całkowitą. Może być równa 0 lub nieograniczona.
- **Roczna ilość uwzględniona w podstawowej**: ilość wymiarów uwzględnionych w każdym roku w przypadku klientów, którzy płacili cykliczną opłatą roczną, musi być liczbą całkowitą. Może mieć wartość 0 lub nieograniczony.

Wymiary rozliczeń są współużytkowane przez wszystkie plany oferty.  Niektóre atrybuty mają zastosowanie do wymiaru we wszystkich planach, a inne atrybuty są specyficzne dla planu.

Atrybuty, które definiują sam wymiar, są współużytkowane przez wszystkie plany oferty.  Przed opublikowaniem oferty zmiany wprowadzone do tych atrybutów z kontekstu dowolnego planu wpłyną na definicję wymiaru we wszystkich planach.  Po opublikowaniu oferty te atrybuty nie będą już edytowalne.  Te atrybuty są następujące:

- ID (Identyfikator)
- Nazwa wyświetlana
- Jednostka miary

Inne atrybuty wymiaru są specyficzne dla każdego planu i mogą mieć różne wartości z planu do zaplanowania.  Przed opublikowaniem planu można edytować te wartości i będzie to miało zastosowanie tylko do tego planu.  Po opublikowaniu planu te atrybuty nie będą już edytowalne.  Te atrybuty są następujące:

- Cena za jednostkę w USD
- Miesięczna ilość uwzględniona w podstawowej  
- Roczna ilość uwzględniona w baseIncluded  

Wymiary mają również dwie specjalne koncepcje: "Enabled" i "Infinite".

- **Włączone** wskazuje, że ten plan uczestniczy w tym wymiarze.  W przypadku tworzenia nowego planu, który nie wysyła zdarzeń użycia na podstawie tego wymiaru, warto pozostawić tę opcję niezaznaczone.  Ponadto wszystkie nowe wymiary dodane po pierwszym opublikowaniu planu będą wyświetlane jako "niewłączone" w już opublikowanym planie.  Wyłączony wymiar nie będzie widoczny na żadnych listach wymiarów dla planu widocznego dla klientów.
- **Nieskończone** reprezentowane przez symbol nieskończoności "∞" wskazuje, że ten plan uczestniczy w tym wymiarze, ale nie emituje użycia względem tego wymiaru.  Jeśli chcesz wskazać swoim klientom, że funkcjonalność reprezentowana przez ten wymiar jest uwzględniona w planie, ale bez limitu użycia.  Wymiar z nieskończonym użyciem będzie widoczny na listach wymiarów dla planu widocznego dla klientów, ze wskazaniem, że nigdy nie będzie naliczana opłata za ten plan.

>[!Note] 
>Następujące scenariusze są jawnie obsługiwane: <br> — Nowy wymiar można dodać do nowego planu.  Nowy wymiar nie zostanie włączony dla żadnego już opublikowanego planu. <br> — Możesz opublikować plan o **stałej stawce** bez żadnych wymiarów, dodać nowy plan i skonfigurować nowy wymiar dla tego planu. Nowy wymiar nie zostanie włączony dla już opublikowanych planów.

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>Ustawianie ceny wymiarów na jednostkę na obsługiwanym rynku

Podobnie jak w przypadku cen ryczałtowych, ceny za wymiar rozliczeń można ustawić na obsługiwany kraj lub region. Należy użyć funkcji importowania i eksportowania danych o cenach w centrum partnerskim w następujący sposób.

1. Zdefiniuj żądane wymiary i Oznacz, które rynki są obsługiwane. 
1. Wyeksportuj te dane do pliku.
1. Dodaj odpowiednie ceny na kraj/region i zaimportuj plik w centrum partnerskim.

Interfejs użytkownika miernika zmieni się, aby odzwierciedlić, że ceny wymiaru mogą być widoczne tylko w pliku.

[![Wymiary komercyjnej usługi pomiarowej Marketplace](media/metering-service-dimensions.png "Kliknij, aby wyświetlić widok powiększony")](media/metering-service-dimensions.png)

### <a name="private-plan"></a>Plan prywatny

Podobnie jak w przypadku planów o płaskich stawkach, plan z wymiarami może być ustawiony jako plan prywatny, dostępny tylko przez zdefiniowanych odbiorców planu.

## <a name="constraints"></a>Ograniczenia

### <a name="trial-behavior"></a>Zachowanie wersji próbnej

Naliczanie opłat naliczanych za pomocą komercyjnej usługi pomiarowej Marketplace nie jest zgodne z oferowaniem bezpłatnej wersji próbnej.  Nie jest możliwe skonfigurowanie planu do korzystania z rozliczeń taryfowych i bezpłatnej wersji próbnej.

### <a name="locking-behavior"></a>Zachowanie blokowania

Ponieważ wymiar używany z usługą pomiaru komercyjnego portalu Marketplace reprezentuje zrozumienie, w jaki sposób klient będzie płacił za usługę, wszystkie szczegóły wymiaru nie będą już edytowalne po opublikowaniu.  Ważne jest, aby wymiary zostały całkowicie zdefiniowane dla planu przed opublikowaniem.

Po opublikowaniu oferty z wymiarem nie można już zmieniać szczegółów na poziomie oferty dla tego wymiaru:

- ID (Identyfikator)
- Nazwa wyświetlana
- Jednostka miary

Po opublikowaniu planu nie można już zmieniać szczegółów na poziomie planu:

- Cena za jednostkę w USD
- Miesięczna ilość uwzględniona w podstawowej
- Roczna ilość uwzględniona w podstawowej
- Czy wymiar jest włączony dla planu, czy nie

### <a name="upper-limits"></a>Górne limity

Maksymalna liczba wymiarów, które można skonfigurować dla jednej oferty, to 18 unikatowych wymiarów.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli masz jeden z następujących problemów, możesz otworzyć bilet pomocy technicznej.

- Problemy techniczne z interfejsem API usługi pomiaru w portalu Marketplace.
- Problem, który należy eskalować ze względu na błąd lub usterkę na stronie (np. nieprawidłowe zdarzenie użycia).
- Wszystkie inne problemy związane z naliczaniem opłat.

Aby poznać opcje pomocy technicznej wydawcy i otworzyć bilet pomocy technicznej w firmie Microsoft, postępuj zgodnie z instrukcjami w temacie [obsługa komercyjnego programu Marketplace w centrum partnerskim](./support.md).

## <a name="next-steps"></a>Następne kroki

- [Interfejsy API usługi pomiaru w witrynie Marketplace](./marketplace-metering-service-apis.md)
