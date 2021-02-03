---
title: Importowanie informacji o urządzeniu
description: Usługa Defender for IoT monitoruje i analizuje ruch dublowany. W takich przypadkach możesz chcieć zaimportować dane do wzbogacania informacji na urządzeniach, które zostały już wykryte.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 333ffbf4107dfd005ba7e7fae6a079a618e0c645
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509439"
---
# <a name="import-device-information-to-a-sensor"></a>Importuj informacje o urządzeniu do czujnika

Usługa Azure Defender for IoT monitoruje i analizuje ruch dublowany. W niektórych przypadkach ze względu na specyficzne dla organizacji zasady konfiguracji niektóre informacje mogą nie zostać przesłane.

W takich przypadkach może zajść potrzeba zaimportowania danych do wzbogacania informacji na urządzeniach, które zostały już wykryte. Dostępne są dwie opcje importowania informacji do czujników:

- **Importuj z mapy**: zaktualizuj nazwę urządzenia, typ, grupę lub warstwę Purdue do mapy.

- **Importuj z ustawień importowania**: zaimportuj system operacyjny urządzenia, adres IP, poziom poprawki lub stan autoryzacji.

## <a name="import-from-the-map"></a>Importuj z mapy

W tej sekcji opisano sposób importowania nazw, typów, grup lub warstw Purdue urządzeń do mapy urządzeń. Można to zrobić z poziomu mapy.

Poniżej przedstawiono wymagania dotyczące importowania:

- **Nazwy**: może składać się z maksymalnie 30 znaków.

- **Typ** lub **warstwa Purdue**: Użyj opcji, które pojawiają się w oknie dialogowym **Właściwości urządzenia** . (Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz polecenie **Wyświetl właściwości**.)

- **Grupa urządzeń**: Utwórz nową grupę o długości do 30 znaków. 

> [!NOTE]
> Aby uniknąć konfliktów, nie należy importować danych, które zostały wyeksportowane z jednej czujnika do innego czujnika.

Aby zaimportować:

1. W menu po stronie wybierz pozycję **urządzenia**.

2. W prawym górnym rogu okna **urządzenia** wybierz opcję :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Zrzut ekranu przedstawiający okno urządzenia.":::

3. Wybierz pozycję **Eksportuj urządzenia**. W wyeksportowanym pliku pojawia się obszerny zakres informacji. Te informacje obejmują protokoły używane przez urządzenie oraz stan autoryzacji urządzenia.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informacje w wyeksportowanym pliku.":::

4. W pliku CSV Zmień tylko nazwę urządzenia, typ, grupę i warstwę Purdue. Następnie zapisz plik. 

   Użyj standardów kapitalizacji przedstawionych w wyeksportowanym pliku. Na przykład dla warstwy Purdue należy używać wszystkich wielkich liter.

5. Z menu rozwijanego **Importuj/Eksportuj** w oknie **urządzenie** wybierz pozycję **Importuj urządzenia**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importuj urządzenia za pomocą okna urządzenia.":::

6. Wybierz pozycję **Importuj urządzenia** i wybierz plik CSV, który chcesz zaimportować. Komunikaty o stanie importowania są wyświetlane na ekranie do momentu zamknięcia okna dialogowego **Importowanie urządzeń** .

## <a name="import-from-import-settings"></a>Importuj z ustawień importowania

W tej sekcji opisano sposób importowania adresu IP urządzenia, systemu operacyjnego, poziomu poprawki lub stanu autoryzacji do mapy urządzeń. Można to zrobić w oknie dialogowym **ustawienia importowania** .

Aby zaimportować adres IP, system operacyjny i poziom poprawek:

1. Pobierz pliki [devices_info_2.2.8 i up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) z [centrum pomocy](https://cyberx-labs.zendesk.com/hc/en-us) i wprowadź informacje w następujący sposób:

   - **Adres IP**: wprowadź adres IP urządzenia.

   - **System operacyjny**: wybierz z listy rozwijanej.

   - **Ostatnia aktualizacja**: Użyj formatu rrrr-mm-dd.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Ekran Opcje.":::

2. W menu po stronie wybierz pozycję **Importuj ustawienia**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Zaimportuj ustawienia.":::

3. Aby przekazać wymaganą konfigurację, w sekcji **Informacje o urządzeniu** wybierz pozycję **Dodaj** i przekaż przygotowany plik CSV.

Aby zaimportować stan autoryzacji:

1. Pobierz i Zapisz plik [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) z centrum pomocy usługi Defender for IoT. Sprawdź, czy plik został zapisany jako wolumin CSV.

2. Wprowadź informacje jako:

   - **Adres IP**: adres IP urządzenia.

   - **Nazwa**: Nazwa autoryzowanego urządzenia. Upewnij się, że nazwy są dokładne. Nazwy nadane urządzeniom na zaimportowanej liście Zastąp nazwy widoczne na mapie urządzeń.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Pliki programu Excel z zaimportowaną listą urządzeń.":::

3. W menu po stronie wybierz pozycję **Importuj ustawienia**.

4. W sekcji **autoryzowane urządzenia** wybierz pozycję **Dodaj** i przekaż zapisany plik CSV.

Po zaimportowaniu informacji otrzymujesz alerty dotyczące nieautoryzowanych urządzeń dla wszystkich urządzeń, które nie są wyświetlane na tej liście.

## <a name="import-device-information-to-the-sensor"></a>Importuj informacje o urządzeniu do czujnika

Czujnik monitoruje i analizuje ruch dublowany. W niektórych przypadkach ze względu na specyficzne dla organizacji zasady konfiguracji niektóre informacje mogą nie zostać przesłane.

W takich przypadkach może zajść potrzeba zaimportowania danych do wzbogacania informacji o urządzeniu na urządzeniach, które zostały już wykryte. Dostępne są dwie opcje importowania informacji do czujników:

- **Importuj z mapy**: zaktualizuj nazwę urządzenia, typ, grupę lub warstwę Purdue do mapy.

- **Importuj z ustawień importowania**: zaimportuj system operacyjny urządzenia, adres IP, poziom poprawki lub stan autoryzacji.

### <a name="import-from-the-map"></a>Importuj z mapy

W tej sekcji opisano sposób importowania nazw, typów, grup lub warstw Purdue urządzeń do mapy urządzeń. Można to zrobić z poziomu mapy.

Poniżej przedstawiono wymagania dotyczące importowania:

- **Nazwy**: może składać się z maksymalnie 30 znaków.

- **Typ** lub **warstwa Purdue**: Użyj opcji, które pojawiają się w oknie dialogowym **Właściwości urządzenia** . (Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz polecenie **Wyświetl właściwości**.)

- **Grupa urządzeń**: Utwórz nową grupę o długości do 30 znaków. 

> [!NOTE]
> Aby uniknąć konfliktów, nie należy importować danych, które zostały wyeksportowane z jednej czujnika do innego czujnika.

Aby zaimportować:

1. W menu po stronie wybierz pozycję **urządzenia**.

2. W prawym górnym rogu okna **urządzenia** wybierz opcję :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Okno, z którego ma zostać wybrane urządzenie.":::

3. Wybierz pozycję **Eksportuj urządzenia**. W wyeksportowanym pliku pojawia się obszerny zakres informacji. Przykłady obejmują protokoły używane przez urządzenie oraz stan autoryzacji urządzenia.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informacje w wyeksportowanym pliku.":::

4. W pliku CSV Zmień tylko nazwę urządzenia, typ, grupę i warstwę Purdue. Następnie zapisz plik. 

   Użyj standardów kapitalizacji przedstawionych w wyeksportowanym pliku. Na przykład dla warstwy Purdue należy używać wszystkich wielkich liter.

5. Z menu rozwijanego **Importuj/Eksportuj** w oknie **urządzenie** wybierz pozycję **Importuj urządzenia**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Zaimportuj urządzenia.":::

6. Wybierz pozycję **Importuj urządzenia** i wybierz plik CSV, który chcesz zaimportować. Komunikaty o stanie importowania są wyświetlane na ekranie do momentu zamknięcia okna dialogowego **Importowanie urządzeń** .

### <a name="import-from-import-settings"></a>Importuj z ustawień importowania 

W tej sekcji opisano sposób importowania adresu IP urządzenia, systemu operacyjnego, poziomu poprawki lub stanu autoryzacji do mapy urządzeń. Można to zrobić w oknie dialogowym **ustawienia importowania** .

Aby zaimportować adres IP, system operacyjny i poziom poprawek:

1. Pobierz pliki [devices_info_2.2.8 i up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) z [centrum pomocy](https://cyberx-labs.zendesk.com/hc/en-us) i wprowadź informacje w następujący sposób:

   - **Adres IP**: adres IP urządzenia.

   - **System operacyjny**: wybierz z listy rozwijanej.

   - **Data ostatniej aktualizacji**: Użyj formatu rrrr-mm-dd.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Zawartość na ekranie.":::

2. W menu po stronie wybierz pozycję **Importuj ustawienia**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Wypełnij ekran ustawienia importowania.":::

3. Aby przekazać wymaganą konfigurację, w sekcji **Informacje o urządzeniu** wybierz pozycję **Dodaj** i przekaż przygotowany plik CSV.

Aby zaimportować stan autoryzacji:

1. Pobierz i Zapisz plik [authorized_devices-examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) z centrum pomocy usługi Defender for IoT. Sprawdź, czy plik został zapisany jako wolumin CSV.

2. Wprowadź informacje jako:

   - **Adres IP**: adres IP urządzenia.

   - **Nazwa**: Nazwa autoryzowanego urządzenia. Sprawdź, czy nazwy są dokładne. Nazwy nadane urządzeniom na zaimportowanej liście Zastąp nazwy widoczne na mapie urządzeń.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Lista importowania do mapy urządzeń.":::

3. W menu po stronie wybierz pozycję **Importuj ustawienia**.

4. W sekcji **autoryzowane urządzenia** wybierz pozycję **Dodaj** i przekaż zapisany plik CSV.

Po zaimportowaniu informacji otrzymujesz alerty dotyczące nieautoryzowanych urządzeń dla wszystkich urządzeń, które nie są wyświetlane na tej liście.

## <a name="see-also"></a>Zobacz też

[Sterowanie monitorowanym ruchem](how-to-control-what-traffic-is-monitored.md)

[Badanie wykryć czujników w spisie urządzeń](how-to-investigate-sensor-detections-in-a-device-inventory.md)
