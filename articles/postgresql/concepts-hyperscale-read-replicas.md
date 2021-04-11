---
title: Odczytaj repliki — Azure Database for PostgreSQL-ze skalowaniem (Citus)
description: W tym artykule opisano funkcję Read Replica w Azure Database for PostgreSQL-Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cf8c64bf3858f0d3b7d633b94bc7302fbe563f87
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023999"
---
# <a name="read-replicas-in-azure-database-for-postgresql---hyperscale-citus"></a>Odczytaj repliki w Azure Database for PostgreSQL-Citus

> [!IMPORTANT]
> Odczytaj repliki w funkcji Citus) są obecnie dostępne w wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
>
> Pełną listę innych nowych funkcji można znaleźć w artykule [dotyczącym funkcji Citus (wersja zapoznawcza)](hyperscale-preview-features.md).

Funkcja Odczytaj replikę umożliwia replikowanie danych z grupy serwerów z Citus (skala) do grupy serwerów tylko do odczytu. Repliki są aktualizowane **asynchronicznie** dzięki technologii replikacji fizycznej PostgreSQL. Można replikować z serwera podstawowego do nieograniczonej liczby replik.

Repliki to nowe grupy serwerów, którymi można zarządzać, podobnie jak w przypadku grup serwerów regularnego skalowania (Citus). Dla każdej repliki odczytu są naliczane opłaty za zasoby obliczeniowe rdzeni wirtualnych i magazyn w GB/miesiąc.

Dowiedz się [, jak tworzyć repliki i zarządzać nimi](howto-hyperscale-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Kiedy używać repliki odczytu

Funkcja odczytu repliki pomaga zwiększyć wydajność i skalowalność obciążeń intensywnie korzystających z odczytu. Obciążenia odczytu mogą być odizolowane dla replik, podczas gdy obciążenia zapisu mogą być kierowane do podstawowego.

Typowy scenariusz polega na tym, że obciążenia analizy biznesowej i analizy używają repliki odczytu jako źródła danych do raportowania.

Ponieważ repliki są tylko do odczytu, nie zmniejszają bezpośrednio obciążeń związanych z pojemnością zapisu w podstawowym.

### <a name="considerations"></a>Zagadnienia do rozważenia

Ta funkcja jest przeznaczona dla scenariuszy, w których opóźnienie replikacji jest akceptowalne i jest przeznaczone do odciążania zapytań. Nie jest to przeznaczone do scenariuszy replikacji synchronicznej, w przypadku których oczekiwane jest, że dane repliki są aktualne. Nastąpi wymierne opóźnienie między podstawową i repliką. Opóźnienie może być minuty lub nawet w godzinach, w zależności od obciążenia i opóźnienia między podstawową i repliką.  Dane z repliki ostatecznie staną się spójne z danymi na serwerze podstawowym. Użyj tej funkcji dla obciążeń, które mogą obsłużyć to opóźnienie. 

## <a name="create-a-replica"></a>Tworzenie repliki

Po uruchomieniu przepływu pracy tworzenia repliki zostanie utworzona pusta Grupa serwerów Citus. Nowa grupa zostanie wypełniona danymi znajdującymi się w podstawowej grupie serwerów. Czas utworzenia zależy od ilości danych w podstawowej i czasu od ostatniego cotygodniowej pełnej kopii zapasowej. Czas może się wahać od kilku minut do kilku godzin.

Funkcja Read Replica używa replikacji fizycznej PostgreSQL, a nie replikacji logicznej. Domyślnym trybem jest replikacja strumieniowa przy użyciu miejsc replikacji.
W razie potrzeby wysyłanie dziennika służy do wykrycia.

Dowiedz się [, jak utworzyć replikę odczytu w Azure Portal](howto-hyperscale-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Nawiązywanie połączenia z repliką

Podczas tworzenia repliki nie są dziedziczone reguły zapory dla podstawowej grupy serwerów. Te reguły należy skonfigurować niezależnie dla repliki.

Replika dziedziczy konto administratora ("Citus") z podstawowej grupy serwerów.
Wszystkie konta użytkowników są replikowane do replik odczytu. Można nawiązać połączenie z repliką odczytu tylko przy użyciu kont użytkowników, które są dostępne na serwerze podstawowym.

Można nawiązać połączenie z węzłem koordynatora repliki przy użyciu jego nazwy hosta i prawidłowego konta użytkownika, tak jak w przypadku grupy serwerów regularnego Citus.
W przypadku serwera o nazwie **moja replika** z nazwą administratora **Citus** można nawiązać połączenie z węzłem koordynatora repliki przy użyciu PSQL:

```bash
psql -h c.myreplica.postgres.database.azure.com -U citus@myreplica -d postgres
```

W wierszu polecenia wprowadź hasło dla konta użytkownika.

## <a name="considerations"></a>Zagadnienia do rozważenia

Ta sekcja zawiera podsumowanie zagadnień dotyczących funkcji odczytu repliki.

### <a name="new-replicas"></a>Nowe repliki

Replika odczytu jest tworzona jako Grupa serwerów nowej funkcji wieloskalowej (Citus). Nie można wykonać istniejącej grupy serwerów w replice. Nie można utworzyć repliki innej repliki odczytu.

### <a name="replica-configuration"></a>Konfiguracja repliki

Replika jest tworzona przy użyciu tych samych ustawień węzła obliczenia, magazyn i proces roboczy co podstawowy. Po utworzeniu repliki można zmienić kilka ustawień, w tym okres przechowywania magazynu i kopii zapasowych. Innych ustawień nie można zmienić w replikach, takich jak rozmiar magazynu i liczba węzłów procesu roboczego.

Pamiętaj, aby zachować duże ilości replik, aby zachować zmiany przychodzące z poziomu podstawowego. Na przykład Pamiętaj, aby przeskalować moc obliczeniową w replikach w przypadku jej skalowania na poziomie podstawowym.

Reguły zapory i ustawienia parametrów nie są dziedziczone z serwera podstawowego do repliki, gdy replika zostanie utworzona lub później.

### <a name="regions"></a>Regiony

Grupy serwerów ze skalowaniem (Citus) obsługują tylko replikację w tym samym regionie.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w Azure Portal](howto-hyperscale-read-replicas-portal.md).
