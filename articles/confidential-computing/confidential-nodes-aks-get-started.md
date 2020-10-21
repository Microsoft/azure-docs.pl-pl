---
title: 'Szybki Start: Wdrażanie klastra usługi Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure z poufnymi węzłami obliczeniowymi'
description: Dowiedz się, jak utworzyć klaster AKS z węzłami poufnymi i wdrożyć aplikację Hello World przy użyciu interfejsu wiersza polecenia platformy Azure.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 994cf78a9a9b8c418d0f29f5d595f88f021659b4
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341910"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-using-azure-cli-preview"></a>Szybki Start: Wdrażanie klastra usługi Azure Kubernetes Service (AKS) z węzłami niejawnego przetwarzania za pomocą interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza)

Ten przewodnik Szybki Start jest przeznaczony dla deweloperów lub operatorów klastrów, którzy chcą szybko utworzyć klaster AKS i wdrożyć aplikację do monitorowania aplikacji przy użyciu usługi Managed Kubernetes na platformie Azure.

## <a name="overview"></a>Omówienie

W tym przewodniku szybki start dowiesz się, jak wdrożyć klaster usługi Azure Kubernetes Service (AKS) z węzłami poufnymi, korzystając z interfejsu wiersza polecenia platformy Azure i uruchomić aplikację Hello World w enklawy. AKS to zarządzana usługa Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. Więcej informacji na temat AKS można znaleźć [tutaj](https://docs.microsoft.com/azure/aks/intro-kubernetes).

> [!NOTE]
> Maszyny wirtualne służące do przetwarzania danych poufnych DCsv2 korzystają z wyspecjalizowanego sprzętu, który podlega wyższej cenie i dostępności regionów. Aby uzyskać więcej informacji, zobacz stronę maszyny wirtualne w obszarze [dostępne jednostki SKU i obsługiwane regiony](virtual-machine-solutions.md).

### <a name="deployment-pre-requisites"></a>Wymagania wstępne dotyczące wdrożenia

1. Mieć aktywną subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
1. Należy zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej na komputerze wdrożenia (Uruchom, `az --version` Aby znaleźć wersję. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli)
1. [AKS — minimalna wersja rozszerzenia w wersji zapoznawczej](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) 0.4.62 
1. Aby można było używać co najmniej sześciu rdzeni **kontrolera domeny <x> s-v2** w Twojej subskrypcji. Domyślnie przydziały maszyn wirtualnych są przydzielone do danych poufnych za korzystanie z subskrypcji platformy Azure 8. Jeśli planujesz obsługę klastra wymagającego więcej niż 8 rdzeni, postępuj zgodnie z [tymi](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) instrukcjami, aby zgłosić bilet wzrostu limitu przydziału

### <a name="confidential-computing-node-features-dcxs-v2"></a>Funkcje węzła poufnego obliczeń (DC <x> s-v2)

1. Węzły procesu roboczego systemu Linux obsługujące tylko kontenery systemu Linux
1. Ubuntu generacja 2 18,04 Virtual Machines
1. Procesor Intel SGX oparty na procesorze z zaszyfrowaną pamięcią podręczną strony (EPC). Przeczytaj więcej [tutaj](https://docs.microsoft.com/azure/confidential-computing/faq)
1. Kubernetes wersja 1.16 +
1. Wstępnie zainstalowany sterownik Intel SGX DCAP. Przeczytaj więcej [tutaj](https://docs.microsoft.com/azure/confidential-computing/faq)
1. Wdrożone przy użyciu interfejsu wiersza polecenia


## <a name="installing-the-cli-pre-requisites"></a>Instalowanie wymagań wstępnych interfejsu wiersza polecenia

Aby zainstalować rozszerzenie AKS-Preview 0.4.62 lub nowsze, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
Aby zaktualizować rozszerzenie interfejsu wiersza polecenia AKS-Preview, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az extension update --name aks-preview
```

Zarejestruj Gen2VMPreview:

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
Wyświetlenie stanu jako zarejestrowanego może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia "AZ Feature list":

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
Gdy stan jest wyświetlany jako zarejestrowane, Odśwież rejestrację dostawcy zasobów Microsoft. ContainerService za pomocą polecenia "AZ Provider Register":

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="creating-an-aks-cluster"></a>Tworzenie klastra AKS

Jeśli masz już klaster AKS, który spełnia powyższe wymagania, [Przejdź do istniejącej sekcji klastra](#existing-cluster) , aby dodać nową pulę węzłów w postaci poufnej.

Najpierw utwórz grupę zasobów dla klastra za pomocą polecenia AZ Group Create. Poniższy przykład *tworzy nazwę grupy zasobów w* regionie *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Teraz Utwórz klaster AKS za pomocą polecenia AZ AKS Create. Poniższy przykład tworzy klaster z pojedynczym węzłem rozmiaru `Standard_DC2s_v2` . Możesz wybrać inną obsługiwaną listę jednostek SKU DCsv2 z tego [miejsca](https://docs.microsoft.com/azure/virtual-machines/dcv2-series):

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_DC2s_v2 \
    --node-count 3 \
    --enable-addon confcom \
    --network-plugin azure \
    --vm-set-type VirtualMachineScaleSets \
    --aks-custom-headers usegen2vm=true
```
Powyższe polecenie powinno inicjować nowy klaster AKS z pulami węzłów **DC <x> s-v2** i automatycznie instalować dwa zestawy demonów — (Pomocnik[urządzenia SGX](confidential-nodes-aks-overview.md#sgx-plugin)  &  [SGX Helper](confidential-nodes-aks-overview.md#sgx-quote))

Pobierz poświadczenia dla klastra AKS za pomocą polecenia AZ AKS Get-Credentials:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Sprawdź, czy węzły zostały prawidłowo utworzone, a zestawy demonów SGX są uruchomione w pulach węzłów **DC <x> s-v2** przy użyciu polecenia polecenia kubectl & Get węzłów, jak pokazano poniżej:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Jeśli dane wyjściowe są zgodne z powyższym, wówczas klaster AKS jest teraz gotowy do uruchamiania poufnych aplikacji.

Przejdź do sekcji [Hello World from enklawy](#hello-world) Deployment, aby przetestować aplikację w enklawy. Lub postępuj zgodnie z poniższymi instrukcjami, aby dodać dodatkowe pule węzłów w AKS (AKS obsługuje mieszanie pul węzłów SGX i puli węzłów innych niż SGX)

>Jeśli zestawy SGX powiązane z usługą nie są zainstalowane w pulach węzłów DCSv2, uruchom poniższe polecenie.

```azurecli-interactive
az aks update --enable-addons confcom --resource-group myResourceGroup --name myAKSCluster
```

![Tworzenie klastra DCSv2 AKS](./media/confidential-nodes-aks-overview/CLIAKSProvisioning.gif)

## <a name="adding-confidential-computing-node-to-existing-aks-cluster"></a>Dodawanie poufnego węzła obliczeniowego do istniejącego klastra AKS<a id="existing-cluster"></a>

W tej sekcji założono, że klaster AKS działa już, który spełnia kryteria wymienione w sekcji wymagania wstępne.

Po pierwsze, program umożliwia włączanie poufnych dodatków AKS związanych z przetwarzaniem w istniejącym klastrze:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
Teraz Dodaj pulę węzłów **DC <x> s-v2** do klastra
    
> [!NOTE]
> Aby można było korzystać z możliwości przetwarzania poufnego, istniejący klaster AKS musi mieć co najmniej jedną pulę węzłów opartą na maszynie wirtualnej **z systemem DC <x> s-v2** . Dowiedz się więcej na temat poufnego przetwarzania jednostek SKU maszyn wirtualnych DCsv2 w tym miejscu [dostępne jednostki SKU i obsługiwane regiony](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
Dane wyjściowe powinny zawierać nowo dodane confcompool1 w klastrze AKS.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Jeśli dane wyjściowe są zgodne z powyższym, wówczas klaster AKS jest teraz gotowy do uruchamiania poufnych aplikacji.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello world z izolowanej aplikacji enklawy <a id="hello-world"></a>
Utwórz plik o nazwie *Hello-World-enklawy. YAML* i wklej następujący manifest YAML. Ten otwarty kod przykładowej aplikacji na podstawie enklawy można znaleźć w [otwartym projekcie enklawy](https://github.com/openenclave/openenclave/tree/master/samples/helloworld).

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



