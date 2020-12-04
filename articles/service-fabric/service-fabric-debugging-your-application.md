---
title: Debugowanie aplikacji w programie Visual Studio
description: Zwiększ niezawodność i wydajność usług, opracowując i debugując je w programie Visual Studio w lokalnym klastrze projektowym.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b7d08d610c883240abedc66c55abba64a74c8e3
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576319"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Debug your Service Fabric application by using Visual Studio (Debugowanie aplikacji usługi Service Fabric przy użyciu programu Visual Studio)
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Zaćmienie/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Debugowanie lokalnej aplikacji Service Fabric
Możesz zaoszczędzić czas i pieniądze, wdrażając i debugując aplikację Service Fabric platformy Azure w klastrze tworzenia komputerów lokalnych. Program Visual Studio 2019 lub 2015 może wdrożyć aplikację w klastrze lokalnym i automatycznie połączyć debuger ze wszystkimi wystąpieniami aplikacji. Program Visual Studio musi być uruchomiony jako administrator, aby podłączyć debuger.

1. Uruchom lokalny klaster programistyczny, wykonując czynności opisane w sekcji [Konfigurowanie środowiska deweloperskiego Service Fabric](service-fabric-get-started.md).
2. Naciśnij klawisz **F5** lub kliknij pozycję **Debuguj**  >  **Rozpocznij debugowanie**.
   
    ![Zrzut ekranu pokazujący menu Debuguj.][startdebugging]
3. Ustaw punkty przerwania w kodzie i przechodząc przez aplikację, klikając polecenie polecenia w menu **Debuguj** .
   
   > [!NOTE]
   > Program Visual Studio dołącza do wszystkich wystąpień aplikacji. Podczas wykonywania kroków wykonywanych przez kod punkty przerwania mogą trafiać przez wiele procesów, co prowadzi do równoczesnych sesji. Spróbuj wyłączyć punkty przerwania, gdy zostaną one trafione, przez nadanie każdemu punktowi przerwania na identyfikator wątku lub przy użyciu zdarzeń diagnostycznych.
   > 
   > 
4. Okno **zdarzenia diagnostyczne** zostanie automatycznie otwarte, aby można było wyświetlać zdarzenia diagnostyczne w czasie rzeczywistym.
   
    ![Wyświetlanie zdarzeń diagnostycznych w czasie rzeczywistym][diagnosticevents]
5. Możesz również otworzyć okno **zdarzeń diagnostycznych** w programie Cloud Explorer.  W obszarze **Service Fabric** kliknij prawym przyciskiem myszy dowolny węzeł i wybierz polecenie **Wyświetl ślady przesyłania strumieniowego**.
   
    ![Otwórz okno zdarzeń diagnostycznych][viewdiagnosticevents]
   
    Jeśli chcesz filtrować ślady do określonej usługi lub aplikacji, Włącz śledzenie przesyłania strumieniowego dla tej konkretnej usługi lub aplikacji.
6. Zdarzenia diagnostyczne mogą być widoczne w automatycznie wygenerowanym pliku **ServiceEventSource.cs** i są wywoływane z kodu aplikacji.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Okno **zdarzeń diagnostycznych** obsługuje filtrowanie, wstrzymywanie i sprawdzanie zdarzeń w czasie rzeczywistym.  Filtr jest prostym wyszukiwaniem ciągu komunikatu o zdarzeniu, w tym jego zawartości.
   
    ![Filtrowanie, wstrzymywanie i wznawianie oraz Inspekcja zdarzeń w czasie rzeczywistym][diagnosticeventsactions]
8. Usługi debugowania przypominają debugowanie dowolnej innej aplikacji. Zwykle ustawisz punkty przerwania za poorednictwem programu Visual Studio, aby ułatwić debugowanie. Mimo że niezawodne kolekcje są replikowane w wielu węzłach, nadal implementują interfejs IEnumerable. Ta implementacja oznacza, że możesz użyć widoku wyników w programie Visual Studio podczas debugowania, aby zobaczyć, co jest przechowywane w. Aby to zrobić, należy ustawić punkt przerwania w dowolnym miejscu w kodzie.
   
    ![Rozpocznij debugowanie aplikacji][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Uruchamianie skryptu w ramach debugowania
W niektórych scenariuszach może być konieczne uruchomienie skryptu w ramach uruchamiania sesji debugowania (np. gdy nie są używane domyślne usługi).

W programie Visual Studio można dodać plik o nazwie **Start-Service.ps1** w folderze **skrypty** projektu aplikacji Service Fabric (sfproj). Ten skrypt zostanie wywołany po utworzeniu aplikacji w klastrze lokalnym.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Debugowanie zdalnej aplikacji Service Fabric
Jeśli Service Fabric aplikacje są uruchomione w klastrze Service Fabric na platformie Azure, można zdalnie debugować te aplikacje bezpośrednio z programu Visual Studio.

> [!NOTE]
> Ta funkcja wymaga [Service Fabric zestawu sdk 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) i [zestawu Azure SDK dla programu .NET 2,9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> Debugowanie zdalne jest przeznaczone do scenariuszy deweloperskich i testowych i nie może być używane w środowiskach produkcyjnych, ponieważ ma to wpływ na uruchomione aplikacje.

1. Przejdź do klastra w programie **Cloud Explorer**. Kliknij prawym przyciskiem myszy i wybierz polecenie **Włącz debugowanie**
   
    ![Włącz debugowanie zdalne][enableremotedebugging]
   
    Ta akcja spowoduje rozpoczęcie procesu włączania rozszerzenia zdalnego debugowania w węzłach klastra i wymaganych konfiguracjach sieci.
2. Kliknij prawym przyciskiem myszy węzeł klastra w programie **Cloud Explorer** i wybierz polecenie **Dołącz debuger**
   
    ![Dołącz debuger][attachdebugger]
3. W oknie dialogowym **Dołącz do procesu** wybierz proces, który chcesz debugować, a następnie kliknij przycisk **Dołącz** .
   
    ![Wybierz proces][chooseprocess]
   
    Nazwa procesu, który chcesz dołączyć, jest równa nazwie zestawu projektu usługi.
   
    Debuger zostanie dołączony do wszystkich węzłów, na których jest uruchomiony proces.
   
   * W przypadku debugowania usługi bezstanowej wszystkie wystąpienia usługi we wszystkich węzłach są częścią sesji debugowania.
   * Jeśli debugujesz usługę stanową, tylko replika podstawowa dowolnej partycji będzie aktywna, więc zostanie przechwycona przez debuger. Jeśli replika podstawowa przenoszona podczas sesji debugowania, przetwarzanie tej repliki będzie nadal częścią sesji debugowania.
   * Aby przechwytywać tylko odpowiednie partycje lub wystąpienia danej usługi, można użyć punktów przerwania warunkowego, aby przerwać tylko określoną partycję lub wystąpienie.
     
     ![Warunkowy punkt przerwania][conditionalbreakpoint]
     
     > [!NOTE]
     > Obecnie nie obsługujemy debugowania klastra Service Fabric z wieloma wystąpieniami tej samej nazwy pliku wykonywalnego usługi.
     > 
     > 
4. Po zakończeniu debugowania aplikacji można wyłączyć rozszerzenie debugowania zdalnego, klikając prawym przyciskiem myszy klaster w programie **Cloud Explorer** i wybierając polecenie **Wyłącz debugowanie** .
   
    ![Wyłącz debugowanie zdalne][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Ślady przesyłania strumieniowego z węzła klastra zdalnego
Istnieje również możliwość strumieniowego przesyłania śladów bezpośrednio z węzła klastra zdalnego do programu Visual Studio. Ta funkcja umożliwia przesyłanie strumieniowe zdarzeń śledzenia funkcji ETW utworzonych w węźle klastra Service Fabric.

> [!NOTE]
> Ta funkcja wymaga [Service Fabric zestawu sdk 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) i [zestawu Azure SDK dla programu .NET 2,9](https://azure.microsoft.com/downloads/).
> Ta funkcja obsługuje tylko klastry działające na platformie Azure.
> 
> 

<!-- -->
> [!WARNING]
> Ślady przesyłania strumieniowego są przeznaczone dla scenariuszy deweloperskich i testowych i nie mogą być używane w środowiskach produkcyjnych, ponieważ mają wpływ na uruchomione aplikacje.
> W scenariuszu produkcyjnym należy polegać na zdarzeniach przekazywania przy użyciu Diagnostyka Azure.

1. Przejdź do klastra w programie **Cloud Explorer**. Kliknij prawym przyciskiem myszy i wybierz opcję **Włącz śledzenie przesyłania strumieniowego**
   
    ![Włącz zdalne ślady przesyłania strumieniowego][enablestreamingtraces]
   
    Ta akcja spowoduje rozpoczęcie procesu włączania rozszerzenia śladów przesyłania strumieniowego w węzłach klastra, a także wymaganych konfiguracji sieci.
2. Rozwiń węzeł **węzła** w programie **Cloud Explorer**, kliknij prawym przyciskiem myszy węzeł, z którego chcesz przesyłać strumieniowo ślady, a następnie wybierz polecenie **Wyświetl ślady przesyłania strumieniowego**
   
    ![Wyświetl zdalne ślady przesyłania strumieniowego][viewremotestreamingtraces]
   
    Powtórz krok 2 dla tylu węzłów, z których chcesz zobaczyć ślady. Każdy strumień węzłów zostanie wyświetlony w dedykowanym oknie.
   
    Teraz można zobaczyć ślady emitowane przez Service Fabric i usługi. Jeśli chcesz filtrować zdarzenia w celu wyświetlenia tylko określonej aplikacji, po prostu wpisz nazwę aplikacji w filtrze.
   
    ![Wyświetlanie śladów przesyłania strumieniowego][viewingstreamingtraces]
3. Po zakończeniu przesyłania strumieniowego z klastra można wyłączyć zdalne ślady przesyłania strumieniowego, klikając prawym przyciskiem myszy klaster w programie **Cloud Explorer** i wybierając pozycję **Wyłącz ślady przesyłania strumieniowego**
   
    ![Wyłącz zdalne ślady przesyłania strumieniowego][disablestreamingtraces]

## <a name="next-steps"></a>Następne kroki
* [Przetestuj usługę Service Fabric](service-fabric-testability-overview.md).
* [Zarządzaj aplikacjami Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
