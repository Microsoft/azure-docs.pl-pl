---
title: 'Szybki Start: pierwsze zapytanie Java'
description: W tym przewodniku szybki start wykonaj kroki, aby włączyć pakiety Maven na wykresie zasobów dla języka Java i uruchomić pierwsze zapytanie.
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 97c04cb8b8180034bdc5109446c79deb56e457c9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223972"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>Szybki Start: uruchamianie pierwszego zapytania wykresu zasobów przy użyciu języka Java

Pierwszym krokiem korzystania z grafu zasobów platformy Azure jest sprawdzenie, czy są zainstalowane wymagane pakiety Maven dla języka Java. Ten przewodnik Szybki Start przeprowadzi Cię przez proces dodawania pakietów Maven do instalacji języka Java.

Po zakończeniu tego procesu dodaliśmy pakiety Maven do instalacji języka Java i uruchomimy pierwsze zapytanie dotyczące grafu zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.21.0**). Jeśli nie jest jeszcze zainstalowana, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

  > [!NOTE]
  > Interfejs wiersza polecenia platformy Azure jest wymagany do włączenia zestawu Azure SDK dla języka Java w celu użycia **uwierzytelniania opartego na interfejsie wiersza polecenia** w poniższych przykładach. Aby uzyskać informacje o innych opcjach, zobacz [Biblioteka Azure Identity Client Library for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

- [Zestaw Java developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja
  8.

- [Apache Maven](https://maven.apache.org/), wersja 3,6 lub nowsza.

## <a name="create-the-resource-graph-project"></a>Tworzenie projektu grafu zasobów

Aby włączyć środowisko Java do wysyłania zapytań do grafu zasobów platformy Azure, Utwórz i skonfiguruj nową aplikację z Maven i zainstaluj wymagane pakiety Maven.

1. Zainicjuj nową aplikację Java o nazwie "argQuery" z [Maven Archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html):

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. Zmień katalog na folder nowego projektu `argQuery` i otwórz go `pom.xml` w ulubionym edytorze. Dodaj następujące `<dependency>` węzły w istniejącym `<dependencies>` węźle:

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. W `pom.xml` pliku Dodaj następujący `<properties>` węzeł w węźle podstawowym, `<project>` Aby zaktualizować wersje źródłową i docelową:

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. W `pom.xml` pliku Dodaj następujący `<build>` węzeł w węźle podstawowym, `<project>` Aby skonfigurować cel i klasę główną dla projektu do uruchomienia.

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. Zastąp wartość domyślną `App.java` w `\argQuery\src\main\java\com\Fabrikam` poniższym kodzie i Zapisz zaktualizowany plik:

   ```java
   package com.Fabrikam;
   
   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;
   
   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];
   
           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));
   
           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);
           
           QueryResponse response = manager.resourceProviders().resources(queryRequest);
   
           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. Kompiluj `argQuery` aplikację konsolową:

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Po skompilowaniu aplikacji konsolowej Java należy wypróbować prostą kwerendę grafu zasobów. Zapytanie zwraca pierwsze pięć zasobów platformy Azure o **nazwie** i **typie zasobu** każdego zasobu.

W każdym wywołaniu do `argQuery` , istnieją zmienne, które są używane, należy zamienić na własne wartości:

- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji
- `{query}` -Zamień na zapytanie dotyczące wykresu zasobów platformy Azure

1. Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure `az login` .

1. Zmień katalogi na `argQuery` folder projektu utworzony przy użyciu poprzedniego `mvn -B archetype:generate` polecenia.

1. Uruchom pierwsze zapytanie dotyczące wykresu zasobów platformy Azure za pomocą Maven, aby skompilować aplikację konsolową i przekazać argumenty. `exec.args`Właściwość identyfikuje argumenty według spacji. Aby zidentyfikować kwerendę jako pojedynczy argument, zawijamy ją z pojedynczymi cudzysłowami ( `'` ).

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > Ponieważ w tym przykładzie zapytania nie określono modyfikatora sortowania, takiego jak `order by` uruchomienie tego zapytania wiele razy, prawdopodobnie zostanie wyznaczony inny zestaw zasobów dla każdego żądania.

1. Zmień argument na `argQuery.exe` i Zmień zapytanie na `order by` Właściwość **name** :

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie porządkuje je.

1. Zmień parametr końcowy na `argQuery.exe` i Zmień zapytanie, tak aby najpierw miało `order by` Właściwość **name** , a następnie `limit` na pięć najważniejszych wyników:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

Gdy ostateczne zapytanie jest uruchamiane kilka razy, przy założeniu, że nic w środowisku nie zmienia się, zwracane wyniki są spójne i uporządkowane według właściwości **nazwy** , ale nadal są ograniczone do pięciu pierwszych wyników.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć aplikację konsolową Java i zainstalowane pakiety, możesz to zrobić, usuwając `argQuery` folder projektu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono aplikację konsolową Java z wymaganymi pakietami wykresów zasobów i uruchomimy pierwsze zapytanie. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)