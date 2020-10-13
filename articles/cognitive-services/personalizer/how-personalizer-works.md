---
title: Jak działa program Personalizuj-Personalizacja
description: _Pętla_ personalizacji używa uczenia maszynowego do kompilowania modelu, który przewiduje najwyższą akcję dla zawartości. Model jest szkolony wyłącznie na danych, które zostały do niego wysłane, z wywołaniami rangi i nagrody.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: cfbe5cf8c19bfafb38f6149391e09350785ebf9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303611"
---
# <a name="how-personalizer-works"></a>Jak działa usługa Personalizacja

Zasób personalizacji, _Pętla szkoleniowa_, korzysta z uczenia maszynowego do kompilowania modelu, który przewiduje najwyższą akcję dla zawartości. Model jest szkolony wyłącznie na danych, które zostały do niego wysłane, z wywołaniami **rangi** i **nagrody** . Każda pętla jest całkowicie niezależna od siebie.

## <a name="rank-and-reward-apis-impact-the-model"></a>Interfejsy API rangi i nagrody wpływają na model

Do interfejsu API rangi są wysyłane _akcje z funkcjami_ i _funkcjami kontekstu_ . Interfejs API **rangi** decyduje się użyć:

* _Wykorzystanie_: bieżący model, aby określić najlepszą akcję na podstawie przeszłych danych.
* _Eksploruj_: Wybierz inną akcję, a nie górną akcję. [Ta wartość procentowa jest konfigurowana](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) dla zasobu personalizacji w Azure Portal.

Możesz określić wynik nagrody i wysłać ten wynik do interfejsu API nagradzania. Interfejs API **nagradzania** :

* Zbiera dane do uczenia modelu przez zarejestrowanie funkcji i nagrody każdego wywołania rangi.
* Używa tych danych do aktualizowania modelu w oparciu o konfigurację określoną w _zasadach nauki_.

## <a name="your-system-calling-personalizer"></a>System wywołujący spersonalizowany

Na poniższej ilustracji przedstawiono przepływ architektury wywołujący wywołania rangi i nagrody:

![tekst alternatywny](./media/how-personalizer-works/personalization-how-it-works.png "Jak działa Personalizacja")

1. Do interfejsu API rangi są wysyłane _akcje z funkcjami_ i _funkcjami kontekstu_ .

    * Personalizowanie decyduje o tym, czy wykorzystać bieżący model, czy eksplorować nowe opcje modelu.
    * Wynik klasyfikacji jest wysyłany do centrum EventHub.
1. Górna ranga jest zwracana do systemu jako _nagradzany Identyfikator akcji_.
    System prezentuje zawartość i ustala wynik nagrody na podstawie własnych reguł firmy.
1. System zwraca wynik nagrody do pętli szkoleniowej.
    * Gdy Personalizowanie otrzymuje wynagrodzenie, wynagrodzenie jest wysyłane do centrum EventHub.
    * Ranga i Nagroda są skorelowane.
    * Model AI jest aktualizowany w oparciu o wyniki korelacji.
    * Aparat wnioskowania jest aktualizowany nowym modelem.

## <a name="personalizer-retrains-your-model"></a>Personalizacja ponownie szkoli model

Personalizacja ponownie szkoli model na podstawie ustawienia **aktualizacji częstotliwości modelu** w zasobie personalizacji w Azure Portal.

Program personalizujer używa wszystkich aktualnie zachowanych danych na podstawie ustawienia **przechowywania danych** w polu Liczba dni w zasobie personalizacji w Azure Portal.

## <a name="research-behind-personalizer"></a>Zbadaj za personalizacją

Personalizacja jest oparta na wykorzystaniu nauki i badań w dziedzinie [nauki wzmacniania](concepts-reinforcement-learning.md) , w tym na dokumentach, w badaniach naukowych i w ciągłych obszarach eksploracji firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [najpopularniejszych scenariuszy](where-can-you-use-personalizer.md) dla programu personalizacji