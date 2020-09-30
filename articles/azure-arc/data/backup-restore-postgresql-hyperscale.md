---
title: Tworzenie kopii zapasowej i przywracanie grup serwerów usługi Azure Database for PostgreSQL — hiperskala
description: Tworzenie kopii zapasowej i przywracanie grup serwerów usługi Azure Database for PostgreSQL — hiperskala
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dde4db7f3eb476b7645e910504e48fea8bb6df0c
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569706"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Tworzenie kopii zapasowych i przywracanie dla usługi Azure ARC z włączonymi grupami serwerów PostgreSQL

Możesz wykonywać pełną kopię zapasową/przywracanie grupy serwerów PostgreSQL z funkcją Azure Arc. Po wykonaniu tej czynności zostanie utworzona kopia zapasowa całego zestawu baz danych we wszystkich węzłach z włączoną grupą serwerów PostgreSQL i/lub przywrócona.
Aby utworzyć kopię zapasową i przywrócić ją, należy upewnić się, że dla grupy serwerów skonfigurowano klasę magazynu kopii zapasowych. Na razie należy wskazać klasę magazynu kopii zapasowych podczas tworzenia grupy serwerów. Nie można jeszcze skonfigurować grupy serwerów tak, aby korzystała z klasy magazynu kopii zapasowej po jej utworzeniu.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> Wersja zapoznawcza nie obsługuje tworzenia kopii zapasowych i przywracania w wersji 11 aparatu Postgres. Obsługuje tylko tworzenie kopii zapasowych i przywracanie dla Postgres w wersji 12.

## <a name="verify-configuration"></a>Weryfikuj konfigurację

Najpierw sprawdź, czy istniejąca Grupa serwerów została skonfigurowana do korzystania z klasy magazynu kopii zapasowej.

Uruchom następujące polecenie po ustawieniu nazwy grupy serwerów:
```console
 azdata arc postgres server show -n postgres01
```
Zapoznaj się z sekcją magazyn danych wyjściowych:
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
Jeśli zobaczysz nazwę klasy magazynu wskazanej w sekcji "kopie zapasowe" danych wyjściowych tego polecenia, oznacza to, że Grupa serwerów została skonfigurowana do używania klasy magazynu kopii zapasowych i jest gotowa do wykonania kopii zapasowych i przywrócenia. Jeśli nie widzisz sekcji "kopie zapasowe", musisz usunąć i utworzyć ponownie grupę serwerów, aby skonfigurować klasę magazynu kopii zapasowych. W tym momencie nie można jeszcze konfigurować klasy magazynu kopii zapasowej po utworzeniu grupy serwerów.

>[!IMPORTANT]
>Jeśli Grupa serwerów jest już skonfigurowana do używania klasy magazynu kopii zapasowych, Pomiń następny krok i przejdź bezpośrednio do kroku "wykonaj ręczną pełną kopię zapasową".

## <a name="create-a-server-group"></a>Tworzenie grupy serwerów 

Następnie utwórz grupę serwerów skonfigurowaną na potrzeby tworzenia kopii zapasowej/przywracania.

Aby można było tworzyć kopie zapasowe i przywracać je, należy utworzyć serwer, który jest skonfigurowany z klasą magazynu.

Aby uzyskać listę klas magazynu dostępnych w klastrze Kubernetes, uruchom następujące polecenie:

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

Na przykład jeśli utworzono proste środowisko na podstawie kubeadm:
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>Wykonaj ręczną pełną kopię zapasową

Następnie wypełnij ręcznie pełną kopię zapasową.

Aby wykonać pełną kopię zapasową wszystkich folderów danych i dzienników grupy serwerów, uruchom następujące polecenie:

```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Gdzie:
- __Nazwa__ wskazuje nazwę kopii zapasowej
- __Nazwa serwera__ wskazuje grupę serwerów
- __nie — oczekiwanie__ wskazuje, że wiersz polecenia nie czeka na ukończenie tworzenia kopii zapasowej, aby móc kontynuować korzystanie z tego okna wiersza polecenia

>**Uwaga**: polecenie umożliwiające wyświetlenie listy kopii zapasowych, które są dostępne do przywrócenia, nie jest jeszcze wyświetlane, Data/godzina utworzenia kopii zapasowej. Dlatego zaleca się nadanie nazwy kopii zapasowej (przy użyciu parametru--name) zawierającego informacje o dacie i godzinie.

To polecenie służy do koordynowania rozproszonej pełnej kopii zapasowej we wszystkich węzłach, które stanowią grupę serwerów z funkcją Azure Arc PostgreSQL. Innymi słowy, będzie tworzyć kopie zapasowe wszystkich danych w koordynatorze i węzłach procesu roboczego.

Na przykład:
```console
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

Po zakończeniu wykonywania kopii zapasowej zostanie zwrócony identyfikator, nazwa i stan kopii zapasowej. Na przykład:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> Nie jest to jeszcze możliwe:
> - Zaplanuj automatyczne kopie zapasowe
> - Pokaż postęp tworzenia kopii zapasowej podczas jej wykonywania

## <a name="list-backups"></a>Wyświetlenie listy kopii zapasowych

Utwórz listę kopii zapasowych, które są dostępne do przywrócenia.

Aby wyświetlić listę kopii zapasowych, które są dostępne do przywrócenia, uruchom następujące polecenie:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Na przykład:
```console
azdata arc postgres backup list --server-name postgres01
```

Zwróci dane wyjściowe podobne do:
```console
ID                                Name                      State    Timestamp
--------------------------------  ------------------------  -------  ------------------------------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done     2020-08-31 14:30:00:00+00:00
```

Sygnatura czasowa wskazuje punkt w czasie UTC, w którym wykonano kopię zapasową.

## <a name="restore-a-backup"></a>Przywracanie kopii zapasowej

Aby przywrócić kopię zapasową całej grupy serwerów, uruchom polecenie:

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

Gdzie:
- __Backup-ID__ to identyfikator kopii zapasowej pokazanej w poleceniu list Backup (zobacz krok 3).
Spowoduje to skoordynowanie rozproszonego pełnego przywracania we wszystkich węzłach, które stanowią grupę serwerów PostgreSQL z funkcją Azure Arc. Innymi słowy, spowoduje to przywrócenie wszystkich danych z koordynatora i węzłów procesu roboczego.

Na przykład:
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Po zakończeniu operacji przywracania zwróci dane wyjściowe podobne do wiersza polecenia:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> Nie jest to jeszcze możliwe:
> - Przywróć kopię zapasową, wskazując jej nazwę
> - Przywracanie grupy serwerów pod inną nazwą lub w innej grupie serwerów
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

Możesz pobrać nazwę i identyfikator kopii zapasowych, uruchamiając polecenie Utwórz kopię zapasową zgodnie z opisem w poprzednim akapicie.

Rozważmy na przykład następujące kopie zapasowe wymienione poniżej:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
a chcesz usunąć pierwsze z nich, uruchom następujące polecenie:
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
Jeśli w tym momencie zawarto listę kopii zapasowych, uzyskasz następujące dane wyjściowe:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

Aby uzyskać więcej informacji na temat polecenia Delete, uruchom polecenie:
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Następne kroki
- Przeczytaj o [skalowaniu (Dodawanie węzłów procesu roboczego)](scale-out-postgresql-hyperscale-server-group.md) do grupy serwerów
- Przeczytaj o [skalowaniu w górę lub w dół (Zwiększanie/zmniejszanie ilości pamięci/rdzeni wirtualnych)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) grupy serwerów
