---
title: Interfejsy API usługi pomiaru — często zadawane pytania — komercyjne witryny Marketplace firmy Microsoft
description: Często zadawane pytania dotyczące interfejsów API usługi pomiarowej dla ofert SaaS w Microsoft AppSource i witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320004"
---
# <a name="marketplace-metered-billing-apis---faq"></a>Interfejsy API rozliczeń mierzonych w witrynie Marketplace — często zadawane pytania

Gdy klient zasubskrybuje usługę SaaS lub aplikację platformy Azure z planem zarządzanych aplikacji z naliczanymi opłatami, będzie można śledzić użycie dla każdego używanego wymiaru rozliczania.  Jeśli użycie przekroczy uwzględnioną ilość ustaloną przez klienta, usługa będzie emitować zdarzenia dotyczące użycia do firmy Microsoft.

## <a name="for-both-saas-offers-and-managed-apps"></a>W przypadku ofert SaaS i aplikacji zarządzanych

### <a name="how-often-is-it-expected-to-emit-usage"></a>Jak często powinieneś emitować użycie?

Najlepiej jest emitować użycie co godzinę w ciągu ostatniej godziny, tylko wtedy, gdy wystąpiło użycie w powyższej godzinie.

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>Czy istnieje krótki okres między jedną emisją a kolejną?

Nie ma takiego ograniczenia. Emituj tylko użycie w miarę ich występowania. Jeśli na przykład musisz przesłać tylko jedną jednostkę użycia na okres istnienia subskrypcji, możesz to zrobić.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Jakie jest maksymalne opóźnienie między czasem wystąpienia zdarzenia a czasem, gdy zdarzenie użycia jest emitowane do firmy Microsoft?

W idealnym przypadku zdarzenie użycia jest emitowane co godzinę dla zdarzeń, które wystąpiły w ciągu ostatniej godziny. Jednak oczekiwane są opóźnienia. Maksymalne dozwolone opóźnienie to 24 godziny, po upływie których zdarzenia użycia nie zostaną zaakceptowane. Najlepszym rozwiązaniem jest zbieranie godzinowego użycia i emisja jest jak jedno zdarzenie na koniec godziny.

Na przykład, jeśli zdarzenie użycia występuje o 1 PM dziennie, do 1 PM w następnym dniu będzie można wyemitować zdarzenie użycia powiązane z tym zdarzeniem.  W przypadku, gdy system emitujący użycie nie działa, może odzyskać, a następnie wysłać zdarzenie użycia dla interwału godzin, w którym wystąpiło użycie, bez utraty dokładności.

W przypadku upływu 24 godzin od rzeczywistego użycia nadal można emitować zużyte jednostki z późniejszymi zdarzeniami użycia.  Jednakże takie rozwiązanie może obniżyć wiarygodność raportów o zdarzeniach dotyczących rozliczeń dla klienta końcowego.  Zalecamy uniknięcie wysyłania emisji liczników raz dziennie/tygodnia/miesiąca.  Będzie to trudniejsze do zrozumienia faktycznego użycia przez klienta oraz rozwiązania problemów lub pytań, które mogą zostać zgłoszone w odniesieniu do zdarzeń użycia.

Kolejną przyczyną, aby wysyłać użycie co godzinę, jest uniknięcie sytuacji, w których użytkownik anuluje subskrypcję, zanim wydawcę wyśle miesięczne/cotygodniowe/comiesięczne zdarzenie emisji.

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>Co się stanie w przypadku wysłania więcej niż jednego zdarzenia użycia w tej samej godzinie?

Tylko jedno zdarzenie użycia jest akceptowane dla jednogodzinnego interwału. Interwał godzinowy zaczyna się od minuty 0 i zostaje zakończony przy minucie 59.  Jeśli jest emitowane więcej niż jedno zdarzenie użycia dla tej samej godziny, wszystkie kolejne zdarzenia użycia są porzucane jako duplikaty.

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>Co się stanie, gdy klient anuluje zakup w czasie dozwolonym przez zasady anulowania?

Wartość stawki ryczałtowej nie zostanie obciążona, ale użycie nadwyżkowe będzie miało zastosowanie.

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>Czy plany taryfowe mogą być używane w przypadku płatności jednorazowych?

Tak, możesz zdefiniować niestandardowy wymiar jako jedną jednostkę płatności jednorazowej i emitować ją tylko raz dla każdego klienta.

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>Czy plany taryfowe mogą być używane w modelu cen warstwowych?

Tak, można go zaimplementować z każdym wymiarem niestandardowym reprezentującym pojedynczą warstwę cenową.

Firma Contoso chce na przykład obciążać $0,5 pocztą e-mail w przypadku pierwszych 1000 wiadomości e-mail, $0,4 dla wiadomości e-mail między 1000 i 5000 wiadomości e-mail oraz $0,2 pocztą e-mail w przypadku powyższych wiadomości e-mail. Mogą definiować trzy wymiary niestandardowe, które odpowiadają trzem warstwom cenowym poczty e-mail. Emituj jednostki pierwszego wymiaru, tak długo, jak liczba wiadomości e-mail pozostaje poniżej 1000, a następnie jednostki drugiego wymiaru, gdy liczba wiadomości e-mail należy do zakresu od 1000 do 5000, a na koniec jednostki trzeciego wymiaru dla powyżej 5000 wiadomości e-mail.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Co się stanie w przypadku awarii usługi pomiaru Marketplace?

Jeśli dostawca ISV wyśle niestandardowy licznik i otrzyma błąd, który mógł zostać spowodowany przez problem związany z firmą Microsoft (zwykle w przypadku, gdy podobne zdarzenia zostały zaakceptowane przed wystąpieniem błędu), wówczas dostawca ISV powinien czekać i ponowić próbę emisji.

Jeśli błąd będzie się powtarzać, prześlij ponownie ten licznik niestandardowy do następnej godziny (sumując ilość). Kontynuuj ten proces do momentu otrzymania odpowiedzi bez błędu.

## <a name="for-saas-offers-only"></a>Tylko dla ofert SaaS

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Co się stanie, gdy wyemitujesz użycie dla subskrypcji SaaS, która została już anulowana.

Każde zdarzenie użycia emitowane na platformie Marketplace nie zostanie zaakceptowane po usunięciu subskrypcji usługi SaaS.

Użycie może być emitowane tylko dla subskrypcji w stanie subskrybowanym (a nie dla subskrypcji w `PendingFulfillmentStart` , `Suspended` , lub `Unsubscribed` stanu).

Jedynym wyjątkiem jest użycie raportowania dla czasu, który był przed anulowaniem subskrypcji SaaS.

Na przykład klient anulował subskrypcję usługi SaaS dzisiaj o godzinie 3 godzin. Teraz 5 PM Wydawca może nadal emitować użycie przez okres między 6 PM wczoraj i 3 PM dla tej subskrypcji SaaS.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Czy możesz uzyskać listę wszystkich subskrypcji SaaS, w tym aktywnych i niesubskrybowanych subskrypcji?

Tak, gdy wywołasz [interfejs API listy Pobierz subskrypcje](pc-saas-fulfillment-api-v2.md#subscription-apis) , ponieważ zawiera on listę wszystkich subskrypcji SaaS. Pole Stan w odpowiedzi dla każdej subskrypcji SaaS przechwytuje, czy subskrypcja jest aktywna, czy nie została anulowana.

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>Czy Data początkowa i końcowa okresu subskrypcji SaaS i nadmierna emisja użycia są połączone?

Zdarzenia nadwyżkowe mogą być emitowane w dowolnym momencie dla istniejącej subskrypcji SaaS w stanie *subskrybowanym* . Jest odpowiedzialny za wydawcę, aby wyemitować zdarzenia użycia na podstawie zasad zdefiniowanych w planie rozliczeniowym. Nadwyżka musi być obliczana na podstawie dat zdefiniowanych w okresie subskrypcji SaaS. 

Na przykład jeśli Wydawca definiuje plan SaaS obejmujący 1000 wiadomości e-mail dla $100 w miesięcznej stawce, każda wiadomość e-mail powyżej 1000 jest rozliczana $1 za pośrednictwem niestandardowego wymiaru.

Gdy klient kupuje i aktywuje subskrypcję w dniu 6 stycznia, będzie naliczana wiadomość e-mail z 1000ą uwzględnioną w ramach stawki ryczałtowej. Tak więc jeśli do 5 lutego (koniec pierwszego miesiąca subskrypcji) zostanie wysłanych tylko 900 wiadomości e-mail, klient będzie obciążany ustaloną stawkę tylko za pierwszy miesiąc tej subskrypcji, a żadne zdarzenia użycia nadwyżkowe nie będą emitowane przez wydawcę od 6 stycznia do 5 lutego. 6 lutego subskrypcja zostanie automatycznie odnowiona, a liczba zostanie uruchomiona ponownie. Jeśli 15 lutego klient osiągnął 1000 wysłanych wiadomości e-mail, reszta wiadomości e-mail wysyłanych do 5 marca będzie naliczana jako nadwyżka ($1 na adres e-mail) na podstawie zdarzeń użycia nadwyżkowego emitowanych przez wydawcę.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [interfejsy API usługi pomiaru Marketplace](./marketplace-metering-service-apis.md).
