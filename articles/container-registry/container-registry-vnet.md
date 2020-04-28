---
title: Ograniczanie dostępu przy użyciu sieci wirtualnej
description: Zezwalaj na dostęp do usługi Azure Container Registry tylko z zasobów w sieci wirtualnej platformy Azure lub z zakresów publicznych adresów IP.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74454333"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Ograniczanie dostępu do usługi Azure Container Registry przy użyciu sieci wirtualnej platformy Azure lub reguł zapory

[Usługa azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczną i prywatną sieć dla zasobów platformy Azure i lokalnych. Ograniczając dostęp do prywatnego rejestru kontenerów platformy Azure z sieci wirtualnej platformy Azure, upewnij się, że tylko zasoby w sieci wirtualnej mają dostęp do rejestru. W przypadku scenariuszy obejmujących wiele lokalizacji można także skonfigurować reguły zapory w taki sposób, aby zezwalały na dostęp do rejestru tylko z określonych adresów IP.

W tym artykule przedstawiono dwa scenariusze konfigurowania zasad dostępu do sieci dla ruchu przychodzącego w rejestrze kontenerów: z maszyny wirtualnej wdrożonej w sieci wirtualnej lub z publicznego adresu IP maszyny wirtualnej.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia mają zastosowanie](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

Jeśli zamiast tego musisz skonfigurować reguły dostępu dla zasobów, aby uzyskać dostęp do rejestru kontenerów za zaporą, zobacz [Konfigurowanie reguł dostępu do usługi Azure Container Registry za zaporą](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Tylko rejestr kontenerów w **warstwie Premium** można skonfigurować przy użyciu reguł dostępu do sieci. Aby uzyskać informacje na temat warstw usługi Registry, zobacz [Azure Container Registry SKU](container-registry-skus.md). 

* Tylko klaster [usługi Azure Kubernetes](../aks/intro-kubernetes.md) lub [maszyna wirtualna](../virtual-machines/linux/overview.md) platformy Azure może być używany jako host do uzyskiwania dostępu do rejestru kontenerów w sieci wirtualnej. *Inne usługi platformy Azure, w tym Azure Container Instances nie są obecnie obsługiwane.*

* Operacje [zadań ACR](container-registry-tasks-overview.md) nie są obecnie obsługiwane w rejestrze kontenerów, do których można uzyskać dostęp w sieci wirtualnej.

* Każdy rejestr obsługuje maksymalnie 100 reguł sieci wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

* Aby skorzystać z kroków interfejsu wiersza polecenia platformy Azure w tym artykule, wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.58 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

* Jeśli nie masz jeszcze rejestru kontenerów, utwórz go (wymagana jednostka SKU Premium) i wypchnij przykładowy obraz, taki `hello-world` jak z usługi Docker Hub. Na przykład użyj [Azure Portal][quickstart-portal] lub [interfejsu wiersza polecenia platformy Azure][quickstart-cli] , aby utworzyć rejestr. 

* Jeśli chcesz ograniczyć dostęp do rejestru przy użyciu sieci wirtualnej w innej subskrypcji platformy Azure, musisz zarejestrować dostawcę zasobów dla Azure Container Registry w tej subskrypcji. Przykład:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Informacje o regułach sieci dla rejestru kontenerów

Usługa Azure Container Registry domyślnie akceptuje połączenia przez Internet z hostów w dowolnej sieci. Za pomocą sieci wirtualnej możesz zezwolić na dostęp do rejestru tylko zasobom platformy Azure, takim jak klaster AKS lub maszyna wirtualna platformy Azure, bez przekraczania granicy sieci. Możesz również skonfigurować reguły zapory sieciowej, aby zezwalać tylko na określone zakresy publicznych adresów IP. 

Aby ograniczyć dostęp do rejestru, najpierw Zmień domyślną akcję rejestru, aby odmówić wszystkich połączeń sieciowych. Następnie Dodaj reguły dostępu do sieci. Klienci, którym udzielono dostępu za pośrednictwem reguł sieci, muszą nadal [uwierzytelniać się w rejestrze kontenerów](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) i uzyskiwać dostęp do danych.

### <a name="service-endpoint-for-subnets"></a>Punkt końcowy usługi dla podsieci

Aby zezwolić na dostęp z podsieci w sieci wirtualnej, należy dodać [punkt końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dla usługi Azure Container Registry. 

Usługi wielodostępne, takie jak Azure Container Registry, używają jednego zestawu adresów IP dla wszystkich klientów. Punkt końcowy usługi przypisuje punkt końcowy, aby uzyskać dostęp do rejestru. Ten punkt końcowy zapewnia ruch optymalnie kierowany do zasobu za pośrednictwem sieci szkieletowej platformy Azure. Tożsamości sieci wirtualnej i podsieci są również przesyłane z każdym żądaniem.

### <a name="firewall-rules"></a>Reguły zapory

W przypadku reguł sieci IP należy podać dozwolone zakresy adresów internetowych przy użyciu notacji CIDR, takiej jak *16.17.18.0/24* lub indywidualne adresy IP, takie jak *16.17.18.19*. Reguły sieci IP są dozwolone tylko dla *publicznych* adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w dokumencie RFC 1918) nie są dozwolone w regułach adresów IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Tworzenie maszyny wirtualnej z obsługą platformy Docker

W tym artykule należy użyć maszyny wirtualnej Ubuntu z obsługą platformy Docker w celu uzyskania dostępu do usługi Azure Container Registry. Aby użyć uwierzytelniania Azure Active Directory do rejestru, należy również zainstalować [interfejs wiersza polecenia platformy Azure][azure-cli] na maszynie wirtualnej. Jeśli masz już maszynę wirtualną platformy Azure, Pomiń ten krok tworzenia.

Możesz użyć tej samej grupy zasobów dla maszyny wirtualnej i rejestru kontenerów. Ta konfiguracja upraszcza czyszczenie na końcu, ale nie jest wymagane. Jeśli zdecydujesz się utworzyć oddzielną grupę zasobów dla maszyny wirtualnej i sieci wirtualnej, uruchom polecenie [AZ Group Create][az-group-create]. Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *westcentralus* :

```azurecli
az group create --name myResourceGroup --location westus
```

Teraz Wdróż domyślną Ubuntu maszynę wirtualną platformy Azure za pomocą [AZ VM Create][az-vm-create]. Poniższy przykład tworzy maszynę wirtualną o nazwie *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po zakończeniu wykonywania polecenia Zanotuj `publicIpAddress` wyświetlane wartości w wierszu polecenia platformy Azure. Użyj tego adresu, aby nawiązać połączenia SSH z maszyną wirtualną i opcjonalnie skonfigurować reguły zapory.

### <a name="install-docker-on-the-vm"></a>Zainstaluj platformę Docker na maszynie wirtualnej

Po uruchomieniu maszyny wirtualnej należy nawiązać połączenie SSH z maszyną wirtualną. Zastąp *publicIpAddress* publicznym adresem IP maszyny wirtualnej.

```bash
ssh azureuser@publicIpAddress
```

Uruchom następujące polecenie, aby zainstalować platformę Docker na maszynie wirtualnej Ubuntu:

```bash
sudo apt install docker.io -y
```

Po zakończeniu instalacji uruchom następujące polecenie, aby sprawdzić, czy program Docker działa prawidłowo na maszynie wirtualnej:

```bash
sudo docker run -it hello-world
```

Dane wyjściowe:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Wykonaj kroki opisane w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure z programem apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) , aby zainstalować interfejs wiersza polecenia platformy Azure na maszynie wirtualnej Ubuntu. W tym artykule upewnij się, że instalujesz wersję 2.0.58 lub nowszą.

Wyjdź z połączenia SSH.

## <a name="allow-access-from-a-virtual-network"></a>Zezwalaj na dostęp z sieci wirtualnej

W tej sekcji Skonfiguruj rejestr kontenerów w taki sposób, aby zezwalał na dostęp z podsieci w sieci wirtualnej platformy Azure. Dostępne są równoważne kroki przy użyciu interfejsu wiersza polecenia platformy Azure i Azure Portal.

### <a name="allow-access-from-a-virtual-network---cli"></a>Zezwalanie na dostęp z sieci wirtualnej — interfejs wiersza polecenia

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Dodawanie punktu końcowego usługi do podsieci

Podczas tworzenia maszyny wirtualnej platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej jest oparta na nazwie maszyny wirtualnej. Na przykład jeśli nazwasz maszyny wirtualnej *myDockerVM*, domyślną nazwą sieci wirtualnej jest *myDockerVMVNET*, z podsiecią o nazwie *myDockerVMSubnet*. Sprawdź to w Azure Portal lub przy użyciu polecenia [AZ Network VNET list][az-network-vnet-list] :

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Dane wyjściowe:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Użyj polecenia [AZ Network VNET Subnet Update][az-network-vnet-subnet-update] , aby dodać punkt końcowy usługi **Microsoft. ContainerRegistry** do podsieci. Zastąp nazwy sieci wirtualnej i podsieci w następującym poleceniu:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Użyj polecenia [AZ Network VNET Subnet show][az-network-vnet-subnet-show] , aby pobrać identyfikator zasobu podsieci. Jest to konieczne w późniejszym kroku, aby skonfigurować regułę dostępu do sieci.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Dane wyjściowe:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Zmień domyślny dostęp sieciowy do rejestru

Domyślnie usługa Azure Container Registry umożliwia nawiązywanie połączeń między hostami w dowolnej sieci. Aby ograniczyć dostęp do wybranej sieci, Zmień domyślną akcję na Odmów dostępu. Zastąp nazwę rejestru następującym poleceniem [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Dodawanie reguły sieci do rejestru

Użyj polecenia [AZ ACR Network-Rule Add][az-acr-network-rule-add] , aby dodać regułę sieci do rejestru, który umożliwia dostęp z podsieci maszyny wirtualnej. Zastąp nazwę rejestru kontenerów i identyfikator zasobu podsieci w następującym poleceniu: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Kontynuuj [Weryfikowanie dostępu do rejestru](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Zezwalanie na dostęp z poziomu sieci wirtualnej — Portal

#### <a name="add-service-endpoint-to-subnet"></a>Dodaj punkt końcowy usługi do podsieci

Podczas tworzenia maszyny wirtualnej platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej jest oparta na nazwie maszyny wirtualnej. Na przykład jeśli nazwasz maszyny wirtualnej *myDockerVM*, domyślną nazwą sieci wirtualnej jest *myDockerVMVNET*, z podsiecią o nazwie *myDockerVMSubnet*.

Aby dodać punkt końcowy usługi dla Azure Container Registry do podsieci:

1. W polu wyszukiwania w górnej części [Azure Portal][azure-portal]wprowadź *sieci wirtualne*. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
1. Z listy sieci wirtualnych wybierz sieć wirtualną, w której wdrożono maszynę wirtualną, taką jak *myDockerVMVNET*.
1. W obszarze **Ustawienia**wybierz pozycję **podsieci**.
1. Wybierz podsieć, w której wdrożono maszynę wirtualną, na przykład *myDockerVMSubnet*.
1. W obszarze **punkty końcowe usługi**wybierz pozycję **Microsoft. ContainerRegistry**.
1. Wybierz pozycję **Zapisz**.

![Dodaj punkt końcowy usługi do podsieci][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Konfigurowanie dostępu do sieci dla rejestru

Domyślnie usługa Azure Container Registry umożliwia nawiązywanie połączeń między hostami w dowolnej sieci. Aby ograniczyć dostęp do sieci wirtualnej:

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **Ustawienia**wybierz pozycję **Zapora i sieci wirtualne**.
1. Aby domyślnie zablokować dostęp, wybierz opcję zezwolenia na dostęp z **wybranych sieci**. 
1. Wybierz pozycję **Dodaj istniejącą sieć wirtualną**, a następnie wybierz sieć wirtualną i podsieć skonfigurowaną z punktem końcowym usługi. Wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Zapisz**.

![Konfigurowanie sieci wirtualnej dla rejestru kontenerów][acr-vnet-portal]

Kontynuuj [Weryfikowanie dostępu do rejestru](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Zezwalaj na dostęp z adresu IP

W tej sekcji Skonfiguruj rejestr kontenerów w taki sposób, aby zezwalał na dostęp z określonego adresu IP lub zakresu. Dostępne są równoważne kroki przy użyciu interfejsu wiersza polecenia platformy Azure i Azure Portal.

### <a name="allow-access-from-an-ip-address---cli"></a>Zezwalanie na dostęp z adresu IP — interfejs wiersza polecenia

#### <a name="change-default-network-access-to-registry"></a>Zmień domyślny dostęp sieciowy do rejestru

Jeśli jeszcze tego nie zrobiono, zaktualizuj konfigurację rejestru, aby domyślnie odmówić dostępu. Zastąp nazwę rejestru następującym poleceniem [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Usuń regułę sieci z rejestru

Jeśli wcześniej została dodana reguła sieciowa zezwalająca na dostęp z podsieci maszyny wirtualnej, Usuń punkt końcowy usługi podsieci i regułę sieci. Zastąp nazwę rejestru kontenerów i identyfikator zasobu podsieci, która została pobrana we wcześniejszej części polecenia [AZ ACR Network-Rule Remove][az-acr-network-rule-remove] : 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Dodawanie reguły sieci do rejestru

Użyj polecenia [AZ ACR Network-Rule Add][az-acr-network-rule-add] , aby dodać regułę sieci do rejestru, który umożliwia dostęp z adresu IP maszyny wirtualnej. Zastąp nazwę rejestru kontenerów i publiczny adres IP maszyny wirtualnej w poniższym poleceniu.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Kontynuuj [Weryfikowanie dostępu do rejestru](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Zezwalanie na dostęp z adresu IP — Portal

#### <a name="remove-existing-network-rule-from-registry"></a>Usuń istniejącą regułę sieci z rejestru

Jeśli wcześniej dodano regułę sieci, aby zezwolić na dostęp z podsieci maszyny wirtualnej, Usuń istniejącą regułę. Pomiń tę sekcję, jeśli chcesz uzyskać dostęp do rejestru z innej maszyny wirtualnej.

* Zaktualizuj ustawienia podsieci, aby usunąć punkt końcowy usługi podsieci dla Azure Container Registry. 

  1. W [Azure Portal][azure-portal]przejdź do sieci wirtualnej, w której wdrożono maszynę wirtualną.
  1. W obszarze **Ustawienia**wybierz pozycję **podsieci**.
  1. Wybierz podsieć, w której wdrożono maszynę wirtualną.
  1. W obszarze **punkty końcowe usługi**Usuń pole wyboru dla elementu **Microsoft. ContainerRegistry**. 
  1. Wybierz pozycję **Zapisz**.

* Usuń regułę sieci, która umożliwia podsieć dostęp do rejestru.

  1. W portalu przejdź do rejestru kontenerów.
  1. W obszarze **Ustawienia**wybierz pozycję **Zapora i sieci wirtualne**.
  1. W obszarze **sieci wirtualne**wybierz nazwę sieci wirtualnej, a następnie wybierz pozycję **Usuń**.
  1. Wybierz pozycję **Zapisz**.

#### <a name="add-network-rule-to-registry"></a>Dodawanie reguły sieci do rejestru

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **Ustawienia**wybierz pozycję **Zapora i sieci wirtualne**.
1. Jeśli jeszcze tego nie zrobiono, wybierz opcję zezwolenia na dostęp z **wybranych sieci**. 
1. W obszarze **sieci wirtualne**upewnij się, że nie wybrano żadnej sieci.
1. W obszarze **Zapora**wprowadź publiczny adres IP maszyny wirtualnej. Lub wprowadź zakres adresów w notacji CIDR, który zawiera adres IP maszyny wirtualnej.
1. Wybierz pozycję **Zapisz**.

![Konfigurowanie reguły zapory dla rejestru kontenerów][acr-vnet-firewall-portal]

Kontynuuj [Weryfikowanie dostępu do rejestru](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Weryfikowanie dostępu do rejestru

Po odczekaniu kilku minut na aktualizację konfiguracji Sprawdź, czy maszyna wirtualna może uzyskać dostęp do rejestru kontenerów. Utwórz połączenie SSH z maszyną wirtualną i uruchom polecenie [AZ ACR login][az-acr-login] , aby zalogować się do rejestru. 

```bash
az acr login --name mycontainerregistry
```

Można wykonać operacje rejestru, takie jak Uruchom `docker pull` , aby ściągnąć przykładowy obraz z rejestru. Zastąp wartość obrazu i tagu odpowiednią dla rejestru poprzedzoną prefiksem nazwy serwera logowania rejestru (wszystkie małe litery):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Platforma Docker pomyślnie ściąga obraz do maszyny wirtualnej.

Ten przykład pokazuje, że można uzyskać dostęp do prywatnego rejestru kontenerów za pomocą reguły dostępu do sieci. Nie można jednak uzyskać dostępu do rejestru z innego hosta logowania, który nie ma skonfigurowanej reguły dostępu do sieci. W przypadku próby zalogowania się z innego hosta za `az acr login` pomocą polecenia `docker login` lub polecenia dane wyjściowe są podobne do następujących:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Przywróć domyślny dostęp do rejestru

Aby przywrócić rejestr w celu zezwolenia na dostęp domyślnie, Usuń wszystkie skonfigurowane reguły sieciowe. Następnie ustaw domyślną akcję na Zezwalaj na dostęp. Dostępne są równoważne kroki przy użyciu interfejsu wiersza polecenia platformy Azure i Azure Portal.

### <a name="restore-default-registry-access---cli"></a>Przywróć domyślny dostęp do rejestru — interfejs wiersza polecenia

#### <a name="remove-network-rules"></a>Usuń reguły sieci

Aby wyświetlić listę reguł sieci skonfigurowanych dla rejestru, uruchom następujące polecenie [AZ ACR Network-Rule list][az-acr-network-rule-list] :

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Dla każdej skonfigurowanej reguły Uruchom polecenie [AZ ACR Network-Rule Remove][az-acr-network-rule-remove] , aby je usunąć. Przykład:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Zezwól na dostęp

Zastąp nazwę rejestru następującym poleceniem [AZ ACR Update][az-acr-update] :
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Przywróć domyślny dostęp do rejestru — Portal


1. W portalu przejdź do rejestru kontenerów i wybierz opcję **Zapora i sieci wirtualne**.
1. W obszarze **sieci wirtualne**zaznacz każdą sieć wirtualną, a następnie wybierz pozycję **Usuń**.
1. W obszarze **Zapora**wybierz każdy zakres adresów, a następnie wybierz ikonę Usuń.
1. W obszarze **Zezwalaj na dostęp z**, wybierz pozycję **wszystkie sieci**. 
1. Wybierz pozycję **Zapisz**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli wszystkie zasoby platformy Azure zostały utworzone w tej samej grupie zasobów i nie będą już potrzebne, możesz opcjonalnie usunąć zasoby za pomocą jednego polecenia [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

Aby wyczyścić zasoby w portalu, przejdź do grupy zasobów zasoby. Po załadowaniu grupy zasobów kliknij pozycję **Usuń grupę zasobów** , aby usunąć grupę zasobów i przechowywane w niej zasoby.

## <a name="next-steps"></a>Następne kroki

Niektóre zasoby i funkcje sieci wirtualnej zostały omówione w tym artykule, ale krótko. Dokumentacja usługi Azure Virtual Network obejmuje wiele następujących zagadnień:

* [Sieć wirtualna](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Podsieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Punkty końcowe usługi](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
