---
title: Obracanie certyfikatów w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak obracać certyfikaty w klastrze Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: 6baad681a9d629c397c53ab90057cc5746fc3b85
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776019"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Obracanie certyfikatów w Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) używa certyfikatów do uwierzytelniania z wieloma jego składnikami. Okresowo może być konieczne obracanie tych certyfikatów ze względów bezpieczeństwa lub zasad. Na przykład możesz mieć zasady rotacji wszystkich certyfikatów co 90 dni.

W tym artykule pokazano, jak obracać certyfikaty w klastrze usługi AKS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.77 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certyfikaty usługi AKS, urzędy certyfikacji i konta usług

Usługa AKS generuje i używa następujących certyfikatów, urzędów certyfikacji i kont usług:

* Serwer interfejsu API usługi AKS tworzy urząd certyfikacji (CA) o nazwie urząd certyfikacji klastra.
* Serwer interfejsu API ma urząd certyfikacji klastra, który podpisuje certyfikaty do komunikacji jednokierunkowej z serwera interfejsu API do kubeletów.
* Każde kubelet tworzy również żądanie podpisania certyfikatu (CSR, Certificate Signing Request) podpisane przez urząd certyfikacji klastra w celu komunikacji z interfejsem kubelet do serwera interfejsu API.
* Agregator interfejsu API używa urzędu certyfikacji klastra do wydawania certyfikatów do komunikacji z innymi interfejsami API. Agregator interfejsu API może również mieć własny urząd certyfikacji do wystawiania tych certyfikatów, ale obecnie używa urzędu certyfikacji klastra.
* Każdy węzeł używa tokenu konta usługi (SA) podpisanego przez urząd certyfikacji klastra.
* Klient `kubectl` ma certyfikat do komunikacji z klastrem AKS.

> [!NOTE]
> Klastry usługi AKS utworzone przed marcem 2019 r. mają certyfikaty, które wygasają po dwóch latach. Każdy klaster utworzony po marcu 2019 r. lub dowolny klaster, który ma obracane certyfikaty, ma certyfikaty urzędu certyfikacji klastra, które wygasają po upływie 30 lat. Wszystkie inne certyfikaty wygasają po dwóch latach. Aby sprawdzić, kiedy klaster został utworzony, użyj właściwości `kubectl get nodes` , aby wyświetlić *wiek* pul węzłów.
> 
> Ponadto możesz sprawdzić datę wygaśnięcia certyfikatu klastra. Na przykład następujące polecenie powłoki Bash wyświetla szczegóły certyfikatu dla *klastra myAKSCluster.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>Obracanie certyfikatów klastra

> [!WARNING]
> Obrócenie certyfikatów przy użyciu narzędzia może `az aks rotate-certs` spowodować do 30 minut przestoju klastra usługi AKS.

Użyj [az aks get-credentials,][az-aks-get-credentials] aby zalogować się do klastra usługi AKS. To polecenie umożliwia również pobranie i skonfigurowanie `kubectl` certyfikatu klienta na komputerze lokalnym.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Użyj `az aks rotate-certs` funkcji , aby obrócić wszystkie certyfikaty, urzędu certyfikacji i skojarze usługi w klastrze.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Ukończenie tego procesu może potrwać do 30 `az aks rotate-certs` minut. Jeśli wykonanie polecenia zakończy się niepowodzeniem, użyj polecenia , aby sprawdzić stan `az aks show` klastra: *Rotacja certyfikatu.* Jeśli klaster jest w stanie awarii, ponownie spróbuj `az aks rotate-certs` obrócić certyfikaty.

Sprawdź, czy stare certyfikaty nie są już prawidłowe, uruchamiając `kubectl` polecenie . Ponieważ certyfikaty używane przez program nie zostały `kubectl` zaktualizowane, zostanie wyświetlony błąd.  Na przykład:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Zaktualizuj certyfikat używany przez `kubectl` program , uruchamiając program `az aks get-credentials` .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Sprawdź, czy certyfikaty zostały zaktualizowane, uruchamiając `kubectl` polecenie , które zakończy się pomyślnie. Na przykład:

```console
kubectl get no
```

> [!NOTE]
> Jeśli masz jakiekolwiek usługi, które działają w oparciu o usługę AKS, takie jak [Azure Dev Spaces][dev-spaces], może być również konieczne zaktualizowanie certyfikatów związanych z [tymi usługami.][dev-spaces-rotate]

## <a name="next-steps"></a>Następne kroki

W tym artykule popisano, jak automatycznie obracać certyfikaty, urzędu certyfikacji i skojarzeń. Aby uzyskać więcej informacji na temat najlepszych rozwiązań w zakresie zabezpieczeń usługi AKS, zobacz Najlepsze rozwiązania dotyczące zabezpieczeń i uaktualnień klastra w u Azure Kubernetes Service [(AKS).][aks-best-practices-security-upgrades]


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
