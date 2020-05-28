---
title: Określ wymagany rozmiar podsieci & zakres
titleSuffix: Azure SQL Managed Instance
description: W tym temacie opisano sposób obliczania rozmiaru podsieci, w której zostaną wdrożone wystąpienia zarządzane usługi Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 5eebde1fc95cb50f8ff7c13b6cbc411484ddf943
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050933"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Określ wymagany rozmiar podsieci & zakres dla wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane Azure SQL należy wdrożyć w ramach [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md)platformy Azure.

Liczba wystąpień zarządzanych przez program SQL, które można wdrożyć w podsieci sieci wirtualnej, zależy od rozmiaru podsieci (zakresu podsieci).

Podczas tworzenia wystąpienia zarządzanego SQL platforma Azure przydziela wiele maszyn wirtualnych w zależności od wybranej warstwy podczas aprowizacji. Ponieważ te maszyny wirtualne są skojarzone z podsiecią, wymagają one adresów IP. Aby zapewnić wysoką dostępność podczas regularnych operacji i konserwacji usług, platforma Azure może przydzielić dodatkowe maszyny wirtualne. W związku z tym liczba wymaganych adresów IP w podsieci jest większa niż liczba wystąpień zarządzanych przez program SQL w tej podsieci.

Zgodnie z projektem wystąpienie zarządzane SQL musi mieć co najmniej 16 adresów IP w podsieci i może korzystać z maksymalnie 256 adresów IP. W związku z tym podczas definiowania zakresów adresów IP podsieci można użyć masek podsieci między/28 i/24. Bit maski sieci wynoszący/28 (14 hostów na sieć) to dobry rozmiar jednego ogólnego celu lub krytycznego dla firmy. Bit maski z/27 (30 hostów na sieć) jest idealnym rozwiązaniem dla wielu wdrożeń wystąpienia zarządzanego SQL w ramach tej samej sieci wirtualnej. Ustawienia bitów maski/26 (62 hosty) i/24 (hosty 254) umożliwiają dalsze skalowanie w sieci wirtualnej w celu obsługi dodatkowych wystąpień zarządzanych przez program SQL.

> [!IMPORTANT]
> Rozmiar podsieci z 16 adresami IP jest minimalny od zera z ograniczonym potencjalną operacją skalowania, taką jak rdzeń wirtualny zmiana rozmiaru. Wybór podsieci z prefiksem/27 lub najdłuższym prefiksem jest zdecydowanie zalecane.

## <a name="determine-subnet-size"></a>Określanie rozmiaru podsieci

Jeśli planujesz wdrożyć wiele wystąpień zarządzanych przez program SQL wewnątrz podsieci i chcesz zoptymalizować rozmiar podsieci, Użyj tych parametrów do utworzenia obliczenia:

- Platforma Azure używa pięciu adresów IP w podsieci do własnych potrzeb
- Każde wystąpienie Ogólnego przeznaczenia wymaga dwóch adresów
- Każde wystąpienie Krytyczne dla działania firmy potrzebuje czterech adresów

**Przykład**: planujesz trzy ogólnego przeznaczenia i dwa krytyczne dla działania firmy wystąpienia zarządzane SQL. Oznacza to, że potrzebujesz 5 + 3 * 2 + 2 * 4 = 19 adresów IP. Ponieważ zakresy adresów IP są zdefiniowane w mocy 2, wymagany jest zakres adresów IP 32 (2 ^ 5). W związku z tym należy zarezerwować podsieć z maską podsieci/27.

> [!IMPORTANT]
> Obliczenia wyświetlane powyżej staną się przestarzałe z dalszych ulepszeń.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane SQL](sql-managed-instance-paas-overview.md).
- Dowiedz się więcej o [architekturze łączności dla wystąpienia zarządzanego SQL](connectivity-architecture-overview.md).
- Zobacz jak [utworzyć sieć wirtualną, w której będą wdrażane wystąpienia zarządzane SQL](virtual-network-subnet-create-arm-template.md)
- W przypadku problemów z usługą DNS zobacz [Konfigurowanie niestandardowego systemu DNS](custom-dns-configure.md)
