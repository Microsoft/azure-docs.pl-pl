---
title: Interakcja z kontenerami systemu Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Dowiedz się, jak uruchamiać Azure Dev Spaces w istniejącym klastrze z kontenerami systemu Windows
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery, kontenery systemu Windows
ms.openlocfilehash: bbef5eafe44e38691327714c14c6a6026d45a3c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777441"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interakcja z kontenerami systemu Windows przy użyciu Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Możesz włączyć obsługę Azure Dev Spaces w nowych i istniejących przestrzeniach nazw Kubernetes. Azure Dev Spaces uruchamiać i instrumentować usługi, które działają w kontenerach systemu Linux. Te usługi mogą również wchodzić w interakcje z aplikacjami, które działają w kontenerach systemu Windows w tej samej przestrzeni nazw. W tym artykule pokazano, jak używać usługi Dev Spaces do uruchamiania usług w przestrzeni nazw z istniejącymi kontenerami systemu Windows. Obecnie nie można debugować ani dołączać kontenerów systemu Windows za pomocą Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Konfigurowanie klastra

W tym artykule przyjęto założenie, że masz już klaster z pulami węzłów systemów Linux i Windows. Jeśli musisz utworzyć klaster z pulami węzłów systemu Linux i Windows, możesz wykonać instrukcje w [tym miejscu.][windows-container-cli]

Połącz się z klastrem przy użyciu [narzędzia kubectl][kubectl], klienta wiersza polecenia kubernetes. Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```azurecli-interactive
kubectl get nodes
```

Poniższe przykładowe dane wyjściowe pokazują klaster z węzłem systemów Windows i Linux. Przed podjęciem czynności upewnij *się,* że stan jest gotowy dla każdego węzła.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Zastosuj [łańcę][using-taints] do węzłów systemu Windows. Taint w węzłach systemu Windows uniemożliwia użytą przez usługę Dev Spaces planowanie kontenerów systemu Linux do uruchamiania w węzłach systemu Windows. Następujące polecenie przykładowe polecenie powoduje zastosowanie awarii do węzła systemu Windows *aksnpwin987654* z poprzedniego przykładu.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Po zastosowaniu awarii do węzła należy skonfigurować pasującą tolerancję w szablonie wdrożenia usługi, aby uruchomić usługę w tym węźle. Przykładowa aplikacja jest już [][sample-application-toleration-example] skonfigurowana przy użyciu tolerancji zgodnej z wartością skonfigurowaną w poprzednim poleceniu.

## <a name="run-your-windows-service"></a>Uruchamianie usługi systemu Windows

Uruchom usługę systemu Windows w klastrze usługi AKS i sprawdź, czy jest ona w *stanie* Uruchomiony. W tym artykule użyto [przykładowej aplikacji do][sample-application] zademonstrowania usługi systemu Windows i Linux działającej w klastrze.

Sklonuj przykładową aplikację z usługi GitHub i przejdź do `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` katalogu:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

Przykładowa aplikacja używa [programu Helm 3][helm-installed] do uruchamiania usługi systemu Windows w klastrze. Przejdź do katalogu `charts` i użyj programu Helm, aby uruchomić usługę systemu Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

Powyższe polecenie używa programu Helm do uruchomienia usługi systemu Windows w przestrzeni *nazw dev.* Jeśli nie masz przestrzeni nazw o nazwie *dev*, zostanie ona utworzona.

Użyj polecenia `kubectl get pods` , aby sprawdzić, czy usługa systemu Windows jest uruchomiona w klastrze. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Włącz Azure Dev Spaces

Włącz usługę Dev Spaces w tej samej przestrzeni nazw, która jest używana do uruchamiania usługi systemu Windows. Następujące polecenie włącza usługę Dev Spaces w przestrzeni *nazw dev:*

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Aktualizowanie usługi systemu Windows dla usługi Dev Spaces

Po włączeniu usługi Dev Spaces w istniejącej przestrzeni nazw z kontenerami, które są już uruchomione, domyślnie usługi Dev Spaces będą próbować i instrumentować wszystkie nowe kontenery działające w tej przestrzeni nazw. Usługa Dev Spaces spróbuje również instrumentować wszystkie nowe kontenery utworzone dla usługi działającej już w przestrzeni nazw . Aby zapobiec instrumentowania kontenera uruchomionego w przestrzeni nazw usługi Dev Spaces, dodaj nagłówek *no-proxy* do elementu `deployment.yaml` .

Dodaj `azds.io/no-proxy: "true"` do `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` pliku:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Użyj `helm list` , aby wyświetlić listę wdrożeń dla usługi systemu Windows:

```cmd
$ helm list --namespace dev
NAME             REVISION   UPDATED                    STATUS    CHART            APP VERSION    NAMESPACE
windows-service    1        Wed Jul 24 15:45:59 2019   DEPLOYED  mywebapi-0.1.0   1.0            dev
```

W powyższym przykładzie nazwa wdrożenia to *windows-service*. Zaktualizuj usługę systemu Windows przy użyciu nowej konfiguracji przy użyciu programu `helm upgrade` :

```cmd
helm upgrade windows-service . --namespace dev
```

Ponieważ zaktualizowano usługę `deployment.yaml` , usługa Dev Spaces nie będzie próbowała instrumentować usługi.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Uruchamianie aplikacji systemu Linux przy użyciu Azure Dev Spaces

Przejdź do katalogu `webfrontend` i użyj poleceń i , aby uruchomić aplikację systemu Linux w `azds prep` `azds up` klastrze.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

Polecenie `azds prep --enable-ingress` generuje pakiet Helm i pliki Dockerfile dla aplikacji.

> [!TIP]
> Plik [Dockerfile](../how-dev-spaces-works-prep.md#prepare-your-code) i pakiet Helm dla projektu są używane przez program Azure Dev Spaces do kompilowania i uruchamiania kodu, ale możesz modyfikować te pliki, jeśli chcesz zmienić sposób kompilowania i uruchamiania projektu.

Polecenie `azds up` uruchamia usługę w przestrzeni nazw .

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Usługę można wyświetlić, otwierając publiczny adres URL, który jest wyświetlany w danych wyjściowych polecenia azds up. W tym przykładzie publiczny adres URL to `http://dev.webfrontend.abcdef0123.eus.azds.io/` . Przejdź do usługi w przeglądarce i kliknij pozycję *Informacje* u góry. Sprawdź, czy jest wyświetlany komunikat z usługi *mywebapi* zawierający wersję systemu Windows używaną przez kontener.

![Przykładowa aplikacja przedstawiająca wersję systemu Windows z witryny mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, Azure Dev Spaces działa.

> [!div class="nextstepaction"]
> [Jak działa usługa Azure Dev Spaces](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#setting-nodepool-taints
[windows-container-cli]: ../../aks/windows-container-cli.md
