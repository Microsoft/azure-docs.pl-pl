---
title: Konfigurowanie usługi Azure SQL Edge
description: Dowiedz się więcej o konfigurowaniu usługi Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 0c49f5ab9f10456c32f7f8516cba0e851fa80e74
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392337"
---
# <a name="configure-azure-sql-edge"></a>Konfigurowanie usługi Azure SQL Edge

Usługa Azure SQL Edge obsługuje konfigurację przy użyciu jednej z następujących dwóch opcji:

- Zmienne środowiskowe
- Plik MSSQL. conf umieszczony w folderze/var/opt/MSSQL

> [!NOTE]
> Ustawianie zmiennych środowiskowych przesłania ustawienia określone w pliku MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Konfigurowanie przy użyciu zmiennych środowiskowych

Usługa Azure SQL Edge uwidacznia kilka różnych zmiennych środowiskowych, których można użyć do skonfigurowania kontenera programu SQL Edge. Te zmienne środowiskowe są podzbiorem tych, które są dostępne dla SQL Server on Linux. Aby uzyskać więcej informacji na temat zmiennych środowiskowych SQL Server on Linux, zobacz [zmienne środowiskowe](/sql/linux/sql-server-linux-configure-environment-variables/).

Następujące nowe zmienne środowiskowe zostały dodane do usługi Azure SQL Edge. 

| Zmienna środowiskowa | Opis | Wartości |     
|-----|-----| ---------- | 
| **Identyfikator planu** | Określa jednostkę SKU usługi Azure SQL Edge, która będzie używana podczas inicjowania. Ta zmienna środowiskowa jest wymagana tylko w przypadku wdrażania usługi Azure SQL Edge przy użyciu Azure IoT Edge. | **ASDE — Developer-Edge-IoT** lub **ASDE-Premium-on-IoT** | 
| **MSSQL_TELEMETRY_ENABLED** | Włącz lub Wyłącz zbieranie danych dotyczących użycia i diagnostyki. | TRUE lub FALSE |  
| **MSSQL_TELEMETRY_DIR** | Ustawia katalog docelowy dla plików inspekcji zbierania danych użycia i diagnostyki. | Lokalizacja folderu w kontenerze programu SQL Edge. Ten folder można zamapować na wolumin hosta przy użyciu punktów instalacji lub woluminów danych. | 
| **MSSQL_PACKAGE** | Określa lokalizację pakietu dacpac lub BACPAC, który ma zostać wdrożony. | Folder, plik lub adres URL sygnatury dostępu współdzielonego zawierający pakiety dacpac lub BACPAC. Aby uzyskać więcej informacji, zobacz [wdrażanie pakietów SQL Database dacpac i BACPAC w programie SQL Edge](deploy-dacpac.md). |


Następująca SQL Server on Linux zmienna środowiskowa nie jest obsługiwana w przypadku usługi Azure SQL Edge. Jeśli jest zdefiniowany, ta zmienna środowiskowa zostanie zignorowana podczas inicjowania kontenera.

| Zmienna środowiskowa | Opis |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Włącz grupę dostępności. Na przykład **1** jest włączona, a **0** jest wyłączone. |

> [!IMPORTANT]
> Zmienna środowiskowa **MSSQL_PID** dla programu SQL Edge akceptuje tylko wartości **Premium** i **Developer** , ponieważ są prawidłowymi wartościami. Usługa Azure SQL Edge nie obsługuje inicjowania przy użyciu klucza produktu.

### <a name="specify-the-environment-variables"></a>Określanie zmiennych środowiskowych

Określ zmienne środowiskowe dla programu SQL Edge podczas wdrażania usługi za pomocą [Azure Portal](deploy-portal.md). Można je dodać w sekcji **zmienne środowiskowe** wdrożenia modułu lub w ramach **opcji tworzenia kontenera**.

Dodaj wartości w **zmiennych środowiskowych**.

![Ustawianie za pomocą listy zmiennych środowiskowych](media/configure/set-environment-variables.png)

Dodaj wartości w obszarze **Opcje tworzenia kontenera**.

![Ustawianie przy użyciu opcji tworzenia kontenera](media/configure/set-environment-variables-using-create-options.png)

> [!NOTE]
> W trybie rozłączonym wdrożenia zmienne środowiskowe można określić za pomocą `-e` lub `--env` lub `--env-file` opcji `docker run` polecenia.

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

Dodano następujące nowe opcje MSSQL. conf dla usługi Azure SQL Edge. 

|Opcja|Opis|
|:---|:---|
|**customerfeedback** | Wybierz, czy SQL Server wysyła informacje zwrotne do firmy Microsoft. Aby uzyskać więcej informacji, zobacz [wyłączanie zbierania danych użycia i diagnostyki](usage-and-diagnostics-data-configuration.md#disable-usage-and-diagnostic-data-collection)|      
|**userrequestedlocalauditdirectory** | Ustawia katalog docelowy dla plików inspekcji zbierania danych użycia i diagnostyki. Aby uzyskać więcej informacji, zobacz [lokalne inspekcje zbierania danych użycia i diagnostyki](usage-and-diagnostics-data-configuration.md#local-audit-of-usage-and-diagnostic-data-collection) |        

Następujące opcje MSSQL. conf nie mają zastosowania do programu SQL Edge:

|Opcja|Opis|
|:---|:---|
|**Opinie klientów** | Wybierz, czy SQL Server wysyła informacje zwrotne do firmy Microsoft. |
|**Profil poczty bazy danych** | Ustaw domyślny profil poczty bazy danych dla SQL Server on Linux. |
|**Wysoka dostępność** | Włącz grupy dostępności. |
|**Distributed Transaction Coordinator firmy Microsoft** | Konfigurowanie i rozwiązywanie problemów z usługą MSDTC w systemie Linux. Dodatkowe opcje konfiguracji związane z transakcją rozproszoną nie są obsługiwane w programie SQL Edge. Aby uzyskać więcej informacji na temat dodatkowych opcji konfiguracji, zobacz [Konfigurowanie usługi MSDTC](/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**Umowy licencyjne usług ML** | Akceptuj umowy EULA języków R i Python dla pakietów Azure Machine Learning. Dotyczy tylko SQL Server 2019.|
|**outboundnetworkaccess** |Włącz wychodzący dostęp [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) do sieci dla rozszerzeń R, Python i Java.|

Następujący przykładowy plik MSSQL. conf działa dla programu SQL Edge. Aby uzyskać więcej informacji na temat formatu pliku MSSQL. conf, zobacz [Format MSSQL. conf](/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

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

Domyślnie kontenery usługi Azure SQL Edge są uruchamiane z niegłównym użytkownikiem/grupą. W przypadku wdrażania za pośrednictwem portalu Azure Marketplace (lub przy użyciu uruchomienia platformy Docker), jeśli nie określono innego użytkownika/grupy, kontenery usługi SQL Edge są uruchamiane jako użytkownik MSSQL (niebędący elementem głównym). Aby określić innego użytkownika niebędącego elementem głównym podczas wdrażania, Dodaj `*"User": "<name|uid>[:<group|gid>]"*` parę klucz-wartość w obszarze Opcje tworzenia kontenera. W poniższym przykładzie jest skonfigurowany do uruchamiania jako użytkownik programu SQL Edge `*IoTAdmin*` .

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

Wcześniej CTPs usługi Azure SQL Edge zostały skonfigurowane tak, aby były uruchamiane jako użytkownicy root. Podczas uaktualniania z wcześniejszych CTPs są dostępne następujące opcje.

- Kontynuuj korzystanie z użytkownika głównego — aby nadal korzystać z użytkownika root, Dodaj `*"User": "0:0"*` parę klucz-wartość w obszarze Opcje tworzenia kontenera.
- Użyj domyślnego użytkownika programu MSSQL — aby użyć domyślnego użytkownika MSSQL, wykonaj poniższe czynności.
  - Dodaj użytkownika o nazwie MSSQL na hoście platformy Docker. W poniższym przykładzie dodamy użytkownika MSSQL z IDENTYFIKATORem 10001. Ten użytkownik jest również dodawany do grupy głównej.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Zmiana uprawnienia do katalogu/woluminu instalacji, w którym znajduje się plik bazy danych 
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
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

Ta technika umożliwia także udostępnianie i wyświetlanie plików na hoście poza platformą Docker.

> [!IMPORTANT]
> Mapowanie woluminu hosta dla **platformy Docker w systemie Windows** nie obsługuje obecnie mapowania kompletnego `/var/opt/mssql` katalogu. Można jednak zmapować podkatalog, na przykład `/var/opt/mssql/data` na maszynę hosta.

> [!IMPORTANT]
> Mapowanie woluminu hosta dla platformy **Docker na komputerze Mac** za pomocą obrazu usługi Azure SQL Edge nie jest teraz obsługiwane. Zamiast tego użyj kontenerów woluminów danych. To ograniczenie jest specyficzne dla `/var/opt/mssql` katalogu. Odczytywanie z zainstalowanego katalogu działa prawidłowo. Na przykład można zainstalować katalog hosta za pomocą-v na komputerze Mac i przywrócić kopię zapasową z pliku. bak, który znajduje się na hoście.

### <a name="use-data-volume-containers"></a>Korzystanie z kontenerów woluminów danych

Drugą opcją jest użycie kontenera woluminów danych. Kontener woluminów danych można utworzyć, określając nazwę woluminu zamiast katalogu hosta z `-v` parametrem. Poniższy przykład tworzy wolumin danych udostępnionych o nazwie **sqlvolume**.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
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