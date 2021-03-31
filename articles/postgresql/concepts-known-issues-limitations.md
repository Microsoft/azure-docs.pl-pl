---
title: Znane problemy i ograniczenia — Azure Database for PostgreSQL — jeden serwer i elastyczny serwer (wersja zapoznawcza)
description: Wyświetla listę znanych problemów, o których powinni wiedzieć klienci.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100106429"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL — znane problemy i ograniczenia

Ta strona zawiera listę znanych problemów w Azure Database for PostgreSQL, które mogą mieć wpływ na aplikację. Przedstawiono w nim również wszelkie środki zaradcze i zalecenia dotyczące obejścia problemu.

## <a name="intelligent-performance---query-store"></a>Inteligentna wydajność — magazyn zapytań

Dotyczy Azure Database for PostgreSQL-jednego serwera.

| Stosowane | Przyczyna | Korekty|
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | Włączenie parametru Server `pg_qs.replace_parameter_placeholders` może prowadzić do zamknięcia serwera w niektórych rzadkich scenariuszach. | W witrynie Azure Portal, w sekcji parametry serwera Zmień `pg_qs.replace_parameter_placeholders` wartość parametru na `OFF` i Zapisz.   | 

## <a name="server-parameters"></a>Parametry serwera

Dotyczy Azure Database for PostgreSQL-pojedynczego serwera i serwera elastycznego

| Stosowane | Przyczyna | Korekty| 
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | Zmiana parametru serwera `max_locks_per_transaction` na wyższą wartość niż [zalecana](https://www.postgresql.org/docs/11/kernel-resources.html) może spowodować, że serwer nie może zostać uruchomiony po ponownym uruchomieniu. | Pozostaw wartość domyślną (32 lub 64) lub przejdź do odpowiedniej wartości na potrzeby [dokumentacji](https://www.postgresql.org/docs/11/kernel-resources.html)PostgreSQL. <br> <br> Po stronie usługi ta czynność jest włączona, aby ograniczyć wysoką wartość na podstawie jednostki SKU.  | 

## <a name="next-steps"></a>Następne kroki
- Zobacz [najlepsze rozwiązania](./concepts-query-store-best-practices.md) dotyczące magazynu zapytań
