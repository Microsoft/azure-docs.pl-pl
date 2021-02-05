---
title: Jak jest naliczany rabat za rezerwację na platformie Azure
description: Ten artykuł pomaga zrozumieć, jak są ogólnie stosowane rabaty na wystąpienia zarezerwowane.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1dec8253d785973aa900434c10f85ec0a63e0599
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574876"
---
# <a name="how-a-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Ten artykuł pomaga zrozumieć, jak są ogólnie stosowane rabaty na wystąpienia zarezerwowane. Rabat związany z rezerwacją dotyczy użycia zasobów pasującego do atrybutów wybranych podczas zakupu rezerwacji. Atrybuty obejmują zakres, w którym są uruchamiane zgodne maszyny wirtualne, bazy danych SQL, bazy danych Azure Cosmos DB lub inne zasoby. Jeśli na przykład chcesz uzyskać rabat na rezerwację dla czterech standardowych maszyn wirtualnych D2 w regionie Zachodnie stany USA, wybierz subskrypcję, w której działają te maszyny wirtualne.

Rabat na rezerwację jest dostępny na zasadzie „*wykorzystaj lub strać*”. Jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną *utracone*.

Na przykład możesz później utworzyć zasób i dopasować go do rezerwacji, która jest niedostatecznie wykorzystywana. Rabat na rezerwację zostanie automatycznie zastosowany do nowego pasującego zasobu.

Jeśli maszyny wirtualne są uruchomione w różnych subskrypcjach w ramach rejestracji/konta, wybierz zakres jako udostępniony. Zakres udostępniony umożliwia stosowanie rabatu na rezerwację w różnych subskrypcjach. Zakres można zmienić po zakupie rezerwacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie rezerwacjami platformy Azure](manage-reserved-vm-instance.md).

Rabat na rezerwację dotyczy tylko zasobów skojarzonych z subskrypcją Enterprise, umową klienta firmy Microsoft, dostawcą usług w chmurze lub subskrypcjami ze stawkami płatności zgodnie z rzeczywistym użyciem. Zasoby działające w ramach subskrypcji z innymi typami ofert nie otrzymują rabatu na rezerwację.

## <a name="when-the-reservation-term-expires"></a>Gdy okres rezerwacji wygaśnie

Na koniec okresu rezerwacji rabat rozliczenia wygasa i od tego momentu zasoby są rozliczane według cen przy płatności zgodnie z rzeczywistym użyciem. Domyślnie rezerwacje nie są ustawiane na automatyczne odnawianie. Automatyczne odnawianie rezerwacji możesz włączyć, wybierając opcję w ustawieniach odnawiania. W przypadku automatycznego odnawiania zastępcza rezerwacja będzie kupowana po wygaśnięciu istniejącej rezerwacji. Domyślnie zastępcza rezerwacja ma te same atrybuty co wygasająca rezerwacja. Opcjonalnie można zmienić częstotliwość rozliczeń, okres lub ilość w ustawieniach odnawiania. Każdy użytkownik mający dostęp na poziomie właściciela do rezerwacji i subskrypcji używanej do rozliczeń może skonfigurować odnawianie.  

## <a name="discount-applies-to-different-sizes"></a>Rabat ma zastosowanie do różnych rozmiarów

W przypadku zakupu rezerwacji rabat może być stosowany do innych wystąpień z atrybutami, które znajdują się w tej samej grupie rozmiarów. Ta funkcja jest określana jako elastyczność rozmiaru wystąpienia. Elastyczność zastosowania rabatu zależy od typu rezerwacji i atrybutów wybranych podczas zakupu rezerwacji.

Plany usługi:

- Wystąpienia zarezerwowane maszyn wirtualnych: Po zakupieniu rezerwacji i wybraniu opcji **Zoptymalizowane pod kątem elastyczności rozmiaru wystąpienia** zastosowanie rabatu zależy od wybranego rozmiaru maszyny wirtualnej. Rezerwacja może dotyczyć rozmiarów maszyn wirtualnych w tej samej grupie serii rozmiarów. Aby uzyskać więcej informacji, zobacz artykuł [Elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych](../../virtual-machines/reserved-vm-instance-size-flexibility.md).
- Pojemność zarezerwowana usługi Azure Storage: Możesz kupić pojemność zarezerwowaną dla kont usługi Azure Storage w warstwie Standardowa w jednostkach po 100 TiB lub 1 PiB na miesiąc. Aby uzyskać informacje na temat regionów obsługujących pojemność zarezerwowaną usługi Azure Storage, zobacz [Cennik blokowych obiektów blob](https://azure.microsoft.com/pricing/details/storage/blobs/). Pojemność zarezerwowana usługi Azure Storage jest dostępna dla wszystkich warstw dostępu (Gorąca, Chłodna i Archiwum) oraz dla dowolnej konfiguracji replikacji (magazyn lokalnie nadmiarowy, magazyn geograficznie nadmiarowy lub magazyn strefowo nadmiarowy).
- Pojemność zarezerwowana usługi SQL Database: Zastosowanie rabatu zależy od wybranej warstwy wydajności. Aby uzyskać więcej informacji, zobacz [opis sposobu stosowania rabatu na rezerwację platformy Azure](understand-reservation-charges.md).
- Pojemność zarezerwowana usługi Azure Cosmos DB: Zastosowanie rabatu zależy od alokowanej przepływności. Aby uzyskać więcej informacji, zobacz [opis sposobu stosowania rabatu na rezerwację usługi Azure Cosmos DB](understand-cosmosdb-reservation-charges.md).

## <a name="how-discounts-apply-to-specific-azure-services"></a>Jak rabaty mają zastosowanie do określonych usług platformy Azure

Przeczytaj następujące artykuły, które odnoszą się do Ciebie, aby dowiedzieć się, jak rabaty mają zastosowanie do określonej usługi platformy Azure:

- [App Service](reservation-discount-app-service.md)
- [Azure Cache for Redis](understand-azure-cache-for-redis-reservation-charges.md)
- [Cosmos DB](understand-cosmosdb-reservation-charges.md)
- [Database for MariaDB](understand-reservation-charges-mariadb.md)
- [Database for MySQL](understand-reservation-charges-mysql.md)
- [Database for PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Data Explorer](understand-azure-data-explorer-reservation-charges.md)
- [Dedykowane hosty](billing-understand-dedicated-hosts-reservation-charges.md)
- [Disk Storage](understand-disk-reservations.md)
- [Red Hat Linux Enterprise](understand-rhel-reservation-charges.md)
- [Plany oprogramowania](understand-suse-reservation-charges.md)
- [Storage](understand-storage-charges.md)
- [SQL Database](understand-reservation-charges.md)
- [Azure Synapse Analytics](reservation-discount-azure-sql-dw.md)
- [Maszyny wirtualne](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>Następne kroki

- [Zarządzanie usługą Azure Reservations](manage-reserved-vm-instance.md)
- [Understand reservation usage for your subscription with pay-as-you-go rates](understand-reserved-instance-usage.md) (Informacje na temat użycia rezerwacji w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows nieuwzględniane w przypadku wystąpień zarezerwowanych](reserved-instance-windows-software-costs.md)