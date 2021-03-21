---
title: Samouczek — Dostosowywanie pulpitu nawigacyjnego operatora na platformie Azure IoT Central
description: W tym samouczku pokazano, jak dostosować pulpit nawigacyjny operatora w aplikacji IoT Central i zarządzać urządzeniami.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 74ce36652b1a00ac6813c62a17d44e2a5486645f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831727"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Samouczek: Dostosowywanie pulpitu nawigacyjnego operatora i zarządzanie urządzeniami w usłudze Azure IoT Central


W tym samouczku, jako Konstruktor, dowiesz się, jak dostosować pulpit nawigacyjny operatora w aplikacji do analizy w Sklepie Azure IoT Central. Operatorzy aplikacji mogą korzystać z dostosowanego pulpitu nawigacyjnego, aby uruchamiać aplikację i zarządzać dołączonymi urządzeniami.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Zmień nazwę pulpitu nawigacyjnego
> * Dostosowywanie kafelków obrazów na pulpicie nawigacyjnym
> * Rozmieść kafelki w celu zmodyfikowania układu
> * Dodawanie kafelków telemetrycznych do warunków wyświetlania
> * Dodaj kafelki właściwości, aby wyświetlić szczegóły urządzenia
> * Dodawanie kafelków poleceń do uruchamiania poleceń

## <a name="prerequisites"></a>Wymagania wstępne

Konstruktor powinien ukończyć ten samouczek, aby utworzyć aplikację analityczną w Sklepie Azure IoT Central i dodać urządzenia:

* [Tworzenie aplikacji analizy w sklepie na platformie Azure IoT Central](./tutorial-in-store-analytics-create-app.md) (wymagane)

## <a name="change-the-dashboard-name"></a>Zmień nazwę pulpitu nawigacyjnego
Aby dostosować pulpit nawigacyjny operatora, należy edytować domyślny pulpit nawigacyjny w aplikacji. Ponadto można tworzyć dodatkowe nowe pulpity nawigacyjne. Pierwszym krokiem dostosowania pulpitu nawigacyjnego w aplikacji jest zmiana nazwy.

1. Przejdź do witryny sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .

1. Otwórz aplikację monitorowania warunków utworzoną w samouczku [Tworzenie aplikacji analizy w sklepie w usłudze Azure IoT Central](./tutorial-in-store-analytics-create-app.md) .

1. Na pasku narzędzi pulpitu nawigacyjnego wybierz pozycję **Edytuj** . W trybie edycji można dostosować wygląd, układ i zawartość pulpitu nawigacyjnego.

    ![Pulpit nawigacyjny edytowanie IoT Central platformy Azure](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-edit.png)

1. Opcjonalnie Ukryj okienko po lewej stronie. Ukrycie lewego okienka zapewnia większy obszar roboczy do edytowania pulpitu nawigacyjnego.

1. Wprowadź przyjazną nazwę pulpitu nawigacyjnego w polu **Nazwa pulpitu nawigacyjnego.** W tym samouczku jest stosowana fikcyjna firma o nazwie contoso, a Przykładowa nazwa pulpitu nawigacyjnego to *contoso Dashboard*. 

1. Wybierz pozycję **Zapisz**. Zmiany są zapisywane na pulpicie nawigacyjnym, a tryb edycji jest wyłączony.

    ![Nazwa pulpitu nawigacyjnego zmiany IoT Central platformy Azure](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Dostosowywanie kafelków obrazów na pulpicie nawigacyjnym
Pulpit nawigacyjny aplikacji IoT Central platformy Azure składa się z co najmniej jednego kafelka. Kafelek to prostokątny kontener służący do wyświetlania zawartości na pulpicie nawigacyjnym. Można kojarzyć różne typy zawartości z kafelkami oraz przeciągać, upuszczać i zmieniać rozmiar kafelków, aby dostosować układ pulpitu nawigacyjnego. Istnieje kilka typów kafelków do wyświetlania zawartości. Kafelki obrazów zawierają obrazy i można dodać adres URL, który umożliwia użytkownikom kliknięcie obrazu. Kafelki etykiet wyświetlają zwykły tekst. Kafelki promocji zawierają sformatowaną zawartość i umożliwiają ustawienie obrazu, adresu URL, tytułu i kodu promocji, który jest renderowany jako HTML. Kafelki telemetrii, właściwości lub polecenia wyświetlają dane specyficzne dla urządzenia. 

W tej sekcji dowiesz się, jak dostosować kafelki obrazów na pulpicie nawigacyjnym.

Aby dostosować kafelek obrazu, który wyświetla obraz marki na pulpicie nawigacyjnym:

1. Na pasku narzędzi pulpitu nawigacyjnego wybierz pozycję **Edytuj** . 

1. Wybierz pozycję **Konfiguruj** na kafelku obrazu wyświetlającym obraz marki Northwind. 

    ![Azure IoT Central Edytowanie obrazu marki](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-edit.png)

1. Zmień **tytuł**. Tytuł pojawia się, gdy użytkownik umieści wskaźnik myszy nad obrazem.

1. Wybierz pozycję **obraz**. Zostanie otwarte okno dialogowe, które umożliwi przekazanie obrazu niestandardowego. 

1. Opcjonalnie określ adres URL obrazu.

1. Wybierz pozycję **Konfiguracja aktualizacji**. Przycisk **Aktualizuj konfigurację** zapisuje zmiany na pulpicie nawigacyjnym i pozostawia tryb edycji włączony.

    ![Azure IoT Central Zapisywanie obrazu marki](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-save.png)

1. Opcjonalnie możesz wybrać pozycję **Konfiguruj** na kafelku zatytułowanej **Dokumentacja** i określić adres URL dla zawartości pomocy technicznej. 

Aby dostosować kafelek obrazu, który wyświetla mapę stref czujników w sklepie:

1. Wybierz pozycję **Konfiguruj** na kafelku obrazu, który wyświetla domyślną mapę strefy magazynu. 

1. Wybierz pozycję **obraz**, a następnie użyj okna dialogowego do przekazania niestandardowego obrazu mapy strefy magazynu. 

1. Wybierz pozycję **Konfiguracja aktualizacji**.

    ![Mapa magazynu usługi Azure IoT Central Save](./media/tutorial-in-store-analytics-customize-dashboard/store-map-save.png)

    Przykładowa Mapa magazynu contoso przedstawia cztery strefy: dwie strefy wyewidencjonowania, strefę do oddziału i opiekę osobistą oraz strefę artykułów spożywczych i rapor. W tym samouczku zostaną skojarzone czujniki z tymi strefami w celu zapewnienia telemetrii.

    ![Strefy magazynu IoT Central platformy Azure](./media/tutorial-in-store-analytics-customize-dashboard/store-zones.png)

1. Wybierz pozycję **Zapisz**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Rozmieść kafelki w celu zmodyfikowania układu
Najważniejszym krokiem w dostosowywaniu pulpitu nawigacyjnego jest ponowne rozmieszczenie kafelków, aby utworzyć przydatny widok. Operatorzy aplikacji używają pulpitu nawigacyjnego do wizualizacji danych telemetrycznych urządzeń, zarządzania urządzeniami i monitorowania warunków w sklepie. Usługa Azure IoT Central upraszcza zadanie tworzenia pulpitu nawigacyjnego w programie Application Builder. Tryb edycji pulpitu nawigacyjnego umożliwia szybkie dodawanie, przenoszenie, zmienianie rozmiaru i usuwanie kafelków. Szablon aplikacji **do analizy w sklepie** umożliwia także uproszczenie zadania tworzenia pulpitu nawigacyjnego. Udostępnia układ działającego pulpitu nawigacyjnego, z połączonymi czujnikami i kafelki, które wyświetlają liczbę wierszy wyewidencjonowywania i warunki środowiska.

W tej sekcji można zmienić układ pulpitu nawigacyjnego w szablonie aplikacji **analizy w sklepie — wyewidencjonowywanie w** celu utworzenia niestandardowego układu.

Aby usunąć kafelki, których nie planujesz używać w aplikacji:

1. Na pasku narzędzi pulpitu nawigacyjnego wybierz pozycję **Edytuj** . 

1. Wybierz pozycję **X Usuń** , aby usunąć następujące kafelki: **Wróć do wszystkich stref**, **odwiedź pulpit nawigacyjny sklepu**, **czas oczekiwania** i wszystkie trzy kafelki skojarzone z **wyewidencjonowaniem 3**. Pulpit nawigacyjny sklepu contoso nie używa tych kafelków. 

    ![IoT Central usuwania kafelków przez platformę Azure](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles.png)

1. Przewiń, aby przenieść pozostałe kafelki pulpitu nawigacyjnego do widoku.

1. Wybierz pozycję **X Usuń** , aby usunąć następujące kafelki: **strefę wypełniania do wyewidencjonowania**, **strefę wyewidencjonowania w chłodnej**, **Ustawienia czujnika użytkowania**, **Ustawienia czujnika termostatu** i **warunki środowiska**. 

   ![Azure IoT Central Usuń pozostałe kafelki](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles-2.png)

1. Wybierz pozycję **Zapisz**. Usunięcie nieużywanych kafelków zwalnia miejsce na stronie edytowania i upraszcza widok pulpitu nawigacyjnego dla operatorów.

1. Wyświetl zmiany na pulpicie nawigacyjnym.

   ![IoT Central platformy Azure po usunięciu kafelków](./media/tutorial-in-store-analytics-customize-dashboard/after-delete-tiles.png)

Po usunięciu nieużywanych kafelków Rozmieść pozostałe kafelki w celu utworzenia zorganizowanego układu. Nowy układ obejmuje miejsce dla kafelków dodawanych w późniejszym kroku.

Aby zmienić rozmieszczenie pozostałych kafelków:

1. Wybierz pozycję **Edytuj**.

1. Wybierz kafelek **oprogramowanie układowe zajętości** i przeciągnij go na prawo od kafelka baterii **zajętość** .

1. Wybierz kafelek **oprogramowanie układowe termostatu** i przeciągnij go na prawo od kafelka baterii **termostat** .

1. Wybierz pozycję **Zapisz**.

1. Wyświetl zmiany układu. 

    ![Kafelki baterii oprogramowania układowego IoT Central platformy Azure](./media/tutorial-in-store-analytics-customize-dashboard/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Dodawanie kafelków telemetrycznych do warunków wyświetlania
Po dostosowaniu układu pulpitu nawigacyjnego możesz dodać kafelki, aby wyświetlić dane telemetryczne. Aby utworzyć kafelek telemetrię, wybierz szablon urządzenia i wystąpienie urządzenia, a następnie wybierz dane telemetryczne dotyczące urządzenia, aby wyświetlić je na kafelku. Szablon aplikacji **Analiza w sklepie — wyewidencjonowywanie** zawiera kilka kafelków telemetrycznych na pulpicie nawigacyjnym. Cztery kafelki w dwóch strefach wyewidencjonowania wyświetlają dane telemetryczne z czujnika symulowanego zajętości. Kafelek **ruch osób** wyświetla liczbę w dwóch strefach wyewidencjonowania. 

W tej sekcji dodasz dwa więcej kafelków telemetrycznych, aby pokazać dane telemetryczne środowiska z czujników RuuviTag dodanych w samouczku [Tworzenie aplikacji do analizy w Sklepie Azure IoT Central](./tutorial-in-store-analytics-create-app.md) . 

Aby dodać kafelki do wyświetlania danych o środowisku z czujników RuuviTag:

1. Wybierz pozycję **Edytuj**.

1. Wybierz pozycję `RuuviTag` z listy **szablon urządzenia** . 

1. Wybierz **wystąpienie urządzenia** jednej z dwóch czujników RuuviTag. W przykładowym sklepie contoso wybierz opcję `Zone 1 Ruuvi` utworzenia kafelka telemetrii dla Strefa 1. 

1. Wybierz `Relative humidity` i `temperature` na liście **telemetrii** . Są to elementy telemetrii, które są wyświetlane dla każdej strefy na kafelku.

1. Wybierz pozycję **Połącz**. 

    ![Azure IoT Central dodać kafelek RuuviTag 1](./media/tutorial-in-store-analytics-customize-dashboard/add-zone1-ruuvi.png)

    Zostanie wyświetlony nowy kafelek, aby wyświetlić łączną wilgotność i dane telemetryczne temperatury dla wybranego czujnika. 

1. Wybierz pozycję **Konfiguruj** na nowym kafelku dla czujnika RuuviTag. 

1. Zmień **tytuł** na *Strefa 1 środowiska*. 

1. Wybierz pozycję **Konfiguracja aktualizacji**.

1. Powtórz poprzednie kroki, aby utworzyć kafelek dla drugiego wystąpienia czujnika. Ustaw **tytuł** na *strefa 2 środowisko* , a następnie wybierz pozycję **Aktualizuj konfigurację.**

1. Przeciągnij kafelek zatytułowany **strefa 2 środowisko** poniżej kafelka **oprogramowania układowego termostatu** . 

1. Przeciągnij kafelek zatytułowany **Strefa 1 środowisko** poniżej kafelka **ruch dla osób** . 

1. Wybierz pozycję **Zapisz**. Pulpit nawigacyjny wyświetla dane telemetryczne strefy w dwóch nowych kafelkach.

    ![Platforma Azure IoT Central wszystkie kafelki RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard/all-ruuvitag-tiles.png)

Aby edytować kafelek **ruch** dotyczący osób, aby wyświetlić dane telemetryczne tylko dla dwóch stref wyewidencjonowania:

1. Wybierz pozycję **Edytuj**. 

1. Wybierz pozycję **Konfiguruj** na kafelku **ruch osób** .

1. W polu **Telemetria** wybierz **liczbę 1**, **liczbę 2** i **liczbę 3**. 

1. Wybierz pozycję **Konfiguracja aktualizacji**. Czyści istniejącą konfigurację na kafelku. 

1. Wybierz pozycję **Konfiguruj** ponownie na kafelku **ruch osób** .

1. W polu **Telemetria** wybierz **liczbę 1** i **liczbę 2**. 

1. Wybierz pozycję **Konfiguracja aktualizacji**. 

1. Wybierz pozycję **Zapisz**.  Zaktualizowany pulpit nawigacyjny wyświetla liczbę tylko dla dwóch stref wyewidencjonowania, które są oparte na czujniku zajętości.

    ![Ruch przychodzący dwóch torów przez platformę Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Dodaj kafelki właściwości, aby wyświetlić szczegóły urządzenia
Operatorzy aplikacji używają pulpitu nawigacyjnego do zarządzania urządzeniami i monitorowania stanu. Dodaj kafelek dla każdego RuuviTagu, aby umożliwić operatorom Wyświetlanie wersji oprogramowania. 

Aby dodać kafelek właściwości dla każdego RuuviTagu:

1. Wybierz pozycję **Edytuj**.

1. Wybierz pozycję `RuuviTag` z listy **szablon urządzenia** . 

1. Wybierz **wystąpienie urządzenia** jednej z dwóch czujników RuuviTag. W przykładowym sklepie contoso wybierz opcję `Zone 1 Ruuvi` utworzenia kafelka telemetrii dla Strefa 1. 

1. Wybierz pozycję **właściwości > wersja oprogramowania**.

1. Wybierz pozycję **Połącz**. 

1. Wybierz pozycję **Konfiguruj** na nowo utworzonym kafelku zatytułowanym **wersja oprogramowania**. 

1. Zmień **tytuł** na *ruuvi 1 wersję oprogramowania*.

1. Wybierz pozycję **Konfiguracja aktualizacji**. 

1. Przeciągnij kafelek zatytułowany **Ruuv 1 wersja oprogramowania** poniżej kafelka **środowiska Strefa 1** .

1. Powtórz poprzednie kroki, aby utworzyć kafelek właściwości wersja oprogramowania dla drugiego RuuviTag. 

1. Wybierz pozycję **Zapisz**.  

    ![Kafelki właściwości usługi Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Dodawanie kafelków poleceń do uruchamiania poleceń
Operatory aplikacji również używają pulpitu nawigacyjnego do zarządzania urządzeniami za pomocą poleceń. Do pulpitu nawigacyjnego można dodawać kafelki poleceń, które będą wykonywać wstępnie zdefiniowane polecenia na urządzeniu. W tej sekcji dodasz kafelek poleceń, aby umożliwić operatorom ponowne uruchomienie bramy Rigado. 

Aby dodać kafelek poleceń w celu ponownego uruchomienia bramy:

1. Wybierz pozycję **Edytuj**. 

1. Wybierz pozycję `C500` z listy **szablon urządzenia** . Jest to szablon bramy usługi Rigado C500. 

1. Wybierz wystąpienie bramy w **wystąpieniu urządzenia**.

1. Wybierz **polecenie > Uruchom ponownie** i przeciągnij je do pulpitu nawigacyjnego obok mapy magazynu. 

1. Wybierz pozycję **Zapisz**. 

1. Wyświetl ukończony pulpit nawigacyjny firmy Contoso. 

    ![Zakończono Dostosowywanie pulpitu nawigacyjnego w usłudze Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/completed-dashboard.png)

1. Opcjonalnie wybierz kafelek **ponownego** uruchamiania, aby uruchomić polecenie ponownego uruchamiania na bramie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Zmień nazwę pulpitu nawigacyjnego
* Dostosowywanie kafelków obrazów na pulpicie nawigacyjnym
* Rozmieść kafelki w celu zmodyfikowania układu
* Dodawanie kafelków telemetrycznych do warunków wyświetlania
* Dodaj kafelki właściwości, aby wyświetlić szczegóły urządzenia
* Dodawanie kafelków poleceń do uruchamiania poleceń

Teraz, po dostosowaniu pulpitu nawigacyjnego w aplikacji do analizy w sklepie IoT Central Azure, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Eksportowanie danych i wizualizacja szczegółowych informacji](./tutorial-in-store-analytics-export-data-visualize-insights.md)
