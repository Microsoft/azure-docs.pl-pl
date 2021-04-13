---
title: Ograniczanie ruchu wychodzącego w klastrze Azure Red Hat OpenShift (ARO)
description: Dowiedz się, jakie porty i adresy są wymagane do kontrolowania ruchu wychodzącego w Azure Red Hat OpenShift (ARO)
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: 24c4686306aff9d84fe7bf74ddfdccff987244d9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368689"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>Kontrolowanie ruchu wychodzącego dla klastra Azure Red Hat OpenShift (ARO) (wersja zapoznawcza)

Ten artykuł zawiera niezbędne szczegóły, które umożliwiają zabezpieczenie ruchu wychodzącego z klastra Azure Red Hat OpenShift (ARO). Zawiera on wymagania dotyczące klastra dla podstawowego wdrożenia usługi ARO oraz dodatkowe wymagania dotyczące opcjonalnego oprogramowania Red Hat i składników innych firm. Na [końcu](#private-aro-cluster-setup) zostanie podany przykład konfigurowania tych wymagań za pomocą Azure Firewall. Należy pamiętać, że te informacje można zastosować do Azure Firewall lub dowolnej metody lub urządzenia ograniczenia ruchu wychodzącego.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że tworzysz nowy klaster. Jeśli potrzebujesz podstawowego klastra ARO, zobacz przewodnik [Szybki start usługi ARO](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster).

> [!IMPORTANT]
> Funkcje usługi ARO w wersji zapoznawczej są dostępne w trybie samoobsługowym z możliwością rezygnacji. Wersje zapoznawcze są udostępniane w sposób "bez ich wyświetlania" i "zgodnie z dostępnymi" i są wykluczone z umów dotyczących poziomu usług i ograniczonej gwarancji. Wersje zapoznawcze ARO są częściowo objęte pomocą techniczną dla klientów na zasadzie najlepszego nakładu pracy.

## <a name="minimum-required-fqdn--application-rules"></a>Minimalna wymagana FQDN/reguły aplikacji

Ta lista jest oparta na liście FQDN znalezionych w dokumentów OpenShift tutaj: https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html

Wymagane są następujące reguły FQDN/aplikacji:

| Docelowa fQDN | Port | Zastosowanie |
| ----------- | ----------- | ------------- |
| **`quay.io`** | **HTTPS:443** | Obowiązkowy element instalacji używany przez klaster. Jest on używany przez klaster do pobierania obrazów kontenerów platformy. |
| **`registry.redhat.io`** | **HTTPS:443** | Obowiązkowe w przypadku dodatków podstawowych. Jest on używany przez klaster do pobierania podstawowych składników, takich jak narzędzia deweloperskie, dodatki oparte na operatorach i obrazy kontenerów dostarczane przez oprogramowanie Red Hat.
| **`mirror.openshift.com`** | **HTTPS:443** | Jest to wymagane w środowisku VDI lub laptopie, aby uzyskać dostęp do dublowanych obrazów i zawartości instalacyjnej. W klastrze wymagane jest pobranie sygnatur wydania platformy, aby wiedzieć, jakie obrazy należy ściągnąć z quay.io. |
| **`api.openshift.com`** | **HTTPS:443** | Wymagane przez klaster do sprawdzania, czy są dostępne aktualizacje przed pobraniem podpisów obrazów. |
| **`arosvc.azurecr.io`** | **HTTPS:443** | Wewnętrzny rejestr prywatny dla operatorów ARO.  Wymagane, jeśli nie zezwalasz na punkty końcowe usługi Microsoft.ContainerRegistry w podsieciach. |
| **`management.azure.com`** | **HTTPS:443** | Jest on używany przez klaster do uzyskiwania dostępu do interfejsów API platformy Azure. |
| **`login.microsoftonline.com`** | **HTTPS:443** | Jest on używany przez klaster do uwierzytelniania na platformie Azure. |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS:443** | Jest to używane na potrzeby monitorowania w Serwisie Microsoft Geneva, dzięki czemu zespół ARO może monitorować klastry klienta. |
| **`*.blob.core.windows.net`** | **HTTPS:443** | Jest to używane na potrzeby monitorowania w Serwisie Microsoft Geneva, dzięki czemu zespół ARO może monitorować klastry klienta. |
| **`*.servicebus.windows.net`** | **HTTPS:443** | Jest to używane na potrzeby monitorowania w Serwisie Microsoft Geneva, dzięki czemu zespół ARO może monitorować klastry klienta. |
| **`*.table.core.windows.net`** | **HTTPS:443** | Jest on używany na potrzeby monitorowania w Firmie Microsoft w Celu monitorowania klastrów klienta przez zespół ARO. |

> [!NOTE] 
> Wielu klientów ujawniających obiekty *.blob, *.table i inne duże przestrzenie adresowe stwarza potencjalny problem z eksfiltracją danych. Warto rozważyć użycie zapory [openshift Egress w](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html) celu ochrony aplikacji wdrożonych w klastrze przed dotarciem do tych miejsc docelowych i używaniem Azure Private Link określonych potrzeb aplikacji.

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>Pełna lista wymaganych i opcjonalnych sieci FQNS

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>PIERWSZA GRUPA: INSTALOWANIE I POBIERANIE PAKIETÓW I NARZĘDZI

- **`quay.io`**: obowiązkowy element instalacji używany przez klaster. Jest on używany przez klaster do pobierania obrazów kontenerów platformy.
- **`registry.redhat.io`**: obowiązkowe w przypadku dodatków podstawowych. Jest on używany przez klaster do pobierania podstawowych składników, takich jak narzędzia deweloperskie, dodatki oparte na operatorach lub obrazy kontenerów dostarczane przez system Red Hat, takie jak oprogramowanie pośredniczące, uniwersalny obraz podstawowy...
- **`sso.redhat.com`**: ten jest wymagany w środowisku VDI lub laptopie w celu nawiązania połączenia z cloud.redhat.com. Jest to witryna, w której można pobrać klucz tajny ściągania i skorzystać z niektórych rozwiązań SaaS, które oferujemy w red hat, aby ułatwić monitorowanie subskrypcji, spisu klastrów, raportowania obciążenia zwrotnego, między innymi.
- **`openshift.org`**: ten jest wymagany w środowisku VDI lub laptopie w celu nawiązania połączenia w celu pobrania obrazów SYSTEMU RH CoreOS, ale na platformie Azure są one wybierane z platformy handlowej, nie ma potrzeby pobierania obrazów systemu operacyjnego.

---

### <a name="second-group-telemetry"></a>DRUGA GRUPA: TELEMETRIA

Wszystkie te sekcje można zrezygnować, ale zanim będziemy wiedzieć, jak to zrobić, sprawdź, co to jest: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html
- **`cert-api.access.redhat.com`**: użyj w środowisku VDI lub laptop.
- **`api.access.redhat.com`**: użyj w środowisku VDI lub laptop.
- **`infogw.api.openshift.com`**: użyj w środowisku VDI lub laptop.
- **`https://cloud.redhat.com/api/ingress`**: użyj w klastrze operatora szczegółowych informacji, który integruje się z programem aaS Red Hat Insights.
Na platformie OpenShift Container Platform klienci mogą zrezygnować z raportowania informacji o kondycji i użyciu. Jednak połączone klastry umożliwiają oprogramowania Red Hat lepsze reagowanie na problemy i lepsze wsparcie naszych klientów oraz lepsze zrozumienie sposobu uaktualniania klastrów produktów. Sprawdź szczegóły tutaj: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html .

---

### <a name="third-group-cloud-apis"></a>TRZECIA GRUPA: INTERFEJSY API W CHMURZE

- **`management.azure.com`**: jest on używany przez klaster do uzyskiwania dostępu do interfejsów API platformy Azure.

---

### <a name="fourth-group-other-openshift-requirements"></a>CZWARTA GRUPA: INNE WYMAGANIA DOTYCZĄCE OPENSHIFT

- **`mirror.openshift.com`**: ta jest wymagana w środowisku VDI lub laptopie w celu uzyskania dostępu do dublowanych obrazów i zawartości instalacyjnej oraz wymagana w klastrze do pobrania sygnatur wydania platformy używanych przez klaster do uzyskiwania informacji o obrazach do ściągnięcia z quay.io.
- **`storage.googleapis.com/openshift-release`**: Alternatywna lokacja do pobierania sygnatur wydania platformy używana przez klaster do wiedzieć, jakie obrazy należy ściągnąć z quay.io.
- **`*.apps.<cluster_name>.<base_domain>`** (LUB równoważny adres URL ARO): w przypadku zezwalania na domeny jest to stosowane w sieci firmowej w celu uzyskiwania dostępu do aplikacji wdrożonych w platformie OpenShift lub uzyskiwania dostępu do konsoli OpenShift.
- **`api.openshift.com`**: wymagane przez klaster w celu sprawdzenia, czy są dostępne aktualizacje przed pobraniem podpisów obrazów.
- **`registry.access.redhat.com`**: w środowisku VDI lub laptopie wymagany jest dostęp do rejestru w celu pobrania obrazów dewelopera podczas korzystania z narzędzia interfejsu wiersza polecenia ODO. (To narzędzie interfejsu wiersza polecenia jest alternatywnym narzędziem interfejsu wiersza polecenia dla deweloperów, którzy nie znają rozwiązania Kubernetes). https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>PIĄTA GRUPA: MICROSOFT & RED HAT ARO MONITORING SERVICE

- **`login.microsoftonline.com`**: jest to używane przez klaster do uwierzytelniania na platformie Azure.
- **`gcs.prod.monitoring.core.windows.net`**: Jest to używane na potrzeby monitorowania w Serwisie Microsoft Geneva, dzięki czemu zespół ARO może monitorować klastry klienta.
- **`*.blob.core.windows.net`**: Jest to używane na potrzeby monitorowania w Serwisie Microsoft Geneva, dzięki czemu zespół ARO może monitorować klastry klienta.
- **`*.servicebus.windows.net`**: Jest to używane na potrzeby monitorowania w Serwisie Microsoft Geneva, dzięki czemu zespół ARO może monitorować klastry klienta.
- **`*.table.core.windows.net`**: Jest to używane na potrzeby monitorowania w Serwisie Microsoft Geneva, dzięki czemu zespół ARO może monitorować klastry klienta.

## <a name="aro-integrations"></a>Integracje ARO

### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów

Istnieją dwie opcje zapewnienia dostępu do usługi Azure Monitor kontenerów. Możesz zezwolić na tag Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)  lub zapewnić dostęp do wymaganych reguł FQDN/aplikacji.  Poniżej znajdują się [instrukcje](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-azure-redhat4-setup) dotyczące dodawania Azure Monitor do istniejącego klastra ARO.

#### <a name="required-network-rules"></a>Wymagane reguły sieci

Wymagane są następujące reguły FQDN/aplikacji:

| Docelowy punkt końcowy                                                             | Protokół | Port    | Zastosowanie  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [Tag usługi](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Ten punkt końcowy służy do wysyłania danych metryk i dzienników do usług Azure Monitor i Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Wymagana FQDN/reguły aplikacji

Następujące reguły FQDN/aplikacji są wymagane dla klastrów ARO z włączoną Azure Monitor dla kontenerów:

| Nazwa FQDN                                    | Port      | Zastosowanie      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | Ten punkt końcowy jest używany do metryk i monitorowania telemetrii przy użyciu Azure Monitor. |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | Ten punkt końcowy jest używany przez Azure Monitor do pozyskania danych analizy dzienników. |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | Ten punkt końcowy jest używany przez usługę omsagent, która jest używana do uwierzytelniania usługi log analytics. |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | Ten punkt końcowy służy do wysyłania danych metryk do Azure Monitor. |


## <a name="private-aro-cluster-setup"></a>Konfiguracja prywatnego klastra ARO
Celem jest zabezpieczenie klastra ARO przez kierowanie ruchu wychodzącego przez Azure Firewall
### <a name="before"></a>Przed:
![Stary adres](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>Po:
![Po](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>Tworzenie prywatnego klastra usługi ARO

### <a name="set-up-vars-for-your-environment"></a>Konfigurowanie funkcji VARS dla środowiska
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>Dodawanie dwóch pustych podsieci do sieci wirtualnej
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>Wyłącz zasady sieciowe dla Private Link Service w sieci wirtualnej i podsieciach. Jest to wymagane, aby usługa ARO uzyskuje dostęp do klastra i zarządzał tym klastrem.
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>Tworzenie podsieci zapory
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>Tworzenie maszyny wirtualnej hosta przeskoku
### <a name="create-a-jump-subnet"></a>Tworzenie podsieci szybkiego skoku
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>Tworzenie maszyny wirtualnej hosta przeskoku
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Tworzenie klastra usługi Azure Red Hat OpenShift
### <a name="get-a-red-hat-pull-secret-optional"></a>Pobierz klucz tajny ściągania z red hat (opcjonalnie)

Wpis tajny ściągania systemu Red Hat umożliwia klastrowi uzyskiwanie dostępu do rejestrów kontenerów systemu Red Hat wraz z inną zawartością. Ten krok jest opcjonalny, ale zalecany.

1. **[Przejdź do portalu menedżera klastra usługi Red Hat OpenShift i](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) zaloguj się.**

   Musisz zalogować się do konta red hat lub utworzyć nowe konto Red Hat przy użyciu służbowego adresu e-mail i zaakceptować warunki i postanowienia.

2. **Kliknij pozycję Pobierz klucz tajny ściągnięcia.**

Przechowuj zapisany `pull-secret.txt` plik w bezpiecznym miejscu — będzie on używany podczas każdego tworzenia klastra.

Podczas uruchamiania polecenia możesz odwołać się do swojego `az aro create` tajnego ściągnięcie przy użyciu `--pull-secret @pull-secret.txt` parametru . Wykonaj `az aro create` polecenie z katalogu, w którym został zapisany `pull-secret.txt` plik. W przeciwnym razie zastąp `@pull-secret.txt` . `@<path-to-my-pull-secret-file`

Jeśli kopiujesz klucz tajny ściągania lub odwołujesz się do niego w innych skryptach, ten klucz tajny powinien być sformatowany jako prawidłowy ciąg JSON.

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>Tworzenie Azure Firewall

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>Aktualizowanie rozszerzenia Azure Firewall instalacji
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>Tworzenie Azure Firewall i konfigurowanie konfiguracji adresu IP
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>Przechwyć Azure Firewall IP do późniejszego użycia
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>Tworzenie trasy UDR i tabeli routingu dla Azure Firewall
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>Dodawanie reguł aplikacji dla Azure Firewall
Reguła działania openshift na podstawie tej [listy:](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall)
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Opcjonalne reguły dla obrazów platformy Docker:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>Kojarzenie podsieci ARO z WZ
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>Testowanie konfiguracji z serwera przeskoku
Te kroki działają tylko w przypadku dodania reguł dla obrazów platformy Docker. 
### <a name="configure-the-jumpbox"></a>Konfigurowanie serwera przeskoku
Zaloguj się do maszyny wirtualnej jumpbox i zainstaluj `azure-cli` `oc-cli` narzędzia , i `jq` . Aby uzyskać informacje na temat instalacji interfejsu wiersza polecenia openshift, zapoznaj się z portalem klienta oprogramowania Red Hat.
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>Zaloguj się do klastra ARO
Lista poświadczeń klastra:
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
Uzyskiwanie punktu końcowego serwera interfejsu API:
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>Pobieranie interfejsu wiersza polecenia oc do przeskoku
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

Zaloguj się przy użyciu programu `oc login` :
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>Uruchamianie systemu CentOS w celu przetestowania łączności zewnętrznej
Tworzenie zasobnika
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
Po uruchomieniu zasobnika zaimekuj do niego plik exec i przetestuj łączność poza nim.

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>Uzyskiwanie dostępu do konsoli sieci Web klastra prywatnego

### <a name="set-up-ssh-forwards-commands"></a>Konfigurowanie poleceń przesyłania dalej SSH

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>Modyfikowanie pliku hostów itp. na komputerze lokalnym
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>Użyj sshuttle jako innej opcji

[SSHuttle](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```