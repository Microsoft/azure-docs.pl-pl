---
title: Przykład Przewodnik dotyczący infrastruktury platformy Azure
description: Poznaj wytyczne dotyczące projektowania i wdrażania przykładowej infrastruktury na platformie Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 500de3f89bd041adf0b73e21762495d8c89e19c8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286295"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Przykład Przewodnik dotyczący infrastruktury platformy Azure dla maszyn wirtualnych z systemem Linux
W tym artykule omówiono Tworzenie przykładowej infrastruktury aplikacji. Szczegółowo projektujemy infrastrukturę dla prostego magazynu online, który łączy wszystkie wytyczne i decyzje dotyczące konwencji nazewnictwa, zestawów dostępności, sieci wirtualnych i modułów równoważenia obciążenia, a także w rzeczywistości wdrażają maszyny wirtualne.

## <a name="example-workload"></a>Przykładowe obciążenie
Cykle firmy Adventure Works chcą skompilować aplikację ze sklepu online na platformie Azure, która składa się z:

* Dwa serwery Nginx z frontonem klienta w warstwie sieci Web
* Dwa serwery Nginx przetwarzania danych i zamówień w warstwie aplikacji
* Dwa serwery MongoDBa częścią klastra podzielonej na fragmenty do przechowywania danych i zamówień produktów w warstwie bazy danych
* Dwa Active Directory kontrolery domeny dla kont klientów i dostawców w warstwie uwierzytelniania
* Wszystkie serwery znajdują się w dwóch podsieciach:
  * podsieć frontonu dla serwerów sieci Web 
  * podsieć zaplecza dla serwerów aplikacji, klastra MongoDB i kontrolerów domeny

![Diagram różnych warstw dla infrastruktury aplikacji](./media/infrastructure-example/example-tiers.png)

Przychodzący zabezpieczony bezpieczny ruch internetowy musi być zrównoważony obciążeniem między serwerami sieci Web, gdy klienci przeglądają magazyn online. Ruch przetwarzania zamówień w formie żądań HTTP z serwerów sieci Web musi być zrównoważony obciążeniem między serwerami aplikacji. Dodatkowo infrastruktura musi być zaprojektowana w celu zapewnienia wysokiej dostępności.

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
* Sieci wirtualne używają AZOS.-VN<strong>[Number]</strong>
* Zestawy dostępności używają Azos-use-AS-**[role]**
* Nazwy maszyn wirtualnych używają Azos-use-VM-**[VMName]**

## <a name="azure-subscriptions-and-accounts"></a>Subskrypcje i konta platformy Azure
Cykle Adventure Works korzystają z subskrypcji przedsiębiorstwa o nazwie Adventure Works Enterprise Subscription, aby zapewnić rozliczenia dla tego obciążenia.

## <a name="storage"></a>Magazyn
Cykle Adventure Works ustaliły, że powinni używać platformy Azure Managed Disks. Podczas tworzenia maszyn wirtualnych są używane warstwy magazynowania dostępne dla magazynu:

* **Magazyn w warstwie Standardowa** dla serwerów sieci Web, serwerów aplikacji i kontrolerów domeny oraz ich dysków z danymi.
* Usługa **Premium Storage** dla serwerów klastrów MongoDB podzielonej na fragmenty i ich dysków z danymi.

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
Aby zachować wysoką dostępność wszystkich czterech warstw magazynu w wierszu, firma Adventure Works Cycles podjęła cztery zestawy dostępności:

* **Azos — użycie jako sieci Web** dla serwerów sieci Web
* **Azos — użycie jako aplikacji** dla serwerów aplikacji
* **Azos-as-DB** dla serwerów w klastrze MongoDB podzielonej na fragmenty
* **Azos-as-DC** dla kontrolerów domeny

## <a name="virtual-machines"></a>Maszyny wirtualne
Cykle firmy Adventure Works decydują o następujących nazwach maszyn wirtualnych platformy Azure:

* **Azos-use-VM-web01** dla pierwszego serwera sieci Web
* **Azos-use-VM-web02** dla drugiego serwera sieci Web
* **Azos-use-app01-VM** dla pierwszego serwera aplikacji
* **Azos-use-VM-app02** dla drugiego serwera aplikacji
* **Azos-db01** dla pierwszego serwera MongoDB w klastrze
* **Azos-db02** drugiego serwera MongoDB w klastrze
* **Azos-use-DC01-VM** dla pierwszego kontrolera domeny
* **Azos-use-VM-dc02** dla drugiego kontrolera domeny

Poniżej przedstawiono konfigurację powstającą.

![Ostatnia infrastruktura aplikacji wdrożona na platformie Azure](./media/infrastructure-example/example-config.png)

Ta konfiguracja obejmuje:

* Sieć wirtualna tylko w chmurze z dwiema podsieciami (frontonu i zaplecza)
* Azure Managed Disks przy użyciu dysków w warstwach Standardowa i Premium
* Cztery zestawy dostępności — jeden dla każdej warstwy sklepu w wierszu
* Maszyny wirtualne dla czterech warstw
* Zewnętrzny zestaw o zrównoważonym obciążeniu oparty na protokole HTTPS dla ruchu internetowego z Internetu do serwerów sieci Web
* Wewnętrzny zestaw o zrównoważonym obciążeniu dla nieszyfrowanego ruchu internetowego z serwerów sieci Web do serwerów aplikacji
* Jedna grupa zasobów
