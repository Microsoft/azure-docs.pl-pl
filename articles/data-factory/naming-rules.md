---
title: Reguły nazewnictwa Azure Data Factory jednostek
description: Opisuje reguły nazewnictwa dla jednostek Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: bef8706d9dcef966397dcddc64ffbd567a8431f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034408"
---
# <a name="azure-data-factory---naming-rules"></a>Reguły nazewnictwa Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W poniższej tabeli przedstawiono reguły nazewnictwa Data Factory artefaktów.

| Nazwa | Unikatowość nazw | Sprawdzanie poprawności |
|:--- |:--- |:--- |
| Fabryka danych | Unikatowy dla Microsoft Azure. W nazwach nie jest rozróżniana wielkość liter, oznacza to, że `MyDF` i `mydf` zapoznaj się z tą samą fabryką danych. |<ul><li>Każda Fabryka danych jest powiązana z dokładnie jedną subskrypcją platformy Azure.</li><li>Nazwy obiektów muszą zaczynać się literą lub cyfrą i mogą zawierać tylko litery, cyfry i znak kreski (-).</li><li>Każdy znak kreski (-) musi być bezpośrednio poprzedzony literą lub cyfrą. Kolejne kreski są niedozwolone w nazwach kontenerów.</li><li>Nazwa może składać się z 3-63 znaków.</li></ul> |
| Połączone usługi/zestawy danych/potoki/przepływy | Unikatowa w ramach fabryki danych. W nazwach nie jest rozróżniana wielkość liter. |<ul><li>Nazwy obiektów muszą zaczynać się literą.</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li><li>Łączniki ("-") nie są dozwolone w nazwach połączonych usług, przepływach danych i zestawach.</li></ul>  |
| Środowisko Integration Runtime |Unikatowa w ramach fabryki danych. W nazwach nie jest rozróżniana wielkość liter. |<ul><li>Nazwa środowiska Integration Runtime może zawierać tylko litery, cyfry i znak kreski (-).</li><li>Pierwszy i ostatni znak musi być literą lub cyfrą. Każdy znak kreski (-) musi być bezpośrednio poprzedzony literą lub cyfrą.</li><li>Kolejne kreski nie są dozwolone w nazwie Integration Runtime. </li></ul> |
| Przekształcenia przepływu danych | Unikatowa w przepływie danych. W nazwach nie jest rozróżniana wielkość liter | <ul><li>Nazwy transformacji przepływu danych mogą zawierać tylko litery i cyfry</li><li>Pierwszy znak musi być literą. </li></ul> |
| Grupa zasobów |Unikatowy dla Microsoft Azure. W nazwach nie jest rozróżniana wielkość liter. | Aby uzyskać więcej informacji, zobacz [reguły i ograniczenia nazewnictwa platformy Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |
| Zmienna & parametrów potoku  |Unikatowa w potoku. W nazwach nie jest rozróżniana wielkość liter. | <ul><li>Sprawdzanie poprawności nazw parametrów i nazw zmiennych jest ograniczone do unikatowości ze względu na zgodność z poprzednimi wersjami.</li><li>W przypadku używania parametrów lub zmiennych do odwoływania się do nazw jednostek, na przykład połączona usługa, stosowane są reguły nazewnictwa jednostek.</li><li>Dobrym sposobem jest przestrzeganie reguł nazewnictwa transformacji przepływu danych w celu nazywania parametrów i zmiennych potoku.</li></ul> |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak utworzyć fabrykę danych, wykonując instrukcje krok po kroku w [przewodniku szybki start: Tworzenie artykułu dla fabryki danych](quickstart-create-data-factory-powershell.md) . 
