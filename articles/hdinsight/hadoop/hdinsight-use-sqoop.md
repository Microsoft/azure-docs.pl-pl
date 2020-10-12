---
title: Uruchamianie zadań Apache Sqoop w usłudze Azure HDInsight (Apache Hadoop)
description: Dowiedz się, jak używać Azure PowerShell z stacji roboczej do uruchamiania Sqoop importowania i eksportowania między klastrem usługi Hadoop i bazą danych Azure SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: 165b7d00c3cf307e7996e84a35bb2a202f448cc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86076882"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Korzystanie z programu Apache Sqoop za pomocą platformy Hadoop w usłudze HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak importować i eksportować dane między klastrem usługi HDInsight a Azure SQL Database za pomocą usługi Apache Sqoop w usłudze HDInsight.

Chociaż Apache Hadoop jest naturalnym wyborem do przetwarzania danych bez struktury i z częściową strukturą, takich jak dzienniki i pliki, może być również konieczna przetwarzanie danych strukturalnych przechowywanych w relacyjnych bazach danych.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) to narzędzie przeznaczone do przesyłania danych między klastrami usługi Hadoop a relacyjnymi bazami danych. Można go użyć do zaimportowania danych z systemu zarządzania relacyjnymi bazami danych (RDBMS), takiego jak SQL Server, MySQL lub Oracle do rozproszonego systemu plików Hadoop (HDFS), Przekształć dane w usłudze Hadoop z MapReduce lub Apache Hive, a następnie wyeksportować dane z powrotem do RDBMS. W tym artykule są używane Azure SQL Database do relacyjnej bazy danych.

> [!IMPORTANT]  
> W tym artykule opisano środowisko testowe do przeprowadzenia transferu danych. Następnie wybierz metodę transferu danych dla tego środowiska z jednej z metod w sekcji [Uruchamianie zadań Sqoop](#run-sqoop-jobs)w dalszej części.

Aby poznać wersje Sqoop, które są obsługiwane w klastrach usługi HDInsight, zobacz [co nowego w wersjach klastra dostarczonych przez usługi HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Omówienie scenariusza

Klaster usługi HDInsight zawiera kilka przykładowych danych. Używasz dwóch następujących próbek:

* Plik dziennika Apache Log4J, który znajduje się w lokalizacji `/example/data/sample.log` . Następujące dzienniki są wyodrębniane z pliku:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Tabela programu Hive o nazwie `hivesampletable` , która odwołuje się do pliku danych znajdującego się w `/hive/warehouse/hivesampletable` . Tabela zawiera niektóre dane urządzeń przenośnych.
  
  | Pole | Typ danych |
  | --- | --- |
  | clientid |ciąg |
  | querytime |ciąg |
  | rynek |ciąg |
  | deviceplatform |ciąg |
  | devicemake |ciąg |
  | devicemodel |ciąg |
  | stan |ciąg |
  | country |ciąg |
  | querydwelltime |double |
  | SessionID |bigint |
  | sessionpagevieworder |bigint |

W tym artykule opisano te dwa zestawy danych do testowania Sqoop import i Export.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Konfigurowanie środowiska testowego

Klaster, baza danych SQL i inne obiekty są tworzone za pośrednictwem Azure Portal przy użyciu szablonu Azure Resource Manager. Szablon można znaleźć w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Szablon Menedżer zasobów wywołuje pakiet BACPAC, aby wdrożyć schematy tabeli w bazie danych SQL.  Pakiet BACPAC znajduje się w publicznym kontenerze obiektów blob, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac . Jeśli chcesz użyć prywatnego kontenera dla plików BACPAC, użyj następujących wartości w szablonie:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importowanie przy użyciu szablonu lub Azure Portal obsługuje tylko importowanie pliku BACPAC z usługi Azure Blob Storage.

1. Wybierz Poniższy obraz, aby otworzyć szablon Menedżer zasobów w Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Wprowadź następujące właściwości:

    |Pole |Wartość |
    |---|---|
    |Subskrypcja |Wybierz swoją subskrypcję platformy Azure z listy rozwijanej.|
    |Grupa zasobów |Wybierz grupę zasobów z listy rozwijanej lub Utwórz nową|
    |Lokalizacja |Wybierz region z listy rozwijanej.|
    |Nazwa klastra |Wprowadź nazwę klastra usługi Hadoop. Używaj tylko małych liter.|
    |Nazwa użytkownika logowania klastra |Zachowaj wstępnie wypełnioną wartość `admin` .|
    |Hasło logowania klastra |Wprowadź hasło.|
    |Nazwa użytkownika SSH |Zachowaj wstępnie wypełnioną wartość `sshuser` .|
    |Hasło ssh |Wprowadź hasło.|
    |Logowanie administratora SQL |Zachowaj wstępnie wypełnioną wartość `sqluser` .|
    |Hasło administratora SQL |Wprowadź hasło.|
    |Lokalizacja _artifacts | Użyj wartości domyślnej, chyba że chcesz użyć własnego pliku BACPAC w innej lokalizacji.|
    |Token SAS lokalizacji _artifacts |Pozostaw to pole puste.|
    |Nazwa pliku BACPAC |Użyj wartości domyślnej, chyba że chcesz użyć własnego pliku BACPAC.|
    |Lokalizacja |Użyj wartości domyślnej.|

    Nazwa [serwera logicznego SQL](../../azure-sql/database/logical-servers.md)  `<ClusterName>dbserver` . Nazwa bazy danych `<ClusterName>db` . Domyślną nazwą konta magazynu będzie `e6qhezrh2pdqu` .

3. Wybierz opcję **Akceptuję warunki i postanowienia podane powyżej**.

4. Wybierz pozycję **Kup**. Zostanie wyświetlony nowy kafelek zatytułowany przesyłanie wdrożenia dla Template deployment. Utworzenie klastra i bazy danych SQL trwa około 20 minut.

## <a name="run-sqoop-jobs"></a>Uruchamianie zadań Sqoop

Usługa HDInsight może uruchamiać zadania Sqoop przy użyciu różnych metod. Skorzystaj z poniższej tabeli, aby zdecydować, która metoda jest dla Ciebie odpowiednia, a następnie postępuj zgodnie z linkiem do przewodnika.

| **Użyj tego** , jeśli chcesz... | ... powłoka **interaktywna** | ... przetwarzanie **wsadowe** | ... z tego **systemu operacyjnego klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [Protokół SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, UNIX, Mac OS X lub Windows |
| [Zestaw SDK dla platformy .NET usługi Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (na razie) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Ograniczenia

* Eksport zbiorczy — za pomocą usługi HDInsight opartej na systemie Linux łącznik Sqoop używany do eksportowania danych do Microsoft SQL Server lub SQL Database nie obsługuje obecnie operacji wstawiania zbiorczego.
* Przetwarzanie wsadowe — za pomocą usługi HDInsight opartej na systemie Linux, gdy podczas wykonywania operacji wstawiania jest używany `-batch` przełącznik, Sqoop wykonuje wielokrotne wstawienia zamiast wsadowe operacje wstawiania.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak używać programu Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Korzystanie z programu Apache Hive z usługą HDInsight](../hdinsight-use-hive.md)
* [Przekazywanie danych do usługi HDInsight](../hdinsight-upload-data.md): Znajdź inne metody przekazywania danych do magazynu HDInsight/Azure Blob Storage.
* [Importowanie i eksportowanie danych między narzędziem Apache Hadoop w usłudze HDInsight i usługą SQL Database przy użyciu narzędzia Apache Sqoop](./apache-hadoop-use-sqoop-mac-linux.md)
