---
title: Rozwiązania do poufnego przetwarzania na platformie Azure na maszynach wirtualnych
description: Dowiedz się więcej o rozwiązaniach do poufnego przetwarzania na platformie Azure na maszynach wirtualnych.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 580c53f311bc8ee70e974df2bc4111e6361d06f6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818966"
---
# <a name="solutions-on-azure-virtual-machines"></a>Rozwiązania na maszynach wirtualnych platformy Azure

Ten artykuł zawiera informacje na temat wdrażania maszyn wirtualnych do poufnego przetwarzania na platformie Azure z procesorami Firmy Intel opartymi na rozszerzeniu [Intel Software Guard Extension](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>Rozmiary maszyn wirtualnych do poufnego przetwarzania na platformie Azure

Maszyny wirtualne poufnego przetwarzania na platformie Azure zostały zaprojektowane w celu ochrony poufności oraz integralności danych i kodu podczas ich przetwarzania w chmurze 

[Seria DCsv2](../virtual-machines/dcv2-series.md) Maszyny wirtualne to najnowsza i najnowsza rodzina rozmiaru poufnego przetwarzania. Te maszyny wirtualne obsługują większy zakres możliwości wdrażania, mają 2 razy większą pamięć podręczną stronicowania enklawy (EPC) i większy wybór rozmiarów w porównaniu do DC-Series wirtualnych. Maszyny [wirtualne serii DC](../virtual-machines/sizes-previous-gen.md#preview-dc-series) są obecnie w wersji zapoznawczej i zostaną wycofane i nie będą ogólnie dostępne.

Rozpocznij wdrażanie maszyny wirtualnej DCsv2-Series za pośrednictwem platformy handlowej firmy Microsoft, korzystając z [samouczka Szybki start.](quick-create-marketplace.md)

### <a name="current-available-sizes-and-regions"></a>Bieżące dostępne rozmiary i regiony

Aby uzyskać listę wszystkich ogólnie dostępnych rozmiarów poufnych obliczeniowych maszyn wirtualnych w dostępnych regionach i strefach dostępności, uruchom następujące polecenie w interfejsie wiersza [polecenia platformy Azure:](/cli/azure/install-azure-cli-windows)

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

Aby uzyskać bardziej szczegółowy widok powyższych rozmiarów, uruchom następujące polecenie:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Wymagania dotyczące dedykowanego hosta
Wdrożenie Standard_DC8_v2 **maszyny** wirtualnej w rodzinie maszyn wirtualnych DCSv2-Series będzie zajmować pełnego hosta i nie będzie współdzielone z innymi dzierżawami ani subskrypcjami. Ta rodzina SKU maszyn wirtualnych zapewnia izolację, która może być potrzebna w celu spełnienia wymagań prawnych dotyczących zgodności i zabezpieczeń, które zwykle są spełnione przez posiadanie dedykowanej usługi hosta. Po wybraniu **Standard_DC8_v2** SKU serwer fizyczny hosta przydzieli do maszyny wirtualnej wszystkie dostępne zasoby sprzętowe, w tym tylko pamięć EPC. Należy pamiętać, że ta funkcja istnieje w projekcie infrastruktury i wszystkie funkcje Standard_DC8_v2 **będą** obsługiwane. To wdrożenie nie jest tym samym, [co usługa Azure Dedicated Host,](../virtual-machines/dedicated-hosts.md) która jest dostarczana przez inne rodziny maszyn wirtualnych platformy Azure.


## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Postępuj zgodnie z samouczkiem Szybki start, aby wdrożyć maszynę wirtualną DCsv2-Series w mniej niż 10 minut. 

- **Subskrypcja platformy Azure** — aby wdrożyć wystąpienie maszyny wirtualnej poufnego przetwarzania, należy wziąć pod uwagę subskrypcję z płatnością zgodnie z użytkowniku lub inną opcję zakupu. Jeśli używasz bezpłatnego konta [platformy Azure,](https://azure.microsoft.com/free/)nie masz limitu przydziału odpowiedniej ilości rdzeni obliczeniowych platformy Azure.

- **Ceny i dostępność regionalna** — cennik maszyn wirtualnych DCsv2-Series można znaleźć na [stronie z cenami maszyn wirtualnych.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) Sprawdź [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) w regionach świadczenia usługi Azure.


- **Limit przydziału rdzeni** — może być konieczne zwiększenie limitu przydziału rdzeni w subskrypcji platformy Azure z wartości domyślnej. Subskrypcja może również ograniczać liczbę rdzeni, które można wdrożyć w niektórych rodzinach rozmiarów maszyn wirtualnych, w tym w serii DCsv2. Aby zażądać zwiększenia limitu przydziału, [otwórz żądanie pomocy technicznej online](../azure-portal/supportability/per-vm-quota-requests.md) bez opłat. Pamiętaj, że limity domyślne mogą się różnić w zależności od kategorii subskrypcji.

  > [!NOTE]
  > Jeśli pomoc techniczna platformy Azure pojemności na dużą skalę, skontaktuj się z działem pomocy. Limity przydziału platformy Azure to limity środków, a nie gwarancje pojemności. Niezależnie od limitu przydziału opłaty są naliczane tylko za rdzenie, których używasz.
  
- **Zmiana rozmiaru** — ze względu na wyspecjalizowany sprzęt można zmienić rozmiar poufnych wystąpień obliczeniowych tylko w obrębie tej samej rodziny rozmiarów. Na przykład rozmiar maszyny wirtualnej serii DCsv2 można zmienić tylko z jednego rozmiaru serii DCsv2 na inny. Zmiana rozmiaru z nieoufnych danych obliczeniowych na poufny rozmiar obliczeniowy nie jest obsługiwana.  

- **Image** — aby zapewnić obsługę rozszerzenia Intel Software Guard Extension (Intel SGX) w poufnych wystąpieniach obliczeniowych, wszystkie wdrożenia muszą być uruchamiane na obrazach generacji 2. Poufne przetwarzanie na platformie Azure obsługuje obciążenia działające w systemach Ubuntu 18.04 Gen 2, Ubuntu 20.04 Gen 2, Windows Server 2019 Gen2 i Windows Server 2016 Gen 2. Przeczytaj o pomocy technicznej dla maszyn wirtualnych [2. generacji na platformie Azure,](../virtual-machines/generation-2.md) aby dowiedzieć się więcej o obsługiwanych i nieobsługiwanych scenariuszach. 

- **Magazyn** — dyski danych maszyny wirtualnej poufnego przetwarzania na platformie Azure i nasze efemeralne dyski systemu operacyjnego znajdują się na dyskach NVMe. Wystąpienia obsługują tylko SSD w warstwie Premium i SSD w warstwie Standardowa, a nie SSD w warstwie Ultra lub HDD w warstwie Standardowa. Rozmiar maszyny **wirtualnej DC8_v2** nie obsługuje magazynu Premium Storage. 

- **Szyfrowanie dysków** — poufne wystąpienia obliczeniowe nie obsługują obecnie szyfrowania dysków. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Zagadnienia dotyczące wysokiej dostępności i odzyskiwania po awarii

W przypadku korzystania z maszyn wirtualnych na platformie Azure odpowiadasz za wdrożenie rozwiązania wysokiej dostępności i odzyskiwania po awarii w celu uniknięcia przestojów. 

Poufne przetwarzanie na platformie Azure nie obsługuje obecnie nadmiarowości stref za pośrednictwem Strefy dostępności danych. Aby uzyskać najwyższą dostępność i nadmiarowość poufnego przetwarzania, użyj [zestawów dostępności](../virtual-machines/availability-set-overview.md). Ze względu na ograniczenia sprzętowe zestawy dostępności dla poufnych wystąpień obliczeniowych mogą mieć maksymalnie 10 domen aktualizacji. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Wdrażanie za pomocą Azure Resource Manager (ARM)

Usługa Azure Resource Manager to usługa wdrażania i zarządzania dla platformy Azure. Zapewnia warstwę zarządzania, która umożliwia tworzenie, aktualizowanie i usuwanie zasobów w ramach subskrypcji platformy Azure. Funkcje zarządzania, takie jak kontrola dostępu, blokady i tagi, mogą być używane do zabezpieczania i organizowania zasobów po wdrożeniu.

Aby dowiedzieć się więcej na temat szablonów arm, [zobacz Template deployment omówienie](../azure-resource-manager/templates/overview.md).

Aby wdrożyć maszynę DCsv2-Series wirtualną w szablonie usługi ARM, użyjemy zasobu [maszyny wirtualnej](../virtual-machines/windows/template-description.md). Upewnij się, że określono poprawne właściwości **dla właściwości vmSize** i **imageReference.**

### <a name="vm-size"></a>Rozmiar maszyny wirtualnej

Określ jeden z następujących rozmiarów w szablonie usługi ARM w zasobie maszyny wirtualnej. Ten ciąg jest umieszczany jako **vmSize** we **właściwościach**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Obraz systemu operacyjnego Gen2

W **obszarze** właściwości należy również odwołać się do obrazu w obszarze **storageProfile**. Użyj *tylko jednego* z następujących obrazów dla **imageReference**.

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
      "20_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "20_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Następne kroki 

Ten artykuł zawiera informacje o kwalifikacjach i konfiguracjach wymaganych podczas tworzenia maszyny wirtualnej poufnego przetwarzania. Teraz możesz przejść do Microsoft Azure Marketplace, aby wdrożyć maszynę wirtualną DCsv2-Series wirtualnej.

> [!div class="nextstepaction"]
> [Wdrażanie maszyny DCsv2-Series wirtualnej w Azure Marketplace](quick-create-marketplace.md)