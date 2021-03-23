---
title: Porady i wskazówki dotyczące korzystania z narzędzia migawek spójnych w aplikacji platformy Azure dla Azure NetApp Files | Microsoft Docs
description: Porady i wskazówki dotyczące korzystania z narzędzia do tworzenia migawek spójnych w aplikacji Azure, których można używać z Azure NetApp Files.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 6465acc0d4ce760e0bf89c73dace7c8c66d37c49
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869943"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>Porady i wskazówki dotyczące korzystania z narzędzia migawek spójnych z aplikacjami platformy Azure (wersja zapoznawcza)

Ten artykuł zawiera porady i wskazówki, które mogą być przydatne w przypadku korzystania z programu AzAcSnap.

## <a name="limit-service-principal-permissions"></a>Ogranicz uprawnienia jednostki usługi

Może być konieczne ograniczenie zakresu nazwy głównej usługi AzAcSnap.  Zapoznaj się z [dokumentacją usługi Azure RBAC](../role-based-access-control/index.yml) , aby uzyskać więcej szczegółów na temat szczegółowych informacji na temat zarządzania dostępem do zasobów platformy Azure.  

Poniżej przedstawiono przykładową definicję roli z minimalnymi wymaganymi akcjami, które są wymagane do działania funkcji AzAcSnap.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

Aby Opcje przywracania działały prawidłowo, jednostka usługi AzAcSnap musi mieć również możliwość tworzenia woluminów.  W takim przypadku definicja roli wymaga dodatkowej akcji, dlatego kompletna jednostka usługi powinna wyglądać podobnie do poniższego przykładu.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots and restores on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```


## <a name="take-snapshots-manually"></a>Ręczne robienie migawek

Przed wykonaniem jakichkolwiek poleceń tworzenia kopii zapasowej ( `azacsnap -c backup` ) Sprawdź konfigurację, uruchamiając polecenia testowe i sprawdzając, czy zostały wykonane pomyślnie.  Poprawne wykonanie tych testów `azacsnap` może komunikować się z zainstalowaną SAP HANA bazą danych i bazowym systemem magazynu SAP HANA na **platformie Azure o dużym wystąpieniu** lub systemie **Azure NetApp Files** .

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

Następnie, aby ręcznie utworzyć kopię zapasową migawki bazy danych, uruchom następujące polecenie:

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>Skonfiguruj automatyczną kopię zapasową migawek

Typowym sposobem na korzystanie z systemów UNIX/Linux jest `cron` zautomatyzowanie uruchomionych poleceń w systemie. Standardową zasadą dla narzędzi migawek jest skonfigurowanie użytkownika `crontab` .

Przykładem `crontab` dla użytkownika w `azacsnap` celu zautomatyzowania migawek jest poniżej.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

Wyjaśnienie powyższego crontab.

- `MAILTO=""`: Jeśli masz pustą wartość, dzięki temu firma CRONUS nie automatycznie próbuje wysłać wiadomości e-mail do użytkownika podczas wykonywania wpisu crontab, ponieważ najprawdopodobniej zakończy się w lokalnym pliku poczty użytkownika.
- Skrócone wersje chronometrażu dla wpisów crontab są oczywiste:
  - `@monthly` = Uruchamiany raz w miesiącu, czyli "0 0 1 * *".
  - `@weekly`  = Uruchamiany raz w tygodniu, czyli "0 0 * * 0".
  - `@daily`   = Uruchom raz dziennie, czyli "0 0 * * *".
  - `@hourly`  = Uruchom raz na godzinę, czyli "0 * * * *".
- Pierwsze pięć kolumn służy do wyznaczania czasu, zapoznaj się z poniższymi kolumnami:
  - `0,15,30,45`: Co 15 minut
  - `0-23`: Co godzinę
  - `*` : Codziennie
  - `*` : Co miesiąc
  - `*` : Każdy dzień tygodnia
- Wiersz polecenia do wykonania zawarty w nawiasach klamrowych "()"
  - `. /home/azacsnap/.profile` = Ściągnij w profilu użytkownika, aby skonfigurować swoje środowisko, w tym $PATH itd.
  - `cd /home/azacsnap/bin` = Zmień katalog wykonywania na lokalizację "/Home/azacsnap/bin", gdzie znajdują się pliki konfiguracyjne.
  - `azacsnap -c .....` = pełne polecenie azacsnap do uruchomienia, w tym wszystkie opcje.

Dokładniejsze wyjaśnienie firmy CRONUS i formatu pliku crontab: <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> Użytkownicy są odpowiedzialni za monitorowanie zadań firmy CRONUS, aby upewnić się, że migawki są generowane pomyślnie.

## <a name="monitor-the-snapshots"></a>Monitorowanie migawek

Poniższe warunki powinny być monitorowane w celu zapewnienia prawidłowego systemu:

1. Dostępne miejsce na dysku. Migawki będą wolno zużywać miejsce na dysku, ponieważ przechowywanie starszych bloków dysków jest zachowywane w migawce.
    1. Aby ułatwić automatyzację zarządzania miejscem na dysku, `--retention` Użyj `--trim` opcji i, aby automatycznie wyczyścić stare migawki i pliki dziennika bazy danych.
1. Pomyślne wykonanie narzędzi migawek
    1. Sprawdź `*.result` plik pod kątem powodzenia lub niepowodzenia ostatniego uruchomienia programu `azacsnap` .
    1. Sprawdź `/var/log/messages` dane wyjściowe `azacsnap` polecenia.
1. Spójność migawek przez przywrócenie ich do innych systemów okresowo.

> [!NOTE]
> Aby wyświetlić szczegóły migawki, wykonaj polecenie `azacsnap -c details` .

## <a name="delete-a-snapshot"></a>Usuń migawkę

Aby usunąć migawkę, wykonaj polecenie `azacsnap -c delete` . Nie można usunąć migawek z poziomu systemu operacyjnego. Aby usunąć migawki magazynu, należy użyć poprawnego polecenia ( `azacsnap -c delete` ).

> [!IMPORTANT]
> Czujność po usunięciu migawki. Po usunięciu nie jest **możliwe** odzyskanie usuniętych migawek.

## <a name="restore-a-snapshot"></a>Przywracanie migawki

Migawkę woluminu magazynu można przywrócić do nowego woluminu ( `-c restore --restore snaptovol` ).  W przypadku dużego wystąpienia platformy Azure wolumin można przywrócić do migawki ( `-c restore --restore revertvolume` ).

> [!NOTE]
> **Nie** podano polecenia odzyskiwania bazy danych.

Migawkę można skopiować z powrotem do obszaru danych SAP HANA, ale SAP HANA nie może być uruchomiona, gdy zostanie utworzona kopia ( `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` ).

W przypadku dużego wystąpienia platformy Azure można skontaktować się z zespołem operacyjnym firmy Microsoft, otwierając żądanie obsługi, aby przywrócić żądaną migawkę z istniejących dostępnych migawek. Żądanie obsługi można otworzyć z Azure Portal: <https://portal.azure.com>

W przypadku podjęcia decyzji o przełączeniu w tryb failover odzyskiwania po awarii `azacsnap -c restore --restore revertvolume` polecenie w witrynie Dr spowoduje automatyczne udostępnienie najnowszych `/hana/data` `/hana/logbackups` migawek woluminów (i), aby umożliwić odzyskanie SAP HANA. Tego polecenia należy używać ostrożnie, ponieważ przerywa replikację między lokacjami produkcyjnymi i DR.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>Skonfiguruj migawki tylko dla woluminów "Boot"

> [!IMPORTANT]
> Ta operacja dotyczy tylko dużych wystąpień platformy Azure.

W niektórych przypadkach klienci mają już narzędzia do ochrony SAP HANA i chcą tylko skonfigurować migawki woluminu "Boot".  W takim przypadku zadanie jest uproszczone i należy wykonać następujące czynności.

1. Wykonaj kroki 1-4 wymagań wstępnych dotyczących instalacji.
1. Włącz komunikację z magazynem.
1. Pobierz Instalator Uruchom Instalatora, aby zainstalować narzędzia Snapshot Tools.
1. Ukończ instalację narzędzi do tworzenia migawek.
1. Utwórz nowy plik konfiguracji w następujący sposób. Szczegóły woluminu rozruchowego muszą znajdować się w OtherVolume Stanza (wpisy użytkownika w <span style="color:red">kolorze czerwonym</span>):
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. Sprawdź plik konfiguracji, zapoznaj się z poniższym przykładem:

    Użyj `cat` polecenia, aby wyświetlić zawartość pliku konfiguracji:

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0 Preview",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume": "t210_sles_boot_azsollabbl20a31_vol"
                  }
                ]
              }
            ],
            "anfStorage": []
          }
        }
      ]
    }
    ```

1. Testowanie kopii zapasowej woluminu rozruchowego

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. Sprawdź, czy jest on wymieniony na liście, `--snapshotfilter` Aby ograniczyć liczbę zwracanych list migawek.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    Dane wyjściowe polecenia:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. Skonfiguruj automatyczną kopię zapasową migawki.

> [!NOTE]
> Konfiguracja komunikacji z SAP HANA nie jest wymagana.

## <a name="restore-a-boot-snapshot"></a>Przywracanie migawki "Boot"

> [!IMPORTANT]
> Ta operacja dotyczy usługi Azure Large instance tylko.
> Serwer zostanie przywrócony do punktu, w którym utworzono migawkę.

Migawkę "Boot" można odzyskać w następujący sposób:

1. Klient będzie musiał zamknąć serwer.
1. Po wyłączeniu serwera klient będzie musiał otworzyć żądanie obsługi, które zawiera identyfikator komputera i migawkę do przywrócenia.
    > Klienci mogą otworzyć żądanie obsługi z Azure Portal: <https://portal.azure.com>
1. Firma Microsoft przywróci jednostkę LUN systemu operacyjnego przy użyciu określonego identyfikatora komputera i migawki, a następnie uruchamia serwer.
1. Klient będzie musiał wtedy potwierdzić, że serwer jest uruchomiony i w dobrej kondycji.

Po przywróceniu nie ma dodatkowych kroków do wykonania.

## <a name="key-facts-to-know-about-snapshots"></a>Najważniejsze fakty dotyczące migawek

Kluczowe atrybuty migawek woluminu magazynu:

- **Lokalizacja migawek**: migawki można znaleźć w katalogu wirtualnym () w `.snapshot` woluminie.  Zobacz następujące przykłady dla **dużych wystąpień platformy Azure**:
  - Database `/hana/data/<SID>/mnt00001/.snapshot`
  - Udostępniać `/hana/shared/<SID>/.snapshot`
  - Dziennik `/hana/logbackups/<SID>/.snapshot`
  - Rozruch: migawki rozruchowe dla elementu HLI **nie są widoczne** dla poziomu systemu operacyjnego, ale można je wyświetlić przy użyciu polecenia `azacsnap -c details` .

  > [!NOTE]
  >  `.snapshot` jest ukrytym folderem *wirtualnym* tylko do odczytu, który zapewnia dostęp tylko do odczytu do migawek.

- **Maksymalna migawka:** Sprzęt może utrzymywać do 250 migawek na wolumin. Polecenie migawka zachowuje maksymalną liczbę migawek dla prefiksu na podstawie zestawu przechowywania w wierszu polecenia i usunie najstarszą migawkę, jeśli przekracza maksymalną liczbę do zachowania.
- **Nazwa migawki:** Nazwa migawki zawiera etykietę prefiksu dostarczoną przez klienta.
- **Rozmiar migawki:** Zależy od rozmiaru/zmian na poziomie bazy danych.
- **Lokalizacja pliku dziennika:** Pliki dziennika generowane przez polecenia są wyprowadzane do folderów zgodnie z definicją w pliku konfiguracyjnym JSON, który domyślnie jest podfolderem w miejscu, w którym jest uruchamiane polecenie (na przykład `./logs` ).

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów](azacsnap-troubleshoot.md)
