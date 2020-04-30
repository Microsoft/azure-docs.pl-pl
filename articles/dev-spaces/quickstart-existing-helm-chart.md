---
title: Opracowywanie aplikacji z istniejącym wykresem Helm na Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: W tym przewodniku szybki start pokazano, jak używać Azure Dev Spaces i wiersza polecenia do tworzenia aplikacji z istniejącym wykresem Helm w usłudze Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
manager: gwallace
ms.openlocfilehash: e767b1ade2a80882ee33ff1fdd718c691dcefcf3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82033564"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Szybki Start: Tworzenie aplikacji przy użyciu istniejącego wykresu Helm na Kubernetes-Azure Dev Spaces
Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Uruchom aplikację z istniejącym wykresem Helm w AKS przy użyciu Azure Dev Spaces w wierszu polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Azure Kubernetes Service

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Poniższe polecenia tworzą grupę zasobów o nazwie Moja *zasobów* i klaster AKS o nazwie *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie Azure Dev Spaces w klastrze AKS

Użyj `use-dev-spaces` polecenia, aby włączyć miejsca deweloperskie w klastrze AKS i postępuj zgodnie z monitami. Poniższe polecenie włącza miejsca deweloperskie w klastrze *MyAKS* w grupie Grupa *zasobów* i tworzy przestrzeń dev o nazwie *dev*.

> [!NOTE]
> `use-dev-spaces` Polecenie spowoduje również zainstalowanie interfejsu wiersza polecenia Azure dev Spaces, jeśli nie został jeszcze zainstalowany. Nie można zainstalować interfejsu wiersza polecenia Azure Dev Spaces w Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Pobierz przykładowy kod aplikacji

W tym artykule użyto [Azure dev Spaces przykładowej aplikacji](https://github.com/Azure/dev-spaces) do zademonstrowania przy użyciu Azure dev Spaces.

Sklonuj aplikację z witryny GitHub i przejdź do katalogu *dev-Spaces/Samples/Python/Start/webfrontonu* :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Przygotowywanie aplikacji

Aby można było uruchomić aplikację na Azure Dev Spaces, wymagany jest wykres pliku dockerfile i Helm. W przypadku niektórych języków, takich jak [Java][java-quickstart], [.NET Core][netcore-quickstart]i [Node. js][nodejs-quickstart], narzędzia klienta Azure dev Spaces mogą generować wszystkie potrzebne zasoby. W przypadku wielu innych języków, takich jak go, PHP i Python, narzędzia klienckie mogą generować wykres Helm, o ile można podać prawidłowy pliku dockerfile. W takim przypadku Przykładowa aplikacja zawiera istniejący wykres pliku dockerfile i Helm

Generuj konfigurację uruchamiania aplikacji z Azure Dev Spaces z istniejącym wykresem Helm i pliku dockerfile za pomocą `azds prep` polecenia:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

Należy uruchomić `prep` polecenie z katalogu *dev-Spaces/Samples/Python/Start/webfrontonu* i określić lokalizację wykresu Helm przy użyciu `--chart`polecenia.

> [!NOTE]
> Może zostać wyświetlone ostrzeżenie: *Ostrzeżenie: pliku dockerfile nie może zostać wygenerowany z powodu nieobsługiwanego języka.* podczas uruchamiania `azds prep`. `azds prep` Polecenie podejmuje próbę wygenerowania [wykresu pliku dockerfile i Helm](how-dev-spaces-works-prep.md#prepare-your-code) dla projektu, ale nie spowoduje zastąpienia żadnych istniejących wykresów wieloetapowe dockerfile lub Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Kompilowanie i uruchamianie kodu na platformie Kubernetes

Kompiluj i uruchamiaj kod w AKS przy użyciu `azds up` polecenia:

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

Możesz zobaczyć, że usługa jest uruchomiona, otwierając publiczny adres URL, który jest wyświetlany w danych wyjściowych `azds up` polecenia. W tym przykładzie publiczny adres URL to *http://dev.service.1234567890abcdef1234.eus.azds.io/*.

> [!NOTE]
> Gdy przejdziesz do usługi w trakcie `azds up`działania, w danych wyjściowych `azds up` polecenia są również wyświetlane dane śledzenia żądań HTTP. Te ślady mogą pomóc w rozwiązywaniu problemów i debugowaniu usługi. Te ślady można wyłączyć przy `--disable-http-traces` użyciu programu `azds up`podczas uruchamiania.

Jeśli zatrzymasz `azds up` polecenie za pomocą *klawiszy CTRL + c*, usługa będzie nadal działać w AKS, a publiczny adres URL pozostanie dostępny.

## <a name="update-code"></a>Aktualizowanie kodu

Aby wdrożyć zaktualizowaną wersję usługi, możesz zaktualizować dowolny plik w projekcie i ponownie uruchomić `azds up` polecenie. Przykład:

1. Jeśli `azds up` nadal działa, naciśnij *klawisze CTRL + c*.
1. Aktualizuj [wiersz 13 w `webfrontend.py` programie](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) do:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Zapisz zmiany.
1. Uruchom ponownie `azds up` polecenie:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Przejdź do działającej usługi i obserwuj wprowadzone zmiany.
1. Naciśnij *klawisze CTRL + c* , aby `azds up` zatrzymać polecenie.

## <a name="clean-up-your-azure-resources"></a>Czyszczenie zasobów platformy Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak Azure Dev Spaces ułatwiają tworzenie bardziej złożonych aplikacji w wielu kontenerach i jak można uprościć programowanie do współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych miejscach.

> [!div class="nextstepaction"]
> [Programowanie zespołowe w Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service