---
title: 'Debugowanie i iteracja na Kubernetes: Visual Studio Code & .NET Core'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: W tym przewodniku szybki start pokazano, jak używać Azure Dev Spaces i Visual Studio Code do debugowania i szybkiej iteracji aplikacji platformy .NET Core w usłudze Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
manager: gwallace
ms.openlocfilehash: b38562879fa67d7ee82e3251ea2fcaa57a2075d6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80240208"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>Szybki Start: debugowanie i iteracja na Kubernetes: Visual Studio Code i .NET Core — Azure Dev Spaces

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Iteracyjnie opracowuj kod w kontenerach przy użyciu Visual Studio Code.
- Debuguj kod w obszarze deweloperskim z Visual Studio Code.

Azure Dev Spaces umożliwia również debugowanie i iterację przy użyciu:
- [Java i Visual Studio Code](quickstart-java.md)
- [Node. js i Visual Studio Code](quickstart-nodejs.md)
- [.NET Core i Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Visual Studio Code zainstalowane](https://code.visualstudio.com/download).
- Rozszerzenia [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) i [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Visual Studio Code zainstalowane.
- [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Azure Kubernetes Service

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Poniższe polecenia tworzą grupę zasobów o nazwie Moja *zasobów* i klaster AKS o nazwie *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie Azure Dev Spaces w klastrze AKS

Użyj `use-dev-spaces` polecenia, aby włączyć miejsca deweloperskie w klastrze AKS i postępuj zgodnie z monitami. Poniższe polecenie włącza miejsca deweloperskie w klastrze *MyAKS* w grupie Grupa *zasobów* i tworzy *domyślny* obszar dev.

> [!NOTE]
> `use-dev-spaces` Polecenie spowoduje również zainstalowanie interfejsu wiersza polecenia Azure dev Spaces, jeśli nie został jeszcze zainstalowany. Nie można zainstalować interfejsu wiersza polecenia Azure Dev Spaces w Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Pobierz przykładowy kod aplikacji

W tym artykule użyto [Azure dev Spaces przykładowej aplikacji](https://github.com/Azure/dev-spaces) do zademonstrowania przy użyciu Azure dev Spaces.

Sklonuj aplikację z usługi GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Przygotuj przykładową aplikację w Visual Studio Code

Otwórz Visual Studio Code, kliknij *plik* , a następnie *Otwórz...*, przejdź do katalogu *dev-Spaces/Samples/dotnetcore/Data-Start/webfronton* , a następnie kliknij przycisk *Otwórz*.

Masz teraz otwarty projekt *webfrontonu* w Visual Studio Code. Aby uruchomić aplikację w obszarze dev, wygeneruj zasoby wykresu Docker i Helm przy użyciu rozszerzenia Azure Dev Spaces w palecie poleceń.

Aby otworzyć paletę poleceń w Visual Studio Code, kliknij przycisk *Widok* i *paleta poleceń*. Rozpocznij wpisywanie `Azure Dev Spaces` i kliknij `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`pozycję.

![Przygotuj pliki konfiguracyjne dla Azure Dev Spaces](./media/common/command-palette.png)

Gdy Visual Studio Code również zostanie wyświetlony komunikat z prośbą o skonfigurowanie publicznego `Yes` punktu końcowego, wybierz opcję włączenia publicznego punktu końcowego.

![Wybierz publiczny punkt końcowy](media/common/select-public-endpoint.png)

To polecenie przygotowuje projekt do uruchomienia w Azure Dev Spaces przez wygenerowanie wykresu pliku dockerfile i Helm. Generuje również katalog *. programu vscode* z konfiguracją debugowania w katalogu głównym projektu.

> [!TIP]
> [Wykres pliku dockerfile i Helm](how-dev-spaces-works-prep.md#prepare-your-code) dla projektu jest używany przez Azure dev Spaces do kompilowania i uruchamiania kodu, ale można modyfikować te pliki, jeśli chcesz zmienić sposób kompilowania i wykonywania projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Kompiluj i uruchamiaj kod w Kubernetes z Visual Studio Code

Kliknij ikonę *debugowania* po lewej stronie, a następnie kliknij pozycję *.NET Core Launch (AZDS)* w górnej części ekranu.

![](media/get-started-netcore/debug-configuration.png)

To polecenie kompiluje i uruchamia usługę w Azure Dev Spaces w trybie debugowania. W oknie *terminalu* u dołu są wyświetlane dane wyjściowe kompilacji i adresy URL dla usługi działającej w Azure dev Spaces. W *konsoli debugowania* są wyświetlane dane wyjściowe dziennika.

> [!Note]
> Jeśli nie widzisz żadnych poleceń Azure Dev Spaces w *palecie poleceń*, upewnij się, że zainstalowano [rozszerzenie Visual Studio Code dla Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Sprawdź również, czy otwarto katalog *dev-Spaces/Samples/dotnetcore/Start-Started/webfronton* w Visual Studio Code.

Możesz zobaczyć, że usługa jest uruchomiona, otwierając publiczny adres URL.

> [!Note]
> Początkowo publiczny adres URL może wskazywać na błąd *nieprawidłowej bramy* . Odczekaj kilka sekund przed odświeżeniem strony sieci Web i powinna zostać wyświetlona usługa.

Kliknij pozycję *Debuguj* , a następnie *Zatrzymaj debugowanie* , aby zatrzymać debuger.

## <a name="update-code"></a>Aktualizowanie kodu

Aby wdrożyć zaktualizowaną wersję usługi, możesz zaktualizować każdy plik w projekcie i ponownie uruchomić program *.NET Core (AZDS)*. Przykład:

1. Jeśli aplikacja jest nadal uruchomiona, kliknij pozycję *Debuguj* , a następnie *Zatrzymaj debugowanie* , aby ją zatrzymać.
1. Aktualizuj [wiersz 22 w `Controllers/HomeController.cs` ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22) do:
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Zapisz zmiany.
1. Uruchom ponownie *uruchomienie programu .NET Core (AZDS)*.
1. Przejdź do działającej usługi, a następnie kliknij pozycję *About (informacje*).
1. Obserwuj zmiany.
1. Kliknij pozycję *Debuguj* , a następnie *Zatrzymaj debugowanie* , aby zatrzymać aplikację.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ustawianie i używanie punktów przerwania do debugowania

Uruchom usługę w trybie debugowania przy użyciu *programu .NET Core Launch (AZDS)*.

Przejdź z powrotem do widoku *Eksploratora* , klikając przycisk *Wyświetl* , a następnie *Eksploratora*. Otwórz `Controllers/HomeController.cs` i kliknij w dowolnym miejscu w wierszu 22, aby umieścić w nim kursor. Aby ustawić punkt przerwania, naciśnij klawisz *F9* lub kliknij pozycję *Debuguj* , a następnie *Przełącz punkt przerwania*.

Otwórz usługę w przeglądarce i zwróć uwagę, że komunikat nie jest wyświetlany. Wróć do Visual Studio Code i obserwuj wiersz 20. Ustawiony punkt przerwania został wstrzymany usługi w wierszu 20. Aby wznowić działanie usługi, naciśnij klawisz *F5* lub kliknij pozycję *Debuguj* i *Kontynuuj*. Wróć do przeglądarki i Zauważ, że komunikat jest teraz wyświetlany.

Podczas uruchamiania usługi w Kubernetes z dołączonym debugerem masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne i informacje o wyjątku.

Usuń punkt przerwania, umieszczając kursor w wierszu 22 `Controllers/HomeController.cs` w i naciskając klawisz *F9*.

## <a name="update-code-from-visual-studio-code"></a>Aktualizuj kod z Visual Studio Code

Gdy usługa jest uruchomiona w trybie debugowania, zaktualizuj wiersz 22 w `Controllers/HomeController.cs`. Przykład:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Zapisz plik. Kliknij pozycję *Debuguj* , a następnie *ponownie uruchom debugowanie* lub na *pasku narzędzi debugowania*kliknij przycisk *Uruchom ponownie debugowanie* .

![](media/common/debug-action-refresh.png)

Otwórz usługę w przeglądarce i sprawdź, czy zostanie wyświetlony zaktualizowany komunikat.

Zamiast ponownie kompilować i wdrażać nowy obraz kontenera przy każdej modyfikacji kodu, Azure Dev Spaces przyrostowo kompiluje kod w istniejącym kontenerze, aby zapewnić szybszą pętlę edycji/debugowania.

## <a name="clean-up-your-azure-resources"></a>Czyszczenie zasobów platformy Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak Azure Dev Spaces ułatwiają tworzenie bardziej złożonych aplikacji w wielu kontenerach i jak można uprościć programowanie do współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych miejscach. 

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](multi-service-netcore.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
