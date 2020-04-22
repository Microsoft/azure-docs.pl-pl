---
title: Często zadawane pytania — Usługa Azure Synapse Analytics
description: Często zadawane pytania dotyczące usługi Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 00a98fdeb4b8febbcb9d1183fabffc7298cc9e4c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770727"
---
# <a name="azure-synapse-analytics-workspace-preview-frequently-asked-questions"></a>Usługa Azure Synapse Analytics (wersja zapoznawcza obszaru roboczego) często zadawane pytania

W tym przewodniku znajdziesz najczęściej zadawane pytania dotyczące Synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Ogólne

### <a name="q-what-is-azure-synapse-analytics"></a>P: Co to jest usługa Azure Synapse Analytics

Odp.: Azure Synapse to zintegrowana platforma danych dla analizy biznesowej, sztucznej inteligencji i ciągłej analizy. Łączy różne środowiska wykonawcze analizy, takie jak SQL i Spark za pośrednictwem jednej platformy, która zapewnia ujednolicony sposób:

- Zabezpiecz swoje zasoby analityczne, w tym sieć, zarządzanie dostępem do puli, danych i artefaktów deweloperskich.
- Łatwe monitorowanie i szybkie optymalizowanie zdarzeń związanych z obszarem roboczym, reagowanie i debugowanie w dowolnej warstwie.
- Zarządzanie metadanymi w różnych aparatach. Utwórz tabelę Platformy Spark, która będzie automatycznie dostępna w bazach danych usługi Azure Synapse.
- Interakcja z danymi za pośrednictwem ujednoliconego środowiska użytkownika. Synapse Studio przenosi deweloperów big data, inżynierów danych, administratorów danych, analityków danych i analityków danych na tę samą platformę.

### <a name="q-how-do-i-get-started-with-azure-synapse-analytics"></a>Pyt.: Jak rozpocząć korzystanie z usługi Azure Synapse Analytics

Odp.: Aby rozpocząć korzystanie z usługi Azure Synapse Analytics, utwórz [obszar roboczy Synapse](https://portal.azure.com) (jest bezpłatny!) i utwórz zasoby, które chcesz w tym obszarze roboczym. Możesz wykonać jeden z naszych samouczków Szybki start, takich jak [Tworzenie puli SQL Synapse](quickstart-create-sql-pool.md) lub [Tworzenie obszaru roboczego,](quickstart-create-workspace.md)który poprowadzi Cię przez prosty przypadek użycia. 

Przykładowe notesy i skrypty SQL można również znaleźć w naszym [repozytorium.](https://github.com/Azure/azure-synapse-analytics/tree/master/samples) Jeśli chcesz połączyć się z publicznym zestawem danych, utwórz nową połączone usługi z następującymi atrybutami:

- azure_storage_account_name = "azureopendatastorage"
- azure_storage_sas_token = "" (zapis **""**)

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>P: Jakie są główne składniki usługi Azure Synapse Analytics

Odp.: Usługa Azure Synapse ma następujące możliwości:

- Funkcje analizy są oferowane za pośrednictwem puli SQL lub SQL na żądanie (wersja zapoznawcza) (bezserwerowa).
- Apache Spark pool (wersja zapoznawcza) z pełną obsługą scala, Python, SparkSQL i C #
- Przepływ danych oferuje bezkońcowe środowisko przekształcania dużych zbiorów danych
- Integracja danych & aranżacji, aby zintegrować dane i operacjonalizacji całego tworzenia kodu
- Studio, aby uzyskać dostęp do wszystkich tych funkcji za pośrednictwem jednego interfejsu użytkownika sieci Web

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>Pyt.: W jaki sposób usługa Azure Synapse Analytics odnosi się do usługi Azure SQL Data Warehouse

Odp.: Usługa Azure Synapse Analytics to ewolucja usługi Azure SQL Data Warehouse w platformę analityczną, która obejmuje pulę SQL jako rozwiązanie magazynu danych. Ta platforma łączy eksplorację danych, pozyskiwania, przekształcanie, przygotowanie i warstwę analizy obsługujących.

## <a name="use-cases"></a>Przypadki zastosowań

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>P: Co to jest dobry przypadek użycia puli Synapse SQL

Odp.: Pula SQL jest sercem potrzeb magazynu danych. Jest to wiodące rozwiązanie magazynu danych pod względem [ceny/wydajności.](https://azure.microsoft.com/services/sql-data-warehouse/compare/) Pula SQL jest wiodącym w branży rozwiązaniem do magazynu danych w chmurze, ponieważ można:

- obsługa dużej i mieszanej gamy obciążeń bez wpływu na wydajność dzięki wysokiej współbieżności i izolacji obciążenia
- łatwe zabezpieczanie danych dzięki zaawansowanym funkcjom, od zabezpieczeń sieci po dostęp do drobnoziarnistego
- korzystać z szerokiej gamy ekosystemów

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>P: Co to jest dobry przypadek użycia spark w Synapse

Odp.: Naszym pierwszym celem jest zapewnienie doskonałego środowiska inżynierii danych do przekształcania danych w jeziorze w partii lub strumieniu. Jego ścisła i prosta integracja z naszą aranżacją danych sprawia, że operacjonalizacja pracy deweloperskiej jest prosta.

Innym przypadkiem użycia dla platformy Spark jest dla analityka danych:

- wyodrębnianie funkcji
- eksplorowanie danych
- szkolić model

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>P: Co to jest dobry przypadek użycia sql na żądanie w Synapse

Odp.: SQL na żądanie jest usługą zapytań za pośrednictwem danych w usłudze data lake. Umożliwia demokratyzację dostępu do wszystkich danych, zapewniając znaną składnię T-SQL do wykonywania zapytań o dane w miejscu, bez konieczności kopiowania lub ładowania danych do specjalistycznego magazynu.

Przykłady przypadków użycia są następujące:

- podstawowe odkrycie i eksploracja - zapewnia analitykom danych, nowo powstającym analitykom danych i inżynierom danych łatwą ścieżkę do pierwszego wglądu w dane żyjące w ich jeziorze danych ze schematem odczytu zapytań T-SQL
- logiczny magazyn danych - analitycy danych mogą uruchamiać pełną ekspresję języka T-SQL, aby bezpośrednio wyszukiwać i analizować dane zamieszkałe w usłudze Azure Storage i używać znanych narzędzi analizy biznesowej (np. usługi Analizy platformy Azure, usługi Power BI Premium itp.), aby odświeżyć pulpity nawigacyjne, ponownie uruchamiając zapytania Starlight Query
- "pojedyncze zapytanie" ETL - pozwala inżynierom danych na przekształcanie danych opartych na usłudze Azure Storage z jednego formatu do drugiego, filtrowanie, agregowanie itp.

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>P: Co to jest dobry przypadek użycia przepływu danych w Synapse

Odp.: Przepływ danych umożliwia inżynierom danych opracowanie logiki transformacji danych graficznych bez pisania kodu. Wynikowe przepływy danych są wykonywane jako działania w ramach integracji danych & aranżacji. Działania przepływu danych mogą być operationalized za pośrednictwem istniejących planowania, kontroli, przepływu i monitorowania możliwości.

## <a name="security-and-access"></a>Bezpieczeństwo i dostęp

Odp.: Kompleksowe środowisko logowania jednokrotnego jest ważnym procesem uwierzytelniania w usłudze Synapse Analytics. Zarządzanie i przekazywanie tożsamości za pośrednictwem pełnej integracji usługi AAD jest koniecznością.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adlsg2"></a>P: Jak uzyskać dostęp do plików i folderów w ADLSg2

Odp.: Dostęp do plików i folderów jest obecnie zarządzany za pośrednictwem ADLSg2. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu do magazynu usługi Data Lake](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>P: Czy mogę korzystać z narzędzi analizy biznesowej innych firm w celu uzyskania dostępu do usługi Azure Synapse Analytics

Odp.: Tak, możesz używać aplikacji biznesowych innych firm, takich jak Tableau i Power BI, do łączenia się z pulą SQL i sql na żądanie. Spark obsługuje technologię IntelliJ.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
- [Korzystanie z Synapse Studio](quickstart-synapse-studio.md)
- [Tworzenie puli SQL](quickstart-create-sql-pool.md)
- [Korzystanie z bazy danych SQL na żądanie](quickstart-sql-on-demand.md)
- [Tworzenie puli platformy Spark Apache](quickstart-create-apache-spark-pool.md) 
