---
title: Plany i rozliczanie
description: Dowiedz się więcej o planach i rozliczeniach dla usługi Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78898475"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Plany i rozliczenia w usłudze Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która jest [wycofywana](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Aby kontynuować pracę z zadaniami skonfigurowanymi w usłudze Scheduler, [Przeprowadź migrację do Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) najszybciej, jak to możliwe. 
>
> Usługa Scheduler nie jest już dostępna w Azure Portal, ale polecenia cmdlet programu PowerShell dla [interfejsu API REST](/rest/api/scheduler) i [usługi Azure Scheduler](scheduler-powershell-reference.md) pozostają dostępne w tym momencie, aby można było zarządzać zadaniami i kolekcjami zadań.

## <a name="job-collection-plans"></a>Plany zbierania zadań

W usłudze Azure Scheduler kolekcja zadań zawiera określoną liczbę zadań. Kolekcja zadań jest płatną jednostką i zawiera plany w warstwach Standardowa, P10 Premium i P20 Premium, które zostały opisane tutaj: 

| Plan zbierania zadań | Maksymalna liczba zadań na kolekcję | Maksymalny cykl | Maksymalna liczba kolekcji zadań na subskrypcję | Limity | 
|:--- |:--- |:--- |:--- |:--- |
| **Standardowa** | 50 zadań na kolekcję | Jeden na minutę. Nie można uruchamiać zadań częściej niż jeden na minutę. | Każda subskrypcja platformy Azure może mieć do 100 kolekcji zadań w warstwie Standardowa. | Dostęp do pełnego zestawu funkcji usługi Scheduler | 
| **P10 Premium** | 50 zadań na kolekcję | Jeden na minutę. Nie można uruchamiać zadań częściej niż jeden na minutę. | Każda subskrypcja platformy Azure może mieć do 10 000 P10 Premium kolekcji zadań. Aby uzyskać więcej kolekcji, <a href="mailto:wapteams@microsoft.com">skontaktuj się z nami</a>. | Dostęp do pełnego zestawu funkcji usługi Scheduler |
| **P20 Premium** | 1000 zadań na kolekcję | Jeden na minutę. Nie można uruchamiać zadań częściej niż jeden na minutę. | Każda subskrypcja platformy Azure może mieć do 5 000 P20 Premium kolekcji zadań. Aby uzyskać więcej kolekcji, <a href="mailto:wapteams@microsoft.com">skontaktuj się z nami</a>. | Dostęp do pełnego zestawu funkcji usługi Scheduler |
|||||| 

## <a name="pricing"></a>Ceny

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Scheduler](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Plany uaktualnienia lub obniżenia poziomu

W dowolnym momencie można uaktualnić lub obniżyć plan kolekcji zadań w ramach planów w warstwach Standardowa, P10 Premium i P20 Premium.

## <a name="active-status-and-billing"></a>Aktywny stan i rozliczenia

Kolekcje zadań są zawsze aktywne, chyba że cała subskrypcja platformy Azure przejdzie do tymczasowego stanu wyłączenia z powodu problemów z rozliczeniami. Mimo że można wyłączyć wszystkie zadania w kolekcji zadań za pomocą pojedynczej operacji, ta akcja nie powoduje zmiany stanu rozliczeń kolekcji zadań, więc w kolekcji zadań *nadal* jest naliczana opłata. Puste kolekcje zadań są uznawane za aktywne i są rozliczane.

Aby upewnić się, że zbieranie zadań nie jest rozliczane, należy usunąć kolekcję zadań.

## <a name="standard-billable-units"></a>Jednostki rozliczane w warstwie Standardowa

Standardowa jednostka rozliczeniowa może mieć maksymalnie 10 kolekcji zadań w warstwie Standardowa. Ze względu na to, że standardowa kolekcja zadań może zawierać do 50 zadań na kolekcję, jedna standardowa jednostka rozliczeniowa pozwala na subskrypcję platformy Azure do 500 zadań lub do niemal 22 *mln* wykonań zadań miesięcznie. Na tej liście wyjaśniono, w jaki sposób opłaty są naliczane na podstawie różnych liczb standardowych kolekcji zadań:

* Jeśli masz od 1 do 10 kolekcji zadań w warstwie Standardowa, opłaty są naliczane za jedną standardową jednostkę rozliczeniową. 

* Jeśli masz od 11 do 20 kolekcji zadań w warstwie Standardowa, opłaty są naliczane za dwie jednostki rozliczeniowe w warstwie Standardowa. 

* Jeśli masz od 21 do 30 standardowych kolekcji zadań, opłaty są naliczane za trzy standardowe jednostki rozliczeniowe i tak dalej.

## <a name="p10-premium-billable-units"></a>Jednostki rozliczane w warstwie Premium P10

Jednostka rozliczana w P10 Premium może mieć do 10 000 P10 Premium kolekcji zadań. Ponieważ kolekcja zadań w warstwie Premium P10 może mieć do 50 zadań na kolekcję, jedna jednostka rozliczeniowa Premium P10 umożliwia subskrypcję platformy Azure do 500 000 zadań lub do niemal 22 *miliardów* wykonywania zadań miesięcznie. 

Kolekcje zadań w warstwie Premium P10 zapewniają te same możliwości co standardowe kolekcje zadań, ale oferują podział cen dla aplikacji, które wymagają wielu kolekcji zadań i zapewniają większą skalowalność. Na tej liście wyjaśniono, w jaki sposób opłaty są naliczane w oparciu o różne liczby kolekcji zadań w warstwie Premium P10:

* Jeśli masz od 1 do 10 000 kolekcji zadań w warstwie Premium, opłaty są naliczane za jedną jednostkę rozliczeniową usługi P10 Premium. 

* Jeśli masz od 10 001 do 20 000 kolekcji zadań w warstwie Premium P10, opłaty są naliczane za dwie jednostki rozliczeniowe w wersji Premium i tak dalej.

## <a name="p20-premium-billable-units"></a>Jednostki rozliczane w warstwie Premium P20

Jednostka rozliczana w P20 Premium może mieć do 5 000 P20 Premium kolekcji zadań. Ponieważ kolekcja zadań P20 Premium może mieć do 1 000 zadań dla każdej kolekcji zadań, jedna jednostka rozliczeniowa Premium P20 umożliwia subskrypcję platformy Azure do 5 000 000 zadań lub do niemal 220 *miliardów* wykonywania zadań miesięcznie.

Kolekcje zadań w warstwie Premium P20 zapewniają takie same możliwości jak kolekcje zadań w warstwie Premium P10, ale również obsługują większą liczbę zadań na kolekcję i większą łączną liczbę zadań, które są ogólne niż P10 Premium, zapewniając większą skalowalność.

## <a name="plan-comparison"></a>Porównanie planu

* Jeśli masz więcej niż 100 zbiorów zadań w warstwie Standardowa (10 standardowych jednostek rozliczeniowych), możesz uzyskać lepszą pomoc, tworząc wszystkie kolekcje zadań w planie Premium.

* Jeśli masz jedną kolekcję zadań w warstwie Standardowa i jedną kolekcję zadań w warstwie Premium, opłaty są naliczane za jedną standardową jednostkę rozliczeniową *i* jedną jednostkę rozliczeniową w warstwie Premium.

  Usługa Scheduler rachunki jest rozliczana na podstawie liczby aktywnych kolekcji zadań w warstwie Standardowa lub Premium.

## <a name="next-steps"></a>Następne kroki

* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)