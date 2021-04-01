---
title: Zdarzenia aktywne i nieaktywne — Personalizacja
description: W tym artykule omówiono użycie aktywnych i nieaktywnych zdarzeń w ramach usługi personalizacji.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 7d1044d02ceba1f3d0996b1fe1c8a9a44b31049b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91253653"
---
# <a name="active-and-inactive-events"></a>Zdarzenia aktywne i nieaktywne

**Aktywne** zdarzenie to dowolne wywołanie rangi, gdzie wiadomo, że użytkownik chce pokazać wynik dla klienta i określić wynik nagrody. Jest to zachowanie domyślne.

**Nieaktywne** zdarzenie jest wywołaniem do rangi, gdzie nie masz pewności, czy użytkownik zobaczy zalecaną akcję z powodu logiki biznesowej. Pozwala to na odrzucanie zdarzenia, co sprawia, że program Personalizuj nie będzie szkolony z domyślnym wynagrodzeniem. Nieaktywne zdarzenia nie powinny wywoływać interfejsu API nagradzania.

Należy pamiętać, że pętla szkoleniowa zna rzeczywisty typ zdarzenia. Nieaktywne zdarzenie nie będzie miało połączenia z wynagrodzeniem. Aktywne zdarzenie powinno mieć połączenie płatne, ale jeśli wywołanie interfejsu API nigdy nie zostanie wykonane, zostanie zastosowany domyślny wynik nagrody. Zmień stan zdarzenia z nieaktywne na aktywne, gdy tylko wiadomo, że będzie miało wpływ na środowisko użytkownika.

## <a name="typical-active-events-scenario"></a>Typowy scenariusz aktywnych zdarzeń

Gdy aplikacja wywołuje interfejs API rangi, otrzymujesz akcję, którą aplikacja powinna wyświetlić w polu **rewardActionId** .  Z tego momentu Personalizacja oczekuje, że nastąpi wywołanie z wynikiem nagrody, który ma ten sam eventId. Wynik nagrody służy do uczenia modelu dla przyszłych wywołań rangi. Jeśli dla eventId nie zostanie odebrane żadne wynagrodzenie, zostanie zastosowane wynagrodzenie domyślne. [Domyślne nagrody](how-to-settings.md#configure-rewards-for-the-feedback-loop) są ustawiane w zasobie personalizacji w Azure Portal.

## <a name="other-event-type-scenarios"></a>Inne scenariusze typów zdarzeń

W niektórych scenariuszach aplikacja może wymagać wywołania rangi, aby nie wie, czy wynik zostanie użyty lub wyświetlony użytkownikowi. Może się tak zdarzyć w sytuacjach, gdy na przykład renderowanie strony z podwyższoną zawartością zostanie zastąpione kampanią marketingową. Jeśli wynik wywołania rangi nigdy nie był używany i użytkownik go nigdy nie wywołał, nie wysyłaj odpowiedniego wywołania.

Zwykle te scenariusze są wykonywane, gdy:

* Korzystasz z interfejsu użytkownika, który może zostać wyświetlony przez użytkownika lub może się nie powieść.
* Aplikacja wykonuje personalizację predykcyjną, w której wywołania rangi są wykonywane z niewielkim kontekstem w czasie rzeczywistym, a aplikacja może nie używać danych wyjściowych.

W takich przypadkach należy użyć personalizacji do wywołania rangi, żądając zdarzenia jako _nieaktywnego_. Personalizacja nie będzie oczekiwać dla tego zdarzenia i nie będzie stosowała domyślnego wynagrodzenia.

W dalszej części logiki biznesowej, jeśli aplikacja używa informacji z wywołania rangi, po prostu _Aktywuj_ zdarzenie. Gdy tylko zdarzenie jest aktywne, Personalizacja oczekuje na odłożenie zdarzenia. Jeśli w interfejsie API nagradzania nie jest wykonywane żadne jawne wywołanie, Personalizacja stosuje wynagrodzenie domyślne.

## <a name="inactive-events"></a>Zdarzenia nieaktywne

Aby wyłączyć szkolenie dla zdarzenia, zadzwoń do rangi przy użyciu `learningEnabled = False` .

W przypadku nieaktywnego zdarzenia uczenie jest niejawnie aktywowane w przypadku wysłania nagrody dla eventId lub wywołania `activate` interfejsu API dla tego EventId.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak określić ocenę nagrody i dane, które należy wziąć pod uwagę](concept-rewards.md).
