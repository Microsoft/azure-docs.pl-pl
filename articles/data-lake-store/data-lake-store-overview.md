---
title: Co to jest usługa Azure Data Lake Storage Gen1? | Microsoft Docs
description: Przegląd Data Lake Storage Gen1 (wcześniej znany jako Azure Data Lake Store) i wartość dostarczana przez inne magazyny danych
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 52c00359fb4e759d9517dee958e31c00feb2b33e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92149236"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Co to jest usługa Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 jest repozytorium skalowalnych w całej firmie dla obciążeń analitycznych danych Big Data. Usługa Azure Data Lake umożliwia przechwytywanie danych dowolnego typu, o dowolnym rozmiarze i szybkości wprowadzania oraz przechowywanie ich w jednym miejscu na potrzeby analiz operacyjnych i poznawczych.

Dostęp do Data Lake Storage Gen1 można uzyskać z usługi Hadoop (dostępnej z klastrem usługi HDInsight) przy użyciu interfejsów API REST zgodnych z WebHDFS. Jest ona przeznaczona do włączania analiz przechowywanych danych i dostrajania wydajności dla scenariuszy analizy danych. Data Lake Storage Gen1 obejmuje wszystkie funkcje klasy korporacyjnej: zabezpieczenia, możliwości zarządzania, skalowalności, niezawodności i dostępności.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Najważniejsze możliwości

Poniżej wymieniono niektóre kluczowe możliwości Data Lake Storage Gen1.

### <a name="built-for-hadoop"></a>Stworzona dla platformy Hadoop

Data Lake Storage Gen1 to Apache Hadoop system plików, który jest zgodny z usługą Hadoop rozproszony system plików (HDFS) i współpracuje z ekosystemem usługi Hadoop. Istniejące aplikacje lub usługi HDInsight, które korzystają z interfejsu API WebHDFS, można łatwo zintegrować z Data Lake Storage Gen1. Data Lake Storage Gen1 udostępnia również Interfejs REST zgodny z WebHDFS dla aplikacji.

Dane przechowywane w Data Lake Storage Gen1 można łatwo analizować przy użyciu platform analitycznych Hadoop, takich jak MapReduce lub Hive. Można zainicjować obsługę klastrów usługi Azure HDInsight i skonfigurować je do bezpośredniego dostępu do danych przechowywanych w Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Nieograniczony magazyn, petabajtowe pliki

Data Lake Storage Gen1 zapewnia nieograniczony magazyn i może przechowywać różne dane do analizy. Nie nakłada żadnych ograniczeń dotyczących rozmiarów kont, rozmiarów plików ani ilości danych, które mogą być przechowywane w usłudze Data Lake. Poszczególne pliki mogą mieć zakres od kilobajtu do petabajtów. Dane są przechowywane trwale przez utworzenie wielu kopii. Nie ma limitu czasu, w którym dane mogą być przechowywane w usłudze Data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Wydajność dostosowana na potrzeby analizy danych big data

Data Lake Storage Gen1 jest zbudowana do uruchamiania systemów analitycznych o dużej skali, które wymagają ogromnej przepływności, aby wykonywać zapytania i analizować duże ilości danych. Usługa Data Lake rozmieszcza części pliku na wielu serwerach magazynu. Zwiększa to przepływność odczytu podczas odczytywania pliku równolegle w celu wykonywania analizy danych.

### <a name="enterprise-ready-highly-available-and-secure"></a>Gotowe do przedsiębiorstwa: wysoce dostępne i bezpieczne

Data Lake Storage Gen1 zapewnia standardową dostępność i niezawodność. Dane są przechowywane trwale dzięki wykonywaniu nadmiarowych kopii, aby zapewnić ochronę danych na wypadek nieoczekiwanych awarii.

Data Lake Storage Gen1 również zapewnia zabezpieczenia klasy korporacyjnej dla przechowywanych danych. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie danych w Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Wszystkie dane

Data Lake Storage Gen1 mogą przechowywać dowolne dane w formacie natywnym, bez konieczności wcześniejszego przekształcenia. Data Lake Storage Gen1 nie wymaga zdefiniowania schematu przed załadowaniem danych, pozostawiając je do poszczególnych platform analitycznych w celu interpretacji danych i zdefiniowania schematu w czasie analizy. Możliwość przechowywania plików o dowolnych rozmiarach i formatach umożliwia Data Lake Storage Gen1 do obsługi danych ze strukturą, częściową strukturą i bez struktury.

Kontenery Data Lake Storage Gen1 dla danych to zasadniczo foldery i pliki. Dane przechowywane są wykonywane przy użyciu zestawów SDK, Azure Portal i programu Azure PowerShell. Jeśli dane są umieszczane w sklepie przy użyciu tych interfejsów i przy użyciu odpowiednich kontenerów, można przechowywać dane dowolnego typu. Data Lake Storage Gen1 nie wykonuje żadnej specjalnej obsługi danych na podstawie typu przechowywanych danych.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Zabezpieczanie danych

Data Lake Storage Gen1 używa Azure Active Directory (Azure AD) do uwierzytelniania oraz list kontroli dostępu (ACL), aby zarządzać dostępem do danych.

| Cecha | Opis |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integruje się z usługą Azure AD w celu zarządzania tożsamościami i dostępem dla wszystkich danych przechowywanych w Data Lake Storage Gen1. Ze względu na integrację Data Lake Storage Gen1 korzyści ze wszystkich funkcji usługi Azure AD, takich jak uwierzytelnianie wieloskładnikowe, dostęp warunkowy, kontrola dostępu oparta na rolach na platformie Azure, monitorowanie użycia aplikacji, monitorowanie zabezpieczeń i alerty itd. Data Lake Storage Gen1 obsługuje uwierzytelnianie przy użyciu protokołu OAuth 2,0 w ramach interfejsu REST. Zobacz [Data Lake Storage Gen1 Authentication](data-lakes-store-authentication-using-azure-active-directory.md).|
| Kontrola dostępu |Data Lake Storage Gen1 zapewnia kontrolę dostępu dzięki obsłudze uprawnień w stylu POSIX przez protokół WebHDFS. Listy ACL można włączyć w folderze głównym, w podfolderach i na pojedynczych plikach. Aby uzyskać więcej informacji na temat sposobu działania list ACL w kontekście Data Lake Storage Gen1, zobacz [Kontrola dostępu w Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Szyfrowanie |Data Lake Storage Gen1 również zapewnia szyfrowanie danych przechowywanych na koncie. Podczas tworzenia konta Data Lake Storage Gen1 należy określić ustawienia szyfrowania. Możesz zdecydować się na zaszyfrowanie danych lub rezygnację z szyfrowania. Aby uzyskać więcej informacji, zobacz [szyfrowanie w Data Lake Storage Gen1](data-lake-store-encryption.md). Aby uzyskać instrukcje dotyczące sposobu zapewnienia konfiguracji związanej z szyfrowaniem, zobacz Wprowadzenie [do Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-get-started-portal.md). |

Aby uzyskać instrukcje dotyczące zabezpieczania danych w Data Lake Storage Gen1, zobacz [Zabezpieczanie danych w programie Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Zgodność aplikacji

Data Lake Storage Gen1 jest zgodny z większością składników Open Source w ekosystemie usługi Hadoop. Integruje się ona również z innymi usługami platformy Azure. Aby dowiedzieć się więcej na temat używania Data Lake Storage Gen1 z składnikami Open Source i innymi usługami platformy Azure, użyj następujących linków:

- Aby uzyskać listę aplikacji typu "open source", które współdziałają z Data Lake Storage Gen1, zobacz [aplikacje i usługi zgodne z Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) .
- Zobacz [Integrowanie z innymi usługami platformy Azure](data-lake-store-integrate-with-other-services.md) , aby dowiedzieć się, jak używać Data Lake Storage Gen1 z innymi usługami platformy Azure w celu zapewnienia szerszego zakresu scenariuszy.
- Zobacz [scenariusze używania Data Lake Storage Gen1](data-lake-store-data-scenarios.md) , aby dowiedzieć się, jak używać Data Lake Storage Gen1 w scenariuszach, takich jak pozyskiwanie danych, przetwarzanie danych, pobieranie danych i Wizualizacja danych.

## <a name="data-lake-storage-gen1-file-system"></a>System plików Data Lake Storage Gen1

Dostęp do Data Lake Storage Gen1 można uzyskać za pośrednictwem systemu plików AzureDataLakeFilesystem (adl://) w środowiskach Hadoop (dostępne z klastrem usługi HDInsight). Aplikacje i usługi korzystające z adl://mogą korzystać z dalszych optymalizacji wydajności, które nie są obecnie dostępne w WebHDFS. W związku z tym Data Lake Storage Gen1 zapewnia elastyczność korzystania z najlepszej wydajności z zalecaną opcją używania adl://lub obsługi istniejącego kodu przez kontynuowanie bezpośredniego używania interfejsu API WebHDFS. Usługa Azure HDInsight w pełni wykorzystuje AzureDataLakeFilesystem do zapewnienia najlepszej wydajności na Data Lake Storage Gen1.

Możesz uzyskać dostęp do danych w Data Lake Storage Gen1 przy użyciu programu `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` . Aby uzyskać więcej informacji na temat sposobu uzyskiwania dostępu do danych w Data Lake Storage Gen1, zobacz [Wyświetlanie właściwości przechowywanych danych](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-get-started-portal.md)
- [Wprowadzenie do Data Lake Storage Gen1 przy użyciu zestawu SDK platformy .NET](data-lake-store-get-started-net-sdk.md)
- [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)