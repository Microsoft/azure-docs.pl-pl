---
title: Stany i rozliczanie Virtual Machines platformy Azure
description: Przegląd różnych stanów, które mogą wejść na maszynę wirtualną i po rozliczeniu użytkownika.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: c206cba3b23a0bf41ce32481980aa466e869bcde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596301"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Stany i rozliczanie Virtual Machines platformy Azure

Usługa Azure Virtual Machines (VM) umożliwia przechodzenie między różnymi stanami, które można podzielić na możliwości *aprowizacji* *i Stany* . Celem tego artykułu jest opisywanie tych stanów i wyraźne wyróżnienie, gdy klienci są rozliczani za użycie wystąpień. 

## <a name="get-states-using-instance-view"></a>Pobierz Stany przy użyciu widoku wystąpienia

Interfejs API widoku wystąpienia zawiera informacje o stanie uruchomienia maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz dokumentację interfejsu API [widoku Virtual Machines-instance](/rest/api/compute/virtualmachines/instanceview) .

Eksplorator zasobów platformy Azure udostępnia prosty interfejs użytkownika do wyświetlania stanu działania maszyny wirtualnej: [Eksplorator zasobów](https://resources.azure.com/).

Stany aprowizacji są widoczne w obszarze właściwości maszyny wirtualnej i widok wystąpienia. Stany mocy są dostępne w widoku wystąpienia maszyny wirtualnej.

Aby pobrać stan mocy wszystkich maszyn wirtualnych w ramach subskrypcji, użyj opcji [Virtual Machines wszystkie interfejsy API](/rest/api/compute/virtualmachines/listall) z parametrem **statusOnly** na *wartość true*.

> [!NOTE]
> [Virtual Machines wszystkie interfejsy API](/rest/api/compute/virtualmachines/listall) z parametrem **statusOnly** ustawionym na wartość true spowodują pobranie Stanów mocy wszystkich maszyn wirtualnych w ramach subskrypcji. Jednak w niektórych rzadkich sytuacjach stan mocy może nie być dostępny z powodu sporadycznych problemów w procesie pobierania. W takich sytuacjach zalecamy ponawianie próby przy użyciu tego samego interfejsu API lub [Azure Resource Health](../service-health/resource-health-overview.md) lub [Graf zasobów platformy Azure](..//governance/resource-graph/overview.md) w celu sprawdzenia stanu mocy maszyn wirtualnych.
 
## <a name="power-states-and-billing"></a>Stany mocy i rozliczenia

Stan zasilacza reprezentuje ostatni znany stan maszyny wirtualnej.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="Obraz przedstawia Diagram stanu zasilacza, przez który maszyna wirtualna może przejść. ":::

Poniższa tabela zawiera opis każdego stanu wystąpienia i wskazuje, czy jest rozliczana za użycie wystąpienia, czy nie.

| Stan zasilacza | Opis | Rozliczenia |  
|---|---|---|
| Uruchamianie| Maszyna wirtualna jest w mocy. |Nienaliczany * | 
| Uruchomienie | Maszyna wirtualna jest w pełni skonfigurowana. Jest to standardowy stan roboczy. | Rozliczane | 
| Zatrzymywanie | Jest to stan przejściowy między uruchomionym i zatrzymanym. | Rozliczane| 
|Zatrzymano | Maszyna wirtualna została zamknięta z poziomu systemu operacyjnego gościa lub przy użyciu interfejsów API wyłączenie. W tym stanie maszyna wirtualna nadal będzie dzierżawić podstawowy sprzęt. Ten stan jest również określany jako *zatrzymany (przydzielono)*. | Rozliczane | 
| Cofanie przydziału | Jest to stan przejściowy między uruchomioną i cofniętą alokacją. | Nienaliczany * | 
| Cofnięto przydział | Maszyna wirtualna wyłączyła dzierżawę na bazowym sprzęcie i jest całkowicie wyłączona. Ten stan jest również określany jako *zatrzymany (cofnięto przydział)*. | Nienaliczany * | 

&#42; niektórych zasobów platformy Azure, takich jak [dyski](https://azure.microsoft.com/pricing/details/managed-disks) i [sieci](https://azure.microsoft.com/pricing/details/bandwidth/) , nadal będą naliczane opłaty.


## <a name="provisioning-states"></a>Stany aprowizacji

Stan aprowizacji to stan zainicjowanej przez użytkownika operacji kontroli płaszczyzny na maszynie wirtualnej. Te Stany są oddzielone od stanu zasilacza maszyny wirtualnej.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="Obraz przedstawia Stany aprowizacji, przez które maszyna wirtualna może przejść.":::

| Stan aprowizacji | Opis | Stan zasilacza | Rozliczenia | 
|---|---|---|---|
| Utwórz | Tworzenie maszyny wirtualnej. | Uruchamianie | Nienaliczany * | 
| Aktualizacja | Aktualizuje model istniejącej maszyny wirtualnej. Niektóre zmiany niezwiązane z modelem na maszynie wirtualnej, takie jak Start i restart, są objęte stanem aktualizacji. | Uruchomienie | Rozliczane | 
| Usuń | Usuwanie maszyny wirtualnej. | Cofanie przydziału | Nienaliczany * |
| cofanie przydziału | Maszyna wirtualna jest w pełni zatrzymana i usunięta z podstawowego hosta. Cofanie przydziału maszyny wirtualnej jest uznawane za aktualizację i będzie wyświetlać Stany aprowizacji podobne do aktualizacji. | Cofanie przydziału | Nienaliczany * | 

&#42; niektórych zasobów platformy Azure, takich jak [dyski](https://azure.microsoft.com/pricing/details/managed-disks) i [sieci](https://azure.microsoft.com/pricing/details/bandwidth/) , nadal będą naliczane opłaty.

## <a name="os-provisioning-states"></a>Stany aprowizacji systemu operacyjnego
Stany aprowizacji systemu operacyjnego mają zastosowanie tylko do maszyn wirtualnych utworzonych przy użyciu obrazu systemu operacyjnego. W przypadku obrazów specjalnych nie będą wyświetlane te Stany. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="Obraz przedstawia stan aprowizacji systemu operacyjnego, przez który maszyna wirtualna może przejść.":::

| Stan aprowizacji systemu operacyjnego | Opis | Stan zasilacza | Rozliczenia | 
|---|---|---|---|
| OSProvisioningInProgress | Maszyna wirtualna jest uruchomiona, a instalacja systemu operacyjnego gościa jest w toku. | Uruchomienie | Rozliczane | 
| OSProvisioningComplete | Jest to stan krótkotrwały. Maszyna wirtualna szybko przechodzi z tego stanu do **sukcesu**. Jeśli rozszerzenia są nadal instalowane, nadal będzie widoczny ten stan, dopóki nie zostaną ukończone. | Uruchomienie | Rozliczane | 
| Powodzenie | Akcje zainicjowane przez użytkownika zostały ukończone. | Uruchomienie | Rozliczane | 
| Niepowodzenie | Reprezentuje operację, która nie powiodła się. Zapoznaj się z kodem błędu, aby uzyskać więcej informacji i możliwych rozwiązań. | Uruchomienie  | Rozliczane | 


## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z [dokumentacją dotyczącą Azure Cost Management i rozliczeń](../cost-management-billing/index.yml)
- Zaplanuj wdrożenia za pomocą [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) .
- Dowiedz się więcej na temat monitorowania maszyny wirtualnej, zobacz [monitorowanie maszyn wirtualnych na platformie Azure](../azure-monitor/vm/monitor-vm-azure.md).