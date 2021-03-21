---
title: Rozwiązania do przetwarzania poufnego platformy Azure na maszynach wirtualnych
description: Dowiedz się więcej o rozwiązaniach do przetwarzania danych poufnych platformy Azure na maszynach wirtualnych.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 8621dc8cfc10ab44ecb358a40fdae1a1b2081734
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566587"
---
# <a name="solutions-on-azure-virtual-machines"></a>Rozwiązania na platformie Azure Virtual Machines

Ten artykuł zawiera informacje na temat wdrażania maszyn wirtualnych w systemie Azure, które korzystają z procesorów firmy Intel, które są obsługiwane przez [rozszerzenie Microsoft Software Guard](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>Poufne rozmiary maszyn wirtualnych platformy Azure

Poufne maszyny wirtualne platformy Azure są przeznaczone do ochrony poufności i integralności danych i kodu podczas przetwarzania w chmurze. 

[Seria DCsv2](../virtual-machines/dcv2-series.md) Maszyny wirtualne to najnowsza i Najnowsza rodzina rozmiarów informacji poufnych. Te maszyny wirtualne obsługują większy zakres możliwości wdrażania, mają 2. enklawy stronę sieci Web (EPC) i większy wybór rozmiarów w porównaniu z naszymi DC-Series maszynami wirtualnymi. Maszyny wirtualne z [serii DC](../virtual-machines/sizes-previous-gen.md#preview-dc-series) są obecnie w wersji zapoznawczej i będą przestarzałe i nieuwzględnione w ogólnej dostępności.

Zacznij wdrożyć maszynę wirtualną DCsv2-Series za pośrednictwem komercyjnej witryny Marketplace firmy Microsoft, postępując zgodnie z [samouczkiem szybki start](quick-create-marketplace.md).

### <a name="current-available-sizes-and-regions"></a>Bieżące dostępne rozmiary i regiony

Aby wyświetlić listę wszystkich ogólnie dostępnych poufnych rozmiarów maszyn wirtualnych w dostępnych regionach i strefach dostępności, uruchom następujące polecenie w [interfejsie wiersza polecenia platformy Azure](/cli/azure/install-azure-cli-windows):

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

Aby zapoznać się z bardziej szczegółowym widokiem powyższych rozmiarów, uruchom następujące polecenie:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Wymagania dedykowanego hosta
Wdrożenie **Standard_DC8_v2** rozmiaru maszyny wirtualnej w rodzinie DCSv2-Series VM zajmie pełny Host i nie będzie współużytkowane z innymi dzierżawcami lub subskrypcjami. Ta rodzina SKU maszyn wirtualnych zapewnia izolację, którą może być potrzebna w celu spełnienia wymagań prawnych dotyczących zgodności i zabezpieczeń, które zwykle są spełnione przez posiadanie dedykowanej usługi hosta. W przypadku wybrania **Standard_DC8_v2** jednostki SKU fizyczny serwer hosta przydzieli wszystkie dostępne zasoby sprzętowe, w tym tylko pamięć sygnatury EPC dla maszyny wirtualnej. Należy pamiętać, że ta funkcja istnieje w ramach projektu infrastruktury i wszystkie funkcje **Standard_DC8_v2** będą obsługiwane. To wdrożenie nie jest takie samo jak [dedykowana usługa hosta platformy Azure](../virtual-machines/dedicated-hosts.md) dostarczana przez inne rodziny maszyn wirtualnych platformy Azure.


## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Postępuj zgodnie z samouczkiem szybkiego startu, aby wdrożyć maszynę wirtualną DCsv2-Series w mniej niż 10 minut. 

- **Subskrypcja platformy Azure** — aby wdrożyć poufne wystąpienie maszyny wirtualnej, weź pod uwagę subskrypcję z płatność zgodnie z rzeczywistym użyciem lub inną opcję zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), nie masz przydziału dla odpowiedniej ilości rdzeni obliczeniowych platformy Azure.

- **Cennik i dostępność regionalna** — znajdź Cennik DCsv2-Series maszyn wirtualnych na [stronie cennika maszyny wirtualnej](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Sprawdź dostępność [produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) w regionach świadczenia usługi Azure.


- **Przydział rdzeni** — może być konieczne zwiększenie limitu przydziału rdzeni w ramach subskrypcji platformy Azure z wartości domyślnej. Twoja subskrypcja może również ograniczyć liczbę rdzeni, które można wdrożyć w niektórych rodzinach rozmiarów maszyn wirtualnych, w tym serii DCsv2. Aby zażądać zwiększenia limitu przydziału, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](../azure-portal/supportability/per-vm-quota-requests.md) . Uwaga, limity domyślne mogą się różnić w zależności od kategorii subskrypcji.

  > [!NOTE]
  > Skontaktuj się z pomocą techniczną platformy Azure, jeśli potrzebujesz dużej pojemności. Przydziały platformy Azure to limity kredytowe, a nie gwarancje wydajności. Niezależnie od limitu przydziału opłata jest naliczana tylko za używane rdzenie.
  
- Zmiana **rozmiaru** — ze względu na wyspecjalizowany sprzęt można zmienić rozmiar poufnych wystąpień obliczeniowych w ramach tej samej rodziny. Na przykład można zmienić rozmiar maszyny wirtualnej serii DCsv2 z jednego rozmiaru serii DCsv2 na inny. Zmienianie rozmiaru niepoufnego rozmiaru obliczeniowego na poufne nie jest obsługiwane.  

- **Obraz** — w celu zapewnienia obsługi rozszerzenia firmy Intel (Intel SGX) w odniesieniu do poufnych wystąpień obliczeniowych wszystkie wdrożenia muszą być uruchamiane na obrazach generacji 2. Usługa Azure CONFIDENTIAL Computing obsługuje obciążenia działające w Ubuntu 18,04 Gen 2, Ubuntu 16,04 Gen 2, Windows Server 2019 Gen2 i Windows Server 2016 Gen 2. Przeczytaj o [obsłudze maszyn wirtualnych 2. generacji na platformie Azure](../virtual-machines/generation-2.md) , aby dowiedzieć się więcej na temat obsługiwanych i nieobsługiwanych scenariuszy. 

- **Magazyn** — usługa Azure poufne dyski danych maszyn wirtualnych i naszych tymczasowych dysków systemu operacyjnego znajdują się na dyskach interfejsu NVMe. Wystąpienia obsługują tylko SSD w warstwie Premium i SSD w warstwie Standardowa dysków, a nie SSD w warstwie Ultra lub HDD w warstwie Standardowa. Rozmiar maszyny wirtualnej **DC8_v2** nie obsługuje magazynu w warstwie Premium. 

- **Szyfrowanie dysków** — poufne wystąpienia obliczeniowe nie obsługują teraz szyfrowania dysków. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Zagadnienia dotyczące wysokiej dostępności i odzyskiwania po awarii

W przypadku korzystania z maszyn wirtualnych na platformie Azure użytkownik jest odpowiedzialny za wdrożenie rozwiązania wysokiej dostępności i odzyskiwania po awarii w celu uniknięcia wszelkich przestojów. 

Dane poufne platformy Azure nie obsługują nadmiarowości strefy za pośrednictwem Strefy dostępności w tym momencie. Aby zapewnić najwyższą dostępność i nadmiarowość na potrzeby danych poufnych, użyj [zestawów dostępności](../virtual-machines/availability-set-overview.md). Ze względu na ograniczenia sprzętowe zestawy dostępności dla wystąpień z danymi poufnymi mogą mieć maksymalnie 10 domen aktualizacji. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Wdrożenie z szablonem Azure Resource Manager (ARM)

Usługa Azure Resource Manager to usługa wdrażania i zarządzania dla platformy Azure. Zapewnia ona warstwę zarządzania, która umożliwia tworzenie, aktualizowanie i usuwanie zasobów w ramach subskrypcji platformy Azure. Możesz użyć funkcji zarządzania, takich jak kontrola dostępu, blokady i Tagi, aby zabezpieczyć i zorganizować zasoby po wdrożeniu.

Aby dowiedzieć się więcej na temat szablonów ARM, zobacz [Template Deployment Omówienie](../azure-resource-manager/templates/overview.md).

Aby wdrożyć maszynę wirtualną DCsv2-Series w szablonie ARM, użyjesz [zasobu maszyny wirtualnej](../virtual-machines/windows/template-description.md). Upewnij się, że określono poprawne właściwości **vmSize** i **elementu imagereference**.

### <a name="vm-size"></a>Rozmiar maszyny wirtualnej

Określ jeden z następujących rozmiarów w szablonie ARM w zasobów maszyny wirtualnej. Ten ciąg jest umieszczany jako **vmSize** we **właściwościach**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Obraz systemu operacyjnego Gen2

W obszarze **Właściwości** należy również odwoływać się do obrazu w obszarze **obszarze storageprofile**. Użyj *tylko jednego* z następujących obrazów dla **elementu imagereference**.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Następne kroki 

W tym artykule omówiono kwalifikacje i konfiguracje potrzebne podczas tworzenia poufnej maszyny wirtualnej. Teraz możesz przejść do Microsoft Azure Marketplace, aby wdrożyć maszynę wirtualną DCsv2-Series.

> [!div class="nextstepaction"]
> [Wdrażanie DCsv2-Series maszyny wirtualnej w portalu Azure Marketplace](quick-create-marketplace.md)