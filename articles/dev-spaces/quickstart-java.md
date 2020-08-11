---
title: 'Debugowanie i iteracja na Kubernetes: Visual Studio Code & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: W tym przewodniku szybki start pokazano, jak używać Azure Dev Spaces i Visual Studio Code do debugowania i szybkiej iteracji aplikacji Java w usłudze Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Java, Helm, Service siatk, routing sieci usługi, polecenia kubectl, k8s
manager: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: 53d7b602166ae463ddb821f5a9992de39f6e1644
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078720"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Szybki Start: debugowanie i iteracja na Kubernetes z Visual Studio Code i Java — Azure Dev Spaces

W tym przewodniku szybki start skonfigurujesz Azure Dev Spaces z zarządzanym klastrem Kubernetes i używasz aplikacji Java w Visual Studio Code, aby iteracyjnie opracowywać i debugować kod w kontenerach. Azure Dev Spaces umożliwia debugowanie i testowanie wszystkich składników aplikacji w usłudze Azure Kubernetes Service (AKS) przy minimalnych konfiguracjach maszyn deweloperskich. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Zestaw Java Development Kit (JDK) 1.8.0 +](https://aka.ms/azure-jdks).
- [Maven 3.5.0 +](https://maven.apache.org/download.cgi).
- [Program Visual Studio Code](https://code.visualstudio.com/download)
- Debuger [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) i [Java dla rozszerzeń Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) dla Visual Studio Code.
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
- Usługi [git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Azure Kubernetes Service

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Następujące polecenia tworzą grupę zasobów o nazwie Moja *zasobów* i klaster AKS o nazwie *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie Azure Dev Spaces w klastrze AKS

Użyj `use-dev-spaces` polecenia, aby włączyć miejsca deweloperskie w KLASTRZE AKS i postępuj zgodnie z monitami. Następujące polecenie włącza miejsca deweloperskie w klastrze *MyAKS* w grupie Grupa *zasobów* i tworzy *domyślny* obszar dev.

> [!NOTE]
> `use-dev-spaces`Polecenie spowoduje również zainstalowanie interfejsu wiersza polecenia Azure dev Spaces, jeśli nie został jeszcze zainstalowany. Nie można zainstalować interfejsu wiersza polecenia Azure Dev Spaces w Azure Cloud Shell.

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

Otwórz Visual Studio Code, wybierz pozycję **plik** , a następnie **Otwórz**, przejdź do katalogu *dev-Spaces/Samples/Java/Start-Started/webfronton* , a następnie wybierz pozycję **Otwórz**.

Masz teraz otwarty projekt *webfrontonu* w Visual Studio Code. Aby uruchomić aplikację w obszarze dev, wygeneruj zasoby wykresu Docker i Helm przy użyciu rozszerzenia Azure Dev Spaces w palecie poleceń.

Aby otworzyć paletę poleceń w Visual Studio Code, wybierz pozycję **Widok** i **paleta poleceń**. Rozpocznij wpisywanie `Azure Dev Spaces` i wybierz **Azure dev Spaces: Przygotuj pliki konfiguracji dla Azure dev Spaces**.

![Przygotuj pliki konfiguracyjne dla Azure Dev Spaces](./media/common/command-palette.png)

Gdy Visual Studio Code również zostanie wyświetlony komunikat z prośbą o skonfigurowanie obrazów podstawowych, uwidoczniony port i publiczny punkt końcowy, należy wybrać `Azul Zulu OpenJDK for Azure (Free LTS)` podstawowy obraz `8080` dla uwidocznionego portu i `Yes` włączyć publiczny punkt końcowy.

![Wybierz obraz podstawowy](media/get-started-java/select-base-image.png)

![Wybierz uwidoczniony port](media/get-started-java/select-exposed-port.png)

![Wybierz publiczny punkt końcowy](media/get-started-java/select-public-endpoint.png)

To polecenie przygotowuje projekt do uruchomienia w Azure Dev Spaces przez wygenerowanie wykresu pliku dockerfile i Helm. Generuje również katalog *. programu vscode* z konfiguracją debugowania w katalogu głównym projektu.

> [!TIP]
> [Wykres pliku dockerfile i Helm](how-dev-spaces-works-prep.md#prepare-your-code) dla projektu jest używany przez Azure dev Spaces do kompilowania i uruchamiania kodu, ale można modyfikować te pliki, jeśli chcesz zmienić sposób kompilowania i uruchamiania projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Kompiluj i uruchamiaj kod w Kubernetes z Visual Studio Code

Wybierz ikonę **debugowania** po lewej stronie i wybierz pozycję **Uruchom program Java (AZDS)** w górnej części ekranu.

![Uruchom program Java](media/get-started-java/debug-configuration.png)

To polecenie kompiluje i uruchamia usługę w Azure Dev Spaces. W oknie **terminalu** u dołu są wyświetlane dane wyjściowe kompilacji i adresy URL dla usługi uruchomionej Azure dev Spaces. W **konsoli debugowania** są wyświetlane dane wyjściowe dziennika.

> [!Note]
> Jeśli nie widzisz żadnych poleceń Azure Dev Spaces w **palecie poleceń**, upewnij się, że zainstalowano [rozszerzenie Visual Studio Code dla Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Sprawdź również, czy otwarto w Visual Studio Code folder *dev Spaces/Samples/Java/Start-Started/webfrontonie* .

Możesz zobaczyć, że usługa jest uruchomiona, otwierając publiczny adres URL.

Wybierz kolejno opcje **Debuguj** i **Zatrzymaj debugowanie** , aby zatrzymać debuger.

## <a name="update-code"></a>Aktualizowanie kodu

Aby wdrożyć zaktualizowaną wersję usługi, możesz zaktualizować dowolny plik w projekcie i ponownie uruchomić **program Java (AZDS)**. Na przykład:

1. Jeśli aplikacja nadal działa, wybierz pozycję **Debuguj** , a następnie **Zatrzymaj debugowanie** , aby je zatrzymać.
1. Zaktualizuj [wiersz 19 w `src/main/java/com/ms/sample/webfrontend/Application.java` ](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) do:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Zapisz zmiany.
1. Ponownie uruchom **program Java (AZDS)**.
1. Przejdź do działającej usługi i obserwuj wprowadzone zmiany.
1. Wybierz pozycję **Debuguj** , a następnie **Zatrzymaj debugowanie** , aby zatrzymać aplikację.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ustawianie i używanie punktów przerwania do debugowania

Uruchom usługę przy użyciu **programu uruchomieniowego języka Java (AZDS)**. Powoduje to również uruchomienie usługi w trybie debugowania.

Przejdź z powrotem do widoku **Eksploratora** , wybierając pozycję **Widok** , a następnie **Eksploratora**. Otwórz *src/Main/Java/com/MS/Sample/webfronton/Application. Java* i kliknij gdziekolwiek w wierszu 19, aby umieścić w nim kursor. Aby ustawić punkt przerwania, naciśnij klawisz **F9** lub wybierz **Debuguj** , a następnie **Przełącz punkt przerwania**.

Otwórz usługę w przeglądarce i zwróć uwagę, że komunikat nie jest wyświetlany. Wróć do Visual Studio Code i obserwuj wiersz 19. Ustawiony punkt przerwania został wstrzymany usługi w wierszu 19. Aby wznowić działanie usługi, naciśnij klawisz **F5** lub wybierz pozycję **Debuguj** , a następnie **Kontynuuj**. Wróć do przeglądarki i Zauważ, że komunikat jest teraz wyświetlany.

Podczas uruchamiania usługi w Kubernetes z dołączonym debugerem masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne i informacje o wyjątku.

Usuń punkt przerwania, umieszczając kursor w wierszu 19 w *src/Main/Java/com/MS/Sample/webfrontonie/Application. Java* i naciskając klawisz **F9**.

## <a name="update-code-from-visual-studio-code"></a>Aktualizuj kod z Visual Studio Code

Gdy usługa jest uruchomiona w trybie debugowania, zaktualizuj wiersz 19 w *src/Main/Java/com/MS/Sample/webfronton/Application. Java*. Na przykład:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Zapisz plik. Wybierz pozycję **Debuguj** , a następnie **ponownie uruchom debugowanie** lub na **pasku narzędzi debugowania**wybierz przycisk **Uruchom ponownie debugowanie** .

![Odśwież debugowanie](media/common/debug-action-refresh.png)

Otwórz usługę w przeglądarce i sprawdź, czy zostanie wyświetlony zaktualizowany komunikat.

Zamiast ponownie kompilować i wdrażać nowy obraz kontenera przy każdej modyfikacji kodu, Azure Dev Spaces przyrostowo kompiluje kod w istniejącym kontenerze, aby zapewnić szybszą pętlę edycji/debugowania.

## <a name="clean-up-your-azure-resources"></a>Czyszczenie zasobów platformy Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak działa Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Jak działa usługa Azure Dev Spaces](how-dev-spaces-works.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
