---
title: Konfigurowanie łącza prywatnego
description: Konfigurowanie prywatnego punktu końcowego w rejestrze kontenerów i włączanie łącza prywatnego w lokalnej sieci wirtualnej
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498915"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Konfigurowanie łącza prywatnego platformy Azure dla rejestru kontenerów platformy Azure 

Można skonfigurować [prywatny punkt końcowy](../private-link/private-endpoint-overview.md) dla rejestru kontenerów platformy Azure, tak aby klienci w sieci wirtualnej platformy Azure bezpiecznie uzyskiwać dostęp do rejestru za pośrednictwem [łącza prywatnego.](../private-link/private-link-overview.md) Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla rejestru. Ruch sieciowy między klientami w sieci wirtualnej a rejestrem przechodzi przez sieć wirtualną i prywatne łącze w sieci szkieletowej firmy Microsoft, eliminując narażenie z publicznego Internetu.

Można [skonfigurować ustawienia DNS](../private-link/private-endpoint-overview.md#dns-configuration) dla prywatnego punktu końcowego, tak aby ustawienia były rozpoznawane na przydzielonym prywatnym adresie IP rejestru. Dzięki konfiguracji DNS klienci i usługi w sieci mogą nadal uzyskiwać dostęp do rejestru przy w pełni kwalifikowanej nazwie domeny rejestru, takiej jak *myregistry.azurecr.io*.

Ta funkcja jest dostępna w warstwie usługi rejestru kontenerów **premium.** Aby uzyskać informacje na temat warstw i limitów usług rejestru, zobacz [Jednostki SKU rejestru kontenerów platformy Azure](container-registry-skus.md).

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej i obowiązują pewne [ograniczenia.](#preview-limitations) Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Obecnie nie można skonfigurować łącza prywatnego z prywatnym punktem końcowym w [rejestrze replikowanym geograficznie](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>Wymagania wstępne

* Aby użyć kroki interfejsu wiersza polecenia platformy Azure w tym artykule, zaleca się interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli]. Lub uruchom w [usłudze Azure Cloud Shell](../cloud-shell/quickstart.md).
* Jeśli nie masz jeszcze rejestru kontenerów, utwórz jeden (wymagana warstwa Premium) i wypchnij przykładowy obraz, taki jak `hello-world` z usługi Docker Hub. Na przykład użyj [witryny Azure portal][quickstart-portal] lub interfejsu [wiersza polecenia platformy Azure,][quickstart-cli] aby utworzyć rejestr.
* Jeśli chcesz skonfigurować dostęp do rejestru przy użyciu łącza prywatnego w innej subskrypcji platformy Azure, musisz zarejestrować dostawcę zasobów dla rejestru kontenerów platformy Azure w tej subskrypcji. Przykład:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Przykłady interfejsu wiersza polecenia platformy Azure w tym artykule używają następujących zmiennych środowiskowych. Zastąp wartości odpowiednie dla danego środowiska. Wszystkie przykłady są sformatowane dla powłoki Bash:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Tworzenie maszyny wirtualnej z obsługą platformy Docker

Na potrzeby testów użyj maszyny Wirtualnej Ubuntu z włączoną funkcją platformy Docker, aby uzyskać dostęp do rejestru kontenerów platformy Azure. Aby użyć uwierzytelniania usługi Azure Active Directory do rejestru, należy również zainstalować [interfejsu wiersza polecenia platformy Azure][azure-cli] na maszynie Wirtualnej. Jeśli masz już maszynę wirtualną platformy Azure, pomiń ten krok tworzenia.

Można użyć tej samej grupy zasobów dla maszyny wirtualnej i rejestru kontenerów. Ta konfiguracja upraszcza oczyszczanie na końcu, ale nie jest wymagana. Jeśli zdecydujesz się utworzyć oddzielną grupę zasobów dla maszyny wirtualnej i sieci wirtualnej, uruchom [grupę AZ create:][az-group-create]

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Teraz wdrożyć domyślną maszynę wirtualną Platformy Azure Ubuntu z [az vm create][az-vm-create]. Poniższy przykład tworzy maszynę wirtualną o nazwie *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po zakończeniu polecenia należy wziąć `publicIpAddress` pod uwagę wyświetlane przez interfejsu wiersza polecenia platformy Azure. Ten adres służy do nawiązywać połączenia SSH z maszyną wirtualną.

### <a name="install-docker-on-the-vm"></a>Instalowanie platformy Docker na maszynie Wirtualnej

Po uruchomieniu maszyny Wirtualnej nawiązać połączenie SSH z maszyną wirtualną. Zamień *publicIpAddress* na publiczny adres IP maszyny Wirtualnej.

```bash
ssh azureuser@publicIpAddress
```

Uruchom następujące polecenia, aby zainstalować docker na maszynie wirtualnej Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Po instalacji uruchom następujące polecenie, aby sprawdzić, czy docker działa poprawnie na maszynie Wirtualnej:

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

Wykonaj kroki opisane w [install Azure CLI z apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) zainstalować interfejs wiersza polecenia platformy Azure na maszynie wirtualnej Ubuntu. Przykład:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Wyjdź z połączenia SSH.

## <a name="set-up-private-link---cli"></a>Konfigurowanie łącza prywatnego — cli

### <a name="get-network-and-subnet-names"></a>Uzyskaj nazwy sieci i podsieci

Jeśli nie masz ich jeszcze, do skonfigurowania łącza prywatnego potrzebne są nazwy sieci wirtualnej i podsieci. W tym przykładzie używasz tej samej podsieci dla maszyny Wirtualnej i prywatnego punktu końcowego rejestru. Jednak w wielu scenariuszach można skonfigurować punkt końcowy w oddzielnej podsieci. 

Podczas tworzenia maszyny Wirtualnej platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej jest oparta na nazwie maszyny wirtualnej. Na przykład, jeśli nazwa maszyny wirtualnej *myDockerVM*, domyślną nazwą sieci wirtualnej jest *myDockerVMVNET*, z podsieci o nazwie *myDockerVMSubnet*. Ustaw te wartości w zmiennych środowiskowych, uruchamiając polecenie [listy sieci wirtualnej az:][az-network-vnet-list]

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>Wyłączanie zasad sieciowych w podsieci

[Wyłącz zasady sieci,](../private-link/disable-private-endpoint-network-policy.md) takie jak sieciowe grupy zabezpieczeń w podsieci dla prywatnego punktu końcowego. Aktualizowanie konfiguracji podsieci za pomocą [aktualizacji podsieci sieci az:][az-network-vnet-subnet-update]

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Konfigurowanie prywatnej strefy DNS

Utwórz prywatną strefę DNS dla domeny rejestru kontenerów platformy Azure. W późniejszych krokach należy utworzyć rekordy DNS dla domeny rejestru w tej strefie DNS.

Aby użyć strefy prywatnej do zastąpienia domyślnej rozdzielczości DNS dla rejestru kontenerów platformy Azure, strefa musi mieć nazwę **privatelink.azurecr.io**. Uruchom następujące polecenie [tworzenia strefy prywatnej dns az,][az-network-private-dns-zone-create] aby utworzyć strefę prywatną:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Tworzenie łącza skojarzenia

Uruchom [az sieci private-dns link vnet utworzyć,][az-network-private-dns-link-vnet-create] aby skojarzyć swoją strefę prywatną z siecią wirtualną. W tym przykładzie utworzy się łącze o nazwie *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Tworzenie prywatnego punktu końcowego rejestru

W tej sekcji utwórz prywatny punkt końcowy rejestru w sieci wirtualnej. Najpierw uzyskaj identyfikator zasobu rejestru:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Uruchom polecenie [tworzenia prywatnego punktu końcowego az][az-network-private-endpoint-create] w celu utworzenia prywatnego punktu końcowego rejestru.

Poniższy przykład tworzy punkt końcowy *myPrivateEndpoint* i połączenie usługi *myConnection*. Aby określić zasób rejestru kontenerów dla punktu końcowego, przekaż: `--group-ids registry`

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Uzyskaj prywatne adresy IP

Uruchom [program AZ network private-endpoint show,][az-network-private-endpoint-show] aby zbadać punkt końcowy dla identyfikatora interfejsu sieciowego:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Skojarzone z interfejsem sieciowym są dwa prywatne adresy IP dla rejestru kontenerów: jeden dla samego rejestru i jeden dla punktu końcowego danych rejestru. Uruchom następujące polecenia [programu AZ Resource Show,][az-resource-show] aby uzyskać prywatne adresy IP rejestru kontenerów i punktu końcowego danych rejestru:

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>Tworzenie rekordów DNS w strefie prywatnej

Poniższe polecenia tworzą rekordy DNS w strefie prywatnej dla punktu końcowego rejestru i jego punktu końcowego danych. Na przykład jeśli masz rejestr o nazwie *myregistry* w regionie *westeurope,* nazwy punktów końcowych są `myregistry.azurecr.io` i `myregistry.westeurope.data.azurecr.io`. 

Najpierw uruchom [az network private-dns record-set a create to][az-network-private-dns-record-set-a-create] create empty A record sets for the registry endpoint and data endpoint:

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

Uruchom polecenie [rekordu rekordu dodatkowego az network private-dns,][az-network-private-dns-record-set-a-add-record] aby utworzyć rekordy A dla punktu końcowego rejestru i punktu końcowego danych:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

Łącze prywatne jest teraz skonfigurowane i gotowe do użycia.

## <a name="set-up-private-link---portal"></a>Konfigurowanie łącza prywatnego — portal

W poniższych krokach założono, że masz już sieć wirtualną i podsieć skonfigurowaną z maszyną wirtualną do testowania. Można również [utworzyć nową sieć wirtualną i podsieć](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **Ustawienia**wybierz pozycję **Prywatne połączenia punktów końcowych (Podgląd)**.
1. Wybierz **+ Prywatny punkt końcowy**.
1. Na karcie **Podstawy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wprowadź nazwę istniejącej grupy lub utwórz nową.|
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź unikatową nazwę. |
    |Region|Wybierz region.|
    |||
5. Wybierz **dalej: Zasób**.
6. Wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz **pozycję Połącz z zasobem platformy Azure w moim katalogu**.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz **pozycję Microsoft.ContainerRegistry/registries**. |
    | Zasób |Wybieranie nazwy rejestru|
    |Podźródło docelowe |Wybierz **rejestr**|
    |||
7. Wybierz **dalej: Konfiguracja**.
8. Wprowadź lub wybierz informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Networking**| |
    | Sieć wirtualna| Wybierz sieć wirtualną, w której jest wdrożona maszyna wirtualna, na przykład *myDockerVMVNET*. |
    | Podsieć | Wybierz podsieć, taką jak *myDockerVMSubnet,* w której jest wdrażana maszyna wirtualna. |
    |**Prywatna integracja z systemem DNS**||
    |Integracja z prywatną strefą DNS |Wybierz **pozycję Tak**. |
    |Prywatna strefa DNS |Wybierz *(nowy) privatelink.azurecr.io* |
    |||

1. Wybierz pozycję **Przegląd + utwórz**. Zostaniesz przesuń do **review + tworzenie** strony, gdzie platforma Azure sprawdza poprawność konfiguracji. 
2. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**.

Po utworzeniu prywatnego punktu końcowego ustawienia DNS w strefie prywatnej są wyświetlane na stronie **Przegląd** punktu końcowego.

![Ustawienia DNS punktu końcowego](./media/container-registry-private-link/private-endpoint-overview.png)

Twoje prywatne łącze jest teraz skonfigurowane i gotowe do użycia.

## <a name="validate-private-link-connection"></a>Sprawdzanie poprawności połączenia łącza prywatnego

Należy sprawdzić, czy zasoby w podsieci prywatnego punktu końcowego łączą się z rejestrem za pośrednictwem prywatnego adresu IP i mają prawidłową integrację prywatnej strefy DNS.

Aby sprawdzić poprawność połączenia łącza prywatnego, SSH do maszyny wirtualnej skonfigurowane w sieci wirtualnej.

Uruchom `nslookup` polecenie, aby rozpoznać adres IP rejestru za pomocą łącza prywatnego:

```bash
nslookup $registryName.azurecr.io
```

Przykładowy wynik pokazuje adres IP rejestru w przestrzeni adresowej podsieci:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Porównaj ten wynik z `nslookup` publicznym adresem IP w danych wyjściowych dla tego samego rejestru w publicznym punkcie końcowym:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operacje rejestru za łączem prywatnym

Sprawdź również, czy można wykonywać operacje rejestru z maszyny wirtualnej w podsieci. Nawiązuj połączenie SSH z maszyną wirtualną i uruchom [az acr login,][az-acr-login] aby zalogować się do rejestru. W zależności od konfiguracji maszyny Wirtualnej może być konieczne `sudo`prefiks następujących poleceń za pomocą programu .

```bash
az acr login --name $registryName
```

Wykonaj operacje rejestru, takie jak `docker pull` pobieranie przykładowego obrazu z rejestru. Zastąp `hello-world:v1` obrazem i tagiem odpowiednim dla rejestru, poprzedzony nazwą serwera logowania rejestru (wszystkie małe litery):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker pomyślnie ściąga obraz do maszyny Wirtualnej.

## <a name="manage-private-endpoint-connections"></a>Zarządzanie połączeniami prywatnych punktów końcowych

Zarządzanie połączeniami prywatnych punktów końcowych rejestru za pomocą portalu Azure lub za pomocą poleceń w grupie poleceń [az acr private-endpoint-connection.][az-acr-private-endpoint-connection] Operacje obejmują zatwierdzanie, usuwanie, listy, odrzucanie lub pokazywać szczegóły połączeń prywatnych punktów końcowych rejestru.

Na przykład, aby wyświetlić listę połączeń prywatnych punktów końcowych rejestru, uruchom polecenie [az acr private-endpoint-connection list.][az-acr-private-endpoint-connection-list] Przykład:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Podczas konfigurowania połączenia prywatnego punktu końcowego przy użyciu kroków w tym artykule, rejestr automatycznie akceptuje połączenia z klientami i usługami, które mają uprawnienia RBAC w rejestrze. Punkt końcowy można skonfigurować tak, aby wymagał ręcznego zatwierdzania połączeń. Aby uzyskać informacje dotyczące zatwierdzania i odrzucania połączeń prywatnych punktów końcowych, zobacz [Zarządzanie połączeniem prywatnego punktu końcowego](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli utworzono wszystkie zasoby platformy Azure w tej samej grupie zasobów i nie są już potrzebne, można opcjonalnie usunąć zasoby za pomocą jednego polecenia [az delete grupy:](/cli/azure/group)

```azurecli
az group delete --name $resourceGroup
```

Aby wyczyścić zasoby w portalu, przejdź do grupy zasobów. Po załadowaniu grupy zasobów kliknij pozycję **Usuń grupę zasobów,** aby usunąć grupę zasobów i przechowywane tam zasoby.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o łączu prywatnym, zobacz dokumentację [łącza prywatnego platformy Azure.](../private-link/private-link-overview.md)
* Alternatywą dla łącza prywatnego jest skonfigurowanie reguł dostępu do sieci w celu ograniczenia dostępu do rejestru. Aby dowiedzieć się więcej, zobacz [Ograniczanie dostępu do rejestru kontenerów platformy Azure przy użyciu sieci wirtualnej platformy Azure lub reguł zapory.](container-registry-vnet.md)

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
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
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
