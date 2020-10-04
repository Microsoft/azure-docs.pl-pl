---
title: Ciągłość działania — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano ciągłość biznesową (przywracanie do punktu w czasie, awaria centrum danych, przywracanie geograficzne, repliki) podczas korzystania z Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: 6bcb1ea6c16fd387dfb7f15f909d1908c20a44d7
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710910"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Przegląd ciągłości działania z Azure Database for PostgreSQL — pojedynczy serwer

Ten przegląd zawiera opis możliwości, które Azure Database for PostgreSQL zapewnia ciągłość działania i odzyskiwanie po awarii. Informacje o opcjach odzyskiwania zdarzeń powodujących zakłócenia, które mogą spowodować utratę danych lub spowodować, że baza danych i aplikacja staną się niedostępne. Dowiedz się, co zrobić, gdy błąd użytkownika lub aplikacji wpływa na integralność danych, region platformy Azure ma awarię lub aplikacja wymaga konserwacji.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkcje, których można użyć w celu zapewnienia ciągłości biznesowej

Podczas opracowywania planu ciągłości biznesowej należy zrozumieć maksymalny akceptowalny czas, po upływie którego aplikacja zostanie w pełni odzyskana po wystąpieniu zdarzenia zakłócenia — jest to cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalną ilość najnowszych aktualizacji danych (przedział czasu), jaką aplikacja może tolerować podczas odzyskiwania po wystąpieniu zdarzenia zakłócenia — jest to cel punktu odzyskiwania (RPO).

Azure Database for PostgreSQL zapewnia funkcje ciągłości biznesowej, które obejmują geograficznie nadmiarowe kopie zapasowe z możliwością inicjowania przywracania geograficznego oraz wdrażania replik odczytu w innym regionie. Każdy z nich ma różne cechy czasu odzyskiwania i potencjalną utratę danych. Dzięki funkcji [przywracania geograficznego](concepts-backup.md) nowy serwer jest tworzony przy użyciu danych kopii zapasowej replikowanych z innego regionu. Całkowity czas przywracania i odzyskiwania zależy od rozmiaru bazy danych i ilości dzienników do odzyskania. Całkowity czas ustanowienia serwera różni się od kilku minut do kilku godzin. W przypadku [replik odczytu](concepts-read-replicas.md)dzienniki transakcji z podstawowego programu są przesyłane strumieniowo do repliki. Opóźnienie między podstawową i repliką zależy od opóźnienia między lokacjami, a także ilości danych do przesłania. W przypadku awarii lokacji głównej, takiej jak błąd strefy dostępności, podwyższanie poziomu repliki zapewnia krótszą RTO i zmniejsza utratę danych. 

W poniższej tabeli porównano RTO i cel punktu odzyskiwania w typowym scenariuszu:

| **Funkcja** | **Podstawowa** | **Ogólnego przeznaczenia** | **Optymalizacja pod kątem pamięci** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Przywracanie do punktu w czasie z kopii zapasowej | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania | Dowolny punkt przywracania w okresie przechowywania |
| Przywracanie geograficzne z kopii zapasowych replikowanych geograficznie | Nieobsługiwane | RTO — różne <br/>Cel punktu odzyskiwania < 1 h | RTO — różne <br/>Cel punktu odzyskiwania < 1 h |
| Repliki do odczytu | RTO — minuty <br/>Cel punktu odzyskiwania < 5 min | RTO — minuty <br/>Cel punktu odzyskiwania < 5 min| RTO — minuty <br/>Cel punktu odzyskiwania < 5 min|

> [!IMPORTANT]
> Oczekiwane RTO i cel punktu odzyskiwania są wyłącznie do celów referencyjnych. Dla tych metryk nie są oferowane żadne umowy SLA.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Odzyskiwanie serwera po błędzie użytkownika lub aplikacji

Można użyć kopii zapasowych usługi do odzyskania serwera z różnych zdarzeń zakłócających działanie. Użytkownik może przypadkowo usunąć niektóre dane, przypadkowo porzucić ważną tabelę lub nawet porzucić całą bazę danych. Aplikacja może przypadkowo zastąpić dobre dane nieprawidłowymi danymi z powodu wady aplikacji i tak dalej.

Można wykonać przywracanie do **punktu w czasie** , aby utworzyć kopię serwera do znanego dobrego punktu w czasie. Ten punkt w czasie musi przypadać w okresie przechowywania kopii zapasowej skonfigurowanym dla serwera. Po przywróceniu danych na nowy serwer można zastąpić oryginalny serwer nowym przywróconym serwerem lub skopiować potrzebne dane z przywróconego serwera na oryginalny serwer.

> [!IMPORTANT]
> **Nie** można przywrócić usuniętych serwerów. Usunięcie serwera spowoduje również usunięcie wszystkich baz danych należących do serwera, których nie można odzyskać. Użyj [blokady zasobów platformy Azure](../azure-resource-manager/management/lock-resources.md) , aby zapobiec przypadkowemu usunięciu serwera.

## <a name="recover-from-an-azure-data-center-outage"></a>Odzyskaj sprawność po awarii centrum danych platformy Azure

Sporadycznie centrum danych platformy Azure może mieć awarię. Gdy wystąpi awaria, powoduje to zakłócenia działania firmy, które może trwać tylko kilka minut, ale może trwać w godzinach.

Jedną z opcji jest poczekanie, aż serwer powróci do trybu online po zakończeniu awarii centrum danych. Działa to w przypadku aplikacji, które mogą zapewnić, że serwer jest w trybie offline przez pewien czas, na przykład w środowisku programistycznym. Gdy centrum danych jest w stanie awarii, nie wiesz, jak długo może trwać awaria, dlatego ta opcja działa tylko wtedy, gdy serwer nie jest potrzebny przez pewien czas.

## <a name="geo-restore"></a>Przywracanie geograficzne

Funkcja przywracania geograficznego przywraca serwer przy użyciu geograficznie nadmiarowych kopii zapasowych. Kopie zapasowe są hostowane w [sparowanym regionie](../best-practices-availability-paired-regions.md)serwera. Można przywrócić z tych kopii zapasowych do dowolnego innego regionu. Przywracanie geograficzne tworzy nowy serwer z danymi z kopii zapasowych. Dowiedz się więcej o przywracaniu geograficznym z [artykułu dotyczącego tworzenia kopii zapasowych i przywracania](concepts-backup.md).

> [!IMPORTANT]
> Przywracanie geograficzne jest możliwe tylko w przypadku aprowizacji serwera z magazynem kopii zapasowych nadmiarowego. Aby przełączyć się z lokalnie nadmiarowego do geograficznie nadmiarowych kopii zapasowych dla istniejącego serwera, należy wykonać zrzut przy użyciu pg_dump istniejącego serwera i przywrócić go na nowo utworzonym serwerze skonfigurowanym z użyciem geograficznie nadmiarowych kopii zapasowych.

## <a name="cross-region-read-replicas"></a>Repliki odczytu między regionami
Za pomocą replik odczytu między regionami można usprawnić planowanie ciągłości działania i odzyskiwania po awarii. Repliki odczytu są aktualizowane asynchronicznie za pomocą technologii replikacji fizycznej PostgreSQL. Dowiedz się więcej na temat odczytywania replik, dostępnych regionów i sposobu przełączenia w tryb failover z [artykułu pojęć dotyczących replik](concepts-read-replicas.md). 

## <a name="faq"></a>Często zadawane pytania
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Gdzie usługa Azure Database for PostgreSQL przechowywać dane klienta?
Domyślnie Azure Database for PostgreSQL nie przenosi ani nie zapisuje danych klienta poza regionem, w którym jest wdrożony. Jednak klienci mogą opcjonalnie wybrać, aby włączyć [geograficznie nadmiarowe kopie zapasowe](concepts-backup.md#backup-redundancy-options) lub utworzyć [replikę odczytu między regionami](concepts-read-replicas.md#cross-region-replication) do przechowywania danych w innym regionie.


## <a name="next-steps"></a>Następne kroki
- Więcej informacji na temat [zautomatyzowanych kopii zapasowych znajduje się w Azure Database for PostgreSQL](concepts-backup.md). 
- Dowiedz się, jak przywrócić za pomocą [Azure Portal](howto-restore-server-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-restore-server-cli.md).
- Dowiedz się więcej [na temat odczytywania replik w Azure Database for PostgreSQL](concepts-read-replicas.md).
