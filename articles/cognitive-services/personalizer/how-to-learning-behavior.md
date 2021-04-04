---
title: Skonfiguruj zachowanie uczenia
description: Tryb współdziałania zapewnia zaufanie do usługi personalizowania i jej możliwości uczenia maszynowego, a także udostępnia metryki, z którymi usługa jest wysyłana — bez ryzyka związanego z ruchem online.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 57a03b107678f83200b11f408784f6455cbceffd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94579295"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Skonfiguruj zachowanie uczenia personalizacji

[Tryb](concept-apprentice-mode.md) działania zapewnia zaufanie i zaufanie do usługi personalizacji oraz jej możliwości uczenia maszynowego i zapewnia gwarancję, że usługa jest wysyłana do informacji, które mogą być uzyskane z programu — bez ryzyka związanego z ruchem online.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>Konfiguruj tryb praktykanta

1. Zaloguj się do [Azure Portal](https://portal.azure.com), aby uzyskać zasób personalizacji.

1. Na stronie **Konfiguracja** na karcie **zachowanie uczenia** wybierz pozycję **Zwróć akcję bazową,** a następnie wybierz pozycję **Zapisz**.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający Konfigurowanie zachowania uczenia w trybie nauczania w Azure Portal](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>Zmiany w istniejącej aplikacji

Istniejąca aplikacja nie powinna zmienić sposobu, w jaki aktualnie wybiera akcje do wyświetlenia lub jak aplikacja określa wartość **, odnoszącą się do** tej akcji. Jedyną zmianą w aplikacji może być kolejność akcji wysyłanych do interfejsu API rangi personalizowania. Akcja wyświetlana aktualnie przez aplikację jest wysyłana jako _Pierwsza akcja_ na liście akcji. [Interfejs API rangi](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) używa tej pierwszej akcji do uczenia modelu personalizowania.

### <a name="configure-your-application-to-call-the-rank-api"></a>Skonfiguruj aplikację do wywoływania interfejsu API rangi

Aby dodać personalizację do aplikacji, należy wywołać interfejsy API rangi i nagrody.

1. Dodaj wywołanie [interfejsu API rangi](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) po punkcie w istniejącej logice aplikacji, gdzie można określić listę akcji i ich funkcji. Pierwsza akcja na liście akcje musi być akcją wybraną przez istniejącą logikę.

1. Skonfiguruj kod, aby wyświetlić akcję skojarzoną z płatnym **identyfikatorem akcji** odpowiedzi interfejsu API rangi.

### <a name="configure-your-application-to-call-reward-api"></a>Skonfiguruj aplikację do wywoływania interfejsu API nagradzania

1. Użyj istniejącej logiki biznesowej, aby obliczyć **wynagrodzenie** wyświetlonej akcji. Wartość musi należeć do zakresu od 0 do 1. Wyślij to wynagrodzenie do narzędzia Personalizacja przy użyciu [interfejsu API nagradzania](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward). Wartość nagrody nie jest oczekiwana natychmiast i można ją opóźniać w okresie czasu — w zależności od logiki biznesowej.

1. Jeśli nie zwrócisz nagrody w skonfigurowanym **czasie oczekiwania na poczekanie**, zamiast tego zostanie użyte domyślne wynagrodzenie.

## <a name="evaluate-apprentice-mode"></a>Oceń tryb praktykanta

W Azure Portal na stronie **oceny** zasobu personalizacji Przejrzyj **bieżącą wydajność uczenia** się.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający ocenę zachowania uczenia się w trybie nauczania w Azure Portal](media/settings/evaluate-apprentice-mode.png)

Tryb praktykanta zapewnia następujące **metryki oceny**:
* **Linia bazowa — średnia wynagrodzenie**: średnie nagrody wartości domyślnej (linii bazowej) aplikacji.
* **Personalizacja — średnia Nagroda**: Średnia z całkowitego nagrody personalizacji byłaby prawdopodobnie osiągnięta.
* **Stosunek liczby osiągnięć do najnowszych zdarzeń 1000**: stosunek linii bazowej i personalizacji, która została znormalizowana, w porównaniu z najnowszymi zdarzeniami 1000.

## <a name="switch-behavior-to-online-mode"></a>Przełącz zachowanie do trybu online

Po określeniu personalizacji jest szkolony z przeciętną 75-85% średniej kroczącej, model jest gotowy do przełączania do trybu online.

W Azure Portal dla zasobu personalizacji na stronie **Konfiguracja** na karcie **zachowanie uczenia** wybierz pozycję **Zwróć najlepszą akcję** , a następnie wybierz pozycję **Zapisz**.

Nie musisz wprowadzać żadnych zmian w wywołaniach interfejsu API rangi i nagrody.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie ustawieniami uczenia i modelu](how-to-manage-model.md)
