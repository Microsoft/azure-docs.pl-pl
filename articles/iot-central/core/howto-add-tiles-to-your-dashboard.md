---
title: Konfigurowanie pod Azure IoT Central pulpitu nawigacyjnego | Microsoft Docs
description: Jako konstruktor dowiedz się, jak skonfigurować domyślny pulpit nawigacyjny aplikacji Azure IoT Central kafelkami.
author: philmea
ms.author: philmea
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 8a8ba765a966409c06dbba636932f7777624f6d4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864257"
---
# <a name="configure-the-application-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego aplikacji

Pulpit **nawigacyjny** jest pierwszą stroną, która jest widzina po nawiązania połączenia z IoT Central aplikacji. Jeśli tworzysz aplikację na podstawie jednego z branżowych szablonów [aplikacji,](./concepts-app-templates.md)aplikacja ma wstępnie zdefiniowany pulpit nawigacyjny do uruchomienia. Jeśli tworzysz aplikację na podstawie niestandardowego szablonu [aplikacji](./concepts-app-templates.md), na pulpicie nawigacyjnym przedstawiono kilka wskazówek, które pomogą Ci rozpocząć pracę.

> [!TIP]
> Oprócz [domyślnego pulpitu nawigacyjnego aplikacji](howto-create-personal-dashboards.md) użytkownicy mogą tworzyć wiele pulpitów nawigacyjnych. Te pulpity nawigacyjne mogą być osobiste tylko dla użytkownika lub udostępniane wszystkim użytkownikom aplikacji.  

## <a name="add-tiles"></a>Dodaj kafelki

Poniższy zrzut ekranu przedstawia pulpit nawigacyjny w aplikacji utworzonej na podstawie szablonu **aplikacji niestandardowej.** Aby dostosować bieżący pulpit nawigacyjny, wybierz **pozycję Edytuj**, aby dodać niestandardowy osobisty lub udostępniony pulpit nawigacyjny, wybierz pozycję **Nowy:**

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Pulpit nawigacyjny dla aplikacji oparty na niestandardowym szablonie aplikacji":::

Po wybraniu opcji **Edytuj** lub **Nowy** pulpit nawigacyjny jest w *trybie edycji.* Możesz użyć narzędzi w  panelu Edytowanie pulpitu nawigacyjnego, aby dodać kafelki do pulpitu nawigacyjnego oraz dostosować i usunąć kafelki na samym pulpicie nawigacyjnym. Aby na przykład dodać kafelek **Telemetria** w celu pokazania bieżącej temperatury zgłoszonej przez co najmniej jedno urządzenie:

1. Wybierz **grupę urządzeń,** a następnie wybierz urządzenia z listy rozwijanej **Urządzenia** do pokazania na kafelku. Zobaczysz teraz dostępne dane telemetryczne, właściwości i polecenia z urządzeń.

1. W razie potrzeby użyj listy rozwijanej, aby wybrać wartość telemetrii do pokazania na kafelku. Możesz dodać więcej elementów do kafelka innego, wybierając pozycję + Telemetria, **+ Właściwość** lub **+ Właściwość chmury**. 

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Dodawanie kafelka telemetrii temperatury do pulpitu nawigacyjnego":::

Po wybraniu wszystkich wartości do pokazania na kafelku kliknij przycisk **Dodaj kafelek.** Kafelek zostanie wyświetlony na pulpicie nawigacyjnym, gdzie można zmienić wizualizację, zmienić jej rozmiar, przenieść ją i skonfigurować.

Po zakończeniu dodawania i dostosowywania kafelków  na pulpicie nawigacyjnym wybierz pozycję Zapisz, aby zapisać zmiany na pulpicie nawigacyjnym, co przenosi Cię poza tryb edycji.

## <a name="customize-tiles"></a>Dostosowywanie kafelków

Aby edytować kafelek, musisz być w trybie edycji.  Dostępne opcje dostosowywania zależą od typu [kafelka](#tile-types):

* Ikona linijki na kafelku umożliwia zmianę wizualizacji. Wizualizacje obejmują wykresy liniowe, wykresy słupkowe, wykresy kołowe, ostatnie znane wartości, kluczowe wskaźniki wydajności (lub kluczowe wskaźniki wydajności), mapy cieplne i mapy.

* Ikona kwadratowa umożliwia zmianę rozmiaru kafelka.

* Ikona koła zębatego umożliwia skonfigurowanie wizualizacji. Na przykład w przypadku wizualizacji wykresu liniowego można wybrać wyświetlanie legendy i osi, a następnie wybrać zakres czasu do wykreślenia.


## <a name="tile-types"></a>Typy kafelków

W poniższej tabeli opisano różne typy kafelków, które można dodać do pulpitu nawigacyjnego:

| Kafelek             | Opis |
| ---------------- | ----------- |
| Znaczniki języka Markdown         | Kafelki Markdown to kafelki z klikaniem, które wyświetlają tekst nagłówka i opisu sformatowany przy użyciu znaczników markdown. Adres URL może być względnym linkiem do innej strony w aplikacji lub bezwzględnym linkiem do witryny zewnętrznej.|
| Obraz            | Kafelki obrazów wyświetlają obraz niestandardowy i można go klikać. Adres URL może być względnym linkiem do innej strony w aplikacji lub bezwzględnym linkiem do witryny zewnętrznej.|
| Etykieta            | Kafelki etykiet zawierają niestandardowy tekst na pulpicie nawigacyjnym. Możesz wybrać rozmiar tekstu. Użyj kafelka etykiety, aby dodać do pulpitu nawigacyjnego odpowiednie informacje, takie jak opisy, szczegóły kontaktu lub pomoc.|
| Liczba            | Kafelki z liczbą wyświetlają liczbę urządzeń w grupie urządzeń.|
| Mapa              | Kafelki mapy wyświetlają lokalizację co najmniej jednego urządzenia na mapie. Można również wyświetlić maksymalnie 100 punktów historii lokalizacji urządzenia. Możesz na przykład wyświetlić próbkowane trasy, gdzie urządzenie było w ciągu ostatniego tygodnia.|
| KPI              |  Kafelki wskaźników KPI wyświetlają zagregowane wartości telemetryczne dla co najmniej jednego urządzenia w pewnym okresie. Można go na przykład użyć do pokazania maksymalnej temperatury i ciśnienia osiągniętego dla co najmniej jednego urządzenia w ciągu ostatniej godziny.|
| Wykres liniowy       | Kafelki wykresu liniowego kreślą co najmniej jedną zagregowaną wartość telemetrii dla co najmniej jednego urządzenia w okresie. Można na przykład wyświetlić wykres liniowy, aby wykreślić średnią temperaturę i ciśnienie jednego lub większej liczby urządzeń z ostatniej godziny.|
| Wykres słupkowy        | Kafelki wykresu słupkowego wykreślą co najmniej jedną zagregowaną wartość telemetrii dla co najmniej jednego urządzenia w okresie. Można na przykład wyświetlić wykres słupkowy, aby pokazać średnią temperaturę i ciśnienie jednego lub większej liczby urządzeń w ciągu ostatniej godziny.|
| Wykres kołowy        | Kafelki wykresu kołowego wyświetlają co najmniej jedną zagregowaną wartość telemetrii dla co najmniej jednego urządzenia w okresie.|
| Mapa cieplna         | Kafelki mapy cieplnej zawierają informacje o co najmniej jednym urządzeniu reprezentowanym jako kolory.|
| Ostatnia znana wartość | Kafelki z ostatnią znaną wartością wyświetlają najnowsze wartości telemetrii dla co najmniej jednego urządzenia. Na przykład możesz użyć tego kafelka, aby wyświetlić najnowsze wartości temperatury, ciśnienia i wilgotności dla co najmniej jednego urządzenia. |
| Historia zdarzeń    | Kafelki Historia zdarzeń wyświetlają zdarzenia dla urządzenia w okresie. Można go na przykład użyć do pokazania wszystkich zdarzeń otwierania i zamykania dla jednego lub większej liczby urządzeń w ciągu ostatniej godziny.|
| Właściwość         |  Kafelki właściwości wyświetlają bieżącą wartość właściwości i właściwości chmury jednego lub większej liczby urządzeń. Na przykład możesz użyć tego kafelka, aby wyświetlić właściwości urządzenia, takie jak producent lub wersja oprogramowania układowego dla urządzenia. |

Obecnie do kafelków, które obsługują wiele urządzeń, można dodać maksymalnie 10 urządzeń.

### <a name="customizing-visualizations"></a>Dostosowywanie wizualizacji

Domyślnie wykresy liniowe pokazują dane w przedziale czasu. Wybrany zakres czasu jest podzielony na 50 zasobników o równym rozmiarze, a dane urządzenia są następnie agregowane na zasobnik, aby zapewnić 50 punktów danych w wybranym zakresie czasu. Jeśli chcesz wyświetlić dane pierwotne, możesz zmienić wybór, aby wyświetlić 100 ostatnich wartości. Aby zmienić zakres czasu lub wybrać wizualizację danych pierwotnych, użyj listy rozwijanej Zakres wyświetlania w **panelu Konfigurowanie wykresu.**

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="Zmienianie zakresu wyświetlania wykresu liniowego":::

W przypadku kafelków, na których są wyświetlane wartości zagregowane, wybierz ikonę koła zębatego obok typu telemetrii w **panelu** Konfigurowanie wykresu, aby wybrać agregację. Możesz wybrać średnią, sumę, maksimum, minimum i liczbę.

W przypadku wykresów liniowych, słupkowych i kołowych można dostosować kolor różnych wartości telemetrii. Wybierz ikonę palety obok telemetrii, którą chcesz dostosować:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Zmienianie koloru wartości telemetrii":::

W przypadku kafelków, które pokazują właściwości ciągu lub wartości telemetrii, możesz wybrać sposób wyświetlania tekstu. Jeśli na przykład urządzenie przechowuje adres URL we właściwości ciągu, można go wyświetlić jako link do kliknięcia. Jeśli adres URL odwołuje się do obrazu, można renderować obraz na ostatnim kafelku znanej wartości lub właściwości. Aby zmienić sposób, w jaki ciąg jest wyświetlany, w konfiguracji kafelka wybierz ikonę koła zębatego obok właściwości lub typu telemetrii:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Zmienianie sposobu, w jaki ciąg jest wyświetlany na kafelku":::

W przypadku **kafelków** **numerycznego**  kluczowego wskaźnika wydajności, ostatniej znanej wartości i właściwości można użyć formatowania warunkowego, aby dostosować kolor kafelka na podstawie jego bieżącej wartości. Aby dodać formatowanie warunkowe, wybierz **pozycję Konfiguruj** na kafelku, a następnie wybierz ikonę **Formatowanie** warunkowe obok wartości do dostosowania:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Zrzut ekranu przedstawiający sposób znalezienia opcji konfiguracji dla kafelka, a następnie ikony formatowania warunkowego":::

Dodaj reguły formatowania warunkowego:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Zrzut ekranu przedstawiający reguły formatowania warunkowego dla średniego przepływu. Istnieją trzy reguły — mniej niż 20 ma kolor zielony, mniej niż 50 jest żółty, a wszystkie powyżej 50 jest czerwone":::
   
Poniższy zrzut ekranu przedstawia efekt reguły formatowania warunkowego:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Zrzut ekranu przedstawiający czerwony kolor tła na kafelku Przepływ średniej wody. Liczba na kafelku to 50,54":::

### <a name="tile-formatting"></a>Formatowanie "kafelka"
Ta funkcja, dostępna w kafelkach KPI, LKV i Property, umożliwia użytkownikom dostosowywanie rozmiaru czcionki, wybieranie dokładności dziesiętnej, skracanie wartości liczbowych (na przykład format 1700 jako 1,7 tys.) lub zawijanie wartości ciągu w kafelkach.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="Format kafelka":::

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować domyślny pulpit nawigacyjny Azure IoT Central aplikacji, możesz dowiedzieć się, [jak utworzyć osobisty pulpit nawigacyjny.](howto-create-personal-dashboards.md)
