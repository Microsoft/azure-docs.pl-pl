---
title: Kontrola dostępu Azure Monitor skoroszytów
description: Uprość złożone raportowanie ze wstępnie skompilowanymi i niestandardowymi skoroszytami z kontrolą dostępu opartą na rolach
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658425"
---
# <a name="access-control"></a>Kontrola dostępu

Kontrola dostępu w skoroszytach obejmuje dwie rzeczy:

* Dostęp wymagany do odczytu danych w skoroszycie. Ten dostęp jest kontrolowany przez standardowe [role platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) dotyczące zasobów używanych w skoroszycie. Skoroszyty nie określają ani nie konfigurują dostępu do tych zasobów. Użytkownicy zazwyczaj uzyskują ten dostęp do tych zasobów przy użyciu roli [czytelnik monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) dla tych zasobów.

* Dostęp wymagany do zapisywania skoroszytów

    - Zapisywanie `("My")` skoroszytów prywatnych nie wymaga żadnych dodatkowych uprawnień. Wszyscy użytkownicy mogą zapisywać prywatne skoroszyty i tylko mogą widzieć te skoroszyty.
    - Zapisywanie skoroszytów udostępnionych wymaga uprawnień do zapisu w grupie zasobów, aby zapisać skoroszyt. Te uprawnienia są zwykle określane przez rolę [współautor monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) , ale można również ustawić za pomocą roli *współautor skoroszyty* .
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Role standardowe z uprawnieniami związanymi z skoroszytem

[Czytnik monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) obejmuje standardowe uprawnienia/Read, które będą używane przez narzędzia do monitorowania (w tym skoroszyty) do odczytywania danych z zasobów.

[Współautor monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) obejmuje ogólne `/write` uprawnienia używane przez różne narzędzia do monitorowania do zapisywania elementów (w tym `workbooks/write` uprawnienia do zapisywania udostępnionych skoroszytów).
"Współautor skoroszytów" dodaje uprawnienia "skoroszyty/zapis" do obiektu w celu zapisania udostępnionych skoroszytów.
Użytkownikom nie są wymagane żadne specjalne uprawnienia do zapisywania prywatnych skoroszytów, które tylko mogą zobaczyć.

W przypadku niestandardowej kontroli dostępu opartej na rolach:

Dodaj `microsoft.insights/workbooks/write` , aby zapisać udostępnione skoroszyty. Aby uzyskać więcej informacji, zobacz Rola [współautor skoroszytu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) .

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](workbooks-visualizations.md) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.
