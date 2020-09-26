---
title: Wysoka dostępność — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Pojęcia dotyczące wysokiej dostępności i odzyskiwania po awarii
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 406b4f2ada665d65ee0452579e24744d1f7cfc63
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314857"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Wysoka dostępność w Azure Database for PostgreSQL — skalowanie (Citus)

Wysoka dostępność (HA) pozwala uniknąć przestojów bazy danych przez utrzymywanie replik w trybie rezerwy dla każdego węzła w grupie serwerów. Jeśli węzeł ulegnie awarii, funkcja wieloskalowania (Citus) przełącza połączenia przychodzące z węzła zakończonego niepowodzeniem do stanu wstrzymania. Tryb failover występuje w ciągu kilku minut, a podwyższone węzły zawsze mają nowe dane za pomocą synchronicznej replikacji przesyłania strumieniowego PostgreSQL.

Aby korzystać z wysokiej dostępności na węźle koordynatora, aplikacje bazy danych muszą wykrywać i ponawiać próby porzucenia połączeń i transakcji zakończonych niepowodzeniem. Nowo podwyższony koordynator będzie dostępny z tymi samymi parametrami połączenia.

Odzyskiwanie można podzielić na trzy etapy: wykrywanie, przełączanie do trybu failover i pełne odzyskiwanie.  Funkcja przeskalowania (Citus) uruchamia okresowe kontrole kondycji każdego węzła i po czterech nieudanych sprawdzeniach, że węzeł nie działa. Funkcja przeskalowania (Citus) następnie promuje stan wstrzymania do stanu węzła podstawowego (tryb failover) i inicjuje nową gotowość do wykonania.
Rozpocznie się replikacja przesyłania strumieniowego, co spowoduje, że nowy węzeł jest aktualny.  Gdy wszystkie dane zostały zreplikowane, węzeł osiągnął pełne odzyskiwanie.

### <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [włączyć wysoką dostępność](howto-hyperscale-high-availability.md) w grupie serwerów Citus.
