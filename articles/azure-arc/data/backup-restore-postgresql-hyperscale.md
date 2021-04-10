---
title: Tworzenie kopii zapasowej i przywracanie grup serwerów usługi Azure Database for PostgreSQL — hiperskala
description: Tworzenie kopii zapasowej i przywracanie grup serwerów usługi Azure Database for PostgreSQL — hiperskala
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686703"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Tworzenie kopii zapasowej i przywracanie grup serwerów PostgreSQL z funkcją Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Po utworzeniu kopii zapasowej lub przywróceniu grupy serwerów z włączoną funkcją Azure Arc PostgreSQL w celu utworzenia kopii zapasowej i/lub przywrócenia całego zestawu baz danych we wszystkich węzłach PostgreSQL grupy serwerów.

## <a name="take-a-manual-full-backup"></a>Ręczna pełna kopia zapasowa

Aby wykonać pełną kopię zapasową wszystkich folderów danych i dzienników grupy serwerów, uruchom następujące polecenie:
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Gdzie:
- __Nazwa__ wskazuje nazwę kopii zapasowej
- __Nazwa serwera__ wskazuje grupę serwerów
- __nie — oczekiwanie__ wskazuje, że wiersz polecenia nie czeka na ukończenie tworzenia kopii zapasowej, aby móc kontynuować korzystanie z tego okna wiersza polecenia

To polecenie służy do koordynowania rozproszonej pełnej kopii zapasowej we wszystkich węzłach, które stanowią grupę serwerów z funkcją Azure Arc PostgreSQL. Innymi słowy, będzie tworzyć kopie zapasowe wszystkich danych w koordynatorze i węzłach procesu roboczego.

Na przykład:

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

Po zakończeniu wykonywania kopii zapasowej zostaną zwrócone identyfikatory, nazwy, rozmiar, stan i sygnatura czasowa kopii zapasowej. Na przykład:
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` wskazuje strefę czasową dla czasu utworzenia kopii zapasowej. W tym przykładzie "+ 00:00" oznacza czas UTC (UTC + 00 godz. 00 minut).

> [!NOTE]
> Nie jest to jeszcze możliwe:
> - Zaplanuj automatyczne kopie zapasowe
> - Pokaż postęp tworzenia kopii zapasowej podczas jej wykonywania

## <a name="list-backups"></a>Wyświetlenie listy kopii zapasowych

Aby wyświetlić listę kopii zapasowych, które są dostępne do przywrócenia, uruchom następujące polecenie:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Na przykład:

```console
azdata arc postgres backup list --server-name postgres01
```

Zwraca dane wyjściowe podobne do:

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

Kolumna timestamp wskazuje punkt w czasie UTC, w którym wykonano kopię zapasową.

## <a name="restore-a-backup"></a>Przywracanie kopii zapasowej
W tej sekcji pokazano, jak wykonać pełne przywracanie lub przywracanie do punktu w czasie. Podczas przywracania pełnej kopii zapasowej, przywracana jest cała zawartość kopii zapasowej. W przypadku przywracania do punktu w czasie należy przywrócić do punktu w czasie. Wszystkie transakcje, które zostały wykonane później niż ten punkt w czasie, nie są przywracane.

### <a name="restore-a-full-backup"></a>Przywróć pełną kopię zapasową
Aby przywrócić całą zawartość kopii zapasowej, uruchom polecenie:
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

Gdzie:
- __Backup-ID__ to identyfikator kopii zapasowej pokazanej w powyższym poleceniu tworzenia kopii zapasowej.
Spowoduje to skoordynowanie rozproszonego pełnego przywracania we wszystkich węzłach, które stanowią grupę serwerów PostgreSQL z funkcją Azure Arc. Innymi słowy, spowoduje to przywrócenie wszystkich danych z koordynatora i węzłów procesu roboczego.

#### <a name="examples"></a>Przykłady:

__Przywróć grupę serwerów postgres01 na samą siebie:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Ta operacja jest obsługiwana tylko dla PostgreSQL w wersji 12 lub nowszej.

__Przywróć grupę serwerów postgres01 do innej grupy serwerów postgres02:__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
Ta operacja jest obsługiwana dla dowolnej wersji programu PostgreSQL, która rozpoczyna się w wersji 11. Należy utworzyć docelową grupę serwerów przed operacją przywracania, która musi mieć taką samą konfigurację i musi używać tego samego obwodu PVC kopii zapasowej, co źródłowa Grupa serwerów.

Po zakończeniu operacji przywracania zwróci dane wyjściowe podobne do wiersza polecenia:

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> Nie jest to jeszcze możliwe:
> - Przywróć kopię zapasową, wskazując jej nazwę
> - Pokaż postęp operacji przywracania


### <a name="do-a-point-in-time-restore"></a>Przywracanie do punktu w czasie

Aby przywrócić grupę serwerów do określonego czasu punktu, uruchom polecenie:
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

Aby odczytać ogólny format polecenia Restore, uruchom polecenie: `azdata arc postgres backup restore --help` .

Gdzie `time` jest punkt w czasie do przywrócenia. Podaj sygnaturę czasową lub liczbę i sufiks ( `m` w minutach, w `h` godzinach, `d` w dniach lub `w` w tygodniach). Na przykład wraca do `1.5h` 90 minut.

#### <a name="examples"></a>Przykłady:
__Wykonaj przywracanie do punktu w czasie w postgres01 grupy serwerów na samą siebie:__

Nie jest jeszcze możliwe przeprowadzenie przywracania do punktu w czasie grupy serwerów.

__Wykonaj przywracanie do punktu w czasie z grupy serwerów postgres01 do innej grupy serwerów postgres02 do określonej sygnatury czasowej:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

Ten przykład przywraca do grupy serwerów postgres02 stan, w którym Grupa serwerów postgres01 była 8 grudnia 2020 o 04:23:48.75 UTC. Należy zauważyć, że wartość "+ 00" wskazuje strefę czasową dla danego punktu w czasie. Jeśli nie wskazano strefy czasowej, zostanie użyta strefa czasowa klienta z uruchomioną operacją przywracania.

Na przykład:
- `2020-12-08 04:23:48.751326+00` jest interpretowany jako `2020-12-08 04:23:48.751326` czas UTC
- Jeśli jesteś w standardowym obszarze czasu pacyficznego (PST = UTC + 08), `2020-12-08 04:23:48.751326` jest interpretowany jako `2020-12-08 12:23:48.751326` UTC, ponieważ ta operacja jest obsługiwana dla dowolnej wersji programu PostgreSQL, która jest uruchamiana w wersji 11. Należy utworzyć docelową grupę serwerów przed operacją przywracania i musi ona używać tego samego obwodu PVC kopii zapasowej, co źródłowa Grupa serwerów.


__Wykonaj przywracanie do punktu w czasie z grupy serwerów postgres01 do innej grupy serwerów postgres02 w określonym czasie w przeszłości:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

Ten przykład przywraca do grupy serwerów postgres02 stan, w którym Grupa serwerów postgres01 była 22 minut temu.
Ta operacja jest obsługiwana dla dowolnej wersji programu PostgreSQL, która rozpoczyna się w wersji 11. Należy utworzyć docelową grupę serwerów przed operacją przywracania i musi ona używać tego samego obwodu PVC kopii zapasowej, co źródłowa Grupa serwerów.

> [!NOTE]
> Nie jest to jeszcze możliwe:
> - Pokaż postęp operacji przywracania

## <a name="delete-backups"></a>Usuwanie kopii zapasowych

Nie można ustawić przechowywania kopii zapasowych w wersji zapoznawczej. Można jednak ręcznie usunąć kopie zapasowe, które nie są potrzebne.
Ogólne polecenie usuwania kopii zapasowych jest następujące:

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

gdzie:
- `--server-name` jest nazwą grupy serwerów, z której użytkownik chce usunąć kopię zapasową
- `--name` jest nazwą kopii zapasowej do usunięcia
- `-id`to identyfikator kopii zapasowej do usunięcia

> [!NOTE]
> `--name` i wykluczają `-id` się wzajemnie.

Na przykład:

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

Możesz pobrać nazwę i identyfikator kopii zapasowych, uruchamiając polecenie Utwórz kopię zapasową zgodnie z opisem w poprzednim akapicie.

Aby uzyskać więcej informacji na temat polecenia Delete, uruchom polecenie:

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Następne kroki
- Przeczytaj o [skalowaniu (Dodawanie węzłów procesu roboczego)](scale-out-postgresql-hyperscale-server-group.md) do grupy serwerów
- Przeczytaj o [skalowaniu w górę lub w dół (Zwiększanie/zmniejszanie ilości pamięci/rdzeni wirtualnych)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) grupy serwerów
