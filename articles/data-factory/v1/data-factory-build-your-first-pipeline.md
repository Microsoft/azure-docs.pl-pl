---
title: 'Samouczek Data Factory: pierwszy potok danych '
description: W tym Azure Data Factory samouczku pokazano, jak utworzyć i zaplanować fabrykę danych, która przetwarza dane przy użyciu skryptu Hive w klastrze usługi Hadoop.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 80644ed2d655544fa176a7be92aec3c01aa3bf14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75966074"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Samouczek: Tworzenie pierwszego potoku do przekształcania danych przy użyciu klastra Hadoop
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-build-your-first-pipeline.md)
> * [Program Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [Program PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Szablon usługi Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [Interfejs API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Szybki start: tworzenie fabryki danych przy użyciu usługi Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

W tym samouczku utworzysz pierwszą fabrykę danych Azure przy użyciu potoku danych. Potok przekształca dane wejściowe przez uruchomienie skryptu Hive w klastrze usługi Azure HDInsight (Hadoop) w celu wygenerowania danych wyjściowych.  

Ten artykuł zawiera omówienie i wymagania wstępne dotyczące samouczka. Po spełnieniu wymagań wstępnych można wykonać samouczek przy użyciu jednego z następujących narzędzi/zestawów SDK: Visual Studio, PowerShell, Menedżer zasobów Template, interfejsu API REST. Wybierz jedną z opcji z listy rozwijanej na początku lub na końcu tego artykułu, aby wykonać samouczek przy użyciu jednej z tych opcji.    

## <a name="tutorial-overview"></a>Omówienie samouczka
Ten samouczek obejmuje wykonanie następujących kroków:

1. Utwórz **fabrykę danych**. Fabryka danych może zawierać co najmniej jeden potok danych służący do przenoszenia i przekształcania danych.

    W tym samouczku utworzysz jeden potok w fabryce danych.
2. Utwórz **potok**. Potok może zawierać jedno lub więcej działań (przykłady: działanie Copy, działanie Hive usługi HDInsight). Ten przykład używa działania Hive usługi HDInsight, które uruchamia skrypt programu Hive w klastrze usługi HDInsight Hadoop. Skrypt najpierw tworzy tabelę, która odwołuje się do danych nieprzetworzonych dziennika sieci Web przechowywanych w usłudze Azure Blob Storage, a następnie dzieli dane pierwotne według roku i miesiąca.

    W tym samouczku potok używa działania programu Hive do przekształcania danych przez uruchomienie zapytania programu Hive w klastrze Azure HDInsight Hadoop.
3. Utwórz **połączone usługi**. Utworzysz połączoną usługę, aby połączyć magazyn danych lub usługę obliczeniową z fabryką danych. Magazyn danych, taki jak usługa Azure Storage, przechowuje dane wejściowe/wyjściowe działań w potoku. Usługa obliczeniowa, taka jak klaster Hadoop usługi HDInsight, przetwarza/przekształca dane.

    W tym samouczku utworzysz dwie połączone usługi: **Azure Storage** i **Azure HDInsight**. Połączona usługa Azure Storage łączy konto usługi Azure Storage, które przechowuje dane wejściowe/wyjściowe do fabryki danych. Połączona usługa Azure HDInsight łączy klaster usługi Azure HDInsight, który jest używany do przekształcania danych w fabrykę danych.
3. Tworzenie wejściowych i wyjściowych **zestawów**danych. Zestaw danych wejściowych reprezentuje dane wejściowe dla działania w potoku, a zestaw danych wyjściowych reprezentuje dane wyjściowe dla działania.

    W tym samouczku zestawy danych wejściowych i wyjściowych określają lokalizacje danych wejściowych i wyjściowych w Blob Storage platformy Azure. Połączona usługa Azure Storage określa używane konto usługi Azure Storage. Wejściowy zestaw danych określa, gdzie znajdują się pliki wejściowe, a wyjściowy zestaw danych określa, gdzie są umieszczane pliki wyjściowe.


Szczegółowe omówienie Azure Data Factory [można znaleźć w artykule wprowadzenie do Azure Data Factory](data-factory-introduction.md) artykułu.

Oto **Widok diagramu** przykładowej fabryki danych, która została utworzona w tym samouczku. **MyFirstPipeline** ma jedno działanie typu Hive, które wykorzystuje zestaw danych **AzureBlobInput** jako dane wejściowe i tworzy zestaw danych **AzureBlobOutput** jako dane wyjściowe.

![Widok diagramu w samouczku Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


W tym samouczku folder **inputdata** kontenera obiektów blob platformy Azure **adfgetstarted** zawiera jeden plik o nazwie Input. log. Ten plik dziennika zawiera wpisy z trzech miesięcy: styczeń, luty i Marzec z 2016. Oto przykładowe wiersze dla każdego miesiąca w pliku wejściowym.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Gdy plik jest przetwarzany przez potok z działaniem Hive usługi HDInsight, działanie uruchamia skrypt programu Hive w klastrze usługi HDInsight, który dzieli dane wejściowe według roku i miesiąca. Skrypt tworzy trzy foldery wyjściowe zawierające plik z wpisami z każdego miesiąca.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Z pokazanych powyżej wierszy przykładu pierwsza z nich (z 2016-01-01) jest zapisywana w pliku 000000_0 w folderze month = 1. Podobnie drugi jest zapisywana w pliku w folderze Month = 2, a trzeci jest zapisywana w pliku w folderze Month = 3.  

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z tym samouczkiem należy spełnić następujące wymagania wstępne:

1. **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Zapoznaj się z artykułem [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/) , aby uzyskać bezpłatne konto próbne.
2. **Azure Storage** — używasz konta usługi Azure Storage do przechowywania danych w tym samouczku. Jeśli nie masz konta usługi Azure Storage, zobacz artykuł [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) . Po utworzeniu konta magazynu Zanotuj **nazwę konta** i **klucz dostępu**. Aby uzyskać informacje na temat pobierania kluczy dostępu do konta magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
3. Pobierz i przejrzyj plik zapytania programu Hive (**HQL**) znajdujący się w lokalizacji: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql) . To zapytanie przekształca dane wejściowe w celu wygenerowania danych wyjściowych.
4. Pobierz i przejrzyj przykładowy plik wejściowy (**Input. log**) znajdujący się w lokalizacji: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Utwórz kontener obiektów BLOB o nazwie **adfgetstarted** w BLOB Storage platformy Azure.
6. Przekaż plik **partitionweblogs. HQL** do folderu **Script** w kontenerze **adfgetstarted** . Użyj narzędzi, takich jak [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/).
7. Przekaż plik **Input. log** do folderu **inputdata** w kontenerze **adfgetstarted** .

Po zakończeniu wymagań wstępnych wybierz jeden z następujących narzędzi/zestawów SDK, aby wykonać samouczek:

- [Program Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Program PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Szablon usługi Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [Interfejs API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Program Visual Studio udostępnia graficzny interfejs użytkownika umożliwiający tworzenie fabryk danych. W programie PowerShell, szablon Menedżer zasobów i opcje interfejsu API REST udostępniają skrypty/programowanie w celu tworzenia fabryk danych.

> [!NOTE]
> Potok danych przedstawiony w tym samouczku przekształca dane wejściowe w celu wygenerowania danych wyjściowych. Nie kopiuje on danych ze źródłowego do docelowego magazynu danych. Aby zapoznać się z samouczkiem dotyczącym kopiowania danych przy użyciu usługi Azure Data Factory, zobacz [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: Kopiowanie danych z usługi Blob Storage do usługi SQL Database).
>
> Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Szczegółowe informacje znajdują się w artykule [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Planowanie i wykonywanie w usłudze Data Factory).
