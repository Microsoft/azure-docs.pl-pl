---
title: Tworzenie aplikacji z istniejącym wykresem Helm na Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: Ten przewodnik Szybki start pokazuje, jak używać usługi Azure Dev Spaces i wiersza polecenia do tworzenia aplikacji z istniejącym wykresem Helm w usłudze Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s
manager: gwallace
ms.openlocfilehash: e767b1ade2a80882ee33ff1fdd718c691dcefcf3
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82033564"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Szybki start: tworzenie aplikacji z istniejącym wykresem Helm na platformie Kubernetes — Azure Dev Spaces
Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Uruchom aplikację z istniejącym wykresem Helm w usłudze AKS przy użyciu usługi Azure Dev Spaces w wierszu polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Kubernetes platformy Azure

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Poniższe polecenia tworzą grupę zasobów o nazwie *MyResourceGroup* i klaster AKS o nazwie *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie usług Azure Dev Spaces w klastrze usługi AKS

Użyj `use-dev-spaces` tego polecenia, aby włączyć miejsca dewelopera w klastrze AKS i postępować zgodnie z instrukcjami. Poniższe polecenie włącza miejsca dewelopera w klastrze *MyAKS* w grupie *MyResourceGroup* i tworzy przestrzeń deweloperów o nazwie *dev*.

> [!NOTE]
> Polecenie `use-dev-spaces` zainstaluje również interfejsu wiersza polecenia azure dev spaces, jeśli nie jest jeszcze zainstalowany. Nie można zainstalować interfejsu wiersza polecenia usługi Azure Dev Spaces w usłudze Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Pobierz przykładowy kod aplikacji

W tym artykule używasz [przykładowej aplikacji Azure Dev Spaces,](https://github.com/Azure/dev-spaces) aby zademonstrować przy użyciu usługi Azure Dev Spaces.

Sklonuj aplikację z gitHub i przejdź do katalogu *dev-spaces/samples/python/getting-started/webfrontend:*

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Przygotowanie aplikacji

Aby uruchomić aplikację w usłudze Azure Dev Spaces, potrzebujesz wykresu Dockerfile i Helm. W przypadku niektórych języków, takich jak [Java][java-quickstart], [.NET core][netcore-quickstart]i [Node.js][nodejs-quickstart], narzędzia klienta usługi Azure Dev Spaces mogą generować wszystkie potrzebne zasoby. W wielu innych językach, takich jak Go, PHP i Python, narzędzie klienta może generować wykres Helm, o ile można podać prawidłowy plik Dockerfile. W takim przypadku przykładowa aplikacja ma istniejący wykres Dockerfile i Helm

Wygeneruj konfigurację uruchamiania aplikacji za pomocą usługi Azure Dev `azds prep` Spaces przy użyciu istniejącego wykresu Helm i pliku dockerfile za pomocą polecenia:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

Polecenie należy `prep` uruchomić z katalogu *dev-spaces/samples/python/getting-started/webfrontend* i określić lokalizację `--chart`wykresu Helm za pomocą .

> [!NOTE]
> Może zostać wyświetlone ostrzeżenie: *OSTRZEŻENIE: Nie można wygenerować pliku dockerfile z powodu nieobsługiwał języka.* podczas `azds prep`pracy . Polecenie `azds prep` próbuje wygenerować [wykres Dockerfile i Helm](how-dev-spaces-works-prep.md#prepare-your-code) dla projektu, ale nie zastąpi żadnych istniejących dockerfiles lub wykresów helm.

## <a name="build-and-run-code-in-kubernetes"></a>Kompilowanie i uruchamianie kodu na platformie Kubernetes

Skompiluj i uruchom kod `azds up` w aks za pomocą polecenia:

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

Możesz zobaczyć uruchomione usługi, otwierając publiczny adres URL, który `azds up` jest wyświetlany w danych wyjściowych z polecenia. W tym przykładzie publiczny *http://dev.service.1234567890abcdef1234.eus.azds.io/* adres URL to .

> [!NOTE]
> Po przejściu do usługi `azds up`podczas uruchamiania, ślady żądań HTTP `azds up` są również wyświetlane w danych wyjściowych polecenia. Te ślady mogą pomóc w rozwiązywaniu problemów i debugowaniu usługi. Można wyłączyć te ślady przy użyciu `--disable-http-traces` podczas uruchamiania `azds up`.

Jeśli `azds up` zatrzymasz polecenie przy użyciu *klawiszy Ctrl+c,* usługa będzie nadal działać w usłudze AKS, a publiczny adres URL pozostanie dostępny.

## <a name="update-code"></a>Aktualizowanie kodu

Aby wdrożyć zaktualizowaną wersję usługi, można zaktualizować dowolny plik `azds up` w projekcie i ponownie uruchomić polecenie. Przykład:

1. Jeśli `azds up` nadal działa, naciśnij *klawisze Ctrl+c*.
1. Aktualizacja [wiersza 13 w: `webfrontend.py` ](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13)
    
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

1. Przejdź do uruchomionej usługi i obserwuj zmiany.
1. Naciśnij *klawisze Ctrl+c,* aby zatrzymać `azds up` polecenie.

## <a name="clean-up-your-azure-resources"></a>Oczyszczanie zasobów platformy Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć tworzenie współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych przestrzeniach.

> [!div class="nextstepaction"]
> [Tworzenie zespołów w usłudze Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service