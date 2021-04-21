---
title: Konfigurowanie prywatnego punktu końcowego za pomocą linku prywatnego
description: Skonfiguruj prywatny punkt końcowy w rejestrze kontenerów i włącz dostęp za pośrednictwem linku prywatnego w lokalnej sieci wirtualnej. Dostęp do linku prywatnego jest funkcją warstwy usługi Premium.
ms.topic: article
ms.date: 03/31/2021
ms.openlocfilehash: d3c7c573b0ffc08a85f5cbe5cc62d3f7c052f0af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781437"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Nawiązywanie prywatnego połączenia z rejestrem kontenerów platformy Azure przy użyciu Azure Private Link


Ogranicz dostęp do rejestru, przypisując prywatne adresy IP sieci wirtualnej do punktów końcowych rejestru i [używając](../private-link/private-link-overview.md)Azure Private Link . Ruch sieciowy między klientami w sieci wirtualnej i prywatnymi punktami końcowymi rejestru przechodzi przez sieć wirtualną i łącze prywatne w sieci szkieletowej firmy Microsoft, eliminując narażenie z publicznego Internetu. Private Link umożliwia również dostęp do rejestru prywatnego [](../expressroute/expressroute-introduction.MD) ze środowisk lokalnych za pośrednictwem Azure ExpressRoute komunikacji równorzędnej lub bramy [sieci VPN.](../vpn-gateway/vpn-gateway-about-vpngateways.md)

Ustawienia [DNS dla](../private-link/private-endpoint-overview.md#dns-configuration) prywatnych punktów końcowych rejestru można skonfigurować tak, aby ustawienia rozpoznały przydzielony prywatny adres IP rejestru. Dzięki konfiguracji DNS klienci i usługi w sieci mogą nadal uzyskać dostęp do rejestru pod w pełni kwalifikowaną nazwą domeny rejestru, taką *jak myregistry.azurecr.io*. 

Ta funkcja jest dostępna w warstwie usługi rejestru kontenerów **Premium.** Obecnie dla rejestru można skonfigurować maksymalnie 10 prywatnych punktów końcowych. Aby uzyskać informacje o warstwach i limitach usługi rejestru, [zobacz Azure Container Registry usługi](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aby wykonać kroki interfejsu wiersza polecenia platformy Azure opisane w tym artykule, zaleca się korzystanie z interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli]. Lub uruchom w [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Jeśli nie masz jeszcze rejestru kontenerów, utwórz go (wymagana warstwa Premium) i zaimportuj przykładowy obraz publiczny, taki jak z [](container-registry-import-images.md) `mcr.microsoft.com/hello-world` Microsoft Container Registry. Aby na przykład utworzyć [rejestr, Azure Portal][quickstart-portal] interfejsu wiersza polecenia platformy [Azure][quickstart-cli] lub interfejsu wiersza polecenia platformy Azure.
* Aby skonfigurować dostęp do rejestru przy użyciu linku prywatnego w innej subskrypcji platformy Azure, musisz zarejestrować dostawcę zasobów dla Azure Container Registry tej subskrypcji. Na przykład:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Przykłady interfejsu wiersza polecenia platformy Azure w tym artykule używają następujących zmiennych środowiskowych. Zastąp wartości odpowiednie dla Twojego środowiska. Wszystkie przykłady są sformatowane dla powłoki Bash:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Konfigurowanie linku prywatnego — interfejs wiersza polecenia

### <a name="get-network-and-subnet-names"></a>Uzyskiwanie nazw sieci i podsieci

Jeśli nie masz ich jeszcze, do skonfigurowania linku prywatnego będą potrzebne nazwy sieci wirtualnej i podsieci. W tym przykładzie użyjemy tej samej podsieci dla maszyny wirtualnej i prywatnego punktu końcowego rejestru. Jednak w wielu scenariuszach punkt końcowy należy skonfigurować w oddzielnej podsieci. 

Podczas tworzenia maszyny wirtualnej platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej jest oparta na nazwie maszyny wirtualnej. Jeśli na przykład nadasz maszynie wirtualnej nazwę *myDockerVM,* domyślną nazwą sieci wirtualnej jest *myDockerVMVNET,* z podsiecią o nazwie *myDockerVMSubnet.* Ustaw następujące wartości w zmiennych środowiskowych, uruchamiając [polecenie az network vnet list:][az-network-vnet-list]

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Wyłączanie zasad sieciowych w podsieci

[Wyłącz zasady sieciowe,](../private-link/disable-private-endpoint-network-policy.md) takie jak sieciowe grupy zabezpieczeń w podsieci dla prywatnego punktu końcowego. Zaktualizuj konfigurację podsieci za pomocą [az network vnet subnet update:][az-network-vnet-subnet-update]

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Konfigurowanie prywatnej strefy DNS

Utwórz [prywatną strefę Azure DNS dla](../dns/private-dns-privatednszone.md) prywatnej domeny rejestru kontenerów platformy Azure. W kolejnych krokach utworzysz rekordy DNS dla domeny rejestru w tej strefie DNS. Aby uzyskać więcej informacji, zobacz [opcje konfiguracji DNS](#dns-configuration-options)w dalszej części tego artykułu.

Aby użyć strefy prywatnej do zastąpienia domyślnego rozpoznawania nazw DNS dla rejestru kontenerów platformy Azure, strefa musi mieć **nazwę privatelink.azurecr.io**. Uruchom następujące polecenie [az network private-dns zone create,][az-network-private-dns-zone-create] aby utworzyć strefę prywatną:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Tworzenie linku skojarzenia

Uruchom [az network private-dns link vnet create,][az-network-private-dns-link-vnet-create] aby skojarzyć strefę prywatną z siecią wirtualną. W tym przykładzie zostanie link o nazwie *myDNSLink.*

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Tworzenie punktu końcowego rejestru prywatnego

W tej sekcji utwórz prywatny punkt końcowy rejestru w sieci wirtualnej. Najpierw pobierz identyfikator zasobu rejestru:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Uruchom polecenie [az network private-endpoint create,][az-network-private-endpoint-create] aby utworzyć prywatny punkt końcowy rejestru.

Poniższy przykład tworzy punkt końcowy *myPrivateEndpoint* i połączenie usługi *myConnection*. Aby określić zasób rejestru kontenerów dla punktu końcowego, przekaż `--group-ids registry` wartość :

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-endpoint-ip-configuration"></a>Uzyskiwanie konfiguracji adresu IP punktu końcowego

Aby skonfigurować rekordy DNS, pobierz konfigurację adresu IP prywatnego punktu końcowego. W tym przykładzie skojarzona z interfejsem sieciowym prywatnego punktu końcowego są dwa prywatne adresy IP dla rejestru kontenerów: jeden dla samego rejestru i jeden dla punktu końcowego danych rejestru. 

Najpierw uruchom az [network private-endpoint show,][az-network-private-endpoint-show] aby odpytować prywatny punkt końcowy o identyfikator interfejsu sieciowego:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Następujące polecenia [az network nic show][az-network-nic-show] pobrać prywatne adresy IP dla rejestru kontenerów i punktu końcowego danych rejestru:

```azurecli
REGISTRY_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateIpAddress" \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateIpAddress" \
  --output tsv)

# An FQDN is associated with each IP address in the IP configurations

REGISTRY_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateLinkConnectionProperties.fqdns" \
  --output tsv)

DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```

> [!NOTE]
> Jeśli rejestr jest [replikowany geograficznie,](container-registry-geo-replication.md)należy zapytanie o dodatkowy punkt końcowy danych dla każdej repliki rejestru.

### <a name="create-dns-records-in-the-private-zone"></a>Tworzenie rekordów DNS w strefie prywatnej

Następujące polecenia tworzą rekordy DNS w strefie prywatnej dla punktu końcowego rejestru i jego punktu końcowego danych. Jeśli na przykład w regionie *westeurope* istnieje rejestr o nazwie *myregistry,* nazwy punktów końcowych to `myregistry.azurecr.io` i `myregistry.westeurope.data.azurecr.io` . 

> [!NOTE]
> Jeśli rejestr jest replikowany [geograficznie,](container-registry-geo-replication.md)utwórz dodatkowe rekordy DNS dla adresu IP punktu końcowego danych każdej repliki.

Najpierw uruchom [az network private-dns record-set a create,][az-network-private-dns-record-set-a-create] aby utworzyć puste zestawy rekordów A dla punktu końcowego rejestru i punktu końcowego danych:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Uruchom [polecenie az network private-dns record-set, aby][az-network-private-dns-record-set-a-add-record] utworzyć rekordy A dla punktu końcowego rejestru i punktu końcowego danych:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $REGISTRY_PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

Link prywatny jest teraz skonfigurowany i gotowy do użycia.

## <a name="set-up-private-link---portal"></a>Konfigurowanie linku prywatnego — portal

Skonfiguruj link prywatny podczas tworzenia rejestru lub dodawania linku prywatnego do istniejącego rejestru. W poniższych krokach przyjęto założenie, że masz już sieć wirtualną i podsieć, które zostały już ustawione na maszynie wirtualnej do testowania. Możesz również [utworzyć nową sieć wirtualną i podsieć](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Tworzenie prywatnego punktu końcowego — nowy rejestr

1. Podczas tworzenia rejestru w portalu na karcie **Podstawowe** w obszarze **SKU** wybierz pozycję **Premium.**
1. Wybierz kartę **Sieć**.
1. W **opcji Łączność sieciowa** wybierz pozycję **Prywatny punkt końcowy**+  >  **Dodaj**.
1. Wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wprowadź nazwę istniejącej grupy lub utwórz nową.|
    | Nazwa | Wprowadź unikatową nazwę. |
    | Podźródło |Wybieranie **rejestru**|
    | **Sieć** | |
    | Sieć wirtualna| Wybierz sieć wirtualną, w której wdrożono maszynę wirtualną, na przykład *myDockerVMVNET.* |
    | Podsieć | Wybierz podsieć, taką jak *myDockerVMSubnet,* w której wdrożono maszynę wirtualną. |
    |**Prywatna strefa DNS integracji**||
    |Integruj z prywatną strefą DNS |Wybierz pozycję **Tak**. |
    |Prywatna strefa DNS |Wybierz *pozycję (Nowy) privatelink.azurecr.io* |
    |||
1. Skonfiguruj pozostałe ustawienia rejestru, a następnie wybierz pozycję **Przejrzyj i utwórz.**

  ![Tworzenie rejestru z prywatnym punktem końcowym](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Tworzenie prywatnego punktu końcowego — istniejący rejestr

1. W portalu przejdź do rejestru kontenerów.
1. W **obszarze Ustawienia** wybierz pozycję **Sieć.**
1. Na karcie **Prywatne punkty końcowe** wybierz pozycję + Prywatny punkt **końcowy**.
1. Na karcie **Podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wprowadź nazwę istniejącej grupy lub utwórz nową.|
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź nazwę. |
    |Region (Region)|Wybierz region.|
    |||
5. Wybierz **pozycję Dalej: Zasób.**
6. Wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz **pozycję Połącz z zasobem platformy Azure w moim katalogu.**|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz **pozycję Microsoft.ContainerRegistry/registries.** |
    | Zasób |Wybierz nazwę rejestru|
    |Docelowy podźródło |Wybieranie **rejestru**|
    |||
7. Wybierz **pozycję Dalej: Konfiguracja.**
8. Wprowadź lub wybierz informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Sieć**| |
    | Sieć wirtualna| Wybierz sieć wirtualną, w której wdrożono maszynę wirtualną, na przykład *myDockerVMVNET.* |
    | Podsieć | Wybierz podsieć, taką jak *myDockerVMSubnet,* w której wdrożono maszynę wirtualną. |
    |**Prywatna strefa DNS integracji**||
    |Integruj z prywatną strefą DNS |Wybierz pozycję **Tak**. |
    |Prywatna strefa DNS |Wybierz *pozycję (Nowy) privatelink.azurecr.io* |
    |||

1. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację. 
2. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

Po utworzeniu prywatnego punktu końcowego ustawienia DNS w strefie prywatnej są wyświetlane na stronie Prywatne **punkty** końcowe w portalu:

1. W portalu przejdź do rejestru kontenerów i wybierz pozycję Ustawienia > **sieci.**
1. Na karcie **Prywatne punkty końcowe** wybierz utworzony prywatny punkt końcowy.
1. Na stronie **Przegląd** przejrzyj ustawienia linku i niestandardowe ustawienia DNS.

  ![Ustawienia DNS punktu końcowego](./media/container-registry-private-link/private-endpoint-overview.png)

Link prywatny jest teraz skonfigurowany i gotowy do użycia.

## <a name="disable-public-access"></a>Wyłączanie dostępu publicznego

W wielu scenariuszach należy wyłączyć dostęp do rejestru z sieci publicznych. Ta konfiguracja uniemożliwia klientom spoza sieci wirtualnej dotarcie do punktów końcowych rejestru. 

### <a name="disable-public-access---cli"></a>Wyłączanie dostępu publicznego — interfejs wiersza polecenia

Aby wyłączyć dostęp publiczny przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom [narzędzie az acr update][az-acr-update] i ustaw wartość `--public-network-enabled` `false` . 

> [!NOTE]
> Argument wymaga interfejsu wiersza polecenia platformy Azure w wersji `public-network-enabled` 2.6.0 lub nowszej. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Wyłączanie dostępu publicznego — portal

1. W portalu przejdź do rejestru kontenerów i wybierz pozycję Ustawienia > **sieci.**
1. Na karcie **Dostęp publiczny** w obszarze **Zezwalaj na dostęp do sieci publicznej** wybierz pozycję **Wyłączone.** Następnie wybierz pozycję **Zapisz**.

## <a name="validate-private-link-connection"></a>Weryfikowanie połączenia z łączem prywatnym

Należy sprawdzić, czy zasoby w podsieci prywatnego punktu końcowego łączą się z rejestrem za pośrednictwem prywatnego adresu IP i mają poprawną integrację prywatnej strefy DNS.

Aby sprawdzić poprawność połączenia z połączeniem prywatnym, połącz się przez SSH z maszyną wirtualną, która została ustawiona w sieci wirtualnej.

Uruchom narzędzie, takie jak lub , aby sprawdzić adres IP rejestru `nslookup` `dig` za pośrednictwem linku prywatnego. Na przykład:

```bash
dig $REGISTRY_NAME.azurecr.io
```

Przykładowe dane wyjściowe pokazują adres IP rejestru w przestrzeni adresowej podsieci:

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Porównaj ten wynik z publicznym adresem IP w danych wyjściowych tego samego rejestru `dig` za pośrednictwem publicznego punktu końcowego:

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>Operacje rejestru za pośrednictwem łącza prywatnego

Sprawdź również, czy można wykonywać operacje rejestru z maszyny wirtualnej w podsieci. Nawiązaniu połączenia SSH z maszyną wirtualną uruchom az [acr login,][az-acr-login] aby zalogować się do rejestru. W zależności od konfiguracji maszyny wirtualnej może być konieczne poprzednienie poniższych poleceń prefiksem `sudo` .

```bash
az acr login --name $REGISTRY_NAME
```

Wykonywanie operacji rejestru, takich jak `docker pull` ściąganie przykładowego obrazu z rejestru. Zastąp element obrazem i tagiem odpowiednim dla rejestru z prefiksem nazwy serwera logowania rejestru `hello-world:v1` (wszystkie małe litery):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker pomyślnie ściąga obraz do maszyny wirtualnej.

## <a name="manage-private-endpoint-connections"></a>Zarządzanie połączeniami prywatnego punktu końcowego

Zarządzanie połączeniami prywatnego punktu końcowego rejestru przy użyciu Azure Portal lub za pomocą poleceń w grupie poleceń [az acr private-endpoint-connection.][az-acr-private-endpoint-connection] Operacje obejmują zatwierdzanie, usuwanie, wyświetlanie listy, odrzucanie lub pokazywanie szczegółów połączeń prywatnego punktu końcowego rejestru.

Aby na przykład wyświetlić listę połączeń prywatnego punktu końcowego rejestru, uruchom polecenie [az acr private-endpoint-connection list.][az-acr-private-endpoint-connection-list] Na przykład:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Po skonfigurowaniu połączenia prywatnego punktu końcowego przy użyciu kroków instrukcje opisane w tym artykule rejestr automatycznie akceptuje połączenia od klientów i usług, które mają uprawnienia RBAC platformy Azure w rejestrze. Punkt końcowy można skonfigurować tak, aby wymagał ręcznego zatwierdzania połączeń. Aby uzyskać informacje na temat sposobu zatwierdzania i odrzucania połączeń z prywatnym punktem końcowym, zobacz [Zarządzanie połączeniem prywatnego punktu końcowego](../private-link/manage-private-endpoint.md).

> [!IMPORTANT]
> Obecnie w przypadku usunięcia prywatnego punktu końcowego z rejestru może być również konieczne usunięcie linku sieci wirtualnej do strefy prywatnej. Jeśli link nie zostanie usunięty, może zostać wyświetlony błąd podobny do `unresolvable host` .

## <a name="dns-configuration-options"></a>Opcje konfiguracji DNS

Prywatny punkt końcowy w tym przykładzie integruje się z prywatną strefą DNS skojarzoną z podstawową siecią wirtualną. Ta konfiguracja korzysta bezpośrednio z usługi DNS udostępnianej przez platformę Azure w celu rozpoznania publicznej nazwy FQDN rejestru jako prywatnych adresów IP w sieci wirtualnej. 

Usługa Private Link obsługuje dodatkowe scenariusze konfiguracji DNS, które korzystają ze strefy prywatnej, w tym z niestandardowymi rozwiązaniami DNS. Możesz na przykład wdrożyć niestandardowe rozwiązanie DNS w sieci wirtualnej lub lokalnie w sieci, która łączy się z siecią wirtualną przy użyciu bramy sieci VPN lub Azure ExpressRoute. 

Aby w tych scenariuszach rozpoznać publiczną w sieci FQDN rejestru prywatny adres IP, należy skonfigurować usługę przesyłania dalej na poziomie serwera do usługi Azure DNS (168.63.129.16). Dokładne opcje i kroki konfiguracji zależą od istniejących sieci i systemu DNS. Aby uzyskać przykłady, zobacz [Konfiguracja usługi DNS prywatnego punktu końcowego platformy Azure.](../private-link/private-endpoint-dns.md)

> [!IMPORTANT]
> Jeśli w celu wysokiej dostępności utworzono prywatne punkty końcowe w kilku regionach, zalecamy użycie oddzielnej grupy zasobów w każdym regionie i umieść w nim sieć wirtualną i skojarzoną prywatną strefę DNS. Ta konfiguracja uniemożliwia również nieprzewidywalne rozpoznawanie nazw DNS spowodowane przez udostępnianie tej samej prywatnej strefy DNS.

### <a name="manually-configure-dns-records"></a>Ręczne konfigurowanie rekordów DNS

W niektórych scenariuszach może być konieczne ręczne skonfigurowanie rekordów DNS w strefie prywatnej zamiast korzystania ze strefy prywatnej udostępnianej przez platformę Azure. Pamiętaj, aby utworzyć rekordy dla każdego z następujących punktów końcowych: punktu końcowego rejestru, punktu końcowego danych rejestru i punktu końcowego danych dla dowolnej dodatkowej repliki regionalnej. Jeśli nie skonfigurowano wszystkich rekordów, rejestr może być nieosiągalny.

> [!IMPORTANT]
> Jeśli później dodasz nową replikę, musisz ręcznie dodać nowy rekord DNS dla punktu końcowego danych w tym regionie. Jeśli na przykład utworzysz replikę *myregistry* w lokalizacji northeurope, dodaj rekord dla `myregistry.northeurope.data.azurecr.io` .

Nazwy FQDN i prywatne adresy IP potrzebne do utworzenia rekordów DNS są skojarzone z interfejsem sieciowym prywatnego punktu końcowego. Te informacje można uzyskać przy użyciu interfejsu wiersza polecenia platformy Azure lub portalu:

* Za pomocą interfejsu wiersza polecenia platformy Azure uruchom [polecenie az network nic show.][az-network-nic-show] Przykładowe polecenia można znaleźć w artykule Get endpoint IP configuration (Uzyskiwanie konfiguracji [adresu IP punktu końcowego)](#get-endpoint-ip-configuration)we wcześniejszej wersji tego artykułu.

* W portalu przejdź do prywatnego punktu końcowego i wybierz pozycję **Konfiguracja DNS.**

Po utworzeniu rekordów DNS upewnij się, że nazwy FQNS rejestru są prawidłowo rozpoznane jako odpowiednie prywatne adresy IP.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli wszystkie zasoby platformy Azure zostały utworzone w tej samej grupie zasobów i nie są już potrzebne, możesz opcjonalnie usunąć zasoby za pomocą pojedynczego polecenia [az group delete:](/cli/azure/group)

```azurecli
az group delete --name $RESOURCE_GROUP
```

Aby wyczyścić zasoby w portalu, przejdź do grupy zasobów. Po załadowaniu grupy zasobów kliknij pozycję Usuń **grupę zasobów,** aby usunąć grupę zasobów i przechowywane w tej grupie zasoby.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o Private Link, zobacz [Azure Private Link](../private-link/private-link-overview.md) dokumentacji.

* Jeśli musisz skonfigurować reguły dostępu do rejestru zza zapory klienta, zobacz Configure rules to access an Azure container registry behind a firewall (Konfigurowanie reguł dostępu do rejestru kontenerów platformy [Azure za zaporą).](container-registry-firewall-access-rules.md)

* [Rozwiązywanie problemów z łącznością z prywatnym punktem końcowym platformy Azure](../private-link/troubleshoot-private-endpoint-connectivity.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az_acr_private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az_acr_private_endpoint_connection_approve
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az_network_private_endpoint_create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az_network_private_endpoint_show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az_network_private_dns_zone_create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_add_record
[az-network-nic-show]: /cli/azure/network/nic#az_network_nic_show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
