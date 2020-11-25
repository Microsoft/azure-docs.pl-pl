---
title: Aplikacja wielowarstwowa platformy .NET używająca usługi Azure Service Bus | Microsoft Docs
description: Samouczek platformy .NET umożliwia utworzenie na platformie Azure aplikacji wielowarstwowej, która używa kolejek usługi Service Bus do komunikacji między warstwami.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 40529df5195a29fbf2ff4887311932c2ffbf471d
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029899"
---
# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>Aplikacja wielowarstwowa platformy .NET używająca kolejek usługi Azure Service Bus

Tworzenie aplikacji dla platformy Microsoft Azure przy użyciu programu Visual Studio oraz bezpłatnego zestawu Azure SDK dla platformy .NET jest proste. Ten samouczek przeprowadzi Cię przez etapy tworzenia aplikacji, która używa wielu zasobów platformy Azure działających w środowisku lokalnym.

Dowiesz się:

* Jak umożliwić tworzenie aplikacji dla platformy Azure na komputerze przez pobranie i zainstalowanie jednego elementu.
* Jak używać programu Visual Studio do tworzenia aplikacji dla platformy Azure.
* Jak utworzyć aplikację wielowarstwową dla platformy Azure przy użyciu ról sieci Web i procesu roboczego.
* Jak komunikować się między warstwami przy użyciu kolejek usługi Service Bus.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

Dzięki temu samouczkowi będziesz w stanie utworzyć i uruchomić aplikację wielowarstwową w usłudze w chmurze platformy Azure. Fronton ma przypisaną rolę sieci Web programu ASP.NET MVC, a zaplecze rolę procesu roboczego używającego kolejki usługi Service Bus. Taką samą aplikację wielowarstwową z frontonem możesz utworzyć jako projekt sieci Web, który jest wdrażany w witrynie sieci Web platformy Azure, a nie jako usługa w chmurze. Możesz również wypróbować samouczek na temat [hybrydowych aplikacji lokalnych/w chmurze platformy .NET](../azure-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md).

Poniższy zrzut ekranu przedstawia ukończoną aplikację.

![Zrzut ekranu strony przesyłania aplikacji.][0]

## <a name="scenario-overview-inter-role-communication"></a>Omówienie scenariusza: komunikacja między rolami
Aby przesłać zamówienie do przetworzenia, składnik interfejsu użytkownika frontonu działający w roli sieci Web musi współdziałać z logiką warstwy środkowej uruchomionej w roli procesu roboczego. W tym przykładzie do komunikacji między warstwami użyto komunikatów usługi Service Bus.

Korzystanie z komunikatów usługi Service Bus między warstwą sieci Web i warstwą środkową oddziela dwa składniki. W przeciwieństwie do komunikatów bezpośrednich (czyli TCP lub HTTP), warstwa sieci Web nie łączy się bezpośrednio z warstwą środkową. Zamiast tego wypycha jednostki pracy jako komunikaty do usługi Service Bus, która w niezawodny sposób je przechowuje do momentu, aż środkowa warstwa będzie gotowa na ich użycie i przetworzenie.

Usługa Service Bus zapewnia dwie jednostki do obsługi komunikatów obsługiwanych przez brokera: kolejki i tematy. W przypadku kolejek każdy komunikat wysyłany do kolejki jest używany przez jednego odbiorcę. Tematy obsługują wzorzec publikowania/subskrypcji, w którym każdy opublikowany komunikat jest udostępniony dla subskrypcji zarejestrowanej w temacie. Każda subskrypcja logicznie zachowuje własną kolejkę komunikatów. Subskrypcje mogą być również konfigurowane przy użyciu reguł filtrowania, które ograniczają zestaw komunikatów przesyłanych do kolejki subskrypcji do tych, które są zgodne z filtrem. W poniższym przykładzie użyto kolejek usługi Service Bus.

![Diagram przedstawiający komunikację między rolą sieci Web, Service Bus i rolą proces roboczy.][1]

Ten mechanizm komunikacji ma kilka zalet w stosunku do komunikatów bezpośrednich:

* **Rozdzielenie czasowe.** Stosując asynchroniczny wzorzec przesyłania komunikatów, producenci i konsumenci nie muszą być online w tym samym czasie. Usługa Service Bus w niezawodny sposób przechowuje komunikaty do momentu, aż strona korzystająca z nich jest gotowa do ich odebrania. Dzięki temu składniki aplikacji rozproszonej mogą być rozłączone zarówno dobrowolnie, na przykład w celu przeprowadzenia konserwacji, jak i z powodu awarii składników, bez wywierania wpływu na system jako całość. Ponadto aplikacja odbierająca komunikaty może wymagać połączenia z Internetem tylko w pewnych porach dnia.
* **Wyrównywanie obciążenia.** W wielu aplikacjach obciążenie systemu różni się w czasie, podczas gdy czas przetwarzania wymagany dla każdej jednostki pracy jest zwykle stały. Pośredniczenie między producentami a konsumentami komunikatów przy pomocy kolejki oznacza, że aplikacja odbierająca komunikaty (proces roboczy) musi być aprowizowana tylko na tyle, aby poradzić sobie ze średnim obciążeniem, a nie obciążeniem szczytowym. Głębokość kolejki rośnie i zmniejsza się w zależności od zmian obciążenia przychodzącego. To wpływa na rozmiar infrastruktury potrzebnej do obsługi obciążenia aplikacji, a więc bezpośrednio przekłada się na oszczędność pieniędzy.
* **Równoważenie obciążenia.** W miarę wzrostu obciążenia można dodawać więcej procesów roboczych odczytujących z kolejki. Każdy komunikat jest przetwarzany tylko przez jeden z procesów roboczych. Ponadto równoważenie obciążenia oparte na ściąganiu pozwala optymalnie wykorzystać maszyny procesów roboczych, nawet jeżeli różnią się one pod względem mocy przetwarzania, ponieważ każda będzie ściągać komunikaty z własną maksymalną szybkością. Ten wzorzec jest często nazywany wzorcem *konkurujących konsumentów*.
  
  ![Diagram przedstawiający komunikację między rolą sieci Web, Service Bus i dwoma rolami procesu roboczego.][2]

W poniższych sekcjach omówiono kod, który implementuje tę architekturę.

## <a name="create-a-namespace"></a>Tworzenie przestrzeni nazw

Pierwszym krokiem jest utworzenie *przestrzeni nazw* i uzyskanie klucza [sygnatury dostępu współdzielonego (SAS)](service-bus-sas.md) dla tej przestrzeni nazw. Przestrzeń nazw wyznacza granice każdej aplikacji uwidacznianej za pośrednictwem usługi Service Bus. Klucz sygnatury dostępu współdzielonego jest generowany przez system po utworzeniu przestrzeni nazw. Kombinacja nazwy przestrzeni nazw i klucza sygnatury dostępu współdzielonego dostarcza poświadczenia dla usługi Service Bus w celu uwierzytelnienia dostępu do aplikacji.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Tworzenie roli sieci Web

W tej sekcji omówione zostanie tworzenie frontonu aplikacji. Najpierw tworzy się strony, które będą wyświetlane w aplikacji.
Następnie dodaje się kod, który przesyła elementy do kolejki usługi Service Bus i wyświetla informacje o stanie kolejki.

### <a name="create-the-project"></a>Tworzenie projektu

1. Używając uprawnień administratora, uruchom program Visual Studio: kliknij prawym przyciskiem myszy ikonę programu **Visual Studio**, a następnie kliknij polecenie **Uruchom jako administrator**. Emulator obliczeń platformy Azure opisany w dalszej części tego artykułu wymaga uruchomienia programu Visual Studio z uprawnieniami administratora.
   
   W menu **Plik** programu Visual Studio kliknij pozycję **Nowy**, a następnie kliknij pozycję **Projekt**.
2. W pozycji **Zainstalowane szablony** w obszarze **Visual C#** kliknij pozycję **Chmura**, a następnie kliknij pozycję **Usługa w chmurze Azure**. Nazwij projekt **MultiTierApp**. Następnie kliknij przycisk **OK**.
   
   ![Zrzut ekranu okna dialogowego Nowy projekt z wybraną chmurą i Visual C# usługa w chmurze Azure wyróżnioną i pokreśloną na czerwono.][9]
3. W okienku **Role** kliknij dwukrotnie pozycję **Role sieci Web ASP.NET**.
   
   ![Zrzut ekranu przedstawiający okno dialogowe Nowa Microsoft Azure usługa w chmurze z wybraną rolą sieci Web ASP.NET i WebRole1 także wybrane.][10]
4. Zatrzymaj kursor nad pozycją **WebRole1** w polu **Rozwiązania dla usług w chmurze Azure**, kliknij ikonę ołówka i zmień nazwę roli sieci Web na **FrontendWebRole**. Następnie kliknij przycisk **OK**. (Upewnij się, że wpisana nazwa to „Frontend”, pisana przez małe „e”, a nie „FrontEnd”.)
   
   ![Zrzut ekranu przedstawiający okno dialogowe Nowa usługa w chmurze Microsoft Azure z nazwą rozwiązania o nazwie FrontendWebRole.][11]
5. W oknie dialogowym **Nowy projekt ASP.NET** na liście **Wybierz szablon** kliknij pozycję **MVC**.
   
   ![Screenshotof okno dialogowe Nowy projekt ASP.NET z wyróżnioną pozycją MVC i pokreślone na czerwono, a opcja Zmień uwierzytelnianie opisane na czerwono.][12]
6. W tym samym oknie dialogowym **Nowy projekt ASP.NET** kliknij przycisk **Zmień uwierzytelnianie**. W oknie dialogowym **Zmienianie uwierzytelniania** upewnij się, że pole wyboru **Bez uwierzytelniania** jest zaznaczone, a następnie kliknij przycisk **OK**. W tym samouczku wdrożysz aplikację, która nie wymaga logowania użytkownika.
   
    ![Zrzut ekranu okna dialogowego Zmienianie uwierzytelniania z wybraną opcją bez uwierzytelniania i wyróżnioną kolorem czerwonym.][16]
7. W oknie dialogowym **Nowy projekt ASP.NET** kliknij przycisk **OK**, aby utworzyć projekt.
8. W **Eksploratorze rozwiązań** w projekcie **FrontendWebRole** kliknij prawym przyciskiem myszy pozycję **Odwołania**, a następnie kliknij pozycję **Zarządzaj pakietami NuGet**.
9. Kliknij kartę **Przeglądanie**, a następnie wyszukaj ciąg **WindowsAzure.ServiceBus**. Wybierz pakiet **WindowsAzure.ServiceBus**, kliknij pozycję **Zainstaluj** i zaakceptuj warunki użytkowania.
   
   ![Zrzut ekranu przedstawiający okno dialogowe Zarządzanie pakietami NuGet z wyróżnioną pozycją WindowsAzure. ServiceBus i opcją instalacji podaną w kolorze czerwonym.][13]
   
   Zwróć uwagę na to, że pojawią się odwołania do wymaganych zestawów klientów i dodanych zostanie kilka nowych plików kodu.
10. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Modele** i kliknij pozycję **Dodaj**, następnie kliknij pozycję **Klasa**. W okienku **Nazwa** wpisz nazwę **OnlineOrder.cs**. Następnie kliknij przycisk **Dodaj**.

### <a name="write-the-code-for-your-web-role"></a>Pisanie kodu dla roli sieci Web
W tej sekcji utworzysz różne strony, które będą wyświetlane przez Twoją aplikację.

1. W pliku OnlineOrder.cs w programie Visual Studio zastąp istniejącą definicję przestrzeni nazw następującym kodem:
   
   ```csharp
   namespace FrontendWebRole.Models
   {
       public class OnlineOrder
       {
           public string Customer { get; set; }
           public string Product { get; set; }
       }
   }
   ```
2. W **Eksploratorze rozwiązań** kliknij dwukrotnie pozycję **Controllers\HomeController.cs**. Dodaj następujące instrukcje **using** u góry pliku, aby uwzględnić przestrzenie nazw dla właśnie utworzonego modelu, a także usługę Service Bus.
   
   ```csharp
   using FrontendWebRole.Models;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   ```
3. W pliku HomeController.cs w programie Visual Studio zastąp istniejącą definicję przestrzeni nazw następującym kodem. Ten kod zawiera metody obsługi przesyłania elementów do kolejki.
   
   ```csharp
   namespace FrontendWebRole.Controllers
   {
       public class HomeController : Controller
       {
           public ActionResult Index()
           {
               // Simply redirect to Submit, since Submit will serve as the
               // front page of this application.
               return RedirectToAction("Submit");
           }
   
           public ActionResult About()
           {
               return View();
           }
   
           // GET: /Home/Submit.
           // Controller method for a view you will create for the submission
           // form.
           public ActionResult Submit()
           {
               // Will put code for displaying queue message count here.
   
               return View();
           }
   
           // POST: /Home/Submit.
           // Controller method for handling submissions from the submission
           // form.
           [HttpPost]
           // Attribute to help prevent cross-site scripting attacks and
           // cross-site request forgery.  
           [ValidateAntiForgeryToken]
           public ActionResult Submit(OnlineOrder order)
           {
               if (ModelState.IsValid)
               {
                   // Will put code for submitting to queue here.
   
                   return RedirectToAction("Submit");
               }
               else
               {
                   return View(order);
               }
           }
       }
   }
   ```
4. W menu **Kompilacja** kliknij pozycję **Kompiluj rozwiązanie**, aby przetestować dokładność pracy wykonanej do tej pory.
5. Teraz utwórz widok dla metody `Submit()`, która została utworzona wcześniej. Kliknij prawym przyciskiem myszy w obrębie metody `Submit()` (przeciążenie metody `Submit()`, która nie przyjmuje żadnych parametrów), a następnie wybierz pozycję **Dodaj widok**.
   
   ![Zrzut ekranu przedstawiający kod z fokusem na metodzie przesyłania i listy rozwijanej z wyróżnioną opcją Dodaj widok.][14]
6. Zostanie wyświetlone okno dialogowe tworzenia widoku. Na liście **Szablony** wybierz pozycję **Utwórz**. Z listy **Klasa modelu** wybierz klasę **OnlineOrder**.
   
   ![Zrzut ekranu okna dialogowego Dodawanie widoku z listami rozwijanymi szablonu i klasy modelu przedstawionymi w kolorze czerwonym.][15]
7. Kliknij pozycję **Dodaj**.
8. Teraz zmień nazwę wyświetlaną aplikacji. W **Eksploratorze rozwiązań** kliknij dwukrotnie plik **Views\Shared\\_Layout.cshtml**, aby otworzyć go w edytorze programu Visual Studio.
9. Zamień wszystkie wystąpienia hasła **My ASP.NET Application** na hasło **Northwind Traders Products**.
10. Usuń linki **Home**, **About** oraz **Contact**. Usuń wyróżniony kod:
    
    ![Zrzut ekranu przedstawiający kod z trzema wierszami akcji H T M L z wyróżnionym kodem linku.][28]
11. Na koniec zmodyfikuj stronę przesyłania w celu uwzględnienia niektórych informacji o kolejce. W **Eksploratorze rozwiązań** kliknij dwukrotnie plik **Views\Home\Submit.cshtml**, aby otworzyć go w edytorze programu Visual Studio. Dodaj następujący wiersz po pozycji `<h2>Submit</h2>`. Na razie pozycja `ViewBag.MessageCount` jest pusta. Wypełnisz ją później.
    
    ```html
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```
12. Twój interfejs użytkownika został zaimplementowany. Naciśnij klawisz **F5**, aby uruchomić aplikację i upewnić się, że jej wygląd jest zgodny z oczekiwaniami.
    
    ![Zrzut ekranu strony przesyłania aplikacji.][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Pisanie kodu przesyłającego elementy do kolejki usługi Service Bus
Teraz dodaj kod przesyłający elementy do kolejki. Najpierw utwórz klasę, która zawiera informacje o połączeniu kolejki usługi Service Bus. Następnie zainicjuj połączenie z pliku Global.aspx.cs. Na koniec zaktualizuj kod przesyłania, który został wcześniej utworzony w pliku HomeController.cs, aby rzeczywiście przesłać elementy do kolejki usługi Service Bus.

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **FrontendWebRole** (kliknij projekt, nie rolę). Kliknij pozycję **Dodaj**, a następnie kliknij pozycję **Klasa**.
2. Nadaj klasie nazwę **QueueConnector.cs**. Kliknij pozycję **Dodaj**, aby utworzyć klasę.
3. Teraz dodaj kod, który zawiera informacje o połączeniu i inicjuje połączenie z kolejką usługi Service Bus. Zamień całą zawartość pliku QueueConnector.cs na następujący kod, a następnie wprowadź wartości dla pozycji `your Service Bus namespace` (nazwa przestrzeni nazw) oraz pozycji `yourKey` będącej **kluczem podstawowym** uzyskanym wcześniej z usługi Azure Portal.
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Web;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   
   namespace FrontendWebRole
   {
       public static class QueueConnector
       {
           // Thread-safe. Recommended that you cache rather than recreating it
           // on every request.
           public static QueueClient OrdersQueueClient;
   
           // Obtain these values from the portal.
           public const string Namespace = "your Service Bus namespace";
   
           // The name of your queue.
           public const string QueueName = "OrdersQueue";
   
           public static NamespaceManager CreateNamespaceManager()
           {
               // Create the namespace manager which gives you access to
               // management operations.
               var uri = ServiceBusEnvironment.CreateServiceUri(
                   "sb", Namespace, String.Empty);
               var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                   "RootManageSharedAccessKey", "yourKey");
               return new NamespaceManager(uri, tP);
           }
   
           public static void Initialize()
           {
               // Using Http to be friendly with outbound firewalls.
               ServiceBusEnvironment.SystemConnectivity.Mode =
                   ConnectivityMode.Http;
   
               // Create the namespace manager which gives you access to
               // management operations.
               var namespaceManager = CreateNamespaceManager();
   
               // Create the queue if it does not exist already.
               if (!namespaceManager.QueueExists(QueueName))
               {
                   namespaceManager.CreateQueue(QueueName);
               }
   
               // Get a client to the queue.
               var messagingFactory = MessagingFactory.Create(
                   namespaceManager.Address,
                   namespaceManager.Settings.TokenProvider);
               OrdersQueueClient = messagingFactory.CreateQueueClient(
                   "OrdersQueue");
           }
       }
   }
   ```
4. Teraz upewnij się, że wywoływanie metody **Initialize** działa. W **Eksploratorze rozwiązań** kliknij dwukrotnie pozycję **Global.asax\Global.asax.cs**.
5. Dodaj następujący wiersz kodu na końcu metody **Application_Start**.
   
   ```csharp
   FrontendWebRole.QueueConnector.Initialize();
   ```
6. Na koniec zaktualizuj kod sieci Web, który został utworzony wcześniej, aby przesyłać elementy do kolejki. W **Eksploratorze rozwiązań** kliknij dwukrotnie pozycję **Controllers\HomeController.cs**.
7. Zaktualizuj metodę `Submit()` (przeciążenie, które nie przyjmuje żadnych parametrów), jak pokazano poniżej, aby uzyskać liczbę komunikatów dla kolejki.
   
   ```csharp
   public ActionResult Submit()
   {
       // Get a NamespaceManager which allows you to perform management and
       // diagnostic operations on your Service Bus queues.
       var namespaceManager = QueueConnector.CreateNamespaceManager();
   
       // Get the queue, and obtain the message count.
       var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
       ViewBag.MessageCount = queue.MessageCount;
   
       return View();
   }
   ```
8. Zaktualizuj metodę `Submit(OnlineOrder order)` (przeciążenie, które nie przyjmuje żadnych parametrów), jak pokazano poniżej, aby przesyłać informację o zamówieniu do kolejki.
   
   ```csharp
   public ActionResult Submit(OnlineOrder order)
   {
       if (ModelState.IsValid)
       {
           // Create a message from the order.
           var message = new BrokeredMessage(order);
   
           // Submit the order.
           QueueConnector.OrdersQueueClient.Send(message);
           return RedirectToAction("Submit");
       }
       else
       {
           return View(order); 
       }
   }
   ```
9. Możesz teraz ponownie uruchomić aplikację. Liczba komunikatów rośnie podczas każdego przesyłania zamówienia.
   
   ![Zrzut ekranu strony przesyłania aplikacji z liczbą komunikatów zwiększoną do 1.][18]

## <a name="create-the-worker-role"></a>Tworzenie roli procesu roboczego
Teraz utworzysz rolę procesu roboczego, która przetwarza zgłoszenia zamówień. W tym przykładzie użyto szablonu projektu programu Visual Studio **Proces roboczy z kolejką usługi Service Bus**. Wymagane poświadczenia zostały już uzyskane z portalu.

1. Upewnij się, że program Visual Studio został połączony z kontem platformy Azure.
2. W **Eksploratorze rozwiązań** programu Visual Studio kliknij prawym przyciskiem myszy folder **Role** znajdujący się pod projektem **MultiTierApp**.
3. Kliknij pozycję **Dodaj**, a następnie kliknij pozycję **Nowy projekt roli procesu roboczego**. Zostanie wyświetlone okno dialogowe **Dodawanie nowego projektu roli**.
   
   ![Zrzut ekranu okienka Eksploratora Soultion z opcją nowy projekt roli proces roboczy i opcję Dodaj wyróżnioną.][26]
4. W oknie dialogowym **Dodawanie nowego projektu roli** kliknij pozycję **Rola procesu roboczego z kolejką usługi Service Bus**.
   
   ![Zrzut ekranu okna dialogowego Nowy projekt roli usługi AD z rolą proces roboczy z Service Busą opcją kolejki z wyróżnioną i pokreśloną kolorem czerwonym.][23]
5. W polu **Nazwa** podaj nazwę projektu **OrderProcessingRole**. Następnie kliknij przycisk **Dodaj**.
6. Skopiuj do schowka parametry połączenia uzyskane w kroku 9 sekcji „Tworzenie przestrzeni nazw usługi Service Bus”.
7. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy rolę **OrderProcessingRole**, która została utworzona w kroku 5 (upewnij się, że klikasz rolę **OrderProcessingRole** w sekcji **Role**, a nie klasę). Następnie kliknij pozycję **Właściwości**.
8. Na karcie **Ustawienia** okna dialogowego **Właściwości** kliknij wewnątrz pola **Wartość** dla pozycji **Microsoft.ServiceBus.ConnectionString**, a następnie wklej skopiowaną w kroku 6 wartość punktu końcowego.
   
   ![Zrzut ekranu przedstawiający okno dialogowe właściwości z wybraną kartą Ustawienia i wiersz tabeli Microsoft. ServiceBus. ConnectionString wyróżniony kolorem czerwonym.][25]
9. Utwórz klasę **OnlineOrder**, aby reprezentowała zamówienia w czasie ich przetwarzania z kolejki. Możesz ponownie użyć klasy, która została już utworzona. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy klasę **OrderProcessingRole** (kliknij prawym przyciskiem myszy ikonę klasy, a nie rolę). Kliknij pozycję **Dodaj**, a następnie kliknij pozycję **Istniejący element**.
10. Przejdź do podfolderu **FrontendWebRole\Models**, a następnie kliknij dwukrotnie plik **OnlineOrder.cs**, aby dodać go do tego projektu.
11. W pliku **WorkerRole.cs** zmień wartość zmiennej **QueueName** z `"ProcessingQueue"` na `"OrdersQueue"`, jak pokazano w poniższym kodzie.
    
    ```csharp
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```
12. Dodaj następującą instrukcję using u góry pliku WorkerRole.cs.
    
    ```csharp
    using FrontendWebRole.Models;
    ```
13. W funkcji `Run()` wewnątrz wywołania `OnMessage()` zastąp zawartość klauzuli `try` następującym kodem.
    
    ```csharp
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```
14. Tworzenie aplikacji zostało zakończone. Możesz przetestować całą aplikację, klikając prawym przyciskiem myszy projekt MultiTierApp w Eksploratorze rozwiązań, wybierając opcję **Ustaw jako projekt startowy**, a następnie naciskając klawisz F5. Pamiętaj, że liczba komunikatów nie zwiększa się, ponieważ rola procesu roboczego przetwarza elementy z kolejki i oznacza je jako ukończone. Możesz wyświetlić dane wyjściowe śledzenia swojej roli procesu roboczego, wyświetlając interfejs użytkownika emulatora obliczeń platformy Azure. Możesz to zrobić, klikając prawym przyciskiem myszy ikonę emulatora w obszarze powiadomień na pasku zadań i wybierając pozycję **Pokaż interfejs użytkownika emulatora obliczeń**.
    
    ![Zrzut ekranu przedstawiający elementy wyświetlane po kliknięciu ikony emulatora. Pokaż interfejs użytkownika emulatora obliczeń znajduje się na liście opcji.][19]
    
    ![Zrzut ekranu przedstawiający okno dialogowe emulator obliczeń Microsoft Azure (Express).][20]

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat usługi Service Bus, zobacz następujące zasoby:  

* [Rozpoczynanie korzystania z kolejek usługi Service Bus][sbacomqhowto]
* [Service Bus service page][sbacom] (Strona usługi Service Bus)  

Aby dowiedzieć się więcej na temat wielowarstwowych scenariuszy, zobacz:  

* [Wielowarstwowa aplikacja platformy .NET korzystająca z tabel, kolejek i obiektów Blob magazynu][mutitierstorage]  

[0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
[2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
[9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
[10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
[11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
[12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
[13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
[14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
[15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
[16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
[17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app2.png

[19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
[20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
[23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
[25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
[26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
[28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

[sbacom]: https://azure.microsoft.com/services/service-bus/  
[sbacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
[mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
