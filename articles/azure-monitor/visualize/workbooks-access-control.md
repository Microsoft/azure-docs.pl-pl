---
title: Kontrola dostępu Azure Monitor skoroszytów
description: Uprość złożone raportowanie ze wstępnie skompilowanymi i niestandardowymi skoroszytami z kontrolą dostępu opartą na rolach
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 7d3bc13dc373cda510153099859cf4cd61b3dd69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710825"
---
# <a name="access-control"></a>Kontrola dostępu

Kontrola dostępu w skoroszytach obejmuje dwie rzeczy:

* Dostęp wymagany do odczytu danych w skoroszycie. Ten dostęp jest kontrolowany przez standardowe [role platformy Azure](../../role-based-access-control/overview.md) dotyczące zasobów używanych w skoroszycie. Skoroszyty nie określają ani nie konfigurują dostępu do tych zasobów. Użytkownicy zazwyczaj uzyskują ten dostęp do tych zasobów przy użyciu roli [czytelnik monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader) dla tych zasobów.

* Dostęp wymagany do zapisywania skoroszytów

    - Zapisywanie `("My")` skoroszytów prywatnych nie wymaga żadnych dodatkowych uprawnień. Wszyscy użytkownicy mogą zapisywać prywatne skoroszyty i tylko mogą widzieć te skoroszyty.
    - Zapisywanie skoroszytów udostępnionych wymaga uprawnień do zapisu w grupie zasobów, aby zapisać skoroszyt. Te uprawnienia są zwykle określane przez rolę [współautor monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-contributor) , ale można również ustawić za pomocą roli *współautor skoroszyty* .
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Role standardowe z uprawnieniami związanymi z skoroszytem

[Czytnik monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader) obejmuje standardowe uprawnienia/Read, które będą używane przez narzędzia do monitorowania (w tym skoroszyty) do odczytywania danych z zasobów.

[Współautor monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-contributor) obejmuje ogólne `/write` uprawnienia używane przez różne narzędzia do monitorowania do zapisywania elementów (w tym `workbooks/write` uprawnienia do zapisywania udostępnionych skoroszytów).
"Współautor skoroszytów" dodaje uprawnienia "skoroszyty/zapis" do obiektu w celu zapisania udostępnionych skoroszytów.
Użytkownikom nie są wymagane żadne specjalne uprawnienia do zapisywania prywatnych skoroszytów, które tylko mogą zobaczyć.

W przypadku ról niestandardowych:

Dodaj `microsoft.insights/workbooks/write` , aby zapisać udostępnione skoroszyty. Aby uzyskać więcej informacji, zobacz Rola [współautor skoroszytu](../../role-based-access-control/built-in-roles.md#monitoring-contributor) .

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](./workbooks-overview.md#visualizations) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.