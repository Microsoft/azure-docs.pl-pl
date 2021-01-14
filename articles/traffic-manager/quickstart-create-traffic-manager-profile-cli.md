---
title: 'Szybki Start: Tworzenie profilu dla HA aplikacji — interfejs wiersza polecenia platformy Azure — Azure Traffic Manager'
description: W tym artykule szybki start opisano sposób tworzenia profilu Traffic Manager w celu utworzenia aplikacji sieci Web o wysokiej dostępności przy użyciu interfejsu wiersza polecenia platformy Azure.
services: traffic-manager
author: duongau
mnager: kumud
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07fadd7b3129b3ca3351e0416c8aa6f49de82212
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201233"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Szybki Start: Tworzenie profilu Traffic Manager dla aplikacji sieci Web o wysokiej dostępności przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start opisano tworzenie profilu usługi Traffic Manager, który zapewni wysoką dostępność aplikacji internetowej.

W tym przewodniku szybki start utworzysz dwa wystąpienia aplikacji sieci Web. Każde z nich jest uruchamiane w innym regionie świadczenia usługi Azure. Utworzysz profil usługi Traffic Manager na podstawie [priorytetu punktu końcowego](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil kieruje ruch użytkowników do lokacji głównej, w której działa aplikacja internetowa. Usługa Traffic Manager stale monitoruje aplikację internetową. Jeśli lokacja główna jest niedostępna, usługa zapewnia automatyczne przejście w tryb failover w lokacji zapasowej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil Traffic Manager za pomocą polecenia [AZ Network Traffic-Manager profile Create](/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) , które kieruje ruch użytkowników na podstawie priorytetu punktu końcowego.

W poniższym przykładzie Zastąp **<profile_name>** z unikatową nazwą profilu Traffic Manager.

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

## <a name="create-web-apps"></a>Tworzenie aplikacji sieci Web

W tym przewodniku Szybki start będą potrzebne dwa wystąpienia aplikacji internetowej wdrożone w dwóch różnych regionach świadczenia usługi Azure (*Wschodnie stany USA* i *Europa Zachodnia*). Każda będzie służyć jako podstawowy punkt końcowy i punkt końcowy trybu failover dla usługi Traffic Manager.

### <a name="create-web-app-service-plans"></a>Tworzenie planów usługi aplikacji sieci Web
Utwórz plany usługi aplikacji sieci Web za pomocą polecenia [AZ appService plan Create](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) dla dwóch wystąpień aplikacji sieci Web, które zostaną wdrożone w dwóch różnych regionach świadczenia usługi Azure.

W poniższym przykładzie Zastąp **<appspname_eastus>** i **<appspname_westeurope>** z unikatową nazwą planu App Service

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

### <a name="create-a-web-app-in-the-app-service-plan"></a>Tworzenie aplikacji sieci Web w planie usługi App Service
Utwórz dwa wystąpienia aplikacji sieci Web używającej polecenia [AZ webapp Create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) w planach App Service w regionach *Wschodnie stany USA* i *Europa Zachodnia* .

W poniższym przykładzie Zastąp **<app1name_eastus>** i **<app2name_westeurope>** z unikatową nazwą aplikacji, a następnie zastąp ciąg **<appspname_eastus**>i **<** appspname_westeurope>przy użyciu nazwy użytej do utworzenia planów App Service w poprzedniej sekcji.

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
Dodaj dwa Web Apps jako punkty końcowe Traffic Manager za pomocą polecenia [AZ Network Traffic-Manager Endpoint Create](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) do profilu Traffic Manager w następujący sposób:

- Określ identyfikator aplikacji sieci Web i Dodaj aplikację sieci Web znajdującą się w regionie *Wschodnie stany USA* platformy Azure jako podstawowy punkt końcowy, aby skierować cały ruch użytkownika. 
- Określ identyfikator aplikacji sieci Web i Dodaj aplikację sieci Web znajdującą się w regionie platformy Azure *Europa Zachodnia* jako punkt końcowy trybu failover. 

Gdy podstawowy punkt końcowy będzie niedostępny, ruch będzie automatycznie kierowany do punktu końcowego trybu failover.

W poniższym przykładzie Zastąp **<app1name_eastus>** i **<App2name_westeurope>** z nazwami aplikacji utworzonymi dla każdego regionu w poprzedniej sekcji. Następnie zastąp **<profile_name>** nazwą profilu używaną w poprzedniej sekcji. 

**Wschodnie stany USA**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Zanotuj identyfikator wyświetlany w danych wyjściowych i użyj następującego polecenia, aby dodać punkt końcowy:

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

Zanotuj identyfikator wyświetlany w danych wyjściowych i użyj następującego polecenia, aby dodać punkt końcowy:

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

## <a name="test-your-traffic-manager-profile"></a>Testowanie profilu Traffic Manager

W tej sekcji sprawdzisz nazwę domeny profilu usługi Traffic Manager. Skonfigurujesz również podstawowy punkt końcowy tak, aby był niedostępny. Na końcu zobaczysz, że aplikacja internetowa jest nadal dostępna. Dzieje się tak, ponieważ usługa Traffic Manager wysyła ruch do punktu końcowego trybu failover.

W poniższym przykładzie Zastąp **<app1name_eastus>** i **<App2name_westeurope>** z nazwami aplikacji utworzonymi dla każdego regionu w poprzedniej sekcji. Następnie zastąp **<profile_name>** nazwą profilu używaną w poprzedniej sekcji.

### <a name="determine-the-dns-name"></a>Ustalanie nazwy DNS

Określ nazwę DNS profilu Traffic Manager za pomocą polecenia [AZ Network Traffic-Manager profile show](/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Skopiuj wartość **RelativeDnsName** . Nazwa DNS profilu Traffic Manager to *http://<* relativednsname *>. trafficmanager.NET*. 

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager
1. W przeglądarce sieci Web wprowadź nazwę DNS profilu Traffic Manager (*http://<* relativednsname *>. trafficmanager.NET*), aby wyświetlić domyślną witrynę sieci Web Twojej aplikacji internetowej.

    > [!NOTE]
    > W tym scenariuszu Szybki start wszystkie żądania są kierowane do podstawowego punktu końcowego. Jest on ustawiony na wartość **Priorytet 1**.
2. Aby wyświetlić Traffic Manager tryb failover w działaniu, wyłącz lokację główną przy użyciu polecenia [AZ Network Traffic-Manager Endpoint Update](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Skopiuj nazwę DNS profilu Traffic Manager (*http://<* relativednsname *>. trafficmanager.NET*), aby wyświetlić witrynę internetową w nowej sesji przeglądarki sieci Web.
4. Sprawdź, czy aplikacja internetowa jest nadal dostępna.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy wszystko będzie gotowe, Usuń grupy zasobów, aplikacje sieci Web i wszystkie pokrewne zasoby za pomocą polecenia [AZ Group Delete](/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono profil Traffic Manager, który zapewnia wysoką dostępność aplikacji sieci Web. Aby dowiedzieć się więcej o kierowaniu ruchu, przejdź do samouczków usługi Traffic Manager.

> [!div class="nextstepaction"]
> [Samouczki usługi Traffic Manager](tutorial-traffic-manager-improve-website-response.md)