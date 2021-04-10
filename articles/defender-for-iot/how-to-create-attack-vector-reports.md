---
title: Tworzenie raportów wektorów ataków
description: Raporty wektorów ataków zapewniają graficzną reprezentację łańcucha luk w zabezpieczeniach, które są możliwe do wykorzystania.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 6c178f379561ca4a572a7ae8f556ae6753d6e2be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784377"
---
# <a name="attack-vector-reporting"></a>Raportowanie wektorów ataków

## <a name="about-attack-vector-reports"></a>Raporty wektorów ataków — informacje

Raporty wektorów ataków zapewniają graficzną reprezentację łańcucha luk w zabezpieczeniach, które są możliwe do wykorzystania. Te luki w zabezpieczeniach mogą dać atakującemu dostęp do kluczowych urządzeń sieciowych. Symulator wektora ataku oblicza wektory ataków w czasie rzeczywistym i analizuje wszystkie wektory ataków dla określonego celu.

Współdziałanie z wektorem ataków pozwala oszacować efekt działań zaradczych w sekwencji ataków. Następnie można określić, na przykład, Jeśli uaktualnienie systemu zakłóca ścieżkę osoby atakującej przez przerwanie łańcucha ataków lub alternatywną ścieżkę ataku. Te informacje ułatwiają określanie priorytetów działań naprawczych i zaradczych.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Wyświetl alerty w centrum sterowania.":::

> [!NOTE]
> Administratorzy i analityki zabezpieczeń mogą wykonać procedury opisane w tej sekcji.

## <a name="create-an-attack-vector-report"></a>Tworzenie raportu wektora ataków

Aby utworzyć symulację wektora ataku:

1. Wybierz pozycję :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="znak plus":::, aby dodać symulację.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="Symulacja wektora ataku.":::

2. Wprowadź właściwości symulacji:

   - **Name**: Nazwa symulacji.

   - **Maksymalne wektory**: Maksymalna liczba wektorów w pojedynczej symulacji.

   - **Pokaż na mapie urządzeń**: Pokaż wektor ataku jako filtr na mapie urządzeń.

   - **Wszystkie urządzenia źródłowe**: wektor ataków będzie traktować wszystkie urządzenia jako źródło ataku.

   - **Źródło ataku**: w przypadku ataku na ataki należy wziąć pod uwagę tylko określone urządzenia.

   - **Wszystkie urządzenia docelowe**: wektor ataków uwzględnia wszystkie urządzenia jako cel ataku.

   - **Obiekt docelowy ataku**: atakujący może uwzględnić tylko określone urządzenia jako cel ataku.

   - **Wyklucz urządzenia**: określone urządzenia zostaną wykluczone z symulacji wektora ataku.

   - **Wyklucz podsieci**: określone podsieci zostaną wykluczone z symulacji wektora ataku.

3. Wybierz pozycję **Dodaj symulację**. Symulacja zostanie dodana do listy symulacje.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Dodaj nową symulację.":::

4. Wybierz, :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: Jeśli chcesz edytować symulację.

   Wybierz, :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: czy chcesz usunąć symulację.

   Zaznacz, :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: Jeśli chcesz oznaczyć symulację jako ulubioną.

5. Zostanie wyświetlona lista wektorów ataków z uwzględnieniem wyniku wektora (z 100), ataku urządzenia źródłowego i ataku na urządzenie docelowe. Wybierz konkretny atak na potrzeby graficznego przedstawiania wektorów ataków.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Wektory ataków.":::

## <a name="see-also"></a>Zobacz też

[Raportowanie wektorów ataków](how-to-create-attack-vector-reports.md)


