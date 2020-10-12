---
title: Zmiana powiadomienia inteligentnego wykrywania — Application Insights platformy Azure
description: Zmień domyślnych odbiorców powiadomień z wykrywania inteligentnego. Wykrywanie inteligentne umożliwia monitorowanie śladów aplikacji za pomocą usługi Azure Application Insights w przypadku nietypowych wzorców w telemetrii śledzenia.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8e2bf4e451ebc3c9ebba2c01dae6703fc79aa606
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87324135"
---
# <a name="smart-detection-e-mail-notification-change"></a>Zmiana powiadomień e-mail dotyczących wykrywania inteligentnego

W oparciu o opinie klientów 1 kwietnia 2019 zmieniamy domyślne role, które odbierają powiadomienia e-mail od wykrywania inteligentnego.

## <a name="what-is-changing"></a>Co się zmieni?

Obecnie powiadomienia e-mail dotyczące wykrywania inteligentnego są domyślnie wysyłane do ról _właściciel subskrypcji_, _współautor subskrypcji_i _czytelnik subskrypcji_ . Te role często obejmują użytkowników, którzy nie aktywnie uczestniczą w monitorowaniu, co sprawia, że wielu z tych użytkowników może niepotrzebnie otrzymywać powiadomienia. Aby ulepszyć to środowisko, wprowadzamy zmianę, dzięki czemu powiadomienia e-mail są domyślnie wysyłane tylko do [czytnika monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader) i [kontrolowania ról współautor](../../role-based-access-control/built-in-roles.md#monitoring-contributor) .

## <a name="scope-of-this-change"></a>Zakres tej zmiany

Ta zmiana wpłynie na wszystkie reguły wykrywania inteligentnego, z wyłączeniem następujących elementów:

* Reguły inteligentnego wykrywania oznaczone jako wersja zapoznawcza. Te reguły inteligentnego wykrywania nie obsługują obecnie powiadomień e-mail.

* Reguła anomalii dotyczących niepowodzeń. Ta zasada zacznie korzystać z nowych ról domyślnych po migracji z klasycznego alertu do platformy ujednoliconych alertów (więcej informacji jest dostępnych [tutaj](../platform/monitoring-classic-retirement.md)).

## <a name="how-to-prepare-for-this-change"></a>Jak przygotować się do tej zmiany?

Aby zapewnić, że powiadomienia e-mail z wykrywania inteligentnego są wysyłane do odpowiednich użytkowników, użytkownicy muszą być przypisani do [czytnika monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader) lub ról [współautor monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-contributor) subskrypcji.

Aby przypisać użytkowników do czytnika monitorowania lub monitorować role współautor za pośrednictwem Azure Portal, wykonaj kroki opisane w artykule [Dodawanie przypisania roli](../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) . Upewnij się, że jako rolę, do której użytkownicy są przypisani, ma zostać wybrany _czytnik monitorowania_ lub _współautor monitorowania_ .

> [!NOTE]
> Ta zmiana nie wpłynie na określonych odbiorców powiadomień o inteligentnym wykryciu skonfigurowanych przy użyciu opcji _dodatkowe Adresaci poczty e-mail_ w ustawieniach reguły. Ci adresaci będą nadal otrzymywać powiadomienia e-mail.

Jeśli masz jakieś pytania lub wątpliwości dotyczące tej zmiany, nie niechętnie zezwalają się z [nami](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wykrywania inteligentnego:

- [Anomalie błędów](./proactive-failure-diagnostics.md)
- [Przecieki pamięci](./proactive-potential-memory-leak.md)
- [Anomalie wydajności](./proactive-performance-diagnostics.md)

