---
title: Azure VMware Solution by CloudSimple
description: Dowiedz się więcej na temat rozwiązań Azure VMware Solutions by CloudSimple, zapoznając się z omówieniem, przewodnikami Szybki start, koncepcjami, samouczkami i przewodnikami z instrukcjami.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.custom: seo-azure-migrate
keywords: vms support, azure vmware solution by cloudsimple, cloudsimple azure, vms tools, vmware documentation
ms.openlocfilehash: a3514a011f940b82702984737d0a93c5da402b55
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108385"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple

Witamy w portalu, w którym scentralizowano zasoby pomocy dotyczące rozwiązania Azure VMware Solution by CloudSimple.
W tej witrynie dokumentacji można zapoznać się z następującymi tematami:

## <a name="overview"></a>Omówienie

Więcej informacji o rozwiązaniu VMware Solution by CloudSimple

* Informacje na temat funkcji, korzyści i scenariuszy użycia: [Co to jest rozwiązanie Azure VMware Solution by CloudSimple](cloudsimple-vmware-solutions-overview.md)
* Zapoznaj się z [kluczowymi pojęciami dotyczącymi administracji](key-concepts.md)

## <a name="quickstart"></a>Szybki start

Dowiedz się, jak zacząć korzystać z rozwiązania

* Opis [inicjowania usługi i pojemności zakupu](quickstart-create-cloudsimple-service.md)
* Dowiedz się, jak utworzyć nowe środowisko VMware: [Konfigurowanie środowiska chmury prywatnej](quickstart-create-private-cloud.md)
* Dowiedz się, jak ujednolicić zarządzanie w środowisku VMware i Azure — artykuł [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)

## <a name="concepts"></a>Pojęcia

Poznaj następujące pojęcia

* [Usługa CloudSimple](cloudsimple-service.md) (określana również jako „Azure VMware Solution by CloudSimple — usługa”). Ten zasób należy utworzyć raz w każdym regionie.
* Kupowanie pojemności dla środowiska przez utworzenie jednego lub większej liczby zasobów [węzła CloudSimple](cloudsimple-node.md). Te zasoby są również określane jako „Azure VMware Solution by CloudSimple — węzeł”.
* Inicjowanie i konfigurowanie środowiska VMware przy użyciu [chmur prywatnych](cloudsimple-private-cloud.md).
* Ujednolicanie zarządzanie przy użyciu[maszyn wirtualnych CloudSimple](cloudsimple-virtual-machines.md) (określanych również jako „Azure VMware Solution by CloudSimple — maszyna wirtualna”).
* Projektowanie sieci bazowej [przy użyciu podsieci/VLAN](cloudsimple-vlans-subnets.md).
* Segmentowanie i zabezpieczanie sieci bazowej za pomocą zasobu [Tabela zapory](cloudsimple-firewall-tables.md).
* Uzyskiwanie bezpiecznego dostępu do środowisk VMware za pośrednictwem sieci WAN przy użyciu[bram VPN Gateway](cloudsimple-vpn-gateways.md).
* Zapewnianie publicznego dostępu do obciążeń przy użyciu [publicznego adresu IP](cloudsimple-public-ip-address.md).
* Ustanawianie łączności z sieciami wirtualnymi platformy Azure i sieciami lokalnymi przy użyciu [połączenia sieciowego platformy Azure](cloudsimple-azure-network-connection.md).
* Konfigurowanie elementów docelowych poczty e-mail dla alertów za pomocą obszaru [Zarządzanie kontami](cloudsimple-account.md).
* Wyświetlanie dzienników aktywności użytkowników i systemu przy użyciu ekranów [Zarządzanie działaniami](cloudsimple-activity.md).
* Przegląd różnych [składników oprogramowania VMware](vmware-components.md).

## <a name="tutorials"></a>Samouczki

Dowiedz się, jak wykonywać typowe zadania, takie jak:

* [Tworzenie usługi CloudSimple](create-cloudsimple-service.md), raz w każdym regionie, w którym mają zostać wdrożone środowiska VMware.
* Zarządzanie podstawową funkcjonalnością usługi w [portalu CloudSimple](access-cloudsimple-portal.md).
* Zapewnianie pojemności i optymalizowanie rozliczeń dla danej infrastruktury przez [zakup węzłów CloudSimple](create-nodes.md).
* Zarządzanie konfiguracjami środowiska VMware przy użyciu chmur prywatnych. Chmury prywatne można [tworzyć](create-private-cloud.md), [rozszerzać](expand-private-cloud.md) i [zmniejszać](shrink-private-cloud.md) oraz [zarządzać](manage-private-cloud.md) nimi.
* Włączanie ujednoliconego zarządzania przez [zamapowanie subskrypcji platformy Azure](azure-subscription-mapping.md).
* Monitorowanie aktywności użytkowników i systemu przy użyciu ekranów [stron działań](monitor-activity.md).
* Konfigurowanie sieci dla danych środowisk przez [utworzenie podsieci i zarządzanie nimi](create-vlan-subnet.md).
* Segmentowanie i zabezpieczanie środowiska [przy użyciu reguł i tabel zapory](firewall.md).
* Zapewnianie dostępu przychodzącego przez Internet dla obciążeń przez [przydzielenie publicznych adresów IP](public-ips.md).
* Zapewnienie łączności z sieci wewnętrznych lub klienckich stacji roboczych przez [skonfigurowanie sieci VPN](vpn-gateway.md).
* Zapewnianie komunikacji ze [środowisk lokalnych](on-premises-connection.md), a także z [sieciami wirtualnymi platformy Azure](virtual-network-connection.md).
* Konfigurowanie elementów docelowych alertów i wyświetlanie całej zakupionej pojemności w [podsumowaniu konta](account.md).
* Wyświetlanie [użytkowników](users.md), którzy uzyskali dostęp do portalu CloudSimple.
* Zarządzanie maszynami wirtualnymi oprogramowania VMware w witrynie Azure Portal:
    * [Tworzenie maszyn wirtualnych](azure-create-vm.md) w witrynie Azure Portal.
    * [Zarządzanie utworzonymi maszynami wirtualnymi](azure-manage-vm.md).

## <a name="how-to-guides"></a>Przewodniki z instrukcjami

W tych przewodnikach opisano rozwiązania realizujące następujące cele:

* [Zabezpieczanie środowiska](private-cloud-secure.md)
* Instalowanie narzędzi innych producentów, włączanie dodatkowych użytkowników i zewnętrznego źródła uwierzytelniania w programie vSphere przy użyciu [eskalacji uprawnień](escalate-privileges.md).
* Konfigurowanie dostępu do różnych usług VMware przez [skonfigurowanie lokalnego systemu DNS](on-premises-dns-setup.md).
* Włączanie przydzielania nazw i adresów dla obciążeń przez [skonfigurowanie dla tych obciążeń systemów DNS i DHCP](dns-dhcp-setup.md).
* Omówienie sposobu, w jaki usługa zapewnia bezpieczeństwo i funkcjonalność na platformie klienta za pomocą [aktualizacji i uaktualnień](vmware-components.md#updates-and-upgrades) usługi.
* Zmniejszanie całkowitego kosztu posiadania związanego z kopiami zapasowymi dzięki utworzeniu przykładowej architektury tworzenia kopii zapasowych za pomocą [oprogramowania do tworzenia kopii zapasowych innego producenta, takiego jak Veeam](backup-workloads-veeam.md).
* Tworzenie bezpiecznego środowiska przez włączenie szyfrowania danych w spoczynku przy użyciu [oprogramowania do szyfrowania KMS innego producenta](vsan-encryption.md).
* Rozszerzanie zarządzania za pomocą usługi Azure Active Directory (Azure AD) na środowisko VMware przez skonfigurowanie [źródła tożsamości usługi Azure AD](azure-ad.md).
