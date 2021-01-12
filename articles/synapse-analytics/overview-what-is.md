---
title: Co to jest usługa Azure Synapse Analytics?
description: Omówienie usługi Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/28/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: e5b12632a60ad5580325fbcda294e1a600bb2b6b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121706"
---
# <a name="what-is-azure-synapse-analytics"></a>Co to jest usługa Azure Synapse Analytics?

Analiza przedsiębiorstwa musi być wydajna na bardzo dużą skalę w przypadku dowolnego rodzaju danych, zarówno nieprzetworzony, rafinowany, jak i wysoce nadzorowany. Zwykle jest to konieczne, aby przedsiębiorstwa współpracowały z dużą ilością danych i technologiami magazynowania danych w złożonych potokach danych, które działają między danymi w magazynach relacyjnych i jeziorach danych. Te rodzaje rozwiązań są trudne do skompilowania, utrzymania i zabezpieczenia. Opóźnienia w zakresie złożoności zapewniające zapotrzebowanie na przedsiębiorstwa.

**Azure Synapse** to zintegrowana usługa analityczna, która przyspiesza czas w celu wglądu w dane między magazynami danych i systemami danych Big Data. Usługa Azure Synapse umożliwia łączenie najlepszych technologii **SQL** używanych w magazynach danych w przedsiębiorstwie, technologii **Spark** używanych w przypadku danych Big Data, **potoków** do integracji danych i ETL/ELT oraz głębokiej integracji z innymi usługami platformy Azure, takimi jak **Power BI**, **CosmosDB** i **Azure**.

## <a name="key-features--benefits"></a>Najważniejsze funkcje & korzyści

### <a name="industry-leading-sql"></a>Wiodące w branży SQL

* **Synapse SQL** to rozproszony system zapytań, który umożliwia przedsiębiorstwom wdrażanie scenariuszy magazynowania danych i wirtualizacji danych przy użyciu standardowych i znanych środowisk T-SQL. Rozszerza również możliwości programu SQL na potrzeby obsługi przesyłania strumieniowego i scenariuszy uczenia maszynowego.

* Program Synapse SQL oferuje **bezserwerowe** i **dedykowane** modele zasobów, oferując opcje zużycia i rozliczeń w celu dopasowania do Twoich potrzeb. Aby zapewnić przewidywalność wydajności i kosztów, utwórz dedykowane pule SQL, umożliwiające zarezerwowanie mocy obliczeniowej dla danych przechowywanych w tabelach SQL. W przypadku obciążeń nieplanowanych lub na rozerwanie należy użyć punktu końcowego SQL Always dostępnego serwera.
* Korzystanie z wbudowanych funkcji **przesyłania strumieniowego** w celu wywożenia danych ze źródeł danych w chmurze w tabelach SQL
* Integrowanie AI z programem SQL przy użyciu modeli **uczenia maszynowego** do oceny danych za pomocą [funkcji przewidywania T-SQL](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)

### <a name="industry-standard-apache-spark"></a>Apache Spark standardowa

**Apache Spark dla systemu Azure Synapse** głęboko i bezproblemowo integruje Apache Spark — najpopularniejszy aparat danych Big Data, który służy do przygotowywania danych, inżynierii danych, ETL i uczenia maszynowego.

* Modele ML z algorytmami SparkML i integracją usługi Azure dla Apache Spark 2,4 z wbudowaną obsługą systemu Linux Foundation w systemie.
* Uproszczony model zasobów, który uwalnia użytkownika od konieczności zarządzania klastrami.
* Szybka konfiguracja platformy Spark i agresywne Skalowanie automatyczne.
* Wbudowana obsługa programu .NET for Spark umożliwiająca ponowne użycie doświadczenia w języku C# i istniejącego kodu platformy .NET w aplikacji platformy Spark.

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Międzyoperacyjność SQL i Apache Spark na Data Lake

Usługa Azure Synapse usuwa tradycyjne bariery technologiczne między programami SQL i Spark. Możesz bezproblemowo mieszać i dopasowywać w zależności od potrzeb i wiedzy fachowej.

* Udostępniony system metadanych zgodny z platformą Hive umożliwia używanie tabel zdefiniowanych dla plików w usłudze Data Lake do bezproblemowego wykorzystania przez platformę Spark lub Hive.
* SQL i Spark mogą bezpośrednio eksplorować i analizować pliki Parquet, CSV, TSV i JSON przechowywane w usłudze Data Lake.
* Szybkie skalowalne obciążenie i zwalnianie danych przechodzących między bazami danych SQL i Spark

### <a name="built-in-data-integration-via-pipelines"></a>Wbudowana integracja danych za pośrednictwem potoków

Usługa Azure Synapse jest wbudowana przy użyciu tego samego aparatu integracji danych i środowisk Azure Data Factory, co pozwala na tworzenie rozbudowanych potoków ETL w skali, bez opuszczania usługi Azure Synapse Analytics.

* Pozyskiwanie danych ze źródeł danych 90 +
* Code-Free ETL z działaniami przepływu danych
* Organizowanie notesów, zadań platformy Spark, procedur składowanych, skryptów SQL i innych

### <a name="unified-management-monitoring-and-security"></a>Ujednolicone zarządzanie, monitorowanie i zabezpieczenia

Usługa Azure Synapse umożliwia przedsiębiorstwom zarządzanie zasobami analitycznymi, monitorowanie użycia i działania oraz Wymuszanie zabezpieczeń.

* Przypisywanie użytkowników do roli w celu uproszczenia dostępu do zasobów analitycznych
* Szczegółowa kontrola dostępu do danych i kodu
* Pojedynczy pulpit nawigacyjny do monitorowania zasobów, użycia i użytkowników w ramach programu SQL i platformy Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** to natywne środowisko sieci Web, które wiąże wszystko ze sobą w przypadku inżynierów danych, dzięki czemu można w jednej lokalizacji wykonać każde zadanie potrzebne do utworzenia kompletnego rozwiązania.

* Tworzenie kompleksowego rozwiązania do analizy w jednym miejscu: pozyskiwanie, eksplorowanie, przygotowywanie, organizowanie, wizualizowanie
* Wiodąca w branży wydajność inżynierów danych pisząc kod SQL lub Spark: Tworzenie, debugowanie i Optymalizacja wydajności
* Integracja z procesami ciągłej integracji/ciągłego wdrażania

## <a name="engage-with-the-synapse-engineering-team"></a>Zaangażuj się z zespołem inżynierów Synapse

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): zadawaj pytania dotyczące programowania.
- [Microsoft Q&stronie pytania](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html): zadawaj pytania techniczne.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Synapse Analytics](get-started.md)
* [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
* [Korzystanie z bezserwerowej puli SQL](quickstart-sql-on-demand.md)
