---
title: Omówienie usługi Azure Data Lake Storage Gen1 w usłudze HDInsight
description: Omówienie pamięci masowej Data Lake Gen1 w hdinsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 15d7f0621ffbf883d267d389fb634a13aa927430
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873357"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Omówienie usługi Azure Data Lake Storage Gen1 w usłudze HDInsight

Usługa Azure Data Lake Storage Gen1 to repozytorium hiperskalowe dla obciążeń analitycznych dużych zbiorów danych w całej organizacji. Za pomocą usługi Azure Data Lake, można przechwytywać dane o dowolnym rozmiarze, typie i szybkości pozyskiwania. I w jednym miejscu do analizy operacyjnej i eksploracyjnej.

Uzyskaj dostęp do magazynu danych Lake Gen1 z usługi Hadoop (dostępnego z klastrem HDInsight) przy użyciu interfejsów API REST zgodnych ze standardem WebHDFS. Data Lake Storage Gen1 został zaprojektowany, aby umożliwić analizę przechowywanych danych i jest dostrojony pod kątem wydajności w scenariuszach analizy danych. Gen1 zawiera możliwości, które są niezbędne dla rzeczywistych przypadków użycia w przedsiębiorstwie. Te funkcje obejmują zabezpieczenia, łatwość zarządzania, zdolność adaptacji, niezawodność i dostępność.

Aby uzyskać więcej informacji na temat usługi Azure Data Lake Storage Gen1, zobacz szczegółowe [omówienie usługi Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Najważniejsze możliwości usługi Data Lake Storage Gen1 są następujące.

## <a name="compatibility-with-hadoop"></a>Kompatybilność z Hadoop

Data Lake Storage Gen1 to system plików Apache Hadoop kompatybilny ze środowiskiem HDFS i Hadoop.  Aplikacje lub usługi HDInsight korzystające z interfejsu API usługi WebHDFS można łatwo zintegrować z data lake storage gen1. Data Lake Storage Gen1 udostępnia również interfejs REST zgodny z usługą WebHDFS dla aplikacji.

Dane przechowywane w umiasienia danych Lake Storage Gen1 można łatwo analizować przy użyciu struktur analitycznych Hadoop. Struktury, takie jak MapReduce lub Hive. Klastry usługi Azure HDInsight można aprowizować i skonfigurować w celu bezpośredniego dostępu do danych przechowywanych w usłudze Data Lake Storage Gen1.

## <a name="unlimited-storage-petabyte-files"></a>Nieograniczony magazyn, petabajtowe pliki

Data Lake Storage Gen1 zapewnia nieograniczoną ilość miejsca do magazynowania i nadaje się do przechowywania różnych rodzajów danych do analizy. Nie nakłada limitów na rozmiary kont ani rozmiarów plików. Lub ilość danych, które mogą być przechowywane w jeziorze danych. Poszczególne pliki mają rozmiar od kilobajtów do petabajtów, dzięki czemu data lake storage gen1 jest doskonałym wyborem do przechowywania dowolnego typu danych. Dane są przechowywane trwale przez wykonanie wielu kopii. I nie ma żadnych ograniczeń, jak długo dane mogą być przechowywane w jeziorze danych.

## <a name="performance-tuning-for-big-data-analytics"></a>Dostrajanie wydajności w przypadku analizy dużych zbiorów danych

Data Lake Storage Gen1 jest przeznaczony do systemów analitycznych. Systemy, które wymagają ogromnej przepływności do wykonywania zapytań i analizowania dużych ilości danych. Jezioro danych rozprzestrzenia części pliku na kilku pojedynczych serwerach pamięci masowej. Podczas analizowania danych, ta konfiguracja poprawia przepływność odczytu, gdy plik jest odczytywany równolegle.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Gotowość dla przedsiębiorstw: Wysoka dostępna i bezpieczna

Data Lake Storage Gen1 zapewnia dostępność i niezawodność w standardzie branżowym. Zasoby danych są przechowywane trwale: nadmiarowe kopie chronią przed nieoczekiwanymi awariami. Przedsiębiorstwa mogą używać usługi Data Lake Storage Gen1 w swoich rozwiązaniach jako ważnej części istniejącej platformy danych.

Data Lake Storage Gen1 zapewnia również bezpieczeństwo przechowywanech danych klasy korporacyjnej. Aby uzyskać więcej informacji, zobacz [zabezpieczanie danych w usłudze Azure Data Lake Storage Gen1](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Elastyczne struktury danych

Data Lake Storage Gen1 może przechowywać dowolne dane w formacie macierzystym, tak jak jest, bez konieczności wcześniejszych przekształceń. Data Lake Storage Gen1 nie wymaga zdefiniowania schematu przed załadowaniem danych. Indywidualna struktura analityczna interpretuje dane i definiuje schemat w czasie analizy. Data Lake Storage Gen1 może obsługiwać dane strukturalne. I dane półstrukturalne i nieustrukturyzowane.

Kontenery Data Lake Storage Gen1 dla danych są zasadniczo folderami i plikami. Działasz na przechowywanych danych przy użyciu zestawów SDK, witryny Azure portal i programu Azure PowerShell. Dane umieszczone w magazynie za pomocą tych interfejsów i kontenerów, można przechowywać dowolny typ danych. Data Lake Storage Gen1 nie wykonuje żadnej specjalnej obsługi danych na podstawie typu danych.

## <a name="data-security-in-data-lake-storage-gen1"></a>Bezpieczeństwo danych w pamięci masowej w uł.o11

Usługa Data Lake Storage Gen1 używa usługi Azure Active Directory do uwierzytelniania i używa list kontroli dostępu (ACL) do zarządzania dostępem do danych.

| **Funkcja** | **Opis** |
| --- | --- |
| Authentication |Usługa Data Lake Storage Gen1 integruje się z usługą Azure Active Directory (Azure AD) w celu zarządzania tożsamościami i dostępem dla wszystkich danych przechowywanych w usłudze Data Lake Storage Gen1. Ze względu na integrację usługa Data Lake Storage Gen1 korzysta ze wszystkich funkcji usługi Azure AD. Funkcje te obejmują: uwierzytelnianie wieloskładnikowe, dostęp warunkowy i kontrolę dostępu opartą na rolach. Ponadto monitorowanie użycia aplikacji, monitorowanie zabezpieczeń i alerty i tak dalej. Data Lake Storage Gen1 obsługuje protokół OAuth 2.0 do uwierzytelniania w interfejsie REST. Zobacz [Uwierzytelnianie w usłudze Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Kontrola dostępu |Data Lake Storage Gen1 zapewnia kontrolę dostępu, obsługując uprawnienia w stylu POSIX, które są udostępniane przez protokół WebHDFS. Listy kontroli dostępu można włączyć dla folderu głównego, podfolderów i poszczególnych plików. Aby uzyskać więcej informacji na temat działania list ACL w kontekście pamięci masowej Data Lake Storage Gen1, zobacz [Kontrola dostępu w programie Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Szyfrowanie |Data Lake Storage Gen1 zapewnia również szyfrowanie danych przechowywanych na koncie. Ustawienia szyfrowania można określić podczas tworzenia konta Usługi Data Lake Storage Gen1. Możesz zdecydować się na szyfrowanie danych lub zdecydować się na brak szyfrowania. Aby uzyskać więcej informacji, zobacz [Szyfrowanie w pamięci masowej usługi Data Lake Gen1](../data-lake-store/data-lake-store-encryption.md). Aby uzyskać instrukcje dotyczące dostarczania konfiguracji związanej z szyfrowaniem, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal.](../data-lake-store/data-lake-store-get-started-portal.md) |

Aby dowiedzieć się więcej o zabezpieczaniu danych w usłudze Data Lake Storage Gen1, zobacz [Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplikacje zgodne z programem Data Lake Storage Gen1

Data Lake Storage Gen1 jest kompatybilny z większością komponentów open source w środowisku Hadoop. Bardzo dobrze integruje się również z innymi usługami Azure.  Skorzystaj z poniższych łączy, aby dowiedzieć się więcej o tym, jak usługa Data Lake Storage Gen1 może być używana zarówno ze składnikami typu open source, jak i innymi usługami platformy Azure.

* Zobacz [aplikacje big data typu open source, które współpracują z usługą Azure Data Lake Storage Gen1.](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)
* Zobacz [integrowanie usługi Azure Data Lake Storage Gen1 z innymi usługami platformy Azure,](../data-lake-store/data-lake-store-integrate-with-other-services.md) aby dowiedzieć się, jak korzystać z usługi Data Lake Storage Gen1 z innymi usługami platformy Azure, aby włączyć szerszy zakres scenariuszy.
* Aby uzyskać [wymagania dotyczące dużych zbiorów danych, zobacz Korzystanie z usługi Azure Data Lake Storage Gen1.](../data-lake-store/data-lake-store-data-scenarios.md)

## <a name="data-lake-storage-gen1-file-system-adl"></a>System plików Data Lake Storage Gen1 (adl://)

W środowiskach Hadoop można uzyskać dostęp do usługi Data Lake Storage Gen1 za pośrednictwem nowego systemu plików AzureDataLakeFilesystem (adl://). Wydajność aplikacji i usług, `adl://` które używają można zoptymalizować w sposób, który nie jest obecnie dostępny w webhdfs. W rezultacie można uzyskać elastyczność albo skorzystać z najlepszej wydajności przy użyciu zalecanej adl://. Lub zachowaj istniejący kod, kontynuując bezpośrednie korzystanie z interfejsu API usługi WebHDFS. Usługa Azure HDInsight w pełni wykorzystuje system AzureDataLakeFilesystem, aby zapewnić najlepszą wydajność w usłudze Data Lake Storage Gen1.

Dostęp do danych w umiołowania magazynu usługi Data Lake Gen1 przy użyciu następującego identyfikatora URI:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do danych w programie Data Lake Storage Gen1, zobacz [Akcje dostępne na przechowywanych danych](../data-lake-store/data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
* [Omówienie usługi Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)