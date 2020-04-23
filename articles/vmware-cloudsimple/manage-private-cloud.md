---
title: Zarządzanie rozwiązaniem Azure VMware przez CloudSimple private cloud
description: W tym artykule opisano możliwości zarządzania zasobami i aktywnością cloudsimple private cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 13496a18f4c99b69a5b8095caf5b74a04d1bba88
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869299"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Zarządzanie zasobami i aktywnością w chmurze prywatnej

Chmury prywatne są zarządzane z portalu CloudSimple.  Sprawdź stan, dostępne zasoby, aktywność w chmurze prywatnej i inne ustawienia z portalu CloudSimple.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

Uzyskaj dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Wyświetlanie listy chmur prywatnych

Karta **Chmury prywatne** na stronie **Zasoby** zawiera listę wszystkich chmur prywatnych w ramach subskrypcji. Informacje obejmują nazwę, liczbę klastrów vSphere, lokalizację, bieżący stan chmury prywatnej i informacje o zasobach.

![Strona chmury prywatnej](media/manage-private-cloud.png)

Wybierz chmurę prywatną, aby uzyskać dodatkowe informacje i działania.

## <a name="private-cloud-summary"></a>Podsumowanie chmury prywatnej

Wyświetl obszerne podsumowanie wybranej chmury prywatnej.  Strona Podsumowanie zawiera serwery DNS wdrożone w chmurze prywatnej.  Przekazywanie dns można skonfigurować z lokalnych serwerów DNS na serwery DNS w chmurze prywatnej.  Aby uzyskać więcej informacji na temat przekazywania usług DNS, zobacz [Konfigurowanie rozpoznawania nazw systemu DNS dla centrum vCenter w chmurze prywatnej z lokalnego](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-dns-setup/).

![Podsumowanie chmury prywatnej](media/private-cloud-summary.png)

### <a name="available-actions"></a>Dostępne akcje

* [Uruchom klienta vSphere](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access). Dostęp do centrum wirtualnego dla tej chmury prywatnej.
* [Węzły zakupu](create-nodes.md). Dodaj węzły do tej chmury prywatnej.
* [Rozwiń](expand-private-cloud.md). Dodaj węzły do tej chmury prywatnej.
* **Odśwież**. Zaktualizuj informacje na tej stronie.
* **Usuń**plik . Chmurę prywatną można usunąć w dowolnym momencie. **Przed usunięciem upewnij się, że wykonasz kopię zapasową wszystkich systemów i danych.** Usunięcie chmury prywatnej powoduje usunięcie wszystkich maszyn wirtualnych, konfiguracji vCenter i danych. Kliknij **pozycję Usuń** w sekcji podsumowania dla wybranej chmury prywatnej. Po usunięciu wszystkie dane private cloud są usuwane w bezpiecznym, wysoce zgodnym procesie usuwania.
* [Zmień uprawnienia vSphere](escalate-private-cloud-privileges.md).  Eskaluj swoje uprawnienia w tej chmurze prywatnej.

## <a name="private-cloud-vlanssubnets"></a>Sieć VLANS/podsieci w chmurze prywatnej

Wyświetl listę zdefiniowanych sieci/podsieci VLAN dla wybranej chmury prywatnej.  Lista zawiera sieci VLAN/podsieci zarządzania utworzone podczas tworzenia chmury prywatnej.

![Chmura prywatna — sieci/podsieci](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Dostępne akcje

* [Dodaj sieć VLANS/Podsieci](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/). Dodaj sieć VLAN/podzbiór do tej chmury prywatnej.

Wybierz sieć VLAN/Podsieć dla następujących akcji
* [Dołącz tabelę zapory](https://docs.microsoft.com/azure/vmware-cloudsimple/firewall/). Dołącz tabelę zapory do tej chmury prywatnej.
* **Edytuj**
* **Usuń** (tylko sieci/podsieci zdefiniowane przez użytkownika)

## <a name="private-cloud-activity"></a>Aktywność w chmurze prywatnej

Wyświetl następujące informacje dotyczące wybranej chmury prywatnej.  Informacje o aktywności to filtrowana lista wszystkich działań dla wybranej chmury prywatnej.  Na tej stronie przedstawiono maksymalnie 25 ostatnich działań.

* Najnowsze alerty
* Ostatnie wydarzenia
* Ostatnie zadania
* Ostatnia kontrola

![Chmura prywatna — aktywność](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Stojaki w chmurze

Stojaki w chmurze są budulcem chmury prywatnej. Każdy stojak zapewnia jednostkę pojemności. CloudSimple automatycznie konfiguruje stojaki w chmurze na podstawie wybranych opcji podczas tworzenia lub rozszerzania chmury prywatnej.  Wyświetl pełną listę stojaków w chmurze, w tym chmurę prywatną, do których każdy jest przypisany.

![Chmura prywatna - Stojaki w chmurze](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Sieć zarządzania programu vSphere

Lista zasobów zarządzania VMware i maszyn wirtualnych, które są obecnie skonfigurowane w chmurze prywatnej. Informacje obejmują wersję oprogramowania, w pełni kwalifikowaną nazwę domeny (FQDN) i adres IP zasobów.

![Chmura prywatna - sieć zarządzania vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Następne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [chmurach prywatnych](cloudsimple-private-cloud.md)