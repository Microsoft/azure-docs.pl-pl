---
title: Przykładowa instruktaż infrastruktury platformy Azure
description: Dowiedz się więcej o kluczowych wskazówki dotyczące projektowania i implementacji dotyczące wdrażania przykładowej infrastruktury na platformie Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: example-scenario
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 43e96b891e60dfcf8bc3c29b202bb60213905372
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869470"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Przykładowy przewodnik po infrastrukturze platformy Azure dla maszyn wirtualnych systemu Windows
W tym artykule o wiele więcej niż w artykule. Szczegółowo projektujemy infrastrukturę dla prostego sklepu internetowego, który łączy wszystkie wytyczne i decyzje dotyczące konwencji nazewnictwa, zestawów dostępności, sieci wirtualnych i modułów równoważenia obciążenia, a także faktycznie wdraża maszyny wirtualne.We detailing an infrastructure for a simple online store that brings together all the guidelines and decisions around naming conventions, availability sets, virtual networks and load balancebalrs, and actually deploying your virtual machines (VMs).

## <a name="example-workload"></a>Przykładowe obciążenie pracą
Adventure Works Cycles chce utworzyć aplikację sklepu internetowego na platformie Azure, która składa się z:

* Dwa serwery usług IIS z front-endem klienta w warstwie sieci web
* Dwa serwery usług IIS przetwarzają dane i zamówienia w warstwie aplikacji
* Dwa wystąpienia programu Microsoft SQL Server z grupami dostępności AlwaysOn (dwa serwery SQL i monitor węzła większościowego) do przechowywania danych produktów i zamówień w warstwie bazy danych
* Dwa kontrolery domeny usługi Active Directory dla kont klientów i dostawców w warstwie uwierzytelniania
* Wszystkie serwery znajdują się w dwóch podsieciach:
  * podsieci front-end dla serwerów sieci web 
  * podsieć zaplecza dla serwerów aplikacji, klastra SQL i kontrolerów domeny

![Diagram różnych warstw infrastruktury aplikacji](./media/infrastructure-example/example-tiers.png)

Przychodzący bezpieczny ruch internetowy musi być równoważący obciążenie między serwerami sieci web, ponieważ klienci przeglądają sklep internetowy. Ruch przetwarzania zamówień w postaci żądań HTTP z serwerów sieci web musi być zrównoważony między serwerami aplikacji. Ponadto infrastruktura musi być zaprojektowana z myślą o wysokiej dostępności.

Powstały wzór musi zawierać:

* Subskrypcja i konto platformy Azure
* Pojedyncza grupa zasobów
* Dyski zarządzane platformy Azure
* Sieć wirtualna z dwiema podsieciami
* Zestawy dostępności dla maszyn wirtualnych o podobnej roli
* Maszyny wirtualne

Wszystkie powyższe są zgodne z tymi konwencjami nazewnictwa:

* Adventure Works Cycles używa **[obciążenia IT]-[lokalizacja]-[Zasób platformy Azure]** jako prefiksu
  * W tym przykładzie **"azos**" (Sklep internetowy platformy Azure) jest nazwą obciążenia IT i "**use**" (East US 2) jest lokalizacją
* Sieci wirtualne używają AZOS-USE-VN **[numer]**
* Zestawy dostępności używają azos-use-as-**[rola]**
* Nazwy maszyn wirtualnych używają azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Subskrypcje i konta platformy Azure
Adventure Works Cycles używa subskrypcji Enterprise o nazwie Adventure Works Enterprise Subscription, aby zapewnić rozliczenia za to obciążenie IT.

## <a name="storage"></a>Magazyn
Adventure Works Cycles określa, że należy używać dysków zarządzanych platformy Azure. Podczas tworzenia maszyn wirtualnych używane są obie dostępne warstwy magazynu:

* **Standardowy magazyn** dla serwerów sieci web, serwerów aplikacji i kontrolerów domeny oraz ich dysków danych.
* **Magazyn w wersji premium** dla maszyn wirtualnych programu SQL Server i ich dysków z danymi.

## <a name="virtual-network-and-subnets"></a>Sieć wirtualna i podsieci
Ponieważ sieć wirtualna nie wymaga ciągłej łączności z siecią lokalną Adventure Work Cycles, zdecydowali się na sieć wirtualną tylko w chmurze.

Utworzyli sieć wirtualną tylko w chmurze z następującymi ustawieniami przy użyciu witryny Azure portal:

* Nazwa: AZOS-USE-VN01
* Lokalizacja: Wschodnie stany USA 2
* Przestrzeń adresowa sieci wirtualnej: 10.0.0.0/8
* Pierwsza podsieć:
  * Nazwa: FrontEnd
  * Przestrzeń adresowa: 10.0.1.0/24
* Druga podsieć:
  * Nazwa: BackEnd
  * Przestrzeń adresowa: 10.0.2.0/24

## <a name="availability-sets"></a>Zestawy dostępności
Aby utrzymać wysoką dostępność wszystkich czterech warstw swojego sklepu internetowego, Adventure Works Cycles zdecydowało się na cztery zestawy dostępności:

* **azos-use-as-web** dla serwerów internetowych
* **azos-use-as-app** dla serwerów aplikacji
* **azos-use-as-sql** dla serwerów SQL
* **azos-use-as-dc** dla kontrolerów domeny

## <a name="virtual-machines"></a>Maszyny wirtualne
Adventure Works Cycles zdecydował o następujących nazwach dla swoich maszyn wirtualnych platformy Azure:

* **azos-use-vm-web01** dla pierwszego serwera www
* **azos-use-vm-web02** dla drugiego serwera www
* **azos-use-vm-app01** dla pierwszego serwera aplikacji
* **azos-use-vm-app02** dla drugiego serwera aplikacji
* **azos-use-vm-sql01** dla pierwszego serwera SQL Server w klastrze
* **azos-use-vm-sql02** dla drugiego serwera SQL Server w klastrze
* **azos-use-vm-dc01** dla pierwszego kontrolera domeny
* **azos-use-vm-dc02** dla drugiego kontrolera domeny

Oto wynikowa konfiguracja.

![Ostateczna infrastruktura aplikacji wdrożona na platformie Azure](./media/infrastructure-example/example-config.png)

Ta konfiguracja zawiera:

* Sieć wirtualna tylko w chmurze z dwiema podsieciami (FrontEnd i BackEnd)
* Dyski zarządzane platformy Azure z dyskami standardowymi i premium
* Cztery zestawy dostępności, po jednym dla każdej warstwy sklepu internetowego
* Maszyny wirtualne dla czterech warstw
* Zestaw z równoważeniem obciążenia zewnętrznego dla ruchu internetowego opartego na protokoi https z Internetu do serwerów sieci web
* Wewnętrzny zestaw z równoważeniem obciążenia dla niezaszyfrowanego ruchu sieci web z serwerów sieci web do serwerów aplikacji
* Pojedyncza grupa zasobów
