---
title: Tworzenie sieci VLAN/podsieci — rozwiązanie Azure VMware według CloudSimple
description: Azure VMware Solutions według CloudSimple — opisuje sposób tworzenia sieci VLAN i podsieci dla chmur prywatnych oraz zarządzania nimi, a następnie stosowania reguł zapory.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06bebcb7369f6604fc79c1d3d0a4a6afa8b0a1da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97896315"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Tworzenie sieci VLAN i podsieci dla chmur prywatnych oraz zarządzanie nimi

Otwórz kartę sieci VLAN/podsieci na stronie sieć, aby utworzyć sieci VLAN/podsieci dla chmur prywatnych i zarządzać nimi. Po utworzeniu sieci VLAN/podsieci można zastosować reguły zapory.

## <a name="create-a-vlansubnet"></a>Tworzenie sieci VLAN/podsieci

1. [Uzyskaj dostęp do portalu CloudSimple](access-cloudsimple-portal.md) i wybierz pozycję **Sieć** w menu po stronie.
2. Wybierz pozycję **VLANs/Subnets**.
3. Kliknij pozycję **Utwórz sieć VLAN/podsieć**.

    ![Strona sieci VLAN/podsieć](media/vlan-subnet-page.png)

4. Wybierz chmurę prywatną dla nowej podsieci/sieci VLAN.
5. Wprowadź identyfikator sieci VLAN.
6. Wprowadź nazwę podsieci.
7. Aby włączyć routing w sieci VLAN (podsieć), określ zakres CIDR podsieci. Upewnij się, że zakres CIDR nie nakłada się na żadną z podsieci lokalnych, podsieci platformy Azure ani podsieci bramy.
8. Kliknij przycisk **Prześlij**.

    ![Utwórz sieć VLAN/podsieć](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Istnieje limit przydziału 30 sieci VLAN na chmurę prywatną. Limity te można zwiększyć, [kontaktując się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Korzystanie z informacji o sieci VLAN do konfigurowania rozproszonej grupy portów w vSphere

Aby utworzyć rozproszoną grupę portów w vSphere, postępuj zgodnie z instrukcjami w temacie VMware "Dodawanie rozproszonej grupy portów" w <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">przewodniku po sieci vSphere</a>. Podczas konfigurowania rozproszonej grupy portów podaj informacje o sieci VLAN z konfiguracji CloudSimple.

![Grupa portów rozproszonych](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Wybierz tabelę zapory

Tabele zapory i skojarzone reguły są zdefiniowane na stronie **tabel zapory > sieci** . Aby wybrać tabelę zapory, która ma zostać zastosowana do sieci VLAN/podsieci dla chmury prywatnej, wybierz pozycję Sieć VLAN/podsieć kliknij **Załącznik tabela zapory** na stronie **sieci VLAN/podsieci** . Aby uzyskać instrukcje dotyczące konfigurowania tabel zapory i definiowania reguł, zobacz [tabele zapory](firewall.md) .

![Link do tabeli zapory](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Podsieć może być skojarzona z jedną tabelą zapory. Tabela zapory może być skojarzona z wieloma podsieciami.

## <a name="edit-a-vlansubnet"></a>Edytowanie sieci VLAN/podsieci

Aby edytować ustawienia sieci VLAN/podsieci, zaznacz ją na stronie **sieci VLAN/podsieci** i kliknij ikonę **Edytuj** . Wprowadź zmiany i kliknij przycisk **Submet**.

## <a name="delete-a-vlansubnet"></a>Usuwanie sieci VLAN/podsieci

Aby usunąć sieci VLAN/podsieć, wybierz ją na stronie **sieci VLAN/podsieci** i kliknij ikonę **Usuń** . Kliknij przycisk **Usuń** , aby potwierdzić.
