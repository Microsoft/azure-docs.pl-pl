---
title: 'Szybki Start: Wdrażanie klastra usługi Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure z poufnymi węzłami obliczeniowymi'
description: Dowiedz się, jak utworzyć klaster AKS z węzłami poufnymi i wdrożyć aplikację Hello World przy użyciu interfejsu wiersza polecenia platformy Azure.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/25/2020
ms.author: amgowda
ms.openlocfilehash: 51b0813849236d9335d1482019f740fc8b23749f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703290"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Szybki Start: Wdrażanie klastra usługi Azure Kubernetes Service (AKS) z węzłami w postaci poufnego przetwarzania danych (DCsv2) przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przewodnik Szybki Start jest przeznaczony dla deweloperów lub operatorów klastrów, którzy chcą szybko utworzyć klaster AKS i wdrożyć aplikację do monitorowania aplikacji przy użyciu usługi Managed Kubernetes na platformie Azure. Można również zainicjować obsługę administracyjną klastra i dodać poufne węzły obliczeniowe z Azure Portal.

## <a name="overview"></a>Omówienie

W tym przewodniku szybki start dowiesz się, jak wdrożyć klaster usługi Azure Kubernetes Service (AKS) z węzłami poufnymi, korzystając z interfejsu wiersza polecenia platformy Azure i uruchamiając prostą aplikację Hello World w enklawy. AKS to zarządzana usługa Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. Więcej informacji na temat AKS można znaleźć [tutaj](../aks/intro-kubernetes.md).

> [!NOTE]
> Maszyny wirtualne służące do przetwarzania danych poufnych DCsv2 korzystają z wyspecjalizowanego sprzętu, który podlega wyższej cenie i dostępności regionów. Aby uzyskać więcej informacji, zobacz stronę maszyny wirtualne w obszarze [dostępne jednostki SKU i obsługiwane regiony](virtual-machine-solutions.md).

### <a name="confidential-computing-node-features-dcxs-v2"></a>Funkcje węzła poufnego obliczeń (DC <x> s-v2)

1. Węzły procesu roboczego systemu Linux obsługujące kontenery systemu Linux
1. Maszyna wirtualna 2. generacji z Ubuntu 18,04 Virtual Machines węzłami
1. Procesor Intel SGX oparty na procesorze z zaszyfrowaną pamięcią podręczną strony (EPC). Przeczytaj więcej [tutaj](./faq.md)
1. Obsługa Kubernetes w wersji 1.16 +
1. Sterownik Intel SGX DCAP został wstępnie zainstalowany na węzłach AKS. Przeczytaj więcej [tutaj](./faq.md)

## <a name="deployment-prerequisites"></a>Wymagania wstępne dotyczące wdrożenia
Samouczek wdrażania wymaga poniższego:

1. Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
1. Interfejs wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej został zainstalowany i skonfigurowany na komputerze wdrożenia (Uruchom, `az --version` Aby znaleźć wersję. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](../container-registry/container-registry-get-started-azure-cli.md)
1. Co najmniej sześć rdzeni **DC <x> s-v2** dostępnych w Twojej subskrypcji do użycia. Domyślnie przydziały maszyn wirtualnych są przydzielone do danych poufnych za korzystanie z subskrypcji platformy Azure 8. Jeśli planujesz obsługę klastra wymagającego więcej niż 8 rdzeni, postępuj zgodnie z [tymi](../azure-portal/supportability/per-vm-quota-requests.md) instrukcjami, aby zgłosić bilet wzrostu limitu przydziału

## <a name="creating-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Tworzenie nowego klastra AKS z poufnymi węzłami obliczeniowymi i dodatkiem
Postępuj zgodnie z poniższymi instrukcjami, aby dodać poufne węzły z obsługą obliczeniową z dodatkiem.

### <a name="step-1-creating-an-aks-cluster-with-system-node-pool"></a>Krok 1. Tworzenie klastra AKS z pulą węzłów systemu

Jeśli masz już klaster AKS, który spełnia powyższe wymagania, [Przejdź do istniejącej sekcji klastra](#existing-cluster) , aby dodać nową pulę węzłów w postaci poufnej.

Najpierw utwórz grupę zasobów dla klastra za pomocą polecenia AZ Group Create. Poniższy przykład *tworzy nazwę grupy zasobów w* regionie *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Teraz Utwórz klaster AKS za pomocą polecenia AZ AKS Create.

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
Powyżej zostanie utworzony nowy klaster AKS z pulą węzłów systemu z włączonym dodatkiem. Teraz Dodaj węzeł użytkownika poufnego typu Nodepool na AKS (DCsv2)

### <a name="step-2-adding-confidential-computing-node-pool-to-aks-cluster"></a>Krok 2: dodanie poufnej puli węzłów obliczeniowych do klastra AKS 

Uruchom poniższe polecenie, aby nodepool użytkownika o `Standard_DC2s_v2` rozmiarze z 3 węzłami. W [tym miejscu](../virtual-machines/dcv2-series.md)możesz wybrać inną obsługiwaną listę jednostek SKU i regionów DCsv2:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```
Powyższe polecenie powoduje zakończenie nowej puli węzłów z **kontrolerem domeny <x> s-v2** z opcją poufnego przetwarzania daemonsets ([SGX](confidential-nodes-aks-overview.md#sgx-plugin)
 
### <a name="step-3-verify-the-node-pool-and-add-on"></a>Krok 3. Weryfikowanie puli węzłów i dodatku
Pobierz poświadczenia dla klastra AKS za pomocą polecenia AZ AKS Get-Credentials:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Upewnij się, że węzły zostały prawidłowo utworzone, a daemonsets powiązane z SGX są uruchomione w puli węzłów **DC <x> s-v2** przy użyciu polecenia polecenia kubectl & Get węzłów, jak pokazano poniżej:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Jeśli dane wyjściowe są zgodne z powyższym, wówczas klaster AKS jest teraz gotowy do uruchamiania poufnych aplikacji.

Przejdź do sekcji [Hello World from enklawy](#hello-world) Deployment, aby przetestować aplikację w enklawy. Lub postępuj zgodnie z poniższymi instrukcjami, aby dodać dodatkowe pule węzłów w AKS (AKS obsługuje łączenie pul węzłów SGX i innych niż SGX pul węzłów)

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Dodawanie poufnej puli węzłów obliczeniowych do istniejącego klastra AKS<a id="existing-cluster"></a>

W tej sekcji założono, że klaster AKS, który jest już uruchomiony, spełnia kryteria wymienione w sekcji wymagania wstępne (dotyczy dodatku).

### <a name="step-1-enabling-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Krok 1. Włączanie dodatku AKS do przetwarzania poufnego w istniejącym klastrze

Uruchom poniższe polecenie, aby włączyć dodatek do przetwarzania poufnego

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
### <a name="step-2-add-dcxs-v2-user-node-pool-to-the-cluster"></a>Krok 2. Dodawanie puli węzłów użytkownika **<x> s-v2 DC** do klastra
    
> [!NOTE]
> Aby można było korzystać z możliwości przetwarzania poufnego, istniejący klaster AKS musi mieć co najmniej jedną pulę węzłów opartą na maszynie wirtualnej **z systemem DC <x> s-v2** . Dowiedz się więcej na temat poufnego przetwarzania jednostek SKU maszyn wirtualnych DCsv2 w tym miejscu [dostępne jednostki SKU i obsługiwane regiony](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```
Powyższe polecenie powinno wyświetlić listę ostatnio dodanej puli węzłów o nazwie confcompool1.

### <a name="step-3-verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Krok 3. sprawdzenie, czy daemonsets są uruchomione na poufnych pulach węzłów

Zaloguj się do istniejącego klastra AKS, aby wykonać poniższą weryfikację. 

```console
kubectl get nodes
```
Dane wyjściowe powinny zawierać nowo dodane confcompool1 w klastrze AKS.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Jeśli dane wyjściowe są zgodne z powyższym, wówczas klaster AKS jest teraz gotowy do uruchamiania poufnych aplikacji. Postępuj zgodnie z poniższymi wdrożeniami aplikacji testowej.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello world z izolowanej aplikacji enklawy <a id="hello-world"></a>
Utwórz plik o nazwie *Hello-World-enklawy. YAML* i wklej następujący manifest YAML. Ten otwarty kod przykładowej aplikacji na podstawie enklawy można znaleźć w [otwartym projekcie enklawy](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). W poniższym wdrożeniu przyjęto założenie, że wdrożono dodatek "confcom".

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Teraz użyj polecenia polecenia kubectl Apply, aby utworzyć przykładowe zadanie, które zostanie uruchomione w zabezpieczonym enklawy, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Można potwierdzić, że obciążenie pomyślnie utworzyło zaufane środowisko wykonawcze (enklawy), uruchamiając następujące polecenia:

```console
$ kubectl get jobs -l app=sgx-test
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć skojarzone pule węzłów lub usunąć klaster AKS, Użyj poniższych poleceń:

Usuwanie klastra AKS
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Następne kroki

Uruchom Język Python, węzeł itd. Aplikacje poufne z wykorzystaniem kontenerów poufnych przez odwiedzenie [przykładów kontenera poufnego](https://github.com/Azure-Samples/confidential-container-samples).

Uruchom aplikacje obsługujące enklawy, odwiedzając [przykłady kontenerów platformy Azure z obsługą enklawy](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).
