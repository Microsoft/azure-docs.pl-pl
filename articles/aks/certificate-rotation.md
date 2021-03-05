---
title: Obróć certyfikaty w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak obrócić certyfikaty w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/15/2019
ms.openlocfilehash: 1871a8deed4d189534915a9b46b6ace071c1126c
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181775"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Obróć certyfikaty w usłudze Azure Kubernetes Service (AKS)

Usługa Azure Kubernetes Service (AKS) używa certyfikatów do uwierzytelniania z wieloma składnikami. Okresowo może być konieczne obrócenie tych certyfikatów z przyczyn związanych z zabezpieczeniami lub zasadami. Na przykład może istnieć zasada, aby obrócić wszystkie certyfikaty co 90 dni.

W tym artykule pokazano, jak obrócić certyfikaty w klastrze AKS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.77 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS certyfikaty, urzędy certyfikacji i konta usług

AKS generuje i używa następujących certyfikatów, urzędów certyfikacji i kont usług:

* Serwer interfejsu API AKS tworzy urząd certyfikacji (CA) o nazwie Cluster-CA.
* Serwer interfejsu API ma urząd certyfikacji klastra, który podpisuje certyfikaty do jednokierunkowej komunikacji z serwera interfejsu API do kubelets.
* Każdy kubelet tworzy również żądanie podpisania certyfikatu (CSR) podpisane przez urząd certyfikacji klastra w celu komunikacji z kubelet z serwerem interfejsu API.
* Magazyn wartości klucza etcd ma certyfikat podpisany przez urząd certyfikacji klastra na potrzeby komunikacji z etcd z serwerem interfejsu API.
* Magazyn wartości klucza etcd tworzy urząd certyfikacji, który podpisuje certyfikaty w celu uwierzytelniania i autoryzacji replikacji danych między replikami etcd w klastrze AKS.
* Agregator interfejsów API używa urzędu certyfikacji klastra do wystawiania certyfikatów do komunikacji z innymi interfejsami API. Agregator API może również mieć własny urząd certyfikacji do wystawiania tych certyfikatów, ale obecnie używa urzędu certyfikacji klastra.
* W każdym węźle jest używany token konta usługi (SA), który jest podpisany przez urząd certyfikacji klastra.
* `kubectl`Klient ma certyfikat do komunikacji z klastrem AKS.

> [!NOTE]
> W przypadku klastrów AKS utworzonych przed Marzec 2019 istnieją certyfikaty, które wygasną po upływie dwóch lat. Każdy klaster utworzony po marcu 2019 lub dowolny klaster, który ma swoje certyfikaty, został obrócony, ma certyfikaty urzędu certyfikacji klastra, które wygasną po 30 latach. Wszystkie inne certyfikaty wygasną po upływie dwóch lat. Aby sprawdzić, kiedy klaster został utworzony, użyj, `kubectl get nodes` Aby zobaczyć *wiek* pul węzłów.
> 
> Ponadto możesz sprawdzić datę wygaśnięcia certyfikatu klastra. Na przykład następujące polecenie bash wyświetla szczegóły certyfikatu dla klastra *myAKSCluster* .
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

## <a name="rotate-your-cluster-certificates"></a>Obróć certyfikaty klastra

> [!WARNING]
> Obrócenie certyfikatów przy użyciu programu `az aks rotate-certs` może spowodować przestoje dla klastra AKS do 30 minut.

Użyj [AZ AKS Get-Credentials][az-aks-get-credentials] , aby zalogować się do klastra AKS. To polecenie pobiera również i konfiguruje `kubectl` certyfikat klienta na komputerze lokalnym.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Służy `az aks rotate-certs` do rotacji wszystkich certyfikatów, urzędów certyfikacji i sygnatury dostępu współdzielonego w klastrze.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Ukończenie tego procesu może potrwać do 30 minut `az aks rotate-certs` . Jeśli polecenie zakończy się niepowodzeniem przed ukończeniem, należy użyć `az aks show` do sprawdzenia stanu klastra jako *rotacji certyfikatu*. Jeśli klaster jest w stanie niepowodzenia, uruchom ponownie, `az aks rotate-certs` Aby ponownie obrócić certyfikaty.

Sprawdź, czy stare certyfikaty nie są już prawidłowe, uruchamiając `kubectl` polecenie. Ponieważ certyfikaty używane przez program nie zostały zaktualizowane `kubectl` , zostanie wyświetlony komunikat o błędzie.  Na przykład:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Zaktualizuj certyfikat używany przez program `kubectl` przez uruchomienie `az aks get-credentials` .

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Sprawdź, czy certyfikaty zostały zaktualizowane, uruchamiając `kubectl` polecenie, które zakończy się pomyślnie. Na przykład:

```console
kubectl get no
```

> [!NOTE]
> Jeśli masz jakiekolwiek usługi działające na AKS, takie jak [Azure dev Spaces][dev-spaces], może być konieczne [zaktualizowanie również certyfikatów związanych z tymi usługami][dev-spaces-rotate] .

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak automatycznie obrócić certyfikaty klastra, urzędy certyfikacji i sygnaturę dostępu współdzielonego. Aby uzyskać więcej informacji na temat najlepszych rozwiązań dotyczących zabezpieczeń [, Zobacz najlepsze rozwiązania dotyczące zabezpieczeń klastrów i uaktualnień w usłudze Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades] .


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: ../dev-spaces/index.yml
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
