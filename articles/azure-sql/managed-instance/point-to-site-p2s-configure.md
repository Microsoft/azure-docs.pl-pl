---
title: Konfigurowanie połączenia punkt-lokacja za pomocą programu SSMS
titleSuffix: Azure SQL Managed Instance
description: Nawiązywanie połączenia z wystąpieniem zarządzanym usługi Azure SQL przy użyciu SQL Server Management Studio (SSMS) przy użyciu połączenia typu punkt-lokacja z lokalnego komputera klienckiego.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 3baf2d7ed7c326895ae40948fc2d0a4cc03021f9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788370"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>Szybki Start: Konfigurowanie połączenia punkt-lokacja z wystąpieniem zarządzanym usługi Azure SQL z lokalnego
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączenia z wystąpieniem zarządzanym usługi Azure SQL przy użyciu [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) z lokalnego komputera klienckiego za pośrednictwem połączenia typu punkt-lokacja. Informacje o połączeniach typu punkt-lokacja znajdują się w temacie [Informacje o sieci VPN typu punkt-lokacja](../../vpn-gateway/point-to-site-about.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start:

- Używa zasobów utworzonych w ramach  [tworzenia wystąpienia zarządzanego](instance-create-quickstart.md) jako punktu początkowego.
- Program wymaga programu PowerShell 5,1 i Azure PowerShell 1.4.0 lub nowszego na lokalnym komputerze klienckim. W razie potrzeby zapoznaj się z instrukcjami dotyczącymi [instalowania modułu Azure PowerShell](/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Wymaga najnowszej wersji [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) na lokalnym komputerze klienckim.

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>Dołączanie bramy sieci VPN do sieci wirtualnej

1. Otwórz program PowerShell na lokalnym komputerze klienckim.

2. Skopiuj ten skrypt programu PowerShell. Ten skrypt dołącza bramę sieci VPN do sieci wirtualnej wystąpienia zarządzanego SQL, która została utworzona w temacie [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md) — Szybki Start. Ten skrypt używa Azure PowerShell AZ module i wykonuje następujące czynności dla hostów opartych na systemie Windows lub Linux:

   - Tworzy i instaluje certyfikaty na komputerze klienckim
   - Oblicza zakres adresów IP podsieci bramy sieci VPN w przyszłości
   - Tworzy podsieć bramy
   - Wdraża szablon Azure Resource Manager, który łączy bramę sieci VPN z podsiecią sieci VPN

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

3. Wklej skrypt w oknie programu PowerShell i podaj wymagane parametry. Wartości dla `<subscriptionId>` , `<resourceGroup>` i `<virtualNetworkName>` powinny być zgodne z tymi, które zostały użyte do [utworzenia wystąpienia zarządzanego](instance-create-quickstart.md) — Szybki Start. Wartość parametru `<certificateNamePrefix>` może być wybranym ciągiem.

4. Wykonaj skrypt programu PowerShell.

> [!IMPORTANT]
> Nie Kontynuuj do momentu zakończenia działania skryptu programu PowerShell.

## <a name="create-a-vpn-connection"></a>Tworzenie połączenia sieci VPN

1. Zaloguj się do [Azure portal](https://portal.azure.com/).
2. Otwórz grupę zasobów, w której została utworzona Brama sieci wirtualnej, a następnie otwórz zasób bramy sieci wirtualnej.
3. Wybierz pozycję **Konfiguracja punktu do lokacji** , a następnie wybierz pozycję **Pobierz klienta sieci VPN** .

    ![Pobierz klienta VPN](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. Na lokalnym komputerze klienckim Wyodrębnij pliki z pliku zip, a następnie otwórz folder przy użyciu wyodrębnionych plików.
5. Otwórz folder **WindowsAmd64** i otwórz plik **VpnClientSetupAmd64.exe** .
6. Jeśli otrzymasz **chroniony komputer z systemem Windows** , kliknij pozycję **więcej informacji** , a następnie kliknij pozycję **Uruchom mimo to** .

    ![Instalowanie klienta VPN](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. W oknie dialogowym Kontrola konta użytkownika kliknij przycisk **Tak** , aby kontynuować.
8. W oknie dialogowym odwołującym się do sieci wirtualnej wybierz pozycję **tak** , aby zainstalować klienta sieci VPN dla sieci wirtualnej.

## <a name="connect-to-the-vpn-connection"></a>Nawiązywanie połączenia z siecią VPN

1. Przejdź do sieci **VPN** w obszarze **Sieć & Internet** na lokalnym komputerze klienckim i wybierz sieć wirtualną wystąpienia zarządzanego SQL, aby nawiązać połączenie z tą wirtualną. Na poniższej ilustracji Sieć wirtualna ma nazwę **MyNewVNet** .

    ![Połączenie VPN](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. Wybierz pozycję **Połącz** .
3. W oknie dialogowym wybierz pozycję **Połącz** .

    ![Zrzut ekranu, który podświetla przycisk Połącz.](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. Gdy zostanie wyświetlony monit, że Menedżer połączeń potrzebuje podniesionych uprawnień, aby zaktualizować tabelę tras, wybierz pozycję **Kontynuuj** .
5. Aby kontynuować, wybierz pozycję **tak** w oknie dialogowym Kontrola konta użytkownika.

   Nawiązano połączenie sieci VPN z siecią wirtualną wystąpienia zarządzanego SQL.

    ![Zrzut ekranu, który podświetla połączony komunikat, gdy połączenie zostało nawiązane.](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>Nawiązywanie połączenia z programem SSMS

1. Na lokalnym komputerze klienckim Otwórz SQL Server Management Studio.
2. W oknie dialogowym **łączenie z serwerem** wprowadź w pełni kwalifikowaną **nazwę hosta** dla wystąpienia zarządzanego w polu **Nazwa serwera** .
3. Wybierz pozycję **uwierzytelnianie SQL Server** , podaj nazwę użytkownika i hasło, a następnie wybierz pozycję **Połącz** .

    ![Łączenie programu SSMS](./media/point-to-site-p2s-configure/ssms-connect.png)  

Po nawiązaniu połączenia możesz wyświetlić bazy danych systemu i użytkownika w węźle bazy danych. Można również wyświetlać różne obiekty w węzłach zabezpieczenia, obiekty serwera, replikacja, zarządzanie, Agent SQL Server i Profiler systemu XEvent.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z przewodnikiem Szybki Start pokazujący, jak nawiązać połączenie z maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie połączenia punkt-lokacja](point-to-site-p2s-configure.md).
- Aby zapoznać się z omówieniem opcji połączenia dla aplikacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym SQL](connect-application-instance.md).
- Aby przywrócić istniejącą bazę danych SQL Server z lokalnego do wystąpienia zarządzanego, można użyć [Azure Database Migration Service do migracji](../../dms/tutorial-sql-server-to-managed-instance.md) lub [polecenia przywracania T-SQL](restore-sample-database-quickstart.md) , aby przywrócić plik kopii zapasowej bazy danych.