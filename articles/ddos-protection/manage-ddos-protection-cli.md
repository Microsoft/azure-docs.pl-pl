---
title: Tworzenie i konfigurowanie planu Azure DDoS Protection pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć plan DDoS Protection przy użyciu interfejsu wiersza polecenia platformy Azure
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 8a8da50dc703d59dc16b5cb6253d39aeb33fd76d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777639"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Szybki start: tworzenie i konfigurowanie interfejsu Azure DDoS Protection Standardowa przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpoczynanie pracy z Azure DDoS Protection Standardowa przy użyciu interfejsu wiersza polecenia platformy Azure. 

Plan ochrony przed DDoS definiuje zestaw sieci wirtualnych, które mają włączoną ochronę przed DDoS w standardowych subskrypcjach. Możesz skonfigurować jeden plan ochrony przed DDoS dla swojej organizacji i połączyć sieci wirtualne z wielu subskrypcji do tego samego planu. 

W tym przewodniku Szybki start utworzysz plan usługi DDoS Protection i połączysz go z siecią wirtualną. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Interfejs wiersza polecenia platformy Azure zainstalowany lokalnie lub Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i używać go lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-ddos-protection-plan"></a>Tworzenie DDoS Protection planu

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową.

Aby utworzyć grupę zasobów, użyj [az group create](/cli/azure/group#az_group_create). W tym przykładzie nadamy grupie zasobów _nazwę MyResourceGroup_ i użyjemy lokalizacji _Wschodnie usa:_

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Teraz utwórz plan ochrony przed działaniami DDoS o _nazwie MyDdosProtectionPlan:_

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Włączanie ochrony przed DDoS dla sieci wirtualnej

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Włączanie ochrony przed DDoS dla nowej sieci wirtualnej

Ochronę przed DDoS można włączyć podczas tworzenia sieci wirtualnej. W tym przykładzie nadamy sieci wirtualnej _nazwę MyVnet:_ 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

Nie można przenieść sieci wirtualnej do innej grupy zasobów lub subskrypcji, gdy dla sieci wirtualnej jest włączona funkcja DDoS Standard. Jeśli musisz przenieść sieć wirtualną z włączoną usługą DDoS Standard, najpierw wyłącz standard DDoS, przenieś sieć wirtualną, a następnie włącz standard DDoS. Po zakończeniu przenoszenia automatycznie dostosowane progi zasad dla wszystkich chronionych publicznych adresów IP w sieci wirtualnej zostaną zresetowane.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Włączanie ochrony przed DDoS dla istniejącej sieci wirtualnej

Podczas [tworzenia planu ochrony przed DDoS](#create-a-ddos-protection-plan)można skojarzyć z planem co najmniej jedną sieć wirtualną. Aby dodać więcej niż jedną sieć wirtualną, po prostu listę nazw lub identyfikatorów rozdzielonych spacjami. W tym przykładzie dodamy sieć _MyVnet:_

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnets MyVnet
```

Alternatywnie można włączyć ochronę przed DDoS dla danej sieci wirtualnej:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

## <a name="validate-and-test"></a>Weryfikowanie i testowanie

Najpierw sprawdź szczegóły planu ochrony przed DDoS:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Sprawdź, czy polecenie zwraca poprawne szczegóły planu ochrony przed DDoS.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz zachować zasoby do następnego samouczka. Jeśli grupa zasobów _MyResourceGroup_ nie jest już potrzebna, usuń ją. Usunięcie grupy zasobów powoduje również usunięcie planu ochrony przed DDoS i wszystkich powiązanych zasobów. 

Aby usunąć grupę zasobów, użyj [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Zaktualizuj podaną sieć wirtualną, aby wyłączyć ochronę przed DDoS:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection false
    --ddos-protection-plan ""
```

Jeśli chcesz usunąć plan ochrony przed DDoS, musisz najpierw usunąć z niego skojarzenie wszystkich sieci wirtualnych. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wyświetlać i konfigurować dane telemetryczne dla planu ochrony przed DDoS, przejdź do samouczków.

> [!div class="nextstepaction"]
> [Wyświetlanie i konfigurowanie telemetrii ochrony przed atakami DDoS](telemetry.md)
