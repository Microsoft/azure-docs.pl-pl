---
title: Limity-Azure Database for PostgreSQL-elastyczny serwer
description: W tym artykule opisano limity na serwerze elastycznym Azure Database for PostgreSQL, takie jak liczba opcji połączenia i aparatu magazynu.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 30c2da4ac750375c66b92cdca552e1a51a8dbc40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940399"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Limity na serwerze elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

W poniższych sekcjach opisano pojemności i limity funkcjonalne usługi bazy danych. Jeśli chcesz dowiedzieć się więcej o warstwach zasobów (obliczeniowych, pamięci, magazynu), zobacz artykuł dotyczący [obliczeń i magazynu](concepts-compute-storage.md) .

## <a name="maximum-connections"></a>Maksymalna liczba połączeń

Poniżej przedstawiono maksymalną liczbę połączeń na warstwę cenową i rdzeni wirtualnych. System Azure wymaga trzech połączeń do monitorowania serwera Azure Database for PostgreSQL-elastycznie.

| Nazwa jednostki SKU             | Rdzeni wirtualnych | Rozmiar pamięci | Maksymalna liczba połączeń | Maksymalna liczba połączeń użytkowników |
|----------------------|--------|-------------|-----------------|----------------------|
| **Z możliwością przenoszenia**        |        |             |                 |                      |
| B1MS                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **Ogólnego przeznaczenia**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32 GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64 GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192 GiB     | 5000            | 4997                 |
| D64s_v3              | 64     | 256 GiB     | 5000            | 4997                 |
| **Zoptymalizowane pod kątem pamięci** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32 GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64 GiB      | 5000            | 4997                 |
| E16s_v3              | 16     | 128 GiB     | 5000            | 4997                 |
| E32s_v3              | 32     | 256 GiB     | 5000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5000            | 4997                 |

Gdy połączenia przekroczą limit, może zostać wyświetlony następujący błąd:
> Błąd krytyczny: Niestety, zbyt wielu klientów

> [!IMPORTANT]
> W celu uzyskania najlepszego środowiska zalecamy użycie połączenia pulę, takiego jak PgBouncer, aby efektywnie zarządzać połączeniami.

Połączenie PostgreSQL, nawet bezczynne, może zajmować około 10 MB pamięci. Ponadto tworzenie nowych połączeń trwa. Większość aplikacji żąda wielu krótkich połączeń, które są związane z tą sytuacją. Wynikiem jest mniej zasobów dostępnych dla rzeczywistego obciążenia, co prowadzi do zmniejszenia wydajności. Pule połączeń mogą służyć do zmniejszania bezczynnych połączeń i ponownego użycia istniejących połączeń. Aby dowiedzieć się więcej, odwiedź nasz [wpis w blogu](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne

### <a name="scale-operations"></a>Operacje skalowania

- Skalowanie magazynu serwera wymaga ponownego uruchomienia serwera.
- Magazyn serwera można skalować tylko w większych przyrostach, zobacz temat [Obliczanie i magazynowanie](concepts-compute-storage.md) , aby uzyskać szczegółowe informacje.
- Zmniejszenie rozmiaru magazynu serwera nie jest obecnie obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera

- Automatyczna Migracja między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwana. Jeśli chcesz uaktualnić do następnej wersji głównej, zrób [zrzut i Przywróć](../howto-migrate-using-dump-and-restore.md) go na serwerze, który został utworzony przy użyciu nowej wersji aparatu.

### <a name="networking"></a>Networking

- Przechodzenie do i z sieci wirtualnej nie jest obecnie obsługiwane.
- Łączenie publicznego dostępu przy użyciu wdrożenia w sieci wirtualnej nie jest obecnie obsługiwane.
- Reguły zapory nie są obsługiwane w sieci wirtualnej, zamiast tego można użyć sieciowych grup zabezpieczeń.
- Serwery baz danych dostępu publicznego mogą łączyć się z publiczną siecią Internet, na przykład przez `postgres_fdw` program, a dostęp tego nie jest ograniczony. Serwery oparte na sieci wirtualnej mogą mieć ograniczony dostęp wychodzący przy użyciu sieciowych grup zabezpieczeń.

### <a name="high-availability"></a>Wysoka dostępność

- Zone-Redundant HA nie jest obecnie obsługiwana w przypadku serwerów z możliwością naszeregowania.
- Adres IP serwera bazy danych zmienia się, gdy serwer przejdzie w tryb failover w trybie rezerwy HA. Upewnij się, że używasz rekordu DNS zamiast adresu IP serwera.

### <a name="availability-zones"></a>Strefy dostępności

- Ręczne przeniesienie serwerów do innej strefy dostępności nie jest obecnie obsługiwane.
- Nie można ręcznie skonfigurować strefy dostępności serwera rezerwowego o wysokiej dostępności.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Aparat Postgres, rozszerzenia i PgBouncer

- Postgres 10 i starsze nie są obsługiwane. Zalecamy użycie opcji [pojedynczego serwera](../overview-single-server.md) , jeśli wymagane są starsze wersje programu Postgres.
- Obsługa rozszerzeń jest obecnie ograniczona do rozszerzeń Postgres `contrib` .
- Wbudowane PgBouncer Connection pulę nie jest obecnie dostępne dla serwerów baz danych w sieci wirtualnej ani dla serwerów z możliwością przetworzenia.

### <a name="stopstart-operation"></a>Operacja zatrzymywania/uruchamiania

- Nie można zatrzymać serwera przez więcej niż siedem dni.

### <a name="scheduled-maintenance"></a>Konserwacja zaplanowana

- Zmiana okna obsługi jest mniejsza niż pięć dni przed zaplanowanym uaktualnieniem. Zmiany zaczną obowiązywać dopiero po następnej zaplanowanej konserwacji.

### <a name="backing-up-a-server"></a>Tworzenie kopii zapasowej serwera

- Kopie zapasowe są zarządzane przez system, obecnie nie ma możliwości ręcznego uruchamiania tych kopii zapasowych. Zalecamy użycie `pg_dump` zamiast tego.
- Kopie zapasowe są zawsze tworzone na podstawie migawek i pełnych kopii zapasowych. Należy pamiętać, że dzienniki transakcji (zapis z wyprzedzeniem — WAL) są oddzielone od pełnych/różnicowych kopii zapasowych i są archiwizowane w sposób ciągły.

### <a name="restoring-a-server"></a>Przywracanie serwera

- W przypadku korzystania z funkcji Przywracanie do momentu nowy serwer jest tworzony z tymi samymi konfiguracjami obliczeniowymi i magazynem co serwer, na którym bazuje.
- Serwery baz danych opartych na sieci wirtualnej są przywracane do tej samej sieci wirtualnej podczas przywracania z kopii zapasowej.
- Nowy serwer utworzony podczas przywracania nie ma reguł zapory istniejących na oryginalnym serwerze. Reguły zapory należy tworzyć oddzielnie dla nowego serwera.
- Przywracanie usuniętego serwera nie jest obsługiwane.
- Przywracanie między regionami nie jest obsługiwane.

### <a name="other-features"></a>Pozostałe funkcje

* Uwierzytelnianie usługi Azure AD nie jest jeszcze obsługiwane. Zaleca się użycie opcji [pojedynczego serwera](../overview-single-server.md) , jeśli wymagane jest uwierzytelnianie usługi Azure AD.
* Repliki odczytu nie są jeszcze obsługiwane. Zaleca się użycie opcji [pojedynczego serwera](../overview-single-server.md) , jeśli wymagane jest odczytanie replik.


## <a name="next-steps"></a>Następne kroki

- [Informacje o dostępnych opcjach obliczeniowych i pamięci masowej](concepts-compute-storage.md)
- Informacje o [obsługiwanych wersjach bazy danych PostgreSQL](concepts-supported-versions.md)
- Zapoznaj [się z tematem jak utworzyć kopię zapasową i przywrócić serwer w Azure Database for PostgreSQL przy użyciu Azure Portal](how-to-restore-server-portal.md)
