---
title: Azure SQL Database dane wyjściowe z Azure Stream Analytics
description: W tym artykule opisano Azure SQL Database jako dane wyjściowe Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 631fdba451f69e44a675d396a42e1cddaea50a3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013961"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Azure SQL Database dane wyjściowe z Azure Stream Analytics

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) jako dane wyjściowe można użyć jako danych wyjściowych, które są relacyjne, lub dla aplikacji, które są zależne od zawartości hostowanej w relacyjnej bazie danych. Azure Stream Analytics zadania zapisu do istniejącej tabeli w SQL Database. Schemat tabeli musi dokładnie pasować do pól i ich typów w danych wyjściowych zadania. Możesz również określić [usługę Azure Synapse Analytics](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) jako dane wyjściowe za pośrednictwem opcji dane wyjściowe SQL Database. Aby dowiedzieć się więcej na temat sposobów zwiększania przepływności zapisu, zobacz artykuł [Stream Analytics z Azure SQL Database jako dane wyjściowe](stream-analytics-sql-output-perf.md) .

Możesz również użyć [wystąpienia zarządzanego usługi Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) jako danych wyjściowych. Należy [skonfigurować publiczny punkt końcowy w wystąpieniu zarządzanym SQL](../azure-sql/managed-instance/public-endpoint-configure.md) , a następnie ręcznie skonfigurować poniższe ustawienia w Azure Stream Analytics. Maszyna wirtualna platformy Azure z systemem SQL Server z dołączoną bazą danych jest również obsługiwana przez ręczne skonfigurowanie ustawień poniżej.

## <a name="output-configuration"></a>Konfiguracja wyjściowa

W poniższej tabeli wymieniono nazwy właściwości i ich opisy dotyczące tworzenia SQL Database danych wyjściowych.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tej bazy danych. |
| baza danych | Nazwa bazy danych, w której wysyłane są dane wyjściowe. |
| Nazwa serwera | Nazwa logicznego serwera SQL lub nazwa wystąpienia zarządzanego. W przypadku wystąpienia zarządzanego SQL wymagane jest określenie portu 3342. Na przykład *sampleserver. Public. Database. Windows. NET, 3342* |
| Nazwa użytkownika | Nazwa użytkownika, która ma dostęp do zapisu w bazie danych. Stream Analytics obsługuje tylko uwierzytelnianie SQL. |
| Hasło | Hasło, aby połączyć się z bazą danych. |
| Tabela | Nazwa tabeli, w której zapisano dane wyjściowe. W nazwie tabeli jest rozróżniana wielkość liter. Schemat tej tabeli powinien dokładnie pasować do liczby pól i ich typów generowanych przez dane wyjściowe zadania. |
|Dziedzicz schemat partycji| Opcja dziedziczenia schematu partycjonowania poprzedniego kroku zapytania, w celu włączenia w pełni równoległej topologii z wieloma składnikami zapisywania do tabeli. Aby uzyskać więcej informacji, zobacz [Azure Stream Analytics danych wyjściowych Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Maksymalna liczba partii| Zalecany górny limit liczby rekordów wysyłanych z każdą zbiorczą transakcją wstawiania.|

Istnieją dwie karty, które umożliwiają wyjście z Azure Stream Analytics usługi Azure Synapse Analytics: SQL Database i Azure Synapse. Zalecamy wybranie karty usługi Azure Synapse Analytics zamiast karty SQL Database, jeśli spełniony jest dowolny z następujących warunków:

* **Przepływność**: jeśli oczekiwana przepływność teraz lub w przyszłości jest większa niż 10 MB/s, użyj opcji danych wyjściowych usługi Azure Synapse, aby uzyskać lepszą wydajność.

* **Partycje wejściowe**: Jeśli masz osiem lub więcej partycji wejściowych, użyj opcji danych wyjściowych usługi Azure Synapse w celu lepszego skalowania w poziomie.

## <a name="partitioning"></a>Partycjonowanie

Partycjonowanie musi być włączone i opiera się na klauzuli PARTITION BY w zapytaniu. Gdy opcja dziedziczenia partycjonowania jest włączona, następuje Partycjonowanie danych wejściowych dla w [pełni działania równoległegoych zapytań](stream-analytics-scale-jobs.md). Aby dowiedzieć się więcej o osiągnięciu lepszej wydajności zapisu podczas ładowania danych do Azure SQL Database, zobacz [Azure Stream Analytics dane wyjściowe do Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="output-batch-size"></a>Rozmiar partii wyjściowej

Maksymalny rozmiar wiadomości można skonfigurować przy użyciu **maksymalnej liczby partii**. Domyślna wartość maksymalna to 10 000, a domyślna wartość minimalna to 100 wierszy na pojedynczy wkład zbiorczy. Aby uzyskać więcej informacji, zobacz [limity usługi Azure SQL](../azure-sql/database/resource-limits-logical-server.md). Każda partia jest początkowo wstawiana zbiorczo z maksymalną liczbą partii. Partia jest dzielona w połowie (do momentu minimalnej liczby partii) na podstawie błędów z możliwością powtarzania z bazy danych SQL.

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z tożsamości zarządzanych do uzyskiwania dostępu do Azure SQL Database lub analizy Synapse Azure z poziomu zadania Azure Stream Analytics (wersja zapoznawcza)](sql-database-output-managed-identity.md)
* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)