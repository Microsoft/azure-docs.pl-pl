---
title: Uzyskaj wgląd w urządzenia wykryte przez określoną czujnik
description: W spisie urządzeń jest wyświetlany szeroki zakres atrybutów urządzeń wykrywanych przez czujnik.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9c868685ad943c1ab9ab263a164111e46294c042
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625435"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Badanie wykryć czujników w spisie urządzeń

W spisie urządzeń jest wyświetlany szeroki zakres atrybutów urządzeń wykrywanych przez czujnik. Dostępne są następujące opcje:

 - Łatwo odfiltruj informacje.

 - Eksportuj informacje do pliku CSV.

 - Zaimportuj szczegóły rejestru systemu Windows.

 - Utwórz grupy do wyświetlania na mapie urządzeń.

## <a name="view-device-attributes-in-the-device-inventory"></a>Wyświetlanie atrybutów urządzenia w spisie urządzeń

Następujące atrybuty są wyświetlane w tabeli spisu urządzeń.

| Parametr | Opis |
|--|--|
| Nazwa | Nazwa urządzenia wykryta przez czujnik. |
| Typ | Typ urządzenia. |
| Dostawca | Nazwa dostawcy urządzenia, zgodnie z definicją w adresie MAC. |
| System operacyjny | System operacyjny urządzenia. |
| Oprogramowanie układowe | Oprogramowanie układowe urządzenia. |
| Adres IP | Adres IP urządzenia. |
| OKREŚLONE | Sieć VLAN urządzenia. Aby uzyskać szczegółowe informacje o tym, jak czujnik wykrywa sieci VLAN, zobacz [Definiowanie nazw sieci VLAN](how-to-manage-the-on-premises-management-console.md#define-vlan-names). (instrukcje: Definiowanie-Management-Console-Network-Settings. MD # define-VLAN-Names). |
| Adres MAC | Adres MAC urządzenia. |
| Protokoły | Protokoły używane przez urządzenie. |
| Niepotwierdzone alerty | Liczba niepotwierdzonych alertów skojarzonych z tym urządzeniem. |
| Jest autoryzowany | Stan autoryzacji zdefiniowany przez użytkownika:<br />- **Prawda**: urządzenie zostało autoryzowane.<br />- **False**: urządzenie nie zostało autoryzowane. |
| Jest znany jako skaner | Zdefiniowane jako urządzenie skanujące przez użytkownika. |
| Program jest urządzeniem programistycznym | Zdefiniowane jako autoryzowane urządzenie programistyczne przez użytkownika. <br />- **Prawda**: urządzenie wykonuje działania programistyczne dla plcs, RTUs i kontrolerów, które są istotne dla stacji inżynieryjnych. <br />- **False**: urządzenie nie jest urządzeniem programistycznym. |
| Grupy | Grupy, w których uczestniczą to urządzenie. |
| Ostatnia aktywność | Ostatnia aktywność przeprowadzona przez urządzenie. |
| Discovered | Po pierwszym napotkaniu tego urządzenia w sieci. |

Aby wyświetlić spis urządzeń:

1. W lewym okienku wybierz pozycję **urządzenia**. Po prawej stronie zostanie otwarte okienko **urządzenia** .

2. W okienku **urządzenia** wybierz pozycję :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: .

Aby ukryć i wyświetlić kolumny, Dostosuj tabelę spisu urządzeń:

1. W prawym górnym menu spisu urządzeń wybierz pozycję :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Ekran ustawienia spisu urządzeń.":::

2. W oknie **ustawienia spisu urządzeń** wybierz kolumny, które mają być wyświetlane w tabeli spisu urządzeń.

3. Zmień lokalizację kolumn w tabeli, używając strzałek.

4. Wybierz pozycję **Zapisz**. Zostanie zamknięte okno **ustawienia spisu urządzeń** , a w tabeli zostaną wyświetlone nowe ustawienia.

### <a name="create-temporary-device-inventory-filters"></a>Tworzenie tymczasowych filtrów spisu urządzeń

Można ustawić filtr określający, jakie informacje są wyświetlane w tabeli. Na przykład możesz zdecydować, że chcesz wyświetlić tylko informacje o urządzeniu PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Urządzenia — uczenie się.":::

Filtr nie jest zapisywany w przypadku opuszczenia spisu.

### <a name="save-device-inventory-filters"></a>Zapisz filtry spisu urządzeń

Możesz zapisać filtr lub kombinację filtrów, które są potrzebne, i ponownie zastosować je w spisie urządzeń. Twórz szersze filtry na podstawie określonego typu urządzenia lub bardziej wąskich filtrów opartych na konkretnym typie i określonym protokole.

Zapisane filtry są również zapisywane jako grupy mapy urządzeń. Ta funkcja zapewnia dodatkowy poziom szczegółowości wyświetlania urządzeń sieciowych na mapie.

Aby utworzyć filtry:

1. W kolumnie, która ma zostać przefiltrowana, wybierz opcję :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: .

2. W oknie dialogowym **Filtr** wybierz typ filtru:

   - **Równa** się: dokładna wartość, według której chcesz filtrować kolumnę. Na przykład w przypadku filtrowania kolumny protokołu zgodnie z parametrem **Equals** i `value=ICMP` , w kolumnie będą widoczne tylko urządzenia korzystające z protokołu ICMP.

   - **Zawiera**: wartość, która jest zawarta w innych wartościach w kolumnie. Na przykład w przypadku filtrowania kolumny protokołu zgodnie z parametrami **zawiera** i `value=ICMP` , w kolumnie będą prezentowane urządzenia używające protokołu ICMP jako części listy protokołów używanych przez urządzenie.

3. Aby zorganizować informacje o kolumnie zgodnie z kolejnością alfabetyczną, wybierz opcję :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Uporządkuj kolejność, wybierając :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: strzałki i :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: .

4. Aby zapisać nowy filtr, zdefiniuj filtr i wybierz pozycję **Zapisz jako**.

5. Aby zmienić definicje filtrów, Zmień definicje i wybierz pozycję **Zapisz zmiany**.

Aby wyświetlić filtry:

- Otwórz lewe okienko i Wyświetl zapisane filtry:

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="Wyświetl filtry w okienku po lewej stronie.":::

### <a name="view-filtered-information-as-a-map-group"></a>Wyświetl filtrowane informacje jako grupę map

Po przełączeniu do widoku mapy filtrowane urządzenia są podświetlane i filtrowane. Zapisana grupa filtrów pojawia się w menu bocznym w grupie **filtry spisu urządzeń** .

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Wyświetl filtry w widoku mapy.":::

## <a name="learn-windows-registry-details"></a>Informacje o rejestrze systemu Windows

Oprócz uczenia urządzeń można wykryć urządzenia IT, w tym stacje robocze i serwery z systemem Microsoft Windows. Te urządzenia są również wyświetlane w spisie urządzeń. Po uzyskaniu informacji o urządzeniach można wzbogacić spis urządzeń ze szczegółowymi informacjami systemu Windows, takimi jak:

- Zainstalowana wersja systemu Windows

- Zainstalowane aplikacje

- Informacje na poziomie poprawki

- Otwieranie portów

- Bardziej niezawodne informacje o wersjach systemu operacyjnego

Do pobrania tych informacji są dostępne dwie opcje:

- Aktywne sondowanie za pomocą zaplanowanych skanów WMI. 

- Lokalne badanie przez dystrybuowanie i uruchamianie skryptu na urządzeniu. Praca ze skryptami lokalnymi pomija ryzyko uruchomienia sondowania WMI w punkcie końcowym. Jest on również przydatny w przypadku regulowanych sieci z kaskadami i jednokierunkowymi elementami.

W tym artykule opisano sposób lokalnego badania rejestru punktu końcowego systemu Windows za pomocą skryptu. Te informacje będą używane do generowania alertów, powiadomień, raportów wyszukiwania danych, oceny ryzyka i raportów wektorów ataków.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Zrzut ekranu wyszukiwania danych.":::

Można przebadać następujące systemy operacyjne Windows:

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- System Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby móc współpracować ze skryptem, musisz spełnić następujące wymagania:

- Do uruchomienia skryptu na urządzeniu wymagane są uprawnienia administratora.

- Czujnik powinien już poznać urządzenie z systemem Windows. Oznacza to, że jeśli urządzenie już istnieje, skrypt pobierze informacje.

- Czujnik służy do monitorowania sieci, z którą jest połączony komputer z systemem Windows.

### <a name="acquire-the-script"></a>Pozyskiwanie skryptu

Aby odebrać skrypt, [skontaktuj się z pomocą techniczną](mailto:support.microsoft.com).

### <a name="deploy-the-script"></a>Wdróż skrypt

Skrypt można wdrożyć raz lub zaplanować bieżące zapytania przy użyciu standardowych metod wdrażania i narzędzi.

### <a name="about-the-script"></a>Informacje o skrypcie

- Skrypt jest uruchamiany jako narzędzie i nie jest zainstalowany. Uruchomienie skryptu nie ma wpływu na punkt końcowy.

- Pliki generowane przez skrypt pozostają na dysku lokalnym, dopóki nie zostaną usunięte.

- Pliki generowane przez skrypt znajdują się obok siebie. Nie należy ich rozdzielić.

- Po ponownym uruchomieniu skryptu w tej samej lokalizacji te pliki są zastępowane.

Aby uruchomić skrypt:  

1. Skopiuj skrypt na dysk lokalny i rozpakuj go. Wyświetlane są następujące pliki:

    - start.bat

    - settings.jsna

    - Data. bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="Widok plików w Eksploratorze plików.":::

2. Uruchom `run.bat` plik.

3. Gdy rejestr jest sondowany, plik z migawką CX pojawia się z informacjami rejestru.

4. Nazwa pliku wskazuje nazwę systemową i datę i godzinę migawki. Przykładowa nazwa pliku to `CX-snaphot_SystemName_Month_Year_Time` .

### <a name="import-device-details"></a>Importuj szczegóły urządzenia

Informacje o każdym punkcie końcowym powinny zostać zaimportowane do czujnika.

Pliki generowane na podstawie zapytań można umieścić w jednym folderze, do którego można uzyskać dostęp z czujników. Użyj standardowych, zautomatyzowanych metod i narzędzi do przenoszenia plików z każdego punktu końcowego systemu Windows do lokalizacji, w której zostaną one zaimportowane do czujnika.

Nie Aktualizuj nazw plików.

Aby zaimportować:

1. Wybierz pozycję **ustawienia importowania** z okna dialogowego **Importuj konfigurację systemu Windows** .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Zaimportuj konfiguracje systemu Windows.":::

2. Wybierz pozycję **Dodaj**, a następnie zaznacz wszystkie pliki (Ctrl + A).

3. Wybierz pozycję **Zamknij**. Informacje o rejestrze urządzenia są importowane. Jeśli wystąpi problem podczas przekazywania jednego z plików, otrzymasz informację o tym, które przekazywanie plików nie powiodło się.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="Przekazywanie dodanych plików zakończyło się pomyślnie.":::

## <a name="export-device-inventory-information"></a>Eksportowanie informacji o spisie urządzeń

Informacje o spisie urządzeń można wyeksportować do pliku programu Excel. Zaimportowane informacje zastępują bieżące informacje.

Aby wyeksportować plik CSV:

- W prawym górnym menu spisu urządzeń wybierz pozycję :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: . Raport CSV jest generowany i pobierany.

## <a name="see-also"></a>Zobacz też

[Badanie wszystkich wykryć czujników przedsiębiorstwa w spisie urządzeń](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Pracuj z widokami mapy witryny](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
