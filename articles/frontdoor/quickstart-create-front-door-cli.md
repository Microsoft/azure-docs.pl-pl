---
title: 'Szybki Start: Konfigurowanie wysokiej dostępności dzięki usługom frontonu platformy Azure — interfejs wiersza polecenia platformy Azure'
description: W tym przewodniku szybki start pokazano, jak utworzyć aplikację sieci Web o wysokiej dostępności i wysokiej wydajności przy użyciu interfejsu wiersza polecenia platformy Azure.
services: front-door
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 0b82c11dcd615dfbdcfd70f5f90edd7ba41b4562
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201621"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Szybki Start: Tworzenie czołowych drzwi dla globalnej aplikacji sieci Web o wysokiej dostępności przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpocznij pracę z usługami platformy Azure z przodu przy użyciu interfejsu wiersza polecenia platformy Azure, aby utworzyć globalną aplikację sieci Web o wysokiej dostępności.

Drzwi tylne kierują ruch internetowy do określonych zasobów w puli zaplecza. Zdefiniowano domenę frontonu, dodanie zasobów do puli zaplecza i utworzenie reguły routingu. W tym artykule jest używana prosta konfiguracja jednej puli zaplecza z dwoma zasobami aplikacji sieci Web i pojedynczą regułą routingu korzystającą ze ścieżki domyślnej "/*".

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Interfejs wiersza polecenia platformy Azure został zainstalowany lokalnie lub Azure Cloud Shell
- Upewnij się, że do interfejsu wiersza polecenia platformy Azure dodano rozszerzenie Front-drzwiczk

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową.

W tym przewodniku szybki start potrzebne są dwie grupy zasobów. Jedno w *środkowe stany USA* i drugie w *Południowo-środkowe stany USA*.

Utwórz grupę zasobów za pomocą [AZ Group Create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDSouthCentral \
    --location southcentralus
```

## <a name="create-two-instances-of-a-web-app"></a>Tworzenie dwóch wystąpień aplikacji sieci Web

Ten przewodnik Szybki Start wymaga dwóch wystąpień aplikacji sieci Web, która działa w różnych regionach platformy Azure. Oba wystąpienia aplikacji sieci Web działają w trybie aktywny/aktywny, więc jeden może obsłużyć ruch.

Jeśli nie masz jeszcze aplikacji sieci Web, użyj poniższego skryptu, aby skonfigurować dwa przykładowe aplikacje sieci Web.

### <a name="create-app-service-plans"></a>Tworzenie planów usługi App Service

Aby można było tworzyć aplikacje sieci Web, potrzebne są dwa plany usługi App Service — jeden w obszarze *środkowe stany USA* , a drugi w regionie *Południowo-środkowe stany USA*.

Utwórz plany usługi App Service za pomocą [AZ appService plan Create](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true):

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanSouthCentralUS \
--resource-group myRGFDSouthCentral
```

### <a name="create-web-apps"></a>Tworzenie aplikacji sieci Web

Uruchomienie następujących poleceń spowoduje utworzenie aplikacji sieci Web w każdym planie usługi App Service w poprzednim kroku. Nazwy aplikacji sieci Web muszą być unikatowe globalnie.

Utwórz aplikację sieci Web za pomocą [AZ webapp Create](/cli/azure/webapp#az_webapp_create&preserve-view=true):

```azurecli-interactive
az webapp create \
--name WebAppContoso1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso2 \
--resource-group myRGFDSouthCentral \
--plan myAppServicePlanSouthCentralUS
```

Zanotuj domyślną nazwę hosta każdej aplikacji sieci Web, aby można było zdefiniować adresy zaplecza podczas wdrażania drzwi do przodu w następnym kroku.

## <a name="create-the-front-door"></a>Utwórz drzwi tylne

Utwórz podstawowe tylne drzwi z domyślnymi ustawieniami równoważenia obciążenia, sondą kondycji i regułami routingu, wykonując następujące czynności:

Utwórz drzwi tylne przy użyciu [AZ Network Front-drzwiczk Create](/cli/azure/ext/front-door/network/front-door#ext_front_door_az_network_front_door_create&preserve-view=true):

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso1.azurewebsites.net webappcontoso2.azurewebsites.net 
```

**--Grupa zasobów:** Określ grupę zasobów, w której chcesz wdrożyć drzwi z przodu.

**--name:** Określ globalnie unikatową nazwę dla drzwi frontonu platformy Azure. 

**--akceptowane protokoły:** Akceptowane wartości to **http** i **https**. Jeśli chcesz używać obu rodzajów, oddzielonych spacjami.

**--adres zaplecza:** W tym miejscu Zdefiniuj nazwę hosta usługi Web Apps rozdzieloną spacją.

Po pomyślnym zakończeniu wdrożenia Zanotuj nazwę hosta w sekcji *frontEndpoints* .

## <a name="test-the-front-door"></a>Testowanie drzwi przednich

Otwórz przeglądarkę internetową i wprowadź nazwę hosta uzyskaną z poleceń. Drzwi tylne przekierują Twoje żądanie do jednego z zasobów zaplecza.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Strona testowania drzwi przednich":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu drzwi przednich, Usuń obie grupy zasobów. Po usunięciu grupy zasobów można także usunąć drzwi z przodu i wszystkie powiązane z nią zasoby. 

Aby usunąć grupę zasobów, użyj [AZ Group Delete](/cli/azure/group#az_group_delete&preserve-view=true):

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDSouthCentral
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:
* Front Door
* Dwie aplikacje sieci Web

Aby dowiedzieć się, jak dodać domenę niestandardową do swoich drzwi, przejdź do samouczków dotyczących drzwi.

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](front-door-custom-domain.md)
