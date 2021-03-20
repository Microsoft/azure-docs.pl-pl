---
title: Akcje zabronione w przypadku dostępu z podwyższonym poziomem uprawnień
description: Aparat VMware przywraca zmiany, aby upewnić się, że usługa pozostanie nieprzerwana, gdy aparat VMware wykryje dowolne z następujących niedozwolonych akcji.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92915436"
---
# <a name="forbidden-actions-during-elevated-access"></a>Akcje zabronione w przypadku dostępu z podwyższonym poziomem uprawnień

W trakcie przedziału czasu podniesienia uprawnień niektóre akcje są zabronione. Gdy aparat VMware wykryje dowolne z następujących niedozwolonych działań, aparat VMware przywraca zmiany, aby upewnić się, że usługa pozostanie nieprzerwana.

## <a name="cluster-actions"></a>Akcje klastra

- Usuwanie klastra z programu vCenter.
- Zmiana wysokiej dostępności vSphere w klastrze.
- Dodawanie hosta do klastra z programu vCenter.
- Usuwanie hosta z klastra z programu vCenter.

## <a name="host-actions"></a>Akcje hosta

- Usuwanie magazynów danych na hoście ESXi.
- Odinstalowywanie programu vCenter Agent od hosta.
- Modyfikowanie konfiguracji hosta.
- Wprowadzanie zmian w profilach hosta.
- Umieszczanie hosta w trybie konserwacji.

## <a name="network-actions"></a>Akcje sieci

- Usuwanie domyślnego przełącznika wirtualnego rozproszonego (DVS) w chmurze prywatnej.
- Usuwanie hosta z domyślnej usługi DVS.
- Importowanie ustawień konfiguracji DVS.
- Ponowne konfigurowanie ustawień konfiguracji DVS.
- Uaktualnianie wszystkich usługa DVS.
- Usuwanie obiektu Port Management.
- Edytowanie zarządzania portem.

## <a name="roles-and-permissions-actions"></a>Akcje ról i uprawnień

- Tworzenie roli globalnej.
- Modyfikowanie lub usuwanie uprawnień do wszystkich obiektów zarządzania.
- Modyfikowanie lub usuwanie wszystkich ról domyślnych.
- Zwiększ uprawnienia roli do wyższego poziomu niż rola właściciela chmury.

## <a name="other-actions"></a>Inne akcje

- Usuwanie wszystkich licencji domyślnych:
  - Program vCenter Server
  - ESXi węzły
  - NSX-T
  - HCX
- Modyfikowanie lub usuwanie puli zasobów zarządzania.
- Klonowanie maszyn wirtualnych zarządzania.


## <a name="next-steps"></a>Następne kroki
[CloudSimple konserwacja i aktualizacje](cloudsimple-maintenance-updates.md) 
