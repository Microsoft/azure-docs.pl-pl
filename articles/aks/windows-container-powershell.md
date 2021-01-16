---
title: Tworzenie kontenera systemu Windows Server w klastrze AKS przy użyciu programu PowerShell
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes, wdrożyć aplikację w kontenerze systemu Windows Server w usłudze Azure Kubernetes Service (AKS) przy użyciu programu PowerShell.
services: container-service
ms.topic: article
ms.date: 05/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 97741423fa8b689a92bd9db78b810e6b86aefcbd
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247118"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>Tworzenie kontenera systemu Windows Server w klastrze usługi Azure Kubernetes Service (AKS) przy użyciu programu PowerShell

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym artykule opisano wdrażanie klastra AKS przy użyciu programu PowerShell. Możesz również wdrożyć `ASP.NET` przykładową aplikację w kontenerze systemu Windows Server w klastrze.

![Obraz przedstawiający przeglądanie do aplikacji przykładowej ASP.NET](media/windows-container-powershell/asp-net-sample-app.png)

W tym artykule założono podstawową wiedzę na temat koncepcji Kubernetes. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell][install-azure-powershell]. Należy również zainstalować moduł programu PowerShell AZ. AKS: 

```azurepowershell-interactive
Install-Module Az.Aks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określony identyfikator subskrypcji za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>Ograniczenia

Następujące ograniczenia są stosowane podczas tworzenia klastrów AKS i zarządzania nimi, które obsługują pule wielu węzłów:

* Nie można usunąć pierwszej puli węzłów.

Następujące dodatkowe ograniczenia mają zastosowanie do pul węzłów systemu Windows Server:

* Klaster AKS może mieć maksymalnie 10 pul węzłów.
* Klaster AKS może mieć maksymalnie 100 węzłów w każdej puli węzłów.
* Nazwa puli węzłów systemu Windows Server ma limit 6 znaków.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[Grupa zasobów platformy Azure](../azure-resource-manager/management/overview.md) to grupa logiczna, w której zasoby platformy Azure są wdrażane i zarządzane. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. Ta lokalizacja wskazuje, gdzie są przechowywane metadane grupy zasobów, a także czy zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobów. Utwórz grupę zasobów za pomocą polecenia cmdlet [New-AzResourceGroup][new-azresourcegroup] .

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie **myResourceGroup** w lokalizacji **eastus**.

> [!NOTE]
> W tym artykule użyto składni programu PowerShell dla poleceń w tym samouczku. Jeśli używasz Azure Cloud Shell, upewnij się, że lista rozwijana w lewym górnym rogu okna Cloud Shell jest ustawiona na **PowerShell**.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Następujące przykładowe dane wyjściowe przedstawiają pomyślnie utworzoną grupę zasobów:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Użyj `ssh-keygen` narzędzia wiersza polecenia, aby wygenerować parę kluczy SSH. Aby uzyskać więcej informacji, zobacz [szybkie kroki: Tworzenie i używanie pary kluczy publiczny-prywatny SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

Aby uruchomić klaster AKS, który obsługuje pule węzłów dla kontenerów systemu Windows Server, klaster musi używać zasad sieciowych, które korzystają z wtyczki sieciowej [Azure CNI][azure-cni-about] (Advanced). Aby uzyskać bardziej szczegółowe informacje ułatwiające planowanie wymaganych zakresów podsieci i zagadnień dotyczących sieci, zobacz [Konfigurowanie usługi Azure CNI Networking][use-advanced-networking]. Aby utworzyć klaster AKS o nazwie **myAKSCluster**, użyj poniższego polecenia cmdlet [New-AzAks][new-azaks] . Poniższy przykład tworzy niezbędne zasoby sieciowe, jeśli nie istnieją.

> [!NOTE]
> Aby zapewnić niezawodne działanie klastra, należy uruchomić co najmniej 2 (dwa) węzły w domyślnej puli węzłów.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -KubernetesVersion 1.16.7 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName akswinuser -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> Jeśli nie możesz utworzyć klastra AKS, ponieważ wersja nie jest obsługiwana w tym regionie, możesz użyć `Get-AzAksVersion -Location eastus` polecenia, aby znaleźć listę obsługiwanych wersji dla tego regionu.

Po kilku minutach polecenie zostanie zakończone i zwróci informacje o klastrze. Czasami klaster może trwać dłużej niż kilka minut. W takich przypadkach Zezwalaj na maksymalnie 10 minut.

## <a name="add-a-windows-server-node-pool"></a>Dodawanie puli węzłów systemu Windows Server

Domyślnie klaster AKS jest tworzony przy użyciu puli węzłów, która może uruchamiać kontenery systemu Linux. Użyj `New-AzAksNodePool` polecenia cmdlet, aby dodać pulę węzłów, która może uruchamiać kontenery systemu Windows Server wraz z pulą węzłów Linux.

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -OsType Windows -Name npwin -KubernetesVersion 1.16.7
```

Powyższe polecenie tworzy nową pulę węzłów o nazwie **npwin** i dodaje ją do **myAKSCluster**. Podczas tworzenia puli węzłów do uruchamiania kontenerów systemu Windows Server, wartość domyślna dla **VmSize** jest **Standard_D2s_v3**. Jeśli wybierzesz opcję ustawienia parametru **VmSize** , sprawdź listę [rozmiarów maszyn wirtualnych z ograniczeniami][restricted-vm-sizes]. Minimalny zalecany rozmiar to **Standard_D2s_v3**. Poprzednie polecenie używa również domyślnej podsieci w domyślnej sieci wirtualnej utworzonej podczas uruchamiania `New-AzAks` .

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl][kubectl]. Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować `kubectl` lokalnie, użyj `Install-AzAksKubectl` polecenia cmdlet:

```azurepowershell-interactive
Install-AzAksKubectl
```

Aby skonfigurować `kubectl` program w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia cmdlet [Import-AzAksCredential][import-azakscredential] . To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```azurepowershell-interactive
kubectl get nodes
```

Następujące przykładowe dane wyjściowe pokazują wszystkie węzły w klastrze. Upewnij się, że stan wszystkich węzłów jest **gotowy**:

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Plik manifestu platformy Kubernetes definiuje żądany stan klastra, w tym informacje o obrazach kontenera do uruchomienia. W tym artykule manifest służy do tworzenia wszystkich obiektów wymaganych do uruchomienia przykładowej aplikacji ASP.NET w kontenerze systemu Windows Server. Ten manifest obejmuje [wdrożenie Kubernetes][kubernetes-deployment] dla przykładowej aplikacji ASP.NET i zewnętrznej [usługi Kubernetes][kubernetes-service] , aby uzyskać dostęp do aplikacji z Internetu.

Przykładowa aplikacja ASP.NET jest dostarczana jako część [przykładów .NET Framework][dotnet-samples] i uruchamiana w kontenerze systemu Windows Server. AKS wymaga, aby kontenery systemu Windows Server były oparte na obrazach **systemu Windows server 2019** lub nowszego. Plik manifestu Kubernetes musi także definiować [Selektor węzła][node-selector] , aby poinformować klaster AKS o konieczności uruchomienia ASP.NET przykładowej aplikacji na węźle, który może uruchamiać kontenery systemu Windows Server.

Utwórz plik o nazwie `sample.yaml` i skopiuj go do poniższej definicji YAML. Jeśli używasz usługi Azure Cloud Shell, ten plik można utworzyć przy użyciu programu `vi` lub `nano`, tak jak podczas pracy w systemie wirtualnym lub fizycznym:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Wdróż aplikację przy użyciu polecenia [kubectl apply][kubectl-apply] i podaj nazwę manifestu YAML:

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

Następujące przykładowe dane wyjściowe pokazują, że wdrożenie i usługa została utworzona pomyślnie:

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testowanie aplikacji

Po uruchomieniu aplikacji usługa Kubernetes udostępnia fronton aplikacji w Internecie.
Ten proces może potrwać kilka minut. Czasami usługa może potrwać dłużej niż kilka minut. W takich przypadkach Zezwalaj na maksymalnie 10 minut.

Aby monitorować postęp, użyj polecenia [kubectl get-service][kubectl-get] z argumentem `--watch`.

```azurepowershell-interactive
kubectl get service sample --watch
```

Początkowo **adres IP** dla **przykładowej** usługi jest pokazywany jako **oczekujący**.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy dla adresu **EXTERNAL-IP** wartość **oczekujący** zmieni się na rzeczywisty publiczny adres IP, naciśnij klawisze `CTRL-C`, aby zatrzymać proces śledzenia narzędzia `kubectl`. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Aby wyświetlić przykładową aplikację w działaniu, Otwórz przeglądarkę internetową na zewnętrzny adres IP usługi.

![Obraz przedstawiający przeglądanie do aplikacji przykładowej ASP.NET](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> Jeśli podczas próby załadowania strony zostanie wyświetlony limit czasu połączenia, należy sprawdzić, czy Przykładowa aplikacja jest gotowa przy użyciu następującego polecenia `kubectl get pods --watch` . Czasami kontener systemu Windows nie zostanie uruchomiony przez czas dostępności zewnętrznego adresu IP.

## <a name="delete-cluster"></a>Usuwanie klastra

Gdy klaster nie jest już konieczny, należy użyć polecenia cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] , aby usunąć grupę zasobów, usługę kontenera i wszystkie powiązane zasoby.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete]. Jeśli używana jest tożsamość zarządzana, tożsamość jest zarządzana przez platformę i nie wymaga usunięcia.

## <a name="next-steps"></a>Następne kroki

W tym artykule został wdrożony klaster Kubernetes i wdrożono `ASP.NET` przykładową aplikację w kontenerze systemu Windows Server. [Dostęp do pulpitu nawigacyjnego sieci Web Kubernetes][kubernetes-dashboard] dla utworzonego klastra.

Aby dowiedzieć się więcej o usłudze AKS i poznać dokładnie proces od kompletnego kodu do wdrożenia, przejdź do samouczka dotyczącego klastra Kubernetes.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
