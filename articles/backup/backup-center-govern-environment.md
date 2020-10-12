---
title: Zapewnianie ładu w zakresie kopii zapasowych przy użyciu centrum kopii zapasowych
description: Dowiedz się, jak zarządzać środowiskiem platformy Azure, aby upewnić się, że wszystkie zasoby są zgodne z perspektywą tworzenia kopii zapasowych z centrum kopii zapasowych.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 8a16769be827bf34e5be82409d5b8eb014cdcf1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614345"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Zapewnianie ładu w zakresie kopii zapasowych przy użyciu centrum kopii zapasowych

Centrum kopii zapasowych pomaga zarządzać środowiskiem platformy Azure, aby upewnić się, że wszystkie zasoby są zgodne z perspektywą tworzenia kopii zapasowych. Poniżej wymieniono niektóre funkcje ładu centrum kopii zapasowych:

* Wyświetlanie i przypisywanie zasad platformy Azure na potrzeby tworzenia kopii zapasowych

* Sprawdź zgodność zasobów ze wszystkimi wbudowanymi zasadami platformy Azure na potrzeby tworzenia kopii zapasowych.

* Wyświetl wszystkie źródła danych, które nie zostały skonfigurowane do tworzenia kopii zapasowej.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

* Zapoznaj się z [matrycą pomocy technicznej](backup-center-support-matrix.md) , aby uzyskać szczegółową listę obsługiwanych i nieobsługiwanych scenariuszy.

## <a name="azure-policies-for-backup"></a>Zasady platformy Azure dla kopii zapasowej

Aby wyświetlić wszystkie [Zasady platformy Azure](https://docs.microsoft.com/azure/governance/policy/overview) , które są dostępne dla kopii zapasowej, wybierz element menu **Zasady platformy Azure dla kopii zapasowej** . Spowoduje to wyświetlenie wszystkich wbudowanych i niestandardowych [definicji zasad platformy Azure na potrzeby tworzenia kopii zapasowych](policy-reference.md) dostępnych do przypisania do subskrypcji i grup zasobów.

Wybranie dowolnej definicji umożliwia [przypisanie zasad](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage#assign-a-policy) do zakresu.

![Wybierz definicje Azure Policy](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>Zgodność kopii zapasowych

Kliknięcie elementu menu zgodność kopii zapasowej ułatwia wyświetlanie [zgodności](https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data) zasobów zgodnie z różnymi wbudowanymi zasadami, które zostały przypisane do środowiska platformy Azure. Można wyświetlić procent zasobów, które są zgodne ze wszystkimi zasadami, a także zasady, które mają co najmniej jeden niezgodny zasób.

![Wyświetl zgodność kopii zapasowych](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>Chronione źródła danych

Wybranie elementu menu możliwe do **ochrony źródła** danych umożliwia wyświetlenie wszystkich źródeł danych, które nie zostały skonfigurowane do tworzenia kopii zapasowej. Można filtrować listę według subskrypcji DataSource, grupy zasobów, lokalizacji, typu i tagów. Po zidentyfikowaniu źródła danych, którego kopię zapasową należy wykonać, można kliknąć prawym przyciskiem myszy odpowiedni element siatki i wybrać pozycję **kopia zapasowa** , aby skonfigurować kopię zapasową zasobu.

![Menu z chronionymi źródłami danych](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie i wykonywanie kopii zapasowych](backup-center-monitor-operate.md)
* [Wykonywanie akcji przy użyciu centrum kopii zapasowych](backup-center-actions.md)
* [Uzyskaj wgląd w kopie zapasowe](backup-center-obtain-insights.md)
