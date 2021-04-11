---
title: Jak używać tagów tabeli do uczenia niestandardowego modelu formularzy — aparat rozpoznawania
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak efektywnie korzystać z etykietowania tagu tabeli nadzorowanej.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5422520c6a863876091d7820a5c07fa2413346c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467862"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Używanie tagów tabeli do uczenia niestandardowego modelu formularzy

W tym artykule dowiesz się, jak szkolić niestandardowy model formularzy przy użyciu tagów tabeli (etykiet). Niektóre scenariusze wymagają bardziej złożonego etykietowania niż po prostu wyrównywanie par klucz-wartość. Takie scenariusze obejmują wyodrębnianie informacji z formularzy zawierających złożone struktury hierarchiczne lub występujące elementy, które nie są automatycznie wykrywane i wyodrębniane przez usługę. W takich przypadkach można użyć tagów tabeli do uczenia niestandardowego modelu formularza.

## <a name="when-should-i-use-table-tags"></a>Kiedy należy używać tagów tabeli?

Poniżej przedstawiono kilka przykładów użycia tagów tabeli, które byłyby odpowiednie:

- Istnieją dane, które mają zostać wyodrębnione jako tabele w formularzach, a struktura tabel jest istotna. Na przykład każdy wiersz tabeli reprezentuje jeden element, a każda kolumna w wierszu reprezentuje konkretną funkcję tego elementu. W takim przypadku można użyć znacznika tabeli, gdzie kolumna reprezentuje funkcje, a wiersz reprezentuje informacje o każdej funkcji.
- Dane, które mają zostać wyodrębnione nie są prezentowane w określonych polach formularza, ale semantycznie, dane mogą pasować do dwuwymiarowej siatki. Na przykład formularz zawiera listę osób i zawiera, imię i nazwisko, nazwisko oraz adres e-mail. Chcesz wyodrębnić te informacje. W takim przypadku można użyć znacznika tabeli z imię, nazwisko i adres e-mail jako kolumnami, a każdy wiersz jest wypełniany informacjami o osobie z listy.

> [!NOTE]
> Aparat rozpoznawania formularzy automatycznie odnajduje i wyodrębnia wszystkie tabele w dokumentach, niezależnie od tego, czy tabele są otagowane. W związku z tym nie musisz oznaczać każdej tabeli z formularza przy użyciu znacznika tabeli, a Tagi tabeli nie muszą replikować struktury bardzo tabeli w formularzu. Tabele wyodrębnione automatycznie przez aparat rozpoznawania formularzy zostaną uwzględnione w sekcji pageResults w danych wyjściowych JSON.

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>Tworzenie tagu tabeli przy użyciu narzędzia Test OCR (FOTT)
<!-- markdownlint-disable MD004 -->
* Ustal, czy ma być znacznik tabeli **dynamiczny** czy o **stałym rozmiarze** . Jeśli liczba wierszy różni się od dokumentu do dokumentu, Użyj dynamicznego znacznika tabeli. Jeśli liczba wierszy jest spójna w dokumentach, Użyj znacznika tabeli o ustalonym rozmiarze.
* Jeśli tag tabeli jest dynamiczny, zdefiniuj nazwy kolumn oraz typ danych i format dla każdej kolumny.
* Jeśli tabela ma stały rozmiar, zdefiniuj nazwę kolumny, nazwę wiersza, typ danych i format dla każdego tagu.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Konfigurowanie tagu tabeli":::

## <a name="label-your-table-tag-data"></a>Etykieta danych tagów tabeli

* Jeśli projekt ma tag tabeli, możesz otworzyć panel etykietowania i wypełnić tag tak jak pola klucz-wartość.
:::image type="content" source="media/table-labeling.png" alt-text="Etykieta z tagami tabeli":::

## <a name="next-steps"></a>Następne kroki

Skorzystaj z naszego przewodnika Szybki Start, aby nauczyć się i korzystać z niestandardowego modelu aparatu rozpoznawania formularzy:

> [!div class="nextstepaction"]
> [Uczenie z etykietami przy użyciu narzędzia do etykietowania przykładowego](quickstarts/label-tool.md)

## <a name="see-also"></a>Zobacz też

* [Co to jest rozpoznawanie formularzy?](overview.md)
>
