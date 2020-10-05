---
title: Często zadawane pytania — Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)
description: Często zadawane pytania dotyczące usługi Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: ba6f79fffe5287be7574d422f026489d4da2795e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87287492"
---
# <a name="azure-synapse-analytics-workspaces-preview-frequently-asked-questions"></a>Analiza usługi Azure Synapse (obszary robocze — wersja zapoznawcza) — często zadawane pytania

W tym przewodniku znajdziesz najczęściej zadawane pytania dotyczące analizy Synapse.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Ogólne

### <a name="q-what-is-azure-synapse-analytics"></a>P: co to jest usługa Azure Synapse Analytics

Odp.: usługa Azure Synapse to Zintegrowana platforma danych dla analizy biznesowej, AI i ciągłego. Łączy on różne środowiska uruchomieniowe analizy, takie jak SQL i Spark, za pomocą pojedynczej platformy, która zapewnia jednolity sposób:

- Zabezpieczanie zasobów analitycznych, w tym sieci, zarządzanie dostępem jednokrotnym do puli, danych i artefaktów programistycznych.
- Łatwo Monitoruj i szybko Optymalizuj, reaguj i Debuguj zdarzenia występujące w działaniach obszaru roboczego w dowolnej warstwie.
- Zarządzaj metadanymi w różnych aparatach. Utwórz tabelę platformy Spark i zostanie ona automatycznie udostępniona w bazach danych usługi Azure Synapse.
- Pracuj z danymi za pomocą ujednoliconego środowiska użytkownika. Synapse Studio zapewnia deweloperom danych Big Data, inżynierów danych, przetwarzający, analityków danych i naukowców z danymi na tej samej platformie.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics/overview-what-is).

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>P: Jakie są główne składniki usługi Azure Synapse Analytics

Odp.: usługa Azure Synapse oferuje następujące możliwości:

- Możliwości analizy są oferowane za poorednictwem puli SQL lub SQL na żądanie (wersja zapoznawcza) (bezserwerowa).
- Pula Apache Spark (wersja zapoznawcza) z pełną obsługą Scala, Python, SparkSQL i C #
- Przepływ danych oferujący środowisko przekształcania danych Big Data bez kodu
- Integracja danych & aranżacji, aby zintegrować swoje dane i operacjonalizować wszystkie Programowanie kodu
- Studio, aby uzyskać dostęp do wszystkich tych funkcji za pomocą pojedynczego internetowego interfejsu użytkownika

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>P: w jaki sposób usługa Azure Synapse Analytics wiąże się z Azure SQL Data Warehouse

Odp.: usługa Azure Synapse Analytics to ewolucja Azure SQL Data Warehouse na platformie analitycznej, która obejmuje pulę SQL jako rozwiązanie magazynu danych. Ta platforma łączy eksplorację, pozyskiwanie, przekształcenie, przygotowanie i obsługę warstwy analitycznej.

## <a name="use-cases"></a>Przypadki zastosowań

### <a name="q-how-do-i-rename-a-published-artifact-dataset-notebook-sql-script-and-so-on-in-azure-synapse"></a>P: Jak mogę zmienić nazwy opublikowanego artefaktu (zestawu danych, notesu, skryptu SQL itd.) w usłudze Azure Synapse?

Odp.: Aby zmienić nazwę opublikowanego pliku artefaktu, należy najpierw sklonować plik i zmienić nazwę nowego pliku na preferowany. Należy ręcznie zaktualizować wszystkie odwołania artefaktu do nowej nazwy pliku i usunąć stary.

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>P: co to jest dobry przypadek użycia dla puli SQL Synapse

Odp.: Pula SQL jest sercem potrzeb hurtowni danych. Jest to wiodące rozwiązanie magazynu danych w [cenie/wydajności](https://azure.microsoft.com/services/sql-data-warehouse/compare/). Pula SQL to wiodące w branży rozwiązanie magazynu danych w chmurze, ponieważ można:

- Obsługuj duże i mieszane różne obciążenia bez wpływu na wydajność dzięki wysokiej współbieżności i izolacji obciążeń
- łatwe Zabezpieczanie danych dzięki zaawansowanym funkcjom z zakresu zabezpieczeń sieci na potrzeby dostępu do zasobów szczegółowych
- Skorzystaj z szerokiego zakresu ekologicznego systemu

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>P: co to jest dobry przypadek użycia dla platformy Spark w Synapse

Odp.: pierwszym celem jest zapewnienie doskonałego środowiska inżynierii danych do przekształcania danych w usłudze Batch lub Stream. Jej ścisła i prosta integracja z naszą aranżacją danych sprawia, że operacjonalizacji prac programistycznych jest prosta.

Innym przypadkiem użycia dla platformy Spark jest dla analityka danych:

- Wyodrębnij funkcję
- Eksplorowanie danych
- uczenie modelu

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>P: co to jest dobry przypadek użycia dla SQL na żądanie w Synapse

Odp.: SQL na żądanie jest usługą zapytania na danych w usłudze Data Lake. Dzięki temu można zdemokratyzuj proces dostęp do wszystkich danych, dostarczając znaną składnię T-SQL służącą do wykonywania zapytań dotyczących danych, bez konieczności kopiowania lub ładowania danych do wyspecjalizowanego magazynu.

Przykłady użycia:

- podstawowe odnajdywanie i Eksploracja — zapewnia analitykom danych, a także inżynierów danych, dzięki łatwej ścieżce do wglądu w dane żyjące w ich usłudze Data Lake przy użyciu schematu na potrzeby odczytywania zapytań T-SQL
- logiczny magazyn danych — analitycy danych mogą uruchamiać pełne wyrazistości języka T-SQL, aby bezpośrednio wysyłać zapytania i analizować dane przechowywane w usłudze Azure Storage oraz korzystać z znanych narzędzi analizy biznesowej (np. usług analiz platformy Azure, Power BI Premium itp.) do odświeżania pulpitów nawigacyjnych przez ponowne uruchomienie zapytań zapytania Starlight
- ETL "Single Query" — umożliwia inżynierom danych Przekształcanie danych opartych na usłudze Azure Storage z jednego formatu na inny, filtrowanie, agregowanie itp. w sposób wysoce równoległy, utrwalanie wyników zapytania w usłudze Azure Storage i natychmiastowe udostępnianie ich w celu dalszej obróbki w Starlight zapytania lub innych interesujących usług

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>P: co to jest dobry przypadek użycia przepływu danych w Synapse

Odp.: przepływ danych umożliwia inżynierom danych Tworzenie logiki transformacji danych graficznych bez pisania kodu. Wyniki przepływów danych są wykonywane jako działania w ramach integracji danych & aranżacji. Działania przepływu danych mogą być operacyjne za pośrednictwem istniejących możliwości planowania, kontroli, przepływu i monitorowania.

## <a name="security-and-access"></a>Bezpieczeństwo i dostęp

Odp.: kompleksowe środowisko logowania jednokrotnego to ważny proces uwierzytelniania w Synapse Analytics. Zarządzanie tożsamościami i przekazywanie ich za pośrednictwem pełnej integracji usługi AAD jest konieczne.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>P: Jak mogę uzyskać dostęp do plików i folderów w ADLS Gen2

Odp.: dostęp do plików i folderów jest obecnie zarządzany za pomocą ADLS Gen2. Aby uzyskać więcej informacji, zobacz [Data Lake kontroli dostępu do magazynu](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>P: Czy można używać narzędzi analizy biznesowej innych firm do uzyskiwania dostępu do usługi Azure Synapse Analytics

Odp.: tak, możesz użyć własnych aplikacji innych firm, takich jak Tableau i Power BI, aby nawiązać połączenie z pulą SQL i SQL na żądanie. Platforma Spark obsługuje IntelliJ.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
- [Korzystanie z programu Synapse Studio](quickstart-synapse-studio.md)
- [Tworzenie puli SQL](quickstart-create-sql-pool-portal.md)
- [Korzystanie z bazy danych SQL na żądanie](quickstart-sql-on-demand.md)
- [Tworzenie puli Apache Spark](quickstart-create-apache-spark-pool-portal.md) 
