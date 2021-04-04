---
title: Szybki Start — Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
description: Rozpocznij pracę z wdrożeniami, Dowiedz się, jak szybko utworzyć zestaw skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 5ed6ff00e2ec9a47cec0290fa88ef3b554287607
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518162"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Szybki start: tworzenie zestawu skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu automatycznego skalowania maszyn wirtualnych i zarządzanie nim. Maszyny wirtualne w zestawie skalowania możesz skalować ręcznie lub możesz zdefiniować reguły skalowania automatycznego na podstawie użycia takich zasobów jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. Moduł równoważenia obciążenia platformy Azure następnie dystrybuuje ruch do wystąpień maszyn wirtualnych w zestawie skalowania. W tym przewodniku Szybki start utworzysz zestaw skalowania maszyn wirtualnych i wdrożysz przykładową aplikację przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.29 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana. 


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Zanim będzie można utworzyć zestaw skalowania, utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [az vmss create](/cli/azure/vmss). Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet*, który jest skonfigurowany do automatycznej aktualizacji w miarę stosowania zmian, a następnie generuje klucze SSH, jeśli nie istnieją, w lokalizacji *~/.ssh/id_rsa*. Te klucze SSH są używane, jeśli musisz zalogować się do wystąpień maszyn wirtualnych. Aby użyć istniejącego zestawu kluczy SSH, zamiast tego użyj parametru `--ssh-key-value` i określ lokalizację swoich kluczy.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="deploy-sample-application"></a>Wdrażanie przykładowej aplikacji
Aby przetestować zestaw skalowania, należy zainstalować podstawową aplikację internetową. Niestandardowe rozszerzenie skryptu platformy Azure służy do pobierania i uruchamiania skryptu, który instaluje aplikację na wystąpieniach maszyn wirtualnych. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Aby uzyskać więcej informacji, zobacz [Omówienie niestandardowego rozszerzenia skryptu](../virtual-machines/extensions/custom-script-linux.md).

Użyj niestandardowego rozszerzenia skryptu, aby zainstalować podstawowy serwer internetowy NGINX. Zastosuj niestandardowe rozszerzenie skryptu, który instaluje NGINX, używając polecenia [az vmss extension set](/cli/azure/vmss/extension) w następujący sposób:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>Zezwalanie na ruch do aplikacji
Podczas tworzenia zestawu skalowania zostanie automatycznie wdrożony moduł równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia dystrybuuje ruch do wystąpień maszyn wirtualnych w zestawie skalowania. Aby umożliwić dotarcie ruchu do przykładowej aplikacji internetowej, utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule). W poniższym przykładzie pokazano tworzenie reguły o nazwie *myLoadBalancerRuleWeb*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-scale-set"></a>Testowanie zestawu skalowania
Aby zapoznać się z działaniem zestawu skalowania, uzyskaj dostęp do przykładowej aplikacji internetowej w przeglądarce internetowej. Uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [az network public-ip show](/cli/azure/network/public-ip). W poniższym przykładzie pokazano uzyskiwanie adresu IP dla modułu *myScaleSetLBPublicIP* utworzonego w ramach zestawu skalowania:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

Wprowadź publiczny adres IP modułu równoważenia obciążenia w przeglądarce internetowej. Moduł równoważenia obciążenia kieruje ruch do jednego z wystąpień maszyn wirtualnych, jak pokazano w poniższym przykładzie:

![Domyślna strona internetowa na serwerze NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Gdy grupa zasobów, zestaw skalowania i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group) w następujący sposób. Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzyliśmy podstawowy zestaw skalowania i użyliśmy niestandardowego rozszerzenia skryptu w celu zainstalowania podstawowego serwera internetowego NGINX na wystąpieniach maszyn wirtualnych. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego sposobu tworzenia zestawów skalowania maszyn wirtualnych platformy Azure i zarządzania nimi.

> [!div class="nextstepaction"]
> [Tworzenie zestawów skalowania maszyn wirtualnych platformy Azure i zarządzanie nimi](tutorial-create-and-manage-cli.md)
