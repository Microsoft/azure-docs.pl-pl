---
title: Uwierzytelnianie z klastra Kubernetes
description: Dowiedz się, jak zapewnić klaster Kubernetes z dostępem do obrazów w rejestrze kontenerów platformy Azure przez utworzenie klucza tajnego przy użyciu nazwy głównej usługi
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77154896"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Ściąganie obrazów z usługi Azure Container Registry do klastra Kubernetes

Usługi Azure Container Registry można użyć jako źródła obrazów kontenerów z dowolnym klastrem Kubernetes, w tym z lokalnymi klastrami Kubernetes, takimi jak [minikube](https://minikube.sigs.k8s.io/) i [rodzaj](https://kind.sigs.k8s.io/). W tym artykule pokazano, jak utworzyć wpis tajny Kubernetes na podstawie nazwy głównej usługi Azure Active Directory. Następnie użyj klucza tajnego do ściągania obrazów z usługi Azure Container Registry w ramach wdrożenia Kubernetes.

> [!TIP]
> Jeśli używasz zarządzanej [usługi Azure Kubernetes](../aks/intro-kubernetes.md), możesz także [zintegrować klaster](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) z docelowym rejestrem kontenerów platformy Azure na potrzeby ściągania obrazów. 

W tym artykule przyjęto założenie, że został już utworzony prywatny rejestr kontenerów platformy Azure. Należy również mieć uruchomiony klaster Kubernetes, który jest dostępny za pomocą narzędzia `kubectl` wiersza polecenia.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Jeśli nie zapiszesz lub nie pamiętasz hasła nazwy głównej usługi, możesz zresetować je za pomocą polecenia [AZ AD Sp Credential Reset][az-ad-sp-credential-reset] :

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

To polecenie zwraca nowe, prawidłowe hasło dla nazwy głównej usługi.

## <a name="create-an-image-pull-secret"></a>Utwórz klucz tajny ściągania obrazu

Kubernetes używa *hasła ściągania obrazu* do przechowywania informacji niezbędnych do uwierzytelnienia w rejestrze. Aby utworzyć klucz tajny ściągania dla rejestru kontenerów platformy Azure, podaj identyfikator jednostki usługi, hasło i adres URL rejestru. 

Utwórz klucz tajny obrazu za pomocą następującego `kubectl` polecenia:

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
gdzie:

| Wartość | Opis |
| :--- | :--- |
| `secret-name` | Nazwa hasła ściągania obrazu, na przykład *ACR-Secret* |
| `namespace` | Kubernetes przestrzeń nazw, w której ma zostać umieszczony klucz tajny <br/> Wymagane tylko, jeśli chcesz umieścić klucz tajny w przestrzeni nazw innej niż domyślna przestrzeń nazw |
| `container-registry-name` | Nazwa usługi Azure Container Registry |
| `service-principal-ID` | Identyfikator jednostki usługi, która będzie używana przez Kubernetes do uzyskiwania dostępu do rejestru |
| `service-principal-password` | Hasło nazwy głównej usługi |

## <a name="use-the-image-pull-secret"></a>Korzystanie z klucza tajnego ściągania obrazu

Po utworzeniu wpisu tajnego obrazu można go użyć do utworzenia Kubernetes i wdrożeń. Podaj nazwę wpisu tajnego `imagePullSecrets` w pliku wdrożenia. Przykład:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

W poprzednim przykładzie jest nazwą `your-awesome-app:v1` obrazu, który ma zostać pobrany z usługi Azure Container Registry, a `acr-secret` to nazwa klucza tajnego ściągnięcia utworzonego w celu uzyskania dostępu do rejestru. Po wdrożeniu programu pod warunkiem program Kubernetes automatycznie pobiera obraz z rejestru, jeśli nie jest jeszcze obecny w klastrze.


## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat pracy z jednostkami usług i Azure Container Registry, zobacz [Azure Container Registry Authentication z](container-registry-auth-service-principal.md) jednostkami usługi
* Dowiedz się więcej na temat wpisów tajnych ściągania obrazów w [dokumentacji Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset