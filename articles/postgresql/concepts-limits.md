---
title: Limity-Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano limity w Azure Database for PostgreSQL-pojedynczym serwerze, takie jak liczba opcji połączenia i aparatu magazynu.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 6f48245983898c542197deb7e0b3cd53bd39be33
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707527"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limity w Azure Database for PostgreSQL-pojedynczym serwerze
W poniższych sekcjach opisano pojemności i limity funkcjonalne usługi bazy danych. Jeśli chcesz dowiedzieć się więcej o warstwach zasobów (obliczeniowych, pamięci, magazynu), zobacz artykuł [warstwy cenowe](concepts-pricing-tiers.md) .


## <a name="maximum-connections"></a>Maksymalna liczba połączeń
Poniżej przedstawiono maksymalną liczbę połączeń na warstwę cenową i rdzeni wirtualnych. System Azure wymaga pięciu połączeń do monitorowania serwera Azure Database for PostgreSQL. 

|**Warstwa cenowa**| **Rdzeń wirtualny**| **Maksymalna liczba połączeń** | **Maksymalna liczba połączeń użytkowników** |
|---|---|---|---|
|Podstawowy| 1| 55 | 50|
|Podstawowy| 2| 105 | 100|
|Ogólnego przeznaczenia| 2| 150| 145|
|Ogólnego przeznaczenia| 4| 250| 245|
|Ogólnego przeznaczenia| 8| 480| 475|
|Ogólnego przeznaczenia| 16| 950| 945|
|Ogólnego przeznaczenia| 32| 1500| 1495|
|Ogólnego przeznaczenia| 64| 1900| 1895|
|Optymalizacja pod kątem pamięci| 2| 300| 295|
|Optymalizacja pod kątem pamięci| 4| 500| 495|
|Optymalizacja pod kątem pamięci| 8| 960| 955|
|Optymalizacja pod kątem pamięci| 16| 1900| 1895|
|Optymalizacja pod kątem pamięci| 32| 1987| 1982|

Gdy połączenia przekroczą limit, może zostać wyświetlony następujący błąd:
> Błąd krytyczny: Niestety, zbyt wielu klientów

> [!IMPORTANT]
> W celu uzyskania najlepszego środowiska zalecamy użycie połączenia pulę, takiego jak pgBouncer, aby efektywnie zarządzać połączeniami.

Połączenie PostgreSQL, nawet bezczynne, może zajmować około 10 MB pamięci. Ponadto tworzenie nowych połączeń trwa. Większość aplikacji żąda wielu krótkich połączeń, które są związane z tą sytuacją. Wynikiem jest mniej zasobów dostępnych dla rzeczywistego obciążenia, co prowadzi do zmniejszenia wydajności. Pulę połączenia, który zmniejsza bezczynne połączenia i ponownie używa istniejących połączeń, będzie pomóc w uniknięciu tego działania. Aby dowiedzieć się więcej, odwiedź nasz [wpis w blogu](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne
### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z podstawowych warstw cenowych nie jest obecnie obsługiwane.
- Zmniejszenie rozmiaru magazynu serwera nie jest obecnie obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatyczna Migracja między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwana. Jeśli chcesz uaktualnić do następnej wersji głównej, zrób [zrzut i Przywróć](./howto-migrate-using-dump-and-restore.md) go na serwerze, który został utworzony przy użyciu nowej wersji aparatu.

> Należy pamiętać, że przed PostgreSQL wersja 10 [zasady dotyczące wersji PostgreSQL](https://www.postgresql.org/support/versioning/) uznawane za uaktualnienie _wersji głównej_ o zwiększenie liczby pierwszej _lub_ drugiej (na przykład 9,5 do 9,6 zostało uznane za uaktualnienie wersji _głównej_ ).
> Począwszy od wersji 10, tylko zmiana pierwszego numeru jest uznawana za uaktualnienie wersji głównej (na przykład 10,0 do 10,1 jest uaktualnieniem wersji _pomocniczej_ , a 10 do 11 jest uaktualnieniem _głównym_ wersji).

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest obsługiwana tylko w przypadku serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="restoring-a-server"></a>Przywracanie serwera
- W przypadku korzystania z funkcji kopie nowy serwer jest tworzony z tymi samymi konfiguracjami warstwy cenowej co serwer, na którym jest oparta.
- Nowy serwer utworzony podczas przywracania nie ma reguł zapory istniejących na oryginalnym serwerze. Reguły zapory należy skonfigurować oddzielnie dla tego nowego serwera.
- Przywracanie usuniętego serwera nie jest obsługiwane.

### <a name="utf-8-characters-on-windows"></a>Znaki UTF-8 w systemie Windows
- W niektórych scenariuszach znaki UTF-8 nie są w pełni obsługiwane w programie Open Source PostgreSQL w systemie Windows, co ma wpływ na Azure Database for PostgreSQL. Aby uzyskać więcej informacji, zobacz wątek w [#15476 błędów w archiwum PostgreSQL](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) .

### <a name="gss-error"></a>Błąd GSS
Jeśli zobaczysz błąd związany z jednostką **GSS**, możesz korzystać z nowszej wersji klienta/sterownika, która nie jest jeszcze w pełni obsługiwana przez usługę Azure Postgres na jednym serwerze. Ten błąd jest znany, aby mieć wpływ na [JDBC wersje sterowników 42.2.15 i 42.2.16](https://github.com/pgjdbc/pgjdbc/issues/1868).
   - Planuje ukończenie aktualizacji przed końcem listopada. Rozważ użycie w międzyczasie wersji sterownika działającego.
   - Lub rozważ wyłączenie żądania GSS.  Użyj parametru połączenia, takiego jak `gssEncMode=disable` .

## <a name="next-steps"></a>Następne kroki
- Dowiedz [się, co jest dostępne w poszczególnych warstwach cenowych](concepts-pricing-tiers.md)
- Informacje o [obsługiwanych wersjach bazy danych PostgreSQL](concepts-supported-versions.md)
- Zapoznaj [się z tematem jak utworzyć kopię zapasową i przywrócić serwer w Azure Database for PostgreSQL przy użyciu Azure Portal](howto-restore-server-portal.md)
