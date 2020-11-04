---
title: Tworzenie zintegrowanych rozwiązań
description: Narzędzia i Partnerzy rozwiązań, które integrują się z dedykowaną pulą SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9f92128266c41912868f6ab74abaa2d374c6d236
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324493"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Zintegruj inne usługi z dedykowaną pulą SQL w usłudze Azure Synapse Analytics.

Funkcja dedykowanej puli SQL w ramach usługi Azure Synapse Analytics umożliwia użytkownikom integrację z wieloma innymi usługami na platformie Azure. Korzystając z Synapse SQL, można utworzyć magazyn danych za pośrednictwem dedykowanego zasobu puli SQL, który może następnie korzystać z kilku dodatkowych usług, z których niektóre obejmują:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Usługa Azure Stream Analytics

Aby uzyskać więcej informacji na temat usług integracji na platformie Azure, zapoznaj się z artykułem [partnerzy integracji](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

Integracja Power BI umożliwia łączenie mocy obliczeniowej hurtowni danych z dynamicznym raportowaniem i wizualizacją Power BI. Obecnie integracja z usługą Power BI obejmuje:

* **Połączenie bezpośrednie** : bardziej zaawansowane połączenie z przekazywaniem logicznym względem magazynu danych obsługiwanego za pomocą dedykowanej puli SQL. Przekazywanie zapewnia szybszą analizę na większą skalę.
* **Otwórz w Power BI** : przycisk "otwórz w Power BI" przekazuje informacje o wystąpieniu do Power BI w uproszczony sposób nawiązywania połączenia.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)lub [Dokumentacja Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory zapewnia użytkownikom zarządzaną platformę do tworzenia złożonej wyodrębniania i potoków ładowania. Integracja dedykowanej puli SQL z Azure Data Factory obejmuje:

* **Procedury składowane** : organizuje wykonywanie procedur składowanych.
* **Kopiowanie** : Użyj podajnika APD do przenoszenia danych do dedykowanej puli SQL. Ta operacja może korzystać ze standardowego mechanizmu przenoszenia danych ADF lub bazy w ramach okładek.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning to w pełni zarządzana usługa analizy, która umożliwia tworzenie modeli Intricate przy użyciu dużego zestawu narzędzi predykcyjnych. Dedykowana Pula SQL jest obsługiwana jako źródło i miejsce docelowe dla tych modeli i ma następujące funkcje:

* **Odczytaj dane:** Modele dysków na dużą skalę przy użyciu języka T-SQL względem dedykowanej puli SQL.
* **Zapisz dane:** Zatwierdź zmiany z dowolnego modelu z powrotem do dedykowanej puli SQL.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics

Azure Stream Analytics to złożona, w pełni zarządzana infrastruktura służąca do przetwarzania i zużywania danych zdarzeń generowanych z usługi Azure Event Hub.  Integracja z dedykowaną pulą SQL umożliwia wydajne przetwarzanie danych przesyłanych strumieniowo i przechowywanie ich wraz z danymi relacyjnymi, co pozwala na dokładniejszą i bardziej zaawansowaną analizę.  

* **Dane wyjściowe zadania:** Wysyłanie danych wyjściowych z zadań Stream Analytics bezpośrednio do dedykowanej puli SQL.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
