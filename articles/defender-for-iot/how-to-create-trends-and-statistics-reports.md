---
title: Generowanie raportów trendów i statystyk
description: Zyskaj wgląd w aktywność sieci, statystyki i trendy przy użyciu usługi Defender for IoT — trendy i statystyk.
ms.date: 2/21/2021
ms.topic: how-to
ms.openlocfilehash: b4539e20ff485f1b6be69fee8e6849298540adcb
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779005"
---
# <a name="sensor-trends-and-statistics-reports"></a>Trendy czujnika i raporty statystyczne

Można tworzyć wykresy widget i wykresy kołowe, aby uzyskać wgląd w trendy sieci i statystyki. Widżety można grupować w obszarze pulpity nawigacyjne zdefiniowane przez użytkownika.

> [!NOTE]
> Analityki i administratorzy zabezpieczeń mogą tworzyć trendy i raporty statystyczne.

Pulpit nawigacyjny składa się z widżetów, które opisują graficznie następujące rodzaje informacji:

- Ruch według portu
- Najpopularniejszy ruch według portu
- Przepustowość kanału
- Całkowita przepustowość
- Aktywne połączenie TCP
- Najpopularniejsza przepustowość według sieci VLAN
- Devices (Urządzenia):
  - Nowe urządzenia
  - Zajęte urządzenia
  - Urządzenia według dostawcy
  - Urządzenia według systemu operacyjnego
  - Liczba urządzeń na sieć VLAN
  - Odłączone urządzenia
- Łączność spadnie o godz.
- Alerty dla incydentów według typu
- Dostęp do tabeli bazy danych
- Elementy widget sekcji protokołu
- DELTAV
  - Rozkład operacji DeltaV Roc
  - DeltaV zdarzenia Roc według nazwy
  - Zdarzenia DeltaV według czasu
- AMS
  - Ruch AMS według portu serwera
  - Ruch z AMS przez polecenie
- Sieć Ethernet i adres IP:
  - Ruch sieciowy sieci Ethernet i IP według usługi PRZELEWu
  - Sieć Ethernet i ruch adresów IP według klasy przelew
  - Ruch sieciowy i adres IP według polecenia
- OPC
  - OPC najważniejsze operacje zarządzania
  - OPC operacji we/wy
- S7 firmy Siemens:
  - Ruch S7 przez funkcję sterowania
  - Ruch S7 przez funkcję podfunkcji
- OKREŚLONE
  - Liczba urządzeń na sieć VLAN
  - Najpopularniejsza przepustowość według sieci VLAN
- 60870-5-104
  - Ruch IEC-60870 przez ASDU
- BACNET
  - Usługi BACnet Services
- DNP3
  - Ruch DNP3 przez funkcję
- SRTP:
  - Ruch SRTP według kodu usługi
  - SRTP błędów według dnia
- SuiteLink:
  - SuiteLink najważniejsze Tagi zapytania
  - Zachowanie tagów numerycznych SuiteLink
- Ruch IEC-60870 przez ASDU
- Ruch DNP3 przez funkcję
- Ruch MMS według usługi
- Ruch Modbus przez funkcję
- Ruch OPC-UA według usługi

> [!NOTE]
>  Czas w widżetach jest ustawiany na podstawie czasu czujnika.

## <a name="create-reports"></a>Tworzenie raportów

Aby wyświetlić pulpity nawigacyjne i widżety:

Wybierz pozycję **trendy & dane statystyczne** w menu po stronie.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Zrzut ekranu przedstawiający badanie.":::

Domyślnie wyniki są wyświetlane w celu wykrycia w ciągu ostatnich siedmiu dni. Możesz użyć narzędzi filtru Zmień ten zakres. Na przykład wyszukiwanie swobodne tekstu.

## <a name="create-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Utwórz nowy pulpit nawigacyjny, wybierając menu rozwijane **pulpitu nawigacyjnego** . Możesz tworzyć i dodawać dowolną liczbę widżetów do pulpitu nawigacyjnego.

Niestandardowe pulpity nawigacyjne można tworzyć przy użyciu następujących opcji:

- Dodawanie widżetu do pulpitu nawigacyjnego

- Usuwanie widżetu z pulpitu nawigacyjnego

- Modyfikowanie filtru widżetu

- Zmień rozmiar widżetu

- Zmień lokalizację widżetu

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="Zmień lokalizację widżetu.":::

Aby utworzyć dostosowany pulpit nawigacyjny:

1. Wybierz pozycję **trendy i statystyka** w okienku po lewej stronie.

1. Wybierz menu rozwijane **Wybierz pulpit** nawigacyjny, a następnie wybierz przycisk **Utwórz pulpit nawigacyjny** .

1. Wprowadź opisową nazwę nowego pulpitu nawigacyjnego, a następnie wybierz pozycję **Utwórz**.

1. Wybierz pozycję **Dodaj widżet** w lewym górnym rogu strony.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Wybierz widżet z magazynu widżetów.":::

1. **Zabezpieczenia** i widżety **operacyjne** są dostępne w prawym górnym rogu okna. Wybieraj spośród różnych kategorii i protokołów. Krótki opis z miniaturą grafiki pojawia się z każdym widżetem. Użyj paska przewijania, aby wyświetlić wszystkie dostępne elementy widget.

1. Wybierz widżet przy użyciu przycisku **kliknij, aby dodać** . Widżet jest natychmiast wyświetlany na pulpicie nawigacyjnym.

Aby usunąć pulpit nawigacyjny:

1. Wybierz nazwę pulpitu nawigacyjnego z menu rozwijanego.

1. Wybierz ikonę **Usuń** , a następnie wybierz przycisk **OK**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Wybierz ikonę Usuń, aby usunąć pulpit nawigacyjny.":::

Aby edytować nazwę pulpitu nawigacyjnego:

1. Wybierz nazwę pulpitu nawigacyjnego z menu rozwijanego.

1. Wybierz ikonę **edycji** .
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Wybierz ikonę Edytuj, aby edytować nazwę pulpitu nawigacyjnego.":::

1. Wprowadź nową nazwę pulpitu nawigacyjnego, a następnie wybierz pozycję **Zapisz**.
 
Aby ustawić domyślny pulpit nawigacyjny:

1. Wybierz nazwę pulpitu nawigacyjnego z menu rozwijanego.

1. Wybierz ikonę **gwiazdki** , aby wybrać pulpit nawigacyjny, który ma zostać ustawiony jako domyślny pulpit nawigacyjny.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Wybierz ikonę gwiazdki, aby wybrać domyślny pulpit nawigacyjny."::: 

Aby zmodyfikować dane filtrowania w widżecie:

1. Wybierz ikonę **filtru** .

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Wybierz ikonę filtru, aby ustawić parametry dla widżetu.":::

1. Edytuj wymagane parametry.

1. Wybierz przycisk **OK**.

Aby usunąć widżet:

- Wybierz :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: ikonę.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Wybierz znak X, aby usunąć widżet.":::

## <a name="creating-widgets"></a>Tworzenie elementów widget 

Magazyn widżetów umożliwia wybranie elementów widget według kategorii i protokołu. Możesz wyświetlić **zabezpieczenia** lub dostępne Widżety **operacyjne** , wybierając je.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Wybierz widżet z magazynu widżetów.":::

Każdy widżet zawiera określone informacje o ruchu systemowym, statystykach sieci, statystykach protokołu, urządzeniu i informacjach o alercie. Wyświetlany jest komunikat, gdy nie ma danych dla widżetu.

Możesz usunąć sekcję z wykresu kołowego, aby zobaczyć względne znaczenie pozostałych wycinków. Wybierz nazwę wycinka w legendzie w dolnej części ekranu, aby to zrobić.

W poniższych sekcjach przedstawiono przykłady przypadków użycia dla niektórych elementów widget.

### <a name="busy-devices-widget"></a>Element widget zajętych urządzeń

Ten widżet zawiera listę pięciu pierwszych urządzeń najgorętszym. W trybie **edycji** można filtrować według znanych protokołów.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="Widok widżetu zajętego urządzenia.":::

### <a name="total-bandwidth-widget"></a>Widżet całkowitej przepustowości

Ten widżet śledzi przepustowość w MB/s (w megabitach na sekundę). Przepustowość jest wskazywana na osi y, z datą wyświetlaną na osi x. Tryb **edycji** umożliwia filtrowanie wyników zgodnie z klientem, serwerem, portem serwera lub podsiecią. Etykietka narzędzia jest wyświetlana po umieszczeniu kursora na grafie.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="Widok widżetu łącznej przepustowości.":::

### <a name="channels-bandwidth-widget"></a>Widżet przepustowość kanałów

Ten widżet wyświetla pięć pierwszych kanałów ruchu sieciowego. Można filtrować według adresu i ustawić liczbę prezentowanych wyników. Wybierz strzałkę w dół, aby wyświetlić więcej kanałów.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="Widok widżetu przepustowość kanałów.":::

### <a name="traffic-by-port-widget"></a>Element widget ruchu według portu

Ten widżet przedstawia ruch według portu, który jest wskazywany przez wykres kołowy z każdym portem wyznaczonym przez inny kolor. Wielkość ruchu w każdym porcie jest proporcjonalna do rozmiaru jego części wykresu kołowego.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="Widok widżetu ruch według portów.":::

### <a name="new-devices-widget"></a>Element widget nowych urządzeń

Ten widżet wyświetla wykres słupkowy nowych urządzeń, który wskazuje liczbę nowych urządzeń odnalezionych w określonym dniu.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="Widok widżetu nowe urządzenia.":::

### <a name="protocol-dissection-widgets"></a>Elementy widget sekcji protokołu

Ten widżet przedstawia wykres kołowy, który umożliwia przyjrzeć się ruchowi na protokół, bez pozostałego kodu funkcji i usług. Rozmiar każdego wycinka koła jest proporcjonalny do wielkości ruchu odnoszącego się do innych wycinków.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="Widok widżetu z sekcją protokołu.":::

### <a name="active-tcp-connections-widget"></a>Widżet aktywnych połączeń TCP

Ten widżet przedstawia wykres pokazujący liczbę aktywnych połączeń TCP w systemie.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Widok aktywnego elementu widget połączeń protokołu TCP.":::

### <a name="incident-by-type-widget"></a>Widżet zdarzenia według typu

Ten widżet przedstawia wykres kołowy pokazujący liczbę incydentów według typu. Jest to liczba alertów wygenerowanych przez każdy aparat w wstępnie zdefiniowanym okresie.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Widok widżetu zdarzenia według typu.":::

## <a name="devices-by-vendor-widget"></a>Widżet urządzenia według dostawcy

Ten widżet przedstawia wykres kołowy pokazujący liczbę urządzeń według dostawcy. Liczba urządzeń dla określonego dostawcy jest proporcjonalna do wielkości części dysku należącej do dostawcy urządzenia względem innych dostawców urządzeń.

## <a name="number-of-devices-per-vlan-widget"></a>Liczba urządzeń na element widget sieci VLAN

Ten widżet przedstawia wykres kołowy pokazujący liczbę odnalezionych urządzeń w sieci VLAN. Rozmiar każdego wycinka koła jest proporcjonalny do liczby odnalezionych urządzeń względem innych wycinków.

Każda sieć VLAN pojawia się ze znacznikiem VLAN przypisanym przez ręcznie dodany czujnik lub nazwę.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="Widok liczby elementów widget.":::

### <a name="top-bandwidth-by-vlan-widget"></a>Widżet najpopularniejsze przepustowości według sieci VLAN

Ten widżet przedstawia użycie przepustowości przez sieć VLAN. Domyślnie widżet pokazuje pięć sieci VLAN o najwyższej użyciu przepustowości.

Dane można filtrować według okresu przedstawionego w elemencie widget. Wybierz strzałkę w dół, aby wyświetlić więcej wyników.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="Widok widżetu najpopularniejsza przepustowość według sieci VLAN.":::

## <a name="system-report"></a>Raport systemowy

Aby pobrać Raport systemowy: 

1. Wybierz pozycję **trendy & dane statystyczne** w menu po stronie.

1. Wybierz pozycję **Raport systemowy** w prawym górnym rogu. Raport zostanie pobrany automatycznie.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Wybierz przycisk Raport systemowy, aby pobrać kopię raportu systemowego.":::

Raport systemowy jest plikiem PDF zawierającym wszystkie dane w systemie:

  - Urządzenia

  - Alerty

  - Informacje o zasadach sieciowych

## <a name="devices-in-a-system-report"></a>Urządzenia w raporcie systemowym 

Raport systemowy zawiera listę wszystkich urządzeń i ich informacje. Na przykład wpisz, nazwa i protokoły. Raport systemowy zawiera również listę urządzeń przypadających na dostawcę.

## <a name="alerts-in-system-report"></a>Alerty w raporcie systemowym 

Raport systemowy zawiera listę wszystkich alertów z informacjami, takimi jak Data i ważność.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="Widok alertów w raportach systemu.":::

## <a name="network-information-in-system-report"></a>Informacje o sieci w raporcie systemowym 

Raport systemowy zawiera szczegółowe informacje o sieci podstawowej. Na przykład kod funkcji DNP3 i otwarte porty dla każdego połączenia.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="Widok funkcji DNP3 z raportu system.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="Widok otwartego portu na połączenie.":::

## <a name="see-also"></a>Zobacz też

[Raportowanie](how-to-create-risk-assessment-reports.md) 
 oceny ryzyka Zapytania wyszukiwania danych [czujników](how-to-create-data-mining-queries.md) 
 [Raportowanie wektorów ataków](how-to-create-attack-vector-reports.md)
