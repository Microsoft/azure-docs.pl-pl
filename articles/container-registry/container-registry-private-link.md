---
title: Konfigurowanie linku prywatnego
description: Skonfiguruj prywatny punkt końcowy w rejestrze kontenerów i Włącz dostęp za pośrednictwem prywatnego linku w lokalnej sieci wirtualnej. Prywatny dostęp do linków jest funkcją warstwy usługi Premium.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 6bea4b2a6bedeac9dd0ff36631ba46adf4be4f8f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148477"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Połącz się prywatnie z usługą Azure Container Registry przy użyciu prywatnego linku platformy Azure


Ogranicz dostęp do rejestru przez przypisanie prywatnych adresów IP do punktów końcowych rejestru i użycie [linku prywatnego platformy Azure](../private-link/private-link-overview.md). Ruch sieciowy między klientami w sieci wirtualnej a prywatnymi punktami końcowymi rejestru przechodzą przez sieć wirtualną i prywatny link w sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Link prywatny umożliwia również dostęp do rejestru prywatnego z zasobów lokalnych za pośrednictwem prywatnej komunikacji równorzędnej [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) lub [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

[Ustawienia DNS](../private-link/private-endpoint-overview.md#dns-configuration) dla prywatnych punktów końcowych rejestru można skonfigurować tak, aby ustawienia były rozpoznawane jako prywatny adres IP przydzielony do rejestru. Dzięki konfiguracji DNS klienci i usługi w sieci mogą nadal uzyskiwać dostęp do rejestru w w pełni kwalifikowanej nazwie domeny, takiej jak *myregistry.azurecr.IO*. 

Ta funkcja jest dostępna w warstwie usługi kontenera **Premium** . Obecnie można skonfigurować maksymalnie 10 prywatnych punktów końcowych dla rejestru. Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry warstw](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aby skorzystać z kroków interfejsu wiersza polecenia platformy Azure w tym artykule, zaleca się użycie interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli]. Lub Uruchom w [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Jeśli nie masz jeszcze rejestru kontenerów, utwórz go (wymagana warstwa Premium) i [zaimportuj](container-registry-import-images.md) przykładowy obraz, taki jak `hello-world` z usługi Docker Hub. Na przykład użyj [Azure Portal][quickstart-portal] lub [interfejsu wiersza polecenia platformy Azure][quickstart-cli] , aby utworzyć rejestr.
* Aby skonfigurować dostęp do rejestru przy użyciu prywatnego linku w innej subskrypcji platformy Azure, należy zarejestrować dostawcę zasobów dla Azure Container Registry w tej subskrypcji. Na przykład:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Przykłady interfejsu wiersza polecenia platformy Azure w tym artykule używają następujących zmiennych środowiskowych. Zastąp wartości odpowiednie dla danego środowiska. Wszystkie przykłady są sformatowane dla powłoki bash:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Konfigurowanie linku prywatnego — interfejs wiersza polecenia

### <a name="get-network-and-subnet-names"></a>Pobieranie nazw sieci i podsieci

Jeśli ich nie masz, będziesz potrzebować nazw sieci wirtualnej i podsieci w celu skonfigurowania prywatnego linku. W tym przykładzie należy użyć tej samej podsieci dla maszyny wirtualnej i prywatnego punktu końcowego rejestru. Jednak w wielu scenariuszach można skonfigurować punkt końcowy w oddzielnej podsieci. 

Podczas tworzenia maszyny wirtualnej platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej jest oparta na nazwie maszyny wirtualnej. Na przykład jeśli nazwasz maszyny wirtualnej *myDockerVM*, domyślną nazwą sieci wirtualnej jest *myDockerVMVNET*, z podsiecią o nazwie *myDockerVMSubnet*. Ustaw te wartości w zmiennych środowiskowych, uruchamiając polecenie [AZ Network VNET list][az-network-vnet-list] :

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

[Wyłącz zasady sieciowe](../private-link/disable-private-endpoint-network-policy.md) , takie jak sieciowe grupy zabezpieczeń w podsieci dla prywatnego punktu końcowego. Zaktualizuj konfigurację podsieci przy użyciu [AZ Network VNET Subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Skonfiguruj prywatną strefę DNS

Utwórz [prywatną strefę DNS](../dns/private-dns-privatednszone.md) dla domeny prywatnej usługi Azure Container Registry. W kolejnych krokach utworzysz rekordy DNS dla domeny rejestru w tej strefie DNS.

Aby użyć strefy prywatnej do zastąpienia domyślnego rozpoznawania nazw DNS dla rejestru kontenerów platformy Azure, strefa musi mieć nazwę **privatelink.azurecr.IO**. Uruchom następujące polecenie [AZ Network Private-DNS Zone Create][az-network-private-dns-zone-create] , aby utworzyć strefę prywatną:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Tworzenie linku skojarzenia

Uruchom [AZ Network Private-DNS link VNET Create][az-network-private-dns-link-vnet-create] , aby skojarzyć strefę prywatną z siecią wirtualną. Ten przykład tworzy łącze o nazwie *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Tworzenie prywatnego punktu końcowego rejestru

W tej sekcji Utwórz prywatny punkt końcowy rejestru w sieci wirtualnej. Najpierw Pobierz identyfikator zasobu rejestru:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Uruchom polecenie [AZ Network Private-Endpoint Create][az-network-private-endpoint-create] , aby utworzyć prywatny punkt końcowy rejestru.

Poniższy przykład tworzy *połączenie*punktów końcowych *myPrivateEndpoint* i połączenia z usługą. Aby określić zasób rejestru kontenerów dla punktu końcowego, należy przekazać `--group-ids registry` :

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

### <a name="get-private-ip-addresses"></a>Pobierz prywatne adresy IP

Uruchom [AZ Network Private-Endpoint show][az-network-private-endpoint-show] , aby wysłać zapytanie do punktu końcowego dla identyfikatora interfejsu sieciowego:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Skojarzona z interfejsem sieciowym w tym przykładzie są dwa prywatne adresy IP dla rejestru kontenerów: jeden dla samego rejestru i jeden dla punktu końcowego danych rejestru. Następujące [AZ Resource show][az-resource-show] Commands Pobierz prywatne adresy IP dla rejestru kontenerów i punkt końcowy danych rejestru:

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> Jeśli rejestr ma [replikację geograficzną](container-registry-geo-replication.md), należy wykonać zapytanie o dodatkowy punkt końcowy danych dla każdej repliki rejestru.

### <a name="create-dns-records-in-the-private-zone"></a>Tworzenie rekordów DNS w strefie prywatnej

Następujące polecenia tworzą rekordy DNS w strefie prywatnej dla punktu końcowego rejestru i jego punktu końcowego danych. Na przykład jeśli w regionie *westeurope* istnieje rejestr o nazwie Moje *rejestry* , nazwy punktów końcowych to `myregistry.azurecr.io` i `myregistry.westeurope.data.azurecr.io` . 

> [!NOTE]
> Jeśli rejestr ma [replikację geograficzną](container-registry-geo-replication.md), Utwórz dodatkowe rekordy DNS dla każdego repliky adres IP punktu końcowego danych.

Najpierw uruchom [AZ Network Private-DNS record-Set a Create][az-network-private-dns-record-set-a-create] , aby utworzyć puste zestawy rekordów dla punktu końcowego rejestru i punktu końcowego danych:

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

Uruchom polecenie [AZ Network Private-DNS record-Set a Add-Record][az-network-private-dns-record-set-a-add-record] , aby utworzyć rekordy a dla punktu końcowego rejestru i punktu końcowego danych:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

Link prywatny jest teraz skonfigurowany i gotowy do użycia.

## <a name="set-up-private-link---portal"></a>Skonfiguruj prywatny link — Portal

Skonfiguruj prywatny link podczas tworzenia rejestru lub Dodaj prywatny link do istniejącego rejestru. W poniższych krokach założono, że masz już skonfigurowaną sieć wirtualną i podsieć z maszyną wirtualną do testowania. Możesz również [utworzyć nową sieć wirtualną i podsieć](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Tworzenie prywatnego punktu końcowego — Nowy Rejestr

1. Podczas tworzenia rejestru w portalu na karcie **podstawowe** w obszarze **jednostka SKU**wybierz pozycję **Premium**.
1. Wybierz kartę **Sieć** .
1. W obszarze **łączność sieciowa**wybierz pozycję **prywatny punkt końcowy**  >  **+ Dodaj**.
1. Wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wprowadź nazwę istniejącej grupy lub Utwórz nową.|
    | Nazwa | Wprowadź unikatową nazwę. |
    | Układ zasobów podrzędnych |Wybierz **Rejestr**|
    | **Sieć** | |
    | Sieć wirtualna| Wybierz sieć wirtualną, w której wdrożono maszynę wirtualną, taką jak *myDockerVMVNET*. |
    | Podsieć | Wybierz podsieć, na przykład *myDockerVMSubnet* , w której wdrożono maszynę wirtualną. |
    |**Integracja Prywatna strefa DNS**||
    |Integruj z prywatną strefą DNS |Wybierz pozycję **Tak**. |
    |Prywatna strefa DNS |Wybierz *(nowy) privatelink.azurecr.IO* |
    |||
1. Skonfiguruj pozostałe ustawienia rejestru, a następnie wybierz pozycję **Przegląd + Utwórz**.

  ![Tworzenie rejestru przy użyciu prywatnego punktu końcowego](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Tworzenie prywatnego punktu końcowego — istniejący rejestr

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **Ustawienia**wybierz pozycję **Sieć**.
1. Na karcie **prywatne punkty końcowe** wybierz pozycję **+ prywatny punkt końcowy**.
1. Na karcie **podstawy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wprowadź nazwę istniejącej grupy lub Utwórz nową.|
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź nazwę. |
    |Region|Wybierz region.|
    |||
5. Wybierz pozycję **Dalej: zasób**.
6. Wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu**.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. ContainerRegistry/rejestry**. |
    | Zasób |Wybierz nazwę rejestru|
    |Podzasób docelowy |Wybierz **Rejestr**|
    |||
7. Wybierz pozycję **Dalej: Konfiguracja**.
8. Wprowadź lub wybierz informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Sieć**| |
    | Sieć wirtualna| Wybierz sieć wirtualną, w której wdrożono maszynę wirtualną, taką jak *myDockerVMVNET*. |
    | Podsieć | Wybierz podsieć, na przykład *myDockerVMSubnet* , w której wdrożono maszynę wirtualną. |
    |**Integracja Prywatna strefa DNS**||
    |Integruj z prywatną strefą DNS |Wybierz pozycję **Tak**. |
    |Prywatna strefa DNS |Wybierz *(nowy) privatelink.azurecr.IO* |
    |||

1. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację. 
2. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

Po utworzeniu prywatnego punktu końcowego ustawienia DNS w strefie prywatnej są wyświetlane na stronie **prywatne punkty końcowe** w portalu:

1. W portalu przejdź do rejestru kontenerów, a następnie wybierz pozycję **ustawienia > sieci**.
1. Na karcie **prywatne punkty końcowe** wybierz utworzony przez siebie prywatny punkt końcowy.
1. Na stronie **Przegląd** przejrzyj ustawienia łączy i niestandardowe ustawienia DNS.

  ![Ustawienia DNS punktu końcowego](./media/container-registry-private-link/private-endpoint-overview.png)

Link prywatny jest teraz skonfigurowany i gotowy do użycia.

## <a name="disable-public-access"></a>Wyłącz dostęp publiczny

W przypadku wielu scenariuszy należy wyłączyć dostęp do rejestru z sieci publicznych. Ta konfiguracja uniemożliwia klientom spoza sieci wirtualnej osiąganie punktów końcowych rejestru. 

### <a name="disable-public-access---cli"></a>Wyłącz dostęp publiczny — interfejs wiersza polecenia

Aby wyłączyć dostęp publiczny za pomocą interfejsu wiersza polecenia platformy Azure, uruchom polecenie [AZ ACR Update][az-acr-update] i ustaw wartość `--public-network-enabled` `false` . 

> [!NOTE]
> `public-network-enabled`Argument wymaga interfejsu wiersza polecenia platformy Azure 2.6.0 lub nowszego. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Wyłącz dostęp publiczny — Portal

1. W portalu przejdź do rejestru kontenerów, a następnie wybierz pozycję **ustawienia > sieci**.
1. Na karcie **dostęp publiczny** w obszarze **Zezwalaj na dostęp do sieci publicznej**wybierz pozycję **wyłączone**. Następnie wybierz pozycję **Zapisz**.

## <a name="validate-private-link-connection"></a>Weryfikowanie połączenia prywatnego linku

Należy sprawdzić, czy zasoby w podsieci prywatnego punktu końcowego łączą się z rejestrem za pośrednictwem prywatnego adresu IP i mają poprawną integrację prywatnej strefy DNS.

Aby sprawdzić poprawność połączenia z linkiem prywatnym, Użyj protokołu SSH do maszyny wirtualnej skonfigurowanej w sieci wirtualnej.

Uruchom narzędzie, takie jak `nslookup` lub, `dig` Aby wyszukać adres IP rejestru za pośrednictwem prywatnego linku. Na przykład:

```bash
dig $REGISTRY_NAME.azurecr.io
```

Przykładowe dane wyjściowe przedstawiają adres IP rejestru w przestrzeni adresowej podsieci:

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

Porównaj ten wynik z publicznym adresem IP w `dig` danych wyjściowych dla tego samego rejestru przez publiczny punkt końcowy:

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

### <a name="registry-operations-over-private-link"></a>Operacje na rejestrze za pośrednictwem prywatnego linku

Sprawdź również, czy można wykonywać operacje na rejestrze z maszyny wirtualnej w podsieci. Nawiąż połączenie SSH z maszyną wirtualną, a następnie uruchom polecenie [AZ ACR login][az-acr-login] , aby zalogować się do rejestru. W zależności od konfiguracji maszyny wirtualnej może być konieczne dodanie do nich prefiksów następujących poleceń `sudo` .

```bash
az acr login --name $REGISTRY_NAME
```

Wykonywanie operacji rejestru, takich jak `docker pull` pobranie przykładowego obrazu z rejestru. Zamień `hello-world:v1` na obraz i tag odpowiedni dla rejestru, poprzedzony prefiksem nazwa serwera logowania rejestru (wszystkie małe litery):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Platforma Docker pomyślnie ściąga obraz do maszyny wirtualnej.

## <a name="manage-private-endpoint-connections"></a>Zarządzanie połączeniami prywatnego punktu końcowego

Zarządzanie połączeniami prywatnego punktu końcowego rejestru przy użyciu Azure Portal lub za pomocą poleceń w grupie poleceń [AZ ACR Private-Endpoint-Connection][az-acr-private-endpoint-connection] . Operacje obejmują Zatwierdź, Usuń, Wyświetl, Odrzuć lub Pokaż szczegóły połączeń prywatnych punktów końcowych rejestru.

Na przykład aby wyświetlić listę połączeń prywatnych punktów końcowych rejestru, uruchom polecenie [AZ ACR Private-Endpoint-Connection list][az-acr-private-endpoint-connection-list] . Na przykład:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Po skonfigurowaniu połączenia prywatnego punktu końcowego za pomocą kroków opisanych w tym artykule rejestr automatycznie akceptuje połączenia od klientów i usług, które mają uprawnienia usługi Azure RBAC w rejestrze. Można skonfigurować punkt końcowy, aby wymagał ręcznego zatwierdzania połączeń. Aby uzyskać informacje na temat zatwierdzania i odrzucania połączeń prywatnych punktów końcowych, zobacz [Zarządzanie połączeniem prywatnego punktu końcowego](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Dodawanie rekordów strefy dla replik

Jak pokazano w tym artykule, podczas dodawania prywatnego połączenia punktu końcowego z rejestrem należy utworzyć rekordy DNS w `privatelink.azurecr.io` strefie dla rejestru i jego punktów końcowych danych w regionach, w których rejestr jest [replikowany](container-registry-geo-replication.md). 

Jeśli później dodasz nową replikę, musisz ręcznie dodać nowy rekord strefy dla punktu końcowego danych w tym regionie. Jeśli na przykład utworzysz replikę *rejestru* w lokalizacji *northeurope* , Dodaj rekord strefy dla `myregistry.northeurope.data.azurecr.io` . Aby uzyskać instrukcje, zobacz [tworzenie rekordów DNS w strefie prywatnej](#create-dns-records-in-the-private-zone) w tym artykule.

## <a name="dns-configuration-options"></a>Opcje konfiguracji DNS

Prywatny punkt końcowy w tym przykładzie integruje się z prywatną strefą DNS skojarzoną z podstawową siecią wirtualną. Ta konfiguracja używa usługi DNS udostępnionej na platformie Azure bezpośrednio w celu rozpoznania publicznej nazwy FQDN rejestru jako prywatnego adresu IP w sieci wirtualnej. 

Link prywatny obsługuje dodatkowe scenariusze konfiguracji DNS, które używają strefy prywatnej, w tym z niestandardowymi rozwiązaniami DNS. Na przykład może istnieć niestandardowe rozwiązanie DNS wdrożone w sieci wirtualnej lub lokalnie w sieci łączącej się z siecią wirtualną przy użyciu bramy sieci VPN. Aby rozwiązać publiczną nazwę FQDN rejestru do prywatnego adresu IP w tych scenariuszach, należy skonfigurować usługę przesyłania dalej na poziomie serwera do usługi Azure DNS (168.63.129.16). Dokładne opcje konfiguracji i kroki zależą od istniejących sieci i systemu DNS. Aby zapoznać się z przykładami, zobacz [Konfiguracja usługi DNS dla prywatnego punktu końcowego platformy Azure](../private-link/private-endpoint-dns.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli wszystkie zasoby platformy Azure zostały utworzone w tej samej grupie zasobów i nie będą już potrzebne, możesz opcjonalnie usunąć zasoby za pomocą jednego polecenia [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name $RESOURCE_GROUP
```

Aby wyczyścić zasoby w portalu, przejdź do grupy zasobów. Po załadowaniu grupy zasobów kliknij pozycję **Usuń grupę zasobów** , aby usunąć grupę zasobów i przechowywane w niej zasoby.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat linku prywatnego, zapoznaj się z dokumentacją [prywatnego linku do platformy Azure](../private-link/private-link-overview.md) .
* Jeśli konieczne jest skonfigurowanie reguł dostępu do rejestru za zaporą klienta, zobacz [Konfigurowanie reguł dostępu do usługi Azure Container Registry za zaporą](container-registry-firewall-access-rules.md).

<!-- LINKS - external -->
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
[az-acr-update]: /cli/azure/acr#az-acr-update
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
