---
title: Wdrażanie narzędzia OKD na platformie Azure
description: Wdrażanie narzędzia OKD na platformie Azure.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: d7d251370aefdfadc0b77a67f6dad1be2dcb9e9a
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759435"
---
# <a name="deploy-okd-in-azure"></a>Wdrażanie narzędzia OKD na platformie Azure

Można użyć jednego z dwóch sposobów wdrażania okd (dawniej OpenShift Origin) na platformie Azure:

- Można ręcznie wdrożyć wszystkie niezbędne składniki infrastruktury platformy Azure, a następnie postępować zgodnie z [dokumentacją OKD](https://docs.okd.io).
- Można również użyć istniejącego [szablonu Menedżera zasobów,](https://github.com/Microsoft/openshift-origin) który upraszcza wdrażanie klastra OKD.

## <a name="deploy-using-the-okd-template"></a>Wdrażanie przy użyciu szablonu OKD

Aby wdrożyć przy użyciu szablonu Menedżera zasobów, należy użyć pliku parametrów do dostarczenia parametrów wejściowych. Aby jeszcze bardziej dostosować wdrożenie, rozwiń repozytorium GitHub i zmień odpowiednie elementy.

Niektóre typowe opcje dostosowywania obejmują, ale nie są ograniczone do:

- Rozmiar maszyny Wirtualnej bastionu (zmienna w azuredeploy.json)
- Konwencje nazewnictwa (zmienne w pliku azuredeploy.json)
- Specyfika klastra OpenShift, zmodyfikowana za pomocą pliku hosts (deployOpenShift.sh)

Szablon [OKD](https://github.com/Microsoft/openshift-origin) ma wiele gałęzi dostępnych dla różnych wersji OKD.  W zależności od potrzeb można wdrożyć bezpośrednio z repozytorium lub rozwidlić repozytorium i wprowadzić niestandardowe zmiany przed wdrożeniem.

Użyj `appId` wartości z jednostki usługi, który `aadClientId` został utworzony wcześniej dla parametru.

Poniżej przedstawiono przykład pliku parametrów o nazwie azuredeploy.parameters.json ze wszystkimi wymaganymi wejściami.

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

Różne wersje mogą mieć różne parametry, więc sprawdź niezbędne parametry dla używanej gałęzi.

### <a name="deploy-using-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure


> [!NOTE] 
> Następujące polecenie wymaga interfejsu wiersza polecenia platformy Azure 2.0.8 lub nowszego. Wersję interfejsu wiersza polecenia `az --version` można zweryfikować za pomocą polecenia. Aby zaktualizować wersję interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

W poniższym przykładzie wdraża klaster OKD i wszystkie powiązane zasoby w grupie zasobów o nazwie openshiftrg, o nazwie wdrożenia myOpenShiftCluster. Do szablonu odwołuje się bezpośrednio z repozytorium GitHub podczas korzystania z pliku parametrów lokalnych o nazwie azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Wdrożenie trwa co najmniej 30 minut, aby zakończyć, na podstawie całkowitej liczby wdrożonych węzłów. Adres URL konsoli OpenShift i nazwa DNS wzorca OpenShift są drukowane na terminalu po zakończeniu wdrażania. Alternatywnie można wyświetlić dane wyjściowe sekcji wdrożenia z witryny Azure portal.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Jeśli nie chcesz powiązać wiersza polecenia oczekującego na zakończenie `--no-wait` wdrożenia, dodaj jako jedną z opcji wdrożenia grupy. Dane wyjściowe z wdrożenia można pobrać z witryny Azure portal w sekcji wdrażania dla grupy zasobów.

## <a name="connect-to-the-okd-cluster"></a>Łączenie się z klastrem OKD

Po zakończeniu wdrażania połącz się z konsolą OpenShift za pomocą przeglądarki za pomocą pliku `OpenShift Console Url`. Alternatywnie można SSH do wzorca OKD. Oto przykład, który używa danych wyjściowych z wdrożenia:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Polecenie [usuń grupę AZ](/cli/azure/group) służy do usuwania grupy zasobów, klastra OpenShift i wszystkich powiązanych zasobów, gdy nie są już potrzebne.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Następne kroki

- [Zadania po wdrożeniu](./openshift-container-platform-3x-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem OpenShift](./openshift-container-platform-3x-troubleshooting.md)
- [Wprowadzenie do okd](https://docs.okd.io)
