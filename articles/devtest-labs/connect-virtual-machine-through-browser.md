---
title: Łączenie się z maszynami wirtualnymi za pomocą przeglądarki Azure | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z maszynami wirtualnymi za pomocą przeglądarki.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 352fc5fd3ff53a00d9f62966ecf21417ad898706
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288056"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Łączenie się z maszynami wirtualnymi za pomocą przeglądarki 

DevTest Labs integrują się z [usługą Azure bastionu](../bastion/index.yml), która umożliwia łączenie się z maszynami wirtualnymi za pomocą przeglądarki. Aby uzyskać informacje na temat włączania tej funkcji w usłudze DevTest Labs, zobacz [Włączanie połączenia przeglądarki na maszynach wirtualnych laboratorium](enable-browser-connection-lab-virtual-machines.md).

Po włączeniu *połączenia przeglądarki* użytkownicy laboratorium mogą uzyskać dostęp do maszyn wirtualnych za pomocą przeglądarki.  

## <a name="create-a-lab-virtual-machine"></a>Tworzenie maszyny wirtualnej laboratorium

Najpierw musisz utworzyć maszynę wirtualną laboratorium w sieci wirtualnej, dla której skonfigurowano bastionu. Wybierz drugą **podsieć** , która została utworzona, a nie AzureBastionSubnet. Możesz wybrać sieć wirtualną podczas tworzenia maszyny wirtualnej, przechodząc do karty **Ustawienia zaawansowane** .

![Tworzenie maszyny wirtualnej](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Uruchom maszynę wirtualną w przeglądarce

Po utworzeniu maszyny wirtualnej można uruchomić ją w przeglądarce, klikając przycisk *przeglądarki Connect* i wprowadzając nazwę użytkownika i hasło dla komputera.  

![Uruchom w przeglądarce](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Następne kroki

[Dodawanie maszyny wirtualnej do laboratorium w Azure DevTest Labs](devtest-lab-add-vm.md)
