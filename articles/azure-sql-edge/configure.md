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
ms.date: 05/19/2020
ms.openlocfilehash: c38bb6100665cc9456b66608660bdca520b934c6
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636244"
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
|**MLServices umowy licencyjne** | Akceptuj umowy EULA języków R i Python dla pakietów Azure Machine Learning. Dotyczy tylko SQL Server 2019.|
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

## <a name="next-steps"></a>Następne kroki

- [Łączenie z usługą Azure SQL Edge](connect.md)
- [Tworzenie kompleksowego rozwiązania IoT przy użyciu usługi SQL Edge](tutorial-deploy-azure-resources.md)
