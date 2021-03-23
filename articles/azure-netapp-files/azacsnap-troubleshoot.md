---
title: Rozwiązywanie problemów z narzędziem migawek spójnej na poziomie aplikacji platformy Azure dla Azure NetApp Files | Microsoft Docs
description: Zawiera informacje dotyczące rozwiązywania problemów z użyciem narzędzia do tworzenia migawek spójnych w aplikacji Azure, których można używać z Azure NetApp Files.
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
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 7ba5a05515284612e17d5aba4cc673c7e78f7ba1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869926"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>Rozwiązywanie problemów z narzędziem do tworzenia migawek spójnych w aplikacji Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z używaniem narzędzia do tworzenia migawek spójnych w aplikacji Azure, których można użyć z Azure NetApp Files.

Poniżej przedstawiono typowe problemy, które mogą wystąpić podczas wykonywania poleceń. Postępuj zgodnie z podanymi instrukcjami dotyczącymi rozwiązania problemu. Jeśli nadal występują problemy, Otwórz żądanie obsługi z Azure Portal i przypisz żądanie do kolejki SAP HANA duże wystąpienie w celu pomoc techniczna firmy Microsoft odpowiedzi.

## <a name="log-files"></a>Pliki dziennika

Jednym z najlepszych źródeł informacji na potrzeby debugowania błędów w programie AzAcSnap są pliki dziennika.  

### <a name="log-file-location"></a>Lokalizacja pliku dziennika

Pliki dziennika są przechowywane w katalogu skonfigurowanym na `logPath` parametr w pliku konfiguracji AzAcSnap.  Domyślna nazwa pliku konfiguracji to `azacsnap.json` i wartość domyślna dla programu `logPath` to `"./logs"` oznacza, że pliki dziennika są zapisywane w `./logs` katalogu względem lokalizacji `azacsnap` uruchomienia polecenia.  Utworzenie `logPath` lokalizacji bezwzględnej (np. `/home/azacsnap/logs` ) spowoduje `azacsnap` wyjście z dzienników, `/home/azacsnap/logs` niezależnie od tego, gdzie `azacsnap` uruchomiono polecenie.

### <a name="log-file-naming"></a>Nazwa pliku dziennika

Nazwa pliku dziennika jest określana na podstawie nazwy aplikacji (np. `azacsnap` ), używanej opcji polecenia ( `-c` np., `backup` `test` `details` itp.) i nazwy pliku konfiguracji (np. default = `azacsnap.json` ).  Dlatego w przypadku używania `-c backup` polecenia nazwa pliku dziennika `azacsnap-backup-azacsnap.log` jest domyślnie zapisywana w katalogu skonfigurowanym przez program `logPath` .  

Ta konwencja nazewnictwa została ustanowiona, aby zezwalać na wiele plików konfiguracji, jednej na bazę danych i zapewnić łatwość lokalizowania skojarzonych dziennika.  W związku z tym, jeśli nazwa pliku konfiguracji to `SID.json` , wówczas nazwa pliku wynik przy użyciu `azacsnap -c backup --configfile SID.json` opcji będzie `azacsnap-backup-SID.log` .

### <a name="result-file-and-syslog"></a>Plik wynikowy i dziennik systemowy

Dla `-c backup` opcji polecenia AzAcSnap zapisuje `*.result` dane do pliku i dziennika systemowego ( `/var/log/messages` ) przy użyciu `logger` polecenia.  Nazwa pliku `*.result` ma taką samą nazwę bazową jak [plik dziennika](#log-file-naming) i znajduje się w tej samej [lokalizacji co plik dziennika](#log-file-location).  Jest to prosty plik wyjściowy o jednej linii dla następujących przykładów.

Przykład danych wyjściowych z `*.result` pliku.
```output
Database # 1 (PR1) : completed ok
```

Przykład danych wyjściowych z `/var/log/messages` pliku.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-azure-netapp-files"></a>Komunikacja zakończona niepowodzeniem z Azure NetApp Files

Podczas sprawdzania poprawności komunikacji przy użyciu Azure NetApp Files komunikacja może zakończyć się niepowodzeniem lub przekroczyć limit czasu.  Upewnij się, że reguły zapory nie blokują ruchu wychodzącego z systemu z systemem AzAcSnap na następujących adresach i portach TCP/IP:-

- (https://) Management. Azure. com: 443
- (https://) login. microsoftonline. com: 443 

## <a name="failed-communication-with-sap-hana"></a>Komunikacja zakończona niepowodzeniem z SAP HANA

Podczas sprawdzania poprawności komunikacji z SAP HANA przez uruchomienie testu z `azacsnap -c test --test hana` i zapewnia następujący błąd:

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Rozwiązanie:**

1. Sprawdź plik konfiguracji (na przykład `azacsnap.json` ) dla każdego wystąpienia platformy Hana, aby upewnić się, że wartości bazy danych SAP HANA są poprawne.
1. Spróbuj uruchomić poniższe polecenie, aby sprawdzić, czy `hdbsql` polecenie znajduje się w ścieżce i czy może nawiązać połączenie z serwerem SAP HANA. W poniższym przykładzie pokazano poprawne uruchomienie polecenia i jego danych wyjściowych.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    W tym przykładzie `hdbsql` polecenie nie należy do użytkowników `$PATH` .

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    W tym przykładzie `hdbsql` polecenie jest tymczasowo dodawane do użytkownika `$PATH` , ale w przypadku uruchomienia pokazuje, że klucz połączenia nie został prawidłowo skonfigurowany za pomocą `hdbuserstore Set` polecenia (szczegółowe informacje znajdują się w przewodniku wprowadzenie):

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > Aby trwale dodać do użytkownika `$PATH` , zaktualizuj `$HOME/.profile` plik użytkownika

## <a name="the-hdbuserstore-location"></a>`hdbuserstore`Lokalizacja

Podczas konfigurowania komunikacji przy użyciu SAP HANA `hdbuserstore` program jest używany do tworzenia ustawień bezpiecznej komunikacji.  `hdbuserstore`Program jest zwykle znaleziony w obszarze `/usr/sap/<SID>/SYS/exe/hdb/` lub `/usr/sap/hdbclient` .  Zwykle Instalator dodaje poprawną lokalizację do `azacsnap` użytkownika `$PATH` .

## <a name="failed-test-with-storage"></a>Test zakończony niepowodzeniem z magazynem

Polecenie nie zostało `azacsnap -c test --test storage` pomyślnie zakończone.

### <a name="azure-large-instance"></a>Duże wystąpienie platformy Azure

W poniższym przykładzie pokazano, jak uruchomić `azacsnap` na SAP HANA na platformie Azure duże wystąpienie:

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Rozwiązanie:** Powyższy błąd jest zwykle wyświetlany, gdy użytkownik usługi Azure Large Instance Storage nie ma dostępu do magazynu bazowego. Aby sprawdzić poprawność dostępu do magazynu za pomocą podanego użytkownika magazynu, uruchom `ssh` polecenie w celu sprawdzenia komunikacji z platformą magazynową.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Przykład z oczekiwaną liczbą wyjściową:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>Nie można ustalić autentyczności hosta "172.18.18.11 (172.18.18.11)"

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Rozwiązanie:** Nie wybieraj opcji tak. Upewnij się, że adres IP magazynu jest prawidłowy. Jeśli problem nadal występuje, Potwierdź adres IP magazynu z zespołem ds. operacyjnych firmy Microsoft.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Poniższy przykład jest uruchamiany `azacsnap` na maszynie wirtualnej przy użyciu Azure NetApp Files:

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Rozwiązanie:**

1. Sprawdź istnienie pliku głównego usługi, `azureauth.json` zgodnie z ustawieniem w `azacsnap.json` pliku konfiguracji.
1. Sprawdź plik dziennika (na przykład), `logs/azacsnap-test-azacsnap.log` Aby sprawdzić, czy nazwa główna usługi ( `azureauth.json` ) ma poprawną zawartość.  Przykład z dziennika w następujący sposób:

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. Sprawdź plik dziennika (na przykład), `logs/azacsnap-test-azacsnap.log` Aby sprawdzić, czy nazwa główna usługi ( `azureauth.json` ) wygasła. Przykład z dziennika w następujący sposób:

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Następne kroki

- [Porady](azacsnap-tips.md)
