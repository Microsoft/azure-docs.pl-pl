---
title: Przegląd Azure Data Lake Storage Gen1 w usłudze HDInsight
description: Omówienie Data Lake Storage Gen1 w usłudze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 947dd125cf9c5f5874eed380b3d69cff11509e31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82187249"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Przegląd Azure Data Lake Storage Gen1 w usłudze HDInsight

Azure Data Lake Storage Gen1 to krótkoterminowe repozytorium w całym przedsiębiorstwie na potrzeby obciążeń analitycznych danych Big Data. Za pomocą Azure Data Lake można przechwytywać dane dowolnego rozmiaru, typu i szybkości pozyskiwania. I w jednym miejscu do analizy operacyjnej i poznawczej.

Data Lake Storage Gen1 dostępu z usługi Hadoop (dostępnej w klastrze usługi HDInsight) przy użyciu interfejsów API REST zgodnych z WebHDFS. Data Lake Storage Gen1 zaprojektowano w celu włączenia analizy przechowywanych danych i dostrajania wydajności w scenariuszach analizy danych. Gen1 zawiera funkcje, które są niezbędne dla rzeczywistych przypadków użycia w przedsiębiorstwie. Te możliwości obejmują zabezpieczenia, możliwości zarządzania, możliwość adaptacji, niezawodność i dostępność.

Aby uzyskać więcej informacji na temat Azure Data Lake Storage Gen1, Zobacz szczegółowe [omówienie Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Kluczowe możliwości Data Lake Storage Gen1 są następujące:

## <a name="compatibility-with-hadoop"></a>Zgodność z usługą Hadoop

Data Lake Storage Gen1 to Apache Hadoop system plików zgodny z systemem HDFS i środowiskiem Hadoop.  Aplikacje lub usługi HDInsight, które używają interfejsu API WebHDFS, można łatwo zintegrować z Data Lake Storage Gen1. Data Lake Storage Gen1 udostępnia również Interfejs REST zgodny z WebHDFS dla aplikacji.

Dane przechowywane w Data Lake Storage Gen1 mogą być łatwo przeanalizowane przy użyciu platform analitycznych Hadoop. Struktury, takie jak MapReduce lub Hive. Klastry usługi Azure HDInsight można zainicjować i skonfigurować do bezpośredniego dostępu do danych przechowywanych w Data Lake Storage Gen1.

## <a name="unlimited-storage-petabyte-files"></a>Nieograniczony magazyn, petabajtowe pliki

Data Lake Storage Gen1 zapewnia nieograniczony magazyn i jest odpowiedni do przechowywania różnych rodzajów danych na potrzeby analizy. Nie nakłada to limitów rozmiarów kont ani rozmiarów plików. Lub ilość danych, które mogą być przechowywane w usłudze Data Lake. Zakres pojedynczych plików jest w rozmiarze od kilobajtów do petabajtów, co sprawia, że Data Lake Storage Gen1 doskonały wybór do przechowywania dowolnego typu danych. Dane są przechowywane trwale przez utworzenie wielu kopii. Nie ma żadnych ograniczeń dotyczących czasu przechowywania danych w usłudze Data Lake.

## <a name="performance-tuning-for-big-data-analytics"></a>Dostrajanie wydajności do analizy danych Big Data

Data Lake Storage Gen1 jest przeznaczony dla systemów analitycznych. Systemy, które wymagają ogromnej przepływności do wykonywania zapytań i analizowania dużej ilości danych. Data Lake rozprzestrzenia części pliku na kilka pojedynczych serwerów magazynu. Podczas analizowania danych ta konfiguracja zwiększa przepływność odczytu, gdy plik jest odczytywany równolegle.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Gotowość do przedsiębiorstwa: wysoce dostępny i bezpieczny

Data Lake Storage Gen1 zapewnia standardową dostępność i niezawodność. Zasoby danych są przechowywane trwale: chroni nadmiarowe kopie przed nieoczekiwanymi awariami. Przedsiębiorstwa mogą używać Data Lake Storage Gen1 w swoich rozwiązaniach jako ważnej części istniejącej platformy danych.

Data Lake Storage Gen1 również zapewnia zabezpieczenia klasy korporacyjnej dla przechowywanych danych. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych w Azure Data Lake Storage Gen1](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Elastyczne struktury danych

Data Lake Storage Gen1 mogą przechowywać dowolne dane w formacie natywnym, tak jak to, bez konieczności uprzedniego przekształcenia. Data Lake Storage Gen1 nie wymaga zdefiniowania schematu przed załadowaniem danych. Pojedyncze środowisko analityczne interpretuje dane i definiuje schemat w czasie analizy. Data Lake Storage Gen1 może obsługiwać dane strukturalne. I dane bez struktury.

Kontenery Data Lake Storage Gen1 dla danych to zasadniczo foldery i pliki. Dane przechowywane są wykonywane przy użyciu zestawów SDK, Azure Portal i Azure PowerShell. Dane są umieszczane w sklepie przy użyciu tych interfejsów i kontenerów, które mogą przechowywać dowolny typ danych. Data Lake Storage Gen1 nie wykonuje żadnej specjalnej obsługi danych na podstawie typu danych.

## <a name="data-security-in-data-lake-storage-gen1"></a>Bezpieczeństwo danych w Data Lake Storage Gen1

Data Lake Storage Gen1 używa Azure Active Directory do uwierzytelniania i używa list kontroli dostępu (ACL) do zarządzania dostępem do danych.

| **Funkcja** | **Opis** |
| --- | --- |
| Uwierzytelnianie |Data Lake Storage Gen1 integruje się z usługą Azure Active Directory (Azure AD) na potrzeby zarządzania tożsamościami i dostępem dla wszystkich danych przechowywanych w Data Lake Storage Gen1. Ze względu na integrację Data Lake Storage Gen1 korzyści ze wszystkich funkcji usługi Azure AD. Do tych funkcji należą: uwierzytelnianie wieloskładnikowe, dostęp warunkowy i kontrola dostępu oparta na rolach. Ponadto monitorowanie użycia aplikacji, monitorowanie zabezpieczeń i alerty itd. Data Lake Storage Gen1 obsługuje uwierzytelnianie przy użyciu protokołu OAuth 2,0 w ramach interfejsu REST. Zobacz [uwierzytelnianie w ramach Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Kontrola dostępu |Data Lake Storage Gen1 zapewnia kontrolę dostępu przez obsługę uprawnień w stylu POSIX, które są udostępniane przez protokół WebHDFS. Listy kontroli dostępu można włączyć dla folderu głównego, podfolderów i poszczególnych plików. Aby uzyskać więcej informacji na temat działania list ACL w kontekście Data Lake Storage Gen1, zobacz [Kontrola dostępu w Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Szyfrowanie |Data Lake Storage Gen1 również zapewnia szyfrowanie danych przechowywanych na koncie. Podczas tworzenia konta Data Lake Storage Gen1 należy określić ustawienia szyfrowania. Możesz zdecydować się na zaszyfrowanie danych lub rezygnację z szyfrowania. Aby uzyskać więcej informacji, zobacz [szyfrowanie w Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Aby uzyskać instrukcje dotyczące sposobu zapewnienia konfiguracji związanej z szyfrowaniem, zobacz Wprowadzenie do [Azure Data Lake Storage Gen1 przy użyciu Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Aby dowiedzieć się więcej na temat zabezpieczania danych w Data Lake Storage Gen1, zobacz [Zabezpieczanie danych przechowywanych w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplikacje zgodne z Data Lake Storage Gen1

Data Lake Storage Gen1 jest zgodny z większością składników Open Source w środowisku Hadoop. Bardzo dobrze integruje się również z innymi usługami Azure.  Skorzystaj z poniższych linków, aby dowiedzieć się więcej o tym, jak Data Lake Storage Gen1 mogą być używane zarówno z składnikami Open Source, jak i innymi usługami platformy Azure.

* Zobacz [aplikacje danych Big Data dla programu Open Source, które współpracują z Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Zobacz [integrowanie Azure Data Lake Storage Gen1 z innymi usługami platformy Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) , aby dowiedzieć się, jak używać Data Lake Storage Gen1 z innymi usługami platformy Azure w celu zapewnienia szerszego zakresu scenariuszy.
* Zobacz [używanie Azure Data Lake Storage Gen1, aby uzyskać wymagania dotyczące danych Big Data](../data-lake-store/data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system-adl"></a>System plików Data Lake Storage Gen1 (adl://)

W środowiskach Hadoop można uzyskać dostęp do Data Lake Storage Gen1 za pomocą nowego systemu plików, AzureDataLakeFilesystem (adl://). Wydajność aplikacji i usług, które są używane, `adl://` można zoptymalizować w sposób, który nie jest obecnie dostępny w WebHDFS. Dzięki temu można uzyskać elastyczność, aby uzyskać najlepszą wydajność przy użyciu zalecanych adl://. Lub Zachowaj istniejący kod, kontynuując bezpośrednie używanie interfejsu API WebHDFS. Usługa Azure HDInsight w pełni wykorzystuje AzureDataLakeFilesystem, aby zapewnić najlepszą wydajność na Data Lake Storage Gen1.

Uzyskaj dostęp do danych w Data Lake Storage Gen1 przy użyciu następującego identyfikatora URI:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do danych w Data Lake Storage Gen1, zobacz [akcje dostępne dla przechowywanych danych](../data-lake-store/data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
* [Omówienie usługi Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)