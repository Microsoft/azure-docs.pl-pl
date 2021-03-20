---
title: Azure Windows Communication Foundation (WCF) — hybrydowa aplikacja lokalna/w chmurze (.NET) | Microsoft Docs
description: Dowiedz się, jak uwidocznić lokalną usługę WCF dla aplikacji internetowej w chmurze za pomocą usługi Azure Relay
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 22b582634b623b39545eca225c8df0130606c2bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90983888"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Uwidacznianie lokalnej usługi WCF dla aplikacji internetowej w chmurze za pomocą usługi Azure Relay

Ten artykuł przedstawia sposób tworzenia hybrydowej aplikacji w chmurze przy użyciu platformy Microsoft Azure i programu Visual Studio. Tworzysz aplikację, która używa wielu zasobów platformy Azure w chmurze. Ten samouczek ułatwia naukę:

* Jak utworzyć lub przystosować istniejącą usługę sieci Web do użytku przez rozwiązanie sieci Web.
* Jak używać usługi przekaźnika Azure Windows Communication Foundation (WCF) do udostępniania danych między aplikacją platformy Azure a usługą sieci Web hostowaną w innym miejscu.

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
>
> * Zainstaluj wymagania wstępne dla tego samouczka.
> * Przegląd scenariusza.
> * Tworzenie przestrzeni nazw.
> * Utwórz serwer lokalny.
> * Utwórz aplikację ASP .NET.
> * Uruchom aplikację lokalnie.
> * Wdróż aplikację sieci Web na platformie Azure.
> * Uruchom aplikację na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .
* [Program Visual Studio 2015 lub nowszy](https://www.visualstudio.com). W przykładach w tym samouczku użyto programu Visual Studio 2019.
* Zestaw Azure SDK dla platformy .NET. Zainstaluj go ze [strony pobierania zestawu SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Jak usługa Azure Relay pomaga w tworzeniu rozwiązań hybrydowych

Rozwiązania biznesowe zwykle składają się z kombinacji kodu niestandardowego i istniejącej funkcji. Kod niestandardowy zainteresuje nowe i unikatowe wymagania biznesowe. Dostępne rozwiązania i systemy udostępniają istniejące funkcje.

Architekci rozwiązań zaczynają stosować usługi w chmurze w celu łatwiejszej obsługi wymagań skali i obniżenia kosztów operacyjnych. W ten sposób stwierdzą, że istniejące zasoby usługi, których chcesz użyć jako bloków konstrukcyjnych dla swoich rozwiązań, znajdują się wewnątrz firmowej zapory i nie są z łatwością dostępne przez rozwiązanie w chmurze. Wiele usług wewnętrznych nie jest wbudowanych lub hostowanych w taki sposób, aby można je było łatwo uwidocznić na krawędzi sieci firmowej.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) podejmuje istniejące usługi sieci Web WCF i zapewnia, że te usługi są bezpiecznie dostępne dla rozwiązań, które znajdują się poza obwodem firmy, bez konieczności wprowadzania niepożądanych zmian w infrastrukturze sieci firmowej. Takie usługi przekazywania wciąż są hostowane wewnątrz istniejącego środowiska, ale delegują one nasłuchiwanie sesji i żądań przychodzących do usługi przekazywania hostowanej w chmurze. Usługa Azure Relay chroni także te usługi przed nieautoryzowanym dostępem przy użyciu uwierzytelniania za pomocą [sygnatury dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md) (SAS, Shared Access Signature).

## <a name="review-the-scenario"></a>Przegląd scenariusza

W tym samouczku utworzysz witrynę internetową ASP.NET, która umożliwi wyświetlanie listy produktów na stronie spisu produktów.

![Scenariusz][0]

W samouczku założono, że informacje o produktach znajdują się w istniejącym systemie lokalnym i uzyskujesz dostęp do tego systemu za pomocą usługi Azure Relay. Usługa sieci Web, która działa w prostej aplikacji konsolowej, symuluje tę sytuację. Zawiera zestaw produktów znajdujących się w pamięci. Możesz uruchomić tę aplikację konsolową na własnym komputerze i wdrożyć rolę sieci Web na platformie Azure. Dzięki temu zobaczysz, jak rola sieci Web działająca w centrum danych platformy Azure jest wywoływana na komputerze. To wywołanie jest wykonywane nawet wtedy, gdy komputer niemal będzie znajdował się za co najmniej jedną zaporą i warstwą translacji adresów sieciowych (NAT).

## <a name="set-up-the-development-environment"></a>Konfigurowanie środowiska projektowego

Przed rozpoczęciem tworzenia aplikacji dla platformy Azure pobierz potrzebne narzędzia i skonfiguruj swoje środowisko deweloperskie:

1. Zainstaluj zestaw Azure SDK dla platformy .NET ze [strony pobierania](https://azure.microsoft.com/downloads/) zestawów SDK.
1. W kolumnie **.NET** wybierz używaną wersję programu [Visual Studio](https://www.visualstudio.com) . Ten samouczek używa programu Visual Studio 2019.
1. Gdy zostanie wyświetlony monit o uruchomienie lub zapisanie Instalatora, wybierz pozycję **Uruchom**.
1. W oknie dialogowym **Instalator platformy sieci Web** wybierz pozycję **Zainstaluj** i Kontynuuj instalację.

Po zakończeniu instalacji masz wszystko, co jest niezbędne do rozpoczęcia opracowywania aplikacji. Zestaw SDK zawiera narzędzia, które pozwalają w łatwy sposób tworzyć aplikacje dla platformy Azure w programie Visual Studio.

## <a name="create-a-namespace"></a>Tworzenie przestrzeni nazw

Pierwszym krokiem jest utworzenie przestrzeni nazw i uzyskanie klucza [sygnatury dostępu współdzielonego (SAS, Shared Access Signature)](../service-bus-messaging/service-bus-sas.md). Przestrzeń nazw wyznacza granice każdej aplikacji uwidacznianej za pośrednictwem usługi przekaźnika. Klucz sygnatury dostępu współdzielonego jest generowany automatycznie przez system po utworzeniu przestrzeni nazw usługi. Kombinacja przestrzeni nazw i klucza sygnatury dostępu współdzielonego usługi dostarcza poświadczenia dla platformy Azure w celu uwierzytelnienia dostępu do aplikacji.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Tworzenie serwera lokalnego

Najpierw utworzysz symulowany system katalogu produktów w środowisku lokalnym.  Ten projekt jest aplikacją konsolową programu Visual Studio i używa [pakietu NuGet usługi Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) w celu uwzględnienia bibliotek i ustawień konfiguracji usługi Service Bus. <a name="create-the-project"></a>

1. Uruchom Microsoft Visual Studio jako administrator. Aby to zrobić, kliknij prawym przyciskiem myszy ikonę programu Visual Studio, a następnie wybierz polecenie **Uruchom jako administrator**.
1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt** wybierz pozycję **aplikacja konsoli (.NET Framework)** dla języka C# i wybierz pozycję **dalej**.
1. Nadaj projektowi nazwę *ProductsServer* i wybierz pozycję **Utwórz**.

   ![Skonfiguruj nowy projekt][11]

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **ProductsServer** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.
1. Wybierz pozycję **Przeglądaj**, a następnie wyszukaj i wybierz pozycję **windowsazure. ServiceBus**. Wybierz pozycję **Zainstaluj** i zaakceptuj warunki użytkowania.

   ![Wybieranie pakietu NuGet][13]

   Wymagane zestawy klientów są teraz przywoływane.

1. Dodaj nową klasę dla kontraktu produktu. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **ProductsServer** i wybierz polecenie **Dodaj**  >  **klasę**.
1. W polu **Nazwa** wprowadź nazwę *ProductsContract. cs* i wybierz pozycję **Dodaj**.

Wprowadź następujące zmiany kodu w rozwiązaniu:

1. W pliku *ProductsContract.cs* zastąp definicję przestrzeni nazw następującym kodem, który definiuje kontrakt dla usługi.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. W programie *program. cs* Zastąp definicję przestrzeni nazw następującym kodem, który dodaje usługę profilu i hosta dla niego.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. W **Eksplorator rozwiązań** kliknij dwukrotnie pozycję **App.config** , aby otworzyć plik w edytorze programu Visual Studio. W dolnej części `<system.ServiceModel>` elementu, ale nadal w `<system.ServiceModel>` , Dodaj następujący kod XML. Pamiętaj, aby zamienić na `yourServiceNamespace` nazwę przestrzeni nazw oraz `yourKey` z kluczem sygnatury dostępu współdzielonego pobranym wcześniej z portalu:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > Błąd spowodowany przez `transportClientEndpointBehavior` to ostrzeżenie i nie jest problemem blokującym dla tego przykładu.

1. W *App.config* w `<appSettings>` elemencie Zastąp wartość parametrów połączenia parametrami połączenia, które zostały wcześniej uzyskane z portalu.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Wybierz kombinację klawiszy Ctrl + Shift + B lub wybierz opcję **Kompiluj**  >  **kompilację rozwiązania** , aby skompilować aplikację i sprawdzić dokładność pracy wykonanej do tej pory.

## <a name="create-an-aspnet-application"></a>Tworzenie aplikacji ASP.NET

W tej sekcji utworzysz prostą aplikację ASP.NET, która będzie wyświetlać dane pobrane z usługi produktów.

### <a name="create-the-project"></a>Tworzenie projektu

1. Upewnij się, że program Visual Studio jest uruchomiony jako administrator.
1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt** wybierz pozycję **aplikacja sieci Web ASP.NET (.NET Framework)** dla języka C# i wybierz pozycję **dalej**.
1. Nadaj projektowi nazwę *ProductsPortal* i wybierz pozycję **Utwórz**.
1. W obszarze **Utwórz nową aplikację sieci Web ASP.NET** wybierz pozycję **MVC** i wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.

   ![Wybieranie aplikacji internetowej ASP.NET][16]

1. W obszarze **Zmień uwierzytelnianie** wybierz pozycję **bez uwierzytelniania** , a następnie wybierz pozycję **OK**. Na potrzeby tego samouczka wdrażana jest aplikacja, która nie wymaga od użytkownika logowania.

    ![Określanie uwierzytelniania][18]

1. Po powrocie do **tworzenia nowej aplikacji sieci Web ASP.NET** wybierz pozycję **Utwórz** , aby utworzyć aplikację MVC.
1. Skonfiguruj zasoby platformy Azure dla nowej aplikacji sieci Web. Wykonaj kroki opisane w temacie [publikowanie aplikacji sieci Web](../app-service/quickstart-dotnet-framework.md#launch-the-publish-wizard). Następnie wróć do tego samouczka i przejdź do następnego kroku.
1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **modele** , a następnie wybierz polecenie **Dodaj**  >  **klasę**.
1. Nazwij klasy *Product. cs*, a następnie wybierz pozycję **Dodaj**.

    ![Tworzenie modelu produktu][17]

### <a name="modify-the-web-application"></a>Modyfikowanie aplikacji internetowej

1. W pliku *Product. cs* w programie Visual Studio Zastąp istniejącą definicję przestrzeni nazw następującym kodem:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. W **Eksplorator rozwiązań** rozwiń węzeł **Kontrolery**, a następnie kliknij dwukrotnie pozycję **HomeController. cs** , aby otworzyć plik w programie Visual Studio.
1. W pliku *HomeController.cs* zastąp istniejącą definicję przestrzeni nazw następującym kodem:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. W **Eksplorator rozwiązań** rozwiń węzeł **widoki**  >  **udostępnione**, a następnie kliknij dwukrotnie pozycję **_Layout. cshtml** , aby otworzyć plik w edytorze programu Visual Studio.
1. Zmień wszystkie wystąpienia `My ASP.NET Application` na *produkty Northwind Traders*.
1. Usuń `Home` linki, `About` i `Contact` . W poniższym przykładzie usuń wyróżniony kod.

    ![Usuwanie wygenerowanych elementów listy][41]

1. W **Eksplorator rozwiązań** rozwiń węzeł **widoki**  >  **Główne**, a następnie kliknij dwukrotnie pozycję **index. cshtml** , aby otworzyć plik w edytorze programu Visual Studio. Zastąp całą zawartość pliku następującym kodem:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Aby sprawdzić dokładność pracy wykonanej do tej pory, możesz wybrać kombinację klawiszy Ctrl + Shift + B, aby skompilować projekt.

### <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Uruchom aplikację, aby sprawdzić, czy działa.

1. Upewnij się, że projekt **ProductsPortal** jest aktywnym projektem. Kliknij prawym przyciskiem myszy nazwę projektu w **Eksplorator rozwiązań** i wybierz pozycję **Ustaw jako projekt startowy**.
1. W programie Visual Studio, wybierz klawisz F5.

Aplikacja powinna uruchomić się w przeglądarce.

![Zrzut ekranu przedstawia przykład aplikacji działającej w przeglądarce z wyróżnionym adresem URL.][21]

## <a name="put-the-pieces-together"></a>Składanie fragmentów

Następny krok polega na połączeniu lokalnego serwera produktów z aplikacją ASP.NET.

1. Jeśli nie jest jeszcze otwarty, w programie Visual Studio Otwórz projekt **ProductsPortal** , który został utworzony w sekcji [Tworzenie aplikacji ASP.NET](#create-an-aspnet-application) .
1. Podobnie jak w sekcji [Tworzenie serwera lokalnego](#create-an-on-premises-server) Dodaj pakiet NuGet do odwołań projektu. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **ProductsPortal** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.
1. Wyszukaj ciąg *WindowsAzure.ServiceBus*, a następnie wybierz element **WindowsAzure.ServiceBus**. Następnie Zakończ instalację i Zamknij to okno dialogowe.
1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **ProductsPortal** , a następnie wybierz pozycję **Dodaj**  >  **istniejący element**.
1. Przejdź do pliku *ProductsContract.cs* z projektu konsolowego **ProductsServer**. Wyróżnij *ProductsContract. cs*. Wybierz strzałkę w dół obok pozycji **Dodaj**, a następnie wybierz pozycję **Dodaj jako link**.

   ![Dodaj jako link][24]

1. W edytorze programu Visual Studio otwórz plik *HomeController.cs* i zastąp definicję przestrzeni nazw następującym kodem. Pamiętaj, aby zamienić na `yourServiceNamespace` nazwę przestrzeni nazw usługi i `yourKey` z kluczem sygnatury dostępu współdzielonego. Ten kod umożliwia klientowi wywołanie usługi lokalnej, zwracając wynik wywołania.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy rozwiązanie **ProductsPortal** . Upewnij się, że kliknij prawym przyciskiem myszy rozwiązanie, a nie projekt. Wybierz pozycję **Dodaj**  >  **istniejący projekt**.
1. Przejdź do projektu **ProductsServer**, a następnie kliknij dwukrotnie plik rozwiązania *ProductsServer.csproj*, aby go dodać.
1. **ProductsServer** musi być uruchomiona, aby wyświetlić dane w **ProductsPortal**. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy rozwiązanie **ProductsPortal** i wybierz pozycję **Właściwości** , aby wyświetlić **strony właściwości**.
1. Wybierz   >  **projekt startowy** wspólne właściwości, a następnie wybierz **wiele projektów startowych**. Upewnij się, że **ProductsServer** i **ProductsPortal** pojawiają się w tej kolejności i że **Akcja** dla obydwu jest **uruchamiana**.

      ![Wiele projektów startowych][25]

1. Zaznacz pozycję **wspólne właściwości**  >  **zależności projektu** po lewej stronie.
1. W obszarze **projekty** wybierz pozycję **ProductsPortal**. Upewnij się, że projekt **ProductsServer** jest wybrany.

    ![Zależności projektu][26]

1. W obszarze **projekty** wybierz pozycję **ProductsServer**. Upewnij się, że **ProductsPortal** nie jest zaznaczone, a następnie wybierz przycisk **OK** , aby zapisać zmiany.

## <a name="run-the-project-locally"></a>Lokalne uruchamianie projektu

Aby przetestować aplikację lokalnie, w programie Visual Studio wybierz klawisz F5. Serwer lokalny, **ProductsServer**, powinien najpierw zacząć pracę, a następnie aplikacja **ProductsPortal** powinna być uruchamiana w oknie przeglądarki. Tym razem pojawi się spis produktów zawierający dane pobrane z lokalnego systemu usługi produktów.

![Aplikacja internetowa][10]

Na stronie **ProductsPortal** wybierz pozycję **Odśwież** . Przy każdym odświeżeniu strony aplikacja serwera wyświetla komunikat podczas wywoływany metody `GetProducts()` z serwera **ProductsServer**.

Zamknij obie aplikacje przed przejściem do następnej sekcji.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Wdrażanie projektu ProductsPortal w aplikacji internetowej platformy Azure

Następnym krokiem jest ponowne opublikowanie frontonu aplikacji internetowej platformy Azure **ProductsPortal** :

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **ProductsPortal** , a następnie wybierz pozycję **Publikuj**. Na stronie **Publikowanie** wybierz pozycję **Publikuj**.

   > [!NOTE]
   > Gdy projekt sieci Web **ProductsPortal** zostanie automatycznie uruchomiony po wdrożeniu, w oknie przeglądarki może zostać wyświetlony komunikat o błędzie. Jest to oczekiwane. Błąd występuje, ponieważ aplikacja **ProductsServer** nie jest jeszcze uruchomiona.
   >

1. Skopiuj adres URL wdrożonej aplikacji sieci Web. Potrzebny będzie adres URL później. Możesz również uzyskać ten adres URL z okna **działania Azure App Service** w programie Visual Studio:

   ![Adres URL wdrożonej aplikacji][9]

1. Zamknij okno przeglądarki, aby zatrzymać działającą aplikację.

<a name="set-productsportal-as-web-app"></a>Przed uruchomieniem aplikacji w chmurze musisz upewnić się, że usługa **ProductsPortal** jest uruchamiana z poziomu programu Visual Studio jako aplikacja internetowa.

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt **ProductsPortal** i wybierz polecenie **Właściwości**.
1. Wybierz pozycję **Sieć Web**. W obszarze **Akcja początkowa** wybierz pozycję **początkowy adres URL**. Wprowadź adres URL wcześniej wdrożonej aplikacji sieci Web, w tym przykładzie `https://productsportal20190906122808.azurewebsites.net/` .

    ![Początkowy adres URL][27]

1. Wybierz pozycję **plik**  >  **Zapisz wszystko**.
1. Wybierz pozycję **Kompiluj**  >  **Skompiluj ponownie rozwiązanie**.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Wybierz klawisz F5, aby skompilować i uruchomić aplikację. Serwer lokalny, który jest aplikacją konsolową **ProductsServer** , powinien najpierw zacząć uruchamiać aplikację **ProductsPortal** w oknie przeglądarki, jak pokazano poniżej:

   ![Uruchamianie aplikacji internetowej na platformie Azure][1]

Spis produktów zawiera dane pobrane z systemu lokalnego usługi produktu, a następnie wyświetla te dane w aplikacji sieci Web. Sprawdź adres URL, aby upewnić się, że aplikacja **ProductsPortal** działa w chmurze jako aplikacja internetowa platformy Azure.

   > [!IMPORTANT]
   > Aplikacja konsolowa **ProductsServer** musi działać i być w stanie udostępniać dane aplikacji **ProductsPortal**. Jeśli przeglądarka wyświetli błąd, zaczekaj kilka sekund, aby **ProductsServer** załadować i wyświetlić następujący komunikat, a następnie Odśwież przeglądarkę.
   >

W przeglądarce Odśwież stronę **ProductsPortal** . Przy każdym odświeżeniu strony aplikacja serwera wyświetla komunikat podczas wywoływany metody `GetProducts()` z serwera **ProductsServer**.

![Zaktualizowane dane wyjściowe][38]

## <a name="next-steps"></a>Następne kroki

Przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Uwidacznianie lokalnej usługi WCF dla klienta WCF spoza sieci](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
