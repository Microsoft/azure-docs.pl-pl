---
title: Platformy i narzędzia dla projektów analizy danych — proces nauki o danych zespołowych
description: Wyszczególniono i omawia zasoby dotyczące danych i analiz dostępne dla przedsiębiorstw, które są standaryzacją w zespołowym procesie nauki danych.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ba7b8af9b50b9173f5e2040bb8b623eeafdd538
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96453847"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformy i narzędzia dla projektów do nauki o danych

Firma Microsoft zapewnia pełen zakres zasobów analitycznych zarówno dla platformy w chmurze, jak i lokalnych. Można je wdrożyć, aby zapewnić wydajne i skalowalność projektów analizy danych. Wskazówki dla zespołów implementujących projekty analizy danych w sposób do śledzenia, kontroli wersji i współpracy są udostępniane przez [proces nauki o danych zespołowych](overview.md) (przetwarzania TDSP).  Aby zapoznać się z konspektem ról pracowników i skojarzonych z nimi zadań, które są obsługiwane przez zespół ds. analizy danych w tym procesie, zobacz temat [role i zadania w procesie nauki danych zespołu](roles-tasks.md).

Zasoby analityczne dostępne dla zespołów analizy danych korzystających z przetwarzania TDSP obejmują:

- Virtual Machines nauki o danych (zarówno w systemie Windows, jak i Linux CentOS)
- Klastry usługi HDInsight Spark
- Azure Synapse Analytics
- Azure Data Lake
- Klastry Hive usługi HDInsight
- Azure File Storage
- SQL Server 2019 R i usługi Python
- Azure Databricks

W tym dokumencie krótko opiszemy zasoby i oferujesz linki do samouczków i przewodniki po opublikowaniu zespołów przetwarzania TDSP. Mogą oni pomóc Ci dowiedzieć się, jak korzystać z nich krok po kroku i zacząć korzystać z nich do kompilowania inteligentnych aplikacji. Więcej informacji na temat tych zasobów jest dostępnych na stronach produktów. 

## <a name="data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM)

Maszyna wirtualna do nauki o danych oferowana zarówno w systemie Windows, jak i Linux przez firmę Microsoft zawiera popularne narzędzia do modelowania i projektowania analizy danych. Obejmuje to następujące narzędzia:

- Microsoft R Server Developer Edition 
- Dystrybucja Anaconda Python
- Notesy Jupyter dla języków Python i R 
- Visual Studio Community Edition z narzędziami Python i R w systemie Windows/zaćmienie w systemie Linux
- Power BI Desktop dla systemu Windows
- SQL Server 2016 Developer Edition w systemie Windows/Postgres w systemie Linux

Obejmuje to również **Narzędzia ml i AI** , takie jak xgboost, Mxnet i Vowpal Wabbit.

Obecnie DSVM jest dostępna w systemach operacyjnych **Windows** i **Linux CentOS** . Wybierz rozmiar DSVM (liczbę rdzeni procesora CPU i ilość pamięci) w zależności od potrzeb projektów analizy danych, które planujesz wykonać. 

Aby uzyskać więcej informacji na temat wersji systemu Windows DSVM, zobacz [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) w witrynie Azure Marketplace. W przypadku wersji systemu Linux DSVM należy zapoznać się z tematem [linux Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804).

Aby dowiedzieć się, jak wykonywać niektóre typowe zadania analizy danych na DSVM wydajnie, zobacz [10 rzeczy, które możesz wykonać na maszynie wirtualnej do analizy danych](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Klastry Azure HDInsight Spark

Apache Spark to platforma przetwarzania równoległego typu open source, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Aparat przetwarzania Spark jest zbudowany z myślą o szybkości, łatwości użycia i zaawansowanej analizie. Możliwości obliczeniowe w pamięci platformy Spark sprawiają, że jest dobrym rozwiązaniem dla iteracyjnych algorytmów uczenia maszynowego i obliczeń grafu. Platforma Spark jest również zgodna z usługą Azure Blob Storage (WASB), dzięki czemu istniejące dane przechowywane na platformie Azure można łatwo przetwarzać przy użyciu platformy Spark.

Tworząc klaster Spark w usłudze HDInsight, tworzysz zasoby obliczeniowe systemu Azure z zainstalowaną i skonfigurowaną platformą Spark. Utworzenie klastra Spark w usłudze HDInsight trwa około 10 minut. Przechowywanie danych do przetworzenia w usłudze Azure Blob Storage. Aby uzyskać informacje na temat korzystania z usługi Azure Blob Storage z klastrem, zobacz [Korzystanie z usługi Azure Blob Storage zgodnej z systemem plików HDFS w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

Zespół przetwarzania TDSP od firmy Microsoft opublikował dwa kompleksowe przewodniki dotyczące korzystania z klastrów Azure HDInsight Spark do kompilowania rozwiązań do nauki o danych, a przy użyciu języka Python i innych Scala. Aby uzyskać więcej informacji na temat **klastrów** usługi Azure HDInsight Spark, zobacz [Omówienie: Apache Spark w usłudze HDInsight w systemie Linux](../../hdinsight/spark/apache-spark-overview.md). Aby dowiedzieć się, jak utworzyć rozwiązanie do nauki o danych przy użyciu języka **Python** w klastrze Azure HDInsight Spark, zobacz [Omówienie analizy danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md). Aby dowiedzieć się, jak utworzyć rozwiązanie do nauki o danych przy użyciu usługi **Scala** w klastrze Azure HDInsight Spark, zobacz [naukę danych przy użyciu Scala i platformy Spark na platformie Azure](scala-walkthrough.md). 


##  <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Usługa Azure Synapse Analytics umożliwia łatwe skalowanie zasobów obliczeniowych w kilka sekund bez konieczności nadmiernej aprowizacji lub nadmiernej płatności. Oferuje również unikatową opcję wstrzymania korzystania z zasobów obliczeniowych, co pozwala na lepsze zarządzanie kosztami chmury. Możliwość wdrażania skalowalnych zasobów obliczeniowych umożliwia przenoszenie wszystkich danych do usługi Azure Synapse Analytics. Koszty magazynu są minimalne i można uruchamiać obliczenia tylko dla części zestawów danych, które mają być analizowane. 

Aby uzyskać więcej informacji na temat usługi Azure Synapse Analytics, zobacz witrynę sieci Web [usługi Azure Synapse Analytics](https://azure.microsoft.com/services/sql-data-warehouse) . Aby dowiedzieć się, jak tworzyć kompleksowe rozwiązania zaawansowanej analizy za pomocą usługi Azure Synapse Analytics, zobacz [proces analizy danych zespołu w działaniu: korzystanie z usługi Azure Synapse Analytics](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake jest jako repozytorium w całym przedsiębiorstwie dla każdego typu danych zbieranych w jednej lokalizacji, przed wszelkimi formalnymi wymaganiami lub nakładanymi schematami. Ta elastyczność umożliwia przechowywanie każdego typu danych w usłudze Data Lake, niezależnie od jego rozmiaru lub struktury oraz od tego, jak jest on pobierany. Organizacje mogą następnie używać usługi Hadoop lub zaawansowanej analizy, aby znaleźć wzorce w tych jeziorach danych. Jeziora dotyczące danych mogą również stanowić repozytorium na potrzeby przygotowania danych z niższymi kosztami przed nadzorem danych i przeniesieniem ich do magazynu danych.

Aby uzyskać więcej informacji na temat Azure Data Lake, zobacz [wprowadzenie Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Aby dowiedzieć się, jak utworzyć skalowalne, kompleksowe rozwiązanie do nauki o danych Azure Data Lake, zobacz [skalowalne analizy danych w Azure Data Lake: kompleksowy przewodnik](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Klastry Hive usługi Azure HDInsight (Hadoop)

Apache Hive to system magazynu danych dla usługi Hadoop, który umożliwia podsumowywanie danych, wykonywanie zapytań oraz analizowanie danych przy użyciu HiveQL, języka zapytań podobnego do SQL. Hive może służyć do interaktywnego eksplorowania danych lub tworzenia zadań przetwarzania wsadowego wielokrotnego użytku.

Program Hive umożliwia tworzenie struktury projektu na danych o dużej strukturze. Po zdefiniowaniu struktury można użyć programu Hive do wykonywania zapytań dotyczących danych w klastrze usługi Hadoop bez konieczności korzystania z programu, a nawet znajomości języka Java lub MapReduce. HiveQL (język zapytań Hive) umożliwia pisanie zapytań zawierających instrukcje podobne do języka T-SQL.

W przypadku analityków danych program Hive może uruchamiać funkcje User-Defined języka Python (UDF) w zapytaniach programu Hive w celu przetworzenia rekordów. Ta możliwość znacznie rozszerza możliwości zapytań Hive w analizie danych. W odróżnieniu od analityków danych może przeprowadzenie skalowalnej inżynierii funkcji w językach, w których są one najczęściej znane: takie jak HiveQL i Python języka SQL. 

Aby uzyskać więcej informacji na temat klastrów Hive usługi Azure HDInsight, zobacz [Używanie technologii Hive i HiveQL z usługą Hadoop w usłudze HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Aby dowiedzieć się, jak utworzyć skalowalne, kompleksowe rozwiązanie do nauki o danych za pomocą klastrów Hive usługi Azure HDInsight, zobacz [proces analizy danych zespołu w akcji: korzystanie z klastrów usługi HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage to usługa oferująca udziały plików w chmurze przy użyciu standardowego protokołu bloku komunikatów serwera (SMB). Obsługiwane są wersje 2.1 i 3.0 protokołu SMB. W usłudze Magazyn plików Azure można migrować starsze aplikacje korzystające z udziałów plików na platformę Azure szybko i bez kosztownych modyfikacji oprogramowania. Aplikacje uruchomione na maszynach wirtualnych lub w ramach usług w chmurze platformy Azure, a także na klientach lokalnych mogą instalować udziały plików w chmurze tak samo jak aplikacja na komputerze instalująca typowy udział SMB. Dowolna liczba składników aplikacji może następnie równocześnie zainstalować udział Magazynu plików i uzyskiwać do niego dostęp.

Szczególnie przydatne w przypadku projektów analizy danych jest możliwość utworzenia magazynu plików platformy Azure jako miejsca do udostępniania danych projektu członkom zespołu projektu. Każdy z nich uzyskuje dostęp do tej samej kopii danych w usłudze Azure File Storage. Mogą również używać tego magazynu plików do udostępniania zestawów funkcji generowanych podczas wykonywania projektu. Jeśli projekt jest zakontraktowaniem klienta, klienci mogą utworzyć magazyn plików platformy Azure w ramach własnej subskrypcji platformy Azure, aby udostępnić Ci dane i funkcje projektu. W ten sposób klient ma pełną kontrolę nad zasobami danych projektu. Aby uzyskać więcej informacji na temat usługi Azure File Storage, zobacz [Rozpoczynanie pracy z usługą Azure File Storage w systemie Windows](../../storage/files/storage-dotnet-how-to-use-files.md) oraz [jak korzystać z usługi Azure File Storage z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R i usługi Python

Usługa R Services (in-Database) udostępnia platformę do tworzenia i wdrażania inteligentnych aplikacji, które mogą odkrywać nowe informacje. Możesz użyć rozbudowanego i zaawansowanego języka R, w tym wielu pakietów dostarczonych przez społeczność R, aby tworzyć modele i generować prognozy na podstawie danych SQL Server. Ponieważ usługi R (in-Database) integrują język R z SQL Server, analiza jest bliska danych, co eliminuje koszty i zagrożenia bezpieczeństwa związane z przeniesieniem danych.

Usługa r Services (in-Database) obsługuje język R typu open source z kompleksowym zestawem SQL Server narzędzi i technologii. Oferują one najwyższą wydajność, bezpieczeństwo, niezawodność i możliwości zarządzania. Możesz wdrażać rozwiązania języka R przy użyciu wygodnych i dobrze znanych narzędzi. Aplikacje produkcyjne mogą wywołać środowisko uruchomieniowe języka R i pobrać prognozy i wizualizacje przy użyciu języka Transact-SQL. Możesz również użyć bibliotek skalowania, aby zwiększyć skalę i wydajność rozwiązań R. Aby uzyskać więcej informacji, zobacz [SQL Server R Services](/sql/advanced-analytics/r/sql-server-r-services).

Zespół przetwarzania TDSP od firmy Microsoft opublikował dwa kompleksowe przewodniki, które pokazują, jak tworzyć rozwiązania do nauki o danych w SQL Server 2016 R Services: jeden dla programistów języka R i jeden dla deweloperów SQL. Dla **programistów języka R**, zobacz [Przewodnik po kompleksowej](/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)analizie danych. W przypadku **deweloperów SQL** zobacz [w temacie Advanced Analytics Database for SQL Developers (samouczek)](/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Dodatek: narzędzia do konfigurowania projektów do nauki o danych

### <a name="install-git-credential-manager-on-windows"></a>Instalowanie programu Git Credential Manager w systemie Windows

Jeśli korzystasz z usługi przetwarzania TDSP w **systemie Windows**, musisz zainstalować program **git Credential Manager (GCM)** , aby komunikować się z repozytoriami git. Aby zainstalować GCM, należy najpierw zainstalować **Chocolaty**. Aby zainstalować Chocolaty i GCM, uruchom następujące polecenia w programie Windows PowerShell jako **administrator**:  

```powershell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
choco install git-credential-manager-for-windows -y
```  

### <a name="install-git-on-linux-centos-machines"></a>Instalowanie usługi Git na maszynach z systemem Linux (CentOS)

Uruchom następujące polecenie bash, aby zainstalować narzędzia Git na komputerach z systemem Linux (CentOS):

```powershell
sudo yum install git
```

### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generuj publiczny klucz SSH na maszynach z systemem Linux (CentOS)

Jeśli używasz maszyn z systemem Linux (CentOS) do uruchamiania poleceń git, musisz dodać publiczny klucz SSH maszyny do Azure DevOps Services, aby ten komputer został rozpoznany przez Azure DevOps Services. Najpierw należy wygenerować publiczny klucz SSH i dodać klucz do publicznych kluczy SSH na stronie ustawień zabezpieczeń Azure DevOps Services. 

1. Aby wygenerować klucz SSH, uruchom następujące dwa polecenia: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Polecenia służące do generowania klucza SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Skopiuj cały klucz SSH, w tym *SSH-RSA*. 
1. Zaloguj się do Azure DevOps Services. 
1. Kliknij **<swoją nazwę \>** w prawym górnym rogu strony, a następnie kliknij pozycję **zabezpieczenia**. 
    
   ![Kliknij swoją nazwę, a następnie kliknij pozycję Zabezpieczenia.](./media/platforms-and-tools/resources-2-user-setting.png)

1. Kliknij pozycję **klucze publiczne SSH**, a następnie kliknij pozycję **+ Dodaj**. 

   ![Kliknij pozycję publiczne klucze SSH, a następnie kliknij pozycję + Dodaj.](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Wklej klucz SSH skopiowany do pola tekstowego i Zapisz.


## <a name="next-steps"></a>Następne kroki

Dostępne są również pełne przewodniki pokazujące wszystkie kroki procesu dla **konkretnych scenariuszy** . Są one wyświetlane i połączone z opisami miniatur w temacie [przykładowe](walkthroughs.md) wskazówki. Ilustrują one sposób łączenia chmur, narzędzi lokalnych i usług w przepływ pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami, które pokazują, jak wykonać kroki w procesie nauki danych zespołu przy użyciu Azure Machine Learning Studio (klasyczny), zobacz ścieżkę [usługi Azure ml](./index.yml) Learning.