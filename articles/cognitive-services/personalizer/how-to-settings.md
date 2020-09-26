---
title: Konfigurowanie usługi Personalizacja
description: Konfiguracja usługi zawiera informacje o tym, jak usługa traktuje korzyści, jak często bada usługa, jak często jest on przełączany i jak dużo danych jest przechowywanych.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 9b7347cb98bcbf2e1d92f115d404197083acef4d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91292612"
---
# <a name="configure-personalizer-learning-loop"></a>Konfigurowanie pętli uczenia personalizacji

Konfiguracja usługi zawiera informacje o tym, jak usługa traktuje korzyści, jak często bada usługa, jak często jest on przełączany i jak dużo danych jest przechowywanych.

Skonfiguruj pętlę uczenia na stronie **Konfiguracja** w Azure Portal dla tego zasobu personalizowania.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>Planowanie zmian konfiguracji

Ponieważ niektóre zmiany konfiguracji [resetują model](#settings-that-include-resetting-the-model), należy zaplanować zmiany w konfiguracji.

Jeśli planujesz używanie [trybu zawodowego](concept-apprentice-mode.md), pamiętaj o przejrzeniu konfiguracji personalizacji przed przełączeniem do trybu praktyk.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Ustawienia, które obejmują Resetowanie modelu

Poniższe akcje wyzwalają ponowne szkolenie modelu przy użyciu danych dostępnych do ostatniego 2 dni.

* Nagradzaj
* Eksploracja

Aby [wyczyścić](how-to-manage-model.md) wszystkie dane, użyj strony **Ustawienia modelu i uczenia** .

## <a name="configure-rewards-for-the-feedback-loop"></a>Skonfiguruj nagrody dla pętli opinii

Skonfiguruj usługę do użycia w pętli szkoleniowej. Zmiany następujących wartości spowodują zresetowanie bieżącego modelu personalizowania i ponowne nauczenie go przy użyciu ostatnich 2 dni danych.

> [!div class="mx-imgBorder"]
> ![Skonfiguruj wartości nagrody dla pętli opinii](media/settings/configure-model-reward-settings.png)

|Wartość|Przeznaczenie|
|--|--|
|Nagradzany czas oczekiwania|Ustawia długość czasu, przez który program Personalizuj będzie zbierać wartości nagrody dla wywołania rangi, rozpoczynając od momentu wywołania rangi. Ta wartość jest ustawiana przez pytanie: "jak długo program Personalizuj powinien czekać na nadawanie nagrody?" Wszystkie nagrody przychodzące po tym oknie będą rejestrowane, ale nie są używane do uczenia się.|
|Wynagrodzenie domyślne|Jeśli Personalizacja nie otrzymuje żadnego nadawania w czasie oczekiwania w przedziale czasowym, skojarzonym z wywołaniem rangi, Personalizowanie przypisze domyślne wynagrodzenie. Domyślnie, a w większości scenariuszy domyślna Nagroda ma wartość zero (0).|
|Agregacja nagrody|Jeśli odebrane zostanie wiele nagrody dla tego samego wywołania interfejsu API rangi, używana jest ta metoda agregacji: **sum** lub **Najwcześniejsza**. Najwcześniej wybierany jest najwcześniejszy wynik otrzymany i odrzuca resztę. Jest to przydatne, jeśli chcesz uzyskać unikatowe wynagrodzenie między możliwymi duplikatami wywołań. |

Po zmianie tych wartości upewnij się, że wybrano pozycję **Zapisz**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Skonfiguruj eksplorację, aby umożliwić adaptację do pętli uczenia

Personalizacja jest w stanie wykrywać nowe wzorce i dostosowywać je do zmian w czasie przez Eksplorowanie alternatyw zamiast korzystania z prognozowania modelu przeszkolonego. Wartość **eksploracji** określa, jaki procent wywołań rangi jest odpowiedzią na eksplorację.

Zmiany tej wartości spowodują zresetowanie bieżącego modelu personalizowania i ponowne nauczenie go z ostatnich 2 dni.

![Wartość eksploracji określa, jaki procent wywołań rangi jest odpowiedzią na eksplorację](media/settings/configure-exploration-setting.png)

Po zmianie tej wartości upewnij się, że wybrano pozycję **Zapisz**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Skonfiguruj częstotliwość aktualizacji modelu dla szkolenia modelu

**Częstotliwość aktualizacji modelu** określa, jak często jest szkolony model.

|Ustawienie częstotliwości|Przeznaczenie|
|--|--|
|1 minuta|Częstotliwości aktualizacji o pojedynczej minucie są przydatne podczas **debugowania** kodu aplikacji przy użyciu narzędzia do personalizacji, wykonywania pokazów lub interaktywnego testowania aspektów uczenia maszynowego.|
|15 minut|Wysokie częstotliwości aktualizacji modelu są przydatne w sytuacjach, w których chcesz **ściśle śledzić zmiany** zachowań użytkownika. Przykłady obejmują witryny, które są uruchamiane na żywo wiadomości, zawartości wirusowej lub licytacji produktu na żywo. W tych scenariuszach można użyć częstotliwości 15 minut. |
|1 godzina|W większości przypadków użycia niższa częstotliwość aktualizacji jest skuteczna.|

![Częstotliwość aktualizacji modelu określa, jak często nowy model personalizowania jest przeszkolny.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Po zmianie tej wartości upewnij się, że wybrano pozycję **Zapisz**.

## <a name="data-retention"></a>Przechowywanie danych

**Okres przechowywania danych** określa, ile dni personalizacji ma przechowywać dzienniki danych. Dzienniki danych przeszłych są wymagane do przeprowadzenia [oceny w trybie offline](concepts-offline-evaluation.md), które służą do mierzenia skuteczności działania programu personalizowania i optymalizowania zasad uczenia.

Po zmianie tej wartości upewnij się, że wybrano pozycję **Zapisz**.



## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak zarządzać modelem](how-to-manage-model.md)
