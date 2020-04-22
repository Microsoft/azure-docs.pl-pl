---
title: Wdrażanie platformy kontenerowej OpenShift 3.11 na platformie Azure
description: Wdrażanie platformy kontenerów OpenShift 3.11 na platformie Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 7d6cd4c6ce7991ae83f6f4a1dd6d8b86fe7eedbc
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757886"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Wdrażanie platformy kontenerowej OpenShift 3.11 na platformie Azure

Można użyć jednej z kilku metod do wdrożenia platformy kontenerowej OpenShift 3.11 na platformie Azure:

- Można ręcznie wdrożyć niezbędne składniki infrastruktury platformy Azure, a następnie postępować zgodnie z [dokumentacją platformy kontenerów OpenShift.](https://docs.openshift.com/container-platform)
- Można również użyć istniejącego [szablonu Menedżera zasobów,](https://github.com/Microsoft/openshift-container-platform/) który upraszcza wdrażanie klastra platformy kontenerów OpenShift.
- Inną opcją jest skorzystanie z [oferty portalu Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)

Dla wszystkich opcji wymagana jest subskrypcja Red Hat. Podczas wdrażania red hat enterprise linux wystąpienie jest zarejestrowany w subskrypcji Red Hat i dołączony do identyfikatora puli, który zawiera uprawnienia do platformy kontenera OpenShift.
Upewnij się, że masz prawidłową nazwę użytkownika, hasło i identyfikator puli Menedżera subskrypcji Red Hat (RHSM). Można użyć klucza aktywacyjnego, identyfikatora organizacji i identyfikatora puli. Te informacje można zweryfikować, https://access.redhat.comlogując się do pliku .


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Wdrażanie przy użyciu szablonu OpenShift Container Platform Resource Manager 3.11

### <a name="private-clusters"></a>Klastry prywatne

Wdrażanie prywatnych klastrów OpenShift wymaga więcej niż tylko braku publicznego adresu IP skojarzonego z głównym modułem równoważenia obciążenia (konsola internetowa) lub z modułem równoważenia obciążenia (router).  Klaster prywatny zazwyczaj używa niestandardowego serwera DNS (nie domyślnego systemu Azure DNS), niestandardowej nazwy domeny (takiej jak contoso.com) i wstępnie zdefiniowanej sieci wirtualnej.  W przypadku klastrów prywatnych należy skonfigurować sieć wirtualną z wyprzedzeniem ze wszystkimi odpowiednimi ustawieniami podsieci i serwera DNS.  Następnie użyj **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**i **existingNodeSubnetReference,** aby określić istniejącą podsieć do użycia przez klaster.

Jeśli wybrano prywatny wzorzec (**masterClusterType**=private), dla **masterPrivateClusterIp**należy określić statyczny prywatny adres IP.  Ten adres IP zostanie przypisany do frontu modułu równoważenia obciążenia głównego.  Adres IP musi znajdować się w cidr dla podsieci głównej i nie jest używany.  **masterClusterDnsType** musi być ustawiona na "niestandardowa", a główna nazwa DNS musi być podana dla **masterClusterDns**.  Nazwa DNS musi być mapowana na statyczny prywatny adres IP i będzie używana do uzyskiwania dostępu do konsoli w węzłach głównych.

Jeśli wybrano router prywatny (**routerClusterType**=private), dla **routerPrivateClusterIp**należy określić statyczny prywatny adres IP .  Ten adres IP zostanie przypisany do frontu modułu równoważenia obciążenia podczerwieni.  Adres IP musi znajdować się w cidr dla podsieci podczerwej i nie jest używany.  **routingSubDomainType** musi być ustawiony na "niestandardowy", a nazwa DNS z symbolami wieloznacznych dla routingu musi być podana dla **routinguSubDomain**.  

Jeśli wybrano prywatne wzorce i router prywatny, należy również wprowadzić niestandardową nazwę domeny dla **domainName**

Po pomyślnym wdrożeniu węzeł bastionu jest jedynym węzłem z publicznym adresem IP, do którego można wpaść.  Nawet jeśli węzły główne są skonfigurowane do publicznego dostępu, nie są one widoczne dla dostępu ssh.

Aby wdrożyć przy użyciu szablonu Menedżera zasobów, należy użyć pliku parametrów do dostarczenia parametrów wejściowych. Aby jeszcze bardziej dostosować wdrożenie, rozwiń repozytorium GitHub i zmień odpowiednie elementy.

Niektóre typowe opcje dostosowywania obejmują, ale nie są ograniczone do:

- Rozmiar maszyny Wirtualnej bastionu (zmienna w azuredeploy.json)
- Konwencje nazewnictwa (zmienne w pliku azuredeploy.json)
- Specyfika klastra OpenShift, zmodyfikowana za pomocą pliku hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurowanie pliku parametrów

Szablon [Platformy kontenerów OpenShift](https://github.com/Microsoft/openshift-container-platform) ma wiele gałęzi dostępnych dla różnych wersji platformy kontenerowej OpenShift.  W zależności od potrzeb można wdrożyć bezpośrednio z repozytorium lub rozwidlać repozytorium i wprowadzić niestandardowe zmiany w szablonach lub skryptach przed wdrożeniem.

Użyj `appId` wartości z jednostki usługi utworzonej wcześniej dla parametru. `aadClientId`

W poniższym przykładzie przedstawiono plik parametrów o nazwie azuredeploy.parameters.json ze wszystkimi wymaganymi wejściami.

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

Różne wersje mogą mieć różne parametry, więc sprawdź niezbędne parametry dla używanej gałęzi.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Plik parameters.json wyjaśniony

| Właściwość | Opis | Prawidłowe opcje | Wartość domyślna |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | Adres URL artefaktów (json, skrypty itp.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Region platformy Azure do wdrażania zasobów |  |  |
| `masterVmSize` | Rozmiar głównej maszyny Wirtualnej. Wybierz jeden z dozwolonych rozmiarów maszyn wirtualnych wymienionych w pliku azuredeploy.json |  | Standardowa_E2s_v3 |
| `infraVmSize` | Rozmiar maszyny Wirtualnej Infra. Wybierz jeden z dozwolonych rozmiarów maszyn wirtualnych wymienionych w pliku azuredeploy.json |  | Standardowa_D4s_v3 |
| `nodeVmSize` | Rozmiar maszyny Wirtualnej węzła aplikacji. Wybierz jeden z dozwolonych rozmiarów maszyn wirtualnych wymienionych w pliku azuredeploy.json |  | Standardowa_D4s_v3 |
| `cnsVmSize` | Rozmiar maszyny Wirtualnej węzła magazynu macierzystego kontenera (CNS). Wybierz jeden z dozwolonych rozmiarów maszyn wirtualnych wymienionych w pliku azuredeploy.json |  | Standardowa_E4s_v3 |
| `osImageType` | Obraz RHEL do użycia. domyślna galeria: Na żądanie; rynek: obraz innych firm | galeria domyślna <br> Marketplace | galeria domyślna |
| `marketplaceOsImage` | Jeśli `osImageType` jest to rynek, wprowadź odpowiednie wartości dla oferty "wydawca", "oferta", "sku", "wersja" oferty rynkowej. Ten parametr jest typem obiektu |  |  |
| `storageKind` | Rodzaj magazynu, który ma być używany  | Zarządzane<br> Niezarządzanych | Zarządzane |
| `openshiftClusterPrefix` | Prefiks klastra używany do konfigurowania nazwy hostów dla wszystkich węzłów.  Od 1 do 20 znaków |  | mycluster (polski) |
| `minoVersion` | Pomocnicza wersja platformy kontenerowej OpenShift 3.11 do wdrożenia |  | 69 |
| `masterInstanceCount` | Liczba węzłów wzorców do wdrożenia | 1, 3, 5 | 3 |
| `infraInstanceCount` | Liczba węzłów podczerwonych do wdrożenia | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Liczba węzłów do wdrożenia | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Liczba węzłów OUN do wdrożenia | 3, 4 | 3 |
| `osDiskSize` | Rozmiar dysku systemu operacyjnego dla maszyny Wirtualnej (w GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Rozmiar dysku danych do dołączenia do węzłów dla woluminu platformy Docker (w GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Rozmiar dysku danych do podłączenia do węzłów OUN do użytku przez glusterfs (w GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nazwa użytkownika administratora zarówno dla logowania systemu operacyjnego (VM), jak i początkowego użytkownika OpenShift |  | ocpadmin (okpadmin) |
| `enableMetrics` | Włącz metryki. Metryki wymagają więcej zasobów, więc wybierz odpowiedni rozmiar dla infra VM | true <br> false | false |
| `enableLogging` | Włącz rejestrowanie. elasticsearch pod wymaga 8 GB pamięci RAM, więc wybierz odpowiedni rozmiar dla Infra VM | true <br> false | false |
| `enableCNS` | Włącz magazyn natywny kontener | true <br> false | false |
| `rhsmUsernameOrOrgId` | Nazwa użytkownika lub identyfikator organizacji menedżera subskrypcji Red Hat |  |  |
| `rhsmPoolId` | Identyfikator puli menedżera subskrypcji Red Hat, który zawiera uprawnienia OpenShift dla węzłów obliczeniowych |  |  |
| `rhsmBrokerPoolId` | Identyfikator puli menedżera subskrypcji Red Hat, który zawiera uprawnienia OpenShift dla wzorców i węzłów podczerwieni. Jeśli nie masz różnych identyfikatorów puli, wprowadź ten sam identyfikator puli jako 'rhsmPoolId' |  |
| `sshPublicKey` | Skopiuj klucz publiczny SSH tutaj |  |  |
| `keyVaultSubscriptionId` | Identyfikator subskrypcji zawierającej przechowalnię kluczy |  |  |
| `keyVaultResourceGroup` | Nazwa grupy zasobów zawierającej magazyn kluczy |  |  |
| `keyVaultName` | Nazwa utworzonego przechowalni kluczy |  |  |
| `enableAzure` | Włącz dostawcę chmury platformy Azure | true <br> false | true |
| `aadClientId` | Identyfikator klienta usługi Azure Active Directory znany również jako identyfikator aplikacji dla podmiotu obsługującego usługę |  |  |
| `domainName` | Nazwa niestandardowej nazwy domeny do użycia (jeśli dotyczy). Ustaw na "brak", jeśli nie wdrażaj w pełni prywatnego klastra |  | brak |
| `masterClusterDnsType` | Typ domeny dla konsoli internetowej OpenShift. "default" użyje etykiety DNS głównego adresu IP na podczerwień. "custom" pozwala zdefiniować własną nazwę | default <br> niestandardowy | default |
| `masterClusterDns` | Niestandardowa nazwa DNS używana do uzyskiwania dostępu do konsoli internetowej OpenShift, jeśli wybrano opcję "niestandardowa"`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Jeśli ustawiona na "nipio", `routingSubDomain` użyje nip.io.  Użyj "niestandardowego", jeśli masz własną domenę, której chcesz użyć do routingu | nipio ( nipio ) <br> niestandardowy | nipio ( nipio ) |
| `routingSubDomain` | Wieloznaczna nazwa DNS, której chcesz użyć do routingu, jeśli wybrano opcję "niestandardowy" dla`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Wybierz, czy chcesz użyć istniejącej sieci wirtualnej, czy utworzyć nową sieć wirtualną | Istniejących <br> new | new |
| `virtualNetworkResourceGroupName` | Nazwa grupy zasobów dla nowej sieci wirtualnej, jeśli wybrano opcję "nowy" dla`virtualNetworkNewOrExisting` |  | resourceGroup().nazwa |
| `virtualNetworkName` | Nazwa nowej sieci wirtualnej do utworzenia, jeśli wybrano opcję "nowy" dla`virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Prefiks adresu nowej sieci wirtualnej |  | 10.0.0.0/14 |
| `masterSubnetName` | Nazwa podsieci głównej |  | mastersubnet ( mastersubnet ) |
| `masterSubnetPrefix` | CIDR używany w podsieci głównej - musi być podzbiorem addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | Nazwa podsieci infra |  | infrasubnet |
| `infraSubnetPrefix` | CIDR używany w podsieci podwładnej - musi być podzbiorem addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | Nazwa podsieci węzła |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR używany w podsieci węzła - musi być podzbiorem addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Pełne odwołanie do istniejącej podsieci dla węzłów głównych. Nie jest potrzebne, jeśli tworzenie nowej sieci wirtualnej / podsieci |  |  |
| `existingInfraSubnetReference` | Pełne odwołanie do istniejącej podsieci dla węzłów podczerwień. Nie jest potrzebne, jeśli tworzenie nowej sieci wirtualnej / podsieci |  |  |
| `existingCnsSubnetReference` | Pełne odwołanie do istniejącej podsieci dla węzłów OUN. Nie jest potrzebne, jeśli tworzenie nowej sieci wirtualnej / podsieci |  |  |
| `existingNodeSubnetReference` | Pełne odwołanie do istniejącej podsieci dla węzłów obliczeniowych. Nie jest potrzebne, jeśli tworzenie nowej sieci wirtualnej / podsieci |  |  |
| `masterClusterType` | Określ, czy klaster używa prywatnych czy publicznych węzłów głównych. Jeśli wybrano opcję prywatna, węzły główne nie będą udostępniane w Internecie za pośrednictwem publicznego adresu IP. Zamiast tego użyje prywatnego ip określonego w`masterPrivateClusterIp` | public <br> private | public |
| `masterPrivateClusterIp` | Jeśli wybrano prywatne węzły główne, prywatny adres IP musi być określony do użycia przez wewnętrzny moduł równoważenia obciążenia dla węzłów głównych. Ten statyczny adres IP musi znajdować się w bloku CIDR dla podsieci głównej i nie jest jeszcze używany. Jeśli wybrane są publiczne węzły główne, ta wartość nie będzie używana, ale nadal musi być określona |  | 10.1.0.200 |
| `routerClusterType` | Określ, czy klaster używa prywatnych czy publicznych węzłów podczerwień. Jeśli wybrano opcję prywatna, węzły podczerwień nie będą udostępniane w Internecie za pośrednictwem publicznego adresu IP. Zamiast tego użyje prywatnego ip określonego w`routerPrivateClusterIp` | public <br> private | public |
| `routerPrivateClusterIp` | Jeśli wybrano prywatne węzły podczerwieni, prywatny adres IP musi być określony do użycia przez wewnętrzny moduł równoważenia obciążenia dla węzłów podczerwone. Ten statyczny adres IP musi znajdować się w bloku CIDR dla podsieci podsieci i nie jest jeszcze używany. Jeśli zaznaczone są publiczne węzły podczerwień, ta wartość nie będzie używana, ale nadal musi być określona |  | 10.2.0.200 |
| `routingCertType` | Używanie certyfikatu niestandardowego do routingu domeny lub domyślnego certyfikatu z podpisem własnym — postępuj zgodnie z instrukcjami w sekcji **Certyfikaty niestandardowe** | selfsigned <br> niestandardowy | selfsigned |
| `masterCertType` | Używanie certyfikatu niestandardowego dla domeny głównej lub domyślnego certyfikatu z podpisem własnym — postępuj zgodnie z instrukcjami w sekcji **Certyfikaty niestandardowe** | selfsigned <br> niestandardowy | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

> [!NOTE] 
> Następujące polecenie wymaga interfejsu wiersza polecenia platformy Azure 2.0.8 lub nowszego. Wersję interfejsu wiersza polecenia `az --version` można zweryfikować za pomocą polecenia. Aby zaktualizować wersję interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

W poniższym przykładzie wdraża klaster OpenShift i wszystkie powiązane zasoby w grupie zasobów o nazwie openshiftrg, o nazwie wdrożenia myOpenShiftCluster. Do szablonu odwołuje się bezpośrednio z repozytorium GitHub, a używany jest plik parametrów lokalnych o nazwie azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Wdrożenie trwa co najmniej 60 minut, aby zakończyć, na podstawie całkowitej liczby wdrożonych węzłów i skonfigurowanych opcji. Adres FQDN i adres URL konsoli OpenShift są drukowane na terminalu po zakończeniu wdrażania.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Jeśli nie chcesz powiązać wiersza polecenia oczekującego na zakończenie `--no-wait` wdrożenia, dodaj jako jedną z opcji wdrożenia grupy. Dane wyjściowe z wdrożenia można pobrać z witryny Azure portal w sekcji wdrażania dla grupy zasobów.

## <a name="connect-to-the-openshift-cluster"></a>Łączenie się z klastrem OpenShift

Po zakończeniu wdrażania pobierz połączenie z sekcji wyjściowej wdrożenia. Połącz się z konsolą OpenShift za pomocą przeglądarki, używając **adresu URL konsoli OpenShift**. Można również SSH do hosta Bastion. Poniżej przedstawiono przykład, w którym nazwa użytkownika administratora jest clusteradmin i bastion publiczny IP DNS FQDN jest bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Polecenie [usuń grupę AZ](/cli/azure/group) służy do usuwania grupy zasobów, klastra OpenShift i wszystkich powiązanych zasobów, gdy nie są już potrzebne.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Następne kroki

- [Zadania po wdrożeniu](./openshift-container-platform-3x-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem usługi OpenShift na platformie Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Wprowadzenie do platformy kontenerowej OpenShift](https://docs.openshift.com)
