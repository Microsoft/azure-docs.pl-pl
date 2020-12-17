---
title: Usuń za pomocą narzędzia migawek spójnej na poziomie aplikacji platformy Azure dla Azure NetApp Files | Microsoft Docs
description: Zawiera Przewodnik dotyczący uruchamiania polecenia Delete narzędzia migawek spójnej na poziomie aplikacji platformy Azure, którego można używać z Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0e2e4beebedb93524da43c5a3fad750b0295f5cd
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632910"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Usuń przy użyciu narzędzia do tworzenia migawek spójnych aplikacji platformy Azure (wersja zapoznawcza)

Ten artykuł zawiera Przewodnik uruchamiania polecenia Delete narzędzia migawek spójnej na poziomie aplikacji platformy Azure, którego można używać z Azure NetApp Files.

## <a name="introduction"></a>Wprowadzenie

Istnieje możliwość usunięcia migawek woluminu i wpisów wykazu bazy danych za pomocą `azacsnap -c delete` polecenia.

> [!IMPORTANT]
> Migawki utworzone poniżej 10 minut przed uruchomieniem tego polecenia nie powinny zostać usunięte ze względu na potencjalną ingerencję z replikacją migawek.

## <a name="command-options"></a>Opcje polecenia

`-c delete`Polecenie ma następujące opcje:

- `--delete hana` w przypadku użycia z opcjami `--hanasid <SID>` i `--hanabackupid <HANA backup id>` spowoduje usunięcie wpisów z wykazu kopii zapasowych SAP HANA pasujących do kryteriów.

- `--delete storage` Użycie z opcją `--snapshot <snapshot name>` spowoduje usunięcie migawki z systemu magazynu zaplecza.

- `--delete sync` w przypadku użycia z opcjami `--hanasid <SID>` i `--hanabackupid <HANA backup id>` Pobiera nazwę migawki magazynu z wykazu kopii zapasowych dla `<HANA backup id>` , a następnie usuwa wpis w wykazie kopii zapasowych _i_ migawkę z dowolnego woluminu zawierającego nazwaną migawkę.

- `--delete sync` w przypadku użycia z programem `--snapshot <snapshot name>` program sprawdzi, czy dla każdego wpisu w wykazie kopii zapasowych dla programu znajduje `<snapshot name>` się SAP HANA identyfikator kopii zapasowej, a także usunie wpis w wykazie kopii zapasowych _i_ migawkę z dowolnego woluminu zawierającego nazwaną migawkę.

- `[--force]` obowiązkowe *Należy używać z zachowaniem ostrożności*.  Ta operacja spowoduje wymuszenie usunięcia bez monitowania o potwierdzenie.

- `[--configfile <config filename>]` jest opcjonalnym parametrem umożliwiającym stosowanie niestandardowych nazw plików konfiguracji.

### <a name="delete-a-snapshot-using-sync-option"></a>Usuń migawkę przy użyciu `sync` opcji "

```bash
azacsnap -c delete --delete sync --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Sprawdza wpisy w wykazie kopii zapasowych dla SAP HANA kopii zapasowej o IDENTYFIKATORze 157979797979, pobiera nazwę migawki magazynu i usuwa zarówno wpis w wykazie kopii zapasowych, jak i migawkę ze wszystkich woluminów zawierających nazwaną migawkę.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Sprawdza wpisy w wykazie kopii zapasowych dla migawki o nazwie hana_hourly .2020-01 -22 _2358, pobiera identyfikator kopii zapasowej SAP HANA i usuwa wpis w wykazie kopii zapasowych oraz migawkę z dowolnego woluminu zawierającego nazwaną migawkę.

### <a name="delete-a-snapshot-using-hana-option"></a>Usuń migawkę przy użyciu `hana` opcji "

```bash
azacsnap -c delete --delete hana --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Usuwa SAP HANA kopii zapasowej 157979797979 z wykazu kopii zapasowych dla identyfikatora SID H80.

### <a name="delete-a-snapshot-using-storage-option"></a>Usuń migawkę przy użyciu `storage` opcji "

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Usuwa migawkę z dowolnych woluminów zawierających migawkę o nazwie hana_hourly .2020-01 -22 _2358.

**Dane wyjściowe przy użyciu `--delete storage` opcji**

Użytkownik zostanie poproszony o potwierdzenie usunięcia.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

Istnieje możliwość uniknięcia potwierdzenia przez użytkownika przy użyciu opcjonalnego `--force` parametru w następujący sposób:

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>Następne kroki

- [Pobierz szczegóły migawki](azacsnap-cmd-ref-details.md)
- [Utwórz kopię zapasową](azacsnap-cmd-ref-backup.md)
