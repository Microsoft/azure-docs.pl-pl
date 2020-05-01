---
title: Co to jest Azure Databricks?
description: Dowiedz się więcej na temat Azure Databricks i sposobu, w jaki zapewnia ona platformę Spark na platformie Azure. Usługa Azure Databricks to platforma analizy oparta na usłudze Apache Spark zoptymalizowana pod kątem platformy usług w chmurze Microsoft Azure.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: overview
ms.date: 04/10/2020
ms.author: mamccrea
ms.custom: mvc
ms.openlocfilehash: 902486f7e19f2dfd7cc64e27589e192c57ef64e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81255519"
---
# <a name="what-is-azure-databricks"></a>Co to jest Azure Databricks?

Usługa Azure Databricks to platforma analizy oparta na usłudze Apache Spark zoptymalizowana pod kątem platformy usług w chmurze Microsoft Azure. Usługa Databricks, zaprojektowana wspólnie z twórcami usługi Apache Spark, jest zintegrowana z platformą Azure w celu zapewnienia konfigurowania jednym kliknięciem, usprawnionych przepływów pracy oraz interakcyjnego obszaru roboczego, który umożliwia współpracę między analitykami danych, inżynierami danych i analitykami biznesowymi.

![Co to jest Azure Databricks?](./media/what-is-azure-databricks/azure-databricks-overview.png "Co to jest Azure Databricks?")

Azure Databricks to szybka i łatwa w obsłudze usługa analityczna do pracy zespołowej bazująca na usłudze Apache Spark. W przypadku potoku danych big data dane (nieprzetworzone lub ze strukturą) są pozyskiwane do platformy Azure za pośrednictwem usługi Azure Data Factory w partiach lub przesyłane strumieniowo prawie w czasie rzeczywistym przy użyciu platformy Kafka, Centrum zdarzeń lub usługi IoT Hub. Te dane trafiają do magazynu typu data lake w celu długoterminowego trwałego przechowywania w usłudze Azure Blob Storage lub usłudze Azure Data Lake Storage. W ramach przepływu pracy analizy użyj usługi Azure Databricks, aby odczytać dane z wielu źródeł danych, takich jak [usługa Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), [usługa Azure Data Lake Storage](../data-lake-store/index.yml), [usługa Azure Cosmos DB](../cosmos-db/index.yml) lub [usługa Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/index.yml), i przekształcić je w przełomowe szczegółowe informacje przy użyciu platformy Spark.

![Potok usługi Databricks](./media/what-is-azure-databricks/databricks-pipeline.png)

## <a name="apache-spark-based-analytics-platform"></a>Platforma analityczna bazująca na projekcie Apache Spark

Usługa Azure Databricks zapewnia pełny zestaw możliwości i technologii klastra Apache Spark typu open source. Platforma Spark w usłudze Azure Databricks obejmuje następujące składniki:

![Platforma Apache Spark w usłudze Azure Databricks](./media/what-is-azure-databricks/apache-spark-ecosystem-databricks.png "Platforma Apache Spark w usłudze Azure Databricks")

* **Spark SQL i elementy DataFrame**: Spark SQL to moduł platformy Spark do pracy z danymi strukturalnymi. Element DataFrame jest rozproszoną kolekcją danych zorganizowanych w nazwanych kolumnach. Jest równoważny tabeli w relacyjnej bazie danych lub ramce danych w języku R/Python.

* **Przesyłanie strumieniowe**: przetwarzanie i analiza danych w czasie rzeczywistym dla aplikacji analitycznych i interakcyjnych. Integruje się z rozwiązaniami HDFS, Flume i Kafka.

* **MLlib**: Biblioteka Machine Learning składająca się z typowych algorytmów uczenia i narzędzi, takich jak Klasyfikacja, regresja, klastrowanie, filtrowanie i współdziałanie, zmniejszenie liczby wymiarów, a także podstawowe elementy pierwotne optymalizacji.

* **GraphX**: wykresy i obliczenia dotyczące wykresów do szerokiego zakresu zastosowań, od analizy poznawczej po eksplorację danych.

* **Interfejs API Spark Core**: zapewnia obsługę języków R, SQL, Python, Scala i Java.

## <a name="apache-spark-in-azure-databricks"></a>Platforma Apache Spark w usłudze Azure Databricks

Usługa Azure Databricks bazuje na możliwościach platformy Spark, zapewniając platformę chmurową, która nie wymaga zarządzania i obejmuje następujące elementy:

- W pełni zarządzane klastry Spark
- Interakcyjny obszar roboczy do eksploracji i wizualizacji
- Platforma do obsługi ulubionych aplikacji opartych na platformie Spark

### <a name="fully-managed-apache-spark-clusters-in-the-cloud"></a>W pełni zarządzane klastry Apache Spark w chmurze

Usługa Azure Databricks zapewnia bezpieczne i niezawodne środowisko produkcyjne w chmurze, zarządzane i obsługiwane przez ekspertów ds. platformy Spark. Można:

* Tworzyć klastry w ciągu sekund.
* Dynamicznie automatycznie skalować klastry w górę i w dół, w tym klastry bezserwerowe, i udostępniać je między zespołami. 
* Korzystać z klastrów programowo przy użyciu interfejsów API REST. 
* Korzystać z bezpiecznych funkcji integracji danych opartych na platformie Spark, które umożliwiają ujednolicanie danych bez centralizacji. 
* Uzyskiwać natychmiastowy dostęp do najnowszych funkcji platformy Apache Spark dodawanych w każdej wersji.

### <a name="databricks-runtime"></a>Środowisko uruchomieniowe usługi Databricks
Środowisko uruchomieniowe usługi Databricks bazuje na platformie Apache Spark i zostało natywnie zaprojektowane pod kątem chmury platformy Azure. 

Dzięki opcji **bezserwerowej** usługa Azure Databricks całkowicie eliminuje złożoność infrastruktury i wymaganie posiadania wiedzy eksperckiej w celu instalowania i konfigurowania infrastruktury danych. Opcja bezserwerowa ułatwia analitykom danych realizację szybkich cyklów pracy w zespole.

Inżynierom danych, których interesuje wydajność zadań produkcyjnych, usługa Azure Databricks zapewnia aparat platformy Spark, który jest szybszy i wydajniejszy dzięki różnym optymalizacjom warstwy wejścia/wyjścia i warstwy przetwarzania (wejście/wyjście usługi Databricks).

### <a name="workspace-for-collaboration"></a>Obszar roboczy współpracy

Dzięki zoptymalizowanemu pod kątem współpracy i zintegrowanemu środowisku usługa Azure Databricks upraszcza proces eksplorowania danych, tworzenia prototypów i uruchamiania aplikacji opartych na danych na platformie Spark.

* Ustalanie sposobu używania danych dzięki łatwej eksploracji danych.
* Dokumentowanie postępu w notesach w językach R, Python, Scala lub SQL.
* Wizualizowanie danych za pomocą kilku kliknięć i korzystanie ze znanych narzędzi, takich jak Matplotlib, ggplot lub d3.
* Tworzenie dynamicznych raportów za pomocą interakcyjnych pulpitów nawigacyjnych.
* Korzystanie z platformy Spark w celu obsługi jednoczesnych interakcji z danymi.

## <a name="enterprise-security"></a>Zabezpieczenia przedsiębiorstwa

Usługa Azure Databricks zapewnia zabezpieczenia klasy korporacyjnej platformy Azure, w tym integrację z usługą Azure Active Directory, kontrolę opartą na rolach oraz umowy SLA, które zapewniają ochronę danych i firmy.

* Integracja z usługą Azure Active Directory umożliwia uruchamianie przy użyciu usługi Azure Databricks kompletnych rozwiązań bazujących na platformie Azure.
* Dostęp oparty na rolach usługi Azure Databricks umożliwia określanie szczegółowych uprawnień do notesów, klastrów, zadań i danych.
* Umowy SLA klasy korporacyjnej. 

> [!IMPORTANT]
>
> Azure Databricks to Microsoft Azure usługa pierwszej firmy, która jest wdrażana w globalnej infrastrukturze chmury publicznej platformy Azure. Cała komunikacja między składnikami usługi, w tym między publicznymi adresami IP w płaszczyźnie kontroli a płaszczyzną danych klienta, pozostaje w obrębie sieci szkieletowej Microsoft Azure. Zobacz również [Microsoft Global Network](https://docs.microsoft.com/azure/networking/microsoft-global-network).


## <a name="integration-with-azure-services"></a>Integracja z usługami Azure

Usługa Azure Databricks jest ściśle zintegrowana z bazami danych i magazynami platformy Azure: SQL Data Warehouse, Cosmos DB, Data Lake Store i Blob Storage. 

## <a name="integration-with-power-bi"></a>Integracja z usługą Power BI
Dzięki rozbudowanej integracji z usługą Power BI usługa Azure Databricks umożliwia szybkie i łatwe odnajdywanie i udostępnianie istotnych wyników analiz. Można też używać innych narzędzi do analizy biznesowej, takich jak oprogramowanie Tableau — za pośrednictwem punktów końcowych klastra JDBC/ODBC.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: uruchamianie zadania Spark w usłudze Azure Databricks](quickstart-create-databricks-workspace-portal.md)
* [Praca z klastrami Spark](/azure/databricks/clusters/index)
* [Praca z notesami](/azure/databricks/notebooks/index)
* [Tworzenie zadań Spark](/azure/databricks/jobs)

 









