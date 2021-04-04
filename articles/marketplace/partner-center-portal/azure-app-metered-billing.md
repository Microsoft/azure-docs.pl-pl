---
title: Naliczanie opłat za aplikacje zarządzane przy użyciu usługi pomiaru Marketplace | Portal Azure Marketplace
description: Ta dokumentacja zawiera Przewodnik po opublikowaniu aplikacji platformy Azure przy użyciu elastycznych modeli rozliczeń.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: d015cec30e516541b50c2acfac38fad898965e1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436355"
---
# <a name="managed-application-metered-billing"></a>Rozliczenia taryfowe zarządzanej aplikacji 

Za pomocą usługi pomiaru w portalu Marketplace można tworzyć zarządzane plany aplikacji dla ofert aplikacji platformy Azure, które są naliczone zgodnie z jednostkami niestandardowymi. Przed opublikowaniem tej oferty należy zdefiniować wymiary rozliczeń, takie jak przepustowość, bilety lub przetworzone wiadomości e-mail. Następnie klienci uiszczają opłaty zgodnie z ich użyciem.  System powiadomi firmę Microsoft za pośrednictwem interfejsu API usługi pomiaru Marketplace w przypadku zdarzeń rozliczanych w miarę ich występowania.

## <a name="prerequisites-for-metered-billing"></a>Wymagania wstępne dotyczące rozliczeń naliczanych

Aby plan aplikacji zarządzanej mógł korzystać z naliczanych opłat, musi:

* Zapoznaj się ze wszystkimi wymaganiami oferty opisanymi w temacie [Tworzenie oferty aplikacji platformy Azure](../create-new-azure-apps-offer.md).
* Skonfiguruj **ceny** opłat dla klientów za miesięczny koszt usługi. Cena może być równa zero, jeśli nie chcesz obciążyć ustalonej opłaty, a zamiast tego wykorzystasz całkowicie z naliczanych opłat.
* Ustaw **Wymiary rozliczeń** dla zdarzeń zliczania, dla których klient będzie obciążany, w oparciu o stałą stawkę.
* Integruj się z [interfejsami API usługi pomiaru Marketplace](./marketplace-metering-service-apis.md) , aby poinformować firmę Microsoft o zdarzeniach rozliczanych.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Jak naliczane są rozliczenia taryfowe przy użyciu cen

Jeśli chodzi o Definiowanie oferty wraz z modelami cenowymi, ważne jest, aby zrozumieć hierarchię oferty.

* Każda oferta aplikacji platformy Azure może mieć szablon rozwiązania lub zarządzane plany aplikacji.
* Opłaty taryfowe są implementowane tylko w przypadku planów aplikacji zarządzanych.
* Z każdym planem zarządzanej aplikacji jest skojarzony model cen. 
* Model cen ma miesięczną opłatą cykliczną, którą można ustawić na $0.
* Oprócz opłaty cyklicznej plan może również obejmować opcjonalne wymiary używane do naliczania opłat za użycie nieuwzględnione w stałej stawce. Każdy wymiar reprezentuje jednostkę rozliczaną, którą usługa będzie komunikować się z firmą Microsoft przy użyciu [interfejsu API usługi pomiaru Marketplace](marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Przykładowa oferta

Przykładowo firma Contoso jest wydawcą z zarządzaną usługą aplikacji o nazwie contoso Analytics (CoA). Certyfikat CoA pozwala klientom analizować duże ilości danych na potrzeby raportowania i magazynowania danych. Firma Contoso jest zarejestrowana jako Wydawca w centrum partnerskim, aby komercyjny program Marketplace opublikował oferty dla klientów platformy Azure. Istnieją dwa plany powiązane z certyfikatem CoA opisanym poniżej:

* Plan podstawowy
    * Analizuj 100 GB i Generuj raporty 100 dla $0 miesięcznie
    * Ponad 100 GB, płatność $10 za każdy 1 GB
    * Ponad 100 raportów, płatność $1 dla każdego raportu
* Plan Premium
    * Analizuj 1000 GB i Generuj raporty 1000 dla $350 miesięcznie
    * Ponad 1000 GB, płatność $100 za każdy 1 TB
    * Ponad 1000 raportów, płatność $0,5 dla każdego raportu

Subskrybowanie usługi CoA przez klienta platformy Azure może analizować i generować raporty miesięcznie zgodnie z wybranym planem. Firma Contoso mierzy użycie do uwzględnionej ilości bez wysyłania żadnych zdarzeń użycia do firmy Microsoft. Gdy klienci zużywają więcej niż uwzględnioną ilość, nie muszą zmieniać planów ani wykonywać żadnych innych czynności. Firma Contoso mierzy nadwyżkę poza uwzględnioną ilością i rozpocznie emitowanie zdarzeń użycia do firmy Microsoft w celu dodatkowego użycia przy użyciu [interfejsu API usługi pomiaru Marketplace](./marketplace-metering-service-apis.md). Firma Microsoft z kolei naliczy klientowi opłaty za dodatkowe użycie określone przez wydawcę.

## <a name="billing-dimensions"></a>Wymiary rozliczeń

Wymiary rozliczeń są używane do komunikowania się z klientem przy użyciu oprogramowania.  Te wymiary są również używane do przekazywania zdarzeń użycia do firmy Microsoft. Są one zdefiniowane w następujący sposób:

* **Identyfikator wymiaru**: niezmienny identyfikator przywoływany podczas emitowania zdarzeń użycia.
* **Nazwa wymiaru**: Nazwa wyświetlana skojarzona z wymiarem, na przykład "wysłane wiadomości SMS".
* **Jednostka miary**: Opis jednostki rozliczeniowej, na przykład "na wiadomość SMS" lub "na 100 wiadomości e-mail".
* **Cena za jednostkę**: Cena jednej jednostki wymiaru.
* **Uwzględniona ilość w okresie miesięcznym**: liczba wymiarów uwzględnionych miesięcznie dla klientów płacących za cykliczną opłatą miesięczną musi być liczbą całkowitą.

Wymiary rozliczeń są współużytkowane przez wszystkie plany oferty. Niektóre atrybuty mają zastosowanie do wymiaru we wszystkich planach, a inne atrybuty są specyficzne dla planu.

Atrybuty, które definiują sam wymiar, są współużytkowane przez wszystkie plany oferty. Przed opublikowaniem oferty zmiany wprowadzone do tych atrybutów z kontekstu dowolnego planu wpłyną na definicję wymiaru we wszystkich planach. Po opublikowaniu oferty te atrybuty nie będą już edytowalne. Atrybuty są następujące:

* Identyfikator
* Nazwa
* Jednostka miary

Inne atrybuty wymiaru są specyficzne dla każdego planu i mogą mieć różne wartości z planu do zaplanowania.  Przed opublikowaniem planu można edytować te wartości i będzie to miało zastosowanie tylko do tego planu. Po opublikowaniu planu te atrybuty nie będą już edytowalne. Atrybuty są następujące:

* Cena za jednostkę
* Uwzględniona ilość dla miesięcznych klientów 
* Uwzględniona ilość dla rocznych klientów 

Wymiary mają również dwie specjalne koncepcje: "Enabled" i "Infinite".

* **Włączone** wskazuje, że ten plan uczestniczy w tym wymiarze.  Tę opcję można pozostawić niesprawdzoną w przypadku tworzenia nowego planu, który nie wysyła zdarzeń użycia opartych na tym wymiarze. Ponadto wszystkie nowe wymiary dodane po pierwszym opublikowaniu planu będą wyświetlane jako "niewłączone" w już opublikowanym planie.  Wyłączony wymiar nie będzie widoczny na żadnych listach wymiarów dla planu widocznego dla klientów.
* **Nieskończona**, reprezentowana przez symbol nieskończoności "∞" wskazuje, że ten plan uczestniczy w tym wymiarze bez pomiaru użycia względem tego wymiaru. Jeśli chcesz wskazać swoim klientom, że funkcjonalność reprezentowana przez ten wymiar jest uwzględniona w planie, ale bez limitu użycia.  Wymiar z nieskończonym użyciem będzie widoczny na listach wymiarów dla planu widocznego dla klientów.  Ten plan nigdy nie wiąże się z opłatami.

>[!Note] 
>Następujące scenariusze są jawnie obsługiwane:  <br> — Nowy wymiar można dodać do nowego planu.  Nowy wymiar nie zostanie włączony dla żadnego już opublikowanego planu. <br> — Można opublikować plan ze stałą opłatą miesięczną i bez żadnych wymiarów, dodać nowy plan i skonfigurować nowy wymiar dla tego planu. Nowy wymiar nie zostanie włączony dla już opublikowanych planów.

## <a name="constraints"></a>Ograniczenia

### <a name="locking-behavior"></a>Zachowanie blokowania

Wymiar używany z usługą pomiaru Marketplace przedstawia zrozumienie, w jaki sposób klient będzie płacił za usługę.  Po opublikowaniu oferty wszystkie szczegóły wymiaru nie są już edytowalne.  Przed opublikowaniem oferty ważne jest, aby wymiary zostały w pełni zdefiniowane.

Po opublikowaniu oferty z wymiarem nie można już zmieniać szczegółów na poziomie oferty dla tego wymiaru:

* Identyfikator
* Nazwa
* Jednostka miary

Po opublikowaniu planu nie można już zmieniać szczegółów na poziomie planu:

* Cena za jednostkę
* Uwzględniona ilość na miesięczny okres
* Czy wymiar jest włączony dla planu

>[!Note]
>Naliczanie opłat za pomocą usługi pomiaru Marketplace nie jest jeszcze obsługiwane w chmurze Azure Government.

### <a name="upper-limits"></a>Górne limity

Maksymalna liczba wymiarów, które można skonfigurować dla jednej oferty, to 30 unikatowych wymiarów.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli masz jeden z następujących problemów, możesz otworzyć bilet pomocy technicznej.

* Problemy techniczne z interfejsem API usługi pomiaru w portalu Marketplace.
* Problem, który należy eskalować ze względu na błąd lub usterkę na stronie (np. nieprawidłowe zdarzenie użycia).
* Wszystkie inne problemy związane z naliczaniem opłat.

Postępuj zgodnie z instrukcjami w temacie [obsługa komercyjnego programu Marketplace w centrum partnerskim](../support.md) , aby poznać opcje pomocy technicznej wydawcy i otworzyć bilet pomocy technicznej w firmie Microsoft.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [interfejsy API usługi pomiaru witryny Marketplace](./marketplace-metering-service-apis.md) .
