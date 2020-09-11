---
title: Co to jest usługa Azure Synapse Analytics?
description: Omówienie usługi Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 653ad7e9b41e1c8a4708f2ab951676c144316192
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "90005858"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Co to jest usługa Azure Synapse Analytics (obszary robocze w wersji zapoznawczej)?

[!INCLUDE [preview](includes/note-preview.md)]

Obecnie analiza przedsiębiorstwa wymaga dużej ilości danych, zarówno w przypadku nieprzetworzonych, rafinowanych, jak i wysoce nadzorowanych. W przeszłości Tworzenie tych rodzajów rozwiązań analitycznych wymaga przedsiębiorstw do łączenia danych Big Data i magazynowania danych, takich jak Spark i SQL. Następnie musimy zintegrować je z rozbudowanymi potokami danych, które pracują z danymi w magazynach relacyjnych i jeziorach danych.  

Takie rozwiązania są trudne do kompilowania, konfigurowania, zabezpieczania i konserwowania, które opóźniają szybkie wydobywanie usługi inteligentnego wglądu w szczegółowe dane.

**Azure Synapse** to zintegrowana usługa analityczna, która przyspiesza czas uzyskiwania wglądu w dane ze wszystkich danych w dowolnej skali, między magazynami danych i systemami analizy danych Big Data. Łączy ona najlepsze zalety technologii **SQL** używanych w magazynie danych w przedsiębiorstwie, technologii **Spark** używanych w analizie danych Big Data oraz **potoków** do integracji danych i ETL/ELT.

Usługa Azure Synapse obejmuje środowisko użytkownika programu **Studio** natywne dla sieci Web, które zapewnia jedno środowisko i model zarządzania, monitorowania, kodowania i zabezpieczeń.

Usługa Azure Synapse zapewnia najprostszą i najszybszą metodę zbierania szczegółowych informacji na temat wszystkich danych w dowolnym rozmiarze przy użyciu analizy, z którą są najczęściej znane. Integruje się to z **Power BI** umożliwienia inżynierom danych tworzenia rozwiązań analitycznych, które działają kompleksowo w celu zapewnienia analizy biznesowej.

Ponadto usługa Azure Synapse ułatwia tworzenie modeli predykcyjnych i zaawansowaną analizę przy użyciu uczenia maszynowego za pośrednictwem wbudowanej obsługi platformy **Azure**.

## <a name="key-features--benefits"></a>Najważniejsze funkcje & korzyści

### <a name="industry-leading-sql"></a>Wiodące w branży SQL

* **Synapse SQL** to rozproszony system zapytań, który umożliwia przedsiębiorstwom wdrażanie scenariuszy magazynowania danych i wirtualizacji danych przy użyciu standardowych środowisk T-SQL, które są znane dla inżynierów danych. Rozszerza również możliwości programu SQL na potrzeby obsługi przesyłania strumieniowego i scenariuszy uczenia maszynowego.

* Program Synapse SQL oferuje **bezserwerowe** i **dedykowane** modele zasobów, oferując opcje zużycia i rozliczeń w celu dopasowania do Twoich potrzeb. Aby zapewnić przewidywalną wydajność i koszt, Utwórz dedykowane pule SQL, aby zarezerwować moc obliczeniową dla danych przechowywanych w tabelach SQL. W przypadku obciążeń nieplanowanych lub na rozerwanie należy użyć punktu końcowego SQL Always dostępnego serwera.
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

Usługa Azure Synapse jest wbudowana przy użyciu tego samego aparatu integracji danych i środowisk, co Azure Data Factory, co pozwala na tworzenie rozbudowanych potoków ETL na skalę bez opuszczania Synapse Analytics.

* Pozyskiwanie danych ze źródeł danych 90 +
* ETL bezpłatny kod z działaniami przepływu danych
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

## <a name="next-steps"></a>Następne kroki

* [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
* [Korzystanie z programu Synapse Studio](quickstart-synapse-studio.md)
* [Tworzenie puli SQL](quickstart-create-sql-pool-portal.md)
* [Korzystanie z bazy danych SQL na żądanie](quickstart-sql-on-demand.md)
* [Tworzenie puli Apache Spark](quickstart-create-apache-spark-pool-portal.md)
