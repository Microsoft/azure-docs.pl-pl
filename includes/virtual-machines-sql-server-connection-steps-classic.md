---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 57f238a8f91df1271e91894b88a7f02118b1f123
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67183175"
---
### <a name="determine-the-dns-name-of-the-virtual-machine"></a>Określ nazwę DNS maszyny wirtualnej
Aby nawiązać połączenie z aparatem bazy danych SQL Server z innego komputera, należy znać nazwę systemu nazw domen (DNS) maszyny wirtualnej. (Jest to nazwa wykorzystywana przez Internet do identyfikowania maszyny wirtualnej. Można użyć adresu IP, ale adres IP może ulec zmianie, gdy platforma Azure przenosi zasoby pod kątem nadmiarowości lub konserwacji. Nazwa DNS będzie stabilna, ponieważ może zostać przekierowana na nowy adres IP.  

1. W witrynie Azure Portal (lub w poprzednim kroku) wybierz pozycję **maszyny wirtualne (klasyczne)**.
2. Wybierz maszynę wirtualną SQL.
3. W bloku **maszyna wirtualna** Skopiuj **nazwę DNS** maszyny wirtualnej.
   
    ![Nazwa DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Nawiązywanie połączenia z aparatem bazy danych z innego komputera
1. Na komputerze podłączonym do Internetu Otwórz SQL Server Management Studio.
2. W oknie dialogowym **łączenie z serwerem** lub **nawiązywanie połączenia z aparatem bazy danych** w polu **Nazwa serwera** wprowadź nazwę DNS maszyny wirtualnej (określoną w poprzednim zadaniu) i numer portu publicznego punktu końcowego w formacie *dnsname, numer_portu* , taki jak **mysqlvm. cloudapp. NET, 57500**.
   
    ![Nawiązywanie połączenia przy użyciu programu SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    Jeśli nie pamiętasz publicznego numeru portu punktu końcowego, który został wcześniej utworzony, możesz go znaleźć w obszarze **punkty końcowe** w bloku **maszyny wirtualnej** .
   
    ![Port publiczny](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. W polu **Authentication** (Uwierzytelnianie) wybierz opcję **SQL Server Authentication** (Uwierzytelnianie programu SQL Server).
4. W polu **Nazwa logowania** wpisz nazwę logowania, który został utworzony we wcześniejszej części zadania.
5. W polu **hasło** wpisz hasło nazwy logowania, która została utworzona w poprzednim zadaniu.
6. Kliknij pozycję **Połącz**.

