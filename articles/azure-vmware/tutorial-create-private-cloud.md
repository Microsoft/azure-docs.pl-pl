---
title: Samouczek — wdrażanie klastra vSphere na platformie Azure
description: Dowiedz się, jak wdrożyć klaster vSphere na platformie Azure przy użyciu rozwiązania Azure VMWare
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: b673a67fac734c9cb63e96b6a0c3dc9182f4b994
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952288"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Samouczek: Wdrażanie chmury prywatnej rozwiązania Azure VMware na platformie Azure

Rozwiązanie Azure VMware umożliwia wdrażanie klastra vSphere na platformie Azure. Minimalne początkowe wdrożenie to trzy hosty. Dodatkowe hosty mogą być dodawane pojedynczo do maksymalnie 16 hostów na klaster. 

Ponieważ rozwiązanie Azure VMware nie pozwala na zarządzanie chmurą prywatną przy użyciu lokalnego programu vCenter podczas uruchamiania, wymagana jest dodatkowa konfiguracja. Te procedury i powiązane z nim wymagania wstępne zostały omówione w tym samouczku.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

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

### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zamiast Azure Portal utworzyć chmurę prywatną rozwiązania Azure VMware, możesz użyć interfejsu wiersza polecenia platformy Azure przy użyciu Azure Cloud Shell.  Aby zapoznać się z listą poleceń, których można używać z rozwiązaniem VMware platformy Azure, zobacz [Azure VMware Commands](/cli/azure/ext/vmware/vmware).

#### <a name="open-azure-cloud-shell"></a>Otwieranie usługi Azure Cloud Shell

Wybierz opcję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wkleić je do Cloud Shell i naciśnij klawisz **Enter** , aby go uruchomić.

#### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Tworzenie chmury prywatnej

Podaj nazwę grupy zasobów oraz chmurę prywatną, lokalizację i rozmiar klastra.

| Właściwość  | Opis  |
| --------- | ------------ |
| **-g** (nazwa grupy zasobów)     | Nazwa grupy zasobów dla zasobów w chmurze prywatnej.        |
| **-n** (nazwa chmury prywatnej)     | Nazwa chmury prywatnej rozwiązania Azure VMware.        |
| **--Lokalizacja**     | Lokalizacja używana dla chmury prywatnej.         |
| **--rozmiar klastra**     | Rozmiar klastra. Wartość minimalna to 3.         |
| **--Blok sieci**     | Blok sieciowy adresu IP CIDR używany przez chmurę prywatną. Blok adresów nie powinien nakładać się na bloki adresów używane w innych sieciach wirtualnych, które znajdują się w ramach subskrypcji i sieci lokalnych.        |
| **--SKU** | Wartość jednostki SKU: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-an-azure-vmware-solution-private-cloud"></a>Usuń chmurę prywatną rozwiązania Azure VMware

Jeśli masz chmurę prywatną rozwiązania VMware platformy Azure, która nie jest już potrzebna, możesz ją usunąć. Chmura prywatna rozwiązania Azure VMware obejmuje izolowaną domenę sieciową, co najmniej jeden klaster vSphere z obsługą dedykowanych węzłów serwera i zazwyczaj wiele maszyn wirtualnych. Po usunięciu chmury prywatnej zostaną usunięte wszystkie maszyny wirtualne, ich dane i klastry. Dedykowane węzły systemu operacyjnego są bezpiecznie czyszczone i zwracane do puli bezpłatna. Domena sieciowa obsługiwana dla klienta jest usuwana.  

> [!CAUTION]
> Usuwanie chmury prywatnej jest operacją nieodwracalną. Po usunięciu chmury prywatnej dane nie mogą zostać odzyskane, ponieważ przerywa wszystkie uruchomione obciążenia i składniki oraz niszczy wszystkie dane chmury prywatnej i ustawienia konfiguracji, w tym publiczne adresy IP.

### <a name="prerequisites"></a>Wymagania wstępne

Po usunięciu chmury prywatnej nie ma możliwości odzyskania maszyn wirtualnych i ich danych. Jeśli dane maszyny wirtualnej będą wymagane później, administrator musi najpierw utworzyć kopię zapasową wszystkich danych przed usunięciem chmury prywatnej.

### <a name="steps-to-delete-an-azure-vmware-solution-private-cloud"></a>Procedura usuwania chmury prywatnej rozwiązania Azure VMware

1. Uzyskaj dostęp do strony rozwiązań VMware platformy Azure w Azure Portal.

2. Wybierz chmurę prywatną, która ma zostać usunięta.
 
3. Wprowadź nazwę chmury prywatnej i wybierz opcję **tak**. Proces usuwania zostanie zakończony w ciągu kilku godzin.  

## <a name="azure-vmware-commands"></a>Polecenia programu VMware platformy Azure

Aby zapoznać się z listą poleceń, których można używać z rozwiązaniem VMware platformy Azure, zobacz [Azure VMware Commands](/cli/azure/ext/vmware/vmware).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz chmurę prywatną rozwiązania Azure VMware
> * Weryfikowanie wdrożenia chmury prywatnej
> * Usuń chmurę prywatną rozwiązania Azure VMware

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć pole skoku. Możesz użyć pola skoku, aby nawiązać połączenie ze środowiskiem, aby można było zarządzać chmurą prywatną lokalnie.


> [!div class="nextstepaction"]
> [Dostęp do chmury prywatnej rozwiązania Azure VMware](tutorial-access-private-cloud.md)