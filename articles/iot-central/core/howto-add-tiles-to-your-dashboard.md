---
title: Dodawanie kafelków do pulpitu nawigacyjnego usługi Azure IoT Central | Microsoft Docs
description: Jako Konstruktor można dowiedzieć się, jak skonfigurować domyślny pulpit nawigacyjny aplikacji platformy Azure IoT Central przy użyciu kafelków.
author: Haley-Rowland
ms.author: harowl
ms.date: 05/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: beeb771ea5053dd0ad867a7568aa64bbb2d0b4ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985323"
---
# <a name="configure-the-application-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego aplikacji

**Pulpit nawigacyjny** jest pierwszą stroną wyświetlaną podczas nawiązywania połączenia z aplikacją IoT Central. Jeśli tworzysz aplikację z jednego z [szablonów aplikacji](./concepts-app-templates.md)ukierunkowanych na branżę, aplikacja ma wstępnie zdefiniowany pulpit nawigacyjny do uruchomienia. Jeśli tworzysz aplikację na podstawie niestandardowego [szablonu aplikacji](./concepts-app-templates.md), na pulpicie nawigacyjnym zostaną wyświetlone wskazówki umożliwiające rozpoczęcie pracy.

> [!TIP]
> Oprócz domyślnego pulpitu nawigacyjnego aplikacji użytkownicy mogą [tworzyć wiele pulpitów nawigacyjnych](howto-create-personal-dashboards.md) . Te pulpity nawigacyjne mogą być osobiste tylko dla użytkownika lub udostępniane wszystkim użytkownikom aplikacji.  

## <a name="add-tiles"></a>Dodaj kafelki

Poniższy zrzut ekranu przedstawia pulpit nawigacyjny w aplikacji utworzonej na podstawie **niestandardowego szablonu aplikacji** . Aby dostosować bieżący pulpit nawigacyjny, wybierz pozycję **Edytuj**, aby dodać niestandardowy osobisty lub udostępniony pulpit nawigacyjny, wybierz pozycję **Nowy**:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Pulpit nawigacyjny dla aplikacji opartych na szablonie aplikacji niestandardowej":::

Po wybraniu opcji **Edytuj** lub **Nowy**pulpit nawigacyjny jest w trybie *edycji* . Za pomocą narzędzi dostępnych w panelu **Edytowanie pulpitu nawigacyjnego** można dodawać kafelki do pulpitu nawigacyjnego oraz dostosowywać i usuwać kafelki na pulpicie nawigacyjnym. Na przykład, aby dodać kafelek **telemetrii** w celu wyświetlenia bieżącej temperatury zgłoszonej przez co najmniej jedno urządzenie:

1. Na panelu **Edytowanie pulpitu nawigacyjnego** wybierz **grupę urządzeń**.
1. Wybierz co najmniej jedno urządzenie na liście rozwijanej **urządzenia** , które ma być wyświetlane na kafelku. Teraz widzisz dostępne dane telemetryczne, właściwości i polecenia z urządzeń.
1. Wybierz pozycję **temperatura** w sekcji Telemetria, a następnie wybierz pozycję **Dodaj kafelek**. Kafelek jest teraz widoczny na pulpicie nawigacyjnym, w którym można zmienić wizualizację, zmienić rozmiar kafelka i skonfigurować go:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Pulpit nawigacyjny dla aplikacji opartych na szablonie aplikacji niestandardowej":::

Po zakończeniu dodawania i dostosowywania kafelków na pulpicie nawigacyjnym wybierz pozycję **Zapisz**.

## <a name="customize-tiles"></a>Dostosuj kafelki

Aby można było dostosować kafelek na pulpicie nawigacyjnym, pulpit nawigacyjny musi być w trybie edycji. Dostępne opcje dostosowywania zależą od [typu kafelka](#tile-types):

* Ikona linijki na kafelku pozwala zmienić wizualizację. Wizualizacje obejmują wykresy liniowe, ostatnie znane wartości oraz mapy cieplne.

* Kwadratowa ikona pozwala zmienić rozmiar kafelka.

* Ikona koła zębatego umożliwia skonfigurowanie wizualizacji. Na przykład dla wizualizacji wykresu liniowego można wybrać opcję wyświetlania legendy i osi oraz wybrać zakres czasu do wykreślenia.

## <a name="tile-types"></a>Typy kafelków

W poniższej tabeli opisano różne typy kafelków, które można dodać do pulpitu nawigacyjnego:

| Kafelek             | Opis |
| ---------------- | ----------- |
| Znaczniki języka Markdown         | Kafelki promocji są kafelkami, które wyświetlają tekst nagłówka i opisu sformatowane przy użyciu promocji. Adres URL może być łączem względnym do innej strony w aplikacji lub bezwzględnym łączem do zewnętrznej witryny.|
| Obraz            | Kafelki obrazu wyświetlają obraz niestandardowy i można go kliknąć. Adres URL może być łączem względnym do innej strony w aplikacji lub bezwzględnym łączem do zewnętrznej witryny.|
| Etykieta            | Kafelki etykiet wyświetlają niestandardowy tekst na pulpicie nawigacyjnym. Możesz wybrać rozmiar tekstu. Użyj kafelka etykiety, aby dodać odpowiednie informacje do pulpitu nawigacyjnego, takie opisy, szczegóły kontaktu lub pomoc.|
| Liczba            | Liczba kafelków przedstawia liczbę urządzeń w grupie urządzeń.|
| Mapa              | Kafelki mapy przedstawiają lokalizację co najmniej jednego urządzenia na mapie. Można również wyświetlić do 100 punktów historii lokalizacji urządzenia. Można na przykład wyświetlić przykładową trasę, w której znajduje się urządzenie w ubiegłym tygodniu.|
| KPI              |  Kafelki KPI przedstawiają zagregowane wartości telemetryczne dla jednego lub większej liczby urządzeń w danym okresie. Na przykład można użyć jej do wyświetlenia maksymalnej temperatury i ciśnienia dla jednego lub większej liczby urządzeń w ciągu ostatniej godziny.|
| Wykres liniowy       | Kafelki wykresu liniowego przedstawiają co najmniej jedną zagregowaną wartość telemetrii dla jednego lub kilku urządzeń w danym okresie czasu. Na przykład możesz wyświetlić wykres liniowy, aby przedstawić średnią temperaturę i ciśnienie jednego lub większej liczby urządzeń w ciągu ostatniej godziny.|
| Wykres słupkowy        | Kafelki wykresu słupkowego przedstawiają co najmniej jedną zagregowaną wartość telemetrii dla jednego lub kilku urządzeń w danym okresie czasu. Na przykład można wyświetlić wykres słupkowy, aby pokazać średnią temperaturę i ciśnienie jednego lub większej liczby urządzeń w ciągu ostatniej godziny.|
| Wykres kołowy        | Kafelki wykresu kołowego przedstawiają co najmniej jedną zagregowaną wartość telemetrii dla jednego lub kilku urządzeń w danym okresie czasu.|
| Mapa cieplna         | Kafelki mapy cieplnej zawierają informacje o jednym lub kilku urządzeniach, reprezentowane jako kolory.|
| Ostatnia znana wartość | Na kafelkach Ostatnia znana wartość są wyświetlane najnowsze wartości telemetryczne dla jednego lub kilku urządzeń. Na przykład można użyć tego kafelka, aby wyświetlić najnowsze wartości temperatury, ciśnienia i wilgotności dla jednego lub większej liczby urządzeń. |
| Historia zdarzeń    | Kafelki historii zdarzeń wyświetlają zdarzenia dotyczące urządzenia w określonym czasie. Na przykład możesz użyć jej do wyświetlania wszystkich zdarzeń otwartych i zamykających dla jednego lub większej liczby urządzeń w ciągu ostatniej godziny.|
| Właściwość         |  Kafelki właściwości wyświetlają bieżącą wartość właściwości i właściwości chmury jednego lub większej liczby urządzeń. Na przykład można użyć tego kafelka, aby wyświetlić właściwości urządzenia, takie jak producent lub wersja oprogramowania układowego urządzenia. |

Obecnie można dodać do 10 urządzeń do kafelków, które obsługują wiele urządzeń.

### <a name="customizing-visualizations"></a>Dostosowywanie wizualizacji

W przypadku kafelków, które wyświetlają wartości zagregowane, wybierz ikonę koła zębatego obok pozycji Typ telemetrii w panelu **Konfiguruj wykres** , aby wybrać agregację. Można wybrać jedną z średnich, sum, maksimum, minimum i Count.

W przypadku wykresów liniowych, wykresów słupkowych i wykresów kołowych można dostosować kolor różnych wartości telemetrii. Wybierz ikonę palety obok danych telemetrycznych, które chcesz dostosować:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Pulpit nawigacyjny dla aplikacji opartych na szablonie aplikacji niestandardowej":::

W przypadku kafelków pokazujących właściwości ciągu lub wartości telemetryczne możesz wybrać sposób wyświetlania tekstu. Na przykład jeśli urządzenie przechowuje adres URL we właściwości ciągu, można go wyświetlić jako link do kliknięcia. Jeśli adres URL odwołuje się do obrazu, można renderować obraz w ostatniej znanej wartości lub kafelku właściwości. Aby zmienić sposób wyświetlania ciągu, w obszarze Konfiguracja kafelka wybierz ikonę koła zębatego obok pozycji Typ telemetrii lub właściwość:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Pulpit nawigacyjny dla aplikacji opartych na szablonie aplikacji niestandardowej":::

W przypadku liczbowych **wskaźników KPI**, **ostatniej znanej wartości**i kafelków **Właściwości** można użyć formatowania warunkowego, aby dostosować kolor kafelka na podstawie jego bieżącej wartości. Aby dodać formatowanie warunkowe, wybierz pozycję **Konfiguruj** na kafelku, a następnie wybierz ikonę **Formatowanie warunkowe** obok wartości, która ma zostać dostosowana:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Pulpit nawigacyjny dla aplikacji opartych na szablonie aplikacji niestandardowej":::

Dodaj reguły formatowania warunkowego:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Pulpit nawigacyjny dla aplikacji opartych na szablonie aplikacji niestandardowej":::

Poniższy zrzut ekranu przedstawia efekt reguły formatowania warunkowego:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Pulpit nawigacyjny dla aplikacji opartych na szablonie aplikacji niestandardowej":::

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować pulpit nawigacyjny domyślnego aplikacji platformy Azure IoT Central, możesz [dowiedzieć się, jak utworzyć osobisty pulpit nawigacyjny](howto-create-personal-dashboards.md).
