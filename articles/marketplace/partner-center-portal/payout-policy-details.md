---
title: Szczegóły zasad wypłaty — Azure Marketplace
description: Szczegóły dotyczące zasad wypłaty, w tym harmonogramów i odniesień.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen-ms
ms.author: mingshen
ms.date: 04/24/2020
ms.openlocfilehash: 801d3ca383ebde75d6590558638df6820e538249
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529947"
---
# <a name="payout-policy-details"></a>Szczegóły zasad wypłat

W tym artykule omówiono proces wypłaty firmy Microsoft, harmonogram wypłaty, miejsce, w którym można znaleźć stan wypłaty oraz zasady związane z oddziałami.

## <a name="where-to-find-upcoming-payouts"></a>Gdzie można znaleźć nadchodzące wypłaty

W centrum partnerskim wybierz **wypłatę** w prawym górnym rogu ekranu:

![Przedstawia ikonę wypłaty w prawym górnym rogu portalu Centrum partnerskiego.](./media/payout-overview.png)

> [!TIP]
> Nie wszystkie role konta mają dostęp do informacji o wypłatach. Aby uzyskać szczegółowe informacje, zobacz [role i uprawnienia, aby uzyskać dostęp do raportu wypłaty](./payout-summary-overview.md#roles-and-permissions).

## <a name="payment-schedules"></a>Harmonogramy płatności

W poniższych sekcjach opisano nasz proces wypłat.

### <a name="enterprise-agreement-transactions-after-may-1-2020"></a>Umowa Enterprise transakcji od 1 maja 2020

#### <a name="update-to-our-commercial-marketplace-publisher-payout-model"></a>Aktualizacja do naszego komercyjnego modelu wypłaty wydawcy w portalu Marketplace

Począwszy od 1 maja 2020, aktualizujemy zasady naszych ofert związanych z produktami zakupionymi w witrynie Azure Marketplace lub AppSource przez klientów z Umowa Enterpriseem firmy Microsoft. Gdy klient kupuje produkt z witryny Azure Marketplace lub AppSource przy użyciu istniejących w firmie Microsoft Umowa Enterprise dla transakcji po 1 maja, 2020 rozpocznie się wystawianie płatnych opłat w okresie następnej wypłaty za 30 dni. Transakcje, w przypadku których klient korzysta z karty kredytowej, nie uległy zmianie i nadal będzie miał 30-dniowy okres przechowywania przed wypłatą. W tych tabelach przedstawiono szczegółowe informacje o harmonogramie wypłaty.

> [!NOTE]
> Zapoznaj się z artykułem [dotyczącym niepłatności klienta](#process-for-customer-non-payment) w przypadku akcji podejmowanych w przypadku niepowodzenia płacenia przez klienta, ale wystawiłeś już wypłatę.

| Zdarzenie  | Data (UTC) | Widoczność partnerów: Raport wypłaty Centrum partnerskiego  |  Widoczność partnerów: Analiza Centrum partnerskiego\* |
| --- | --- | --- | --- |
| Transakcja lub miesiąc użycia | 8/1/2020 – 8/31/2020 | Brak | **Raport użycia**: pokazywane nowe użycie (odświeżone co cztery godziny)<br>**Raport z kolejnością**: brak |
| Zakończenie okresu (miesiąc) | 8/31/2020 | Brak | **Raport użycia**: pokazywane zużycie końcowe miesiąca<br>**Raport z kolejnością**: brak |
| Wygenerowane zamówienie | 9/3/2020 – 9/7/2020 | Brak | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Oblicz zdobywanie wypłat | 9/4/2020 – 9/10/2020 | Oznaczono jako **nieprzetworzony** w historii transakcji na pulpicie nawigacyjnym wypłaty | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Miesięczna wypłata | 10/5/2020 | Oznaczone jako **nadchodzące** w historii transakcji na pulpicie nawigacyjnym wypłaty | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Data wypłaty\** | 10/15/2020 | Oznaczone jako **wysłane** w historii transakcji i w sekcji płatności na pulpicie nawigacyjnym wypłaty | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Zebrana faktura klienta | 12/1/2020 | Oznaczone jako **wysłane** w historii transakcji i w sekcji płatności na pulpicie nawigacyjnym wypłaty | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne  |
|  |  |  |  |

\*Raporty użycia i zamówień są dostępne w sekcji Analiza w centrum partnerskim.</br>\** Data wypłaty przypada w pacyficznym czasie standardowym (PST).

### <a name="customers-who-pay-using-credit-card-or-invoice"></a>Klienci, którzy płacisz przy użyciu karty kredytowej lub faktury

Wszystkie zakupy z kartą kredytową lub fakturą miesięczną mają 30-dniowy okres przechowywania, aby upewnić się, że środki zostały wyczyszczone i nie obciążeń zwrotnych ani nie ma podejrzenia oszustwa.

| Zdarzenie  | Data (UTC) | Widoczność partnerów: Raport wypłaty Centrum partnerskiego  |  Widoczność partnerów: Analiza Centrum partnerskiego\*  |
| --- | --- | --- | --- |
| Transakcja lub miesiąc użycia | 8/1/2019 - 8/31/2019 | Brak | **Raport użycia**: pokazywane nowe użycie (odświeżone co cztery godziny)<br>**Raport z kolejnością**: brak |
| Zakończenie okresu (miesiąc) | 8/31/2019 | Brak | **Raport użycia**: pokazywane zużycie końcowe miesiąca<br>**Raport z kolejnością**: brak |
| Wygenerowane zamówienie | 9/3/2019 – 9/7/2019 | Brak | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Zebrana faktura klienta | 9/7/2019 – 9/10/2019 | Brak | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Oblicz wypłatę | 9/8/2019 -9/12/2019 | Oznaczono jako **nieprzetworzony** w historii transakcji na pulpicie nawigacyjnym wypłaty | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Miesięczna wypłata | 11/5/2019\* | Oznaczone jako **nadchodzące** w historii transakcji na pulpicie nawigacyjnym wypłaty | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Data wypłaty\** | 11/15/2019 | Oznaczone jako **wysłane** w historii transakcji i w sekcji płatności na pulpicie nawigacyjnym wypłaty | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
|  |  |  |  |

\*Raporty użycia i zamówień są dostępne w sekcji Analiza w centrum partnerskim.</br>\** Data wypłaty przypada w pacyficznym czasie standardowym (PST).

### <a name="enterprise-agreement-transactions-prior-to-may-1-2020"></a>Umowa Enterprise transakcje przed 1 maja 2020

Wszystkie zakupy występujące przed tą datą są przetwarzane i płatne zgodnie z poniższym harmonogramem po zebraniu przez firmę Microsoft płatności od klientów i przetworzeniu opłaty za witrynę Marketplace.

| Zdarzenie  | Data (UTC)  | Widoczność partnerów: Raport wypłaty Centrum partnerskiego  |  Widoczność partnerów: Analiza Centrum partnerskiego\*  |
| --- | --- | --- | --- |
| Transakcja lub miesiąc użycia | 8/1/2019 – 8/31/2019 | Brak | **Raport użycia**: pokazywane nowe użycie (odświeżone co cztery godziny)<br>**Raport z kolejnością**: brak |
| Zakończenie okresu (miesiąc) | 8/31/2019 | Brak | **Raport użycia**: pokazywane zużycie końcowe miesiąca<br>**Raport z kolejnością**: brak |
| Wygenerowane zamówienie | 9/3/2019 – 9/7/2019 | Brak | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Zebrana faktura klienta | 12/1/2019 | Brak | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Oblicz wypłatę | 12/5/2019 – 12/7/2019 | Oznaczono jako **nieprzetworzony** w historii transakcji na pulpicie nawigacyjnym wypłaty | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Miesięczna wypłata | 1/5/2019 | Oznaczone jako **nadchodzące** w historii transakcji na pulpicie nawigacyjnym wypłaty | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
| Data wypłaty\** | 1/15/2019 | Oznaczone jako **wysłane** w historii transakcji i w sekcji płatności na pulpicie nawigacyjnym wypłaty | **Raport użycia**: użycie pokazane z identyfikatorem IDZamówienia/OrderLineItemID<br>**Zamówienie raportu**: zamówienia klienta wyświetlane jako aktywne |
|  |  |  |  |

\*Raporty użycia i zamówień są dostępne w sekcji Analiza w centrum partnerskim.</br>\** Data wypłaty przypada w pacyficznym czasie standardowym (PST).

## <a name="process-for-customer-non-payment"></a>Proces dla niepłaty klienta

W rzadkich przypadkach firma Microsoft nie jest w stanie zebrać płatności od klientów na potrzeby zakupów w portalu Marketplace. Gdy klient nie może uregulować firmy Microsoft zgodnie z harmonogramem rozliczeń, rozpocznie się proces zbierania danych. Ten proces trwa około czterech miesięcy i obejmuje trwałą komunikację od firmy Microsoft. Jeśli płatność nie zostanie odebrana na koniec tego procesu, firma Microsoft zapisuje środki jako niezbierane.

W ramach procesu wypłaty w tym miejscu firma Microsoft mogła już wypłacić środki na wydawców, które ostatecznie nie są zbierane. W związku z tym mamy proces uzgadniania tych kwot. Aby mieć pewność, że płatność (już otrzymana) może zostać uzgodniona, otrzymasz powiadomienie, gdy klient jest w procesie zbierania danych, a zakupy są prawdopodobnie wypisywane.

Firma Microsoft zwróci wszelkie płatne opłaty za korzystanie z jednej z następujących metod: (1) firma Microsoft może odjęciu niepłatnych kwot od przyszłych wypłat. na przykład jeśli $1 000 wypłaty są uznawane za niezbierane i odpisywane, przyszłe wypłaty zostaną wstrzymane do momentu odzyskania $1 000 lub (2) firma Microsoft może zażądać zwrotu lub wydawców faktury za jakiekolwiek niezebrane kwoty.

Poniżej przedstawiono przykładowy harmonogram:

| Zdarzenie | Przybliżona data | Widoczność partnerów |
| --- | --- | --- |
| Przykładowa data wypłaty | 10/15/2020 | Sekcja oznaczone jako **wysłane** w historii transakcji i w sekcji płatności na pulpicie nawigacyjnym wypłaty |
| <font color="red">Jeśli klient nie płacisz firmie Microsoft</font> | 12/2/2020 – 12/5/2020 | Bez zmian, taka sama jak powyżej |
| Klient otrzymuje wiadomość e-mail z pierwszą opóźnioną płatnością | 12/6/2020 | Brak |
| Klient otrzymuje zwykłe wiadomości e-mail o rosnącej pilności | 12/7/2020 – 1/31/2021 | Brak |
| Wydawca otrzymuje powiadomienie, że jest to prawdopodobnie | 1/7/2021 | Powiadomienie e-mail wysłane do wydawcy, że jego klient nie przesłał jeszcze płatności. Identyfikator transakcji i kwota dolara są uwzględniane. |
| Klient otrzymuje powiadomienie o zakończeniu | 2/1/2021 | Brak |
| Zakończenia procesu zbierania/fundusze są wypisywane | 2/15/2021 | Powiadomienie e-mail wysłane do wydawcy, że środki zostały wypisane. Identyfikator transakcji i kwota dolara są uwzględniane. |
| Wypłata jest potrącana | 3/1/2021 | Wydawca zobaczy negatywną transakcję w zestawieniu wypłaty Centrum partnerskiego |
| Wypłata została wstrzymana | 3/15/2021 | Przyszłe wypłaty zostaną wyświetlone w zestawieniu wypłaty w centrum partnerskim. Wydawca nie otrzyma płatności do momentu, gdy saldo nie będzie już ujemne.  |
|||

## <a name="number-of-days-for-payments-to-reach-a-payout-account"></a>Liczba dni, w przypadku których płatność osiągnie konto płatne

Zwykle wysyłamy wszelkie płatności należne w danym miesiącu w piętnastym dniu tego miesiąca, ale w celu uzyskania płatności na Twoje konto zajmiemy dodatkowy czas. Liczba dni zależy od formy płatności używanej dla Twojego konta, zgodnie z poniższym opisem.

> [!NOTE]
> Pokazane poniżej dni są przybliżone; Płatność za korzystanie z konta może potrwać dłużej lub krócej.

| Metoda płatności     | Liczba dni do uzyskania dostępu do konta wypłaty     |
|--------------------|--------------------------------------------|
| PayPal             | 1 dzień roboczy                             |
| ACH/SEPA           | 2-3 dni roboczych                          |
| Przelew      | 7-10 dni roboczych                         |

## <a name="next-step"></a>Następny krok

- [Szczegóły podatku](./tax-details-paid-transactions.md)
