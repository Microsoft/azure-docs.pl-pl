---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 51dc04fbef8d09878f33d7fda6f15039d3afba3e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563504"
---
### <a name="configure-a-dns-label-for-the-public-ip-address"></a>Konfigurowanie etykiety DNS dla publicznego adresu IP

Aby połączyć się z aparatem bazy danych programu SQL Server z Internetu, rozważ utworzenie etykiety DNS dla publicznego adresu IP. Możesz połączyć się przy użyciu adresu IP, ale etykieta DNS tworzy rekord A, który jest łatwiejszy do zidentyfikowania i streszcza podstawowy publiczny adres IP.

> [!NOTE]
> Etykiety DNS nie są wymagane, jeśli planujesz tylko połączenie z wystąpieniem programu SQL Server w ramach tej samej sieci wirtualnej lub połączenie lokalne.

Aby utworzyć etykietę DNS, wybierz najpierw **maszyny wirtualne** w portalu. Wybierz maszynę wirtualną programu SQL Server, aby wyświetlić jej właściwości.

1. W przeglądzie maszyny wirtualnej wybierz **publiczny adres IP**.

    ![publiczny adres IP](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

1. We właściwościach publicznego adresu IP rozwiń sekcję **Konfiguracja**.

1. Wprowadź nazwę etykiety DNS. Ta nazwa to rekord A, którego można użyć do nawiązania połączenia z maszyną wirtualną programu SQL Server przy użyciu nazwy, a nie bezpośrednio adresu IP.

1. Kliknij przycisk **Zapisz**.

    ![etykieta DNS](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Nawiązywanie połączenia z aparatem bazy danych z innego komputera

1. Na komputerze podłączonym do Internetu otwórz program SQL Server Management Studio (SSMS). Jeśli nie masz programu SQL Server Management Studio, możesz pobrać go [tutaj](/sql/ssms/download-sql-server-management-studio-ssms).

1. W oknie dialogowym **Connect to Server** (Łączenie z serwerem) lub **Connect to Database Engine** (Łączenie z aparatem bazy danych) edytuj wartość **Server name** (Nazwa serwera). Wprowadź adres IP lub pełną nazwę DNS maszyny wirtualnej (określoną w poprzednim zadaniu). Możesz także dodać przecinek i podać port TCP programu SQL Server. Na przykład `mysqlvmlabel.eastus.cloudapp.azure.com,1433`.

1. W polu **Authentication** (Uwierzytelnianie) wybierz opcję **SQL Server Authentication** (Uwierzytelnianie programu SQL Server).

1. W polu **Login** (Logowanie) wpisz nazwę prawidłowego identyfikatora logowania SQL.

1. W polu **Password** (Hasło) wpisz hasło logowania.

1. Kliknij przycisk **Podłącz**.

    ![nawiązywanie połączenia w programie ssms](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)