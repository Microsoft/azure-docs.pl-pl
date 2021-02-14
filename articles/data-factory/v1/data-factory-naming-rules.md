---
title: Reguły nazewnictwa Azure Data Factory jednostek — wersja 1
description: Opisuje reguły nazewnictwa dla jednostek Data Factory v1.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 83621a7ceeae32ea4b55e3f22fff61d50e8cdb60
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380172"
---
# <a name="rules-for-naming-azure-data-factory-entities"></a>Reguły nazewnictwa Azure Data Factory jednostek
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [reguły nazewnictwa w programie Data Factory](../naming-rules.md).

W poniższej tabeli przedstawiono reguły nazewnictwa Data Factory artefaktów.

| Nazwa | Unikatowość nazw | Sprawdzanie poprawności |
|:--- |:--- |:--- |
| Data Factory |Unikatowy dla Microsoft Azure. W nazwach nie jest rozróżniana wielkość liter, oznacza to, że `MyDF` i `mydf` zapoznaj się z tą samą fabryką danych. |<ul><li>Każda Fabryka danych jest powiązana z dokładnie jedną subskrypcją platformy Azure.</li><li>Nazwy obiektów muszą zaczynać się literą lub cyfrą i mogą zawierać tylko litery, cyfry i znak kreski (-).</li><li>Każdy znak kreski (-) musi być bezpośrednio poprzedzony literą lub cyfrą. Kolejne kreski są niedozwolone w nazwach kontenerów.</li><li>Nazwa może składać się z 3-63 znaków.</li></ul> |
| Połączone usługi/tabele/potoki |Unikatowy w usłudze Fabryka danych. W nazwach nie jest rozróżniana wielkość liter. |<ul><li>Maksymalna liczba znaków w nazwie tabeli: 260.</li><li>Nazwy obiektów muszą zaczynać się literą, cyfrą lub znakiem podkreślenia (_).</li><li>Następujące znaki nie są dozwolone: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li></ul> |
| Grupa zasobów |Unikatowy dla Microsoft Azure. W nazwach nie jest rozróżniana wielkość liter. |<ul><li>Maksymalna liczba znaków: 1000.</li><li>Nazwa może zawierać litery, cyfry i następujące znaki: "-", "_", "," i "."</li></ul> |

