---
title: Zarządzanie rozwiązaniem VMware platformy Azure za pomocą chmury prywatnej CloudSimple
description: Opisuje możliwości dostępne do zarządzania zasobami i aktywności w chmurze prywatnej CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f2f66c2e1e2e8aa596393d4c69a757138ab5a91
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895210"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Zarządzanie zasobami i aktywnością chmury prywatnej

Chmurami prywatnymi zarządza się z poziomu portalu CloudSimple.  Sprawdź stan, dostępne zasoby, działanie w chmurze prywatnej oraz inne ustawienia w portalu CloudSimple.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

Dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Wyświetl listę chmur prywatnych

Na karcie **chmury prywatne** na stronie **zasoby** są wyświetlane wszystkie chmury prywatne w ramach subskrypcji. Informacje obejmują nazwę, liczbę klastrów vSphere, lokalizację, bieżący stan chmury prywatnej i informacje o zasobach.

![Strona chmury prywatnej](media/manage-private-cloud.png)

Wybierz chmurę prywatną, aby uzyskać dodatkowe informacje i akcje.

## <a name="private-cloud-summary"></a>Podsumowanie chmury prywatnej

Wyświetl kompleksowe podsumowanie wybranej chmury prywatnej.  Strona podsumowania zawiera serwery DNS wdrożone w chmurze prywatnej.  Można skonfigurować przekazywanie DNS z lokalnych serwerów DNS do serwerów DNS w chmurze prywatnej.  Aby uzyskać więcej informacji na temat przekazywania DNS, zobacz [Konfigurowanie systemu DNS do rozpoznawania nazw w przypadku chmury prywatnej dla programu vCenter](./on-premises-dns-setup.md)w środowisku lokalnym.

![Podsumowanie chmury prywatnej](media/private-cloud-summary.png)

### <a name="available-actions"></a>Dostępne akcje

* [Uruchom klienta vSphere](./vcenter-access.md). Uzyskaj dostęp do programu vCenter dla tej chmury prywatnej.
* [Kup węzły](create-nodes.md). Dodaj węzły do tej chmury prywatnej.
* [Rozwiń węzeł](expand-private-cloud.md). Dodaj węzły do tej chmury prywatnej.
* **Odśwież**. Zaktualizuj informacje na tej stronie.
* **Delete**. Chmurę prywatną można usunąć w dowolnym momencie. **Przed usunięciem upewnij się, że utworzono kopię zapasową wszystkich systemów i danych.** Usunięcie chmury prywatnej spowoduje usunięcie wszystkich maszyn wirtualnych, konfiguracji programu vCenter i danych. Kliknij pozycję **Usuń** w sekcji Podsumowanie dla wybranej chmury prywatnej. Po usunięciu wszystkie dane w chmurze prywatnej są usuwane w bezpiecznym, wysoce zgodnym procesie wymazywania.
* [Zmień uprawnienia vSphere](escalate-private-cloud-privileges.md).  Eskalacja uprawnień do tej chmury prywatnej.

## <a name="private-cloud-vlanssubnets"></a>Sieci VLAN/podsieci w chmurze prywatnej

Wyświetl listę zdefiniowanych sieci VLAN/podsieci dla wybranej chmury prywatnej.  Lista zawiera sieci VLAN i podsieci zarządzania utworzone podczas tworzenia chmury prywatnej.

![Chmura prywatna — sieci VLAN/podsieci](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Dostępne akcje

* [Dodawanie sieci VLAN/podsieci](./create-vlan-subnet.md). Dodaj sieć VLAN/podzestaw do tej chmury prywatnej.

Wybierz sieć VLAN/podsieć dla następujących akcji
* [Dołącz tabelę zapory](./firewall.md). Dołącz tabelę zapory do tej chmury prywatnej.
* **Edytuj**
* **Usuń** (tylko zdefiniowane przez użytkownika sieci VLAN/podsieci)

## <a name="private-cloud-activity"></a>Aktywność chmury prywatnej

Wyświetl następujące informacje dotyczące wybranej chmury prywatnej.  Informacje o działaniu to filtrowana lista wszystkich działań dla wybranej chmury prywatnej.  Ta strona zawiera maksymalnie 25 ostatnich działań.

* Ostatnie alerty
* Ostatnie zdarzenia
* Ostatnie zadania
* Ostatnia Inspekcja

![Chmura prywatna — aktywność](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Stojaki w chmurze

Stojaki w chmurze są blokami konstrukcyjnymi chmury prywatnej. Każdy stojak zapewnia jednostkę pojemności. CloudSimple automatycznie konfiguruje Stojaki w chmurze na podstawie wybranych opcji podczas tworzenia lub rozszerzania chmury prywatnej.  Zapoznaj się z pełną listą stojaków w chmurze, w tym chmurę prywatną, do której każdy jest przypisany.

![Chmura prywatna — Stojaki w chmurze](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Sieć zarządzania programu vSphere

Lista zasobów zarządzania VMware i maszyn wirtualnych aktualnie skonfigurowanych w chmurze prywatnej. Informacje obejmują wersję oprogramowania, w pełni kwalifikowaną nazwę domeny (FQDN) i adres IP zasobów.

![Chmura prywatna — sieć zarządzania vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Następne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [chmurach prywatnych](cloudsimple-private-cloud.md)
