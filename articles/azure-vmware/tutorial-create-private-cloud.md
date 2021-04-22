---
title: Samouczek — wdrażanie Azure VMware Solution prywatnej
description: Dowiedz się, jak utworzyć i wdrożyć Azure VMware Solution chmurę prywatną
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: ea4bf27a1ff14e4872bc2a0e19daa032dd4ba66d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870917"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>Samouczek: wdrażanie Azure VMware Solution prywatnej

Azure VMware Solution umożliwia wdrożenie klastra vSphere na platformie Azure. Minimalne początkowe wdrożenie to trzy hosty. Dodatkowe hosty można dodawać po jednym na raz, maksymalnie 16 hostów na klaster.

Ponieważ Azure VMware Solution nie pozwala na zarządzanie chmurą prywatną przy użyciu lokalnego programu vCenter podczas uruchamiania, potrzebna jest dodatkowa konfiguracja. Te procedury i powiązane wymagania wstępne zostały uwzględnione w tym samouczku.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie chmury Azure VMware Solution prywatnej
> * Weryfikowanie wdrożonej chmury prywatnej

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Odpowiednie prawa administracyjne i uprawnienia do tworzenia chmury prywatnej. Musisz mieć co najmniej poziom współautora w subskrypcji.
- Postępuj zgodnie z informacjami zebranymi w artykule [planowania,](production-ready-deployment-steps.md) aby wdrożyć Azure VMware Solution.
- Upewnij się, że skonfigurowano odpowiednią sieć zgodnie z opisem w te [tematu Samouczek: Lista kontrolna sieci.](tutorial-network-checklist.md)
- Hosty zostały zaaprowizowane, a dostawca zasobów Microsoft.AVS został zarejestrowany zgodnie z opisem w żądaniu hostów i włącz [dostawcę zasobów Microsoft.AVS.](enable-azure-vmware-solution.md)

## <a name="create-a-private-cloud"></a>Tworzenie chmury prywatnej

Możesz utworzyć chmurę prywatną Azure VMware Solution przy użyciu interfejsu [Azure Portal](#azure-portal) lub interfejsu wiersza [polecenia platformy Azure.](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zamiast tworzyć Azure Portal chmury Azure VMware Solution, można użyć interfejsu wiersza polecenia platformy Azure przy użyciu Azure Cloud Shell.  Aby uzyskać listę poleceń, których można używać z programem Azure VMware Solution, zobacz Azure VMware commands (Polecenia [programu Azure VMware).](/cli/azure/vmware)

#### <a name="open-azure-cloud-shell"></a>Otwieranie usługi Azure Cloud Shell

Wybierz **pozycję Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić aplikację Cloud Shell osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz **pozycję** Kopiuj, aby skopiować bloki kodu, wklej je do Cloud Shell i naciśnij klawisz **Enter,** aby je uruchomić.

#### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą [polecenia "az group create".](/cli/azure/group) Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o *nazwie myResourceGroup* w *lokalizacji eastus:*

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Tworzenie chmury prywatnej

Podaj nazwę grupy zasobów i chmurę prywatną, lokalizację i rozmiar klastra.

| Właściwość  | Opis  |
| --------- | ------------ |
| **-g** (Nazwa grupy zasobów)     | Nazwa grupy zasobów dla zasobów chmury prywatnej.        |
| **-n** (Nazwa chmury prywatnej)     | Nazwa chmury Azure VMware Solution prywatnej.        |
| **--location**     | Lokalizacja używana dla chmury prywatnej.         |
| **--cluster-size**     | Rozmiar klastra. Minimalna wartość to 3.         |
| **--network-block**     | Blok sieciowy adresów IP CIDR do użycia w chmurze prywatnej. Blok adresów nie powinien pokrywać się z blokami adresów używanymi w innych sieciach wirtualnych, które znajdują się w twojej subskrypcji i sieciach lokalnych.        |
| **--sku** | Wartość SKU: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="azure-vmware-commands"></a>Polecenia programu Azure VMware

Aby uzyskać listę poleceń, których można używać z programem Azure VMware Solution, [zobacz Azure VMware commands (Polecenia programu Azure VMware).](/cli/azure/vmware)

## <a name="next-steps"></a>Następne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie chmury Azure VMware Solution prywatnej
> * Weryfikowanie wdrożonej chmury prywatnej
> * Usuwanie Azure VMware Solution prywatnej

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć pole przeskoku. Za pomocą usługi przeskoku możesz połączyć się ze środowiskiem, aby zarządzać chmurą prywatną lokalnie.


> [!div class="nextstepaction"]
> [Uzyskiwanie dostępu do Azure VMware Solution prywatnej](tutorial-access-private-cloud.md)
