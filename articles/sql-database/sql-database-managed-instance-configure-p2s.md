---
title: Konfigurowanie wystąpienia zarządzanego przez P2S
description: Nawiąż połączenie z wystąpieniem zarządzanym Azure SQL Database przy użyciu SQL Server Management Studio przy użyciu połączenia typu punkt-lokacja z lokalnego komputera klienckiego.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 434b3d46d19138b494c9e29add3affb2eecc9c45
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773752"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Szybki Start: Konfigurowanie połączenia typu punkt-lokacja z wystąpieniem zarządzanym Azure SQL Database z lokalnego

Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączenia z Azure SQL Database wystąpieniem zarządzanym przy użyciu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) z lokalnego komputera klienckiego w ramach połączenia typu punkt-lokacja. Informacje o połączeniach typu punkt-lokacja można znaleźć w temacie [about the Point-to-site VPN](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start:

- Używa utworzonych zasobów [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md) jako punktu początkowego.
- Program wymaga programu PowerShell 5,1 i AZ PowerShell 1.4.0 lub nowszego na lokalnym komputerze klienckim. W razie potrzeby zapoznaj się z instrukcjami dotyczącymi [instalowania modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Wymaga najnowszej wersji programu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) na lokalnym komputerze klienckim.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Dołączanie bramy sieci VPN do sieci wirtualnej wystąpienia zarządzanego

1. Otwórz program PowerShell na lokalnym komputerze klienckim.

2. Skopiuj ten skrypt programu PowerShell. Ten skrypt dołącza VPN Gateway do sieci wirtualnej wystąpienia zarządzanego, która została utworzona w temacie [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md) — Szybki Start. Ten skrypt używa Azure PowerShell AZ module i wykona następujące czynności dla hostów opartych na systemie Windows lub Linux:

   - Tworzy i instaluje certyfikaty na komputerze klienckim
   - Oblicza zakres adresów IP przyszłych VPN Gateway podsieci
   - Tworzy GatewaySubnet
   - Wdraża szablon Azure Resource Manager, który dołącza VPN Gateway do podsieci sieci VPN

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. Wklej skrypt w oknie programu PowerShell i podaj wymagane parametry. Wartości dla `<subscriptionId>` , `<resourceGroup>` i `<virtualNetworkName>` powinny być zgodne z tymi, które zostały użyte do szybkiego startu [tworzenia wystąpienia zarządzanego](sql-database-managed-instance-get-started.md) . Wartość parametru `<certificateNamePrefix>` może być wybranym ciągiem.

4. Wykonaj skrypt programu PowerShell.

> [!IMPORTANT]
> Nie Kontynuuj do momentu zakończenia działania skryptu programu PowerShell.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Utwórz połączenie sieci VPN z wystąpieniem zarządzanym

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz grupę zasobów, w której została utworzona Brama sieci wirtualnej, a następnie otwórz zasób bramy sieci wirtualnej.
3. Wybierz pozycję **Konfiguracja punktu do lokacji** , a następnie wybierz pozycję **Pobierz klienta sieci VPN**.

    ![Pobierz klienta VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Na lokalnym komputerze klienckim Wyodrębnij pliki z pliku zip, a następnie otwórz folder przy użyciu wyodrębnionych plików.
5. Otwórz folder "**WindowsAmd64** " i Otwórz plik **VpnClientSetupAmd64. exe** .
6. Jeśli otrzymasz **chroniony komputer z systemem Windows** , kliknij pozycję **więcej informacji** , a następnie kliknij pozycję **Uruchom mimo to**.

    ![Instalowanie klienta VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. W oknie dialogowym Kontrola konta użytkownika kliknij przycisk **Tak**, aby kontynuować.
8. W oknie dialogowym odwołującym się do sieci wirtualnej wybierz pozycję **tak** , aby zainstalować klienta sieci VPN dla sieci wirtualnej.

## <a name="connect-to-the-vpn-connection"></a>Nawiązywanie połączenia z siecią VPN

1. Przejdź do sieci **VPN** w **sieci & Internet** na lokalnym komputerze klienckim i wybierz swoją sieć wirtualną wystąpienia zarządzanego, aby nawiązać połączenie z tą wirtualną. Na poniższej ilustracji Sieć wirtualna ma nazwę **MyNewVNet**.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Wybierz pozycję **Połącz**.
3. W oknie dialogowym wybierz pozycję **Połącz**.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Po wyświetleniu monitu, że Menedżer połączeń potrzebuje podwyższonego poziomu uprawnień do aktualizowania tabeli tras, wybierz pozycję **Kontynuuj**.
5. Aby kontynuować, wybierz pozycję **tak** w oknie dialogowym Kontrola konta użytkownika.

   Nawiązano połączenie sieci VPN z siecią wirtualną wystąpienia zarządzanego.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Łączenie się z wystąpieniem zarządzanym przy użyciu programu SSMS

1. Na lokalnym komputerze klienckim Otwórz SQL Server Management Studio (SSMS).
2. W oknie dialogowym **łączenie z serwerem** wprowadź w pełni kwalifikowaną **nazwę hosta** dla wystąpienia zarządzanego w polu **Nazwa serwera** .
3. Wybierz pozycję **uwierzytelnianie SQL Server**, podaj nazwę użytkownika i hasło, a następnie wybierz pozycję **Połącz**.

    ![nawiązywanie połączenia w programie ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po nawiązaniu połączenia możesz wyświetlić bazy danych systemu i użytkownika w węźle bazy danych. Można również wyświetlać różne obiekty w węzłach zabezpieczenia, obiekty serwera, replikacja, zarządzanie, Agent SQL Server i Profiler systemu XEvent.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z przewodnikiem Szybki Start pokazujący, jak nawiązać połączenie z maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie połączenia punkt-lokacja](sql-database-managed-instance-configure-p2s.md).
- Aby uzyskać omówienie opcji połączenia dla aplikacji, zobacz artykuł [Łączenie aplikacji z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md).
- Aby przywrócić istniejącą bazę danych SQL Server z lokalnego do wystąpienia zarządzanego, można użyć [Azure Database Migration Service (DMS) do migracji](../dms/tutorial-sql-server-to-managed-instance.md) lub [polecenia przywracania T-SQL](sql-database-managed-instance-get-started-restore.md) , aby przywrócić plik kopii zapasowej bazy danych.
