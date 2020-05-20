---
title: Dodawanie kafelków do pulpitu nawigacyjnego | Microsoft Docs
description: Jako Konstruktor można dowiedzieć się, jak skonfigurować domyślny pulpit nawigacyjny aplikacji platformy Azure IoT Central.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1110f76a792a7e3955d5fd32e01ac1566d467151
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659098"
---
# <a name="configure-the-application-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego aplikacji

**Pulpit nawigacyjny** to strona, która ładuje się, gdy użytkownicy, którzy mają dostęp do aplikacji, przejdą do adresu URL aplikacji. Jeśli aplikacja została utworzona przy użyciu jednego z **szablonów aplikacji**, aplikacja będzie mieć wstępnie zdefiniowany pulpit nawigacyjny do uruchomienia. Jeśli aplikacja została utworzona przy użyciu szablonu aplikacji **niestandardowych** , na pulpicie nawigacyjnym zostaną wyświetlone wskazówki dotyczące rozpoczynania pracy.

> [!NOTE]
> Oprócz domyślnego pulpitu nawigacyjnego aplikacji użytkownicy mogą [tworzyć wiele pulpitów nawigacyjnych](howto-create-personal-dashboards.md) . Te pulpity nawigacyjne mogą być osobiste tylko dla użytkownika lub udostępniane wszystkim użytkownikom aplikacji.  

## <a name="add-tiles"></a>Dodaj kafelki

Poniższy zrzut ekranu przedstawia pulpit nawigacyjny w aplikacji utworzonej na podstawie **niestandardowego szablonu aplikacji** . Aby dostosować domyślny pulpit nawigacyjny dla aplikacji, wybierz pozycję **Edytuj** w lewym górnym rogu strony.

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny dla aplikacji na podstawie szablonu "Przykładowa contoso"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Wybranie pozycji **Edytuj** powoduje otwarcie panelu Biblioteka pulpitu nawigacyjnego. Biblioteka zawiera elementy pierwotne kafelków i pulpitów nawigacyjnych, których można użyć do dostosowania pulpitu nawigacyjnego.

> [!div class="mx-imgBorder"]
> ![Biblioteka pulpitu nawigacyjnego](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Na przykład można dodać kafelek **telemetrii** dla bieżącej temperatury urządzenia. W tym celu:

1. Wybierz **szablon urządzenia**
1. Wybierz urządzenie z **urządzeń** , które ma być wyświetlane na kafelku pulpitu nawigacyjnego. Zostanie wyświetlona lista właściwości urządzenia, które mogą być używane na kafelku.
1. Aby utworzyć kafelek na pulpicie nawigacyjnym, kliknij pozycję **temperatura** i przeciągnij ją do obszaru pulpit nawigacyjny. Możesz również kliknąć pole wyboru obok pozycji **temperatura** i kliknąć pozycję **Dodaj kafelek**. Poniższy zrzut ekranu przedstawia Wybieranie szablonu urządzenia i wystąpienia urządzenia, a następnie Tworzenie kafelka telemetrii na pulpicie nawigacyjnym.
1. Wybierz pozycję **Zapisz** w lewym górnym rogu, aby zapisać kafelek na pulpicie nawigacyjnym.

> [!div class="mx-imgBorder"]
> ![Formularz "Konfigurowanie szczegółów urządzenia" z informacjami szczegółowymi dotyczącymi ustawień i właściwości](media/howto-add-tiles-to-your-dashboard/device-details.png)

Teraz po wyświetleniu domyślnego pulpitu nawigacyjnego aplikacji przez operatora zostanie wyświetlony nowy kafelek z **temperaturą** urządzenia. Każdy kafelek ma wstępnie wybrany wykres, wykres itp., który będzie wyświetlany podczas tworzenia kafelka. Jednak użytkownicy mogą edytować i zmieniać tę wizualizację.  

> [!div class="mx-imgBorder"]
> ![Karta "pulpit nawigacyjny" z wyświetlonymi ustawieniami i właściwościami kafelka](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Edytuj kafelki

Aby edytować kafelek na pulpicie nawigacyjnym, najpierw kliknij pozycję **Edytuj** w lewym górnym rogu strony, co spowoduje otwarcie trybu edycji pulpitu nawigacyjnego i wszystkich jego kafelków.  

> [!div class="mx-imgBorder"]
> ![Ekran pulpitu nawigacyjnego z aktywnym trybem edycji dla wybranego kafelka](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Następnie kliknij ikonę **koła zębatego** w prawym górnym rogu kafelka, który chcesz edytować. Tutaj możesz edytować aspekty kafelka, w tym jego tytuł, wizualizację, agregację itp.

> [!div class="mx-imgBorder"]
> ![Lista rozwijana dla ustawień agregacji kafelka](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Wizualizację wykresu można także zmienić, klikając ikonę **linijki** na kafelku.

> [!div class="mx-imgBorder"]
> ![Lista rozwijana dla ustawień wizualizacji kafelka](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Typy kafelków

Poniższa tabela zawiera podsumowanie użycia kafelków w usłudze Azure IoT Central:

| Kafelek | Pulpit nawigacyjny | Opis
| ----------- | ------- | ------- |
| Zawartość | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki obsługiwane przez promocji są kafelkami, które wyświetlają tekst nagłówka i opisu. Możesz również użyć tego kafelka jako kafelka linku, aby umożliwić użytkownikowi nawigację do adresu URL związanego z Twoją aplikacją.|
| Obraz | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki obrazu wyświetlają obraz niestandardowy i można go kliknąć. Za pomocą kafelka obrazu Dodaj grafikę do pulpitu nawigacyjnego i opcjonalnie Zezwól użytkownikowi na przejście do adresu URL istotnego dla aplikacji.|
| Etykieta | Pulpity nawigacyjne aplikacji |Kafelki etykiet wyświetlają niestandardowy tekst na pulpicie nawigacyjnym. Możesz wybrać rozmiar tekstu. Użyj kafelka etykiety, aby dodać odpowiednie informacje do pulpitu nawigacyjnego, takie opisy, szczegóły kontaktu lub pomoc.|
| Mapa | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki mapy przedstawiają lokalizację urządzenia na mapie. Można również wyświetlić do 100 punktów historii lokalizacji urządzenia. Można na przykład wyświetlić przykładową trasę, w której znajduje się urządzenie w ubiegłym tygodniu.|
| Wykres liniowy | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki wykresu liniowego przedstawiają wykres zagregowanej miary dla urządzenia w danym okresie czasu. Na przykład można wyświetlić wykres liniowy, który pokazuje średnią temperaturę i ciśnienie urządzenia w ciągu ostatniej godziny.|
| Wykres słupkowy | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki wykresu słupkowego przedstawiają wykres zagregowanych pomiarów dla urządzenia w danym okresie czasu. Na przykład można wyświetlić wykres słupkowy, który pokazuje średnią temperaturę i ciśnienie urządzenia w ciągu ostatniej godziny.|
| Wykres kołowy | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki wykresu kołowego przedstawiają wykres zagregowanych pomiarów dla urządzenia w danym okresie czasu.|
| Mapa cieplna | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki mapy cieplnej zawierają informacje o urządzeniu reprezentowane jako kolory.|
| Historia zdarzeń | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki historii zdarzeń wyświetlają zdarzenia dotyczące urządzenia w określonym czasie. Na przykład można użyć go do wyświetlenia wszystkich zmian temperatury urządzenia w ciągu ostatniej godziny.|
| Historia stanu | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki historii stanu wyświetlają wartości pomiarów dla danego przedziału czasu. Na przykład można użyć go do wyświetlenia wartości temperatury dla urządzenia w ciągu ostatniej godziny.|
| KPI | Pulpity nawigacyjne aplikacji i urządzeń | Kafelki KPI przedstawiają zagregowane dane telemetryczne lub pomiary zdarzeń przez okres. Można na przykład użyć jej do wyświetlenia maksymalnej temperatury dla urządzenia w ciągu ostatniej godziny.|
| Ostatnia znana wartość | Pulpity nawigacyjne aplikacji i urządzeń |Na kafelkach Ostatnia znana wartość jest wyświetlana najnowsza wartość dla danych telemetrycznych lub stanu. Na przykład można użyć tego kafelka, aby wyświetlić najnowsze pomiary temperatury, ciśnienia i wilgotności dla urządzenia. |
| Właściwość | Pulpity nawigacyjne aplikacji i urządzeń | Kafelki właściwości wyświetlają bieżącą wartość właściwości i właściwości chmury urządzenia. Na przykład można użyć tego kafelka, aby wyświetlić właściwości urządzenia, takie jak wersja producenta lub oprogramowania układowego urządzenia. |

### <a name="customizing-visualizations"></a>Dostosowywanie wizualizacji

W przypadku wykresów liniowych, wykresów słupkowych i wykresów kołowych można dostosować kolory wyświetlane przez różne telemetrii na wykresie. W tym celu wybierz ikonę palate obok danych telemetrycznych, które chcesz dostosować, a następnie wybierz kolor.

> [!div class="mx-imgBorder"]
> ![Lista rozwijana dla ustawień wyświetlania koloru telemetrii](media/howto-add-tiles-to-your-dashboard/color-customization.png)

Dla telemetrii lub właściwości, które są typu String, można wybrać sposób wizualizacji tekstu. Jeśli na przykład urządzenie wyśle adres URL jako dane telemetryczne ciągu, można wizualizować ten adres URL jako link do kliknięcia. Jeśli adres URL odwołuje się do obrazu, można renderować obraz w ostatniej znanej wartości lub kafelku właściwości. Można zmienić sposób wyświetlania telemetrii ciągów, zaznaczając koła zębatego obok nazwy telemetrii. W ten sposób można wyświetlić tekst jako tekst, link lub obraz.

> [!div class="mx-imgBorder"]
> ![Lista rozwijana dla ustawień wizualizacji ciągów](media/howto-add-tiles-to-your-dashboard/string-viz-customization.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować pulpit nawigacyjny domyślnego aplikacji platformy Azure IoT Central, możesz [dowiedzieć się, jak utworzyć osobisty pulpit nawigacyjny](howto-create-personal-dashboards.md).
