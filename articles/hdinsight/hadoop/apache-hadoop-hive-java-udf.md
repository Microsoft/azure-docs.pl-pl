---
title: Funkcja Java zdefiniowana przez użytkownika (UDF) z Apache Hive usługą Azure HDInsight
description: Dowiedz się, jak utworzyć opartą na języku Java funkcję (UDF), która działa z Apache Hive. Ten przykład UDF konwertuje tabelę ciągów tekstowych na małe litery.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 11/20/2019
ms.openlocfilehash: bf9c2ea544c6b510a0507b6b020f0eae0b101dc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946569"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Używanie formatu UDF języka Java z Apache Hive w usłudze HDInsight

Dowiedz się, jak utworzyć opartą na języku Java funkcję (UDF), która działa z Apache Hive. Funkcja UDF języka Java w tym przykładzie konwertuje tabelę ciągów tekstowych na wszystkie małe litery.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java developer Kit (JDK) w wersji 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* Pakiet [Apache Maven](https://maven.apache.org/download.cgi) został prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z usługą Apache.  Maven to system kompilacji projektu dla projektów języka Java.
* [Schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) magazynu podstawowego klastrów. Będzie to wasb://dla usługi Azure Storage, abfs://dla Azure Data Lake Storage Gen2 lub adl://dla Azure Data Lake Storage Gen1. Jeśli w usłudze Azure Storage włączono opcję bezpiecznego transferu, identyfikator URI mógłby być `wasbs://` .  Zobacz również [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

* Edytor tekstu lub środowisko IDE języka Java

    > [!IMPORTANT]  
    > W przypadku tworzenia plików w języku Python na kliencie systemu Windows należy użyć edytora, który używa LF jako końca wiersza. Jeśli nie masz pewności, czy Edytor używa LF lub CRLF, zobacz sekcję [Rozwiązywanie problemów](#troubleshooting) , aby zapoznać się z instrukcjami dotyczącymi usuwania znaku CR.

## <a name="test-environment"></a>Środowisko testowe

Środowisko używane na potrzeby tego artykułu było komputerem z systemem Windows 10.  Polecenia zostały wykonane w wierszu polecenia, a różne pliki były edytowane w Notatniku. Zmodyfikuj odpowiednio dla danego środowiska.

W wierszu polecenia wprowadź poniższe polecenia, aby utworzyć środowisko robocze:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Tworzenie przykładu UDF Java

1. Utwórz nowy projekt Maven, wprowadzając następujące polecenie:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    To polecenie tworzy katalog o nazwie `exampleudf` , który zawiera projekt Maven.

2. Po utworzeniu projektu Usuń `exampleudf/src/test` katalog, który został utworzony w ramach projektu, wprowadzając następujące polecenie:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Otwórz `pom.xml` , wprowadzając następujące polecenie:

    ```cmd
    notepad pom.xml
    ```

    Następnie zastąp istniejący `<dependencies>` wpis następującym kodem XML:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Te wpisy określają wersję usługi Hadoop i Hive zawartą w usłudze HDInsight 3,6. Informacje o wersjach usługi Hadoop i Hive dostarczanych z usługą HDInsight można znaleźć w dokumencie dotyczącym [wersji składnika usługi HDInsight](../hdinsight-component-versioning.md) .

    Dodaj `<build>` sekcję przed `</project>` wierszem na końcu pliku. Ta sekcja powinna zawierać następujący kod XML:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
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

    Te wpisy definiują sposób kompilowania projektu. W odniesieniu do wersji środowiska Java używanej przez projekt oraz sposobu tworzenia uberjar do wdrożenia w klastrze.

    Zapisz plik po wprowadzeniu zmian.

4. Wprowadź poniższe polecenie, aby utworzyć i otworzyć nowy plik `ExampleUDF.java` :

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Następnie skopiuj i wklej poniższy kod Java do nowego pliku. Następnie zamknij plik.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Ten kod implementuje funkcję UDF, która akceptuje wartość ciągu i zwraca małą wersję ciągu.

## <a name="build-and-install-the-udf"></a>Kompiluj i zainstaluj funkcję UDF

W poniższych poleceniach Zastąp `sshuser` wartość rzeczywistą nazwą użytkownika, jeśli jest inna. Zamień `mycluster` na rzeczywistą nazwę klastra.

1. Skompiluj i Spakuj system UDF, wprowadzając następujące polecenie:

    ```cmd
    mvn compile package
    ```

    To polecenie kompiluje i pakuje plik UDF do `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` pliku.

2. Użyj `scp` polecenia, aby skopiować plik do klastra usługi HDInsight, wprowadzając następujące polecenie:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Połącz się z klastrem przy użyciu protokołu SSH, wprowadzając następujące polecenie:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Z otwartej sesji SSH Skopiuj plik jar do magazynu usługi HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Korzystanie z formatu UDF z platformy Hive

1. Uruchom klienta programu Z usługi Beeline z sesji SSH, wprowadzając następujące polecenie:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    W tym poleceniu przyjęto założenie, że dla konta logowania dla klastra użyto domyślnych ustawień **administratora** .

2. Po nadejściu `jdbc:hive2://localhost:10001/>` monitu wprowadź następujące polecenie, aby dodać funkcję UDF do Hive i uwidocznić ją w ramach funkcji.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Użyj formatu UDF, aby konwertować wartości pobrane z tabeli do małych liter.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    To zapytanie wybiera stan z tabeli, konwertuje ciąg na małe litery, a następnie wyświetla je wraz z niezmodyfikowaną nazwą. Dane wyjściowe wyglądają podobnie do następującego tekstu:

    ```output
    +---------------+---------------+--+
    |  exampleudf   |     state     |
    +---------------+---------------+--+
    | california    | California    |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | utah          | Utah          |
    | utah          | Utah          |
    | colorado      | Colorado      |
    +---------------+---------------+--+
    ```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas uruchamiania zadania programu Hive może wystąpić błąd podobny do następującego:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Przyczyną tego problemu może być zakończenie wierszy w pliku języka Python. Wiele edytorów systemu Windows domyślnie używa CRLF jako końca wiersza, ale aplikacje systemu Linux zazwyczaj oczekują LF.

Aby usunąć znaki CR przed przekazaniem pliku do usługi HDInsight, można użyć następujących instrukcji programu PowerShell:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Następne kroki

Aby poznać inne sposoby pracy z usługą Hive, zobacz [używanie Apache Hive z usługą HDInsight](hdinsight-use-hive.md).

Aby uzyskać więcej informacji na temat usługi Hive User-Defined, zobacz sekcję [Apache Hive operatory i funkcje User-Defined](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) w witrynie typu wiki usługi Hive w witrynie Apache.org.