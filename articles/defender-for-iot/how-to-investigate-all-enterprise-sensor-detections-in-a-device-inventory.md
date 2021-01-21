---
title: Informacje o urządzeniach odnalezionych przez wszystkie czujniki przedsiębiorstwa
description: Użyj spisu urządzeń w lokalnej konsoli zarządzania, aby uzyskać Kompleksowy wgląd w informacje o urządzeniu z połączonych czujników. Do zarządzania tymi informacjami służą narzędzia importu, eksportu i filtrowania.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9da5c8c89ee124e527584164b21b096ac815e5ca
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625468"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>Zbadaj wszystkie wykrycia czujnika przedsiębiorstwa w spisie urządzeń

Informacje o urządzeniu z połączonych czujników można wyświetlić za pomocą *spisu urządzeń* w lokalnej konsoli zarządzania. Ta funkcja zapewnia kompleksowy wgląd we wszystkie informacje o sieci. Do zarządzania tymi informacjami służą narzędzia importu, eksportu i filtrowania. Wyświetlane są również informacje o stanie z podłączonymi wersjami czujników.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="Zrzut ekranu przedstawiający tabelę dane spisu urządzeń.":::

W poniższej tabeli opisano kolumny tabeli w spisie urządzeń.

| Parametr | Opis |
|--|--|
| **Niepotwierdzone alerty** | Liczba nieobsłużonych alertów skojarzonych z tym urządzeniem. |
| **Jednostka biznesowa** | Jednostka biznesowa, która zawiera to urządzenie. |
| **Region** | Region, który zawiera to urządzenie. |
| **Witryna** | Lokacja zawierająca to urządzenie. |
| **Strefa** | Strefa, która zawiera to urządzenie. |
| **Wprowadzony** | Czujnik usługi Azure Defender dla IoT, który chroni to urządzenie. |
| **Nazwa** | Nazwa tego urządzenia wykryta przez usługę Defender for IoT. |
| **Typ** | Typ urządzenia, taki jak PLC lub HMI. |
| **Dostawca** | Nazwa dostawcy urządzenia, zgodnie z definicją w adresie MAC. |
| **System operacyjny** | System operacyjny urządzenia. |
| **Oprogramowanie układowe** | Oprogramowanie układowe urządzenia. |
| **Adres IP** | Adres IP urządzenia. |
| **OKREŚLONE** | Sieć VLAN urządzenia. |
| **Adres MAC** | Adres MAC urządzenia. |
| **Protokoły** | Protokoły używane przez urządzenie. |
| **Niepotwierdzone alerty** | Liczba nieobsłużonych alertów skojarzonych z tym urządzeniem. |
| **Jest autoryzowany** | Stan autoryzacji urządzenia:<br />- **Prawda**: urządzenie zostało autoryzowane.<br />- **False**: urządzenie nie zostało autoryzowane. |
| **Jest znany jako skaner** | Czy to urządzenie wykonuje działania podobne do skanowania w sieci. |
| **Program jest urządzeniem programistycznym** | Bez względu na to, czy jest to urządzenie programistyczne:<br />- **Prawda**: urządzenie wykonuje działania programistyczne dla plcs, RTUs i kontrolerów, które są istotne dla stacji inżynieryjnych.<br />- **False**: urządzenie nie jest urządzeniem programistycznym. |
| **Grupy** | Grupy, w których uczestniczą to urządzenie. |
| **Ostatnia aktywność** | Ostatnia aktywność przeprowadzona przez urządzenie. |
| **Discovered** | Po pierwszym napotkaniu tego urządzenia w sieci. |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>Integrowanie danych z spisem urządzeń przedsiębiorstwa

Funkcje integracji danych pozwalają ulepszyć dane w spisie urządzeń przy użyciu informacji z innych zasobów przedsiębiorstwa. Te źródła obejmują dyrektywy CMDB, DNS, zapory i interfejsy API sieci Web.

Możesz użyć tych informacji, aby dowiedzieć się. Przykład:

- Daty zakupu urządzenia i daty końcowej gwarancji

- Użytkownicy odpowiedzialni za każde urządzenie

- Otwarte bilety dla urządzeń

- Ostatnia data uaktualnienia oprogramowania układowego

- Urządzenia, na których zezwolono na dostęp do Internetu

- Urządzenia z uruchomionymi aktywnymi aplikacjami antywirusowymi

- Użytkownicy zalogowani do urządzeń

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="Tabela danych na ekranie spisu urządzeń.":::

Możesz zintegrować dane przez:

- Ręczne dodawanie

- Uruchamianie niestandardowych skryptów dostępnych w usłudze Defender for IoT

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="Diagram integratora danych przedsiębiorstwa.":::

Możesz współpracować z usługą Defender for IoT, aby skonfigurować system do odbierania zapytań interfejsu API sieci Web.

Aby ręcznie dodać dane:

1. W menu po stronie wybierz pozycję **spis urządzeń** , a następnie wybierz pozycję :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="Edytuj ustawienia spisu urządzenia.":::

2. W oknie dialogowym **ustawienia spisu urządzeń** wybierz pozycję **Dodaj kolumnę niestandardową**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Dodaj kolumnę niestandardową do spisu.":::

3. W oknie dialogowym **Dodaj kolumnę niestandardową** Dodaj nową nazwę kolumny (do 250 znaków UTF), wybierz pozycję **Ręczne** i wybierz pozycję **Zapisz**. Nowy element pojawi się w oknie dialogowym **ustawienia spisu urządzeń** .

4. W prawym górnym rogu okna **spisu urządzeń** wybierz :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: i wybierz pozycję **Eksportuj wszystkie spisy urządzeń**. Plik CSV jest generowany.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="Wyeksportowany plik CSV.":::

5. Ręcznie Dodaj informacje do nowej kolumny i Zapisz plik.

6. W prawym górnym rogu okna **spisu urządzeń** wybierz pozycję :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: **Importuj ręczne wprowadzanie kolumn** i przejdź do pliku CSV. Nowe dane zostaną wyświetlone w tabeli **spisu urządzeń** .

Aby zintegrować dane z innych jednostek przedsiębiorstwa:

1. W prawym górnym rogu okna **spisu urządzeń** wybierz :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: i wybierz pozycję **Eksportuj wszystkie spisy urządzeń**.

2. W oknie dialogowym **ustawienia spisu urządzeń** wybierz pozycję **Dodaj kolumnę niestandardową**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Dodaj kolumnę niestandardową do spisu.":::

3. W oknie dialogowym **Dodaj kolumnę niestandardową** Dodaj nową nazwę kolumny (do 250 znaków UTF), a następnie wybierz opcję **Automatyczne**. Wyświetlane są opcje skryptu **przekazywania** i **skryptu testowego** .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="Automatycznie Dodaj kolumny niestandardowe.":::

4. Przekaż i przetestuj skrypt otrzymany z [Pomoc techniczna firmy Microsoft](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="retrieve-information-from-the-device-inventory"></a>Pobierz informacje ze spisu urządzeń

Można pobrać obszerny zakres informacji o urządzeniu wykrywanych przez czujniki zarządzane i zintegrować te informacje z systemami partnerskimi. Można na przykład uzyskać informacje dotyczące czujnika, strefy, identyfikatora witryny, adresu IP, adresu MAC, oprogramowania układowego, protokołu i dostawcy. Odfiltruj Informacje pobierane na podstawie:

- Autoryzowane i nieautoryzowane urządzenia.

- Urządzenia skojarzone z określonymi lokacjami.

- Urządzenia skojarzone z określonymi strefami.

- Urządzenia skojarzone z określonymi czujnikami.

Aby pobrać i zintegrować te informacje, Pracuj z poleceniami interfejsu API usługi Defender for IoT. Aby uzyskać więcej informacji, zobacz temat [czujnik interfejsów API usługi Defender for IoT i interfejsy API konsoli zarządzania](references-work-with-defender-for-iot-apis.md).

## <a name="filter-the-device-inventory"></a>Filtrowanie spisu urządzeń

Można filtrować spis urządzeń w celu wyświetlenia interesujących kolumn. Można na przykład wyświetlić informacje o urządzeniu PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="Zrzut ekranu przedstawiający spis urządzeń.":::

Filtr jest wyczyszczony po opuszczeniu okna.

Aby użyć tego samego filtru wiele razy, można zapisać filtr lub kombinację filtrów, które są potrzebne. Możesz otworzyć lewe okienko i wyświetlić zapisane filtry:

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="Ekran spisów urządzeń.":::

Aby odfiltrować spis urządzeń:

1. W kolumnie, która ma zostać przefiltrowana, wybierz opcję :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: .

2. W oknie dialogowym **Filtr** wybierz typ filtru:

   - **Równa** się: dokładna wartość, według której chcesz filtrować kolumnę. Na przykład w przypadku filtrowania kolumny protokołu zgodnie z parametrem **Equals** i `value=ICMP` , w kolumnie będą widoczne tylko urządzenia korzystające z protokołu ICMP.

   - **Zawiera**: wartość, która jest zawarta w innych wartościach w kolumnie. Na przykład w przypadku filtrowania kolumny protokołu zgodnie z parametrami **zawiera** i `value=ICMP` , w kolumnie będą prezentowane urządzenia używające protokołu ICMP jako części listy protokołów używanych przez urządzenie.

3. Aby zorganizować informacje o kolumnie zgodnie z kolejnością alfabetyczną, wybierz opcję :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Uporządkuj kolejność, wybierając :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: strzałki i :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: .

4. Aby zapisać nowy filtr, zdefiniuj filtr i wybierz pozycję **Zapisz jako**.

5. Aby zmienić definicje filtrów, Zmień definicje i wybierz pozycję **Zapisz zmiany**.

## <a name="view-device-information-per-zone"></a>Wyświetl informacje o urządzeniu dla każdej strefy

Poniższe informacje o urządzeniach można znaleźć w strefie.

### <a name="view-a-device-map"></a>Wyświetlanie mapy urządzeń

Aby wyświetlić mapę urządzenia czujnika dla wybranej strefy:

- W oknie **Zarządzanie lokacją** wybierz pozycję **Wyświetl mapę strefy** na pasku zawierającym nazwę strefy.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Domyślny region do domyślnej jednostki biznesowej.":::

Zostanie wyświetlone okno **Mapowanie urządzeń** . Pokazuje wszystkie elementy sieci powiązane z wybraną strefą, w tym czujniki, podłączone do nich urządzenia i inne informacje.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Zrzut ekranu przedstawiający mapę strefy.":::

Następujące narzędzia są dostępne do wyświetlania informacji dotyczących urządzeń i urządzeń z mapy. Aby uzyskać szczegółowe informacje o każdej z tych funkcji, zobacz *Podręcznik użytkownika platformy Defender for IoT*.

- **Widoki powiększenia mapy**: widok uproszczony, widok połączeń i widok szczegółowy. Widok wyświetlonej mapy różni się w zależności od poziomu powiększenia mapy. Można przełączać się między widokami mapy przez dostosowanie poziomów powiększenia.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Narzędzia do wyszukiwania map i układu**: narzędzia używane do wyświetlania różnych segmentów sieci, urządzeń, grup urządzeń lub warstw.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Zrzut ekranu przedstawiający widok narzędzia do wyszukiwania i układu.":::

- **Etykiety i wskaźniki na urządzeniach:** Na przykład liczba urządzeń zgrupowanych w podsieci w sieci IT. W tym przykładzie jest to 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Zrzut ekranu etykiet i wskaźników.":::

- **Wyświetl właściwości urządzenia**: na przykład czujnik monitorujący urządzenie i podstawowe właściwości urządzeń. Kliknij prawym przyciskiem myszy urządzenie, aby wyświetlić jego właściwości.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Zrzut ekranu przedstawiający widok właściwości urządzenia.":::

- **Alert skojarzony z urządzeniem:** Kliknij prawym przyciskiem myszy urządzenie, aby wyświetlić powiązane alerty.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Zrzut ekranu przedstawiający Widok wyświetlanie alertów.":::

### <a name="view-alerts-associated-with-a-zone"></a>Wyświetlanie alertów skojarzonych ze strefą

Aby wyświetlić alerty skojarzone z określoną strefą:

- Wybierz ikonę alertu w oknie **strefy** . 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Widok domyślnej jednostki biznesowej z przykładami.":::

Aby uzyskać więcej informacji, zobacz temat [Omówienie: Praca z alertami](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Wyświetlanie spisu urządzeń dla strefy

Aby wyświetlić spis urządzeń skojarzony z określoną strefą:

- W oknie **strefa** wybierz pozycję **Wyświetl spis urządzeń** .

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Zostanie wyświetlony ekran spisu urządzeń.":::

Aby uzyskać więcej informacji, zobacz temat [badanie wszystkich wykrywania czujników przedsiębiorstwa w spisie urządzeń](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

### <a name="view-additional-zone-information"></a>Wyświetl dodatkowe informacje o strefie

Dostępne są następujące dodatkowe informacje o strefie:

- **Szczegóły strefy**: Wyświetl liczbę urządzeń, alertów i czujników skojarzonych ze strefą.

- **Szczegóły czujnika**: Wyświetl nazwę, adres IP i wersję każdego czujnika przypisanego do strefy.

- **Stan łączności**: jeśli czujnik jest odłączony, Połącz się z czujnika. Zobacz [czujniki połączeń z lokalną konsolą zarządzania](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Postęp aktualizacji**: jeśli czujnik podłączony jest uaktualniany, zostaną wyświetlone Stany uaktualnienia. Podczas uaktualniania lokalna Konsola zarządzania nie odbiera informacji o urządzeniu z czujnika.

## <a name="see-also"></a>Zobacz też

[Badanie wykryć czujników w spisie urządzeń](how-to-investigate-sensor-detections-in-a-device-inventory.md)
