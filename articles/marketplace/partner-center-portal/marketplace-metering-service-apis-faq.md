---
title: Interfejsy API usługi pomiaru — często zadawane pytania — komercyjne witryny Marketplace firmy Microsoft
description: Często zadawane pytania dotyczące interfejsów API usługi pomiarowej dla ofert SaaS w Microsoft AppSource i witrynie Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: eb27089777baaaa7a29e020318fbc7635792af2d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857900"
---
# <a name="marketplace-metering-service-apis---faq"></a>Interfejsy API usługi pomiaru w witrynie Marketplace — często zadawane pytania

Gdy użytkownik platformy Azure subskrybuje usługę SaaS, która obejmuje rozliczenia taryfowe, będzie można śledzić użycie dla każdego wymiaru rozliczeń używanego przez klienta. Jeśli użycie przekroczy uwzględnioną ilość ustaloną przez klienta, usługa będzie emitować zdarzenia dotyczące użycia do firmy Microsoft.

## <a name="emit-usage-events"></a>Emituj zdarzenia użycia

>[!Note]
>Ta sekcja ma zastosowanie tylko do ofert SaaS, w których co najmniej jeden plan ma wymiary usługi pomiarowej zdefiniowane w momencie publikowania oferty.

![Emituj zdarzenia użycia](media/isv-emits-usage-event.png)

Aby uzyskać informacje na temat kontraktu interfejsu API na potrzeby emitowania zdarzeń użycia, zobacz [interfejs API zdarzeń użycia usługi Batch SaaS](./marketplace-metering-service-apis.md#batch-usage-event) .

### <a name="how-often-is-it-expected-to-emit-usage"></a>Jak często powinieneś emitować użycie?

Najlepiej jest emitować użycie co godzinę w ciągu ostatniej godziny, tylko wtedy, gdy wystąpiło użycie w powyższej godzinie.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Jakie jest maksymalne opóźnienie między czasem wystąpienia zdarzenia a czasem, gdy zdarzenie użycia jest emitowane do firmy Microsoft?

W idealnym przypadku zdarzenie użycia jest emitowane co godzinę dla zdarzeń, które wystąpiły w ciągu ostatniej godziny. Jednak oczekiwane są opóźnienia. Maksymalne dozwolone opóźnienie to 24 godziny, po upływie których zdarzenia użycia nie zostaną zaakceptowane.

Na przykład, jeśli zdarzenie użycia występuje o 1 PM dziennie, do 1 PM w następnym dniu będzie można wyemitować zdarzenie użycia powiązane z tym zdarzeniem. Gdy system emitujący użycie wygaśnie, odzyska, a następnie wyśle zdarzenie użycia dla interwału godzin, w którym wystąpiło użycie, bez utraty dokładności.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Co się stanie w przypadku wysłania więcej niż jednego zdarzenia użycia w tej samej godzinie?

Tylko jedno zdarzenie użycia jest akceptowane dla interwału godzinowego. Interwał godzinowy zaczyna się od minuty 0 i zostaje zakończony przy minucie 59.  Jeśli jest emitowane więcej niż jedno zdarzenie użycia dla tego samego interwału godzinowego, wszystkie kolejne zdarzenia użycia są porzucane jako duplikaty.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Co się stanie, gdy wyemitujesz użycie dla subskrypcji SaaS, która została już anulowana.

Każde zdarzenie użycia emitowane na platformie Marketplace nie zostanie zaakceptowane po usunięciu subskrypcji usługi SaaS.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Czy możesz uzyskać listę wszystkich subskrypcji SaaS, w tym aktywnych i niesubskrybowanych subskrypcji?

Tak, gdy wywołasz `GET /saas/subscriptions` interfejs API, zawiera on listę wszystkich subskrypcji SaaS. Pole Stan w odpowiedzi dla każdej subskrypcji SaaS przechwytuje, czy subskrypcja jest aktywna, czy nie została anulowana. Wywołanie listy subskrypcji zwraca maksymalnie 100 subskrypcje.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Co się stanie w przypadku awarii usługi pomiaru Marketplace?

Jeśli dostawca niezależnego dostawcy oprogramowania wyśle niestandardowy licznik i odbierze błąd, powinien czekać, a następnie ponowić próbę.

Jeśli błąd będzie się powtarzać, prześlij ponownie ten licznik niestandardowy do następnej godziny (sumując ilość). Kontynuuj ten proces do momentu otrzymania odpowiedzi bez błędu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [interfejsy API usługi pomiaru Marketplace](./marketplace-metering-service-apis.md).
