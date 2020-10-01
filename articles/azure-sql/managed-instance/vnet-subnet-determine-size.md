---
title: Określ wymagany rozmiar podsieci & zakres
titleSuffix: Azure SQL Managed Instance
description: W tym temacie opisano sposób obliczania rozmiaru podsieci, w której zostanie wdrożone wystąpienie zarządzane Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617643"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Określ wymagany rozmiar podsieci & zakres dla wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane Azure SQL należy wdrożyć w ramach [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md)platformy Azure.

Liczba wystąpień zarządzanych, które można wdrożyć w podsieci sieci wirtualnej, zależy od rozmiaru podsieci (zakresu podsieci).

Podczas tworzenia wystąpienia zarządzanego platforma Azure przydziela wiele maszyn wirtualnych w zależności od wybranej warstwy podczas aprowizacji. Ponieważ te maszyny wirtualne są skojarzone z podsiecią, wymagają one adresów IP. Aby zapewnić wysoką dostępność podczas regularnych operacji i konserwacji usług, platforma Azure może przydzielić dodatkowe maszyny wirtualne. W związku z tym liczba wymaganych adresów IP w podsieci jest większa niż liczba wystąpień zarządzanych w tej podsieci.

Zgodnie z projektem wystąpienie zarządzane wymaga co najmniej 32 adresów IP w podsieci. W związku z tym podczas definiowania zakresów adresów IP podsieci można użyć minimalnej maski podsieci /27. Zalecane jest staranne zaplanowanie rozmiaru podsieci dla wdrożeń wystąpienia zarządzanego. Dane wejściowe, które należy wziąć pod uwagę podczas planowania, są następujące:

- Liczba wystąpień zarządzanych, takich jak następujące parametry wystąpienia:
  - warstwa usług
  - Generowanie sprzętu
  - Liczba rdzeni wirtualnych
- Planuje skalowanie w górę/w dół lub zmiana warstwy usług

> [!IMPORTANT]
> Rozmiar podsieci z 16 adresami IP (Maska podsieci/28) umożliwi wdrożenie wystąpienia zarządzanego w tym środowisku, ale powinien być używany tylko do wdrażania pojedynczego wystąpienia używanego do oceny lub w scenariuszach deweloperskich/testowych, w których operacje skalowania nie będą wykonywane.

## <a name="determine-subnet-size"></a>Określanie rozmiaru podsieci

Należy zmienić rozmiar podsieci zgodnie z wdrożeniem przyszłych wystąpień i skalowaniem. Następujące parametry mogą ułatwić tworzenie obliczeń:

- Platforma Azure używa pięciu adresów IP w podsieci do własnych potrzeb
- Każdy klaster wirtualny przydziela dodatkową liczbę adresów 
- Każde wystąpienie zarządzane używa liczby adresów, które są zależne od warstwy cenowej i generowania sprzętu

> [!IMPORTANT]
> Nie można zmienić zakresu adresów podsieci, jeśli jakieś zasoby istnieją w podsieci. Nie jest również możliwe przenoszenie wystąpień zarządzanych z jednej podsieci do innej. Jeśli to możliwe, należy rozważyć użycie większych podsieci zamiast mniejszych, aby zapobiec problemom w przyszłości.

GP = ogólnego przeznaczenia; BC = krytyczne dla działania firmy; VC = klaster wirtualny

| **Generator sprzętu** | **Warstwa cenowa** | **Użycie platformy Azure** | **Użycie VC** | **Użycie wystąpienia** | **Ogólnego*** |
| --- | --- | --- | --- | --- | --- |
| Obliczenia | GP | 5 | 1 | 5 | 11 |
| Obliczenia | BC | 5 | 1 | 5 | 11 |
| 5 rdzeń | GP | 5 | 6 | 3 | 14 |
| 5 rdzeń | BC | 5 | 6 | 5 | 16 |

  \* Całkowita kolumna zawiera liczbę adresów, które zostałyby wykonane, gdy jedno wystąpienie zostanie wdrożone w podsieci. Każde dodatkowe wystąpienie w podsieci dodaje liczbę adresów reprezentowanych w kolumnie użycie wystąpienia. Adresy reprezentowane za pomocą kolumny użycie platformy Azure są udostępniane w wielu klastrach wirtualnych, natomiast adresy reprezentowane za pomocą kolumny użycie VC są udostępniane między wystąpieniami umieszczonymi w tym klastrze wirtualnym.

Operacja aktualizacji zwykle wymaga zmiany rozmiaru klastra wirtualnego. W niektórych okolicznościach operacja aktualizacji będzie wymagała utworzenia klastra wirtualnego (Aby uzyskać więcej szczegółów, zobacz artykuł dotyczący [operacji administracyjnych](sql-managed-instance-paas-overview.md#management-operations)). W przypadku tworzenia klastra wirtualnego liczba wymaganych dodatkowych adresów jest równa liczbie adresów reprezentowanych przez kolumnę użycie VC z adresami wymaganymi dla wystąpień umieszczonych w tym klastrze wirtualnym (kolumna użycie wystąpienia).

**Przykład 1**: planujesz posiadanie jednego wystąpienia zarządzanego ogólnego przeznaczenia (obliczenia sprzęt) i jednego wystąpienia zarządzanego przez firmę (sprzęt 5 rdzeń). Oznacza to, że do wdrożenia wymagane jest co najmniej 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 adresy IP. Ponieważ zakresy adresów IP są zdefiniowane w mocy 2, podsieć wymaga minimalnego zakresu adresów IP 32 (2 ^ 5) dla tego wdrożenia.<br><br>
Jak wspomniano powyżej, w niektórych przypadkach operacja aktualizacji będzie wymagała utworzenia klastra wirtualnego. Oznacza to, że na przykład w przypadku aktualizacji wystąpienia programu 5 rdzeń o krytycznym znaczeniu dla firmy, które wymaga utworzenia klastra wirtualnego, należy udostępnić dodatkowe 6 + 5 = 11 adresów IP. Ponieważ korzystasz już z 22 adresów 32, nie ma żadnych dostępnych adresów dla tej operacji. W związku z tym należy zarezerwować podsieć z maską podsieci/26 (64 adresów).

**Przykład 2**: planujesz trzy (5 rdzeń sprzęt) i dwa krytyczne wystąpienia zarządzane przez firmę (sprzęt 5 rdzeń) umieszczone w tej samej podsieci. Oznacza to, że potrzebujesz 5 + 6 + 3 * 3 + 2 * 5 = 30 adresów IP. W związku z tym należy zarezerwować podsieć z maską podsieci/26. Wybranie maski podsieci/27 spowoduje, że pozostała liczba adresów będzie równa 2 (32 – 30), co uniemożliwi wykonywanie operacji aktualizacji dla wszystkich wystąpień jako dodatkowych adresów w podsieci w celu przeprowadzenia skalowania wystąpienia.

**Przykład 3**: planujesz posiadanie jednego wystąpienia zarządzanego ogólnego przeznaczenia (5 rdzeń sprzęt) i wykonywanie operacji aktualizacji rdzeni wirtualnych z czasu do czasu. Oznacza to, że potrzebujesz 5 + 6 + 1 * 3 + 3 = 17 adresów IP. Ponieważ zakresy adresów IP są zdefiniowane w mocy 2, wymagany jest zakres adresów IP 32 (2 ^ 5). W związku z tym należy zarezerwować podsieć z maską podsieci/27.

### <a name="address-requirements-for-update-scenarios"></a>Wymagania dotyczące adresów dla scenariuszy aktualizacji

Gdy wystąpienia operacji skalowania tymczasowo wymagają dodatkowej pojemności IP, która zależy od warstwy cenowej i generowania sprzętu

| **Generator sprzętu** | **Warstwa cenowa** | **Scenariusz** | **Dodatkowe adresy*** |
| --- | --- | --- | --- |
| Obliczenia | GP lub BC | Skalowanie rdzeni wirtualnych | 5 |
| Obliczenia | GP lub BC | Skalowanie magazynu | 5 |
| Obliczenia | GP lub BC | Przełączanie z zasad grupy do BC lub BC do GP | 5 |
| Obliczenia | GP | Przełączanie do 5 rdzeń * | 9 |
| Obliczenia | BC | Przełączanie do 5 rdzeń * | 11 |
| 5 rdzeń | GP | Skalowanie rdzeni wirtualnych | 3 |
| 5 rdzeń | GP | Skalowanie magazynu | 0 |
| 5 rdzeń | GP | Przełączanie do BC | 5 |
| 5 rdzeń | BC | Skalowanie rdzeni wirtualnych | 5 |
| 5 rdzeń | BC | Skalowanie magazynu | 5 |
| 5 rdzeń | BC | Przełączanie do zasad grupy | 3 |

  \* Obliczenia sprzęt jest wycofywany i nie jest już dostępny dla nowych wdrożeń. Zaktualizuj generowanie sprzętu z obliczenia do 5 rdzeń, aby korzystać z funkcji specyficznych dla generacji sprzętu 5 rdzeń.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane Azure SQL?](sql-managed-instance-paas-overview.md).
- Dowiedz się więcej o [architekturze łączności dla wystąpienia zarządzanego SQL](connectivity-architecture-overview.md).
- Zobacz jak [utworzyć sieć wirtualną, w której zostanie wdrożone wystąpienie zarządzane SQL](virtual-network-subnet-create-arm-template.md).
- Problemy związane z usługą DNS można znaleźć w temacie [Configure a Custom DNS](custom-dns-configure.md).
