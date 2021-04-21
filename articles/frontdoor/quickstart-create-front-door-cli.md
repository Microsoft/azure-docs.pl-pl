---
title: 'Szybki start: konfigurowanie wysokiej dostępności za pomocą usługi Azure Front Door — interfejs wiersza polecenia platformy Azure'
description: Ten przewodnik Szybki start pokazuje, jak używać usługi Azure Front Door do tworzenia globalnej aplikacji internetowej o wysokiej dostępności i wysokiej wydajności przy użyciu interfejsu wiersza polecenia platformy Azure.
services: front-door
author: duongau
manager: KumudD
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/19/2021
ms.author: duau
ms.openlocfilehash: 99204a2d4c3a2455f0916878fb09a348dc79ac7a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778781"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Szybki start: tworzenie aplikacji Front Door dla globalnej aplikacji internetowej o wysokiej dostępie przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpoczynanie pracy z Azure Front Door przy użyciu interfejsu wiersza polecenia platformy Azure w celu utworzenia globalnej aplikacji internetowej o wysokiej dostępnej wydajności.

Interfejs Front Door kieruje ruch internetowy do określonych zasobów w puli zaplecza. Zdefiniowano domenę frontoni, dodasz zasoby do puli zaplecza i utworzysz regułę rozsyłania. W tym artykule użyto prostej konfiguracji jednej puli zaplecza z dwoma zasobami aplikacji internetowej i pojedynczej reguły routingu przy użyciu domyślnego dopasowania ścieżki "/*".

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="Diagram przedstawiający Front Door wdrażania przy użyciu interfejsu wiersza polecenia platformy Azure." border="false":::

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Interfejs wiersza polecenia platformy Azure zainstalowany lokalnie lub Azure Cloud Shell
- Upewnij się, że rozszerzenie usługi Front Door jest dodane do interfejsu wiersza polecenia platformy Azure

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i używać go lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową.

W tym przewodniku Szybki start potrzebne są dwie grupy zasobów. Jeden w *środkowych usa* i drugi w *południowo-środkowych usa*.

Utwórz grupę zasobów za pomocą [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDEast \
    --location eastus
```

## <a name="create-two-instances-of-a-web-app"></a>Tworzenie dwóch wystąpień aplikacji internetowej

W tym przewodniku Szybki start wymagane są dwa wystąpienia aplikacji internetowej, które działają w różnych regionach świadczenia usługi Azure. Oba wystąpienia aplikacji internetowej działają w trybie aktywny/aktywny, więc jedno z nich może serwisować ruch.

Jeśli nie masz jeszcze aplikacji internetowej, użyj poniższego skryptu, aby skonfigurować dwie przykładowe aplikacje internetowe.

### <a name="create-app-service-plans"></a>Tworzenie planów usługi App Service

Aby można było utworzyć aplikacje internetowe, potrzebne będą  dwa plany usługi App Service: jeden w środkowych usa i drugi w *regionach Wschodnie usa.*

Utwórz plany usługi App Service za pomocą [az appservice plan create:](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true)

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanEastUS \
--resource-group myRGFDEast
```

### <a name="create-web-apps"></a>Tworzenie aplikacji internetowych

Uruchomienie poniższych poleceń spowoduje utworzenie aplikacji internetowej w każdym planie usługi App Service w poprzednim kroku. Nazwy aplikacji internetowych muszą być globalnie unikatowe.

Utwórz aplikację internetową za pomocą [az webapp create](/cli/azure/webapp#az_webapp_create&preserve-view=true):

```azurecli-interactive
az webapp create \
--name WebAppContoso-1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso-2 \
--resource-group myRGFDEast \
--plan myAppServicePlanEastUS
```

Zanotuj domyślną nazwę hosta każdej aplikacji internetowej, aby można było zdefiniować adresy zaplecza podczas wdrażania Front Door w następnym kroku.

## <a name="create-the-front-door"></a>Tworzenie Front Door

Utwórz podstawową regułę Front Door domyślnymi ustawieniami równoważenia obciążenia, sondą kondycji i regułami routingu, uruchamiając następujące czynności:

Utwórz Front Door za pomocą [az network front-door create](/cli/azure/ext/front-door/network/front-door#ext_front_door_az_network_front_door_create&preserve-view=true):

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso-1.azurewebsites.net webappcontoso-2.azurewebsites.net 
```

**--resource-group:** Określ grupę zasobów, w której chcesz wdrożyć Front Door.

**--name:** Określ globalnie unikatową nazwę Azure Front Door. 

**--accepted-protocols:** Akceptowane wartości to **http** i **https.** Jeśli chcesz użyć obu, należy użyć obu rozdzielonych spacjami.

**--backend-address:** W tym miejscu zdefiniuj nazwę hosta obu aplikacji internetowych oddzieloną spacją.

Po pomyślnym zakończeniu wdrażania zanotuj nazwę hosta w *sekcji frontEndpoints.*

## <a name="test-the-front-door"></a>Testowanie Front Door

Otwórz przeglądarkę internetową i wprowadź nazwę hosta uzyskaną z poleceń . Żądanie Front Door skierować żądanie do jednego z zasobów zaplecza.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Front Door testowania":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą Front Door, usuń obie grupy zasobów. Usunięcie grupy zasobów powoduje również usunięcie grupy zasobów Front Door wszystkich powiązanych zasobów. 

Aby usunąć grupę zasobów, użyj [az group delete](/cli/azure/group#az_group_delete&preserve-view=true):

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDEast
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono:
* Front Door
* Dwie aplikacje internetowe

Aby dowiedzieć się, jak dodać domenę niestandardową do Front Door, przejdź do Front Door samouczków.

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](front-door-custom-domain.md)
