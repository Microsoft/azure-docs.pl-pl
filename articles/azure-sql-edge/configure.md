---
title: Konfigurowanie usługi Azure SQL Edge (wersja zapoznawcza)
description: Dowiedz się więcej o konfigurowaniu usługi Azure SQL Edge (wersja zapoznawcza).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: 722d33e76b6009a44811dfcb8a3238b042ec6918
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816885"
---
# <a name="configure-azure-sql-edge-preview"></a>Konfigurowanie usługi Azure SQL Edge (wersja zapoznawcza)

Usługa Azure SQL Edge obsługuje konfigurację przy użyciu jednej z następujących dwóch opcji:

- Zmienne środowiskowe
- Plik MSSQL. conf umieszczony w folderze/var/opt/MSSQL

> [!NOTE]
> Ustawianie zmiennych środowiskowych przesłania ustawienia określone w pliku MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Konfigurowanie przy użyciu zmiennych środowiskowych

Usługa Azure SQL Edge uwidacznia kilka różnych zmiennych środowiskowych, których można użyć do skonfigurowania kontenera programu SQL Edge. Te zmienne środowiskowe są podzbiorem tych, które są dostępne dla SQL Server on Linux. Aby uzyskać więcej informacji na temat zmiennych środowiskowych SQL Server on Linux, zobacz [zmienne środowiskowe](/sql/linux/sql-server-linux-configure-environment-variables/).

Następująca SQL Server on Linux zmienna środowiskowa nie jest obsługiwana w przypadku usługi Azure SQL Edge. Jeśli jest zdefiniowany, ta zmienna środowiskowa zostanie zignorowana podczas inicjowania kontenera.

| Zmienna środowiskowa | Opis |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Włącz grupę dostępności. Na przykład **1** jest włączona, a **0** jest wyłączone. |

> [!IMPORTANT]
> Zmienna środowiskowa **MSSQL_PID** dla programu SQL Edge akceptuje tylko wartości **Premium** i **Developer** , ponieważ są prawidłowymi wartościami. Usługa Azure SQL Edge nie obsługuje inicjowania przy użyciu klucza produktu.

> [!NOTE]
> Pobierz [postanowienia licencyjne dotyczące oprogramowania firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=2128283) dla usługi Azure SQL Edge.

### <a name="specify-the-environment-variables"></a>Określanie zmiennych środowiskowych

Określ zmienne środowiskowe dla programu SQL Edge podczas wdrażania usługi za pomocą [Azure Portal](deploy-portal.md). Można je dodać w sekcji **zmienne środowiskowe** wdrożenia modułu lub w ramach **opcji tworzenia kontenera**.

Dodaj wartości w **zmiennych środowiskowych**.

![Ustawianie za pomocą listy zmiennych środowiskowych](media/configure/set-environment-variables.png)

Dodaj wartości w obszarze **Opcje tworzenia kontenera**.

![Ustawianie przy użyciu opcji tworzenia kontenera](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>Konfigurowanie przy użyciu pliku MSSQL. conf

Usługa Azure SQL Edge nie zawiera [Narzędzia konfiguracji MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) , takiego jak SQL Server on Linux. Należy ręcznie skonfigurować plik MSSQL. conf i umieścić go na trwałym dysku magazynującym, który jest mapowany do folderu/var/opt/MSSQL/w module SQL Edge. Podczas wdrażania programu SQL Edge z poziomu portalu Azure Marketplace to mapowanie jest określone jako opcja **mounts** w **opcjach tworzenia kontenera**.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

Następujące opcje MSSQL. conf nie mają zastosowania do programu SQL Edge:

|Opcja|Opis|
|:---|:---|
|**Opinie klientów** | Wybierz, czy SQL Server wysyła informacje zwrotne do firmy Microsoft. |
|**Profil poczty bazy danych** | Ustaw domyślny profil poczty bazy danych dla SQL Server on Linux. |
|**Wysoka dostępność** | Włącz grupy dostępności. |
|**Distributed Transaction Coordinator firmy Microsoft** | Konfigurowanie i rozwiązywanie problemów z usługą MSDTC w systemie Linux. Dodatkowe opcje konfiguracji związane z transakcją rozproszoną nie są obsługiwane w programie SQL Edge. Aby uzyskać więcej informacji na temat dodatkowych opcji konfiguracji, zobacz [Konfigurowanie usługi MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**Umowy licencyjne usług ML** | Akceptuj umowy EULA języków R i Python dla pakietów Azure Machine Learning. Dotyczy tylko SQL Server 2019.|
|**outboundnetworkaccess** |Włącz wychodzący dostęp [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) do sieci dla rozszerzeń R, Python i Java.|

Następujący przykładowy plik MSSQL. conf działa dla programu SQL Edge. Aby uzyskać więcej informacji na temat formatu pliku MSSQL. conf, zobacz [Format MSSQL. conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>Uruchamianie usługi Azure SQL Edge jako użytkownika niebędącego głównym

Począwszy od usługi Azure SQL Edge CTP 2.2, kontenery usługi SQL Edge mogą działać z niegłównym użytkownikiem/grupą. W przypadku wdrożenia w portalu Azure Marketplace, jeśli nie określono innego użytkownika/grupy, kontenery usługi SQL Edge są uruchamiane jako użytkownik MSSQL (niebędący elementem głównym). Aby określić innego użytkownika niebędącego elementem głównym podczas wdrażania, Dodaj `*"User": "<name|uid>[:<group|gid>]"*` parę klucz-wartość w obszarze Opcje tworzenia kontenera. W poniższym przykładzie jest skonfigurowany do uruchamiania jako użytkownik programu SQL Edge `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Aby zezwolić użytkownikom spoza katalogu głównego na dostęp do plików bazy danych znajdujących się na woluminach zainstalowanych, należy się upewnić, że użytkownik/Grupa, w której jest uruchomiony kontener, ma uprawnienia Odczyt & zapis w magazynie plików trwałych. W poniższym przykładzie ustawimy użytkownika innego niż root z user_id 10001 jako właściciel plików. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Uaktualnianie z wcześniejszych wersji CTP

Starsze wersje CTP usługi Azure SQL Edge zostały skonfigurowane tak, aby były uruchamiane jako użytkownicy root. Podczas uaktualniania z wcześniejszych wersji CTP dostępne są następujące opcje:

- Kontynuuj korzystanie z użytkownika głównego — aby nadal korzystać z użytkownika root, Dodaj `*"User": "0:0"*` parę klucz-wartość w obszarze Opcje tworzenia kontenera.
- Użyj domyślnego użytkownika programu MSSQL — aby użyć domyślnego użytkownika MSSQL, wykonaj poniższe czynności.
  - Dodaj użytkownika o nazwie MSSQL na hoście platformy Docker. W poniższym przykładzie dodamy użytkownika MSSQL z IDENTYFIKATORem 10001. Ten użytkownik jest również dodawany do grupy głównej.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Zmień uprawnienie do katalogu/woluminu instalacji, w którym znajduje się plik bazy danych 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Użyj innego konta użytkownika niebędącego elementem głównym — w celu użycia innego konta użytkownika niebędącego kontem głównym
  - Zaktualizuj opcje tworzenia kontenera, aby określić `*"User": "user_name | user_id*` parę klucz-wartość w obszarze Opcje tworzenia kontenera. Zastąp user_name lub user_id rzeczywistym user_name lub user_id z hosta platformy Docker. 
  - Zmień uprawnienia do katalogu/woluminu instalacji.

## <a name="persist-your-data"></a>Utrwalanie danych

Zmiany konfiguracji i pliki bazy danych usługi Azure SQL Edge są utrwalane w kontenerze nawet po ponownym uruchomieniu kontenera za pomocą `docker stop` i `docker start` . Jednak po usunięciu kontenera z programu `docker rm` wszystkie elementy w kontenerze zostaną usunięte, łącznie z usługą Azure SQL Edge i bazami danych. W poniższej sekcji wyjaśniono, jak używać **woluminów danych** do utrwalania plików bazy danych, nawet jeśli skojarzone kontenery są usuwane.

> [!IMPORTANT]
> W przypadku usługi Azure SQL Edge ważne jest, aby zrozumieć trwałość danych w Docker. Oprócz dyskusji w tej sekcji zapoznaj się z dokumentacją platformy Docker dotyczącą [zarządzania danymi w kontenerach platformy Docker](https://docs.docker.com/engine/tutorials/dockervolumes/).

### <a name="mount-a-host-directory-as-data-volume"></a>Instalowanie katalogu hosta jako woluminu danych

Pierwszą opcją jest zainstalowanie katalogu na hoście jako woluminu danych w kontenerze. Aby to zrobić, użyj `docker run` polecenia z `-v <host directory>:/var/opt/mssql` flagą. Dzięki temu dane będą przywracane między wykonaniami kontenera.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge-developer
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge-developer
```

Ta technika umożliwia także udostępnianie i wyświetlanie plików na hoście poza platformą Docker.

> [!IMPORTANT]
> Mapowanie woluminu hosta dla **platformy Docker w systemie Windows** nie obsługuje obecnie mapowania kompletnego `/var/opt/mssql` katalogu. Można jednak zmapować podkatalog, na przykład `/var/opt/mssql/data` na maszynę hosta.

> [!IMPORTANT]
> Mapowanie woluminu hosta dla platformy **Docker na komputerze Mac** za pomocą obrazu usługi Azure SQL Edge nie jest teraz obsługiwane. Zamiast tego użyj kontenerów woluminów danych. To ograniczenie jest specyficzne dla `/var/opt/mssql` katalogu. Odczytywanie z zainstalowanego katalogu działa prawidłowo. Na przykład można zainstalować katalog hosta za pomocą-v na komputerze Mac i przywrócić kopię zapasową z pliku. bak, który znajduje się na hoście.

### <a name="use-data-volume-containers"></a>Korzystanie z kontenerów woluminów danych

Drugą opcją jest użycie kontenera woluminów danych. Kontener woluminów danych można utworzyć, określając nazwę woluminu zamiast katalogu hosta z `-v` parametrem. Poniższy przykład tworzy wolumin danych udostępnionych o nazwie **sqlvolume**.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge-developer
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge-developer
```

> [!NOTE]
> Ta technika niejawnie tworzenia ilości danych w poleceniu Run nie działa ze starszymi wersjami platformy Docker. W takim przypadku należy wykonać jawne kroki opisane w dokumentacji platformy Docker, [Tworzenie i Instalowanie kontenera woluminów danych](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container).

Nawet po zatrzymaniu i usunięciu tego kontenera wolumin danych będzie się utrzymywał. Można go wyświetlić za pomocą `docker volume ls` polecenia.

```bash
docker volume ls
```

Jeśli następnie utworzysz inny kontener o tej samej nazwie, nowy kontener będzie używać tych samych danych usługi Azure SQL Edge zawartych w tym woluminie.

Aby usunąć kontener woluminów danych, użyj `docker volume rm` polecenia.

> [!WARNING]
> Po usunięciu kontenera woluminów danych wszystkie dane usługi Azure SQL Edge w kontenerze zostaną *trwale* usunięte.


## <a name="next-steps"></a>Następne kroki

- [Łączenie z usługą Azure SQL Edge](connect.md)
- [Tworzenie kompleksowego rozwiązania IoT przy użyciu usługi SQL Edge](tutorial-deploy-azure-resources.md)
