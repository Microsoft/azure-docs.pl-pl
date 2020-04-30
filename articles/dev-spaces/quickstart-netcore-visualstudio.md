---
title: 'Debugowanie i iteracja na Kubernetes: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: W tym przewodniku szybki start pokazano, jak używać Azure Dev Spaces i programu Visual Studio do debugowania i szybkiej iteracji aplikacji platformy .NET Core w usłudze Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: e6fa490285582c827b8704496fb7a20a7eb9cfbc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82166023"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Szybki Start: debugowanie i iteracja na Kubernetes: Visual Studio & .NET Core — Azure Dev Spaces

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Iteracyjne tworzenie kodu w kontenerach przy użyciu programu Visual Studio.
- Debuguj kod uruchomiony w klastrze przy użyciu programu Visual Studio.

Azure Dev Spaces umożliwia również debugowanie i iterację przy użyciu:
- [Java i Visual Studio Code](quickstart-java.md)
- [Node. js i Visual Studio Code](quickstart-nodejs.md)
- [.NET Core i Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- Program Visual Studio 2019 w systemie Windows z zainstalowanym obciążeniem programowania na platformie Azure. Możesz również użyć programu Visual Studio 2017 w systemie Windows z zainstalowanym obciążeniem programowaniem w sieci Web i [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools) . Jeśli nie masz zainstalowanego programu Visual Studio, Pobierz go [tutaj](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Azure Kubernetes Service

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Aby utworzyć klaster:

1. Zaloguj się do [Azure Portal](https://portal.azure.com)
1. Wybierz pozycję *+ Utwórz zasób > usługę Kubernetes*. 
1. Wprowadź _subskrypcję_, _grupę zasobów_, _nazwę klastra Kubernetes_, _region_, _wersję Kubernetes_i _prefiks nazwy DNS_.

    ![Utwórz AKS w Azure Portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Kliknij pozycję *Przegląd + utwórz*.
1. Kliknij przycisk *Utwórz*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie Azure Dev Spaces w klastrze AKS

Przejdź do klastra AKS w Azure Portal a następnie kliknij pozycję *Spaces*(Tworzenie). Zmień *wartość opcji* *Użyj spacji dev* na tak, a następnie kliknij przycisk *Zapisz*.

![Włącz funkcję Spaces dev w Azure Portal](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Tworzenie nowej aplikacji sieci Web ASP.NET

1. Otwórz program Visual Studio.
1. Tworzenie nowego projektu.
1. Wybierz *ASP.NET Core aplikacji sieci Web* i kliknij przycisk *dalej*.
1. Nadaj nazwę projekt *webfrontonu* , a następnie kliknij przycisk *Utwórz*.
1. Po wyświetleniu monitu wybierz pozycję *aplikacja sieci Web (Model-View-Controller)* dla szablonu.
1. Wybierz pozycję *.NET Core* i *ASP.NET Core 2,1* w górnej części strony.
1. Kliknij przycisk *Utwórz*.

## <a name="connect-your-project-to-your-dev-space"></a>Połącz projekt z obszarem deweloperskim

W projekcie wybierz **Azure dev Spaces** z listy rozwijanej ustawienia uruchamiania, jak pokazano poniżej.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

W oknie dialogowym Azure Dev Spaces wybierz *subskrypcję* i *klaster usługi Azure Kubernetes*. Pozostaw *miejsce* ustawione na *domyślne* i Włącz pole wyboru *dostępne publicznie* . Kliknij przycisk *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Ten proces służy do wdrażania usługi w *domyślnym* obszarze deweloperskim z publicznie dostępnym adresem URL. Jeśli wybierzesz klaster, który nie został skonfigurowany do pracy z usługą Azure Dev Spaces, zobaczysz komunikat z pytaniem, czy chcesz go skonfigurować. Kliknij przycisk *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Publiczny adres URL dla usługi uruchomionej w *domyślnym* obszarze dev jest wyświetlany w oknie *danych wyjściowych* :

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

W powyższym przykładzie publiczny adres URL to `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`. 

Wybierz pozycję **Debuguj** , a następnie **Rozpocznij debugowanie**. Po kilku sekundach zostanie uruchomiona usługa, a program Visual Studio otworzy przeglądarkę z publicznym adresem URL usługi. Jeśli przeglądarka nie jest automatycznie otwierana, przejdź do publicznego adresu URL usługi w przeglądarce i skontaktuj się z usługą uruchomioną w obszarze dev.

Ten proces mógł wyłączyć publiczny dostęp do usługi. Aby włączyć dostęp publiczny, można zaktualizować wartość transferu danych przychodzących [w *wartości. YAML*][ingress-update].

## <a name="update-code"></a>Aktualizowanie kodu

Jeśli program Visual Studio jest nadal połączony z obszarem deweloperskim, kliknij przycisk Zatrzymaj. Zmień wiersz 20 w `Controllers/HomeController.cs` na:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Zapisz zmiany i wybierz pozycję **Debuguj** , a następnie **Rozpocznij debugowanie**. Po kilku sekundach zostanie uruchomiona usługa, a program Visual Studio otworzy przeglądarkę z publicznym adresem URL usługi. Jeśli przeglądarka nie jest automatycznie otwierana, przejdź do publicznego adresu URL usługi w przeglądarce i kliknij pozycję *informacje*. Zwróć uwagę, że zostanie wyświetlony zaktualizowany komunikat.

Zamiast ponownie kompilować i wdrażać nowy obraz kontenera przy każdej modyfikacji kodu, Azure Dev Spaces przyrostowo kompiluje kod w istniejącym kontenerze, aby zapewnić szybszą pętlę edycji/debugowania.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ustawianie i używanie punktów przerwania do debugowania

Jeśli program Visual Studio jest nadal połączony z obszarem deweloperskim, kliknij przycisk Zatrzymaj. Otwórz `Controllers/HomeController.cs` i kliknij w dowolnym miejscu w wierszu 20, aby umieścić w nim kursor. Aby ustawić punkt przerwania, naciśnij klawisz *F9* lub kliknij pozycję *Debuguj* , a następnie *Przełącz punkt przerwania*. Aby uruchomić usługę w trybie debugowania w obszarze deweloperskim, naciśnij klawisz *F5* lub kliknij pozycję *Debuguj* , a następnie *Rozpocznij debugowanie*.

Otwórz usługę w przeglądarce i zwróć uwagę, że komunikat nie jest wyświetlany. Wróć do programu Visual Studio i obserwuj wiersz 20 został wyróżniony. Ustawiony punkt przerwania został wstrzymany usługi w wierszu 20. Aby wznowić działanie usługi, naciśnij klawisz *F5* lub kliknij pozycję *Debuguj* i *Kontynuuj*. Wróć do przeglądarki i Zauważ, że komunikat jest teraz wyświetlany.

Podczas uruchamiania usługi w Kubernetes z dołączonym debugerem masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne i informacje o wyjątku.

Usuń punkt przerwania, umieszczając kursor w wierszu 20 `Controllers/HomeController.cs` w i naciskając klawisz *F9*.

## <a name="clean-up-your-azure-resources"></a>Czyszczenie zasobów platformy Azure

Przejdź do grupy zasobów w Azure Portal a następnie kliknij pozycję *Usuń grupę zasobów*. Alternatywnie możesz użyć polecenia [AZ AKS Delete](/cli/azure/aks#az-aks-delete) :

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
