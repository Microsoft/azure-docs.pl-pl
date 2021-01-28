---
title: Korzystanie z programu Apache Maven w celu utworzenia klienta Java HBase dla usługi Azure HDInsight
description: Dowiedz się, jak za pomocą platformy Apache Maven utworzyć aplikację Apache HBase opartą na języku Java, a następnie wdrożyć ją w usłudze HBase w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seodec18, devx-track-java
ms.date: 12/24/2019
ms.openlocfilehash: 13616cf99db39ec4dac1d13e3dcd2cefc5a44614
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942967"
---
# <a name="build-java-applications-for-apache-hbase"></a>Tworzenie aplikacji Java dla platformy Apache HBase

Dowiedz się, jak utworzyć aplikację [Apache HBase](https://hbase.apache.org/) w języku Java. Następnie użyj aplikacji z HBase w usłudze Azure HDInsight.

Kroki opisane w tym dokumencie wykorzystują [Apache Maven](https://maven.apache.org/) do tworzenia i kompilowania projektu. Maven to narzędzie do zarządzania projektami oprogramowania i zrozumienia, które pozwala na tworzenie oprogramowania, dokumentacji i raportów dla projektów języka Java.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache HBase w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java developer Kit (JDK) w wersji 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Pakiet [Apache Maven](https://maven.apache.org/download.cgi) został prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z usługą Apache.  Maven to system kompilacji projektu dla projektów języka Java.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* W przypadku korzystania z programu PowerShell należy użyć polecenia [AZ module](/powershell/azure/).

* Edytor tekstów. W tym artykule jest wykorzystywany program Microsoft Notepad.

## <a name="test-environment"></a>Środowisko testowe

Środowisko używane na potrzeby tego artykułu było komputerem z systemem Windows 10.  Polecenia zostały wykonane w wierszu polecenia, a różne pliki były edytowane w Notatniku. Zmodyfikuj odpowiednio dla danego środowiska.

W wierszu polecenia wprowadź poniższe polecenia, aby utworzyć środowisko robocze:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Tworzenie projektu Maven

1. Wprowadź następujące polecenie, aby utworzyć projekt Maven o nazwie **hbaseapp**:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    To polecenie tworzy katalog o nazwie `hbaseapp` w bieżącej lokalizacji, który zawiera podstawowy projekt Maven. Drugie polecenie zmienia katalog roboczy na `hbaseapp` . Trzecie polecenie tworzy nowy katalog, `conf` który będzie później używany. `hbaseapp`Katalog zawiera następujące elementy:

    * `pom.xml`: Model obiektów projektu ([pliku pom](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) zawiera informacje i szczegóły konfiguracji używane do kompilowania projektu.
    * `src\main\java\com\microsoft\examples`: Zawiera kod aplikacji.
    * `src\test\java\com\microsoft\examples`: Zawiera testy dla aplikacji.

2. Usuń wygenerowany przykładowy kod. Usuń wygenerowane pliki testowe i aplikacje `AppTest.java` , a następnie `App.java` Wprowadź poniższe polecenia:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualizowanie modelu obiektów projektu

Pełne odwołanie do pliku pom.xml można znaleźć w temacie https://maven.apache.org/pom.html .  Otwórz `pom.xml` , wprowadzając następujące polecenie:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Dodaj zależności

W programie `pom.xml` Dodaj następujący tekst do `<dependencies>` sekcji:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Ta sekcja wskazuje, że projekt wymaga składników **HBase-Client** i **Phoenix-Core** . W czasie kompilacji te zależności są pobierane z domyślnego repozytorium Maven. Aby dowiedzieć się więcej o tej zależności, możesz użyć [wyszukiwania Maven Central Repository](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) .

> [!IMPORTANT]  
> Numer wersji klienta HBase musi być zgodny z wersją platformy Apache HBase dostarczoną z klastrem usługi HDInsight. Skorzystaj z poniższej tabeli, aby znaleźć poprawny numer wersji.

| Wersja klastra usługi HDInsight | Wersja Apache HBase do użycia |
| --- | --- |
| 3,6 | 1.1.2 |
| 4,0 | 2.0.0 |

Aby uzyskać więcej informacji na temat wersji i składników usługi HDInsight, zobacz [co to są różne składniki Apache Hadoop dostępne w usłudze HDInsight](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Konfiguracja kompilacji

Wtyczki Maven umożliwiają dostosowanie etapów kompilacji projektu. Ta sekcja służy do dodawania dodatków plug-in, zasobów i innych opcji konfiguracji kompilacji.

Dodaj następujący kod do `pom.xml` pliku, a następnie Zapisz i zamknij plik. Ten tekst musi znajdować się wewnątrz `<project>...</project>` tagów w pliku, na przykład między `</dependencies>` i `</project>` .

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
            <goals>
            <goal>shade</goal>
            </goals>
        </execution>
        </executions>
    </plugin>
    </plugins>
</build>
```

Ta sekcja służy do konfigurowania zasobu ( `conf/hbase-site.xml` ), który zawiera informacje o konfiguracji dla HBase.

> [!NOTE]  
> Możesz również ustawić wartości konfiguracyjne za pośrednictwem kodu. Zobacz komentarze w `CreateTable` przykładzie.

Ta sekcja służy również do konfigurowania [wtyczki kompilatora Apache Maven](https://maven.apache.org/plugins/maven-compiler-plugin/) i [wtyczki programu Apache Maven](https://maven.apache.org/plugins/maven-shade-plugin/). Wtyczka kompilatora służy do kompilowania topologii. Wtyczka do cieniowania służy do zapobiegania duplikowaniu licencji w pakiecie JAR utworzonym przez Maven. Ta wtyczka służy do zapobiegania błędowi "zduplikowane pliki licencji" w czasie wykonywania w klastrze usługi HDInsight. Użycie Maven-odcieni-plugin z `ApacheLicenseResourceTransformer` implementacją uniemożliwia błąd.

Maven-odcieni-Wtyczka tworzy również plik JAR Uber, który zawiera wszystkie zależności wymagane przez aplikację.

### <a name="download-the-hbase-sitexml"></a>Pobierz hbase-site.xml

Użyj następującego polecenia, aby skopiować konfigurację HBase z klastra HBase do `conf` katalogu. Zastąp ciąg `CLUSTERNAME` nazwą klastra usługi HDInsight, a następnie wprowadź polecenie:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Tworzenie aplikacji

### <a name="implement-a-createtable-class"></a>Zaimplementuj klasę Create

Wprowadź poniższe polecenie, aby utworzyć i otworzyć nowy plik `CreateTable.java` . Wybierz opcję **tak** w wierszu polecenia, aby utworzyć nowy plik.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Następnie skopiuj i wklej poniższy kod Java do nowego pliku. Następnie zamknij plik.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

Ten kod jest `CreateTable` klasą, która tworzy tabelę o nazwie `people` i wypełnia ją niektórym wstępnie zdefiniowanym użytkownikom.

### <a name="implement-a-searchbyemail-class"></a>Implementowanie klasy SearchByEmail

Wprowadź poniższe polecenie, aby utworzyć i otworzyć nowy plik `SearchByEmail.java` . Wybierz opcję **tak** w wierszu polecenia, aby utworzyć nowy plik.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Następnie skopiuj i wklej poniższy kod Java do nowego pliku. Następnie zamknij plik.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

`SearchByEmail`Klasa może służyć do wykonywania zapytań dotyczących wierszy według adresu e-mail. Ze względu na to, że używa filtru wyrażenia regularnego, podczas korzystania z klasy można podać ciąg lub wyrażenie regularne.

### <a name="implement-a-deletetable-class"></a>Implementowanie klasy Delete

Wprowadź poniższe polecenie, aby utworzyć i otworzyć nowy plik `DeleteTable.java` . Wybierz opcję **tak** w wierszu polecenia, aby utworzyć nowy plik.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Następnie skopiuj i wklej poniższy kod Java do nowego pliku. Następnie zamknij plik.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

`DeleteTable`Klasa czyści tabele HBase utworzone w tym przykładzie, wyłączając i upuszczając tabelę utworzoną przez `CreateTable` klasę.

## <a name="build-and-package-the-application"></a>Kompilowanie i pakowanie aplikacji

1. W `hbaseapp` katalogu Użyj następującego polecenia, aby skompilować plik JAR zawierający aplikację:

    ```cmd
    mvn clean package
    ```

    To polecenie kompiluje i pakuje aplikację do pliku JAR.

2. Po zakończeniu wykonywania polecenia `hbaseapp/target` katalog zawiera plik o nazwie `hbaseapp-1.0-SNAPSHOT.jar` .

   > [!NOTE]  
   > `hbaseapp-1.0-SNAPSHOT.jar`Plik to Uber jar. Zawiera wszystkie zależności wymagane do uruchomienia aplikacji.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Przekaż zadania JAR i Run (SSH)

Poniższe kroki służą `scp` do kopiowania jar do podstawowego węzła głównego platformy Apache HBase w klastrze usługi HDInsight. `ssh`Polecenie jest następnie używane do nawiązania połączenia z klastrem i uruchomienia przykładu bezpośrednio w węźle głównym.

1. Przekaż plik jar do klastra. Zastąp `CLUSTERNAME` ciąg nazwą klastra usługi HDInsight, a następnie wprowadź następujące polecenie:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Nawiąż połączenie z klastrem HBase. Zastąp `CLUSTERNAME` ciąg nazwą klastra usługi HDInsight, a następnie wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Aby utworzyć tabelę HBase przy użyciu aplikacji Java, użyj następującego polecenia w otwartym połączeniu SSH:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    To polecenie tworzy tabelę HBase o nazwie **ludzie** i wypełnia ją danymi.

4. Aby wyszukać adresy e-mail przechowywane w tabeli, użyj następującego polecenia:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Otrzymujesz następujące wyniki:

    ```console
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

5. Aby usunąć tabelę, użyj następującego polecenia:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Przekazywanie zadań JAR i Run (PowerShell)

Poniższe kroki używają Azure PowerShell [AZ module](/powershell/azure/new-azureps-module-az) , aby przekazać plik jar do domyślnego magazynu dla klastra Apache HBase. Polecenia cmdlet usługi HDInsight są następnie używane do zdalnego uruchamiania przykładów.

1. Po zainstalowaniu i skonfigurowaniu elementu AZ module utwórz plik o nazwie `hbase-runner.psm1` . Użyj następującego tekstu jako zawartości tego pliku:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Ten plik zawiera dwa moduły:

   * **Add-HDInsightFile** — służy do przekazywania plików do klastra
   * **Start-HBaseExample** — służy do uruchamiania utworzonych wcześniej klas

2. Zapisz `hbase-runner.psm1` plik w `hbaseapp` katalogu.

3. Zarejestruj moduły w Azure PowerShell. Otwórz nowe okno Azure PowerShell i edytuj poniższe polecenie, zastępując je `CLUSTERNAME` nazwą klastra. Następnie wprowadź następujące polecenia:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Użyj następującego polecenia, aby przekazać `hbaseapp-1.0-SNAPSHOT.jar` do klastra.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Po wyświetleniu monitu wprowadź nazwę logowania klastra (administratora) i hasło. Polecenie przekazuje `hbaseapp-1.0-SNAPSHOT.jar` do `example/jars` lokalizacji w magazynie podstawowym klastra.

5. Aby utworzyć tabelę przy użyciu programu `hbaseapp` , użyj następującego polecenia:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Po wyświetleniu monitu wprowadź nazwę logowania klastra (administratora) i hasło.

    To polecenie tworzy tabelę o nazwie **osoby** w HBase w klastrze usługi HDInsight. To polecenie nie wyświetla żadnych danych wyjściowych w oknie konsoli.

6. Aby wyszukać wpisy w tabeli, użyj następującego polecenia:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Po wyświetleniu monitu wprowadź nazwę logowania klastra (administratora) i hasło.

    To polecenie używa `SearchByEmail` klasy do wyszukiwania dowolnych wierszy, w których `contactinformation` rodzina kolumn i `email` kolumna zawierają ciąg `contoso.com` . Powinny zostać wyświetlone następujące wyniki:

    ```output
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

    Użycie **fabrikam.com** dla `-emailRegex` wartości zwraca użytkowników, którzy mają **fabrikam.com** w polu adres e-mail. Możesz również użyć wyrażeń regularnych jako terminu wyszukiwania. Na przykład **^ r** zwraca adresy e-mail zaczynające się od litery "r".

7. Aby usunąć tabelę, użyj następującego polecenia:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Brak wyników lub nieoczekiwanych wyników podczas korzystania z Start-HBaseExample

Użyj `-showErr` parametru, aby wyświetlić błąd standardowy (stderr), który jest generowany podczas wykonywania zadania.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak używać SqlLine z Apache HBase](apache-hbase-query-with-phoenix.md)