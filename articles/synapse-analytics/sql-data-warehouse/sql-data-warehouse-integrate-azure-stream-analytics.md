---
title: Używanie Azure Stream Analytics w dedykowanej puli SQL
description: Porady dotyczące używania Azure Stream Analytics z dedykowaną pulą SQL w usłudze Azure Synapse na potrzeby tworzenia rozwiązań w czasie rzeczywistym.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 8fbe546beb1004214e544f8eb160884c0f64ef9e
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "96458219"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Używanie Azure Stream Analytics z dedykowaną pulą SQL w usłudze Azure Synapse Analytics

Azure Stream Analytics to w pełni zarządzana usługa dostarczająca wysoce opóźnienia i skalowalne, skomplikowane przetwarzanie złożone przetwarzania zdarzeń na danych przesyłanych strumieniowo w chmurze. Podstawowe informacje można uzyskać, odczytując [wprowadzenie do Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Następnie można dowiedzieć się, jak utworzyć kompleksowe rozwiązanie przy użyciu Stream Analytics, postępując zgodnie z samouczkiem [Rozpoczynanie pracy przy użyciu Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

W tym artykule dowiesz się, jak korzystać z dedykowanej puli SQL jako ujścia danych wyjściowych w celu pozyskiwania dużych przepływności przy użyciu Azure Stream Analytics zadań.

## <a name="prerequisites"></a>Wymagania wstępne

* Azure Stream Analytics zadanie — aby utworzyć zadanie Azure Stream Analytics, wykonaj kroki opisane w samouczku [wprowadzenie do korzystania z Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) , aby:  

    1. Tworzenie danych wejściowych centrum zdarzeń
    2. Konfigurowanie i uruchamianie aplikacji generatora zdarzeń
    3. Inicjowanie obsługi zadania Stream Analytics
    4. Określ dane wejściowe i zapytania dotyczące zadania
* Dedykowana Pula SQL — Aby utworzyć nową dedykowaną pulę SQL, wykonaj kroki opisane w [przewodniku szybki start: Tworzenie dedykowanej puli SQL](../quickstart-create-sql-pool-portal.md).

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Określ dane wyjściowe przesyłania strumieniowego, aby wskazywały dedykowaną pulę SQL

### <a name="step-1"></a>Krok 1

W Azure Portal przejdź do zadania Stream Analytics, a następnie kliknij pozycję dane **wyjściowe** w menu **topologia zadania** .

### <a name="step-2"></a>Krok 2

Kliknij przycisk **Dodaj** , a następnie wybierz pozycję **Azure Synapse Analytics** z menu rozwijanego.

![Wybierz pozycję Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Krok 3

Podaj następujące wartości:

* *Alias wyjściowy*: wprowadź przyjazną nazwę dla tego wyjścia zadania.
* *Subskrypcja*:
  * Jeśli dedykowana Pula SQL znajduje się w tej samej subskrypcji co zadanie Stream Analytics, kliknij pozycję ***Wybierz pozycję Azure Synapse Analytics z subskrypcji** _.
  Jeśli dedykowana Pula SQL znajduje się w innej subskrypcji, kliknij pozycję Podaj ręcznie ustawienia usługi Azure Synapse Analytics.
* *Baza danych*: wybierz docelową bazę danych z listy rozwijanej.
* *Nazwa użytkownika*: Określ nazwę użytkownika konta, które ma uprawnienia do zapisu w bazie danych.
* *Hasło*: Podaj hasło do określonego konta użytkownika.
* *Tabela*: Określ nazwę docelowej tabeli w bazie danych.
* Kliknij przycisk **Zapisz**

![Ukończony formularz analizy usługi Azure Synapse](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Krok 4

Aby można było uruchomić test, należy utworzyć tabelę w dedykowanej puli SQL.  Uruchom następujący skrypt tworzenia tabeli, używając programu SQL Server Management Studio (SSMS) lub wybranego narzędzia do wykonywania zapytań.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Krok 5

Na Azure Portal zadania Stream Analytics kliknij nazwę zadania.  Kliknij przycisk **_test_* _ w okienku _*_Szczegóły danych wyjściowych_*_ .

![Przycisk Testuj na Outpout szczegóły ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) po pomyślnym nawiązaniu połączenia z bazą danych w portalu zostanie wyświetlone powiadomienie.

### <a name="step-6"></a>Krok 6

Kliknij menu _*_zapytania_*_ w obszarze _*_topologia zadania_*_ i Zmień zapytanie, aby wstawić dane do utworzonego wyjścia strumienia.  Kliknij przycisk _*_Testuj wybrane zapytanie_*_ , aby przetestować zapytanie.  Kliknij przycisk _*_Zapisz zapytanie_*_ , gdy test zapytania zakończy się pomyślnie.

![Zapisz zapytanie](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Krok 7

Uruchom zadanie Azure Stream Analytics.  Kliknij przycisk _*_Start_*_ w menu _*_Przegląd_*_ .

![Uruchamianie zadania usługi Stream Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Kliknij przycisk _ *_Rozpocznij_** w okienku Rozpocznij zadanie.

![Kliknij przycisk Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem integracji, zobacz [integrowanie innych usług](sql-data-warehouse-overview-integrate.md).
Aby uzyskać więcej porad programistycznych, zobacz [decyzje projektowe i techniki kodowania dla dedykowanej puli SQL](sql-data-warehouse-overview-develop.md).
