---
title: Wizualizowanie danych monitorowania zdalnego przy użyciu Power BI na platformie Azure | Microsoft Docs
description: Ten samouczek używa Power BI Desktop i Cosmos DB do integrowania danych z rozwiązania do zdalnego monitorowania w dostosowanej wizualizacji. Dzięki temu użytkownicy mogą tworzyć własne niestandardowe pulpity nawigacyjne i udostępniać je użytkownikom, którzy nie znajdują się w rozwiązaniu.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: ba669c86c406959a3f92949e5f806fa011527d47
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070915"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Wizualizowanie danych monitorowania zdalnego przy użyciu Power BI

W tym samouczku przedstawiono sposób podłączenia danych rozwiązania do monitorowania zdalnego z CosmosDB do Power BI. Po ustanowieniu tego połączenia można utworzyć własne niestandardowe pulpity nawigacyjne i dodać je ponownie na pulpicie nawigacyjnym rozwiązania do monitorowania zdalnego. Ta workstream umożliwia tworzenie bardziej wyspecjalizowanych wykresów oprócz tych, które są używane przez program. Korzystając z tego samouczka, można przeprowadzić integrację z innymi strumieniami danych lub utworzyć niestandardowe pulpity nawigacyjne, które będą używane poza rozwiązaniem do zdalnego monitorowania. Tworzenie pulpitów nawigacyjnych w Power BI oznacza, że każdy panel może współistnieć ze sobą w miarę zaznaczania konkretnych fragmentów. Na przykład możesz mieć filtr, który pokazuje tylko informacje o symulowanych samochodach, a każda z nich będzie mogła korzystać z informacji o symulowanym wózku. Jeśli chcesz użyć narzędzia innego niż Power BI, możesz również przetworzyć te kroki, aby użyć narzędzia do wizualizacji wybranych i podpiąć do bazy danych Cosmos lub niestandardowej bazy danych, jeśli zostały one skonfigurowane. 

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć obecnie uruchomione rozwiązanie do monitorowania zdalnego
- Musisz mieć dostęp do [Azure Portal](https://portal.azure.com) i Twojej subskrypcji, w której jest uruchomione IoT Hub i rozwiązanie
- Musisz mieć zainstalowany program [Power BI Desktop](https://powerbi.microsoft.com) , wszystkie wersje


## <a name="information-needed-from-azure-portal"></a>Informacje wymagające Azure Portal

1. W razie potrzeby przejdź do [Azure Portal](https://portal.azure.com) i zaloguj się

2. Na panelu po lewej stronie kliknij pozycję grupy zasobów.

    ![Nawigacja panelu bocznego](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Przejdź do grupy zasobów, w której działa Twoje rozwiązanie IoT, a następnie kliknij przycisk, który ma zostać przeniesiony na stronę omówienia tej grupy zasobów. 

4. Na tej stronie Przegląd kliknij element, który ma typ "Azure Cosmos DB konto", a następnie przejdziesz do strony Przegląd strumienia Cosmos DB dla tego rozwiązania IoT.

    ![Resource Group](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Na panelu po lewej stronie kliknij sekcję "klucze" i zanotuj następujące wartości, które mają być używane w Power BI:

   - URI
   - Klucz podstawowy

     ![Zrzut ekranu przedstawia konto usługi Azure Cosmos D B z wybranym elementem Keys.](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Konfigurowanie strumienia w Power BI
  
1. Otwórz aplikację klasyczną Power BI, a następnie kliknij pozycję "Pobierz dane" w lewym górnym rogu. 

    ![Pobieranie danych](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Po wyświetleniu monitu o wprowadzenie danych wybierz opcję Wyszukaj ciąg "Azure Cosmos DB" i wybierz ten łącznik. Ten łącznik zasadniczo pobiera dane bezpośrednio z bazy danych Cosmos rozwiązania Azure IoT
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Wprowadź informacje, które zostały zarejestrowane powyżej:

    * URI
    * Klucz podstawowy

4. Zaznacz wszystkie tabele, które mają zostać zaimportowane do Power BI. Ta akcja spowoduje rozpoczęcie ładowania danych. Im dłużej działa Twoje rozwiązanie, tym dłużej może minąć potrzeba załadowania danych (do kilku godzin). 

    ![Importuj tabele](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Po zakończeniu ładowania danych kliknij pozycję "Edytuj zapytania" w górnym wierszu Power BI i rozwiń wszystkie tabele, klikając strzałki na żółtym pasku dla każdej tabeli. Zostanie to zasadniczo rozwinięte, aby pokazać wszystkie kolumny. Zobaczysz, jak dane dotyczące elementów, takich jak wilgotność, czas przyspieszenia itp., są nieprawidłowe.

    ![Nowa kolumna](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Na przykład dane wprowadzane do Power BI zostały zmienione na czas systemu UNIX, gdy został on dostarczony przez łącznik. Aby dostosować tę konwersję, przechodząc do przodu można utworzyć nową kolumnę i użyć tego równania w celu uzyskania formatu daty i godziny: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Zaktualizowana tabela](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document. Device. msg. Receives to tylko jedna z kolumn z formatowaniem w systemie UNIX i może zostać zastąpiona przez inne osoby, które wymagają konwersji. 
  
    Inne punkty danych zostały przekonwertowane na ciąg typu można zmienić na podwójne lub int, tam gdzie jest to odpowiednie, przy użyciu tych samych kroków jak powyżej.

## <a name="creating-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Gdy strumień zostanie połączony, możesz utworzyć spersonalizowane pulpity nawigacyjne. Poniższy pulpit nawigacyjny to przykład przejmowania danych telemetrycznych przez nasze symulowane urządzenia i pokazujący różne przedziały, takie jak: 

* Lokalizacja urządzenia na mapie (z prawej)
* Urządzenia o ich stanie i ważności. (z lewej u góry)
* Urządzenia z obowiązującymi regułami oraz ewentualne alerty, które wyłączają się dla nich (po lewej stronie)

![Wizualizacja usługi Power BI](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publikowanie pulpitu nawigacyjnego i odświeżanie danych

Po pomyślnym utworzeniu pulpitów nawigacyjnych zalecamy [opublikowanie pulpitów nawigacyjnych Power BI](/power-bi/desktop-upload-desktop-files) , aby udostępnić je innym osobom.

Należy również [odświeżyć dane](/power-bi/refresh-data) na opublikowanym pulpicie nawigacyjnym, aby upewnić się, że masz najnowszy zestaw danych.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób wizualizacji danych monitorowania zdalnego przy użyciu Power BI

Aby uzyskać więcej informacji na temat dostosowywania rozwiązania do monitorowania zdalnego, zobacz:

* [Dostosowywanie interfejsu użytkownika rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize.md)
* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)