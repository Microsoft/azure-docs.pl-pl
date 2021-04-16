---
title: Używanie Azure Stream Analytics w dedykowanej puli SQL
description: Porady dotyczące używania Azure Stream Analytics z dedykowaną pulą SQL w Azure Synapse tworzenia rozwiązań w czasie rzeczywistym.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 0c7f139b50cd43e3e8862fda3f5401a853ced8d0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566583"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Używanie Azure Stream Analytics z dedykowaną pulą SQL w programie Azure Synapse Analytics

Azure Stream Analytics to w pełni zarządzana usługa zapewniająca małe opóźnienia, wysoce dostępne, skalowalne, złożone przetwarzanie zdarzeń przesyłane strumieniowo w chmurze. Podstawowe informacje możesz poznać, czytając [wprowadzenie do Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Następnie możesz dowiedzieć się, jak utworzyć rozwiązanie end-to-end za pomocą usługi Stream Analytics, korzystając z samouczka [Rozpoczynanie](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) pracy Azure Stream Analytics samouczku.

W tym artykule dowiesz się, jak używać dedykowanej puli SQL jako ujścia danych wyjściowych do pozyskiwania danych o wysokiej przepływności za pomocą Azure Stream Analytics zadań.

## <a name="prerequisites"></a>Wymagania wstępne

* Azure Stream Analytics — aby utworzyć zadanie Azure Stream Analytics, wykonaj kroki opisane w samouczku Wprowadzenie [Azure Stream Analytics,](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) aby:  

    1. Tworzenie danych wejściowych centrum zdarzeń
    2. Konfigurowanie i uruchamianie aplikacji generatora zdarzeń
    3. Aprowizuj Stream Analytics zadań
    4. Określanie danych wejściowych zadania i zapytania
* Dedykowana pula SQL — aby utworzyć nową dedykowaną pulę SQL, wykonaj kroki opisane w przewodniku Szybki [start: tworzenie dedykowanej puli SQL.](../quickstart-create-sql-pool-portal.md)

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Określanie danych wyjściowych przesyłania strumieniowego, aby wskazać dedykowaną pulę SQL

### <a name="step-1"></a>Krok 1

W Azure Portal przejdź do zadania Stream Analytics i kliknij pozycję Dane wyjściowe **w** menu **Topologia** zadania.

### <a name="step-2"></a>Krok 2

Kliknij przycisk **Dodaj** i wybierz pozycję **Azure Synapse Analytics** z menu rozwijanego.

![Wybierz Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Krok 3

Podaj następujące wartości:

* *Alias danych wyjściowych:* wprowadź przyjazną nazwę dla danych wyjściowych zadania.
* *Subskrypcja:*
  * Jeśli dedykowana pula SQL znajduje się w tej samej subskrypcji co zadanie Stream Analytics, kliknij pozycję Wybierz Azure Synapse Analytics ***subskrypcji.***
  * Jeśli dedykowana pula SQL znajduje się w innej subskrypcji, kliknij pozycję Azure Synapse Analytics ręcznie.
* *Baza* danych: wybierz docelową bazę danych z listy rozwijanej.
* *Nazwa użytkownika:* określ nazwę użytkownika konta, które ma uprawnienia do zapisu dla bazy danych.
* *Hasło:* podaj hasło dla określonego konta użytkownika.
* *Tabela:* określ nazwę tabeli docelowej w bazie danych.
* kliknij przycisk **Zapisz**

![Ukończono Azure Synapse Analytics formularza](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Krok 4

Przed uruchomieniem testu należy utworzyć tabelę w dedykowanej puli SQL.  Uruchom poniższy skrypt tworzenia tabeli przy użyciu SQL Server Management Studio (SSMS) lub wybranego narzędzia do wykonywania zapytań.

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

Na Azure Portal zadania Stream Analytics kliknij nazwę zadania.  Kliknij przycisk ***Testuj** _ w okienku _ *_Szczegóły danych wyjściowych_**.

![Przycisk Testuj na stronie Outpout details (Szczegóły wy wychodzące) Gdy połączenie z bazą danych zakończy się pomyślnie, w portalu zostanie ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) wyświetlony komunikat.

### <a name="step-6"></a>Krok 6

Kliknij menu ***Zapytanie** _ w obszarze _*_Topologia zadania_*_ i zmień zapytanie, aby wstawić dane do utworzonych danych wyjściowych strumienia.  Kliknij przycisk _*_Testuj wybrane zapytanie,_*_ aby przetestować zapytanie.  Po *_pomyślnym przetestowaniu_* zapytania kliknij przycisk _Zapisz zapytanie *.

![Zapisywanie zapytania](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Krok 7

Uruchom Azure Stream Analytics zadania.  Kliknij przycisk ***Start** _ w menu _ *_Przegląd_**.

![Uruchamianie zadania usługi Stream Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Kliknij przycisk ***Start*** w okienku uruchamiania zadania.

![Kliknij przycisk Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Następne kroki

Omówienie integracji zawiera temat [Integrowanie innych usług.](sql-data-warehouse-overview-integrate.md)
Aby uzyskać więcej porad dotyczących programowania, zobacz Design decisions and coding techniques for dedicated SQL pool (Decyzje [projektowe i techniki kodowania dedykowanej puli SQL).](sql-data-warehouse-overview-develop.md)
