---
title: 'Szybki start: tworzenie profilu wysokiej jakości aplikacji — interfejs wiersza polecenia platformy Azure — Azure Traffic Manager'
description: W tym artykule Szybki start opisano, jak utworzyć profil Traffic Manager, aby utworzyć aplikację internetową o wysokiej dostępie przy użyciu interfejsu wiersza polecenia platformy Azure.
services: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 18c77a2b4cbf61979a2ba085640a03c209e890e7
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727928"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Szybki start: tworzenie profilu Traffic Manager aplikacji internetowej o wysokiej dostępie przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start opisano tworzenie profilu usługi Traffic Manager, który zapewni wysoką dostępność aplikacji internetowej.

W tym przewodniku Szybki start utworzysz dwa wystąpienia aplikacji internetowej. Każde z nich jest uruchamiane w innym regionie świadczenia usługi Azure. Utworzysz profil usługi Traffic Manager na podstawie [priorytetu punktu końcowego](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil kieruje ruch użytkowników do lokacji głównej, w której działa aplikacja internetowa. Usługa Traffic Manager stale monitoruje aplikację internetową. Jeśli lokacja główna jest niedostępna, usługa zapewnia automatyczne przejście w tryb failover w lokacji zapasowej.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Diagram przedstawiający środowisko Traffic Manager wdrażania przy użyciu interfejsu wiersza polecenia." border="false":::

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *lokalizacji eastus:*

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil Traffic Manager za pomocą narzędzia [az network traffic-manager profile create,](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-create) który kieruje ruchem użytkowników na podstawie priorytetu punktu końcowego.

W poniższym przykładzie **zastąp**<profile_name>unikatową nazwą Traffic Manager profilu.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Tworzenie aplikacji internetowych

W tym przewodniku Szybki start będą potrzebne dwa wystąpienia aplikacji internetowej wdrożone w dwóch różnych regionach świadczenia usługi Azure (*Wschodnie stany USA* i *Europa Zachodnia*). Każda będzie służyć jako podstawowy punkt końcowy i punkt końcowy trybu failover dla usługi Traffic Manager.

### <a name="create-web-app-service-plans"></a>Tworzenie planów usługi aplikacji internetowej
Utwórz plany usługi aplikacji internetowej przy użyciu narzędzia [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) dla dwóch wystąpień aplikacji internetowej, które zostaną wdrożone w dwóch różnych regionach platformy Azure.

W poniższym przykładzie zastąp wartości **<appspname_eastus>** i **<appspname_westeurope>** unikatową nazwą App Service Plan

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Tworzenie aplikacji internetowej w planie usługi App Service
Utwórz dwa wystąpienia aplikacji internetowej przy użyciu narzędzia [az webapp create](/cli/azure/webapp#az-webapp-create) w planach usługi App Service regionach świadczenia usługi Azure Wschodnie stany *USA* *i Europa* Zachodnia.

W poniższym przykładzie zastąp wartości **<app1name_eastus>** **i<app2name_westeurope>** unikatową nazwą  aplikacji, a nazwy<appspname_eastus>**i<appspname_westeurope>** nazwami użytymi do utworzenia planów App Service w poprzedniej sekcji.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager
Dodaj te dwa Web Apps jako Traffic Manager końcowe przy użyciu narzędzia [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-create) do profilu Traffic Manager w następujący sposób:

- Określ identyfikator aplikacji internetowej i dodaj aplikację internetową znajdującą się w regionie świadczenia usługi Azure *Wschodnie* usa jako podstawowy punkt końcowy do rozsyłania całego ruchu użytkowników. 
- Określ identyfikator aplikacji internetowej i dodaj aplikację internetową znajdującą się w regionie *platformy* Azure Europa Zachodnia jako punkt końcowy trybu failover. 

Gdy podstawowy punkt końcowy będzie niedostępny, ruch będzie automatycznie kierowany do punktu końcowego trybu failover.

W poniższym przykładzie zastąp **<app1name_eastus>** i **<app2name_westeurope>** nazwami aplikacji utworzonymi dla każdego regionu w poprzedniej sekcji. Następnie zastąp **<profile_name>** nazwą profilu używaną w poprzedniej sekcji. 

**Punkt końcowy Wschodnie usa**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Zanotuj identyfikator wyświetlany w danych wyjściowych i użyj polecenia w następującym poleceniu, aby dodać punkt końcowy:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Punkt końcowy Europa Zachodnia**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Zanotuj identyfikator wyświetlany w danych wyjściowych i użyj polecenia w następującym poleceniu, aby dodać punkt końcowy:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Testowanie profilu Traffic Manager aplikacji

W tej sekcji sprawdzisz nazwę domeny profilu usługi Traffic Manager. Skonfigurujesz również podstawowy punkt końcowy tak, aby był niedostępny. Na końcu zobaczysz, że aplikacja internetowa jest nadal dostępna. Dzieje się tak, ponieważ usługa Traffic Manager wysyła ruch do punktu końcowego trybu failover.

W poniższym przykładzie zastąp **<app1name_eastus>** i **<app2name_westeurope>** nazwami aplikacji utworzonymi dla każdego regionu w poprzedniej sekcji. Następnie zastąp **<profile_name>** nazwą profilu używaną w poprzedniej sekcji.

### <a name="determine-the-dns-name"></a>Ustalanie nazwy DNS

Określ nazwę DNS profilu Traffic Manager za pomocą [narzędzia az network traffic-manager profile show](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Skopiuj **wartość RelativeDnsName.** Nazwa DNS profilu Traffic Manager to *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager
1. W przeglądarce internetowej wprowadź nazwę DNS profilu usługi Traffic Manager *(http://<* relativednsname *>.trafficmanager.net),* aby wyświetlić domyślną witrynę internetową aplikacji internetowej.

    > [!NOTE]
    > W tym scenariuszu Szybki start wszystkie żądania są kierowane do podstawowego punktu końcowego. Jest on ustawiony na wartość **Priorytet 1**.
2. Aby wyświetlić Traffic Manager trybu failover, wyłącz lokację główną za pomocą [narzędzia az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Skopiuj nazwę DNS profilu Traffic Manager *(http://<* relativednsname *>.trafficmanager.net),* aby wyświetlić witrynę internetową w nowej sesji przeglądarki internetowej.
4. Sprawdź, czy aplikacja internetowa jest nadal dostępna.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy wszystko będzie gotowe, usuń grupy zasobów, aplikacje internetowe i wszystkie powiązane zasoby, używając [narzędzia az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono profil Traffic Manager, który zapewnia wysoką dostępność aplikacji internetowej. Aby dowiedzieć się więcej o kierowaniu ruchu, przejdź do samouczków usługi Traffic Manager.

> [!div class="nextstepaction"]
> [Samouczki usługi Traffic Manager](tutorial-traffic-manager-improve-website-response.md)