---
title: Określ wymagany rozmiar podsieci & zakres
titleSuffix: Azure SQL Managed Instance
description: W tym temacie opisano sposób obliczania rozmiaru podsieci, w której zostanie wdrożone wystąpienie zarządzane Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 8d1073dbcced9532390776a23dd17c1f572cce40
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686685"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Określ wymagany rozmiar podsieci & zakres dla wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane Azure SQL należy wdrożyć w ramach [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md)platformy Azure.

Liczba wystąpień zarządzanych, które można wdrożyć w podsieci sieci wirtualnej, zależy od rozmiaru podsieci (zakresu podsieci).

Podczas tworzenia wystąpienia zarządzanego platforma Azure przydziela wiele maszyn wirtualnych w zależności od wybranej warstwy podczas aprowizacji. Ponieważ te maszyny wirtualne są skojarzone z podsiecią, wymagają one adresów IP. Aby zapewnić wysoką dostępność podczas regularnych operacji i konserwacji usług, platforma Azure może przydzielić dodatkowe maszyny wirtualne. W związku z tym liczba wymaganych adresów IP w podsieci jest większa niż liczba wystąpień zarządzanych w tej podsieci.

Zgodnie z projektem wystąpienie zarządzane musi mieć co najmniej 16 adresów IP w podsieci i może korzystać z maksymalnie 256 adresów IP. W związku z tym podczas definiowania zakresów adresów IP podsieci można użyć masek podsieci między/28 i/24. Bit maski sieci wynoszący/28 (14 hostów na sieć) to dobry rozmiar jednego wdrożenia ogólnego lub krytycznego dla firmy. Bit maski z/27 (30 hostów na sieć) idealnie sprawdza się w przypadku wielu wdrożeń wystąpienia zarządzanego SQL w ramach tej samej sieci wirtualnej. Ustawienia bitów maski/26 (62 hosty) i/24 (hosty 254) umożliwiają dalsze skalowanie w sieci wirtualnej w celu obsługi dodatkowych wystąpień zarządzanych.

> [!IMPORTANT]
> Rozmiar podsieci z 16 adresami IP jest minimalny od zera z ograniczonym potencjalną operacją skalowania, taką jak rdzeń wirtualny zmiana rozmiaru. Wybór podsieci z prefiksem/27 lub najdłuższym prefiksem jest zdecydowanie zalecane.

## <a name="determine-subnet-size"></a>Określanie rozmiaru podsieci

Jeśli planujesz wdrożyć wiele wystąpień zarządzanych wewnątrz podsieci i chcesz zoptymalizować rozmiar podsieci, Użyj tych parametrów do tworzenia obliczeń:

- Platforma Azure używa pięciu adresów IP w podsieci do własnych potrzeb
- Każde wystąpienie ogólnego zastosowania wymaga dwóch adresów
- Każde wystąpienie krytyczne dla działania firmy wymaga czterech adresów

**Przykład**: planujesz trzy cele ogólnego zastosowania i dwa wystąpienia zarządzane o krytycznym znaczeniu dla firmy. Oznacza to, że potrzebujesz 5 + 3 * 2 + 2 * 4 = 19 adresów IP. Ponieważ zakresy adresów IP są zdefiniowane w kompetencjach 2, wymagany jest zakres adresów IP 32 (2 ^ 5). W związku z tym należy zarezerwować podsieć z maską podsieci/27.

> [!IMPORTANT]
> Obliczenia wyświetlane powyżej staną się przestarzałe z dalszych ulepszeń.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane Azure SQL?](sql-managed-instance-paas-overview.md).
- Dowiedz się więcej o [architekturze łączności dla wystąpienia zarządzanego SQL](connectivity-architecture-overview.md).
- Zobacz jak [utworzyć sieć wirtualną, w której zostanie wdrożone wystąpienie zarządzane SQL](virtual-network-subnet-create-arm-template.md).
- Problemy związane z usługą DNS można znaleźć w temacie [Configure a Custom DNS](custom-dns-configure.md).
