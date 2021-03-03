---
title: Wdrażanie OpenShift kontenera platform 3,11 na platformie Azure
description: Wdróż OpenShift kontenerów platformy 3,11 na platformie Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 054fb2ffc65b44d5436282eab5327f0facf39c06
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671184"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Wdrażanie OpenShift kontenera platform 3,11 na platformie Azure

Do wdrożenia OpenShift kontenerów platformy 3,11 na platformie Azure można użyć jednej z kilku metod:

- Niezbędne składniki infrastruktury platformy Azure można wdrożyć ręcznie, a następnie skorzystać z [dokumentacji platformy kontenera OpenShift](https://docs.openshift.com/container-platform).
- Można również użyć istniejącego [szablonu Menedżer zasobów](https://github.com/Microsoft/openshift-container-platform/) , który upraszcza wdrażanie klastra platformy kontenerów OpenShift.
- Innym rozwiązaniem jest użycie [oferty portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

W przypadku wszystkich opcji wymagana jest subskrypcja Red Hat. Podczas wdrażania wystąpienie Red Hat Enterprise Linux jest zarejestrowane w ramach subskrypcji Red Hat i dołączone do identyfikatora puli zawierającego uprawnienia dla platformy kontenera OpenShift.
Upewnij się, że masz prawidłową nazwę użytkownika, hasło i Identyfikator puli usługi Red Hat Subscription Manager (RHSM). Możesz użyć klucza aktywacji, identyfikatora organizacji i identyfikatora puli. Możesz sprawdzić te informacje, logując się do https://access.redhat.com .


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Wdrażanie przy użyciu szablonu OpenShift Container platform Menedżer zasobów 3,11

### <a name="private-clusters"></a>Klastry prywatne

Wdrożenie prywatnych klastrów OpenShift wymaga więcej niż nie posiadania publicznego adresu IP skojarzonego z głównym modułem równoważenia obciążenia (konsolą sieci Web) lub usługą równoważenia obciążenia (router).  Klaster prywatny zwykle używa niestandardowego serwera DNS (nie domyślnego Azure DNS), niestandardowej nazwy domeny (takiej jak contoso.com) i wstępnie zdefiniowanych sieci wirtualnych.  W przypadku klastrów prywatnych należy wcześniej skonfigurować sieć wirtualną ze wszystkimi odpowiednimi podsieciami i ustawieniami serwera DNS.  Następnie użyj **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference** i **existingNodeSubnetReference** , aby określić istniejącą podsieć do użycia przez klaster.

Jeśli wybrano prywatny wzorzec (**masterClusterType**= Private), statyczny prywatny adres IP musi być określony dla **masterPrivateClusterIp**.  Ten adres IP zostanie przypisany do frontonu głównego modułu równoważenia obciążenia.  Adres IP musi należeć do zakresu CIDR dla podsieci głównej i nie jest używany.  **masterClusterDnsType** musi być ustawiona na wartość "Custom", a główna nazwa DNS musi być podana dla **masterClusterDns**.  Nazwa DNS musi być zmapowana do statycznego prywatnego adresu IP i zostanie użyta w celu uzyskania dostępu do konsoli programu w węzłach głównych.

W przypadku wybrania routera prywatnego (**routerClusterType**= Private) należy określić statyczny prywatny adres IP dla **routerPrivateClusterIp**.  Ten adres IP zostanie przypisany do frontonu usługi równoważenia obciążenia.  Adres IP musi należeć do zakresu CIDR dla podsieci infrastruktury i nie jest używany.  **routingSubDomainType** musi być ustawiona na wartość "Custom", a symbol wieloznaczny DNS dla routingu musi być podany dla **routingSubDomain**.  

W przypadku wybrania prywatnych wzorców i routera prywatnego należy również wprowadzić nazwę domeny niestandardowej dla **nazwa_domeny**

Po pomyślnym wdrożeniu węzeł bastionu jest jedynym węzłem z publicznym adresem IP, w którym można obsłużyć protokół SSH.  Nawet jeśli węzły główne są skonfigurowane pod kątem dostępu publicznego, nie są one widoczne dla dostępu SSH.

Aby wdrożyć przy użyciu szablonu Menedżer zasobów, należy użyć pliku parametrów w celu dostarczenia parametrów wejściowych. Aby dodatkowo dostosować wdrożenie, rozwidlenie repozytorium GitHub i zmiana odpowiednich elementów.

Niektóre typowe opcje dostosowania obejmują, ale nie są ograniczone do:

- Rozmiar maszyny wirtualnej bastionu (zmienna w azuredeploy.jsna)
- Konwencje nazewnictwa (zmienne w azuredeploy.json)
- OpenShift specyficzne dla klastra, zmodyfikowane za pomocą pliku hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfiguruj plik parametrów

[Szablon platformy kontenera OpenShift](https://github.com/Microsoft/openshift-container-platform) ma wiele gałęzi dostępnych dla różnych wersji platformy kontenera OpenShift.  Na podstawie Twoich potrzeb można wdrożyć bezpośrednio z repozytorium lub utworzyć rozwidlenie repozytorium i wprowadzić niestandardowe zmiany w szablonach lub skryptach przed wdrożeniem.

Użyj `appId` wartości z jednostki usługi utworzonej wcześniej dla `aadClientId` parametru.

W poniższym przykładzie przedstawiono plik parametrów o nazwie azuredeploy.parameters.jsna wszystkich wymaganych danych wejściowych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Zastąp parametry określonymi informacjami.

Różne wersje mogą mieć inne parametry, aby zweryfikować wymagane parametry używanej gałęzi.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy.Parameters.jsna wyjaśnionym pliku

| Właściwość | Opis | Prawidłowe opcje | Wartość domyślna |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | Adres URL artefaktów (JSON, skrypty itp.) |  |  https: \/ /RAW.githubusercontent.com/Microsoft/OpenShift-Container-platform/Master  |
| `location` | Region platformy Azure, do którego mają zostać wdrożone zasoby |  |  |
| `masterVmSize` | Rozmiar głównej maszyny wirtualnej. Wybierz jeden z dozwolonych rozmiarów maszyn wirtualnych wymienionych w azuredeploy.jsw pliku |  | Standardowa_E2s_v3 |
| `infraVmSize` | Rozmiar infrastruktury maszyny wirtualnej. Wybierz jeden z dozwolonych rozmiarów maszyn wirtualnych wymienionych w azuredeploy.jsw pliku |  | Standardowa_D4s_v3 |
| `nodeVmSize` | Rozmiar maszyny wirtualnej węzła aplikacji. Wybierz jeden z dozwolonych rozmiarów maszyn wirtualnych wymienionych w azuredeploy.jsw pliku |  | Standardowa_D4s_v3 |
| `cnsVmSize` | Rozmiar maszyny wirtualnej węzła magazynu natywnego (CN) kontenera. Wybierz jeden z dozwolonych rozmiarów maszyn wirtualnych wymienionych w azuredeploy.jsw pliku |  | Standardowa_E4s_v3 |
| `osImageType` | Obraz RHEL do użycia. defaultgallery: na żądanie; Marketplace: obraz innej firmy | defaultgallery <br> Marketplace | defaultgallery |
| `marketplaceOsImage` | Jeśli `osImageType` jest Marketplace, wprowadź odpowiednie wartości w polu Wydawca, "Oferta", "SKU", "wersja" oferty portalu Marketplace. Ten parametr jest typem obiektu |  |  |
| `storageKind` | Typ magazynu do użycia  | zarządzanych<br> niepodlegającą | zarządzanych |
| `openshiftClusterPrefix` | Prefiks klastra używany do konfigurowania nazw hostów dla wszystkich węzłów.  Od 1 do 20 znaków |  | mycluster |
| `minoVersion` | Pomocnicza wersja OpenShift kontenera platform 3,11 do wdrożenia |  | 69 |
| `masterInstanceCount` | Liczba węzłów głównych do wdrożenia | 1, 3, 5 | 3 |
| `infraInstanceCount` | Liczba węzłów infrastruktury do wdrożenia | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Liczba węzłów do wdrożenia | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Liczba węzłów CNS do wdrożenia | 3, 4 | 3 |
| `osDiskSize` | Rozmiar dysku systemu operacyjnego dla maszyny wirtualnej (w GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Rozmiar dysku danych do dołączenia do węzłów dla woluminu platformy Docker (w GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Rozmiar dysku danych do dołączenia do węzłów CNS do użycia przez glusterfs (w GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nazwa użytkownika administratora dla logowania jednosystemowego (VM) i początkowego użytkownika OpenShift |  | ocpadmin |
| `enableMetrics` | Włącz metryki. Metryki wymagają większej ilości zasobów, aby wybrać odpowiedni rozmiar dla infrastruktury maszyny wirtualnej | true <br> fałsz | fałsz |
| `enableLogging` | Włącz rejestrowanie. Elasticsearch pod wymaga 8 GB pamięci RAM, więc wybierz odpowiedni rozmiar dla infrastruktury maszyny wirtualnej | true <br> fałsz | fałsz |
| `enableCNS` | Włącz magazyn macierzysty kontenera | true <br> fałsz | fałsz |
| `rhsmUsernameOrOrgId` | Nazwa użytkownika lub identyfikator organizacji programu Red Hat Subscription Manager |  |  |
| `rhsmPoolId` | Identyfikator puli Menedżera subskrypcji Red Hat zawierający uprawnienia OpenShift dla węzłów obliczeniowych |  |  |
| `rhsmBrokerPoolId` | Identyfikator puli programu Red Hat Subscription Manager, który zawiera uprawnienia OpenShift dla wzorców i węzłów infrastruktury. Jeśli nie masz innych identyfikatorów puli, wprowadź ten sam Identyfikator puli co "rhsmPoolId" |  |
| `sshPublicKey` | Skopiuj swój klucz publiczny SSH tutaj |  |  |
| `keyVaultSubscriptionId` | Identyfikator subskrypcji subskrypcji zawierającej Key Vault |  |  |
| `keyVaultResourceGroup` | Nazwa grupy zasobów, która zawiera Key Vault |  |  |
| `keyVaultName` | Nazwa utworzonego Key Vault |  |  |
| `enableAzure` | Włącz dostawcę chmury platformy Azure | true <br> fałsz | true |
| `aadClientId` | Identyfikator klienta Azure Active Directory znany również jako identyfikator aplikacji dla jednostki usługi |  |  |
| `domainName` | Nazwa niestandardowej nazwy domeny do użycia (jeśli dotyczy). Ustaw na wartość "none", jeśli nie jest wdrażany w pełni prywatny klaster |  | brak |
| `masterClusterDnsType` | Typ domeny dla konsoli sieci Web OpenShift. wartość "default" spowoduje użycie etykiety DNS publicznego adresu IP infrastruktury Master. element "Custom" umożliwia zdefiniowanie własnej nazwy | default <br> niestandardowe | default |
| `masterClusterDns` | Niestandardowa nazwa DNS, która ma być używana do uzyskiwania dostępu do konsoli sieci Web OpenShift w przypadku wybrania elementu "Custom" dla `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Jeśli zostanie ustawiona na wartość "nipio", `routingSubDomain` użyje NIP.IO.  Użyj elementu "Custom", jeśli masz własną domenę, która ma być używana do routingu | nipio <br> niestandardowe | nipio |
| `routingSubDomain` | Symbol wieloznaczny DNS, który ma być używany na potrzeby routingu w przypadku wybrania elementu "Custom" dla `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Wybierz, czy chcesz użyć istniejącego Virtual Network, czy utworzyć nowy Virtual Network | istniejącego <br> new | new |
| `virtualNetworkResourceGroupName` | Nazwa grupy zasobów dla nowego Virtual Network w przypadku wybrania elementu "New" dla `virtualNetworkNewOrExisting` |  | resourceName (). Name |
| `virtualNetworkName` | Nazwa nowego Virtual Network do utworzenia, jeśli wybrano element "New" dla `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Prefiks adresu nowej sieci wirtualnej |  | 10.0.0.0/14 |
| `masterSubnetName` | Nazwa podsieci głównej |  | mastersubnet |
| `masterSubnetPrefix` | CIDR używany dla podsieci głównej — musi być podzbiorem addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | Nazwa podsieci infrastruktury |  | infrasubnet |
| `infraSubnetPrefix` | CIDR użyty dla podsieci infrastruktury musi być podzbiorem addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | Nazwa podsieci węzła |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR używane dla podsieci węzła — musi być podzbiorem addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Pełne odwołanie do istniejącej podsieci dla węzłów głównych. Niewymagane w przypadku tworzenia nowej sieci wirtualnej/podsieci |  |  |
| `existingInfraSubnetReference` | Pełne odwołanie do istniejącej podsieci dla węzłów infrastruktury. Niewymagane w przypadku tworzenia nowej sieci wirtualnej/podsieci |  |  |
| `existingCnsSubnetReference` | Pełne odwołanie do istniejącej podsieci dla węzłów CNS. Niewymagane w przypadku tworzenia nowej sieci wirtualnej/podsieci |  |  |
| `existingNodeSubnetReference` | Pełne odwołanie do istniejącej podsieci dla węzłów obliczeniowych. Niewymagane w przypadku tworzenia nowej sieci wirtualnej/podsieci |  |  |
| `masterClusterType` | Określ, czy klaster ma używać prywatnych, jak i publicznych węzłów głównych. W przypadku wybrania opcji prywatne węzły główne nie będą uwidaczniane w Internecie za pośrednictwem publicznego adresu IP. Zamiast tego będzie używany prywatny adres IP określony w `masterPrivateClusterIp` | public <br> private | public |
| `masterPrivateClusterIp` | W przypadku wybrania prywatnych węzłów głównych należy określić prywatny adres IP do użycia przez wewnętrzny moduł równoważenia obciążenia dla węzłów głównych. Ten statyczny adres IP musi znajdować się w bloku CIDR dla podsieci głównej i nie jest już używany. Jeśli wybrane są publiczne węzły główne, ta wartość nie zostanie użyta, ale nadal musi być określona |  | 10.1.0.200 |
| `routerClusterType` | Określ, czy klaster ma używać prywatnych, czy publicznych węzłów infrastruktury. W przypadku wybrania opcji prywatne węzły infrastruktury nie będą ujawniane w Internecie za pośrednictwem publicznego adresu IP. Zamiast tego będzie używany prywatny adres IP określony w `routerPrivateClusterIp` | public <br> private | public |
| `routerPrivateClusterIp` | W przypadku wybrania prywatnych węzłów infrastruktury należy określić prywatny adres IP do użycia przez wewnętrzny moduł równoważenia obciążenia dla węzłów infrastruktury. Ten statyczny adres IP musi znajdować się w bloku CIDR dla podsieci infrastruktury i nie jest już używany. Jeśli są wybrane węzły infrastruktury publicznej, ta wartość nie zostanie użyta, ale nadal musi być określona |  | 10.2.0.200 |
| `routingCertType` | Użyj certyfikatu niestandardowego dla domeny routingu lub domyślnego certyfikatu z podpisem własnym — postępuj zgodnie z instrukcjami w sekcji **certyfikaty niestandardowe** | selfsigned <br> niestandardowe | selfsigned |
| `masterCertType` | Użyj certyfikatu niestandardowego dla domeny głównej lub domyślnego certyfikatu z podpisem własnym — wykonaj instrukcje w sekcji **certyfikaty niestandardowe** | selfsigned <br> niestandardowe | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

> [!NOTE] 
> Następujące polecenie wymaga interfejsu wiersza polecenia platformy Azure 2.0.8 lub nowszego. Możesz sprawdzić wersję interfejsu wiersza polecenia za pomocą `az --version` poleceń. Aby zaktualizować wersję interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latesti).

W poniższym przykładzie wdrożono klaster OpenShift i wszystkie powiązane zasoby w grupie zasobów o nazwie openshiftrg z nazwą wdrożenia myOpenShiftCluster. Do szablonu odwołuje się bezpośrednio z repozytorium GitHub, a plik parametrów lokalnych o nazwie azuredeploy.parameters.jsw pliku jest używany.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Wdrożenie trwa co najmniej 60 minut na podstawie łącznej liczby wdrożonych węzłów i skonfigurowanych opcji. Bastionu nazwa FQDN i adres URL DNS konsoli OpenShift do terminalu po zakończeniu wdrożenia.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Jeśli nie chcesz powiązać wiersza polecenia w oczekiwanie na ukończenie wdrożenia, Dodaj `--no-wait` jako jedną z opcji wdrożenia grupy. Dane wyjściowe wdrożenia można pobrać z Azure Portal w sekcji Wdrażanie dla grupy zasobów.

## <a name="connect-to-the-openshift-cluster"></a>Nawiązywanie połączenia z klastrem OpenShift

Po zakończeniu wdrażania Pobierz połączenie z sekcji Wyjście wdrożenia. Nawiąż połączenie z konsolą OpenShift z przeglądarką przy użyciu **adresu URL konsoli OpenShift**. Można również przeprowadzić protokół SSH do hosta bastionu. Poniżej znajduje się przykład, w którym nazwa użytkownika administratora to clusteradmin, a bastionu publiczny adres IP DNS FQDN to bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Użyj polecenia [AZ Group Delete](/cli/azure/group) , aby usunąć grupę zasobów, klaster OpenShift i wszystkie powiązane zasoby, gdy nie są już potrzebne.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Następne kroki

- [Zadania po wdrożeniu](./openshift-container-platform-3x-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem OpenShift na platformie Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Wprowadzenie do platformy kontenerów OpenShift](https://docs.openshift.com)
