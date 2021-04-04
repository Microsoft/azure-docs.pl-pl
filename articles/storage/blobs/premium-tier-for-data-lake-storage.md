---
title: Warstwa Premium dla Azure Data Lake Storage | Microsoft Docs
description: Skorzystaj z warstwy wydajności Premium z Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/30/2020
ms.author: normesta
ms.openlocfilehash: be440407fb6f4d9715ba80c584af023a5f662394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93324217"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Warstwa Premium dla Azure Data Lake Storage

Azure Data Lake Storage Gen2 teraz obsługuje [warstwę wydajności Premium](storage-blob-performance-tiers.md#premium-performance). Warstwa wydajności Premium jest idealnym rozwiązaniem w przypadku aplikacji do analizy danych Big Data i obciążeń, które wymagają niskich opóźnień i mają dużą liczbę transakcji.

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>Obciążenia, które mogą korzystać z warstwy wydajności Premium

Przykładowe obciążenia obejmują interaktywne obciążenia, IoT, analiza przesyłania strumieniowego, sztuczną inteligencję i uczenie maszynowe. 

**Interaktywne obciążenia** 

Te obciążenia wymagają natychmiastowej aktualizacji i opinii użytkowników, takich jak handel elektroniczny i aplikacje do mapowania, interaktywne aplikacje wideo itp. Na przykład w aplikacji handlu elektronicznego mniej często oglądane elementy mogą nie być buforowane. Jednak muszą być natychmiast wyświetlane na żądanie klienta. Innym przykładem, analityki danych, analitycy i deweloperzy mogą szybko uzyskiwać wgląd w dane, uruchamiając zapytania dotyczące danych przechowywanych na koncie, które korzystają z warstwy wydajności Premium. 

**Analiza IoT/Streaming** 

W scenariuszu IoT wiele mniejszych operacji zapisu może być wypychanych do chmury co sekundę. Duże ilości danych mogą być pozyskiwane, agregowane na potrzeby analizy, a następnie usuwane prawie natychmiast. Możliwości dużego pozyskiwania warstwy wydajności Premium sprawiają, że jest wydajna dla tego typu obciążenia. 

**Sztuczna inteligencja/Uczenie maszynowe (AI/ML)** 

AI/ML zajmuje się zużyciem i przetwarzaniem różnych typów danych, na przykład wizualizacji, mowy i tekstu. Ten typ obciążeń obliczeniowych o wysokiej wydajności zajmuje się dużymi ilościami danych, które wymagają szybkiej reakcji i wydajnego czasu pozyskiwania danych. 

## <a name="cost-effectiveness"></a>Opłacalność

Warstwa wydajności Premium ma wyższy koszt magazynowania, ale obniży koszt transakcji w porównaniu do standardowej warstwy wydajności. Jeśli Twoje aplikacje i obciążenia wykonują dużą liczbę transakcji, warstwa wydajności Premium może być opłacalna.

W poniższej tabeli przedstawiono efektywność kosztową warstwy Premium dla Azure Data Lake Storage. Każda kolumna reprezentuje liczbę transakcji w miesiącu.  Każdy wiersz reprezentuje procent transakcji, które są odczytywane transakcje. Każda komórka w tabeli zawiera wartość procentową obniżki kosztów skojarzoną z wartością procentową odczytania transakcji oraz liczbę wykonanych transakcji. 

Na przykład przy założeniu, że Twoje konto znajduje się w regionie Wschodnie stany USA 2, liczba transakcji z Twoim kontem przekracza 90M, a 70% tych transakcji jest odczytanych transakcji, a warstwa wydajności Premium jest bardziej opłacalna.

> [!div class="mx-imgBorder"]
> ![miejsce na obraz](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> Jeśli wolisz oszacować efektywność kosztów na podstawie liczby transakcji na sekundę dla każdego TB danych, możesz użyć nagłówków kolumn, które są wyświetlane w dolnej części tabeli.

Aby uzyskać więcej informacji o cenach, zobacz stronę z [cennikiem Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/) .

## <a name="feature-availability"></a>Dostępność funkcji 

Niektóre funkcje magazynu obiektów BLOB mogą nie być dostępne lub mogą mieć tylko częściową pomoc techniczną w warstwie wydajności Premium. Aby uzyskać pełną listę, zobacz [funkcje magazynu obiektów BLOB dostępne w Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md). Następnie przejrzyj listę [znanych problemów](data-lake-storage-known-issues.md) , aby ocenić wszelkie luki w działaniu.

## <a name="enabling-the-premium-performance-tier"></a>Włączanie warstwy wydajności Premium 

Możesz użyć warstwy Premium dla Azure Data Lake Storage przez utworzenie konta BlockBlobStorage z **włączonym** ustawieniem **hierarchicznej przestrzeni nazw** . Aby uzyskać pełne wskazówki, zobacz [Tworzenie konta BlockBlobStorage konta](storage-blob-create-account-block-blob.md) .

Podczas tworzenia konta upewnij się, że wybrano opcję wydajność **Premium** i rodzaj konta **BlockBlobStorage** .

> [!div class="mx-imgBorder"]
> ![Utwórz blockblobstorageacount](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

Włącz ustawienie **hierarchicznej przestrzeni nazw** na karcie **Zaawansowane** na stronie **Tworzenie konta magazynu** . To ustawienie należy włączyć podczas tworzenia konta. Nie można włączyć go później.

Na poniższej ilustracji przedstawiono to ustawienie na stronie **Tworzenie konta magazynu** .

> [!div class="mx-imgBorder"]
> ![Ustawienie hierarchicznej przestrzeni nazw](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>Dostępność w regionach

Warstwa Premium dla Azure Data Lake Storage jest dostępna w następujących regionach.

|Region (Region)|Nadmiarowość|
|--|--|
|East US|LRS, ZRS|
|Wschodnie stany USA 2|LRS, ZRS|
|Central US|LRS|
|Zachodnie stany USA|LRS|
|Zachodnie stany USA 2|LRS, ZRS|
|Zachodnio-środkowe stany USA|LRS|
|South Central US|LRS|
|Kanada Środkowa|LRS|
|Kanada Wschodnia|LRS|
|Europa Północna|LRS, ZRS|
|West Europe|LRS, ZRS|
|Południowe Zjednoczone Królestwo|LRS|
|Zachodnie Zjednoczone Królestwo|LRS|
|Francja Środkowa|LRS|
|Azja Wschodnia|LRS|
|Korea Środkowa|LRS|
|Korea Południowa|LRS|
|Indie Środkowe|LRS|
|Indie Zachodnie|LRS|
|Północne Zjednoczone Emiraty Arabskie|LRS|
|Japonia Wschodnia|LRS|
|Japonia Zachodnia|LRS|
|Azja Południowo-Wschodnia|LRS, ZRS|
|Australia Wschodnia|LRS, ZRS|
|Australia Południowo-Wschodnia|LRS|
|Brazylia Południowa|LRS|

## <a name="next-steps"></a>Następne kroki

Skorzystaj z warstwy Premium, aby uzyskać Azure Data Lake Storage z ulubioną usługą analizy, taką jak Azure Databricks, Azure HDInsight i Azure Synapse Analytics. 

- [Samouczek: Azure Data Lake Storage Gen2, Azure Databricks & Spark](data-lake-storage-use-databricks-spark.md) 
- [Używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) Usługa HDInsight obsługuje obecnie konto używające warstwy wydajności Premium wraz z klastrem HBase, w którym włączono szybsze operacje zapisu.
- [Szybki Start: Tworzenie obszaru roboczego Synapse](../../synapse-analytics/quickstart-create-workspace.md)

