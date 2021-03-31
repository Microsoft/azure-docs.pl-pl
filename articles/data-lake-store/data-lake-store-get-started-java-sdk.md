---
title: Zestaw Java SDK — operacje na systemie plików na Data Lake Storage Gen1 — Azure
description: Użyj zestawu SDK języka Java dla Azure Data Lake Storage Gen1 do wykonywania operacji systemu plików na Data Lake Storage Gen1, takich jak tworzenie folderów i przekazywanie i pobieranie plików danych.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: a2c55a2d3277bbb6c3cf72f5ea703780d2a5e9bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87318848"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-java-sdk"></a>Operacje systemu plików na Azure Data Lake Storage Gen1 przy użyciu zestawu Java SDK
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-data-operations-net-sdk.md)
> * [Zestaw SDK Java](data-lake-store-get-started-java-sdk.md)
> * [Interfejs API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Dowiedz się, jak za pomocą zestawu SDK języka Java Azure Data Lake Storage Gen1 wykonywać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych itp. Aby uzyskać więcej informacji na temat Data Lake Storage Gen1, zobacz [Azure Data Lake Storage Gen1](data-lake-store-overview.md).

Możesz uzyskać dostęp do dokumentacji interfejsu API zestawu Java SDK dla Data Lake Storage Gen1 w [Azure Data Lake Storage Gen1 dokumentacji interfejsu API języka Java](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Wymagania wstępne
* Zestaw Java Development Kit (JDK 7 lub nowszy, korzystający z języka Java w wersji 1.7 lub nowszej)
* Konto Data Lake Storage Gen1. Postępuj zgodnie z instrukcjami w punkcie wprowadzenie [do Azure Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Ten samouczek używa programu Maven na potrzeby zależności między kompilacją i projektem. Chociaż istnieje możliwość kompilacji bez używania systemu kompilacji, takiego jak Maven lub Gradle, systemy te znacznie ułatwiają zarządzanie zależnościami.
* (Opcjonalnie) Wtyczka [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) lub [Eclipse](https://www.eclipse.org/downloads/) przypominająca środowisko IDE lub podobna.

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java
Przykładowy kod dostępny [w usłudze GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) przeprowadzi Cię przez proces tworzenia plików w magazynie, łączenia plików, pobierania pliku i usuwania niektórych plików z magazynu. Ta część artykułu przeprowadzi Cię przez najważniejsze fragmenty kodu.

1. Utwórz projekt Maven za pomocą polecenia [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) w wierszu polecenia lub w środowisku IDE. Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki IntelliJ, zobacz [tutaj](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki Eclipse, zobacz [tutaj](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Dodaj poniższe zależności do Twojego pliku **pom.xml** programu Maven. Dodaj następujący fragment kodu przed **\</project>** tagiem:
   
    ```xml
    <dependencies>
        <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.1.5</version>
        </dependency>
        <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
        </dependency>
    </dependencies>
    ```
   
    Pierwsza zależność polega na użyciu zestawu SDK Data Lake Storage Gen1 ( `azure-data-lake-store-sdk` ) z repozytorium Maven. Drugą zależnością (`slf4j-nop`) jest określenie struktury rejestrowania używanej dla tej aplikacji. Zestaw Data Lake Storage Gen1 SDK używa fasady rejestrowania [SLF4J](https://www.slf4j.org/) , która umożliwia wybór spośród wielu popularnych platform rejestrowania, takich jak Log4J, rejestrowanie w języku Java, Logback itp. lub bez rejestrowania. W tym przykładzie wyłączyliśmy rejestrowanie, dlatego używamy powiązania **slf4j-nop**. Aby używać innych opcji rejestrowania w aplikacji, zobacz [tutaj](https://www.slf4j.org/manual.html#projectDep).

3. Dodaj do swojej aplikacji następujące instrukcje importowania.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

    import java.io.*;
    import java.util.Arrays;
    import java.util.List;
    ```

## <a name="authentication"></a>Authentication

* W przypadku uwierzytelniania użytkownika końcowego dla aplikacji zobacz [uwierzytelnianie użytkowników końcowych w Data Lake Storage Gen1 przy użyciu języka Java](data-lake-store-end-user-authenticate-java-sdk.md).
* Aby uzyskać uwierzytelnianie między usługami dla aplikacji, zobacz Uwierzytelnianie między usługami i [Data Lake Storage Gen1 przy użyciu języka Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-a-data-lake-storage-gen1-client"></a>Tworzenie klienta Data Lake Storage Gen1
Utworzenie obiektu [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) wymaga określenia nazwy konta Data Lake Storage Gen1 i dostawcy tokenów wygenerowanego podczas uwierzytelniania za pomocą Data Lake Storage Gen1 (zobacz sekcję [uwierzytelniania](#authentication) ). Nazwa konta Data Lake Storage Gen1 musi być w pełni kwalifikowaną nazwą domeny. Na przykład Zastąp pole **FILLIN w tym miejscu** , tak jak **mydatalakestoragegen1.azuredatalakestore.NET**.

```java
private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);
```

Fragmenty kodu w poniższych sekcjach zawierają przykłady niektórych typowych operacji systemu plików. Aby zobaczyć inne operacje, można zapoznać się z pełnym [Data Lake Storage Gen1 dokumentacją interfejsu API zestawu Java SDK](https://azure.github.io/azure-data-lake-store-java/javadoc/) obiektu **ADLStoreClient** .

## <a name="create-a-directory"></a>Tworzenie katalogu

Poniższy fragment kodu tworzy strukturę katalogów w katalogu głównym określonego konta Data Lake Storage Gen1.

```java
// create directory
client.createDirectory("/a/b/w");
System.out.println("Directory created.");
```

## <a name="create-a-file"></a>Tworzenie pliku

Poniższy fragment kodu tworzy plik (c.txt) w strukturze katalogów i zapisuje pewne dane do tego pliku.

```java
// create file and write some content
String filename = "/a/b/c.txt";
OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
PrintStream out = new PrintStream(stream);
for (int i = 1; i <= 10; i++) {
    out.println("This is line #" + i);
    out.format("This is the same line (%d), but using formatted output. %n", i);
}
out.close();
System.out.println("File created.");
```

Możesz również utworzyć plik (d.txt) przy użyciu tablic typu Byte.

```java
// create file using byte arrays
stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
byte[] buf = getSampleContent();
stream.write(buf);
stream.close();
System.out.println("File created using byte array.");
```

Definicja funkcji `getSampleContent` używana w poprzednim fragmencie kodu jest dostępna jako część przykładu [w serwisie GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/). 

## <a name="append-to-a-file"></a>Dołączanie do pliku

Poniższy fragment kodu dołącza zawartość do istniejącego pliku.

```java
// append to file
stream = client.getAppendStream(filename);
stream.write(getSampleContent());
stream.close();
System.out.println("File appended.");
```

Definicja funkcji `getSampleContent` używana w poprzednim fragmencie kodu jest dostępna jako część przykładu [w serwisie GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="read-a-file"></a>Odczytywanie pliku

Poniższy fragment kodu odczytuje zawartość z pliku na koncie Data Lake Storage Gen1.

```java
// Read File
InputStream in = client.getReadStream(filename);
BufferedReader reader = new BufferedReader(new InputStreamReader(in));
String line;
while ( (line = reader.readLine()) != null) {
    System.out.println(line);
}
reader.close();
System.out.println();
System.out.println("File contents read.");
```

## <a name="concatenate-files"></a>Łączenie plików

Poniższy fragment kodu łączy dwa pliki na koncie Data Lake Storage Gen1. W przypadku powodzenia połączony plik zastępuje te dwa istniejące pliki.

```java
// concatenate the two files into one
List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
client.concatenateFiles("/a/b/f.txt", fileList);
System.out.println("Two files concatenated into a new file.");
```

## <a name="rename-a-file"></a>Zmienianie nazwy pliku

Poniższy fragment kodu zmienia nazwę pliku na koncie Data Lake Storage Gen1.

```java
//rename the file
client.rename("/a/b/f.txt", "/a/b/g.txt");
System.out.println("New file renamed.");
```

## <a name="get-metadata-for-a-file"></a>Pobranie metadanych dla pliku

Poniższy fragment kodu pobiera metadane pliku na koncie Data Lake Storage Gen1.

```java
// get file metadata
DirectoryEntry ent = client.getDirectoryEntry(filename);
printDirectoryInfo(ent);
System.out.println("File metadata retrieved.");
```

## <a name="set-permissions-on-a-file"></a>Ustawianie uprawnień do pliku

Poniższy fragment kodu ustawia uprawnienia do pliku, który został utworzony przez Ciebie w poprzedniej sekcji.

```java
// set file permission
client.setPermission(filename, "744");
System.out.println("File permission set.");
```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Poniższy fragment kodu rekursywnie wyświetla zawartość katalogu.

```java
// list directory contents
List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
System.out.println("Directory listing for directory /a/b:");
for (DirectoryEntry entry : list) {
    printDirectoryInfo(entry);
}
System.out.println("Directory contents listed.");
```

Definicja funkcji `printDirectoryInfo` używana w poprzednim fragmencie kodu jest dostępna jako część przykładu [w serwisie GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="delete-files-and-folders"></a>Usuwanie plików i folderów

Poniższy fragment kodu usuwa określone pliki i foldery na koncie Data Lake Storage Gen1 cyklicznie.

```java
// delete directory along with all the subdirectories and files in it
client.deleteRecursive("/a");
System.out.println("All files and folders deleted recursively");
promptEnterKey();
```

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji
1. Aby przeprowadzić uruchomienie z poziomu środowiska IDE, znajdź i naciśnij przycisk **Run** (Uruchom). Aby przeprowadzić uruchomienie z poziomu programu Maven, użyj polecenia [exec:exec](https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Aby wygenerować autonomiczny plik JAR, który można uruchomić z poziomu wiersza polecenia, skompiluj plik JAR zawierający wszystkie zależności, używając [wtyczki zestawu Maven](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Przykładem jest pom.xml w [przykładowym kodzie źródłowym w witrynie GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) .

## <a name="next-steps"></a>Następne kroki
* [Zapoznawanie się z dokumentacją JavaDoc dotyczącą zestawu SDK Java](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)


