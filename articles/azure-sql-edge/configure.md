---
title: Konfigurowanie usługi Azure SQL Edge (wersja zapoznawcza)
description: Informacje o konfigurowaniu usługi Azure SQL Edge (wersja zapoznawcza)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a28724e00f59fe049d1d9d6dfbcbc5a3f9556124
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235146"
---
# <a name="configure-azure-sql-edge-preview"></a>Konfigurowanie usługi Azure SQL Edge (wersja zapoznawcza)

Usługa Azure SQL Edge obsługuje konfigurację przy użyciu jednej z następujących dwóch opcji:

- Korzystanie ze zmiennych środowiskowych.
- Przy użyciu pliku MSSQL. conf umieszczonego w folderze/var/opt/MSSQL

> [!NOTE]
> Ustawianie zmiennych środowiskowych przesłania ustawienia określone w pliku MSSQL. conf.

## <a name="configure-using-environment-variables"></a>Konfigurowanie przy użyciu zmiennych środowiskowych

Usługa Azure SQL Edge uwidacznia kilka różnych zmiennych środowiskowych, których można użyć do skonfigurowania kontenera programu SQL Edge. Te zmienne środowiskowe są podzbiorem zmiennych środowiskowych dostępnych dla SQL Server on Linux. Aby uzyskać więcej informacji na temat zmiennych środowiskowych SQL Server on Linux, zobacz [zmienne środowiskowe](/sql/linux/sql-server-linux-configure-environment-variables/).

Następujące zmienne środowiskowe SQL Server on Linux nie są obsługiwane w przypadku usługi Azure SQL Edge. Jeśli zostały zdefiniowane, te zmienne środowiskowe zostaną zignorowane podczas inicjowania kontenera.

| Zmienna środowiskowa | Opis |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Włącz grupę dostępności. Na przykład wartość "1" jest włączona, a wartość "0" jest wyłączona |

> [!IMPORTANT]
> Zmienna środowiskowa *MSSQL_PID* dla programu SQL Edge akceptuje tylko wartości **Premium** i **Developer** , ponieważ są prawidłowymi wartościami. Usługa Azure SQL Edge nie obsługuje inicjowania przy użyciu klucza produktu.

> [!NOTE]
> Aby pobrać umowę licencyjną użytkownika końcowego usługi Azure SQL Edge, należy zapoznać się z [Umową licencjonowania użytkownika końcowego](https://go.microsoft.com/fwlink/?linkid=2128283).

### <a name="specifying-the-environment-variables"></a>Określanie zmiennych środowiskowych

Zmienne środowiskowe dla programu SQL Edge można określić podczas wdrażania usługi Azure SQL Edge za pomocą [Azure Portal](deploy-portal.md). Tę wartość można dodać w sekcji "zmienne środowiskowe" wdrożenia modułu lub w ramach opcji tworzenia kontenera, zgodnie z poniższym opisem.

*Ustawianie przy użyciu zmiennych środowiskowych — opcje*

![Ustawianie przy użyciu listy zmiennych środowiskowych](media/configure/set-environment-variables.png)

*Ustawianie przy użyciu opcji tworzenia kontenera*

![Ustawianie przy użyciu opcji tworzenia kontenera](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>Skonfiguruj przy użyciu pliku MSSQL. conf

Usługa Azure SQL Edge nie obejmuje [Narzędzia konfiguracji MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) , takiego jak SQL Server on Linux, ponieważ plik MSSQL. conf należy ręcznie skonfigurować i umieścić na dysku trwałego, który jest mapowany do folderu/var/opt/MSSQL/w module SQL Edge. Podczas wdrażania programu SQL Edge z poziomu portalu Azure Marketplace to mapowanie jest określone jako opcja * * mounts w opcji tworzenia kontenera

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
|**Opinie klientów** | Zdecyduj, czy SQL Server wysyłał Opinie do firmy Microsoft. |
|**Profil Poczta bazy danych** | Ustaw domyślny profil poczty bazy danych dla SQL Server on Linux. |
|**Wysoka dostępność** | Włącz grupy dostępności. |
|**Distributed Transaction Coordinator firmy Microsoft** | Konfigurowanie i rozwiązywanie problemów z usługą MSDTC w systemie Linux. Dodatkowe opcje konfiguracji związane z transakcjami rozproszonymi nie są również obsługiwane w programie SQL Edge. Aby uzyskać więcej informacji na temat dodatkowych opcji konfiguracji, zobacz [Konfigurowanie usługi MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) |
|**MLServices umowy licencyjne** | Akceptuj umowy EULA języków R i Python dla pakietów Machine Learning Services. Dotyczy tylko SQL Server 2019.|
|**outboundnetworkaccess** |Włącz wychodzący dostęp [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) do sieci dla rozszerzeń R, Python i Java.|

Przykładowy plik MSSQL. conf, który działa dla programu SQL Edge, znajduje się poniżej. Aby uzyskać więcej informacji na temat formatu pliku MSSQL. conf, zobacz [Format MSSQL. conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

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

## <a name="next-step"></a>Następny krok

- [Łączenie z usługą Azure SQL Edge](connect.md)
- [Tworzenie kompleksowego rozwiązania IoT przy użyciu programu SQL Edge](tutorial-deploy-azure-resources.md)
