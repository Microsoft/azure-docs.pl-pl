---
title: Skonfiguruj narzędzie do tworzenia migawek spójnych w aplikacji Azure dla Azure NetApp Files | Microsoft Docs
description: Zawiera Przewodnik dotyczący uruchamiania polecenia configure narzędzia migawek spójnej na poziomie aplikacji platformy Azure, którego można używać z Azure NetApp Files.
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
ms.openlocfilehash: 0875aae8bb9049fc96377c1c98efa7391211d08f
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632977"
---
# <a name="configure-azure-application-consistent-snapshot-tool-preview"></a>Konfigurowanie narzędzia do tworzenia migawek spójnych w aplikacji Azure (wersja zapoznawcza)

Ten artykuł zawiera Przewodnik dotyczący uruchamiania polecenia configure narzędzia migawek spójnej na poziomie aplikacji platformy Azure, którego można używać z Azure NetApp Files.

## <a name="introduction"></a>Wprowadzenie

Plik konfiguracji można utworzyć lub edytować za pomocą `azacsnap -c configure` polecenia.

## <a name="command-options"></a>Opcje polecenia

`-c configure`Polecenie ma następujące opcje

- `--configuration new` Aby utworzyć nowy plik konfiguracji.

- `--configuration edit` Aby edytować istniejący plik konfiguracji.

- `[--configfile <config filename>]` jest opcjonalnym parametrem umożliwiającym stosowanie niestandardowych nazw plików konfiguracji.

## <a name="configuration-file-for-snapshot-tools"></a>Plik konfiguracji dla narzędzi migawek

Plik konfiguracji można utworzyć, uruchamiając `azacsnap -c configure --configuration new` .  Domyślnie nazwa pliku konfiguracji to `azacsnap.json` .  Niestandardową nazwę pliku można użyć z `--configfile=` parametrem (na przykład `--configfile=<customname>.json` ) w przypadku konfiguracji usługi Azure Large instance:

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>Szczegóły wymaganych wartości

Poniższe sekcje zawierają szczegółowe wskazówki dotyczące różnych wartości wymaganych dla pliku konfiguracji.

### <a name="sap-hana-values"></a>SAP HANA wartości

Podczas dodawania *bazy danych* do konfiguracji wymagane są następujące wartości:

- **Adres serwera Hana** = nazwa hosta lub adres IP serwera SAP HANA.
- Identyfikator **SID platformy Hana** = SAP HANA identyfikatora systemu.
- **Numer wystąpienia Hana** = numer wystąpienia SAP HANA.
- **Klucz magazynu użytkownika platformy Hana HDB** — użytkownik SAP HANA skonfigurowany z uprawnieniami do uruchamiania kopii zapasowych bazy danych.

- Pojedynczy węzeł: adres IP i nazwa hosta węzła
- HSR z STONITH: adres IP i nazwa hosta węzła
- Skalowanie w poziomie (N + N, N + M): bieżący adres IP i nazwa hosta węzła głównego
- HSR bez STONITH: adres IP i nazwa hosta węzła
- Wiele identyfikatorów SID w jednym węźle: Nazwa hosta i adres IP węzła obsługującego te identyfikatory SID

### <a name="azure-large-instance-hli-storage-values"></a>Wartości magazynu Azure Large Instance (HLI)

W przypadku dodawania *magazynu magazynowania* do sekcji bazy danych wymagane są następujące wartości:

- **Nazwa użytkownika magazynu** = ta wartość jest nazwą użytkownika służącą do nawiązywania połączenia SSH z magazynem.
- **Adres IP magazynu** = adres systemu magazynu.
- **Nazwa woluminu magazynu** = nazwa woluminu do utworzenia migawki.  Tę wartość można określić na wiele sposobów, na przykład najprostszą jest wypróbowanie następującego polecenia powłoki:

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Wartości magazynu Azure NetApp Files (ANF)

Podczas dodawania *magazynu ANF* do sekcji bazy danych wymagane są następujące wartości:

- **Nazwa pliku uwierzytelniania nazwy głównej usługi** = jest to `authfile.json` plik wygenerowany w Cloud Shell podczas konfigurowania komunikacji z usługą Azure NetApp Files Storage.
- **Pełny identyfikator zasobu woluminu magazynu ANF** = pełny identyfikator zasobu dla migawki woluminu.  Można go pobrać z: Azure Portal – > ANF – > Volume — > ustawienia/właściwości — > identyfikator zasobu

## <a name="configuration-file-overview-azacsnapjson"></a>Omówienie pliku konfiguracji ( `azacsnap.json` )

W poniższym przykładzie `azacsnap.json` jest skonfigurowany przy użyciu jednego identyfikatora SID.

Wartości parametrów muszą być ustawione na specyficzne dla klienta środowisko SAP HANA.
W przypadku systemu **Azure w dużych wystąpieniach** te informacje są udostępniane przez usługę Microsoft Service Management podczas wywołania dołączania/przekazują i są dostępne w pliku programu Excel, który jest udostępniany podczas przekazują. Otwórz żądanie obsługi, jeśli chcesz ponownie podać te informacje.

Poniżej znajduje się tylko przykład, dlatego należy odpowiednio zaktualizować wszystkie wartości.

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0 Preview",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
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

> [!NOTE]
> W przypadku scenariusza odzyskiwania po awarii, gdy kopie zapasowe mają być uruchamiane w lokacji odzyskiwania po awarii, nazwa serwera HANA skonfigurowana w pliku konfiguracji DR (na przykład `DR.json` ) w lokacji Dr powinna być taka sama jak nazwa serwera produkcyjnego.

> [!NOTE]
> W przypadku dużego wystąpienia platformy Azure adres IP magazynu musi znajdować się w tej samej podsieci co Pula serwerów. Na przykład w tym przypadku nasza podsieć puli serwerów to 172. postanowienia. 18.0/24 i nasz przypisany adres IP magazynu to 172.18.18.11.

## <a name="next-steps"></a>Następne kroki

- [Testowanie narzędzia migawek spójnej na poziomie aplikacji platformy Azure](azacsnap-cmd-ref-test.md)
