---
title: Tworzenie zintegrowanych rozwiązań
description: Narzędzia i Partnerzy rozwiązań, które integrują się z Synapse puli SQL.
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
ms.openlocfilehash: 16cfdfb475ce21ed4b51dc9140e59df701363f27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201008"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool-data-warehouse"></a>Integrowanie innych usług za pomocą puli SQL Synapse (magazyn danych)

Możliwości puli SQL w usłudze Azure Synapse Analytics umożliwiają użytkownikom integrację z wieloma innymi usługami na platformie Azure. Korzystając z Synapse SQL, można utworzyć magazyn danych za pośrednictwem zasobu puli SQL, który może następnie korzystać z kilku dodatkowych usług, z których niektóre obejmują:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Usługa Azure Stream Analytics

Aby uzyskać więcej informacji na temat usług integracji na platformie Azure, zapoznaj się z artykułem [partnerzy integracji](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

Integracja Power BI umożliwia łączenie mocy obliczeniowej hurtowni danych z dynamicznym raportowaniem i wizualizacją Power BI. Obecnie integracja z usługą Power BI obejmuje:

* **Połączenie bezpośrednie**: bardziej zaawansowane połączenie z przekazywaniem logicznym względem magazynu danych obsługiwanego przy użyciu puli SQL. Przekazywanie zapewnia szybszą analizę na większą skalę.
* **Otwórz w Power BI**: przycisk "otwórz w Power BI" przekazuje informacje o wystąpieniu do Power BI w uproszczony sposób nawiązywania połączenia.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)lub [Dokumentacja Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory zapewnia użytkownikom zarządzaną platformę do tworzenia złożonej wyodrębniania i potoków ładowania. Integracja puli SQL z Azure Data Factory obejmuje:

* **Procedury składowane**: organizuje wykonywanie procedur składowanych.
* **Kopiowanie**: Użyj podajnika APD do przenoszenia danych do puli SQL. Ta operacja może korzystać ze standardowego mechanizmu przenoszenia danych ADF lub bazy w ramach okładek.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning to w pełni zarządzana usługa analizy, która umożliwia tworzenie modeli Intricate przy użyciu dużego zestawu narzędzi predykcyjnych. Pula SQL jest obsługiwana jako źródło i miejsce docelowe dla tych modeli i ma następujące funkcje:

* **Odczytaj dane:** Modele dysków na dużą skalę przy użyciu języka T-SQL względem puli SQL.
* **Zapisz dane:** Zatwierdź zmiany z dowolnego modelu z powrotem do puli SQL.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics

Azure Stream Analytics to złożona, w pełni zarządzana infrastruktura służąca do przetwarzania i zużywania danych zdarzeń generowanych z usługi Azure Event Hub.  Integracja z pulą SQL umożliwia wydajne przetwarzanie danych przesyłanych strumieniowo i przechowywanie ich wraz z danymi relacyjnymi, co pozwala na dokładniejszą i bardziej zaawansowaną analizę.  

* **Dane wyjściowe zadania:** Wyślij dane wyjściowe z zadań Stream Analytics bezpośrednio do puli SQL.

Aby uzyskać więcej informacji, zobacz [Integrowanie z Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
