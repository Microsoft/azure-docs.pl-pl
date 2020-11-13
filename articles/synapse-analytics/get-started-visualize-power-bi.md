---
title: 'Samouczek: Rozpoczynanie pracy z usługą Azure Synapse Analytics — Wizualizacja danych obszaru roboczego za pomocą Power BI'
description: W tym samouczku dowiesz się, jak utworzyć obszar roboczy Power BI, połączyć obszar roboczy usługi Azure Synapse i utworzyć zestaw danych Power BI, który wykorzystuje dane w obszarze roboczym usługi Azure Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 64a87df4e4fea9fb29e787ab3420f1a62f41323d
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592417"
---
# <a name="visualize-data-with-power-bi"></a>Wizualizacja danych przy użyciu usługi Power BI

W tym samouczku dowiesz się, jak utworzyć obszar roboczy Power BI, połączyć obszar roboczy usługi Azure Synapse i utworzyć zestaw danych Power BI, który wykorzystuje dane w obszarze roboczym usługi Azure Synapse. 

## <a name="overview"></a>Omówienie

Na podstawie danych z NYC taksówkami zostały utworzone zagregowane zestawy datadataset w dwóch tabelach:
- **nyctaxi.passengercountstats**
- **SQLDB1. dbo. PassengerCountStats**

Obszar roboczy Power BI można połączyć z obszarem roboczym usługi Azure Synapse. Ta funkcja umożliwia łatwe uzyskiwanie danych do obszaru roboczego Power BI. Raporty dotyczące Power BI można edytować bezpośrednio w obszarze roboczym usługi Azure Synapse.

### <a name="create-a-power-bi-workspace"></a>Tworzenie obszaru roboczego Power BI

1. Zaloguj się do [PowerBI.Microsoft.com](https://powerbi.microsoft.com/).
1. Utwórz nowy obszar roboczy Power BI o nazwie **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Połącz obszar roboczy usługi Azure Synapse z nowym obszarem roboczym Power BI

1. W programie Synapse Studio przejdź do pozycji **Zarządzaj**  >  **połączonymi usługami**.
1. Wybierz pozycję **Nowy**  >  **Połącz z Power BI**
1. Ustaw **nazwę** na **NYCTaxiWorkspace1**
1. Ustaw **nazwę obszaru roboczego** na **NYCTaxiWorkspace1**
1. Wybierz pozycję **Utwórz**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Tworzenie Power BI zestawu danych, który używa danych w obszarze roboczym usługi Azure Synapse

1. W programie Synapse Studio przejdź do pozycji **opracowywanie**  >  **Power BI**.
1. Przejdź do **NYCTaxiWorkspace1**  >  **Power BI zestawy** danych i wybierz pozycję **Nowy Power BI zestaw danych**.
1. Umieść kursor nad bazą danych **SQLDB1** i wybierz pozycję **Pobierz plik pbids**.
1. Otwórz pobrany plik **pbids** . Zostanie otwarty Power BI pulpitu i automatycznie nawiąże połączenie z usługą **SQLDB1** w obszarze roboczym usługi Azure Synapse.
1. Jeśli zostanie wyświetlone okno dialogowe o nazwie **baza danych programu SQL Server** :
    1. Wybierz **konto Microsoft**.
    1. Wybierz pozycję **Zaloguj** się i zaloguj się na swoim koncie.
    1. Wybierz pozycję **Połącz**.
1. Po otwarciu okna dialogowego **nawigatora** sprawdź tabelę **PassengerCountStats** , a następnie wybierz pozycję **Load (ładowanie** ).
1. Po wyświetleniu okna dialogowego **Ustawienia połączenia** wybierz pozycję **zapytanie** bezpośrednie  >  **OK**.
1. Wybierz przycisk **raportu** po lewej stronie.
1. Dodaj **Wykres liniowy** do raportu.
    1. Przeciągnij kolumnę **PassengerCount** do osi **wizualizacje**  >  **Axis**.
    1. Przeciągnij kolumny **SumTripDistance** i **AvgTripDistance** na wartości **wizualizacje**  >  **Values**.
1. Na karcie **Narzędzia główne** wybierz pozycję **Publikuj**.
1. Wybierz przycisk **Zapisz** , aby zapisać zmiany.
1. Wybierz nazwę pliku **PassengerAnalysis. pbix** , a następnie wybierz pozycję **Zapisz**.
1. W obszarze **Wybierz lokalizację docelową** wybierz pozycję **NYCTaxiWorkspace1** , a następnie kliknij pozycję **Wybierz**.
1. Zaczekaj na zakończenie publikowania.

### <a name="configure-authentication-for-your-dataset"></a>Konfigurowanie uwierzytelniania dla zestawu danych

1. Otwórz [PowerBI.Microsoft.com](https://powerbi.microsoft.com/) i **Zaloguj się**.
1. Po lewej stronie, w obszarze **obszary robocze** , wybierz obszar roboczy **NYCTaxiWorkspace1** .
1. W tym obszarze roboczym Znajdź zestaw danych o nazwie **Analiza pasażera** i raport o nazwie **Analiza pasażera**.
1. Zatrzymaj wskaźnik myszy nad zestawem danych **PassengerAnalysis** , wybierz przycisk wielokropka (...), a następnie wybierz pozycję **Ustawienia**.
1. W polu **poświadczenia źródła danych** Ustaw **metodę uwierzytelniania** na **OAuth2** , a następnie wybierz pozycję **Zaloguj**.

### <a name="edit-a-report-in-synapse-studio"></a>Edytowanie raportu w programie Synapse Studio

1. Wróć do Synapse Studio i wybierz pozycję **Zamknij i Odśwież**.
1. Przejdź do centrum **opracowywania** .
1. Umieść kursor nad **Power BI** , a następnie wybierz węzeł Odśwież **Power BI raporty** .
1. W obszarze **Power BI** powinna zostać wyświetlona:
    * W obszarze **NYCTaxiWorkspace1**  >  **Power BI zestawy** danych nowy element dataset o nazwie **PassengerAnalysis**.
    * W obszarze **NYCTaxiWorkspace1**  >  **Power BI raporty** nowy raport o nazwie **PassengerAnalysis**.
1. Wybierz raport **PassengerAnalysis** . Raport zostanie otwarty i będzie można go edytować bezpośrednio w programie Synapse Studio.

## <a name="monitor-activities"></a>Monitorowanie działań

1. W programie Synapse Studio przejdź do centrum **monitora** .
1. W tej lokalizacji można zobaczyć historię wszystkich działań podejmowanych w obszarze roboczym, które są teraz aktywne.
1. Eksploruj **przebiegi potokowe** , **Apache Spark aplikacje** i **żądania SQL** , aby zobaczyć, co zostało już zrobione w obszarze roboczym.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Monitorowanie](get-started-monitor.md)
                                 

