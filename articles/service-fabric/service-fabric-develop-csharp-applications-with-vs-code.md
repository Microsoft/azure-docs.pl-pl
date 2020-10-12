---
title: Tworzenie aplikacji .NET Core za pomocą Visual Studio Code
description: W tym artykule przedstawiono sposób kompilowania, wdrażania i debugowania aplikacji .NET Core Service Fabric przy użyciu Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 1d7478e6b81ef2c53ca6194197336e91d3ff250b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75614527"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Opracowywanie aplikacji Service Fabric C# przy użyciu Visual Studio Code

[Rozszerzenie Service Fabric Reliable Services dla vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) ułatwia tworzenie aplikacji platformy .NET Service Fabric Core w systemach operacyjnych Windows, Linux i macOS.

W tym artykule opisano sposób kompilowania, wdrażania i debugowania aplikacji Service Fabric .NET Core przy użyciu Visual Studio Code.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że zainstalowano już VS Code, Service Fabric Reliable Services rozszerzenia dla VS Code oraz wszystkie zależności wymagane dla środowiska deweloperskiego. Aby dowiedzieć się więcej, zobacz [wprowadzenie](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Pobieranie przykładu
W tym artykule jest stosowana aplikacja CounterService w [repozytorium usługi Service Fabric .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Aby sklonować repozytorium do komputera deweloperskiego, uruchom następujące polecenie w oknie terminalu (okno polecenia w systemie Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Otwórz aplikację w VS Code

### <a name="windows"></a>Windows
Kliknij prawym przyciskiem myszy ikonę VS Code w menu Start i wybierz polecenie **Uruchom jako administrator**. Aby dołączyć debuger do usług, musisz uruchomić VS Code jako administrator.

### <a name="linux"></a>Linux
Za pomocą terminalu przejdź do ścieżki/service-fabric-dotnet-core-getting-started/Services/CounterService z katalogu, do którego aplikacja została sklonowana lokalnie.

Uruchom następujące polecenie, aby otworzyć VS Code jako użytkownik główny, aby debuger mógł dołączyć do Twoich usług.
```
sudo code . --user-data-dir='.'
```

Aplikacja powinna teraz pojawić się w obszarze roboczym VS Code.

![Aplikacja usługi Counter w obszarze roboczym](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Kompilowanie aplikacji
1. Naciśnij klawisz (Ctrl + Shift + p), aby otworzyć **paletę poleceń** w vs Code.
2. Wyszukaj i wybierz polecenie **Service Fabric: build Application** . Dane wyjściowe kompilacji są wysyłane do zintegrowanego terminalu.

   ![Polecenie kompilowania aplikacji w VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Wdrażanie aplikacji w klastrze lokalnym
Po skompilowaniu aplikacji można wdrożyć ją w klastrze lokalnym. 

1. W **palecie poleceń**wybierz **polecenie Service Fabric: Deploy Application (localhost)**. Dane wyjściowe procesu instalacji są wysyłane do terminalu zintegrowanego.

   ![Polecenie wdrażania aplikacji w VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Po zakończeniu wdrażania Uruchom przeglądarkę i Otwórz Service Fabric Explorer: http: \/ /localhost: 19080/Explorer. Powinna zostać wyświetlona aplikacja. Może to potrwać pewien czas. 

   ![Aplikacja usługi Counter w Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Po sprawdzeniu, czy aplikacja jest uruchomiona, uruchom przeglądarkę i Otwórz Tę stronę: http: \/ /localhost: 31002. Jest to fronton sieci Web aplikacji. Odśwież stronę, aby zobaczyć bieżącą wartość licznika w miarę jego przyrostu.

   ![Aplikacja usługi Counter w przeglądarce](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Publikowanie aplikacji w klastrze usługi Azure Service Fabric
Wraz z wdrażaniem aplikacji w klastrze lokalnym można również opublikować ją w klastrze zdalnego Service Fabric platformy Azure. 

1. Upewnij się, że aplikacja została skompilowana przy użyciu powyższych instrukcji. Zaktualizuj wygenerowany plik konfiguracji `Cloud.json` , podając szczegóły zdalnego klastra, w którym chcesz publikować.

2. W **palecie poleceń**wybierz **polecenie Service Fabric: Publish Application**. Dane wyjściowe procesu instalacji są wysyłane do terminalu zintegrowanego.

   ![Polecenie publikowania aplikacji w VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Po zakończeniu wdrażania Uruchom przeglądarkę i Otwórz Service Fabric Explorer: `https:<clusterurl>:19080/Explorer` . Powinna zostać wyświetlona aplikacja. Może to potrwać pewien czas. 

## <a name="debug-the-application"></a>Debugowanie aplikacji
Podczas debugowania aplikacji w VS Code, aplikacja musi działać w klastrze lokalnym. Punkty przerwania można następnie dodać do kodu.

Aby ustawić punkt przerwania i debugowanie, wykonaj następujące czynności:
1. W Eksploratorze Otwórz plik */src/CounterServiceApplication/CounterService/CounterService.cs* i ustaw punkt przerwania w wierszu 62 wewnątrz `RunAsync` metody.
3. Kliknij ikonę debugowania na **pasku działania** , aby otworzyć widok debugera w vs Code. Kliknij ikonę koła zębatego w górnej części widoku debuger i wybierz pozycję **.NET Core** z menu środowisko listy rozwijanej. Zostanie otwarty launch.jspliku. Możesz zamknąć ten plik. Po wybraniu opcji konfiguracji w menu Debugowanie konfiguracji obok przycisku Uruchom (zielona strzałka) powinna zostać wyświetlona opcja konfiguracja.

   ![Ikona debugowania w obszarze roboczym VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Wybierz z menu Debuguj konfigurację opcję **Dołącz do programu .NET Core** .

   ![Ikona debugowania w obszarze roboczym VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Otwórz Service Fabric Explorer w przeglądarce: http: \/ /localhost: 19080/Explorer. Kliknij pozycję **aplikacje** i przejdź do szczegółów, aby określić węzeł podstawowy, na którym działa CounterService. Na obrazie poniżej węzła podstawowego dla CounterService jest węzeł 0.

   ![Węzeł podstawowy dla CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. W VS Code kliknij ikonę Run (zielona strzałka) obok konfiguracji debugowania **dołączania do programu .NET Core** . W oknie dialogowym Wybieranie procesu wybierz proces CounterService uruchomiony w węźle podstawowym, który został zidentyfikowany w kroku 4.

   ![Proces podstawowy](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Punkt przerwania w pliku *CounterService.cs* zostanie szybko trafiony. Następnie można eksplorować wartości zmiennych lokalnych. Użyj paska narzędzi debugowania w górnej części VS Code, aby kontynuować wykonywanie, przekroczyć wiersze, wkroczyć metody lub krok poza bieżącą metodę. 

   ![Wartości debugowania](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Aby zakończyć sesję debugowania, kliknij ikonę wtyczki na pasku narzędzi debugowania w górnej części VS Code.
   
   ![Rozłącz z debugerem](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Po zakończeniu debugowania można użyć polecenia **Service Fabric: Remove aplikacji** , aby usunąć aplikację CounterService z klastra lokalnego. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak opracowywać i debugować aplikacje Service Fabric Java przy użyciu vs Code](./service-fabric-develop-java-applications-with-vs-code.md).



