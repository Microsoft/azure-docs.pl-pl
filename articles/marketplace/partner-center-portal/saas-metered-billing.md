---
title: Naliczanie opłat za pomocą usługi pomiaru Marketplace | Portal Azure Marketplace
description: Ta dokumentacja zawiera Przewodnik po opublikowaniu ofert SaaS z elastycznymi modelami rozliczeń.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e5a4813301cbab16d1cffabaaa60688f6e826ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80281327"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Naliczanie opłat za pomocą usługi pomiaru Marketplace

Korzystając z usługi pomiaru Marketplace, możesz utworzyć oferty typu "oprogramowanie jako usługa" (SaaS) w komercyjnym programie Marketplace, które są rozliczone zgodnie z jednostkami niestandardowymi.  Przed opublikowaniem tej oferty należy zdefiniować wymiary rozliczeń, takie jak przepustowość, bilety lub przetworzone wiadomości e-mail.  Następnie klienci uiszczają opłaty zgodnie z ich użyciem, a system informuje firmę Microsoft za pośrednictwem interfejsu API usługi pomiaru Marketplace dla zdarzeń rozliczanych w miarę ich występowania.  

## <a name="prerequisites-for-metered-billing"></a>Wymagania wstępne dotyczące rozliczeń naliczanych

Aby oferta SaaS mogła korzystać z naliczanych opłat, musi:

* Zapoznaj się ze wszystkimi wymaganiami oferty dotyczącymi [sprzedaży za pomocą oferty firmy Microsoft](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) , jak opisano w temacie [Tworzenie oferty SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* Integruj się z [interfejsami API realizacji SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) , aby klienci mogli inicjować udostępnianie oferty i łączyć się z nią.  
* Należy skonfigurować model cen dla **stawki ryczałtowej** w celu naliczania opłat dla klientów usługi.  Wymiary są opcjonalnym rozszerzeniem modelu cen ryczałtowych. 
* Integruj się z [interfejsami API usługi pomiaru Marketplace](./marketplace-metering-service-apis.md) , aby poinformować firmę Microsoft o zdarzeniach rozliczanych.

>[!Note]
>Usługa pomiaru Marketplace jest dostępna tylko w modelu rozliczania stawki ryczałtowej i nie dotyczy modelu rozliczania na użytkownika.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Jak naliczane są rozliczenia taryfowe przy użyciu cen

Jeśli chodzi o Definiowanie oferty wraz z modelami cenowymi, ważne jest, aby zrozumieć hierarchię oferty.

* Każda oferta SaaS jest konfigurowana do sprzedaży przez firmę Microsoft.  Tego ustawienia nie można zmienić po opublikowaniu oferty.
* Każda oferta SaaS, skonfigurowana do sprzedaży przez firmę Microsoft, może mieć jeden lub więcej planów. Użytkownik subskrybuje ofertę SaaS, ale jest zakupiony przez firmę Microsoft w ramach planu.
* Z każdym planem jest skojarzony model cen: **stawka ryczałtowa** lub **za użytkownika**. Wszystkie plany w ofercie muszą być skojarzone z tym samym modelem cen. Na przykład nie może istnieć oferta, w której jeden z planów jest modelem cen ryczałtowych, a drugi to model cenowy użytkownika.
* W ramach każdego planu skonfigurowanego dla modelu rozliczania stawki ryczałtowej jest uwzględniana co najmniej jedna opłata cykliczna (która może być $0):
    * Cykliczna opłata **miesięczna** : zryczałtowany miesięczna opłata, która jest przedpłata za cykl miesięczny, gdy użytkownik kupuje plan.
    * Cykliczna opłata **roczna** : ryczałtowa opłata roczna, która jest przedpłata za cykl roczny w przypadku zakupu planu.
* Oprócz opłat cyklicznych plan może również obejmować opcjonalne wymiary używane do naliczania opłat za użycie nieuwzględnione w stałej stawce.   Każdy wymiar reprezentuje jednostkę rozliczaną, którą usługa będzie komunikować się z firmą Microsoft przy użyciu [interfejsu API usługi pomiaru Marketplace](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Przykładowa oferta

Przykładowo firma Contoso jest wydawcą z usługą SaaS o nazwie contoso Notification Services (CNS). CNS pozwala klientom wysyłać powiadomienia za pośrednictwem poczty e-mail lub tekstu. Firma Contoso jest zarejestrowana jako Wydawca w centrum partnerskim, aby komercyjny program Marketplace opublikował oferty dla klientów platformy Azure.  Istnieją dwa plany skojarzone z CNS, podane poniżej:

* Plan podstawowy
    * Wysyłaj 10000 wiadomości e-mail i 1000 dla USD/miesiąc
    * W przypadku wiadomości e-mail z 10000, płatność $1 dla każdej 100 wiadomości e-mail
    * Oprócz 1000 tekstów, płatność $0,02 dla każdego tekstu
* Plan Premium
    * Wyślij 50000 wiadomości e-mail i 10000 dla $350 miesięcznie
    * W przypadku wiadomości e-mail z 50000, płatność $0,5 dla każdej 100 wiadomości e-mail
    * Oprócz 10000 tekstów, płatność $0,01 dla każdego tekstu

Zasubskrybowanie usługi CNS przez klienta platformy Azure będzie możliwe do wysłania uwzględnionej ilości tekstu i wiadomości e-mail miesięcznie zgodnie z wybranym planem.  Firma Contoso mierzy użycie do uwzględnionej ilości bez wysyłania żadnych zdarzeń użycia do firmy Microsoft.  Gdy klienci zużywają więcej niż uwzględnioną ilość, nie muszą zmieniać planów ani wykonywać żadnych innych czynności.  Firma Contoso mierzy nadwyżkę poza uwzględnioną ilością i rozpocznie emitowanie zdarzeń użycia do firmy Microsoft w celu dodatkowego użycia przy użyciu [interfejsu API usługi pomiaru Marketplace](./marketplace-metering-service-apis.md).  Firma Microsoft z kolei naliczy klientowi opłaty za dodatkowe użycie określone przez wydawcę.

## <a name="billing-dimensions"></a>Wymiary rozliczeń

Wymiary rozliczeń są używane do komunikowania się z klientem przy użyciu oprogramowania, a także do przekazywania zdarzeń użycia do firmy Microsoft. Są one zdefiniowane w następujący sposób:

* **Identyfikator wymiaru**: niezmienny identyfikator przywoływany podczas emitowania zdarzeń użycia.
* **Nazwa wymiaru**: Nazwa wyświetlana skojarzona z wymiarem, np. "wysłane wiadomości SMS".
* **Jednostka miary**: Opis jednostki rozliczeniowej, np. "na wiadomość SMS" lub "na 100 wiadomości e-mail".
* **Cena za jednostkę**: Cena jednej jednostki wymiaru.  
* **Uwzględniona ilość w okresie miesięcznym**: liczba wymiarów uwzględnionych miesięcznie dla klientów płacących za cykliczną opłatą miesięczną musi być liczbą całkowitą.
* **Uwzględniona ilość w okresie rocznym**: liczba dostępnych wymiarów miesięcznie w przypadku klientów, którzy płacili cykliczne opłaty roczne, muszą być liczbami całkowitymi.

Wymiary rozliczeń są współużytkowane przez wszystkie plany oferty.  Niektóre atrybuty mają zastosowanie do wymiaru we wszystkich planach, a inne atrybuty są specyficzne dla planu.

Atrybuty definiujące sam wymiar są współużytkowane przez wszystkie plany oferty.  Przed opublikowaniem oferty zmiany wprowadzone do tych atrybutów z kontekstu dowolnego planu wpłyną na definicję wymiaru we wszystkich planach.  Po opublikowaniu oferty te atrybuty nie będą już edytowalne.  Te atrybuty są następujące:

* Identyfikator
* Nazwa
* Jednostka miary

Inne atrybuty wymiaru są specyficzne dla każdego planu i mogą mieć różne wartości z planu do zaplanowania.  Przed opublikowaniem planu można edytować te wartości, których dotyczy tylko ten plan.  Po opublikowaniu planu te atrybuty nie będą już edytowalne.  Te atrybuty są następujące:

* Cena za jednostkę
* Uwzględniona ilość dla miesięcznych klientów 
* Uwzględniona ilość dla rocznych klientów 

Wymiary mają również dwie specjalne koncepcje: "Enabled" i "Infinite".

* **Włączone** wskazuje, że ten plan uczestniczy w tym wymiarze.  Możesz pozostawić tę niesprawdzoną wartość, jeśli tworzysz nowy plan, który nie wysyła zdarzeń użycia na podstawie tego wymiaru.  Ponadto wszystkie nowe wymiary dodane po pierwszym opublikowaniu planu będą wyświetlane jako "niewłączone" w już opublikowanym planie.  Wyłączony wymiar nie będzie widoczny na żadnych listach wymiarów dla planu widocznego dla klientów.
* **Nieskończona**, reprezentowana przez symbol nieskończoności "∞" wskazuje, że ten plan uczestniczy w tym wymiarze, ale nie zlicza użycia względem tego wymiaru.  Jeśli chcesz wskazać swoim klientom, że funkcjonalność reprezentowana przez ten wymiar jest uwzględniona w planie, ale bez limitu użycia.  Wymiar z nieskończonym użyciem będzie widoczny na listach wymiarów dla planu widocznego dla klientów, ze wskazaniem, że nigdy nie będzie naliczana opłata za ten plan.

>[!Note] 
>Następujące scenariusze są jawnie obsługiwane: <br> — Nowy wymiar można dodać do nowego planu.  Nowy wymiar nie zostanie włączony dla żadnego już opublikowanego planu. <br> — Możesz opublikować plan o **stałej stawce** bez żadnych wymiarów, dodać nowy plan i skonfigurować nowy wymiar dla tego planu. Nowy wymiar nie zostanie włączony dla już opublikowanych planów.

## <a name="constraints"></a>Ograniczenia

### <a name="trial-behavior"></a>Zachowanie wersji próbnej

Naliczanie opłat naliczanych przy użyciu usługi pomiaru Marketplace nie jest zgodne z oferowaniem bezpłatnej wersji próbnej.  Nie jest możliwe skonfigurowanie planu do korzystania z rozliczeń taryfowych i bezpłatnej wersji próbnej.

### <a name="locking-behavior"></a>Zachowanie blokowania

Ponieważ wymiar używany z usługą pomiaru w portalu Marketplace reprezentuje zrozumienie, jak klient będzie płacił za usługę, wszystkie szczegóły wymiaru nie będą już edytowalne po opublikowaniu.  Ważne jest, aby wymiary zostały całkowicie zdefiniowane dla planu przed opublikowaniem.
  
Po opublikowaniu oferty z wymiarem nie można już zmieniać szczegółów na poziomie oferty dla tego wymiaru:

* Identyfikator
* Nazwa
* Jednostka miary

Po opublikowaniu planu nie można już zmieniać szczegółów na poziomie planu:

* Cena za jednostkę
* Uwzględniona ilość na miesięczny okres
* Uwzględniona ilość na okres roczny
* Czy wymiar jest włączony dla planu

### <a name="upper-limits"></a>Górne limity

Maksymalna liczba wymiarów, które można skonfigurować dla jednej oferty, to 18 unikatowych wymiarów.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli masz jedną z następujących czynności, możesz otworzyć bilet pomocy technicznej.

* Problemy techniczne z interfejsem API usługi pomiaru w portalu Marketplace.
* Problem, który należy eskalować ze względu na błąd lub usterkę na stronie (np. nieprawidłowe zdarzenie użycia).
* Wszystkie inne problemy związane z naliczaniem opłat. 

Wykonaj poniższe kroki, aby przesłać bilet pomocy technicznej:

1. Przejdź do [strony pomocy technicznej](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). Pierwsze niektóre menu rozwijane są wypełniane automatycznie. Aby uzyskać pomoc techniczną, zidentyfikuj rodzinę produktów jako **usługi w chmurze i online**, czyli produkt jako **wydawcę w portalu Marketplace**.  Nie zmieniaj wstępnie wypełnionego menu rozwijanego.
2. W obszarze "Wybierz wersję produktu" Wybierz pozycję **zarządzanie ofertami na żywo**.
3. W obszarze "Wybierz kategorię, która najlepiej opisz problem", wybierz pozycję **aplikacje SaaS**.
4. W obszarze "Wybierz problem, który najlepiej opisuje problem" Wybierz pozycję **naliczanie opłat**.
5. Po wybraniu przycisku **dalej** nastąpi przekierowanie do strony **szczegółów problemu** , na której można wprowadzić więcej szczegółów dotyczących problemu.

Aby uzyskać więcej opcji pomocy technicznej wydawcy [, zobacz pomoc techniczną dotyczącą komercyjnego programu w witrynie Marketplace w centrum partnerskim](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) .

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [interfejsy API usługi pomiaru witryny Marketplace](./marketplace-metering-service-apis.md) .
