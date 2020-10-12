---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183173"
---
1. Po nawiązaniu połączenia z maszyną wirtualną przy użyciu pulpitu zdalnego wyszukaj pozycję **Menedżer konfiguracji**:

    ![Otwieranie Menedżera konfiguracji programu SQL Server](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. W Menedżerze konfiguracji programu SQL Server w okienku konsoli rozwiń pozycję **Konfiguracja sieciowa programu SQL Server**.

1. W okienku konsoli kliknij pozycję **Protokoły dla MSSQLSERVER** (domyślna nazwa wystąpienia). W okienku szczegółów kliknij prawym przyciskiem myszy pozycję **TCP** , a następnie kliknij pozycję **Włącz** , jeśli nie została jeszcze włączona.

    ![Włączanie protokołu TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. W okienku konsoli kliknij pozycję **Usługi programu SQL Server**. W okienku szczegółów kliknij prawym przyciskiem myszy pozycję **SQL Server (*Nazwa wystąpienia*)** (wystąpienie domyślne to **SQL Server (MSSQLSERVER)**), a następnie kliknij pozycję **Uruchom ponownie**, aby zatrzymać i ponownie uruchomić wystąpienie SQL Server.

    ![Ponowne uruchamianie aparatu bazy danych](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Zamknij Menedżera konfiguracji programu SQL Server.

Aby uzyskać więcej informacji na temat włączana protokołów dla aparatu bazy danych programu SQL Server, zobacz [Włączanie lub wyłączanie protokołu sieciowego serwera](https://msdn.microsoft.com/library/ms191294.aspx).
