---
title: Dowiedz się więcej na temat trybów aranżacji dla zestawów skalowania maszyn wirtualnych na platformie Azure
description: Dowiedz się więcej na temat trybów aranżacji dla zestawów skalowania maszyn wirtualnych na platformie Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 10/23/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: eb7d4d8a6f1c1ee55601cdd839e330147e60bcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011077"
---
# <a name="orchestration-modes-preview"></a>Tryby aranżacji (wersja zapoznawcza)

> [!CAUTION]
> Dziękujemy za wszystkich osoby, które uczestniczyły w tej publicznej wersji zapoznawczej. Mogliśmy zebrać cenne opinie z naszej społeczności. Ta wersja zapoznawcza jest teraz **ZAMKNIĘTA** dla nowych uczestników, aby można było zintegrować Opinie. Będziemy aktualizować to miejsce przy użyciu nowych informacji.

Zestawy skalowania maszyn wirtualnych stanowią logiczne grupowanie maszyn wirtualnych zarządzanych przez platformę. Zestawy skalowania umożliwiają utworzenie modelu konfiguracji maszyny wirtualnej, automatyczne dodanie lub usunięcie dodatkowych wystąpień na podstawie obciążenia procesora lub pamięci oraz automatyczne uaktualnienie do najnowszej wersji systemu operacyjnego. Tradycyjnie zestawy skalowania umożliwiają tworzenie maszyn wirtualnych przy użyciu modelu konfiguracji maszyny wirtualnej dostarczonego w momencie tworzenia zestawu skalowania, a zestaw skalowania może zarządzać wyłącznie maszynami wirtualnymi utworzonymi niejawnie na podstawie modelu konfiguracji.

W trybie aranżacji zestawu skalowania (wersja zapoznawcza) można teraz wybrać, czy zestaw skalowania ma organizować maszyny wirtualne, które są tworzone jawnie poza modelem konfiguracji zestawu skalowania, lub wystąpieniami maszyn wirtualnych utworzonymi niejawnie na podstawie modelu konfiguracji. Tryb aranżacji zestawu skalowania ułatwia również Projektowanie infrastruktury maszyny wirtualnej pod kątem wysokiej dostępności, wdrażając te maszyny wirtualne w domenach błędów i Strefy dostępności.


Zestawy skalowania maszyn wirtualnych będą obsługiwały dwa różne tryby aranżacji:

- ScaleSetVM — wystąpienia maszyn wirtualnych dodane do zestawu skalowania są oparte na modelu konfiguracji zestawu skalowania. Cykl życia wystąpienia maszyny wirtualnej — tworzenie, aktualizowanie, usuwanie — jest zarządzany przez zestaw skalowania.
- Maszyna wirtualna (maszyny wirtualne) — maszyny wirtualne utworzone poza zestaw skalowania można jawnie dodać do zestawu skalowania. 
 

> [!IMPORTANT]
> Tryb aranżacji jest definiowany podczas tworzenia zestawu skalowania i nie można go zmienić ani zaktualizować później. 
> 
> Ta funkcja zestawów skalowania maszyn wirtualnych jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. 
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Tryby aranżacji

| Cecha                     | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Model konfiguracji maszyny wirtualnej      | Brak                                       | Wymagane |
| Dodawanie nowej maszyny wirtualnej do zestawu skalowania  | Maszyny wirtualne są jawnie dodawane do zestawu skalowania podczas tworzenia maszyny wirtualnej. | Maszyny wirtualne są niejawnie tworzone i dodawane do zestawu skalowania na podstawie modelu konfiguracji maszyny wirtualnej, liczby wystąpień i reguł skalowania automatycznego | |
| Usuwanie maszyny wirtualnej                   | Maszyny wirtualne muszą zostać usunięte pojedynczo, zestaw skalowania nie zostanie usunięty, jeśli zawiera on maszyny wirtualne. | Maszyny wirtualne można usuwać pojedynczo, usuwając zestaw skalowania spowoduje usunięcie wszystkich wystąpień maszyn wirtualnych.  |
| Dołączanie/Odłączanie maszyn wirtualnych           | Nieobsługiwane                              | Nieobsługiwane |
| Cykl życia wystąpienia (tworzenie przy użyciu operacji usuwania) | Maszyny wirtualne i ich artefakty (takie jak dyski i karty sieciowe) mogą być zarządzane niezależnie. | Wystąpienia i ich artefakty (takie jak dyski i karty sieciowe) są niejawne w wystąpieniach zestawu skalowania, które je tworzą. Nie mogą być odłączone ani zarządzane oddzielnie poza zestawem skalowania |
| Domeny błędów               | Można zdefiniować domeny błędów. 2 lub 3 w oparciu o obsługę regionalną i 5 dla strefy dostępności. | Może definiować domeny błędów przechodzące od 1 do 5 |
| Domeny aktualizacji              | Domeny aktualizacji są automatycznie mapowane na domeny błędów | Domeny aktualizacji są automatycznie mapowane na domeny błędów |
| Strefy dostępności          | Obsługuje wdrożenie regionalne lub maszyny wirtualne w jednej strefie dostępności | Obsługuje wdrożenie regionalne lub wiele Strefy dostępności; Może definiować strategię równoważenia strefy |
| Automatycznego skalowania                   | Nieobsługiwane                              | Obsługiwane |
| Uaktualnienie systemu operacyjnego                  | Nieobsługiwane                              | Obsługiwane |
| Aktualizacje modelu               | Nieobsługiwane                              | Obsługiwane |
| Kontrolka wystąpienia            | Pełna kontrola maszyny wirtualnej. Maszyny wirtualne mają w pełni kwalifikowany identyfikator URI obsługujący pełen zakres możliwości zarządzania MASZYNami wirtualnymi platformy Azure (np. Azure Policy, Azure Backup i Azure Site Recovery). | Maszyny wirtualne są zasobami zależnymi od zestawu skalowania. Można uzyskać dostęp do wystąpień do zarządzania tylko za pomocą zestawu skalowania. |
| Model wystąpienia              | Definicja modelu Microsoft. COMPUTE/VirtualMachines. | Definicja modelu Microsoft. COMPUTE/VirtualMachineScaleSets/VirtualMachines. |
| Pojemność                    | Można utworzyć pusty zestaw skalowania; do zestawu skalowania można dodać do 200 maszyn wirtualnych | Zestawy skalowania można definiować przy użyciu liczby wystąpień 0-1000 |
| Move                        | Obsługiwane                                  | Obsługiwane |
| Pojedyncza grupa umieszczania = = false | Nieobsługiwane                          | Obsługiwane |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Omówienie opcji dostępności](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machine-scale-sets%2ftoc.json).
