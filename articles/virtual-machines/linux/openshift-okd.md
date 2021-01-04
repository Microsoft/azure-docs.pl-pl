---
title: Wdrażanie OKD na platformie Azure
description: Wdróż OKD na platformie Azure.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: ed74d0829a8372ea8391fcd331eef2ef3af3dae0
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704915"
---
# <a name="deploy-okd-in-azure"></a>Wdrażanie OKD na platformie Azure

Aby wdrożyć OKD (dawniej OpenShift) na platformie Azure, możesz użyć jednego z dwóch sposobów:

- Możesz ręcznie wdrożyć wszystkie niezbędne składniki infrastruktury platformy Azure, a następnie postępować zgodnie z [dokumentacją OKD](https://docs.okd.io).
- Można również użyć istniejącego [szablonu Menedżer zasobów](https://github.com/Microsoft/openshift-origin) , który upraszcza Wdrożenie klastra OKD.

## <a name="deploy-using-the-okd-template"></a>Wdrażanie przy użyciu szablonu OKD

Aby wdrożyć przy użyciu szablonu Menedżer zasobów, należy użyć pliku parametrów w celu dostarczenia parametrów wejściowych. Aby dodatkowo dostosować wdrożenie, rozwidlenie repozytorium GitHub i zmiana odpowiednich elementów.

Niektóre typowe opcje dostosowania obejmują, ale nie są ograniczone do:

- Rozmiar maszyny wirtualnej bastionu (zmienna w azuredeploy.jsna)
- Konwencje nazewnictwa (zmienne w azuredeploy.json)
- OpenShift specyficzne dla klastra, zmodyfikowane za pomocą pliku hosts (deployOpenShift.sh)

[Szablon OKD](https://github.com/Microsoft/openshift-origin) ma wiele gałęzi dostępnych dla różnych wersji programu OKD.  Na podstawie Twoich potrzeb można wdrożyć bezpośrednio z repozytorium lub utworzyć rozwidlenie repozytorium i wprowadzić zmiany niestandardowe przed wdrożeniem.

Użyj `appId` wartości z jednostki usługi utworzonej wcześniej dla `aadClientId` parametru.

Poniżej znajduje się przykład pliku parametrów o nazwie azuredeploy.parameters.jsna wszystkich wymaganych danych wejściowych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Zastąp parametry określonymi informacjami.

Różne wersje mogą mieć inne parametry, dlatego należy sprawdzić wymagane parametry używanej gałęzi.

### <a name="deploy-using-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure


> [!NOTE] 
> Następujące polecenie wymaga interfejsu wiersza polecenia platformy Azure 2.0.8 lub nowszego. Możesz sprawdzić wersję interfejsu wiersza polecenia za pomocą `az --version` poleceń. Aby zaktualizować wersję interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

W poniższym przykładzie wdrożono klaster OKD i wszystkie powiązane zasoby w grupie zasobów o nazwie openshiftrg z nazwą wdrożenia myOpenShiftCluster. Ten szablon jest przywoływany bezpośrednio z repozytorium GitHub przy użyciu pliku parametrów lokalnych o nazwie azuredeploy.parameters.json.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Wdrożenie trwa co najmniej 30 minut na podstawie łącznej liczby wdrożonych węzłów. Adres URL konsoli programu OpenShift i nazwa DNS wzorca OpenShift są drukowane do terminalu po zakończeniu wdrożenia. Alternatywnie można wyświetlić sekcję dane wyjściowe wdrożenia z Azure Portal.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Jeśli nie chcesz powiązać wiersza polecenia w oczekiwanie na ukończenie wdrożenia, Dodaj `--no-wait` jako jedną z opcji wdrożenia grupy. Dane wyjściowe wdrożenia można pobrać z Azure Portal w sekcji Wdrażanie dla grupy zasobów.

## <a name="connect-to-the-okd-cluster"></a>Nawiązywanie połączenia z klastrem OKD

Po zakończeniu wdrażania Nawiąż połączenie z konsolą OpenShift za pomocą przeglądarki `OpenShift Console Url` . Alternatywnie można także przeprowadzić protokół SSH do wzorca OKD. Poniżej znajduje się przykład, który używa danych wyjściowych wdrożenia:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Użyj polecenia [AZ Group Delete](/cli/azure/group) , aby usunąć grupę zasobów, klaster OpenShift i wszystkie powiązane zasoby, gdy nie są już potrzebne.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Następne kroki

- [Zadania po wdrożeniu](./openshift-container-platform-3x-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem OpenShift](./openshift-container-platform-3x-troubleshooting.md)
- [Wprowadzenie do korzystania z OKD](https://docs.okd.io)
