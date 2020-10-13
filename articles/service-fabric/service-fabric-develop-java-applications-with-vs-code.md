---
title: Tworzenie aplikacji Java za pomocą Visual Studio Code
description: W tym artykule przedstawiono sposób kompilowania, wdrażania i debugowania aplikacji w języku Java Service Fabric przy użyciu Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.custom: devx-track-java
ms.author: pepogors
ms.openlocfilehash: cc65deb924a9f3367c2ea1d7c71544743ccf2697
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327365"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Opracowywanie aplikacji Service Fabric Java za pomocą Visual Studio Code

[Rozszerzenie Service Fabric Reliable Services dla vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) ułatwia tworzenie aplikacji Java Service Fabric w systemach operacyjnych Windows, Linux i macOS.

W tym artykule opisano sposób kompilowania, wdrażania i debugowania aplikacji w języku Java Service Fabric przy użyciu Visual Studio Code.

> [!IMPORTANT]
> Aplikacje Java Service Fabric można opracowywać na maszynach z systemem Windows, ale można je wdrożyć tylko w klastrach systemu Linux platformy Azure. Debugowanie aplikacji Java nie jest obsługiwane w systemie Windows.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że zainstalowano już VS Code, Service Fabric Reliable Services rozszerzenia dla VS Code oraz wszystkie zależności wymagane dla środowiska deweloperskiego. Aby dowiedzieć się więcej, zobacz [wprowadzenie](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Pobieranie przykładu
W tym artykule jest stosowana aplikacja do głosowania w [przykładowym repozytorium usługi Service Fabric Java z przewodnikiem Szybki Start](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Aby sklonować repozytorium do komputera deweloperskiego, uruchom następujące polecenie w oknie terminalu (okno polecenia w systemie Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Otwórz aplikację w VS Code

Otwórz program VS Code.  Kliknij ikonę Eksploratora na **pasku działania** , a następnie kliknij pozycję **Otwórz folder**lub kliknij pozycję  **plik-> Otwórz folder**. Przejdź do katalogu *./Service-Fabric-Java-QuickStart/Voting* w folderze, do którego zostało Sklonowane repozytorium, a następnie kliknij przycisk **OK**. Obszar roboczy powinien zawierać te same pliki, jak pokazano na poniższym zrzucie ekranu.

![Aplikacja do głosowania w języku Java w obszarze roboczym](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Kompilowanie aplikacji

1. Naciśnij klawisz (Ctrl + Shift + p), aby otworzyć **paletę poleceń** w vs Code.
2. Wyszukaj i wybierz polecenie **Service Fabric: build Application** . Dane wyjściowe kompilacji są wysyłane do zintegrowanego terminalu.

   ![Polecenie kompilowania aplikacji w VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Wdrażanie aplikacji w klastrze lokalnym
Po skompilowaniu aplikacji można wdrożyć ją w klastrze lokalnym. 

> [!IMPORTANT]
> Wdrażanie aplikacji Java w klastrze lokalnym nie jest obsługiwane na maszynach z systemem Windows.

1. W **palecie poleceń**wybierz **polecenie Service Fabric: Deploy Application (localhost)**. Dane wyjściowe procesu instalacji są wysyłane do terminalu zintegrowanego.

   ![Polecenie wdrażania aplikacji w VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Po zakończeniu wdrażania Uruchom przeglądarkę i Otwórz Service Fabric Explorer: `http://localhost:19080/Explorer` . Powinna zostać wyświetlona aplikacja. Może to potrwać pewien czas. 

   ![Aplikacja do głosowania w Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Po zweryfikowaniu, że aplikacja jest uruchomiona, uruchom przeglądarkę i Otwórz Tę stronę: `http://localhost:8080` . Jest to fronton sieci Web aplikacji. Możesz dodawać elementy i klikać je w celu głosowania.

   ![Aplikacja do głosowania w przeglądarce](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Aby usunąć aplikację z klastra, wybierz polecenie **Service Fabric: Remove Application** w **palecie poleceń**. Dane wyjściowe procesu odinstalowywania są wysyłane do terminalu zintegrowanego. Za pomocą Service Fabric Explorer można sprawdzić, czy aplikacja została usunięta z klastra lokalnego.

## <a name="debug-the-application"></a>Debugowanie aplikacji
Podczas debugowania aplikacji w VS Code, aplikacja musi działać w klastrze lokalnym. Punkty przerwania można następnie dodać do kodu.

> [!IMPORTANT]
> Debugowanie aplikacji Java nie jest obsługiwane na maszynach z systemem Windows.

Aby przygotować VotingDataService i aplikację do głosowania na potrzeby debugowania, wykonaj następujące czynności:

1. Aktualizowanie pliku *głosowania/VotingApplication/VotingDataServicePkg/Code/EntryPoint. sh* .
Skomentuj polecenie w wierszu 6 (Użyj "#") i Dodaj następujące polecenie do dołu pliku:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Zaktualizuj plik *głosu/VotingApplication/ApplicationManifest.xml* . Ustaw atrybuty **MinReplicaSetSize** i **wartość targetreplicasetsize** na wartość "1" w elemencie **klasy statefulservice** :
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Kliknij ikonę debugowania na **pasku działania** , aby otworzyć widok debugera w vs Code. Kliknij ikonę koła zębatego w górnej części widoku debuger i wybierz pozycję **Java** z menu środowisko listy rozwijanej. Zostanie otwarty launch.jspliku. 

   ![Ikona debugowania w obszarze roboczym VS Code](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. W launch.jsw pliku ustaw wartość portu w konfiguracji o nazwie **debug (Dołącz)** do **8001**. Zapisz plik.

   ![Konfiguracja debugowania dla launch.jsna](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Wdróż aplikację w klastrze lokalnym za pomocą polecenia **Service Fabric: Deploy Application (localhost)** . Sprawdź, czy aplikacja działa w Service Fabric Explorer. Aplikacja jest teraz gotowa do debugowania.

Aby ustawić punkt przerwania, wykonaj następujące czynności:

1. W Eksploratorze Otwórz plik */Voting/VotingDataService/src/statefulservice/VotingDataService.Java* . Ustaw punkt przerwania w pierwszym wierszu kodu w `try` bloku w `addItem` metodzie (wiersz 80).
   
   ![Ustaw punkt przerwania w usłudze danych do głosowania](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Upewnij się, że punkty przerwania są ustawiane na wierszach wykonywalnych kodu. Dla przykładowych punktów przerwania ustawionych dla deklaracji metody, `try` instrukcji lub `catch` instrukcji zostanie pominięte przez debuger.
2. Aby rozpocząć debugowanie, kliknij ikonę debugowania na **pasku działania**, wybierz konfigurację **debugowania (dołączania)** z menu Debuguj, a następnie kliknij przycisk Run (zielona strzałka).

   ![Debuguj (Dołącz) konfigurację](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. W przeglądarce internetowej wejdź na stronę `http://localhost:8080`. Wpisz nowy element w polu tekstowym, a następnie kliknij pozycję **+ Dodaj**. Powinien zostać trafiony punkt przerwania. Możesz użyć paska narzędzi debugowania w górnej części VS Code, aby kontynuować wykonywanie, przekroczyć wiersze, wkroczać metody lub krok poza bieżącą metodę. 
   
   ![Punkt przerwania trafień](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Aby zakończyć sesję debugowania, kliknij ikonę wtyczki na pasku narzędzi debugowania w górnej części VS Code.
   
   ![Rozłącz z debugerem](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Po zakończeniu debugowania można użyć polecenia **Service Fabric: Remove aplikacji** , aby usunąć aplikację do głosowania z klastra lokalnego. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak opracowywać i debugować aplikacje Service Fabric w języku C# przy użyciu vs Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
