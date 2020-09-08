---
title: Samouczek — wdrażanie klastra vSphere na platformie Azure
description: Dowiedz się, jak wdrożyć klaster vSphere na platformie Azure przy użyciu rozwiązania Azure VMWare
ms.topic: tutorial
ms.date: 09/07/2020
ms.openlocfilehash: 69a29a459ba283bb34169112ac2fa174ac6a14af
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512379"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Samouczek: Wdrażanie chmury prywatnej rozwiązania Azure VMware na platformie Azure

Rozwiązanie Azure VMware umożliwia wdrażanie klastra vSphere na platformie Azure. Minimalne początkowe wdrożenie to trzy hosty. Dodatkowe hosty mogą być dodawane pojedynczo do maksymalnie 16 hostów na klaster. 

Ponieważ rozwiązanie Azure VMware nie pozwala na zarządzanie chmurą prywatną przy użyciu lokalnego programu vCenter podczas uruchamiania, wymagana jest dodatkowa konfiguracja i połączenie z lokalnym wystąpieniem programu vCenter, siecią wirtualną i wieloma innymi. Te procedury i powiązane z nim wymagania wstępne zostały omówione w tym samouczku.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz chmurę prywatną rozwiązania Azure VMware
> * Weryfikowanie wdrożenia chmury prywatnej

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Odpowiednie prawa administracyjne i uprawnienia do tworzenia chmury prywatnej.
- Upewnij się, że skonfigurowano odpowiednią sieć, zgodnie z opisem w [samouczku: Lista kontrolna sieci](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>Tworzenie chmury prywatnej

Chmurę prywatną rozwiązania Azure VMware można utworzyć za pomocą [Azure Portal](#azure-portal) lub przy użyciu [interfejsu wiersza polecenia platformy Azure](#azure-cli).

### <a name="azure-portal"></a>Witryna Azure Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-avs-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zamiast Azure Portal utworzyć chmurę prywatną rozwiązania Azure VMware, możesz użyć interfejsu wiersza polecenia platformy Azure przy użyciu Azure Cloud Shell. Jest to bezpłatna interaktywna powłoka ze wspólnymi narzędziami platformy Azure wstępnie zainstalowanymi i skonfigurowanymi do użycia z Twoim kontem. 

#### <a name="open-azure-cloud-shell"></a>Otwieranie usługi Azure Cloud Shell

Aby otworzyć Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wkleić je do Cloud Shell i naciśnij klawisz **Enter** , aby go uruchomić.

#### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Tworzenie chmury prywatnej

Podaj nazwę grupy zasobów, nazwę chmury prywatnej, lokalizację, rozmiar klastra.

| Właściwość  | Opis  |
| --------- | ------------ |
| **-g** (nazwa grupy zasobów)     | Nazwa grupy zasobów dla zasobów w chmurze prywatnej.        |
| **-n** (nazwa chmury prywatnej)     | Nazwa chmury prywatnej rozwiązania Azure VMware.        |
| **--Lokalizacja**     | Lokalizacja używana dla chmury prywatnej.         |
| **--rozmiar klastra**     | Rozmiar klastra. Wartość minimalna to 3.         |
| **--Blok sieci**     | Blok sieciowy adresu IP CIDR używany przez chmurę prywatną. Blok adresów nie powinien nakładać się na bloki adresów używane w innych sieciach wirtualnych, które znajdują się w ramach subskrypcji i sieci lokalnych.        |
| **--SKU** | Wartość jednostki SKU: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>Usuń chmurę prywatną (Azure Portal)

Jeśli masz chmurę prywatną rozwiązania VMware platformy Azure, która nie jest już potrzebna, możesz ją usunąć. Po usunięciu chmury prywatnej zostaną usunięte wszystkie klastry wraz ze wszystkimi składnikami.

Aby to zrobić, przejdź do chmury prywatnej w Azure Portal i wybierz pozycję **Usuń**. Na stronie Potwierdzenie Potwierdź nazwę chmury prywatnej, a następnie wybierz pozycję **tak**.

> [!CAUTION]
> Usuwanie chmury prywatnej jest operacją nieodwracalną. Po usunięciu chmury prywatnej dane nie mogą zostać odzyskane, ponieważ kończy wszystkie uruchomione obciążenia, składniki i niszczy wszystkie dane w chmurze prywatnej oraz ustawienia konfiguracji, w tym publiczne adresy IP. 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz chmurę prywatną rozwiązania Azure VMware
> * Zweryfikowano wdrożenie chmury prywatnej

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć sieć wirtualną do użycia z chmurą prywatną w ramach konfigurowania lokalnego zarządzania klastrami w chmurze prywatnej.

> [!div class="nextstepaction"]
> [Tworzenie Virtual Network](tutorial-configure-networking.md)
