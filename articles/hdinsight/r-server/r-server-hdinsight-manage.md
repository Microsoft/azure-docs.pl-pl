---
title: Zarządzanie klastrem usługi ML w usłudze HDInsight — Azure
description: Dowiedz się, jak zarządzać różnymi zadaniami w klastrze usług ML w usłudze Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/19/2019
ms.openlocfilehash: e4c9124ebd0b61b8db1b1da964355a3c36b5bba5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930567"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Zarządzanie klastrem usług ML w usłudze Azure HDInsight

W tym artykule dowiesz się, jak zarządzać istniejącym klastrem usług w usłudze Azure HDInsight w celu wykonywania zadań, takich jak dodawanie wielu równoczesnych użytkowników, nawiązywanie zdalnego połączenia z klastrem usługi ML, zmiana kontekstu obliczeniowego itp.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi ML w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję **usługi ml** dla **typu klastra**.

* Klient protokołu Secure Shell (SSH): klient SSH jest używany do zdalnego łączenia z klastrem usługi HDInsight i uruchamiania poleceń bezpośrednio w klastrze. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH z usługą HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="enable-multiple-concurrent-users"></a>Włączanie obsługi równoczesnych użytkowników

Można włączyć wielu współbieżnych użytkowników klastra usługi ML w usłudze HDInsight, dodając więcej użytkowników do węzła brzegowego, na którym działa wersja Community RStudio. Podczas tworzenia klastra usługi HDInsight musisz podać dwóch użytkowników: użytkownika HTTP i użytkownika SSH:

![HDI Azure Portal parametry logowania](./media/r-server-hdinsight-manage/hdi-concurrent-users1.png)

- **Nazwa użytkownika logowania klastra**: użytkownik HTTP uwierzytelniany za pośrednictwem bramy HDInsight, która umożliwia ochronę utworzonych klastrów usługi HDInsight. Ten użytkownik HTTP służy do uzyskiwania dostępu do interfejsu użytkownika Apache Ambari, Apache Hadoop interfejsu użytkownika PRZĘDZy oraz innych składników interfejsu użytkownika.
- **Nazwa użytkownika protokołu SSH (Secure Shell)**: użytkownik SSH zapewniający dostęp do klastra za pośrednictwem protokołu Secure Shell. Jest to użytkownik systemu Linux, który ma dostęp do wszystkich węzłów głównych, węzłów procesu roboczego oraz węzłów krawędzi. Pozwala to na korzystanie z dowolnego węzła klastra zdalnego za pomocą protokołu Secure Shell.

Wersja społeczności programu R Studio Server używana w klastrze usługi ML w usłudze HDInsight akceptuje tylko nazwę użytkownika systemu Linux i hasło jako mechanizm logowania. Przekazywanie tokenów nie jest obsługiwane. W przypadku próby uzyskania dostępu do programu R Studio po raz pierwszy w klastrze usługi ML należy zalogować się dwukrotnie.

- Najpierw Zaloguj się przy użyciu poświadczeń użytkownika HTTP za pośrednictwem bramy usługi HDInsight.

- Następnie zaloguj się do RStudio przy użyciu poświadczeń użytkownika protokołu SSH.
  
Aktualnie podczas aprowizowania klastra usługi HDInsight można utworzyć tylko jedno konto użytkownika SSH. Aby umożliwić wielu użytkownikom dostęp do klastra usług ML w usłudze HDInsight, należy utworzyć dodatkowych użytkowników w systemie Linux.

Ponieważ RStudio działa w węźle brzegowym klastra, należy wykonać kilka kroków tutaj:

1. Zalogowanie się do węzła brzegowego przy użyciu istniejącego użytkownika SSH
2. Dodaj użytkowników systemu Linux w węźle krawędzi
3. Przy pomocy utworzonego użytkownika możesz korzystać z programu RStudio Community

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Krok 1. użycie utworzonego użytkownika SSH do logowania się do węzła brzegowego

Postępuj zgodnie z instrukcjami w obszarze [Connect to HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) , aby uzyskać dostęp do węzła brzegowego. Adres węzła brzegowego klastra usług ML w usłudze HDInsight to `CLUSTERNAME-ed-ssh.azurehdinsight.net` .

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Krok 2. Dodawanie użytkowników systemu Linux w węźle krawędzi

Aby dodać użytkownika do węzła krawędzi, uruchom te polecenia:

```bash
# Add a user 
sudo useradd <yournewusername> -m

# Set password for the new user
sudo passwd <yournewusername>
```

Poniższy zrzut ekranu przedstawia dane wyjściowe.

![zrzut ekranu danych wyjściowych współbieżnych użytkowników](./media/r-server-hdinsight-manage/hdi-concurrent-users2.png)

Po wyświetleniu monitu o podanie "bieżące hasło protokołu Kerberos:" naciśnij klawisz **Enter** , aby go zignorować. Podanie opcji `-m` w poleceniu `useradd` powoduje, że system utworzy folder macierzysty użytkownika, wymagany przez program RStudio Community.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Krok 3. Korzystanie z programu RStudio Community przy pomocy utworzonego użytkownika

Dostęp do RStudio z `https://CLUSTERNAME.azurehdinsight.net/rstudio/` . Jeśli logujesz się po raz pierwszy po utworzeniu klastra, wprowadź poświadczenia administratora klastra, a następnie poświadczenia użytkownika SSH utworzone przez Ciebie. Jeśli nie jest to Twoje pierwsze logowanie, wprowadź tylko poświadczenia dla utworzonego użytkownika SSH.

Możesz również zalogować się przy użyciu oryginalnych poświadczeń (domyślnie jest to *sshuser*) współbieżnie z innego okna przeglądarki.

Pamiętaj, że nowo dodani użytkownicy nie mają uprawnień użytkownika root w systemie Linux, ale mają takie same prawa dostępu do wszystkich plików w magazynie zdalnym HDFS i WASB.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Zdalne nawiązywanie połączenia z usługami Microsoft ML

Dostęp do kontekstu obliczeniowego usługi HDInsight Spark można skonfigurować ze zdalnego wystąpienia klienta ML uruchomionego na pulpicie. W tym celu należy określić opcje (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches i sshProfileScript) podczas definiowania kontekstu obliczeniowego obliczeniowego rxspark na pulpicie: na przykład:

```r
myNameNode <- "default"
myPort <- 0

mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
mySshUsername  <- '<sshuser>'# HDI SSH username
mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

mySparkCluster <- RxSpark(
    hdfsShareDir = myhdfsShareDir,
    shareDir     = myShareDir,
    sshUsername  = mySshUsername,
    sshHostname  = mySshHostname,
    sshSwitches  = mySshSwitches,
    sshProfileScript = '/etc/profile',
    nameNode     = myNameNode,
    port         = myPort,
    consoleOutput= TRUE
)
```

Aby uzyskać więcej informacji, zobacz sekcję "Używanie Microsoft Machine Learning Server jako klient Apache Hadoop" w temacie [jak używać kolekcję funkcji revoscaler w kontekście obliczeń Apache Spark](/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Używanie kontekstu obliczeniowego

Kontekst obliczeniowy pozwala określić, czy obliczenia są wykonywane lokalnie w węźle krawędzi czy są rozproszone w węzłach klastra usługi HDInsight.  Aby zapoznać się z przykładem ustawiania kontekstu obliczeniowego z serwerem RStudio, zobacz [wykonywanie skryptu języka R w klastrze usługi ml w usłudze Azure HDInsight przy użyciu serwera RStudio](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Dystrybucja kodu R do wielu węzłów

Za pomocą usługi ML w usłudze HDInsight można korzystać z istniejącego kodu R i uruchamiać go w wielu węzłach klastra za pomocą programu `rxExec` . Funkcja ta jest przydatna podczas czyszczenia parametrów lub przeprowadzania symulacji. Poniższy kod przedstawia przykładowe użycie programu `rxExec`:

```r
rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
```

Jeśli nadal używasz kontekstu Spark, to polecenie zwraca wartość nodename dla węzłów procesu roboczego, `(Sys.info()["nodename"])` w których uruchomiono kod. Na przykład w klastrze z czterema węzłami oczekuje się, że dane wyjściowe są podobne do następującego fragmentu kodu:

```r
$rxElem1
    nodename
"wn3-mymlser"

$rxElem2
    nodename
"wn0-mymlser"

$rxElem3
    nodename
"wn3-mymlser"

$rxElem4
    nodename
"wn3-mymlser"
```

## <a name="access-data-in-apache-hive-and-parquet"></a>Dostęp do danych w Apache Hive i Parquet

Usługi HDInsight ML umożliwiają bezpośredni dostęp do danych w usłudze Hive i Parquet do użycia przez funkcje skalowania w kontekście obliczeń Spark. Te możliwości są dostępne za pomocą nowych funkcji źródła danych programu ScaleR o nazwie RxHiveData i RxParquetData, które używają kodu Spark SQL do ładowania danych bezpośrednio do elementów DataFrame aparatu Spark na potrzeby analizy przez program ScaleR.

Poniżej przedstawiono przykładowy kod korzystający z nowych funkcji:

```r
#Create a Spark compute context:
myHadoopCluster <- rxSparkConnect(reset = TRUE)

#Retrieve some sample data from Hive and run a model:
hiveData <- RxHiveData("select * from hivesampletable",
                       colInfo = list(devicemake = list(type = "factor")))
rxGetInfo(hiveData, getVarInfo = TRUE)

rxLinMod(querydwelltime ~ devicemake, data=hiveData)

#Retrieve some sample data from Parquet and run a model:
rxHadoopMakeDir('/share')
rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
pqData <- RxParquetData('/share/claimsParquet',
                        colInfo = list(
                            age    = list(type = "factor"),
                            car.age = list(type = "factor"),
                            type = list(type = "factor")
                        ) )
rxGetInfo(pqData, getVarInfo = TRUE)

rxNaiveBayes(type ~ age + cost, data = pqData)

#Check on Spark data objects, cleanup, and close the Spark session:
lsObj <- rxSparkListData() # two data objs are cached
lsObj
rxSparkRemoveData(lsObj)
rxSparkListData() # it should show empty list
rxSparkDisconnect(myHadoopCluster)
```

Aby uzyskać dodatkowe informacje na temat korzystania z tych nowych funkcji, zobacz Pomoc online w usłudze ML za pomocą `?RxHivedata` `?RxParquetData` poleceń i.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Instalowanie dodatkowych pakietów języka R w klastrze

### <a name="to-install-r-packages-on-the-edge-node"></a>Aby zainstalować pakiety języka R w węźle brzegowym

Jeśli chcesz zainstalować dodatkowe pakiety R w węźle brzegowym, możesz użyć `install.packages()` bezpośrednio z poziomu konsoli języka r, po połączeniu z węzłem brzegowym za pośrednictwem protokołu SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Aby zainstalować pakiety języka R w węźle procesu roboczego

Aby zainstalować pakiety języka R na węzłach procesu roboczego klastra, musisz użyć akcji skryptu. Akcje skryptu to skrypty powłoki Bash używane do wprowadzania zmian w konfiguracji klastra usługi HDInsight lub instalowania dodatkowego oprogramowania, np. pakietów R. 

> [!IMPORTANT]  
> Dodatkowe pakiety R można zainstalować przy użyciu akcji skryptu dopiero po utworzeniu klastra. Nie należy używać tej procedury podczas tworzenia klastra, ponieważ skrypt korzysta z całkowicie skonfigurowanych usług ML.

1. Wykonaj kroki opisane w sekcji [Dostosowywanie klastrów za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

3. W przypadku **akcji przesyłania skryptu** podaj następujące informacje:

   * W obszarze **Typ skryptu** wybierz pozycję **niestandardowy**.

   * W polu **Nazwa** Podaj nazwę akcji skryptu.

     * Dla **identyfikatora URI skryptu bash** wprowadź  `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh` . Jest to skrypt instalujący dodatkowe pakiety języka R w węźle procesu roboczego

   * Zaznacz pole wyboru tylko dla **procesu roboczego**.

   * **Parametry**: pakiety R do zainstalowania. Na przykład `bitops stringr arules`

   * Zaznacz to pole wyboru, aby **zachować tę akcję skryptu**.  

   > [!NOTE]
   > 1. Domyślnie wszystkie pakiety języka R są instalowane z migawki repozytorium programu Microsoft MRAN spójne z zainstalowaną wersją programu ML Server. Jeśli chcesz zainstalować nowsze wersje pakietów, musisz uwzględnić pewne ryzyko niezgodności. Jednak możesz to zrobić za pomocą parametru `useCRAN` użytego jako pierwszy element listy pakietów, na przykład `useCRAN bitops, stringr, arules`.  
   > 2. Niektóre pakiety R wymagają dodatkowych bibliotek systemu Linux. Dla wygody usługi HDInsight ML są wstępnie instalowane z zależnościami wymaganymi przez najpopularniejsze 100 najpopularniejszych pakietów R. Jednak jeśli instalowane pakiety R wymagają jeszcze innych bibliotek, musisz pobrać skrypt podstawowy użyty tutaj i dodać kroki instalowania bibliotek systemowych. Następnie musisz przekazać zmodyfikowany skrypt do publicznego kontenera obiektów blob w usłudze Azure Storage i użyć zmodyfikowanego skryptu do zainstalowania pakietów.
   >    Aby uzyskać informacje na temat tworzenia akcji skryptu, zobacz [Script Action development](../hdinsight-hadoop-script-actions-linux.md) (Tworzenie akcji skryptu).

   ![Azure Portal akcji przesyłania skryptu](./media/r-server-hdinsight-manage/submit-script-action.png)

4. Wybierz polecenie **Utwórz**, aby uruchomić skrypt. Po zakończeniu działania skryptu pakiety R będą dostępne we wszystkich węzłach procesu roboczego.

## <a name="next-steps"></a>Następne kroki

* [Operationalize ML Services cluster on HDInsight (Obsługa operacji klastra usług ML w usłudze HDInsight)](r-server-operationalize.md)
* [Opcje kontekstu obliczeń dla klastra usługi ML w usłudze HDInsight](r-server-compute-contexts.md)
* [Azure Storage options for ML Services cluster on HDInsight (Opcje usługi Azure Storage dla klastra usług ML w usłudze HDInsight)](r-server-storage.md)