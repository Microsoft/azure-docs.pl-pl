---
title: Przykład Przewodnik dotyczący infrastruktury platformy Azure
description: Poznaj wytyczne dotyczące projektowania i wdrażania przykładowej infrastruktury na platformie Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: example-scenario
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 43e96b891e60dfcf8bc3c29b202bb60213905372
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81869470"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Przykład Przewodnik dotyczący infrastruktury platformy Azure dla maszyn wirtualnych z systemem Windows
W tym artykule omówiono Tworzenie przykładowej infrastruktury aplikacji. Szczegółowo projektujemy infrastrukturę dla prostego sklepu online, który łączy wszystkie wytyczne i decyzje dotyczące konwencji nazewnictwa, zestawów dostępności, sieci wirtualnych i modułów równoważenia obciążenia, a także faktycznie wdrażają maszyny wirtualne.

## <a name="example-workload"></a>Przykładowe obciążenie
Cykle Adventure Works chcą utworzyć aplikację ze sklepu online na platformie Azure, która składa się z:

* Dwa serwery IIS z frontonem klienta w warstwie sieci Web
* Dwa serwery IIS przetwarzające dane i zamówienia w warstwie aplikacji
* Dwa Microsoft SQL Server wystąpienia z grupami dostępności AlwaysOn (dwa serwery SQL i monitor z większością węzłów) do przechowywania danych i zamówień produktów w warstwie bazy danych
* Dwa Active Directory kontrolery domeny dla kont klientów i dostawców w warstwie uwierzytelniania
* Wszystkie serwery znajdują się w dwóch podsieciach:
  * podsieć frontonu dla serwerów sieci Web 
  * podsieć zaplecza dla serwerów aplikacji, klastra SQL i kontrolerów domeny

![Diagram różnych warstw dla infrastruktury aplikacji](./media/infrastructure-example/example-tiers.png)

Przychodzący bezpieczny ruch internetowy musi być zrównoważony obciążenie między serwerami sieci Web, gdy klienci przeglądają sklep online. Ruch przetwarzania zamówień w formie żądań HTTP z serwerów sieci Web musi być zrównoważony między serwerami aplikacji. Dodatkowo infrastruktura musi być zaprojektowana w celu zapewnienia wysokiej dostępności.

Projekt wynikający z tego musi zawierać:

* Subskrypcja i konto platformy Azure
* Jedna grupa zasobów
* Dyski zarządzane platformy Azure
* Sieć wirtualna z dwiema podsieciami
* Zestawy dostępności dla maszyn wirtualnych z podobną rolą
* Maszyny wirtualne

Wszystkie powyższe zasady nazewnictwa są następujące:

* Cykle Adventure Works używają **[obciążenia IT]-[lokalizacja]-[zasób platformy Azure]** jako prefiks
  * W tym przykładzie "**Azos**" (magazyn online Azure) jest nazwą obciążenia IT i "**use**" (Wschodnie stany USA 2) jest lokalizacją
* Sieci wirtualne używają AZOS.-VN **[Number]**
* Zestawy dostępności używają Azos-use-AS-**[role]**
* Nazwy maszyn wirtualnych używają Azos-use-VM-**[VMName]**

## <a name="azure-subscriptions-and-accounts"></a>Subskrypcje i konta platformy Azure
Cykle Adventure Works korzystają z subskrypcji przedsiębiorstwa o nazwie Adventure Works Enterprise Subscription, aby zapewnić rozliczenia dla tego obciążenia.

## <a name="storage"></a>Magazyn
Cykle Adventure Works ustaliły, że powinni używać platformy Azure Managed Disks. Podczas tworzenia maszyn wirtualnych są używane obie dostępne warstwy magazynowania:

* **Magazyn w warstwie Standardowa** dla serwerów sieci Web, serwerów aplikacji i kontrolerów domeny oraz ich dysków z danymi.
* Usługa **Premium Storage** dla maszyn wirtualnych SQL Server i ich dysków z danymi.

## <a name="virtual-network-and-subnets"></a>Sieć wirtualna i podsieci
Ponieważ sieć wirtualna nie wymaga ciągłej łączności z siecią firmy Adventure Work Cycles, decyduje się ona w sieci wirtualnej tylko w chmurze.

Tworzą one sieć wirtualną tylko w chmurze o następujących ustawieniach przy użyciu Azure Portal:

* Name: AZOS-USE-VN01
* Lokalizacja: Wschodnie stany USA 2
* Przestrzeń adresowa sieci wirtualnej: 10.0.0.0/8
* Pierwsza podsieć:
  * Nazwa: fronton
  * Przestrzeń adresowa: 10.0.1.0/24
* Druga podsieć:
  * Nazwa: zaplecze
  * Przestrzeń adresowa: 10.0.2.0/24

## <a name="availability-sets"></a>Zestawy dostępności
Aby zachować wysoką dostępność wszystkich czterech warstw swojego sklepu online, firma Adventure Works Cycles zdecydowała się na czterech zestawach dostępności:

* **Azos — użycie jako sieci Web** dla serwerów sieci Web
* **Azos — użycie jako aplikacji** dla serwerów aplikacji
* **Azos — użycie jako SQL** dla serwerów SQL
* **Azos-as-DC** dla kontrolerów domeny

## <a name="virtual-machines"></a>Maszyny wirtualne
Cykle firmy Adventure Works decydują o następujących nazwach maszyn wirtualnych platformy Azure:

* **Azos-use-VM-web01** dla pierwszego serwera sieci Web
* **Azos-use-VM-web02** dla drugiego serwera sieci Web
* **Azos-use-app01-VM** dla pierwszego serwera aplikacji
* **Azos-use-VM-app02** dla drugiego serwera aplikacji
* **Azos-use-sql01-VM** dla pierwszego serwera SQL Server w klastrze
* **Azos-use-sql02-VM** dla drugiego serwera SQL Server w klastrze
* **Azos-use-DC01-VM** dla pierwszego kontrolera domeny
* **Azos-use-VM-dc02** dla drugiego kontrolera domeny

Poniżej przedstawiono konfigurację powstającą.

![Ostatnia infrastruktura aplikacji wdrożona na platformie Azure](./media/infrastructure-example/example-config.png)

Ta konfiguracja obejmuje:

* Sieć wirtualna tylko w chmurze z dwiema podsieciami (frontonu i zaplecza)
* Managed Disks platformy Azure z dyskami Standard i Premium
* Cztery zestawy dostępności — jeden dla każdej warstwy sklepu online
* Maszyny wirtualne dla czterech warstw
* Zewnętrzny zestaw o zrównoważonym obciążeniu oparty na protokole HTTPS dla ruchu internetowego z Internetu do serwerów sieci Web
* Wewnętrzny zestaw o zrównoważonym obciążeniu dla nieszyfrowanego ruchu internetowego z serwerów sieci Web do serwerów aplikacji
* Jedna grupa zasobów
