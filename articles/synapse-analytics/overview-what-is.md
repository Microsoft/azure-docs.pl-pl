---
title: Co to jest usługa Azure Synapse Analytics?
description: Omówienie usługi Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 65f6c6627093358f8cbc66055bb9b16561f7c610
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424664"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Co to jest usługa Azure Synapse Analytics (wersja zapoznawcza obszarów roboczych)?

[!INCLUDE [preview](includes/note-preview.md)]

Obecnie analiza dla przedsiębiorstw wymaga pracy na masową skalę na dowolnych danych, zarówno surowych, wyrafinowanych, jak i wysoce wyselekcjonowanych. W przeszłości tworzenie tego rodzaju rozwiązań analitycznych wymagało od przedsiębiorstw łączenia technologii dużych zbiorów danych i magazynowania danych, takich jak Spark i SQL. Następnie musieliby zintegrować je z potokami danych bogatych, które działają między danymi w magazynach relacyjnych i jeziorach danych.  

Rozwiązania takie jak ten są trudne do zbudowania, konfiguracji, zabezpieczenia i utrzymania, co opóźnia szybkie wyodrębnienie inteligentnego wglądu.

**Azure Synapse** to zintegrowana usługa analityczna, która skraca czas do wglądu ze wszystkich danych w dowolnej skali, w magazynach danych i systemach analizy dużych zbiorów danych. Łączy w sobie najlepsze technologie **SQL** używane w hurtowni danych przedsiębiorstwa, technologie **Spark** używane w analizie dużych zbiorów danych i **potoki** do organizowania działań i przenoszenia danych. 

Usługa Azure Synapse jest wyposażona w **środowisko** użytkownika studio natywnego w sieci Web, które zapewnia pojedyncze środowisko i model zarządzania, monitorowania, kodowania i zabezpieczeń.

Usługa Azure Synapse zapewnia najprostszy i najszybszy sposób, w jaki przedsiębiorstwo może zbierać szczegółowe informacje o dowolnych danych o dowolnym rozmiarze przy użyciu analiz, które są najbardziej znane. Jest on głęboko zintegrowany z **usłudze Power BI,** umożliwiając inżynierom danych tworzenie rozwiązań analitycznych, które działają kompleksowo w celu zapewnienia analizy biznesowej. 

Ponadto usługa Azure Synapse ułatwia tworzenie modeli predykcyjnych i zaawansowane analizy za pomocą uczenia maszynowego za pośrednictwem wbudowanej obsługi **usługi AzureML.**

## <a name="key-features--benefits"></a>Najważniejsze cechy & korzyści

### <a name="industry-leading-sql"></a>Wiodący w branży sql

* **Synapse SQL** to rozproszony system zapytań, który umożliwia przedsiębiorstwom implementowanie scenariuszy magazynowania danych i wirtualizacji danych przy użyciu standardowych środowisk T-SQL znanych inżynierom danych. Rozszerza również możliwości sql adres scenariuszy przesyłania strumieniowego i uczenia maszynowego.

* Synapse SQL oferuje zarówno **bezserwerowe, jak** i **aprowizacji** modeli zasobów, oferując zużycie i opcje rozliczeń, aby dopasować do Twoich potrzeb. Aby uzyskać przewidywalną wydajność i koszt, pule aprowizowania rezerwują moc obliczeniową dla danych przechowywanych w tabelach SQL. W przypadku nieplanowanych lub pękniętych obciążeń należy użyć bezserwerowego, zawsze dostępnego punktu końcowego SQL.
* Korzystanie z wbudowanych funkcji **przesyłania strumieniowego** do wyładowywać dane ze źródeł danych w chmurze do tabel SQL
* Integracja AI z SQL, przy użyciu modeli **uczenia maszynowego** do oceny danych za pomocą funkcji T-SQL PREDICT

### <a name="industry-standard-apache-spark"></a>Standardowa w branży platforma Apache Spark

**Synapse Spark** głęboko i bezproblemowo integruje Apache Spark - najpopularniejszy silnik big data open source używany do przygotowywania danych, inżynierii danych, ETL i uczenia maszynowego.

* Modele ml z algorytmami SparkML i integracją usługi AzureML dla platformy Apache Spark 2.4 z wbudowaną obsługą systemu Linux Foundation Delta Lake.
* Uproszczony model zasobów, który zwalnia cię z konieczności martwienia się o zarządzanie klastrami.
* Szybkie uruchamianie spark i agresywne skalowanie automatyczne.
* Wbudowana obsługa platformy .NET for Spark umożliwia ponowne użycie wiedzy specjalistycznej języka C# i istniejącego kodu platformy .NET w aplikacji platformy Spark.

### <a name="interop-of-sql-and-spark-on-your-data-lake"></a>Interop SQL i Spark na data lake

Usługa Azure Synapse usuwa bariery tradycyjnej technologii między używaniem sql i platformy Spark razem. Możesz bezproblemowo łączyć i dopasowywki w zależności od potrzeb i wiedzy.

* Współużytkowany system metadanych zgodny z gałęzią danych umożliwia bezproblemowe korzystanie z tabel zdefiniowanych w plikach w układzie danych przez platformę Spark lub Hive.
* SQL i Spark mogą bezpośrednio eksplorować i analizować pliki Parquet, CSV, TSV i JSON przechowywane w ułowiowym umiaśnie danych.
* Szybkie skalowalne ładowanie i zwalnianie danych między bazami danych SQL i Spark

### <a name="built-in-orchestration-via-pipelines"></a>Wbudowana aranżacja za pomocą rurociągów

Usługa Azure Synapse jest wbudowana z tym samym aparatem integracji danych i środowiskami co usługa Azure Data Factory, umożliwiając tworzenie potoków danych bogatych bez użycia oddzielnego aparatu aranżacji.

* Przenoszenie danych między synapse i 85+ lokalnych źródeł danych
* Zaaranżuj notesy, potoki, zadania spark, skrypty SQL, procedury przechowywane
* Bez kodu ETL z działaniami przepływu danych

### <a name="unified-management-monitoring-and-security"></a>Ujednolicone zarządzanie, monitorowanie i bezpieczeństwo

Usługa Azure Synapse zapewnia przedsiębiorstwom jeden sposób zarządzania zasobami analitycznymi, monitorowania użycia i aktywności oraz wymuszania zabezpieczeń.

* Przypisywanie użytkowników do roli w celu uproszczenia dostępu do zasobów analitycznych
* Szczegółowa kontrola dostępu do danych i kodu
* Jeden pulpit nawigacyjny do monitorowania zasobów, użycia i użytkowników w językach SQL i Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** to środowisko natywne w sieci Web, które łączy wszystko ze sobą dla inżynierów danych, umożliwiając im w jednym miejscu wykonanie każdego zadania potrzebnego do zbudowania kompletnego rozwiązania.

* Zbuduj kompleksowe rozwiązanie analityczne w jednym miejscu: pozyskiwania, odkrywania, przygotowywania, organizowania, wizualizowania
* Wiodąca w branży produktywność dla inżynierów danych piszących kod SQL lub Spark: tworzenie, debugowanie i optymalizacja wydajności
* Integracja z korporacyjnymi procesami ciągłej integracji/ciągłego wdrażania

## <a name="next-steps"></a>Następne kroki

* [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
* [Korzystanie z Synapse Studio](quickstart-synapse-studio.md)
* [Tworzenie puli SQL](quickstart-create-sql-pool.md)
* [Korzystanie z języka SQL na żądanie](quickstart-sql-on-demand.md)
* [Tworzenie puli platformy Spark Apache](quickstart-create-apache-spark-pool.md)
