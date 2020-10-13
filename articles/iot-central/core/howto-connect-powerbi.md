---
title: Wizualizowanie danych usługi Azure IoT Central na pulpicie nawigacyjnym Power BI | Microsoft Docs
description: Użyj rozwiązania Power BI dla IoT Central platformy Azure, aby wizualizować i analizować dane IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: f996bb2d5126ef038ca872aee1f1893979a5229b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081002"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Wizualizowanie i analizowanie danych IoT Central platformy Azure na pulpicie nawigacyjnym Power BI

*Ten temat dotyczy administratorów i deweloperów rozwiązań.*

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Potok rozwiązania Power BI":::

Użyj rozwiązania Power BI dla platformy Azure IoT Central v3, aby utworzyć zaawansowany pulpit nawigacyjny Power BI do monitorowania wydajności urządzeń IoT. Na pulpicie nawigacyjnym Power BI można:

- Śledzenie ilości danych wysyłanych przez urządzenia z biegiem czasu
- Porównywanie woluminów danych między różnymi strumieniami telemetrii
- Filtrowanie w dół do danych wysyłanych przez określone urządzenia
- Wyświetlanie najnowszych danych telemetrycznych w tabeli

To rozwiązanie służy do konfigurowania potoku, który odczytuje dane z konta [usługi Azure Blob](howto-export-data-blob-storage.md) Storage. Potok używa Azure Functions, Azure Data Factory i Azure SQL Database, aby przetwarzać i przekształcać dane. Możesz wizualizować i analizować dane w raporcie Power BI pobranym jako plik PBIX. Wszystkie zasoby są tworzone w ramach subskrypcji platformy Azure, dzięki czemu można dostosować każdy składnik do swoich potrzeb.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz mieć aktywną subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Skonfigurowanie rozwiązania wymaga następujących zasobów:

- Aplikacja IoT Central w wersji 3. Aby dowiedzieć się, jak sprawdzić wersję aplikacji, zobacz [Informacje o aplikacji](./howto-get-app-info.md). Aby dowiedzieć się, jak utworzyć aplikację IoT Central, zobacz [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md).
- Ciągły eksport danych skonfigurowany do eksportowania telemetrii, urządzeń i szablonów urządzeń do usługi Azure Blob Storage. Aby dowiedzieć się więcej, zobacz [jak wyeksportować dane IoT do miejsc docelowych na platformie Azure](howto-export-data.md).
  - Upewnij się, że tylko aplikacja IoT Central eksportuje dane do kontenera obiektów BLOB.
  - [Urządzenia muszą wysyłać komunikaty kodowane w formacie JSON](../../iot-hub/iot-hub-devguide-messages-d2c.md). Urządzenia muszą określać `contentType:application/JSON` `contentEncoding:utf-8` `contentEncoding:utf-16` `contentEncoding:utf-32` Właściwości systemu komunikatów i lub lub.
- Power BI Desktop (Najnowsza wersja). Zobacz [Power BI pobierania](https://powerbi.microsoft.com/downloads/).
- Power BI Pro (Jeśli chcesz udostępnić pulpit nawigacyjny innym osobom).

> [!NOTE]
> Jeśli używasz aplikacji IoT Central w wersji 2, zobacz [Wizualizacja i analizowanie danych platformy Azure IoT Central na pulpicie nawigacyjnym Power BI](https://docs.microsoft.com/previous-versions/azure/iot-central/core/howto-connect-powerbi) w poprzedniej wersji witryny dokumentacji.

## <a name="install"></a>Instalowanie

Aby skonfigurować potok, przejdź do strony [Power BI rozwiązanie dla platformy Azure IoT Central v3](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) w witrynie **Microsoft AppSource** . Wybierz pozycję **Pobierz teraz**i postępuj zgodnie z instrukcjami.

Po otwarciu pliku PBIX upewnij się, że odczytywanie i postępuj zgodnie z instrukcjami na stronie tytułowej. W tych instrukcjach opisano sposób łączenia raportu z bazą danych SQL.

## <a name="report"></a>Raport

Plik PBIX zawiera raport **urządzenia i Telemetria** przedstawia widok historyczny danych telemetrycznych wysłanych przez urządzenia. Zawiera podział różnych typów telemetrii, a także przedstawia najnowsze dane telemetryczne wysyłane przez urządzenia.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Potok rozwiązania Power BI":::

## <a name="pipeline-resources"></a>Zasoby potoku

Możesz uzyskać dostęp do wszystkich zasobów platformy Azure, które tworzą potok w Azure Portal. Wszystkie zasoby znajdują się w grupie zasobów utworzonej podczas konfigurowania potoku.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="Potok rozwiązania Power BI":::

Na poniższej liście opisano role poszczególnych zasobów w potoku:

### <a name="azure-functions"></a>Azure Functions

Aplikacja funkcji platformy Azure jest wyzwalana za każdym razem, gdy IoT Central zapisuje nowy plik w usłudze BLOB Storage. Funkcje wyodrębniają dane z obiektów blob, urządzeń i szablonów urządzeń, aby wypełnić pośrednie tabele SQL używane przez Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory nawiązuje połączenie z SQL Database jako połączonej usługi. Uruchamia procedury składowane w celu przetworzenia danych i zapisania ich w tabelach analizy.

Azure Data Factory jest uruchamiany co 15 minut w celu przekształcenia najnowszej partii danych do załadowania do tabel SQL (czyli bieżącej minimalnej liczby dla **wyzwalacza okna wirowania**).

### <a name="azure-sql-database"></a>Azure SQL Database

Azure Data Factory generuje zestaw tabel analizy dla Power BI. Możesz eksplorować te schematy w Power BI i używać ich do tworzenia własnych wizualizacji.

## <a name="estimated-costs"></a>Szacowane koszty

[Power BI rozwiązanie dla platformy Azure IoT Central v3](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) w witrynie Microsoft AppSource zawiera link do szacowania kosztów dla wdrażanych zasobów.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wizualizować dane w Power BI, sugerowanym następnym krokiem jest zapoznanie się z [tematem zarządzanie urządzeniami](howto-manage-devices.md).
