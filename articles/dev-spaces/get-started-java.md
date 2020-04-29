---
title: 'Tworzenie Kubernetesego miejsca deweloperskiego: Visual Studio Code & Java'
services: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 09/26/2018
ms.topic: tutorial
description: W tym samouczku pokazano, jak używać Azure Dev Spaces i Visual Studio Code do debugowania i szybkiej iteracji aplikacji Java w usłudze Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
manager: gwallace
ms.openlocfilehash: c71471d1a89188a065bafef2c5b6372aeff35851
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80240538"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-code-and-java-with-azure-dev-spaces"></a>Utwórz Kubernetese miejsce deweloperskie: Visual Studio Code i Java z Azure Dev Spaces

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Tworzenie środowiska Kubernetes na platformie Azure zoptymalizowanego pod kątem programowania — _przestrzeń deweloperów_.
- Iteracyjne tworzenie kodu w kontenerach przy użyciu programu VS Code i wiersza polecenia.
- Efektywne tworzenie i testowanie kodu w środowisku zespołu.

> [!Note]
> Jeśli w dowolnym momencie **masz zablokowany dostęp** do programu, zobacz sekcję [Rozwiązywanie problemów](troubleshooting.md) .

## <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure
Usługa Azure Dev Spaces wymaga minimalnej konfiguracji komputera lokalnego. Większość ustawień obszaru deweloperskiego jest przechowywana w chmurze i udostępniana innym użytkownikom. Zacznij od pobrania i uruchomienia [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Logowanie do interfejsu wiersza polecenia platformy Azure
Zaloguj się do platformy Azure. W oknie terminala wpisz następujące polecenie:

```azurecli
az login
```

> [!Note]
> Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Jeśli masz wiele subskrypcji platformy Azure...
Możesz wyświetlić swoje subskrypcje, uruchamiając polecenie: 

```azurecli
az account list --output table
```

Znajdź subskrypcję, która ma *wartość true dla właściwości* *IsDefault*.
Jeśli nie jest to subskrypcja, której chcesz użyć, możesz zmienić subskrypcję domyślną:

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Tworzenie klastra Kubernetes obsługującego usługę Azure Dev Spaces

W wierszu polecenia Utwórz grupę zasobów w [regionie, który obsługuje Azure dev Spaces][supported-regions].

```azurecli
az group create --name MyResourceGroup --location <region>
```

Utwórz klaster Kubernetes za pomocą następującego polecenia:

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Utworzenie klastra trwa kilka minut.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurowanie klastra usługi AKS do korzystania z usługi Azure Dev Spaces

Wpisz następujące polecenie interfejsu wiersza polecenia platformy Azure, podając grupę zasobów, która zawiera klaster usługi AKS, oraz nazwę klastra usługi AKS. Polecenie konfiguruje na klastrze obsługę usługi Azure Dev Spaces.

   ```azurecli
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

> [!IMPORTANT]
> Proces konfiguracji usługi Azure Dev Spaces spowoduje usunięcie przestrzeni nazw `azds` w klastrze, jeśli taka istnieje.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Uzyskiwanie debugowania rozwiązania Kubernetes dla programu VS Code
Rozbudowane funkcje, takie jak debugowanie rozwiązania Kubernetes, są dostępne dla deweloperów platformy .NET Core i środowiska Node.js korzystających z programu VS Code.

1. Jeśli nie masz programu [VS Code](https://code.visualstudio.com/Download), zainstaluj go.
1. Pobierz i zainstaluj [rozszerzenie usługi Azure Dev Spaces dla programu VS](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Kliknij pozycję Zainstaluj najpierw na stronie rozszerzenia w witrynie Marketplace, a następnie w programie VS Code. 

Aby debugować aplikacje w języku Java za pomocą usługi Azure Dev Spaces, pobierz i zainstaluj rozszerzenie [Java Debugger for Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) dla programu VS Code. Kliknij pozycję Zainstaluj najpierw na stronie rozszerzenia w witrynie Marketplace, a następnie w programie VS Code.

## <a name="create-a-web-app-running-in-a-container"></a>Tworzenie aplikacji internetowej uruchomionej w kontenerze

W tej sekcji utworzysz aplikację internetową w języku Java i uruchomisz ją w kontenerze w środowisku Kubernetes.

### <a name="create-a-java-web-app"></a>Tworzenie aplikacji internetowej w języku Java
Pobierz kod z usługi GitHub, przechodząc [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) do lokalizacji i wybierając opcję **Klonuj lub Pobierz** , aby pobrać repozytorium GitHub do środowiska lokalnego. Kod używany w tym przewodniku znajduje się w lokalizacji `samples/java/getting-started/webfrontend`.

## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Przygotowywanie kodu do opracowywania na platformie Docker i Kubernetes
W tej chwili masz podstawową aplikację internetową, która działa lokalnie. Teraz konteneryzujesz ją, tworząc zasoby, które definiują kontener aplikacji i sposób jego wdrażania na platformie Kubernetes. To zadanie łatwo wykonać za pomocą usługi Azure Dev Spaces: 

1. Uruchom program VS Code i otwórz folder `webfrontend`. (Możesz zignorować wszystkie domyślne monity o dodanie zasobów debugowania lub przywrócenie projektu).
1. Otwórz zintegrowany terminal w programie VS Code (przy użyciu menu **Widok > Zintegrowany terminal**).
1. Uruchom następujące polecenie (upewnij się, że bieżący folder to **webfrontend**):

    ```cmd
    azds prep --enable-ingress
    ```

Polecenie `azds prep` interfejsu wiersza polecenia platformy Azure generuje zasoby platformy Docker i Kubernetes z ustawieniami domyślnymi:
* Plik `./Dockerfile` opisuje obraz kontenera aplikacji oraz sposób kompilowania kodu źródłowego i uruchamiania go w kontenerze.
* [Pakiet Helm](https://docs.helm.sh) w folderze `./charts/webfrontend` opisuje, jak wdrożyć kontener na platformie Kubernetes.

> [!TIP]
> [Wykres pliku dockerfile i Helm](how-dev-spaces-works-prep.md#prepare-your-code) dla projektu jest używany przez Azure dev Spaces do kompilowania i uruchamiania kodu, ale można modyfikować te pliki, jeśli chcesz zmienić sposób kompilowania i wykonywania projektu.

Na razie nie trzeba rozumieć pełnej zawartości tych plików. Warto jednak wspomnieć, że **te same zasoby konfiguracji jako kodu platformy Kubernetes i Docker mogą być używane od etapu opracowywania po etap produkcyjny, co zapewnia większą spójność w różnych środowiskach.**
 
Polecenie `prep` generuje również plik o nazwie `./azds.yaml`. Jest to plik konfiguracji usługi Azure Dev Spaces. Uzupełnia on artefakty platformy Docker i Kubernetes o dodatkową konfigurację zapewniającą środowisko programowania przyrostowego na platformie Azure.

## <a name="build-and-run-code-in-kubernetes"></a>Kompilowanie i uruchamianie kodu na platformie Kubernetes
Teraz uruchomimy nasz kod! W oknie terminalu uruchom to polecenie z **głównego folderu kodu**, webfrontend: 

```cmd
azds up
```

Śledź dane wyjściowe polecenia — wraz z postępem zauważysz kilka rzeczy:
- Kod źródłowy jest synchronizowany z przestrzenią deweloperów na platformie Azure.
- Obraz kontenera jest tworzony na platformie Azure, zgodnie z tym, co określają elementy zawartości aparatu Docker w folderze kodu.
- Tworzone są obiekty Kubernetes, które wykorzystują obraz kontenera, zgodnie z tym, co określa plan narzędzia Helm w folderze kodu.
- Wyświetlane są informacje o punktach końcowych kontenera. W tym przypadku spodziewamy się publicznego adresu URL HTTP.
- Zakładając, że powyższe etapy zakończą się powodzeniem, podczas uruchamiania kontenera powinny być widoczne dane wyjściowe `stdout` (i `stderr`).

> [!Note]
> Te kroki będą trwać dłużej podczas pierwszego uruchomienia polecenia `up`, ale kolejne uruchomienia powinny być szybsze.

### <a name="test-the-web-app"></a>Testowanie aplikacji internetowej
Przeskanuj dane wyjściowe konsoli w poszukiwaniu informacji o publicznym adresie URL, który został utworzony za pomocą polecenia `up`. Będzie on mieć postać: 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
```

Określ publiczny adres URL usługi w danych wyjściowych `up` polecenia. Zostanie ona zakończona `.azds.io`. W powyższym przykładzie publiczny adres URL to `http://webfrontend.1234567890abcdef1234.eus.azds.io/`.

Aby wyświetlić swoją aplikację internetową, Otwórz publiczny adres URL w przeglądarce. Ponadto informacja `stdout` i `stderr` wyjście są przesyłane strumieniowo do okna terminalu *śledzenia azds* podczas korzystania z aplikacji sieci Web. Zobaczysz również informacje o śledzeniu żądań HTTP, które przechodzą przez system. Ułatwia to śledzenie złożonych wywołań wielousługowych podczas opracowywania. Instrumentacja dodana przez funkcję Spaces dev udostępnia śledzenie tego żądania.

> [!Note]
> Oprócz publicznego adresu URL można użyć alternatywnego `http://localhost:<portnumber>` adresu URL, który jest wyświetlany w danych wyjściowych konsoli. Jeśli używasz adresu URL hosta lokalnego, może się wydawać, że kontener działa lokalnie, ale faktycznie jest on uruchamiany w usłudze AKS. Azure Dev Spaces używa funkcji *przesyłania dalej portów* Kubernetes, aby mapować port localhost do kontenera działającego w AKS. Ułatwia to korzystanie z usługi z komputera lokalnego.

### <a name="update-a-content-file"></a>Aktualizowanie pliku zawartości
Usługa Azure Dev Spaces umożliwia nie tylko uruchamianie kodu w środowisku Kubernetes — pozwala też szybko i wielokrotnie wyświetlać efekt zmian wprowadzonych w kodzie w środowisku Kubernetes w chmurze.

1. W oknie terminalu naciśnij klawisze `Ctrl+C`, aby zatrzymać polecenie `azds up`.
1. Otwórz `src/main/java/com/ms/sample/webfrontend/Application.java`i edytuj wiadomość z powitaniem w [wierszu 19](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19):

    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Zapisz plik.
1. Uruchom polecenie `azds up` w oknie terminalu.

Spowoduje to ponowne skompilowanie obrazu kontenera i ponowne wdrożenie planu narzędzia Helm. Aby zobaczyć efekt zmian kodu w uruchomionej aplikacji, po prostu odśwież przeglądarkę.

Oprócz tego dostępna jest jeszcze *szybsza metoda* opracowywania kodu. Omówimy ją w następnej sekcji. 

## <a name="debug-a-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes

W tej sekcji użyjesz programu VS Code do bezpośredniego debugowania naszego kontenera uruchomionego na platformie Azure. Ponadto dowiesz się, jak uzyskać szybszą pętlę edycji, uruchamiania i testowania.

![](media/common/edit-refresh-see.png)

> [!Note]
> **Jeśli utkniesz** w dowolnym momencie, zobacz sekcję [Rozwiązywanie problemów](troubleshooting.md) lub prześlij komentarz na tej stronie.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicjowanie zasobów debugowania za pomocą rozszerzenia programu VS Code
Najpierw musisz skonfigurować projekt kodu, aby program VS Code komunikował się z naszą przestrzenią deweloperów na platformie Azure. Rozszerzenie programu VS Code dla usługi Azure Dev Spaces udostępnia polecenie pomocnika w celu ustawienia konfiguracji debugowania. 

Otwórz okno **Paleta poleceń** (za pomocą menu **Widok | Paleta poleceń**) i przy użyciu autouzupełniania wpisz i wybierz to polecenie: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Spowoduje to dodanie konfiguracji debugowania dla usługi Azure Dev Spaces w obszarze folderu `.vscode`. Nie należy mylić tego polecenia z poleceniem `azds prep`, które umożliwia skonfigurowanie projektu na potrzeby wdrożenia.

![](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Wybieranie konfiguracji debugowania w rozwiązaniu AZDS
1. Aby otworzyć widok debugowania, kliknij ikonę debugowania na **pasku działań** w bocznej części programu VS Code.
1. Wybierz pozycję **Launch Java Program (AZDS)** (Uruchom program Java — AZDS) jako aktywną konfigurację debugowania.

![](media/get-started-java/debug-configuration.png)

> [!Note]
> Jeśli na palecie poleceń nie widać żadnych poleceń usługi Azure Dev Spaces, upewnij się, że masz zainstalowane rozszerzenie VS Code dla usługi Azure Dev Spaces. Upewnij się, że obszar roboczy otwarty w VS Code jest folderem zawierającym `azds.yaml`.

### <a name="debug-the-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes
Po naciśnięciu klawisza **F5** możesz debugować kod w środowisku Kubernetes.

Podobnie jak w przypadku polecenia `up` kod jest synchronizowany z obszarem deweloperskim, a kontener jest kompilowany i wdrażany w środowisku Kubernetes. Oczywiście tym razem debuger jest dołączany do zdalnego kontenera.

> [!Tip]
> VS Code pasek stanu zmieni kolor na pomarańczowy, co oznacza, że debuger jest dołączony. Zostanie również wyświetlony adres URL, który można kliknąć, aby otworzyć aplikację.

![](media/common/vscode-status-bar-url.png)

Ustaw punkt przerwania w pliku kodu po stronie serwera, na przykład w obrębie funkcji `greeting()` w pliku źródłowym `src/main/java/com/ms/sample/webfrontend/Application.java`. Odświeżenie strony w przeglądarce powoduje aktywowanie punktu przerwania.

Masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne, informacje o wyjątkach itd., zupełnie jakby kod był wykonywany lokalnie.

### <a name="edit-code-and-refresh"></a>Edytowanie kodu i odświeżanie
Przy aktywnym debugerze wprowadź zmianę w kodzie. Na przykład zmodyfikuj wiadomość powitalną w pliku `src/main/java/com/ms/sample/webfrontend/Application.java`. 

```java
public String greeting()
{
    return "I'm debugging Java code in Azure!";
}
```

Zapisz plik, a następnie w **okienku Akcje debugowania**kliknij przycisk **Uruchom ponownie** .

![](media/common/debug-action-refresh.png)

Zamiast ponownego kompilowania i wdrażania nowego obrazu kontenera przy każdej zmianie kodu, co często zajmuje dużo czasu, usługa Azure Dev Spaces przyrostowo ponownie kompiluje kod w istniejącym kontenerze, co przyspiesza działanie pętli edytowania/debugowania.

Odśwież aplikację internetową w przeglądarce. Twój komunikat powinien zostać wyświetlony w interfejsie użytkownika.

**Umiesz już korzystać z metody szybkiego wprowadzania zmian w kodzie i debugowania bezpośrednio w środowisku Kubernetes.** Następnie zobaczysz, jak utworzyć i wywołać drugi kontener.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o opracowywaniu dla wielu usług](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
