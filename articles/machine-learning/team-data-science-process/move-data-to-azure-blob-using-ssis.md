---
title: Przenoszenie danych magazynu obiektów BLOB za pomocą łączników usług SSIS — proces nauki o danych zespołowych
description: Dowiedz się, jak przenosić dane do lub z usługi Azure Blob Storage przy użyciu pakietu SQL Server Integration Services Feature Pack dla platformy Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9572d612e7ee8e2fd72850ba14447e8449f0f371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322011"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Przenoszenie danych do i z usługi Azure Blob Storage przy użyciu łączników usług SSIS
[Pakiet SQL Server Integration Services Feature Pack dla platformy Azure](/sql/integration-services/azure-feature-pack-for-integration-services-ssis) udostępnia składniki umożliwiające łączenie się z platformą Azure, przenoszenie danych między platformą Azure i lokalnymi źródłami danych oraz przetwarzanie danych przechowywanych na platformie Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Po przeniesieniu danych lokalnych do chmury klienci mogą uzyskać dostęp do swoich danych z dowolnej usługi platformy Azure, aby wykorzystać pełną moc zestawu technologii platformy Azure. Dane mogą być następnie używane, na przykład w Azure Machine Learning lub w klastrze usługi HDInsight.

Przykłady dotyczące korzystania z tych zasobów platformy Azure znajdują się w przewodnikach [SQL](sql-walkthrough.md) i [HDInsight](hive-walkthrough.md) .

Omówienie scenariuszy kanonicznych, które używają usług SSIS do realizacji potrzeb firmy wspólnych w scenariuszach integracji danych hybrydowych, można znaleźć [w temacie więcej dzięki SQL Server Integration Services Feature Pack for Azure](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238) blog.

> [!NOTE]
> Pełny Wprowadzenie do usługi Azure Blob Storage można znaleźć w [temacie Podstawowe informacje](../../storage/blobs/storage-quickstart-blobs-dotnet.md) dotyczące usługi Azure BLOB i [usłudze Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać zadania opisane w tym artykule, musisz mieć skonfigurowaną subskrypcję platformy Azure i konto usługi Azure Storage. Aby przekazać lub pobrać dane, potrzebna jest nazwa konta i klucz konta usługi Azure Storage.

* Aby skonfigurować **subskrypcję platformy Azure**, zobacz [bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Instrukcje dotyczące tworzenia **konta magazynu** i uzyskiwania informacji o kontach i kluczach znajdują się w temacie [Informacje o kontach usługi Azure Storage](../../storage/common/storage-account-create.md).

Aby korzystać z **łączników usług SSIS**, należy pobrać następujące polecenie:

* **SQL Server 2014 lub 2016 Standard (lub nowszy)**: instalacja zawiera SQL Server Integration Services.
* **Microsoft SQL Server 2014 lub 2016 Integration Services Feature Pack for Azure**: te łączniki można odpowiednio pobrać z witryny [SQL Server 2014 integration services](https://www.microsoft.com/download/details.aspx?id=47366) i [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) .

> [!NOTE]
> Program SSIS jest instalowany z SQL Server, ale nie jest uwzględniony w wersji Express. Aby uzyskać informacje o aplikacjach uwzględnionych w różnych wersjach SQL Server, zobacz [wersje SQL Server](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Aby zapoznać się z materiałami szkoleniowymi dotyczącymi usług SSIS, zobacz [praktyczne uczenie usług SSIS](https://www.microsoft.com/sql-server/training-certification)

Aby uzyskać informacje na temat sposobu tworzenia prostych pakietów wyodrębniania, transformacji i ładowania (ETL) przy użyciu programu SISS, zobacz [samouczek usług SSIS: Tworzenie prostego pakietu ETL](/sql/integration-services/ssis-how-to-create-an-etl-package).

## <a name="download-nyc-taxi-dataset"></a>Pobierz zestaw danych NYC taksówki
W przykładzie opisanym w tym miejscu użyto publicznie dostępnego zestawu danych — zestawu danych [podróży z NYC taksówkami](https://www.andresmh.com/nyctaxitrips/) . Zestaw danych składa się z około 173 000 000 taksówki kolarstwu w NYC roku 2013. Istnieją dwa typy danych: informacje o wykorzystaniu danych i taryfy czasowej. Ponieważ istnieje plik dla każdego miesiąca, mamy 24 pliki, z których każdy jest około 2 GB nieskompresowany.

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do usługi Azure Blob Storage
Aby przenieść dane za pomocą pakietu SSIS Feature Pack z lokalnego do magazynu obiektów blob platformy Azure, użyjemy wystąpienia [**zadania przekazywania obiektów blob platformy Azure**](/sql/integration-services/control-flow/azure-blob-upload-task), pokazanego tutaj:

![Konfigurowanie maszyny wirtualnej do analizy danych](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Parametry używane przez zadanie są opisane tutaj:

| Pole | Opis |
| --- | --- |
| **AzureStorageConnection** |Określa istniejącego Menedżera połączeń usługi Azure Storage lub tworzy nowy, który odwołuje się do konta usługi Azure Storage, które wskazuje lokalizację, w której są hostowane pliki obiektów BLOB. |
| **BlobContainer** |Określa nazwę kontenera obiektów blob, który przechowuje przekazane pliki jako obiekty blob. |
| **BlobDirectory** |Określa katalog obiektów blob, w którym załadowany plik jest przechowywany jako blokowy obiekt BLOB. Katalog obiektów BLOB jest wirtualną strukturą hierarchiczną. Jeśli obiekt BLOB już istnieje, zastępuje go. |
| **LocalDirectory** |Określa katalog lokalny, który zawiera pliki do przekazania. |
| **Nazwa pliku** |Określa filtr nazw w celu wybrania plików o określonym wzorcu nazwy. Na przykład \* plik. xls \* zawiera pliki, takie jak MySheet001.xls i MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Określa filtr zakresu czasu. Pliki zmodyfikowane po *TimeRangeFrom* i przed *TimeRangeTo* są uwzględniane. |

> [!NOTE]
> Poświadczenia **AzureStorageConnection** muszą być poprawne, a **BlobContainer** musi istnieć przed próbą przeniesienia.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Pobieranie danych z usługi Azure Blob Storage
Aby pobrać dane z usługi Azure Blob Storage do magazynu lokalnego przy użyciu usług SSIS, użyj wystąpienia [zadania pobierania obiektów blob platformy Azure](/sql/integration-services/control-flow/azure-blob-download-task).

## <a name="more-advanced-ssis-azure-scenarios"></a>Bardziej zaawansowane scenariusze SSIS-Azure
Pakiet Feature Pack dla usług SSIS umożliwia obsługę bardziej złożonych przepływów przez pakowanie zadań jednocześnie. Na przykład dane obiektów BLOB mogą być przekazywane bezpośrednio do klastra usługi HDInsight, którego dane wyjściowe można pobrać z powrotem do obiektu BLOB, a następnie do magazynu lokalnego. W przypadku usług SSIS można uruchamiać zadania Hive i świńskie w klastrze usługi HDInsight przy użyciu dodatkowych łączników usług SSIS:

* Aby uruchomić skrypt Hive w klastrze usługi Azure HDInsight przy użyciu usług SSIS, użyj [zadania Hive usługi Azure HDInsight](/sql/integration-services/control-flow/azure-hdinsight-hive-task).
* Aby uruchomić skrypt wieprzowy w klastrze usługi Azure HDInsight przy użyciu usług SSIS, użyj [zadania trzody chlewnej usługi Azure HDInsight](/sql/integration-services/control-flow/azure-hdinsight-pig-task).