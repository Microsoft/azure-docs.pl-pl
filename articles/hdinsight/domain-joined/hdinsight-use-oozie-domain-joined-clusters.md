---
title: Przepływy pracy Apache Oozie & zabezpieczenia przedsiębiorstwa — Azure HDInsight
description: Zabezpiecz przepływy pracy Apache Oozie przy użyciu usługi Azure HDInsight pakiet Enterprise Security. Dowiedz się, jak zdefiniować przepływ pracy Oozie i przesłać zadanie Oozie.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: dc20a258028bb76351ae70362234e7c4c8c741a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699406"
---
# <a name="run-apache-oozie-in-azure-hdinsight-clusters-with-enterprise-security-package"></a>Uruchamianie programu Apache Oozie w klastrach usługi Azure HDInsight przy użyciu pakiet Enterprise Security

Apache Oozie to przepływ pracy i system koordynacji, który zarządza zadaniami Apache Hadoop. Usługa Oozie jest zintegrowana z stosem usługi Hadoop i obsługuje następujące zadania:

- Apache MapReduce
- Apache świni
- Apache Hive
- Apache Sqoop

Można również użyć Oozie do planowania zadań specyficznych dla systemu, takich jak programy Java lub skrypty powłoki.

## <a name="prerequisite"></a>Wymaganie wstępne

Klaster Azure HDInsight Hadoop z pakiet Enterprise Security (ESP). Zobacz [Konfigurowanie klastrów usługi HDInsight przy użyciu protokołu ESP](./apache-domain-joined-configure-using-azure-adds.md).

> [!NOTE]  
> Aby uzyskać szczegółowe instrukcje dotyczące używania Oozie w klastrach innych niż ESP, zobacz [Korzystanie z przepływów pracy Apache Oozie w usłudze Azure HDInsight opartej na systemie Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Nawiązywanie połączenia z klastrem ESP

Aby uzyskać więcej informacji na temat Secure Shell (SSH), zobacz [nawiązywanie połączenia z usługą HDInsight (Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. Aby sprawdzić pomyślne uwierzytelnianie Kerberos, użyj `klist` polecenia. W przeciwnym razie użyj `kinit` do uruchomienia uwierzytelniania Kerberos.

1. Zaloguj się do bramy usługi HDInsight, aby zarejestrować token OAuth wymagane w celu uzyskania dostępu do Azure Data Lake Storage:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    Kod odpowiedzi o stanie **200 OK** wskazuje pomyślną rejestrację. Sprawdź nazwę użytkownika i hasło, jeśli otrzymasz nieautoryzowaną odpowiedź, na przykład 401.

## <a name="define-the-workflow"></a>Definiowanie przepływu pracy

Definicje przepływów pracy Oozie są zapisywane w języku definicji procesu Apache Hadoop (hPDL). hPDL to język definicji procesu XML. Wykonaj następujące kroki, aby zdefiniować przepływ pracy:

1. Skonfiguruj obszar roboczy użytkownika domeny:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   Zamień `DomainUser` na nazwę użytkownika domeny.
   Zamień na `DomainUserPath` ścieżkę katalogu macierzystego użytkownika domeny.
   Zamień `ClusterVersion` na wersję platformy danych klastra.

2. Użyj następującej instrukcji, aby utworzyć i edytować nowy plik:

   ```bash
   nano workflow.xml
   ```

3. Po otwarciu edytora nano wprowadź następujący kod XML jako zawartość pliku:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
    ```

4. Zamień `clustername` na nazwę klastra.

5. Aby zapisać plik, wybierz **kombinację klawiszy Ctrl + X**. Wprowadź **Y**. Następnie wybierz klawisz **Enter**.

    Przepływ pracy jest podzielony na dwie części:

   - **Poświadczeń.** Ta sekcja zajmuje się poświadczeniami, które są używane do uwierzytelniania akcji Oozie:

     Ten przykład używa uwierzytelniania dla akcji Hive. Aby dowiedzieć się więcej, zobacz [Akcja uwierzytelnianie](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     Usługa Credential umożliwia Oozieą akcje personifikacji użytkownika w celu uzyskania dostępu do usług Hadoop.

   - **Transakcji.** Ta sekcja ma trzy akcje: map — zmniejszenie, serwer Hive 2 i serwer Hive 1:

     - Akcja Mapuj — Redukuj uruchamia przykład z pakietu Oozie dla mapy — redukuje dane wyjściowe zagregowanej liczby wyrazów.

     - Akcje serwera Hive 2 i programu Hive Server 1 uruchamiają zapytanie w przykładowej tabeli Hive dostarczonej z usługą HDInsight.

     Akcje programu Hive używają poświadczeń zdefiniowanych w sekcji poświadczeń do uwierzytelniania za pomocą słowa kluczowego `cred` w elemencie Action.

6. Użyj następującego polecenia, aby skopiować `workflow.xml` plik do `/user/<domainuser>/examples/apps/map-reduce/workflow.xml` :

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. Zastąp `domainuser` wartość nazwą użytkownika domeny.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Zdefiniuj plik właściwości dla zadania Oozie

1. Użyj poniższej instrukcji, aby utworzyć i edytować nowy plik dla właściwości zadania:

    ```bash
    nano job.properties
    ```

2. Po otwarciu edytora nano Użyj następującego kodu XML jako zawartości pliku:

   ```bash
   nameNode=adl://home
   jobTracker=headnodehost:8050
   queueName=default
   examplesRoot=examples
   oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
   hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
   hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
   oozie.use.system.libpath=true
   user.name=[domainuser]
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - Użyj `adl://home` identyfikatora URI dla `nameNode` właściwości, jeśli masz Azure Data Lake Storage Gen1 jako podstawowy magazyn klastra. Jeśli używasz usługi Azure Blob Storage, przejdź do `wasb://home` . Jeśli używasz Azure Data Lake Storage Gen2, przejdź do `abfs://home` .
   - Zastąp `domainuser` wartość nazwą użytkownika domeny.
   - Zamień na `ClusterShortName` krótką nazwę klastra. Na przykład, jeśli nazwa klastra to https:// *[przykład link]* sechadoopcontoso.azurehdisnight.NET, jest to `clustershortname` pierwsze sześć znaków klastra: **sechad**.
   - Zamień na `jdbcurlvalue` adres URL JDBC z konfiguracji programu Hive. Przykładem jest JDBC: hive2://headnodehost: 10001/; transportmode = http.
   - Aby zapisać plik, wybierz CTRL + X, ENTER `Y` , a następnie wybierz **Enter**.

   Ten plik właściwości musi być obecny lokalnie podczas uruchamiania zadań Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Tworzenie niestandardowych skryptów Hive dla zadań Oozie

Można utworzyć dwa skrypty Hive dla serwera Hive 1 i serwera Hive 2, jak pokazano w poniższych sekcjach.

### <a name="hive-server-1-file"></a>Plik programu Hive Server 1

1. Utwórz i edytuj plik dla działań serwera Hive 1:

    ```bash
    nano countrowshive1.hql
    ```

2. Utwórz skrypt:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Zapisz plik w rozproszony system plików Apache Hadoop (HDFS):

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Plik programu Hive Server 2

1. Utwórz i edytuj pole dla akcji programu Hive Server 2:

    ```bash
    nano countrowshive2.hql
    ```

2. Utwórz skrypt:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Zapisz plik w systemie plików HDFS:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Przesyłanie zadań Oozie

Przesyłanie zadań Oozie dla klastrów ESP jest podobne do przesyłania zadań Oozie w klastrach innych niż ESP.

Aby uzyskać więcej informacji, zobacz [Używanie platformy Apache Oozie z usługą Apache Hadoop do definiowania i uruchamiania przepływu pracy w usłudze Azure HDInsight opartej na systemie Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Wyniki z Ooziego przesłania zadania

Zadania Oozie są uruchamiane dla użytkownika. Tak więc w przypadku Apache Hadoop w ramach usługi PRZĘDZy i usługi Apache Ranger Audit Logs są wyświetlane zadania uruchamiane jako personifikowany użytkownik. Dane wyjściowe interfejsu wiersza polecenia zadania Oozie wyglądają podobnie jak w poniższym kodzie:

```output
Job ID : 0000015-180626011240801-oozie-oozi-W
------------------------------------------------------------------------------------------------
Workflow Name : map-reduce-wf
App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
Status        : SUCCEEDED
Run           : 0
User          : alicetest
Group         : -
Created       : 2018-06-26 19:25 GMT
Started       : 2018-06-26 19:25 GMT
Last Modified : 2018-06-26 19:30 GMT
Ended         : 2018-06-26 19:30 GMT
CoordAction ID: -

Actions
------------------------------------------------------------------------------------------------
ID                        Status    Ext ID            ExtStatus                 ErrCode
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@:start:    OK    -                         OK             -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@mr-test    OK    job_1529975666160_0051    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive2    OK    job_1529975666160_0053    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive    OK     job_1529975666160_0055    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@end       OK     -                         OK             -
-----------------------------------------------------------------------------------------------
```

W dziennikach inspekcji Ranger dla akcji programu Hive Server 2 są wyświetlane Oozie z akcją użytkownika. Widoki Ranger i PRZĘDZy są widoczne tylko dla administratora klastra.

## <a name="configure-user-authorization-in-oozie"></a>Konfigurowanie autoryzacji użytkownika w Oozie

Oozie przez siebie sama ma konfigurację autoryzacji użytkowników, która umożliwia blokowanie użytkownikom zatrzymywania lub usuwania zadań innych użytkowników. Aby włączyć tę konfigurację, należy ustawić `oozie.service.AuthorizationService.security.enabled` na `true` . 

Aby uzyskać więcej informacji, zobacz [Instalowanie i Konfigurowanie oprogramowania Apache Oozie](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

W przypadku składników, takich jak serwer programu Hive 1, w przypadku których wtyczka Ranger jest niedostępna lub nie jest obsługiwana, możliwe jest tylko duże upoważnienie HDFS. Szczegółowa Autoryzacja jest dostępna tylko za poorednictwem wtyczek Ranger.

## <a name="get-the-oozie-web-ui"></a>Pobierz interfejs użytkownika sieci Web Oozie

Interfejs użytkownika sieci Web Oozie zapewnia internetowy widok stanu zadań Oozie w klastrze. Aby uzyskać interfejs użytkownika sieci Web, wykonaj następujące kroki w obszarze klastry ESP:

1. Dodaj [węzeł brzegowy](../hdinsight-apps-use-edge-node.md) i Włącz [uwierzytelnianie przy użyciu protokołu SSH Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Postępuj zgodnie z instrukcjami [interfejsu użytkownika sieci Web Oozie](../hdinsight-use-oozie-linux-mac.md) , aby włączyć tunelowanie ssh do węzła brzegowego i uzyskać dostęp do interfejsu użytkownika sieci Web.

## <a name="next-steps"></a>Następne kroki

- [Użyj programu Apache Oozie z Apache Hadoop, aby zdefiniować i uruchomić przepływ pracy w usłudze Azure HDInsight opartej na systemie Linux](../hdinsight-use-oozie-linux-mac.md).
- [Łączenie z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
