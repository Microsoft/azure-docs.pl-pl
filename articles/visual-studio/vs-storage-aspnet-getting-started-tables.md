---
title: Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu programu Visual Studio (ASP.NET)
description: Jak rozpocząć korzystanie z usługi Azure Table Storage w projekcie ASP.NET w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c49df689ae859c93046c19af043aa2001dbb5481
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75979629"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Rozpoczynanie pracy z usługą Azure Table Storage i usługami połączonymi programu Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Omówienie

Usługa Azure Table Storage umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa to magazyn danych NoSQL, który akceptuje uwierzytelnione wywołania z chmury platformy Azure i poza nią. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.

W tym samouczku pokazano, jak napisać kod ASP.NET dla niektórych typowych scenariuszy przy użyciu jednostek usługi Azure Table Storage. Te scenariusze obejmują tworzenie tabeli i Dodawanie, wykonywanie zapytań i usuwanie jednostek tabel. 

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Konto usługi Azure Storage](../storage/common/storage-account-create.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Tworzenie kontrolera MVC 

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **Kontrolery**, a następnie z menu kontekstowego wybierz pozycję **Dodaj->kontroler**.

    ![Dodawanie kontrolera do aplikacji ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC 5 — pusty**, a następnie wybierz pozycję **Dodaj**.

    ![Określ typ kontrolera MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. W oknie dialogowym **Dodawanie kontrolera** Nadaj nazwę kontrolerowi *TablesController*i wybierz pozycję **Dodaj**.

    ![Nazwij kontroler MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Dodaj następujące dyrektywy *using* do `TablesController.cs` pliku:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Tworzenie klasy modelu

Wiele przykładów w tym artykule używa klasy pochodnej **klasy tableentity**o nazwie **CustomerEntity**. Poniższe kroki przeprowadzą Cię przez proces deklarowania tej klasy jako klasy modelu:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **modele**, a następnie z menu kontekstowego wybierz polecenie **Dodaj klasy >**.

1. W oknie dialogowym **Dodaj nowy element** Nadaj nazwę klasie, **CustomerEntity**.

1. Otwórz `CustomerEntity.cs` plik i Dodaj następującą dyrektywę **using** :

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Zmodyfikuj klasę tak, aby po zakończeniu Klasa została zadeklarowana jak w poniższym kodzie. Klasa deklaruje klasę jednostki o nazwie **CustomerEntity** , która używa imienia klienta jako klucza wiersza i nazwiska jako klucza partycji.

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>Tworzenie tabeli

Poniższe kroki ilustrują sposób tworzenia tabeli:

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki opisane w artykule [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **Create** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach metody **Create** można uzyskać obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz obiekt **CloudTableClient** reprezentuje klienta usługi Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz obiekt w **chmurze** , który reprezentuje odwołanie do żądanej nazwy tabeli. Metoda **CloudTableClient. GetTableReference** nie wykonuje żądania względem magazynu tabel. Odwołanie jest zwracane niezależnie od tego, czy tabela istnieje. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Wywołaj metodę **Cloud. metodę createifnotexists** , aby utworzyć tabelę, jeśli jeszcze nie istnieje. Metoda **Cloud. metodę createifnotexists** zwraca **wartość true** , jeśli tabela nie istnieje i została pomyślnie utworzona. W przeciwnym razie zwracana jest **wartość false** .    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Zaktualizuj **ViewBag** o nazwę tabeli.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **tabele**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** w polu Nazwa widoku wprowadź wartość **Utwórz** , a następnie wybierz pozycję **Dodaj**.

1. Otwórz `CreateTable.cshtml`i zmodyfikuj go tak, aby wyglądał jak w poniższym fragmencie kodu:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Utwórz tabelę** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Tworzenie tabeli](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Jak wspomniano wcześniej, Metoda **Cloud. metodę createifnotexists** zwraca **wartość true** tylko wtedy, gdy tabela nie istnieje i została utworzona. W związku z tym, jeśli aplikacja zostanie uruchomiona, gdy tabela istnieje, metoda zwraca **wartość false**. Aby uruchomić aplikację wiele razy, należy usunąć tabelę przed ponownym uruchomieniem aplikacji. Usuwanie tabeli można wykonać za pomocą metody **Cloud. Delete** . Możesz również usunąć tabelę przy użyciu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) lub [Eksplorator usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

*Jednostki* mapują do\# obiektów C przy użyciu klasy niestandardowej pochodnej od **klasy tableentity**. Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki. W tej sekcji zobaczysz, jak zdefiniować klasę jednostki, która używa imienia klienta jako klucza wiersza i nazwiska jako klucza partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji, niemniej użycie różnych kluczy partycji umożliwia zwiększenie skalowalności operacji równoległych. Dla każdej właściwości, która powinna być przechowywana w usłudze Table Service, właściwość musi być właściwością publiczną obsługiwanego typu, która ujawnia zarówno ustawienia, jak i pobieranie wartości.
Klasa jednostki *musi* deklarować publiczny Konstruktor bez parametrów.

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki opisane w artykule [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment).

1. Otwórz plik `TablesController.cs`.

1. Dodaj następującą dyrektywę, aby kod w `TablesController.cs` pliku mógł uzyskać dostęp do klasy **CustomerEntity** :

    ```csharp
    using StorageAspnet.Models;
    ```

1. Dodaj metodę o nazwie **AddEntity** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W metodzie **AddEntity** Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz obiekt **CloudTableClient** reprezentuje klienta usługi Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz obiekt w **chmurze** , który reprezentuje odwołanie do tabeli, do której zamierzasz dodać nową jednostkę. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz wystąpienie i zainicjuj klasę **CustomerEntity** .

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Utwórz obiekt **TableOperation** , który wstawia jednostkę klient.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Wykonaj operację wstawiania, wywołując metodę **Cloud. Execute** . Wynik operacji można sprawdzić, sprawdzając Właściwość **pozycji tableresult. HttpStatusCode** . Kod stanu 2xx wskazuje, że akcja żądana przez klienta została przetworzona pomyślnie. Na przykład pomyślne wstawienia nowych jednostek są wynikiem kodu stanu HTTP 204, co oznacza, że operacja została pomyślnie przetworzona, a serwer nie zwrócił żadnej zawartości.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Zaktualizuj **ViewBag** z nazwą tabeli oraz wynikami operacji wstawiania.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **tabele**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź **AddEntity** dla nazwy widoku, a następnie wybierz pozycję **Dodaj**.

1. Otwórz `AddEntity.cshtml`i zmodyfikuj go tak, aby wyglądał jak w poniższym fragmencie kodu:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Dodaj jednostkę** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Dodawanie jednostki](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Aby sprawdzić, czy jednostka została dodana, wykonaj kroki opisane w sekcji, aby [uzyskać pojedynczą jednostkę](#get-a-single-entity). Możesz również użyć [Eksplorator usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) , aby wyświetlić wszystkie jednostki dla tabel.

## <a name="add-a-batch-of-entities-to-a-table"></a>Dodawanie partii jednostek do tabeli

Oprócz możliwości [dodawania jednostki do tabeli pojedynczo](#add-an-entity-to-a-table)można także dodać jednostki w partii. Dodawanie jednostek w usłudze Batch zmniejsza liczbę operacji rundy między kodem a usługą tabel platformy Azure. Poniższe kroki ilustrują, jak dodać wiele jednostek do tabeli przy użyciu jednej operacji wstawiania:

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki opisane w artykule [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment).

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **addentities** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W metodzie **addentities** Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz obiekt **CloudTableClient** reprezentuje klienta usługi Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz obiekt w **chmurze** , który reprezentuje odwołanie do tabeli, do której chcesz dodać nowe jednostki. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Tworzenie wystąpienia niektórych obiektów klienta na podstawie klasy modelu **CustomerEntity** przedstawionej w sekcji [Dodaj jednostkę do tabeli](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Pobierz obiekt **obiektu tablebatchoperation** .

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Dodaj jednostki do obiektu operacji wstawiania wsadowego.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Wykonaj operację wstawiania wsadowego, wywołując metodę **Cloud. ExecuteBatch** .   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. Metoda **Cloud. ExecuteBatch** zwraca listę obiektów **pozycji tableresult** , w których każdy obiekt **pozycji tableresult** można sprawdzić w celu określenia sukcesu lub niepowodzenia poszczególnych operacji. Na potrzeby tego przykładu Przekaż listę do widoku i pozwól, aby widok wyświetlał wyniki każdej operacji. 
 
    ```csharp
    return View(results);
    ```

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **tabele**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź **addentities** dla nazwy widoku, a następnie wybierz pozycję **Dodaj**.

1. Otwórz `AddEntities.cshtml`i zmodyfikuj go tak, aby wyglądał następująco.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Dodaj jednostki** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Dodawanie jednostek](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Aby sprawdzić, czy jednostka została dodana, wykonaj kroki opisane w sekcji, aby [uzyskać pojedynczą jednostkę](#get-a-single-entity). Możesz również użyć [Eksplorator usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) , aby wyświetlić wszystkie jednostki dla tabel.

## <a name="get-a-single-entity"></a>Pobieranie pojedynczej jednostki

W tej części przedstawiono sposób uzyskiwania pojedynczej jednostki z tabeli przy użyciu klucza wiersza i klucza partycji jednostki. 

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki opisane w artykule [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment)i używanie danych z [dodawania partii jednostek do tabeli](#add-a-batch-of-entities-to-a-table). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **GetSingle** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W ramach metody **GetSingle** Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz obiekt **CloudTableClient** reprezentuje klienta usługi Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz obiekt w **chmurze** , który reprezentuje odwołanie do tabeli, z której pobierasz jednostkę. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz obiekt operacji pobierania, który pobiera obiekt Entity pochodzący z **klasy tableentity**. Pierwszy parametr to *partitionKey*, a drugi parametr to *rowKey*. Korzystając z klasy **CustomerEntity** i danych przedstawionych w sekcji [Dodaj partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table), Poniższy fragment kodu wysyła zapytanie do tabeli dla jednostki **CustomerEntity** o wartości *partitionKey* "Kowalski" i *rowKey* wartość "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Wykonaj operację pobierania.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Przekaż wynik do widoku do wyświetlenia.

    ```csharp
    return View(result);
    ```

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **tabele**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź wartość **GetSingle** dla nazwy widoku, a następnie wybierz pozycję **Dodaj**.

1. Otwórz `GetSingle.cshtml`i zmodyfikuj go tak, aby wyglądał jak w poniższym fragmencie kodu:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Uruchom aplikację i wybierz pozycję **Pobierz pojedynczą** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Pobierz pojedynczy](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji

Jak wspomniano w sekcji, [Dodaj jednostkę do tabeli](#add-an-entity-to-a-table), kombinację partycji i klucza wiersza jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki z różnymi kluczami partycji. W tej sekcji przedstawiono sposób wykonywania zapytań względem tabeli dla wszystkich jednostek z określonej partycji.  

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki opisane w artykule [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment)i używanie danych z [dodawania partii jednostek do tabeli](#add-a-batch-of-entities-to-a-table). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **getpartition** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W metodzie **getpartition** Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz obiekt **CloudTableClient** reprezentuje klienta usługi Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz obiekt w **chmurze** , który reprezentuje odwołanie do tabeli, z której są pobierane jednostki. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz wystąpienie obiektu **TableQuery** , określając zapytanie w klauzuli **WHERE** . Korzystając z klasy **CustomerEntity** i danych przedstawionych w sekcji [Dodaj partię jednostek do tabeli](#add-a-batch-of-entities-to-a-table), Poniższy fragment kodu wysyła zapytanie do tabeli dla wszystkich jednostek, w których **PartitionKey** (nazwisko klienta) ma wartość "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. W pętli Wywołaj metodę **Cloud. ExecuteQuerySegmented** , która przekazuje obiekt zapytania, który został utworzony w poprzednim kroku.  Metoda **Cloud. ExecuteQuerySegmented** zwraca obiekt **TableContinuationToken** , który — gdy **wartość null** wskazuje, że nie ma więcej jednostek do pobrania. W obrębie pętli Użyj innej pętli, aby wykonać iterację zwróconych jednostek. W poniższym przykładzie kodu każda zwrócona jednostka jest dodawana do listy. Po zakończeniu pętli lista jest przenoszona do widoku do wyświetlenia: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **tabele**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź wartość **getpartition** dla nazwy widoku, a następnie wybierz pozycję **Dodaj**.

1. Otwórz `GetPartition.cshtml`i zmodyfikuj go tak, aby wyglądał jak w poniższym fragmencie kodu:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Pobierz partycję** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Pobierz partycję](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Usuwanie jednostki

W tej sekcji przedstawiono sposób usuwania jednostki z tabeli.

> [!NOTE]
> 
> W tej sekcji założono, że wykonano kroki opisane w artykule [Konfigurowanie środowiska deweloperskiego](#set-up-the-development-environment)i używanie danych z [dodawania partii jednostek do tabeli](#add-a-batch-of-entities-to-a-table). 

1. Otwórz plik `TablesController.cs`.

1. Dodaj metodę o nazwie **DeleteEntity** , która zwraca **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. W metodzie **DeleteEntity** Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure: (Zmień * &lt;nazwę konta magazynu>* na nazwę konta usługi Azure Storage, do którego uzyskujesz dostęp).
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz obiekt **CloudTableClient** reprezentuje klienta usługi Table Service.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Pobierz obiekt w **chmurze** , który reprezentuje odwołanie do tabeli, z której usuwasz jednostkę. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Utwórz obiekt operacji usuwania, który pobiera obiekt Entity pochodzący z **klasy tableentity**. W tym przypadku używamy klasy **CustomerEntity** i danych przedstawionych w sekcji [Dodawanie partii jednostek do tabeli](#add-a-batch-of-entities-to-a-table). Element **ETag** jednostki musi być ustawiony na prawidłową wartość.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Wykonaj operację usuwania.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Przekaż wynik do widoku do wyświetlenia.

    ```csharp
    return View(result);
    ```

1. W **Eksplorator rozwiązań**rozwiń folder **widoki** , kliknij prawym przyciskiem myszy pozycję **tabele**, a następnie z menu kontekstowego wybierz polecenie **Dodaj->widok**.

1. W oknie dialogowym **Dodawanie widoku** wprowadź **DeleteEntity** w polu Nazwa widoku, a następnie wybierz pozycję **Dodaj**.

1. Otwórz `DeleteEntity.cshtml`i zmodyfikuj go tak, aby wyglądał jak w poniższym fragmencie kodu:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. W **Eksplorator rozwiązań**rozwiń węzeł **widoki — >folder udostępniony** i Otwórz `_Layout.cshtml`.

1. Po ostatnim **pliku HTML. actionlink**Dodaj następujący **plik HTML. actionlink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Uruchom aplikację, a następnie wybierz pozycję **Usuń jednostkę** , aby zobaczyć wyniki podobne do poniższego zrzutu ekranu:
  
    ![Pobierz pojedynczy](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Następne kroki
Wyświetl więcej poradników dotyczących funkcji, aby dowiedzieć się więcej o dodatkowych opcjach przechowywania danych na platformie Azure.

  * [Rozpoczynanie pracy z usługą Azure Blob Storage i usługami połączonymi programu Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Rozpoczynanie pracy z usługą Azure queue storage i usługami połączonymi programu Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
