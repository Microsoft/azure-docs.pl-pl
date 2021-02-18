---
title: Rozwiązywanie problemów z programem Visual Studio
description: Dowiedz się, jak rozwiązywać problemy z aplikacją App Service przy użyciu narzędzi debugowania zdalnego, śledzenia i rejestrowania wbudowanych w Visual Studio 2013.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 65eb4e8fefb74219c927fe507baceca7c55462be
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095712"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Rozwiązywanie problemów z aplikacją w Azure App Service przy użyciu programu Visual Studio
## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono sposób korzystania z narzędzi Visual Studio Tools w celu ułatwienia debugowania aplikacji w [App Service](./overview.md), przez uruchamianie w [trybie debugowania](/visualstudio/debugger/) zdalnie lub przez Przeglądanie dzienników aplikacji i dzienników serwera sieci Web.

Dowiesz się:

* Które funkcje zarządzania aplikacjami są dostępne w programie Visual Studio.
* Jak korzystać z widoku zdalnego programu Visual Studio, aby wprowadzać szybkie zmiany w aplikacji zdalnej.
* Jak uruchomić tryb debugowania zdalnie, gdy projekt jest uruchomiony na platformie Azure, zarówno dla aplikacji, jak i dla zadania WebJob.
* Jak utworzyć dzienniki śledzenia aplikacji i wyświetlić je podczas tworzenia aplikacji przez aplikację.
* Jak wyświetlać dzienniki serwera sieci Web, w tym szczegółowe komunikaty o błędach i śledzenie nieudanych żądań.
* Jak wysłać dzienniki diagnostyczne do konta usługi Azure Storage i wyświetlić je w tym miejscu.

Jeśli masz Visual Studio Ultimate, możesz również użyć [IntelliTrace](/visualstudio/debugger/intellitrace) do debugowania. IntelliTrace nie jest uwzględniony w tym samouczku.

## <a name="prerequisites"></a><a name="prerequisites"></a>Wymagania wstępne
Ten samouczek współpracuje ze środowiskiem programistycznym, projektem sieci Web i aplikacją App Service, która została skonfigurowana w temacie [Tworzenie aplikacji ASP.NET w programie Azure App Service](quickstart-dotnet-framework.md). W przypadku sekcji zadań WebJob potrzebna jest aplikacja utworzona w programie wprowadzenie do [zestawu SDK Azure WebJobs][GetStartedWJ].

Przykłady kodu przedstawione w tym samouczku dotyczą aplikacji sieci Web w języku C# MVC, ale procedury rozwiązywania problemów są takie same dla aplikacji Visual Basic i formularzy sieci Web.

W samouczku przyjęto założenie, że używasz programu Visual Studio 2019. 

Funkcja dzienników przesyłania strumieniowego działa tylko w przypadku aplikacji przeznaczonych .NET Framework 4 lub nowszych.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>Konfiguracja i zarządzanie aplikacjami
Program Visual Studio zapewnia dostęp do podzbioru funkcji zarządzania aplikacjami i ustawień konfiguracji dostępnych w [Azure Portal](https://go.microsoft.com/fwlink/?LinkId=529715). W tej sekcji zobaczysz, co jest dostępne za pomocą **Eksplorator serwera**. Aby zapoznać się z najnowszymi funkcjami integracji platformy Azure, wypróbuj również program **Cloud Explorer** . Możesz otworzyć oba okna z menu **Widok** .

1. Jeśli jeszcze nie zalogowano się do platformy Azure w programie Visual Studio, kliknij prawym przyciskiem myszy pozycję **Azure** , a następnie wybierz pozycję Połącz z **subskrypcją Microsoft Azure** w **Eksplorator serwera**.

    Alternatywą jest zainstalowanie certyfikatu zarządzania, który umożliwia dostęp do Twojego konta. Jeśli zdecydujesz się zainstalować certyfikat, kliknij prawym przyciskiem myszy węzeł **platformy Azure** w **Eksplorator serwera**, a następnie wybierz pozycję **Zarządzaj i Filtruj subskrypcje** w menu kontekstowym. W oknie dialogowym **Zarządzanie subskrypcjami Microsoft Azure** kliknij kartę **Certyfikaty** , a następnie kliknij przycisk **Importuj**. Postępuj zgodnie z instrukcjami, aby pobrać, a następnie zaimportować plik subskrypcji (nazywany także plikiem *. publishsettings* ) dla konta platformy Azure.

   > [!NOTE]
   > Jeśli pobierasz plik subskrypcji, Zapisz go w folderze spoza katalogów kodu źródłowego (na przykład w folderze pliki do pobrania), a następnie Usuń po zakończeniu importowania. Złośliwy użytkownik, który uzyskuje dostęp do pliku subskrypcji, może edytować, tworzyć i usuwać usługi platformy Azure.
   >
   >

    Aby uzyskać więcej informacji na temat nawiązywania połączenia z zasobami platformy Azure z poziomu programu Visual Studio, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md).
2. W **Eksplorator serwera** rozwiń węzeł **Azure** i rozwiń pozycję **App Service**.
3. Rozwiń grupę zasobów zawierającą aplikację utworzoną w temacie [Tworzenie aplikacji ASP.NET w Azure App Service](quickstart-dotnet-framework.md), a następnie kliknij prawym przyciskiem myszy węzeł aplikacji, a następnie kliknij pozycję **Wyświetl ustawienia**.

    ![Wyświetl ustawienia w Eksplorator serwera](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Zostanie wyświetlona karta **aplikacja sieci Web platformy Azure** , w której można wyświetlić zadania zarządzania aplikacjami i konfiguracji, które są dostępne w programie Visual Studio.

    ![Okno aplikacji sieci Web platformy Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    W tym samouczku użyjesz listy rozwijanej rejestrowanie i śledzenie. Będziesz również używać debugowania zdalnego, ale użyjesz innej metody, aby ją włączyć.

    Aby uzyskać informacje na temat ustawień aplikacji i pól parametrów połączenia w tym oknie, zobacz [Azure App Service: jak działają ciągi aplikacji i parametry połączenia](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Jeśli chcesz wykonać zadanie zarządzania aplikacjami, które nie może zostać wykonane w tym oknie, kliknij przycisk **Otwórz w Portal zarządzania** , aby otworzyć okno przeglądarki do Azure Portal.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Dostęp do plików aplikacji w Eksplorator serwera
Zwykle projekt sieci Web jest wdrażany z `customErrors` flagą w pliku Web.config ustawionym na `On` lub `RemoteOnly` , co oznacza, że nie można uzyskać przydatnego komunikatu o błędzie, gdy coś się nie stało. W przypadku wielu błędów wszystkie te strony są takie same jak następujące:

**Błąd serwera w aplikacji "/":**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png" alt-text="Zrzut ekranu przedstawiający błąd serwera w aplikacji &quot;/&quot; w przeglądarce sieci Web.":::

**Wystąpił błąd:**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png" alt-text="Zrzut ekranu przedstawiający przykład błędu ogólnego występujący w przeglądarce sieci Web.":::

**Witryna sieci Web nie może wyświetlić strony**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png" alt-text="Zrzut ekranu przedstawiający witrynę internetową nie można wyświetlić błędu strony w przeglądarce sieci Web.":::

Często najprostszym sposobem znalezienia przyczyny błędu jest włączenie szczegółowych komunikatów o błędach, które pierwszy z powyższych zrzutów ekranu wyjaśnia, jak to zrobić. Wymaga zmiany we wdrożonym pliku Web.config. Można edytować *Web.config* plik w projekcie i ponownie wdrożyć projekt lub utworzyć [Web.config Przekształć](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) i wdrożyć kompilację debugowania, ale istnieje szybszy sposób: w **Eksplorator rozwiązań** można bezpośrednio wyświetlać i edytować pliki w aplikacji zdalnej przy użyciu funkcji *Widok zdalny* .

1. W **Eksplorator serwera** rozwiń węzeł **Azure**, rozwiń węzeł **App Service**, rozwiń grupę zasobów, w której znajduje się aplikacja, a następnie rozwiń węzeł aplikacji.

    Zobaczysz węzły, które zapewniają dostęp do plików zawartości i plików dziennika aplikacji.
2. Rozwiń węzeł **pliki** , a następnie kliknij dwukrotnie plik *Web.config* .

    ![Otwórz Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Program Visual Studio otwiera plik Web.config z aplikacji zdalnej i wyświetla [zdalny] obok nazwy pliku na pasku tytułu.
3. Dodaj następujący wiersz do `system.web` elementu:

    `<customErrors mode="Off"></customErrors>`

    ![Edytuj Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Odśwież przeglądarkę wyświetlającą niepomocowy komunikat o błędzie, a teraz zostanie wyświetlony szczegółowy komunikat o błędzie, taki jak Poniższy przykład:

    ![Szczegółowy komunikat o błędzie](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Wyświetlony błąd został utworzony przez dodanie wiersza pokazanego w kolorze czerwonym do *Views\Home\Index.cshtml*).

Edytowanie pliku Web.config to tylko jeden przykład scenariuszy, w których możliwość odczytywania i edytowania plików w aplikacji App Service ułatwia rozwiązywanie problemów.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Aplikacje zdalnego debugowania
Jeśli szczegółowy komunikat o błędzie nie zawiera wystarczającej ilości informacji i nie można ponownie utworzyć błędu, inny sposób rozwiązywania problemów jest wykonywany zdalnie w trybie debugowania. Można ustawić punkty przerwania, manipulować bezpośrednio pamięcią, krok po kroku, a nawet zmienić ścieżkę kodu.

Debugowanie zdalne nie działa w wersjach Express programu Visual Studio.

W tej sekcji przedstawiono sposób debugowania zdalnego przy użyciu projektu utworzonego w programie [Tworzenie aplikacji ASP.NET w Azure App Service](quickstart-dotnet-framework.md).

1. Otwórz projekt sieci Web, który został utworzony w temacie [Tworzenie aplikacji ASP.NET w Azure App Service](quickstart-dotnet-framework.md).

1. Otwórz *Controllers\HomeController.cs*.

1. Usuń `About()` metodę i Wstaw w jej miejscu następujący kod.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Ustaw punkt przerwania](/visualstudio/debugger/) w `ViewBag.Message` wierszu.

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie kliknij pozycję **Publikuj**.

1. Z listy rozwijanej **profil** wybierz ten sam profil, który został użyty podczas [tworzenia aplikacji ASP.NET w Azure App Service](quickstart-dotnet-framework.md). Następnie kliknij pozycję Ustawienia.

1. W oknie dialogowym **Publikowanie** kliknij kartę **Ustawienia** , a następnie zmień **konfigurację** na **Debuguj**, a następnie kliknij przycisk **Zapisz**.

    ![Publikuj w trybie debugowania](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Kliknij przycisk **Opublikuj**. Po zakończeniu wdrażania i otwarciu przeglądarki w adresie URL platformy Azure aplikacji Zamknij przeglądarkę.

1. W **Eksplorator serwera** kliknij prawym przyciskiem myszy aplikację, a następnie kliknij pozycję **Dołącz debuger**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png" alt-text="Zrzut ekranu przedstawiający okno Eksplorator serwera, w którym zaznaczono aplikację, a następnie klikając pozycję Dołącz debuger.":::

    Przeglądarka zostanie automatycznie otwarta na stronie głównej działającej na platformie Azure. Może być konieczne odczekanie 20 sekund lub tak, dopóki platforma Azure skonfiguruje serwer na potrzeby debugowania. To opóźnienie występuje tylko podczas pierwszego uruchomienia w trybie debugowania w aplikacji w ciągu 48 godzin. Po ponownym uruchomieniu debugowania w tym samym okresie nie ma opóźnienia.

    > [!NOTE] 
    > Jeśli masz problemy z uruchomieniem debugera, spróbuj wykonać go przy użyciu programu **Cloud Explorer** zamiast **Eksplorator serwera**.
    >

1. Kliknij pozycję **informacje** w menu.

    Program Visual Studio zostaje zatrzymany w punkcie przerwania, a kod jest uruchomiony na platformie Azure, a nie na komputerze lokalnym.

1. Umieść kursor nad `currentTime` zmienną, aby wyświetlić wartość czasu.

    ![Wyświetl zmienną w trybie debugowania działającym na platformie Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    Wyświetlany czas to czas serwera platformy Azure, który może znajdować się w innej strefie czasowej niż komputer lokalny.

1. Wprowadź nową wartość dla `currentTime` zmiennej, na przykład "teraz uruchomiona na platformie Azure".

1. Naciśnij klawisz F5, aby kontynuować działanie.

     Na stronie informacje działającej na platformie Azure jest wyświetlana nowa wartość wprowadzona w zmiennej currentTime.

     ![Informacje o stronie z nową wartością](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a> Zdalne debugowanie zadań WebJob
W tej sekcji pokazano, jak debugować zdalnie przy użyciu utworzonego projektu i aplikacji w artykule [Rozpoczynanie pracy z zestawem SDK Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funkcje przedstawione w tej sekcji są dostępne tylko w Visual Studio 2013 z aktualizacją Update 4 lub nowszą.

Zdalne debugowanie działa tylko z ciągłymi zadaniami WebJob. Zaplanowane i na żądanie zadania WebJob nie obsługują debugowania.

1. Otwórz projekt sieci Web, który został utworzony w temacie [Rozpoczynanie pracy z zestawem SDK Azure WebJobs][GetStartedWJ].

2. W projekcie ContosoAdsWebJob Otwórz *Functions.cs*.

3. [Ustaw punkt przerwania](/visualstudio/debugger/) na pierwszej instrukcji w `GnerateThumbnail` metodzie.

    ![Ustaw punkt przerwania](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt sieci Web (a nie projekt Zadania WebJob), a następnie kliknij pozycję **Publikuj**.

5. Z listy rozwijanej **profil** wybierz ten sam profil, który został użyty w temacie Rozpoczynanie [pracy z zestawem SDK Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Kliknij kartę **Ustawienia** i Zmień **konfigurację** na **Debuguj**, a następnie kliknij przycisk **Publikuj**.

    Program Visual Studio wdraża projekty sieci Web i Zadania WebJob, a przeglądarka otwiera adres URL platformy Azure aplikacji.

7. W **Eksplorator serwera** rozwiń węzeł **Azure > App Service > grupę zasobów > aplikację > WebJobs > Continuous**, a następnie kliknij prawym przyciskiem myszy pozycję **ContosoAdsWebJob**.

8. Kliknij pozycję **Dołącz debuger**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png" alt-text="Zrzut ekranu przedstawiający Eksplorator serwera pokazywanie ContosoAdsWebJob wybranych w menu rozwijanym i zaznaczono debuger.":::

    Przeglądarka zostanie automatycznie otwarta na stronie głównej działającej na platformie Azure. Może być konieczne odczekanie 20 sekund lub tak, dopóki platforma Azure skonfiguruje serwer na potrzeby debugowania. To opóźnienie występuje tylko podczas pierwszego uruchomienia w trybie debugowania w aplikacji w ciągu 48 godzin. Po ponownym uruchomieniu debugowania w tym samym okresie nie ma opóźnienia.

9. W przeglądarce sieci Web otwartej na stronie głównej contoso ads Utwórz nową reklamę.

    Utworzenie usługi AD powoduje utworzenie komunikatu w kolejce, który jest wybierany przez zadanie WebJob i przetworzone. Gdy zestaw SDK zadań WebJob wywołuje funkcję w celu przetworzenia komunikatu kolejki, kod trafi punkt przerwania.

10. Gdy debuger przerwie się w punkcie przerwania, można testować i zmieniać wartości zmiennych, gdy program korzysta z chmury. Na poniższej ilustracji debuger pokazuje zawartość obiektu blobInfo, który został przekazana do `GenerateThumbnail` metody.

     ![Obiekt blobInfo w debugerze](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Naciśnij klawisz F5, aby kontynuować działanie.

     `GenerateThumbnail`Metoda kończy tworzenie miniatury.

12. W przeglądarce Odśwież stronę indeksu i zobaczysz miniaturę.

13. W programie Visual Studio naciśnij klawisze SHIFT + F5, aby zatrzymać debugowanie.

14. W **Eksplorator serwera** kliknij prawym przyciskiem myszy węzeł ContosoAdsWebJob, a następnie kliknij pozycję **Wyświetl pulpit nawigacyjny**.

15. Zaloguj się przy użyciu poświadczeń platformy Azure, a następnie kliknij nazwę zadania WebJob, aby przejść do strony zadania WebJob.

     ![Kliknij pozycję ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Pulpit nawigacyjny pokazuje, że `GenerateThumbnail` funkcja została ostatnio wykonana.

     (Gdy następnym razem klikniesz pozycję **Wyświetl pulpit nawigacyjny**, nie musisz logować się, a przeglądarka przechodzi bezpośrednio do strony zadania WebJob).

16. Kliknij nazwę funkcji, aby wyświetlić szczegółowe informacje o wykonywaniu funkcji.

     ![Szczegóły funkcji](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Jeśli funkcja [zapisała dzienniki](https://github.com/Azure/azure-webjobs-sdk/wiki), można kliknąć pozycję **ToggleOutput** , aby je zobaczyć.

## <a name="notes-about-remote-debugging"></a>Notes about remote debugging (Uwagi dotyczące zdalnego debugowania)

* Uruchamianie w trybie debugowania w środowisku produkcyjnym nie jest zalecane. Jeśli aplikacja produkcyjna nie jest skalowana w poziomie wielu wystąpień serwera, debugowanie uniemożliwia serwerowi sieci Web odpowiadanie na inne żądania. Jeśli masz wiele wystąpień serwera sieci Web, po dołączeniu do debugera otrzymujesz losowe wystąpienie i nie masz możliwości zapewnienia, że kolejne żądania przeglądarki przechodzą do tego samego wystąpienia. Ponadto zazwyczaj nie są wdrażane kompilacje debugowania w środowisku produkcyjnym, a optymalizacja kompilatora dla kompilacji wydań może uniemożliwić wyświetlenie tego, co dzieje się w wierszu w kodzie źródłowym. W przypadku rozwiązywania problemów produkcyjnych najlepszym zasobem jest śledzenie aplikacji i Dzienniki serwera sieci Web.
* Unikaj długotrwałych zatrzymań w punktach przerwania podczas debugowania zdalnego. System Azure traktuje proces, który jest zatrzymany przez dłużej niż kilka minut, jako proces nieodpowiadający i zamyka go.
* Podczas debugowania serwer wysyła dane do programu Visual Studio, co może mieć wpływ na opłaty za przepustowość. Aby uzyskać informacje o stawkach przepustowości, zobacz [Cennik platformy Azure](https://azure.microsoft.com/pricing/calculator/).
* Upewnij się, że `debug` atrybut `compilation` elementu w pliku *Web.config* ma wartość true. Domyślnie jest ustawiona wartość true w przypadku publikowania konfiguracji kompilacji debugowania.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Jeśli okaże się, że debuger nie przekroczy kodu, który ma być debugowany, może zajść potrzeba zmiany ustawienia Tylko mój kod.  Aby uzyskać więcej informacji, zobacz [Określanie, czy debugować tylko kod użytkownika przy użyciu tylko mój kod w programie Visual Studio](/visualstudio/debugger/just-my-code).
* Czasomierz jest uruchamiany na serwerze po włączeniu funkcji zdalnego debugowania, a po 48 godzinach funkcja jest automatycznie wyłączona. Ten limit 48 godzin jest realizowany ze względu na bezpieczeństwo i wydajność. Możesz łatwo włączyć tę funkcję na tyle razy, ile chcesz. Zalecamy pozostawienie jej wyłączonej, jeśli nie będziesz aktywnie debugować.
* Debuger można dołączyć ręcznie do dowolnego procesu, a nie tylko procesu aplikacji (w3wp.exe). Aby uzyskać więcej informacji na temat używania trybu debugowania w programie Visual Studio, zobacz [debugowanie w programie Visual Studio](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Omówienie dzienników diagnostycznych
Aplikacja ASP.NET działająca w aplikacji App Service może tworzyć następujące rodzaje dzienników:

* **Dzienniki śledzenia aplikacji**<br/>
  Aplikacja tworzy te dzienniki przez wywołanie metod klasy [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) .
* **Dzienniki serwera sieci Web**<br/>
  Serwer sieci Web tworzy wpis dziennika dla każdego żądania HTTP do aplikacji.
* **Szczegółowe dzienniki komunikatów o błędach**<br/>
  Serwer sieci Web tworzy stronę HTML z dodatkowymi informacjami dotyczącymi nieudanych żądań HTTP (żądania w wyniku kodu stanu 400 lub nowszego).
* **Dzienniki śledzenia niepomyślnych żądań**<br/>
  Serwer sieci Web tworzy plik XML ze szczegółowymi informacjami śledzenia dla nieudanych żądań HTTP. Serwer sieci Web udostępnia również plik XSL do formatowania kodu XML w przeglądarce.

Rejestrowanie ma wpływ na wydajność aplikacji, więc platforma Azure zapewnia możliwość włączania lub wyłączania każdego typu dziennika zgodnie z wymaganiami. W przypadku dzienników aplikacji można określić, że mają być zapisane tylko dzienniki powyżej określonego poziomu ważności. Podczas tworzenia nowej aplikacji domyślnie wszystkie rejestrowania są wyłączone.

Dzienniki są zapisywane w plikach w folderze *LogFiles* w systemie plików aplikacji i są dostępne za pośrednictwem protokołu FTP. Dzienniki serwera sieci Web i dzienniki aplikacji można również zapisać na koncie usługi Azure Storage. Większą ilość dzienników można zachować na koncie magazynu, niż jest to możliwe w systemie plików. W przypadku korzystania z systemu plików ograniczenie do maksymalnie 100 megabajtów dzienników jest ograniczone. (Dzienniki systemu plików są przeznaczone tylko do krótkoterminowego przechowywania. Platforma Azure usuwa stare pliki dziennika, aby zwolnić miejsce na nowe po osiągnięciu limitu.  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Tworzenie i wyświetlanie dzienników śledzenia aplikacji
W tej sekcji wykonasz następujące zadania:

* Dodaj do projektu sieci Web instrukcje śledzenia, które zostały utworzone w temacie Rozpoczynanie [pracy z platformą Azure i ASP.NET](quickstart-dotnet-framework.md).
* Wyświetlanie dzienników podczas lokalnego uruchamiania projektu.
* Wyświetlanie dzienników w miarę ich generowania przez aplikację działającą na platformie Azure.

Aby uzyskać informacje o sposobach tworzenia dzienników aplikacji w zadaniach WebJob, zobacz [jak korzystać z usługi Azure queue storage przy użyciu zestawu SDK WebJobs — jak pisać dzienniki](https://github.com/Azure/azure-webjobs-sdk/wiki). Poniższe instrukcje dotyczące wyświetlania dzienników i kontrolowania sposobu ich przechowywania na platformie Azure mają zastosowanie również do dzienników aplikacji utworzonych przez Zadania WebJob.

### <a name="add-tracing-statements-to-the-application"></a>Dodawanie instrukcji śledzenia do aplikacji
1. Otwórz *Controllers\HomeController.cs* i Zastąp `Index` metody, `About` , i `Contact` następującym kodem w celu dodania `Trace` instrukcji i `using` instrukcji dla `System.Diagnostics` :

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. Dodaj `using System.Diagnostics;` instrukcję do górnej części pliku.

### <a name="view-the-tracing-output-locally"></a>Wyświetlanie danych wyjściowych śledzenia lokalnie
1. Naciśnij klawisz F5, aby uruchomić aplikację w trybie debugowania.

    Domyślny odbiornik śledzenia zapisuje wszystkie dane wyjściowe śledzenia do okna **dane wyjściowe** wraz z innymi danymi wyjściowymi debugowania. Na poniższej ilustracji przedstawiono dane wyjściowe instrukcji Trace, które zostały dodane do `Index` metody.

    ![Śledzenie w oknie debugowania](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Poniższe kroki pokazują, jak wyświetlać dane wyjściowe śledzenia na stronie sieci Web bez kompilowania w trybie debugowania.
1. Otwórz plik Web.config aplikacji (znajdujący się w folderze projektu) i Dodaj `<system.diagnostics>` element na końcu pliku tuż przed elementem zamykającym `</configuration>` :

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

`WebPageTraceListener`Umożliwia wyświetlanie danych wyjściowych śledzenia przez przechodzenie do `/trace.axd` .
1. Dodaj <a href="/previous-versions/dotnet/netframework-4.0/6915t83k(v=vs.100)">element Trace</a> w obszarze `<system.web>` w pliku Web.config, na przykład następujący przykład:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Naciśnij klawisze CTRL+F5, aby uruchomić aplikację.
1. Na pasku adresu okna przeglądarki Dodaj wartość *Trace. axd* do adresu URL, a następnie naciśnij klawisz ENTER (adres URL jest podobny do `http://localhost:53370/trace.axd` ).
1. Na stronie **ślad aplikacji** kliknij pozycję **Wyświetl szczegóły** w pierwszym wierszu (nie w wierszu BrowserLink).

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png" alt-text="Zrzut ekranu strony śledzenia aplikacji w przeglądarce internetowej pokazujący szczegóły widoku wybrane w pierwszym wierszu.":::

    Zostanie wyświetlona strona **szczegóły żądania** , a w sekcji **Informacje o śledzeniu** są wyświetlane dane wyjściowe instrukcji Trace dodanych do `Index` metody.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png" alt-text="Zrzut ekranu strony szczegóły żądania w przeglądarce sieci Web, w której wyświetlany jest komunikat wyróżniony w sekcji Informacje o śledzeniu.":::

    Domyślnie program `trace.axd` jest dostępny tylko lokalnie. Jeśli chcesz udostępnić ją z poziomu aplikacji zdalnej, możesz dodać `localOnly="false"` do `trace` elementu w pliku *Web.config* , jak pokazano w następującym przykładzie:

    ```xml
    <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />
    ```

    `trace.axd`Nie zaleca się jednak włączania w aplikacji produkcyjnej ze względów bezpieczeństwa. W poniższych sekcjach zobaczysz łatwiejszy sposób odczytywania dzienników śledzenia w aplikacji App Service.

### <a name="view-the-tracing-output-in-azure"></a>Wyświetlanie danych wyjściowych śledzenia na platformie Azure
1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt sieci Web i kliknij pozycję **Publikuj**.
2. W oknie dialogowym **Publikowanie sieci Web** kliknij pozycję **Opublikuj**.

    Po opublikowaniu aktualizacji przez program Visual Studio otwiera okno przeglądarki na stronie głównej (przy założeniu, że **docelowy adres URL** nie został wyczyszczony na karcie **połączenie** ).
3. W **Eksplorator serwera** kliknij prawym przyciskiem myszy aplikację i wybierz polecenie **Wyświetl dzienniki przesyłania strumieniowego**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png" alt-text="Zrzut ekranu przedstawiający Eksplorator serwera po kliknięciu prawym przyciskiem myszy aplikacji, z wyświetleniem dzienników przesyłania strumieniowego wybranych w nowym oknie.":::

    Okno **dane wyjściowe** pokazuje, że nawiązano połączenie z usługą przesyłania strumieniowego dziennika i dodaje wiersz powiadomienia każdej minuty, która przechodzi bez dziennika do wyświetlenia.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png" alt-text="Zrzut ekranu przedstawiający okno dane wyjściowe z przykładem połączenia z usługą przesyłania strumieniowego dzienników z wierszami powiadomień.":::

4. W oknie przeglądarki, w którym wyświetlana jest Strona główna aplikacji, kliknij pozycję **kontakt**.

    W ciągu kilku sekund dane wyjściowe z śladu poziomu błędu dodanego do `Contact` metody pojawią się w oknie **danych wyjściowych** .

    ![Śledzenie błędów w oknie danych wyjściowych](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Program Visual Studio wyświetla tylko ślady poziomu błędu, ponieważ jest to ustawienie domyślne po włączeniu usługi monitorowania dzienników. Podczas tworzenia nowej aplikacji App Service wszystkie rejestrowania są domyślnie wyłączone, ponieważ zostały one wcześniej otwarte na stronie Ustawienia:

    ![Wylogowywanie aplikacji](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Jednak po wybraniu **widoku Wyświetlanie dzienników przesyłania strumieniowego** program Visual Studio automatycznie zmienił **Rejestrowanie aplikacji (system plików)** do **błędu**, co oznacza, że raportowane są dzienniki na poziomie błędów. Aby wyświetlić wszystkie dzienniki śledzenia, można zmienić to ustawienie na **pełne**. Po wybraniu poziomu ważności niższej niż błąd, zgłaszane są również wszystkie dzienniki o wyższym poziomie ważności. Dlatego po wybraniu opcji pełne widoczne są również informacje, ostrzeżenie i dzienniki błędów.  

5. W **Eksplorator serwera** kliknij prawym przyciskiem myszy aplikację, a następnie kliknij pozycję **Wyświetl ustawienia** jak wcześniej.
6. Zmień **Rejestrowanie aplikacji (system plików)** na **pełne**, a następnie kliknij przycisk **Zapisz**.

    ![Ustawianie poziomu śledzenia na pełne](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. W oknie przeglądarki, które wyświetla się teraz na stronie **kontaktowej** , kliknij pozycję **Strona główna**, a następnie kliknij pozycję **informacje**, a następnie kliknij pozycję **kontakt**.

    W ciągu kilku sekund okno **dane wyjściowe** zawiera wszystkie dane wyjściowe śledzenia.

    ![Pełne dane wyjściowe śledzenia](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    W tej sekcji można włączyć i wyłączyć rejestrowanie przy użyciu ustawień aplikacji. Możesz również włączyć i wyłączyć detektory śledzenia, modyfikując plik Web.config. Jednak zmodyfikowanie pliku Web.config powoduje odtworzenie domeny aplikacji, podczas gdy włączenie rejestrowania za pomocą konfiguracji aplikacji nie wykonuje tego. Jeśli ten problem zajmuje dużo czasu na odtworzenie lub nieprzerwane, odtwarzanie domeny aplikacji może "naprawić" i wymusić odczekanie do momentu ponownego uruchomienia. Włączenie diagnostyki na platformie Azure umożliwia natychmiastowe rozpoczęcie przechwytywania informacji o błędach bez konieczności odtwarzania domeny aplikacji.

### <a name="output-window-features"></a>Funkcje okna danych wyjściowych
Karta **dzienniki Microsoft Azure** w oknie **danych wyjściowych** zawiera kilka przycisków i pole tekstowe:

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png" alt-text="Zrzut ekranu przedstawiający przyciski i pole tekstowe karty Dzienniki Microsoft Azure w oknie danych wyjściowych.":::

Są to następujące funkcje:

* Wyczyść okno **dane wyjściowe** .
* Włącz lub Wyłącz Zawijanie wierszy.
* Uruchamianie lub zatrzymywanie monitorowania dzienników.
* Określ, które dzienniki mają być monitorowane.
* Pobierz dzienniki.
* Filtrowanie dzienników na podstawie ciągu wyszukiwania lub wyrażenia regularnego.
* Zamknij okno **dane wyjściowe** .

Jeśli wprowadzisz ciąg wyszukiwania lub wyrażenie regularne, program Visual Studio filtruje informacje rejestrowania na kliencie. Oznacza to, że można wprowadzić kryteria po wyświetleniu dzienników w oknie **dane wyjściowe** i można zmienić kryteria filtrowania bez konieczności ponownego generowania dzienników.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Wyświetlanie dzienników serwera sieci Web
Dzienniki serwera sieci Web rejestruje wszystkie działania HTTP dla aplikacji. Aby wyświetlić je w oknie **danych wyjściowych** , należy je włączyć dla aplikacji i powiedzieć programowi Visual Studio, który ma być monitorowany.

1. Na karcie **Konfiguracja aplikacji sieci Web platformy Azure** , która została otwarta z **Eksplorator serwera** Zmień rejestrowanie serwera sieci Web **na wartość włączone**, a następnie kliknij przycisk **Zapisz**.

    ![Włącz rejestrowanie serwera sieci Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. W oknie **dane wyjściowe** kliknij przycisk **Określ, które dzienniki Microsoft Azure mają być monitorowane** .

    ![Określ, które dzienniki platformy Azure mają być monitorowane](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. W oknie dialogowym **Microsoft Azure opcje rejestrowania** wybierz pozycję **Dzienniki serwera sieci Web**, a następnie kliknij przycisk **OK**.

    ![Monitorowanie dzienników serwera sieci Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. W oknie przeglądarki, w którym jest wyświetlana aplikacja, kliknij pozycję **Strona główna**, a następnie kliknij pozycję **informacje**, a następnie kliknij pozycję **kontakt**.

    Dzienniki aplikacji są zwykle wyświetlane jako pierwsze, a następnie Dzienniki serwera sieci Web. Aby dzienniki były widoczne, może być konieczne poczekanie.

    ![Dzienniki serwera sieci Web w oknie danych wyjściowych](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Domyślnie po pierwszym włączeniu dzienników serwera sieci Web przy użyciu programu Visual Studio system Azure zapisuje dzienniki w systemie plików. Alternatywnie można użyć Azure Portal, aby określić, że Dzienniki serwera sieci Web powinny być zapisywane do kontenera obiektów BLOB na koncie magazynu.

Jeśli używasz portalu, aby włączyć rejestrowanie serwera sieci Web na koncie usługi Azure Storage, a następnie Wyłącz rejestrowanie w programie Visual Studio, po ponownym włączeniu rejestrowania w programie Visual Studio Ustawienia konta magazynu zostaną przywrócone.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Wyświetlanie szczegółowych dzienników komunikatów o błędach
Szczegółowe dzienniki błędów zawierają dodatkowe informacje o żądaniach HTTP, które powodują powstanie kodów odpowiedzi na błędy (400 lub więcej). Aby wyświetlić je w oknie **danych wyjściowych** , należy je włączyć dla aplikacji i powiedzieć programowi Visual Studio, który ma być monitorowany.

1. Na karcie **Konfiguracja aplikacji sieci Web platformy Azure** , która została otwarta w **Eksplorator serwera**, Zmień **szczegółowe komunikaty o błędach** **na wartość włączone**, a następnie kliknij przycisk **Zapisz**.

    ![Włącz szczegółowe komunikaty o błędach](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. W oknie **dane wyjściowe** kliknij przycisk **Określ, które dzienniki Microsoft Azure mają być monitorowane** .

3. W oknie dialogowym **Microsoft Azure opcje rejestrowania** kliknij pozycję **wszystkie dzienniki**, a następnie kliknij przycisk **OK**.

    ![Monitoruj wszystkie dzienniki](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Na pasku adresu okna przeglądarki Dodaj znak dodatkowy do adresu URL, aby spowodować błąd 404 (na przykład `http://localhost:53370/Home/Contactx` ), a następnie naciśnij klawisz ENTER.

    Po kilku sekundach szczegółowy dziennik błędów pojawi się w oknie **danych wyjściowych** programu Visual Studio.

    ![Szczegółowy dziennik błędów — okno danych wyjściowych](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Kontrolka i kliknięcie linku, aby wyświetlić dane wyjściowe dziennika sformatowane w przeglądarce:

    ![Szczegółowy dziennik błędów — okno przeglądarki](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Pobierz Dzienniki systemu plików
Wszystkie dzienniki, które można monitorować w oknie **danych wyjściowych** , można również pobrać jako plik *. zip* .

1. W oknie **dane wyjściowe** kliknij pozycję **Pobierz dzienniki przesyłania strumieniowego**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png" alt-text="Zrzut ekranu przedstawiający okno dane wyjściowe z wyróżnionym przyciskiem Pobierz dzienniki przesyłania strumieniowego.":::

    Eksplorator plików otwiera folder *pliki do pobrania* z wybranym pobranym plikiem.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png" alt-text="Zrzut ekranu przedstawiający folder pliki do pobrania w Eksploratorze plików z wybranym pobranym plikiem.":::

2. Wyodrębnij plik *zip* i Wyświetl następującą strukturę folderów:

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png" alt-text="Zrzut ekranu przedstawiający strukturę folderu pliku zip po wyodrębnieniu pliku.":::

   * Dzienniki śledzenia aplikacji znajdują się w plikach *txt* w folderze *LogFiles\Application* .
   * Dzienniki serwera sieci Web znajdują się w plikach *. log* w folderze *LogFiles\http\RawLogs* Do wyświetlania tych plików i manipulowania nimi można użyć narzędzia, takiego jak [parser dzienników](https://www.iis.net/downloads/community/2010/04/log-parser-22) .
   * Szczegółowe dzienniki komunikatów o błędach znajdują się w plikach *. html* w folderze *LogFiles\DetailedErrors* .

     (Folder *Deployments* jest przeznaczony dla plików utworzonych przez publikowanie kontroli źródła. nie ma niczego związanego z publikowaniem w programie Visual Studio. Folder *git* jest przeznaczony dla śladów związanych z publikowaniem kontroli źródła i usługą przesyłania strumieniowego plików dziennika.  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-account-create.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Wyświetlanie dzienników śledzenia nieudanych żądań
Dzienniki śledzenia niepomyślnych żądań są przydatne, gdy trzeba zrozumieć szczegóły dotyczące sposobu obsługi przez usługi IIS żądania HTTP w scenariuszach takich jak zapisywanie adresów URL lub problemy z uwierzytelnianiem.

Aplikacje App Service używają tych samych funkcji śledzenia niepomyślnych żądań, które były dostępne w usługach IIS 7,0 i nowszych. Nie masz dostępu do ustawień usług IIS, które konfigurują, które błędy są rejestrowane. Po włączeniu śledzenia nieudanych żądań wszystkie błędy są przechwytywane.

Śledzenie nieudanych żądań można włączyć przy użyciu programu Visual Studio, ale nie można ich przeglądać w programie Visual Studio. Te dzienniki są plikami XML. Usługa dziennika przesyłania strumieniowego monitoruje tylko te pliki, które są uznawane za czytelne w trybie zwykłego tekstu:  *txt*, *HTML* i *. log* .

Dzienniki śledzenia niepomyślnych żądań można wyświetlić w przeglądarce bezpośrednio za pośrednictwem protokołu FTP lub lokalnego po użyciu narzędzia FTP, aby pobrać je na komputer lokalny. W tej sekcji przedstawiono bezpośrednie wyświetlanie ich w przeglądarce.

1. Na karcie **Konfiguracja** okna **aplikacji sieci Web platformy Azure** , które zostało otwarte z **Eksplorator serwera**, Zmień **śledzenie nieudanych żądań** na **włączone**, a następnie kliknij przycisk **Zapisz**.

    ![Włącz śledzenie nieudanych żądań](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Na pasku adresu okna przeglądarki, w którym znajduje się aplikacja, Dodaj znak dodatkowy do adresu URL, a następnie kliknij przycisk ENTER, aby spowodować błąd 404.

    Powoduje to utworzenie dziennika śledzenia niepomyślnych żądań, a w poniższych krokach pokazano, jak wyświetlić lub pobrać dziennik.

3. W programie Visual Studio na karcie **Konfiguracja** okna **aplikacji sieci Web platformy Azure** kliknij pozycję **Otwórz w Portal zarządzania**.

4. Na stronie [](https://portal.azure.com) **Ustawienia** Azure Portal aplikacji kliknij pozycję **poświadczenia wdrażania**, a następnie wprowadź nową nazwę użytkownika i hasło.

    ![Nowa nazwa użytkownika i hasło FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Podczas logowania należy użyć pełnej nazwy użytkownika z prefiksową nazwą aplikacji. Na przykład, jeśli wprowadzisz "myid" jako nazwę użytkownika, a witryna to "przykład", zalogujesz się jako "myexample\myid".
    >

5. W nowym oknie przeglądarki przejdź do adresu URL, który jest wyświetlany w obszarze **Nazwa hosta FTP** lub **Nazwa hosta FTPS** na stronie **Przegląd** dla swojej aplikacji.

6. Zaloguj się przy użyciu poświadczeń FTP utworzonych wcześniej (w tym prefiks nazwy aplikacji dla nazwy użytkownika).

    Przeglądarka pokazuje folder główny aplikacji.

7. Otwórz folder *LogFiles* .

    ![Otwórz folder LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Otwórz folder o nazwie W3SVC i wartości liczbowej.

    ![Otwórz folder W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Folder zawiera pliki XML dla wszystkich błędów, które zostały zarejestrowane po włączeniu śledzenia niepomyślnych żądań, oraz plik XSL, który może być używany przez przeglądarkę do formatowania kodu XML.

    ![Folder W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Kliknij plik XML dla żądania zakończonego niepowodzeniem, dla którego chcesz zobaczyć informacje o śledzeniu.

    Na poniższej ilustracji przedstawiono część informacji śledzenia dla przykładowego błędu.

    ![Śledzenie nieudanych żądań w przeglądarce](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>Następne kroki
Widzisz, jak program Visual Studio ułatwia Wyświetlanie dzienników utworzonych przez aplikację App Service. W poniższych sekcjach znajdują się linki do dodatkowych zasobów dotyczących pokrewnych tematów:

* Rozwiązywanie problemów App Service
* Debugowanie w Visual Studio
* Zdalne debugowanie na platformie Azure
* Śledzenie w aplikacjach ASP.NET
* Analizowanie dzienników serwera sieci Web
* Analizowanie dzienników śledzenia nieudanych żądań
* Debugowanie Cloud Services

### <a name="app-service-troubleshooting"></a>Rozwiązywanie problemów App Service
Aby uzyskać więcej informacji na temat rozwiązywania problemów z aplikacjami w Azure App Service, zobacz następujące zasoby:

* [Jak monitorować aplikacje](web-sites-monitor.md)
* [Badanie przecieków pamięci w Azure App Service z Visual Studio 2013](https://devblogs.microsoft.com/devops/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013/). Wpis w blogu firmy Microsoft ALM na temat funkcji programu Visual Studio na potrzeby analizowania problemów z pamięcią zarządzaną.
* [Azure App Service narzędzia online, których należy wiedzieć](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Wpis w blogu według Amit firmy Apple.

Aby uzyskać pomoc dotyczącą określonego pytania dotyczącego rozwiązywania problemów, uruchom wątek na jednym z następujących forów:

* [Forum platformy Azure w witrynie ASP.NET](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Forum platformy Azure na platformie Microsoft Q&A](/answers/topics/azure-webapps.html).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Debugowanie w Visual Studio
Aby uzyskać więcej informacji na temat korzystania z trybu debugowania w programie Visual Studio, zobacz [debugowanie w programie Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) i [porady dotyczące debugowania w programie Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Zdalne debugowanie na platformie Azure
Aby uzyskać więcej informacji na temat debugowania zdalnego dla aplikacji App Service i zadań WebJob, zobacz następujące zasoby:

* [Wprowadzenie do zdalnego debugowania Azure App Service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Wprowadzenie do debugowania zdalnego Azure App Service część 2 — wewnątrz debugowania zdalnego](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Wprowadzenie do zdalnego debugowania w Azure App Service część 3 — środowisko o wiele wystąpień i GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Debugowanie zadań WebJob (wideo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Jeśli aplikacja korzysta z internetowego interfejsu API platformy Azure lub Mobile Services zaplecza i musisz ją debugować, zobacz [debugowanie zaplecza platformy .NET w programie Visual Studio](/archive/blogs/azuremobile/debugging-net-backend-in-visual-studio).

### <a name="tracing-in-aspnet-applications"></a>Śledzenie w aplikacjach ASP.NET
Nie istnieją szczegółowe i aktualne wprowadzenie do śledzenia ASP.NET dostępnego w Internecie. Najlepszym rozwiązaniem jest rozpoczęcie pracy ze starymi materiałami wstępnymi napisanymi dla formularzy sieci Web, ponieważ MVC jeszcze nie istniały i uzupełnienie tego programu o nowsze wpisy w blogu, które koncentrują się na określonych problemach. Oto kilka dobrych miejsc do uruchomienia są następujące zasoby:

* [Monitorowanie i telemetria (kompilowanie Real-World aplikacji w chmurze przy użyciu platformy Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Rozdział książki elektronicznej z zaleceniami dotyczącymi śledzenia w aplikacjach w chmurze platformy Azure.
* [Śledzenie ASP.NET](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Stary, ale wciąż dobry zasób dla podstawowego wprowadzenia do tematu.
* [Obiekty nasłuchujące śledzenia](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Informacje na temat detektorów śledzenia, ale nie wspominają [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Przewodnik: Integrowanie śledzenia ASP.NET z systemem. śledzenie diagnostyki](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Ten artykuł jest również stary, ale zawiera dodatkowe informacje, które nie obejmują artykułu wprowadzającego.
* [Śledzenie w widokach Razor ASP.NET MVC](https://devblogs.microsoft.com/aspnet/tracing-in-asp-net-mvc-razor-views/)<br/>
  Oprócz śledzenia w widokach Razor, wpis zawiera również opis sposobu tworzenia filtru błędów w celu rejestrowania wszystkich nieobsłużonych wyjątków w aplikacji MVC. Aby uzyskać informacje o tym, jak rejestrować wszystkie Nieobsłużone wyjątki w aplikacji formularzy sieci Web, zapoznaj się z przykładem Global. asax w [kompletnym przykładzie dla programów obsługi błędów](/previous-versions/bb397417(v=vs.140)) w witrynie MSDN. Jeśli chcesz rejestrować pewne wyjątki w programie MVC lub Web Forms, ale pozwól, aby domyślna obsługa platformy zaczęła obowiązywać, możesz przechwycić i ponownie zgłosić jak w poniższym przykładzie:

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Rejestrowanie śledzenia diagnostyki przesyłania strumieniowego z wiersza polecenia platformy Azure (plus możliwość wypróbowania innowacyjnego!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Jak korzystać z wiersza polecenia w celu wykonania tego samouczka pokazuje, jak to zrobić w programie Visual Studio. [Możliwość wypróbowania innowacyjnego](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) to narzędzie do debugowania aplikacji ASP.NET.
* [Korzystanie z Web Apps rejestrowania i diagnostyki — z David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) i [dziennikami przesyłania strumieniowego z Web Apps — z David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Filmy wideo według Scott Hanselman i David Ebbo.

W przypadku rejestrowania błędów alternatywą dla pisania własnego kodu śledzenia jest użycie platformy rejestrowania typu open source, takiej jak [ELMAH](https://nuget.org/packages/elmah/). Aby uzyskać więcej informacji, zobacz [wpisy w blogu Scotta Hanselman dotyczące programu ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Ponadto nie trzeba używać ASP.NET ani `System.Diagnostics` śledzenia do pobierania dzienników przesyłania strumieniowego z platformy Azure. Usługa dziennika przesyłania strumieniowego aplikacji App Service strumieniuje dowolny plik *txt*, *HTML* lub *. log* , który znajduje się w folderze *LogFiles* . W związku z tym można utworzyć własny system rejestrowania, który zapisuje dane w systemie plików aplikacji, a plik jest automatycznie przesyłany strumieniowo i pobierany. Wystarczy napisać kod aplikacji, który tworzy pliki w folderze *d:\home\logfiles* .

### <a name="analyzing-web-server-logs"></a>Analizowanie dzienników serwera sieci Web
Aby uzyskać więcej informacji na temat analizowania dzienników serwera sieci Web, zobacz następujące zasoby:

* [LogParser](https://www.iis.net/downloads/community/2010/04/log-parser-22)<br/>
  Narzędzie do wyświetlania danych w dziennikach serwera sieci Web (pliki *. log* ).
* [Rozwiązywanie problemów z wydajnością lub błędami aplikacji usługi IIS za pomocą LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Wprowadzenie do narzędzia Analizator dzienników, którego można użyć do analizowania dzienników serwera sieci Web.
* [Wpisy w blogu przez Robert McMurraya na korzystanie z usługi LogParser](/archive/blogs/robert_mcmurray/using-logparser-with-ftp-7-x-sessions)<br/>
* [Kod stanu HTTP w usługach IIS 7,0, IIS 7,5 i IIS 8,0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analizowanie dzienników śledzenia nieudanych żądań
W witrynie sieci Web Microsoft TechNet znajduje [się sekcja śledzenie żądań zakończonych niepowodzeniem](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) , które mogą być pomocne w zrozumieniu sposobu korzystania z tych dzienników. Jednakże ta dokumentacja koncentruje się głównie na konfigurowaniu śledzenia nieudanych żądań w usługach IIS, których nie można wykonać w Azure App Service.

[GetStarted]: quickstart-dotnetcore.md?pivots=platform-windows
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki