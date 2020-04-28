---
title: Łączenie się z maszynami wirtualnymi za pomocą przeglądarki Azure | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z maszynami wirtualnymi za pomocą przeglądarki.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74974301"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Łączenie się z maszynami wirtualnymi za pomocą przeglądarki 

DevTest Labs integrują się z [usługą Azure bastionu](https://docs.microsoft.com/azure/bastion/), która umożliwia łączenie się z maszynami wirtualnymi za pomocą przeglądarki. Aby uzyskać informacje na temat włączania tej funkcji w usłudze DevTest Labs, zobacz [Włączanie połączenia przeglądarki na maszynach wirtualnych laboratorium](enable-browser-connection-lab-virtual-machines.md).

Po włączeniu *połączenia przeglądarki* użytkownicy laboratorium mogą uzyskać dostęp do maszyn wirtualnych za pomocą przeglądarki.  

## <a name="create-a-lab-virtual-machine"></a>Tworzenie maszyny wirtualnej laboratorium

Najpierw musisz utworzyć maszynę wirtualną laboratorium w sieci wirtualnej, dla której skonfigurowano bastionu. Wybierz drugą **podsieć** , która została utworzona, a nie AzureBastionSubnet. Możesz wybrać sieć wirtualną podczas tworzenia maszyny wirtualnej, przechodząc do karty **Ustawienia zaawansowane** .

![Tworzenie maszyny wirtualnej](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Uruchom maszynę wirtualną w przeglądarce

Po utworzeniu maszyny wirtualnej można uruchomić ją w przeglądarce, klikając przycisk *przeglądarki Connect* i wprowadzając nazwę użytkownika i hasło dla komputera.  

![Uruchom w przeglądarce](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Następne kroki

[Dodawanie maszyny wirtualnej do laboratorium w Azure DevTest Labs](devtest-lab-add-vm.md)
