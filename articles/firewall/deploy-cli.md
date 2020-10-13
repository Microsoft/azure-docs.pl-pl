---
title: Wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule dowiesz się, jak wdrożyć i skonfigurować zaporę platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 7f00b57edb37cc5bb5c8340663d619e526c2eacb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89075430"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Kontrolowanie dostępu do sieciowego ruchu wychodzącego jest ważną częścią ogólnego planu zabezpieczeń sieci. Na przykład możesz chcieć ograniczyć dostęp do witryn sieci Web. Można też ograniczyć liczbę wychodzących adresów IP i portów, do których można uzyskać dostęp.

Jednym ze sposobów kontrolowania dostępu do sieciowego ruchu wychodzącego z podsieci platformy Azure jest użycie usługi Azure Firewall. Za pomocą usługi Azure Firewall można skonfigurować następujące reguły:

* Reguły aplikacji, które definiują w pełni kwalifikowane nazwy domen (FQDN), do których można uzyskać dostęp z podsieci. Nazwa FQDN może również [zawierać wystąpienia SQL](sql-fqdn-filtering.md).
* Reguły sieci, które definiują adres źródłowy, protokół, port docelowy i adres docelowy.

Ruch sieciowy podlega skonfigurowanym regułom zapory podczas kierowania ruchu sieciowego do zapory jako bramy domyślnej podsieci.

W tym artykule utworzysz uproszczoną pojedynczą sieć wirtualną z trzema podsieciami w celu łatwego wdrożenia. W przypadku wdrożeń produkcyjnych zaleca się [model Hub i szprych](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) . Zapora znajduje się w własnej sieci wirtualnej. Serwery obciążenia znajdują się w sieci wirtualnych komunikacji równorzędnej w tym samym regionie co co najmniej jedna podsieć.

* **AzureFirewallSubnet** — w tej podsieci znajduje się zapora.
* **Workload-SN** — w tej podsieci znajduje się serwer obciążeń. Ruch sieciowy tej podsieci przechodzi przez zaporę.
* **Jump-SN** — w tej podsieci znajduje się serwer przesiadkowy. Serwer przesiadkowy ma publiczny adres IP, z którym możesz nawiązać połączenie przy użyciu pulpitu zdalnego. Stamtąd możesz połączyć się (przy użyciu innego pulpitu zdalnego) z serwerem obciążeń.

![Infrastruktura sieci samouczka](media/tutorial-firewall-rules-portal/Tutorial_network.png)

W tym artykule omówiono sposób wykonywania następujących zadań:

* Konfigurowanie testowego środowiska sieciowego
* Wdrażanie zapory
* Tworzenie trasy domyślnej
* Skonfiguruj regułę aplikacji, aby zezwolić na dostęp do www.google.com
* Konfigurowanie reguły sieci w celu umożliwienia dostępu do zewnętrznych serwerów DNS
* Testowanie zapory

Jeśli wolisz, możesz wykonać tę procedurę przy użyciu [Azure Portal](tutorial-firewall-deploy-portal.md) lub [Azure PowerShell](deploy-ps.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, uruchom interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby znaleźć wersję, uruchom polecenie **AZ--Version**. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

Zainstaluj rozszerzenie zapory platformy Azure:

```azurecli-interactive
az extension add -n azure-firewall
```


## <a name="set-up-the-network"></a>Konfigurowanie sieci

Najpierw utwórz grupę zasobów zawierającą zasoby wymagane do wdrożenia zapory. Następnie utwórz sieć wirtualną, podsieci i serwery do obsługi testowania.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów zawiera wszystkie zasoby wdrożenia.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Ta sieć wirtualna ma trzy podsieci.

> [!NOTE]
> Rozmiar podsieci AzureFirewallSubnet to/26. Aby uzyskać więcej informacji o rozmiarze podsieci, zobacz [często zadawane pytania dotyczące zapory platformy Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Teraz utwórz maszyny wirtualne przesiadkową i obciążeń, a następnie umieść je w odpowiednich podsieciach.
Po wyświetleniu monitu wpisz hasło dla maszyny wirtualnej.

Utwórz Srv-Jump maszynę wirtualną.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Utwórz kartę sieciową dla Srv-Work z określonymi adresami IP serwera DNS i bez publicznego adresu IP do przetestowania.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Teraz Utwórz maszynę wirtualną obciążenia.
Po wyświetleniu monitu wpisz hasło dla maszyny wirtualnej.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

Teraz Wdróż zaporę w sieci wirtualnej.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Zanotuj prywatny adres IP. Użyjesz go później podczas tworzenia trasy domyślnej.

## <a name="create-a-default-route"></a>Tworzenie trasy domyślnej

Tworzenie tabeli z wyłączonym propagacją tras BGP

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Utwórz trasę.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Kojarzenie tabeli tras z podsiecią

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Konfigurowanie reguły aplikacji

Reguła aplikacji zezwala na dostęp wychodzący do www.google.com.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone. Te nazwy FQDN są specyficzne dla platformy i nie można ich używać do innych celów. Aby uzyskać więcej informacji, zobacz [Infrastrukturalne nazwy FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurowanie reguły sieci

Reguła sieci umożliwia dostęp wychodzący do dwóch adresów IP na porcie 53 (DNS).

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>Testowanie zapory

Teraz Przetestuj zaporę, aby upewnić się, że działa zgodnie z oczekiwaniami.

1. Zanotuj prywatny adres IP dla maszyny wirtualnej **SRV** :

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Połącz pulpit zdalny z maszyną wirtualną z **przeskokiem SRV** i zaloguj się. W tym miejscu Otwórz połączenie pulpitu zdalnego z prywatnym adresem IP **SRV** i zaloguj się.

3. W obszarze **SRV (Pracuj**) Otwórz okno programu PowerShell i uruchom następujące polecenia:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Oba polecenia powinny zwracać odpowiedzi, pokazując, że zapytania DNS są przekazywane przez zaporę.

1. Uruchom następujące polecenia:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   `www.google.com`Żądania powinny się zakończyć pomyślnie, a `www.microsoft.com` żądania powinny kończyć się niepowodzeniem. Pokazuje to, że reguły zapory działają zgodnie z oczekiwaniami.

Teraz sprawdzono, że reguły zapory działają:

* Możesz rozpoznać nazwy DNS przy użyciu skonfigurowanego zewnętrznego serwera DNS.
* Możesz przejść do jednej z dozwolonych nazw FQDN, ale nie do innych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zasoby zapory można zachować w następnym samouczku lub, jeśli nie są już potrzebne, Usuń grupę zasobów **test-PD-RG** , aby usunąć wszystkie zasoby związane z zaporą:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Następne kroki

* [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
