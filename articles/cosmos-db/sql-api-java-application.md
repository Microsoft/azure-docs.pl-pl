---
title: 'Samouczek: Tworzenie aplikacji sieci Web w języku Java przy użyciu Azure Cosmos DB i interfejsu API SQL'
description: 'Samouczek: ten samouczek aplikacji sieci Web Java pokazuje, jak używać Azure Cosmos DB i interfejsu API SQL do przechowywania i uzyskiwania dostępu do danych z aplikacji Java hostowanej w usłudze Azure Websites.'
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 489f4aad78437edad2184e773d97d35af24f3249
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285998"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Samouczek: Tworzenie aplikacji sieci Web w języku Java przy użyciu Azure Cosmos DB i interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

W tym samouczku aplikacji internetowej w języku Java pokazano, jak przy użyciu usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji w języku Java hostowanej w usłudze Azure App Service Web Apps. Z tego artykułu dowiesz się:

* Jak utworzyć prostą aplikację JSP (JavaServer Pages) w środowisku Eclipse.
* Jak pracować z usługą Azure Cosmos DB przy użyciu [zestawu SDK języka Java dla usługi Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java).

W tym samouczku aplikacji w języku Java pokazano, jak utworzyć aplikację do zarządzania zadaniami opartą na sieci Web, która pozwala na tworzenie, pobieranie i oznaczanie zadań jako ukończonych, jak pokazano na poniższej ilustracji. Każde z zadań z listy zadań do wykonania będzie przechowywane jako dokument JSON w usłudze Azure Cosmos DB.

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="Aplikacja My ToDo List w języku Java":::

> [!TIP]
> Ten samouczek tworzenia aplikacji zakłada, że masz już pewne doświadczenie w korzystaniu z języka Java. Jeśli nie znasz języka Java lub [wstępnie wymaganych narzędzi](#Prerequisites), zalecamy pobranie kompletnego projektu [todo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) z usługi GitHub i skompilowanie go zgodnie z [instrukcjami znajdującymi się na końcu artykułu](#GetProject). Po jego skompilowaniu możesz przejrzeć ten artykuł, aby przeanalizować kod w kontekście projektu.  

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>Wymagania wstępne dotyczące tego samouczka aplikacji internetowej w języku Java

Przed rozpoczęciem korzystania z tego samouczka tworzenia aplikacji należy dysponować następującymi elementami:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Zestaw Java Development Kit (JDK) 7+](/java/azure/jdk/).
* [Środowisko Eclipse IDE for Java EE Developers.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Witryna internetowa platformy Azure z włączonym środowiskiem uruchomieniowym języka Java (np. Tomcat lub Jetty).](../app-service/quickstart-java.md)

Jeśli instalujesz te narzędzia po raz pierwszy, coreservlets.com zawiera Przewodnik po procesie instalacji w sekcji szybki start w [samouczku: Instalowanie TomCat7 i używanie go z](https://www.youtube.com/watch?v=jOdCfW7-ybI&t=2s) zaem.

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Tworzenie konta usługi Azure Cosmos DB

Zacznijmy od utworzenia konta usługi Azure Cosmos DB. Jeśli masz już konto lub jeśli korzystasz z emulatora usługi Azure Cosmos DB na potrzeby tego samouczka, możesz od razu przejść do sekcji [Krok 2. Tworzenie aplikacji Java JSP](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>Tworzenie aplikacji Java JSP

Aby utworzyć aplikację JSP:

1. Najpierw zaczniemy od utworzenia projektu Java. Uruchom środowisko Eclipse, a następnie w menu **File** (Plik) kliknij polecenie **New** (Nowy), a potem kliknij polecenie **Dynamic Web Project** (Dynamiczny projekt sieci Web). Jeśli **dynamiczny projekt sieci Web** nie jest wyświetlany na liście dostępnych projektów, wykonaj następujące czynności: kliknij pozycję **plik**, kliknij pozycję **Nowy**, kliknij pozycję **projekt**..., rozwiń węzeł **Sieć Web**, kliknij pozycję **dynamiczny projekt sieci Web**, a następnie kliknij przycisk **dalej**.
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="Tworzenie aplikacji Java JSP":::

1. Wprowadź nazwę projektu w polu **Project name** (Nazwa projektu) i z menu rozwijanego **Target Runtime** (Docelowe środowisko uruchomieniowe) opcjonalnie wybierz wartość (np. Apache Tomcat v7.0), a następnie kliknij przycisk **Finish** (Zakończ). Wybranie docelowego środowiska uruchomieniowego umożliwia uruchamianie projektu lokalnie za pośrednictwem środowiska Eclipse.

1. W środowisku Eclipse w widoku Project Explorer (Eksplorator projektów) rozwiń projekt. Kliknij prawym przyciskiem myszy folder **WebContent**, kliknij polecenie **New** (Nowy), a następnie kliknij polecenie **JSP File** (Plik JSP).

1. W oknie dialogowym **New JSP File** (Nowy plik JSP) nazwij plik **index.jsp**. Pozostaw folder **WebContent** jako folder nadrzędny, w sposób pokazany na poniższej ilustracji, a następnie kliknij przycisk **Next** (Dalej).
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="Tworzenie nowego pliku JSP — samouczek aplikacji internetowej w języku Java":::

1. W oknie dialogowym **Select JSP Template** (Wybierz szablon pliku JSP) na potrzeby tego samouczka wybierz szablon **New JSP File (html)**, a następnie kliknij przycisk **Finish** (Zakończ).

1. Gdy plik *index.jsp* zostanie otwarty w oknie przezaćmienie, Dodaj tekst do wyświetlenia **Hello World!** wewnątrz istniejącego elementu `<body>`. Zaktualizowana zawartość elementu `<body>` powinna wyglądać podobnie do następującego kodu:

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. Zapisz plik *pindex.js* .

1. Jeśli docelowe środowisko uruchomieniowe zostało ustawione w kroku 2, możesz kliknąć pozycję **Project** (Projekt), a następnie pozycję **Run** (Uruchom), aby uruchomić aplikację JSP lokalnie:

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Witaj świecie – samouczek aplikacji w języku Java":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>Instalowanie zestawu Java SDK usługi SQL

Najprostszym sposobem pobrania zestawu SDK Java usługi SQL i jego zależności jest skorzystanie z usługi [Apache Maven](https://maven.apache.org/). W tym celu należy przekonwertować projekt do projektu Maven, wykonując następujące czynności:

1. Kliknij prawym przyciskiem myszy projekt w widoku Project Explorer (Eksplorator projektów), kliknij polecenie **Configure** (Konfiguruj), kliknij pozycję **Convert to Maven Project** (Konwertuj na projekt Maven).

1. W oknie **Tworzenie nowego pliku pom** zaakceptuj ustawienia domyślne, a następnie kliknij przycisk **Zakończ**.

1. W widoku **Project Explorer** (Eksplorator projektów) otwórz plik pom.xml.

1. Na karcie **Dependencies** (Zależności) w okienku **Dependencies** (Zależności) kliknij pozycję **Add** (Dodaj).

1. W oknie **Select Dependency** (Wybierz zależność) wykonaj następujące czynności:
   
   * W polu **Identyfikator grupy** wprowadź wartość `com.azure` .
   * W polu **Identyfikator artefaktu** wprowadź `azure-cosmos` .
   * W polu **wersja** wprowadź wartość `4.11.0` .
  
   Można też dodać kod XML zależności dla identyfikatora grupy i identyfikatora artefaktu bezpośrednio do pliku *pom.xml* :

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-cosmos</artifactId>
     <version>4.11.0</version>
   </dependency>
   ```

1. Kliknij przycisk **OK** , a Maven zainstaluje zestaw SQL Java SDK lub zapisz plik pom.xml.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>Korzystanie z usługi Azure Cosmos DB w aplikacji Java

Teraz Dodajmy modele, widoki i kontrolery do aplikacji sieci Web.

### <a name="add-a-model"></a>Dodawanie modelu

Najpierw zdefiniujemy model w nowym pliku *TodoItem. Java*. `TodoItem`Klasa definiuje schemat elementu wraz z metodami pobierającymi i setter:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>Dodaj klasy obiektów dostępu do danych (DAO)

Utwórz obiekt dostępu do danych (DAO) do abstrakcyjnego utrwalania elementów do wykonania w Azure Cosmos DB. Aby zapisać zadania do wykonania w kolekcji, klient musi wiedzieć, w której bazie danych i kolekcji powinien je zachowywać (jak to jest wskazane przez linki do samego siebie). Ogólnie rzecz biorąc, najlepiej jest zapisać w pamięci podręcznej obiekty bazy danych i kolekcji, tam gdzie to jest możliwe, aby uniknąć dodatkowych połączeń do bazy danych.

1. Aby wywołać usługę Azure Cosmos DB, należy utworzyć wystąpienie nowego `cosmosClient` obiektu. Ogólnie rzecz biorąc najlepiej jest ponownie użyć `cosmosClient` obiektu zamiast konstruowania nowego klienta dla każdego kolejnego żądania. Klienta można użyć ponownie, definiując go w `cosmosClientFactory` klasie. Zaktualizuj wartości hosta i MASTER_KEY zapisane w [kroku 1](#CreateDB). Zastąp zmienną hosta adresem URI i Zastąp MASTER_KEY kluczem podstawowym. Użyj poniższego kodu, aby utworzyć `CosmosClientFactory` klasę w pliku *CosmosClientFactory. Java* :

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/CosmosClientFactory.java":::

1. Utwórz nowy plik *TodoDao. Java* i Dodaj `TodoDao` klasę w celu utworzenia, zaktualizowania, odczytania i usunięcia elementów do wykonania:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDao.java":::

1. Utwórz nowy plik *MockDao. Java* i Dodaj `MockDao` klasę, ta klasa implementuje `TodoDao` klasę do wykonywania operacji CRUD na elementach:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/MockDao.java":::

1. Utwórz nowy plik *DocDbDao. Java* i Dodaj `DocDbDao` klasę. Ta klasa definiuje kod, aby zachować TodoItems w kontenerze, pobiera bazę danych i kolekcję, jeśli istnieje, lub utworzyć nową, jeśli nie istnieje. W tym przykładzie używa się [Gson](https://code.google.com/p/google-gson/) do serializacji i deserializacji zwykłych obiektów Java TodoItem (Pojo) do dokumentów JSON. Aby zapisać zadania do wykonania w kolekcji, klient musi wiedzieć, w której bazie danych i kolekcji powinien je zachowywać (jak to jest wskazane przez linki do samego siebie). Ta klasa definiuje również funkcję pomocnika, która pobiera dokumenty przez inny atrybut (np. "ID"), a nie do samego łącza. Możesz użyć metody pomocnika, aby pobrać dokument JSON TodoItem o IDENTYFIKATORze, a następnie zdeserializować go do POJO.

   Możesz również użyć `cosmosClient` obiektu klienta, aby uzyskać kolekcję lub listę TodoItems przy użyciu zapytania SQL. Na koniec należy zdefiniować metodę Delete, aby usunąć TodoItem z listy. Poniższy kod przedstawia zawartość `DocDbDao` klasy:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/DocDbDao.java":::

1. Następnie utwórz nowy plik *TodoDaoFactory. Java* i Dodaj `TodoDaoFactory` klasę, która tworzy nowy obiekt DocDbDao:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>Dodawanie kontrolera

Dodaj kontroler *TodoItemController* do swojej aplikacji. W tym projekcie użyto [Projektu Lombok](https://projectlombok.org/), aby wygenerować konstruktor, metody pobierające, metody ustawiające i konstruktora. Alternatywnie można napisać ten kod ręcznie lub utworzyć go za pomocą IDE.

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>Utwórz serwletu

Następnie utwórz serwletu, aby kierować żądania HTTP do kontrolera. Utwórz plik *ApiServlet. Java* i Zdefiniuj w nim następujący kod:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Współpracuj nad resztą aplikacji Java

Teraz, gdy już zakończysz pracę z usługą, wszystko to jest możliwe do skompilowania szybkiego interfejsu użytkownika i przełączenia go do obiektu DAO.

1. Potrzebujesz interfejsu użytkownika sieci Web do wyświetlania użytkownikowi. Napiszmy ponownie *index.jsp* utworzony wcześniej przy użyciu następującego kodu:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. Na koniec Napisz kod JavaScript po stronie klienta, aby powiązać interfejs użytkownika sieci Web i serwletu razem:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. Teraz pozostało już tylko przetestować aplikację. Uruchom aplikację lokalnie, a następnie dodaj jakieś zadania do wykonania, wpisując nazwę zadania oraz jego kategorię i klikając przycisk **Add Task** (Dodaj zadanie). Po wyświetleniu elementu można zaktualizować, czy jest on kompletny, przełączając pole wyboru i klikając polecenie **Aktualizuj zadania**.

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Wdrożenie aplikacji w języku Java w usłudze Azure Web Sites

Usługa Azure Web Sites sprawia, że wdrożenie aplikacji Java sprowadza się do wyeksportowania aplikacji jako pliku WAR i przesłania go na serwer za pomocą systemu kontroli źródła (np. Git) lub FTP.

1. Aby wyeksportować aplikację jako plik WAR, kliknij prawym przyciskiem myszy projekt w widoku **Project Explorer** (Eksplorator projektów), kliknij pozycję **Export** (Eksportuj), a następnie kliknij pozycję **WAR File** (Plik WAR).

1. W oknie **WAR Export** (Eksport pliku WAR) wykonaj następujące czynności:
   
   * W polu projekt sieci Web wprowadź polecenie Azure-Cosmos-Java-Sample.
   * W polu Miejsce docelowe wybierz miejsce zapisania pliku WAR.
   * Kliknij przycisk **Finish** (Zakończ).

1. Teraz, gdy masz już plik WAR, możesz go po prostu przesłać do katalogu **webapps** swojej usługi Azure Web Sites. Aby uzyskać instrukcje dotyczące przekazywania pliku, zobacz [Add a Java application to Azure App Service Web Apps](../app-service/quickstart-java.md) (Dodawanie aplikacji Java do usługi Web Apps w usłudze Azure App Service). Po przekazaniu pliku WAR do katalogu webapps środowisko uruchomieniowe wykryje, że zostało dodane i zostanie automatycznie załadowane.

1. Aby wyświetlić gotową aplikację, przejdź do `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-cosmos-java-sample/` i zacznij dodawać zadania.

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>Pobieranie projektu z usługi GitHub

Wszystkie przykłady w tym samouczku są zawarte w projekcie [todo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) w usłudze GitHub. Aby zaimportować projekt todo do środowiska Eclipse, upewnij się, że dysponujesz oprogramowaniem i zasobami wymienionymi w sekcji [Wymagania wstępne](#Prerequisites), a następnie wykonaj następujące czynności:

1. Zainstaluj [Projekt Lombok](https://projectlombok.org/). Lombok służy w projekcie do generowania konstruktorów, metod pobierających i ustawiających. Po pobraniu pliku lombok.jar kliknij go dwukrotnie, aby go zainstalować, lub zainstaluj go przy użyciu wiersza polecenia.

1. Jeśli środowisko Eclipse jest otwarte, zamknij je i uruchom ponownie, aby załadować Lombok.

1. W środowisku Eclipse w menu **File** (Plik) kliknij pozycję **Import** (Importuj).

1. W okienku **Import** (Import), kliknij pozycję **Git** (Usługa Git), kliknij pozycję **Projects from Git** (Projekty z usługi Git), a następnie kliknij przycisk **Next** (Dalej).

1. Na ekranie **Select Repository Source** (Wybierz źródło repozytorium) kliknij pozycję **Clone URI** (Klonuj URI).

1. Na ekranie **Source Git Repository** (Źródłowe repozytorium Git) w polu **URI** wprowadź adres https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app, a następnie kliknij przycisk **Next** (Dalej).

1. Na ekranie **wybór gałęzi** upewnij się, że jest zaznaczona opcja **główny** , a następnie kliknij przycisk **dalej**.

1. Na ekranie **Local Destination** (Lokalne miejsce docelowe) kliknij przycisk **Browse** (Przeglądaj), aby wybrać folder, do którego można skopiować repozytorium, a następnie kliknij przycisk **Next** (Dalej).

1. Na ekranie **Select a wizard to use for importing projects** (Wybór kreatora importującego projekty) upewnij się, że jest zaznaczona opcja **Import existing projects** (Importuj istniejące projekty), a następnie kliknij przycisk **Next** (Dalej).

1. Na ekranie **Import Projects** (Import projektów) usuń zaznaczenie przy projekcie **DocumentDB**, a następnie kliknij przycisk **Finish** (Zakończ). Projekt DocumentDB zawiera zestaw SDK Java usługi Azure Cosmos DB, który zamiast tego zostanie dodany jako zależność.

1. W **Eksploratorze projektów** przejdź do Azure-Cosmos-Java-sample\src\com.Microsoft.Azure.Cosmos.sample.dao\DocumentClientFactory.Java i Zastąp wartości HOST i MASTER_KEY wartościami identyfikator URI i klucz podstawowy dla konta Azure Cosmos DB, a następnie Zapisz plik. Aby uzyskać więcej informacji, zobacz [krok 1. Utwórz konto usługi Azure Cosmos Database](#CreateDB).

1. W **Eksploratorze projektów** kliknij prawym przyciskiem myszy pozycję **Azure-Cosmos-Java-Sample**, kliknij pozycję **ścieżka kompilacji**, a następnie kliknij pozycję **Konfiguruj ścieżkę kompilacji**.

1. Na ekranie **Java Build Path** (Ścieżka kompilacji języka Java) w oknie po prawej stronie wybierz kartę **Libraries** (Biblioteki), a następnie kliknij pozycję **Add External JARs** (Dodaj zewnętrzne pliki JAR). Przejdź do lokalizacji pliku lombok.jar, kliknij przycisk **Open** (Otwórz), a następnie kliknij przycisk **OK**.

1. Użyj kroku 12, aby otworzyć ponownie okno **Properties** (Właściwości), a następnie w oknie po lewej stronie kliknij pozycję **Targeted Runtimes** (Docelowe środowiska uruchomieniowe).

1. Na ekranie **Targeted Runtimes** (Docelowe środowiska uruchomieniowe) kliknij pozycję **New** (Nowy), wybierz pozycję **Apache Tomcat v7.0**, a następnie kliknij przycisk **OK**.

1. Użyj kroku 12, aby otworzyć ponownie okno **Properties** (Właściwości), a następnie w oknie po lewej stronie kliknij pozycję **Project Facets** (Aspekty projektu).

1. Na ekranie **Project Facets** (Aspekty projektu) wybierz pozycję **Dynamic Web Module** (Dynamiczny moduł sieci Web) oraz pozycję **Java**, a następnie kliknij pozycję **OK**.

1. Na karcie **Servers** (Serwery) w dolnej części ekranu kliknij prawym przyciskiem myszy pozycję **Tomcat v7.0 Server at localhost**, a następnie kliknij pozycję **Add and Remove** (Dodaj i usuń).

1. W oknie **Dodawanie i usuwanie** Przenieś pozycję **Azure-Cosmos-Java-Sample** do **skonfigurowanego** pola, a następnie kliknij przycisk **Zakończ**.

1. Na karcie **Servers** (Serwery) kliknij prawym przyciskiem myszy pozycję **Tomcat v7.0 Server at localhost**, a następnie kliknij pozycję **Restart** (Uruchom ponownie).

1. W przeglądarce przejdź do `http://localhost:8080/azure-cosmos-java-sample/` i zacznij dodawać zadania do listy zadań. Należy pamiętać, że jeśli zmieniono domyślne wartości portów, zmień 8080 na wybraną wartość.

1. Aby wdrożyć projekt w witrynie sieci Web platformy Azure, zobacz [krok 6. Wdróż aplikację w witrynach sieci Web systemu Azure](#Deploy).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji node.js przy użyciu Azure Cosmos DB](sql-api-nodejs-application.md)
