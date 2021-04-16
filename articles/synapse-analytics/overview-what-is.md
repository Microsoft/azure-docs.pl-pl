---
title: Co to jest usługa Azure Synapse Analytics?
description: Omówienie Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 03/24/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 652f98659f96b36e3185432e50d9d36dc569bd43
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537944"
---
# <a name="what-is-azure-synapse-analytics"></a>Co to jest usługa Azure Synapse Analytics?

**Azure Synapse** to usługa analizy przedsiębiorstwa, która skraca czas wglądu w magazyny danych i systemy danych big data. Azure Synapse łączy najlepsze technologie **SQL** używane w magazynowaniu danych przedsiębiorstwa, technologie **platformy Spark** używane dla danych big **data,** potoki integracji danych i ETL/ELT oraz głęboką integrację z innymi usługami platformy Azure, takimi jak **Power BI,** **CosmosDB** i **AzureML.**

![Diagram Azure Synapse Analytics architektury.](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>Wiodące w branży bazy danych SQL

**Synapse SQL** to rozproszony system zapytań dla języka T-SQL, który umożliwia magazynowanie danych i wirtualizację danych oraz rozszerza język T-SQL w celu adresowania scenariuszy przesyłania strumieniowego i uczenia maszynowego.

* Synapse SQL oferuje zarówno **modele zasobów bez serwera,** jak i **dedykowane.** Aby zapewnić przewidywalność wydajności i kosztów, utwórz dedykowane pule SQL, umożliwiające zarezerwowanie mocy obliczeniowej dla danych przechowywanych w tabelach SQL. W przypadku nieplanowanych lub dużych obciążeń należy używać zawsze dostępnego, bez serwera punktu końcowego SQL.
* Korzystanie z wbudowanych możliwości przesyłania **strumieniowego** w celu przechowywania danych ze źródeł danych w chmurze do tabel SQL
* Integrowanie AI z bazą danych SQL przy użyciu modeli **uczenia maszynowego** w celu oceny danych przy użyciu [funkcji PREDICT języka T-SQL](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="industry-standard-apache-spark"></a>Standardy branżowe Apache Spark

**Apache Spark for Azure Synapse** i bezproblemowo integruje Apache Spark — najpopularniejszy aparat danych big data open source używany do przygotowywania danych, inżynierii danych, ETL i uczenia maszynowego.

* Modele uczenia maszynowego z algorytmami SparkML i integracją z usługą AzureML dla platformy Apache Spark 2.4 z wbudowaną obsługą usługi Delta Lake w systemie Linux Foundation.
* Uproszczony model zasobów, który pozwala nie martwić się o zarządzanie klastrami.
* Szybkie uruchamianie platformy Spark i agresywne skalowanie automatyczne.
* Wbudowana obsługa platformy .NET dla platformy Spark, która umożliwia ponowne użycie wiedzy z zakresu języka C# i istniejącego kodu platformy .NET w aplikacji platformy Spark.

## <a name="working-with-your-data-lake"></a>Praca z urządzeniem Data Lake

Azure Synapse usuwa tradycyjne bariery technologiczne między używaniem razem języka SQL i platformy Spark. Możesz bezproblemowo mieszać i dorównać w zależności od potrzeb i wiedzy.

* Tabele zdefiniowane w plikach w data lake są bezproblemowo używane przez usługę Spark lub Hive.
* Program SQL i platforma Spark mogą bezpośrednio eksplorować i analizować pliki Parquet, CSV, TSV i JSON przechowywane w data lake.
* Szybkie, skalowalne ładowanie danych między bazami danych SQL i Spark

## <a name="built-in-data-integration"></a>Wbudowana integracja danych

Azure Synapse zawiera ten sam aparat integracji danych i środowiska co Azure Data Factory, co umożliwia tworzenie rozbudowanych potoków ETL na dużą skalę bez opuszczania Azure Synapse Analytics.

* Pozysuj dane z ponad 90 źródeł danych
* Code-Free ETL z działaniami przepływu danych
* Organizowanie notesów, zadań Platformy Spark, procedur składowanych, skryptów SQL i nie tylko

## <a name="unified-experience"></a>Ujednolicone środowisko 

**Synapse Studio** zapewnia przedsiębiorstwom jeden sposób tworzenia rozwiązań, konserwacji i zabezpieczania w jednym środowisku użytkownika

* Wykonywanie kluczowych zadań: pozyskaj, eksploruj, przygotuj, aranżuj, wizualizować
* Monitorowanie zasobów, użycia i użytkowników w programach SQL i Spark
* Korzystanie z kontroli dostępu opartej na rolach w celu uproszczenia dostępu do zasobów analitycznych
* Pisanie kodu SQL lub Spark i integracja z procesami integracji/cd przedsiębiorstwa

## <a name="engage-with-the-synapse-community"></a>Kontakt ze społecznością synapse

- [Microsoft Q&A:](/answers/topics/azure-synapse-analytics.html)Zadawanie pytań technicznych.
- [Stack Overflow:](https://stackoverflow.com/questions/tagged/azure-synapse)Zadawanie pytań programistów.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Synapse Analytics](get-started.md)
* [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
* [Korzystanie z bezserwerowej puli SQL](quickstart-sql-on-demand.md)
