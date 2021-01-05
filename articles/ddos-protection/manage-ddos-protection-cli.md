---
title: Tworzenie i konfigurowanie planu Azure DDoS Protection przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć plan DDoS Protection przy użyciu interfejsu wiersza polecenia platformy Azure
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 6c628d93c112a770c85a10d0eff958614a7cf4cb
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814163"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Szybki Start: Tworzenie i Konfigurowanie standardu Azure DDoS Protection przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpocznij pracę ze standardem Azure DDoS Protection przy użyciu interfejsu wiersza polecenia platformy Azure. 

Plan ochrony DDoS definiuje zbiór sieci wirtualnych, dla których włączono standard ochrony DDoS w ramach subskrypcji. Można skonfigurować jeden plan ochrony DDoS dla Twojej organizacji i połączyć sieci wirtualne z wielu subskrypcji w ramach tego samego planu. 

W tym przewodniku szybki start utworzysz plan ochrony DDoS i połączysz go z siecią wirtualną. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Interfejs wiersza polecenia platformy Azure został zainstalowany lokalnie lub Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-ddos-protection-plan"></a>Utwórz plan DDoS Protection

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową.

Aby utworzyć grupę zasobów, użyj polecenie [AZ Group Create](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-create). W tym przykładzie zmienimy nazwę _naszej grupy zasobów_ i użyjemy lokalizacji _Wschodnie stany USA_ :

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Teraz Utwórz plan ochrony DDoS o nazwie _MyDdosProtectionPlan_:

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Włącz ochronę DDoS dla sieci wirtualnej

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Włącz ochronę DDoS dla nowej sieci wirtualnej

Ochronę DDoS można włączyć podczas tworzenia sieci wirtualnej. W tym przykładzie zmienimy nazwę naszej sieci wirtualnej _MyVnet_: 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
```

Nie można przenieść sieci wirtualnej do innej grupy zasobów lub subskrypcji, gdy dla sieci wirtualnej jest włączony Standard DDoS. Jeśli musisz przenieść sieć wirtualną z włączonym standardem DDoS, wyłącz najpierw pozycję DDoS Standard, Przenieś sieć wirtualną, a następnie Włącz Standard DDoS. Po przeniesieniu wartości progowe dla wszystkich chronionych publicznych adresów IP w sieci wirtualnej zostaną zresetowane.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Włącz ochronę DDoS dla istniejącej sieci wirtualnej

Podczas [tworzenia planu ochrony DDoS](#create-a-ddos-protection-plan)można skojarzyć co najmniej jedną sieć wirtualną z planem. Aby dodać więcej niż jedną sieć wirtualną, wystarczy podać nazwy lub identyfikatory, rozdzielone spacjami. W tym przykładzie dodamy _MyVnet_:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnet MyVnet
```

Alternatywnie można włączyć ochronę DDoS dla danej sieci wirtualnej:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection true
```

## <a name="validate-and-test"></a>Weryfikuj i Testuj

Najpierw zapoznaj się ze szczegółami planu ochrony DDoS:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Sprawdź, czy polecenie zwraca poprawne szczegóły planu ochrony DDoS.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz zachować zasoby dla następnego samouczka. Jeśli nie jest już potrzebne, Usuń grupę _zasobów zasobu._ Po usunięciu grupy zasobów należy również usunąć plan ochrony DDoS i wszystkie powiązane z nim zasoby. 

Aby usunąć grupę zasobów, użyj [AZ Group Delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az_group_delete):

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Zaktualizuj daną sieć wirtualną, aby wyłączyć ochronę DDoS:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection false
```

W celu usunięcia planu ochrony DDoS należy najpierw usunąć skojarzenie wszystkich sieci wirtualnych. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wyświetlać i konfigurować dane telemetryczne dla planu ochrony DDoS, przejdź do samouczków.

> [!div class="nextstepaction"]
> [Wyświetlanie i konfigurowanie telemetrii ochrony przed atakami DDoS](telemetry.md)