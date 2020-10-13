---
title: Zarządzanie serwerem przetwarzania maszyn wirtualnych VMware/odzyskiwanie awaryjne serwera fizycznego w Azure Site Recovery
description: W tym artykule opisano zarządzanie serwerem przetwarzania na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware/serwerów fizycznych przy użyciu Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: a547a874c42d06d8453b154847561d8b5f0dabb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361396"
---
# <a name="manage-process-servers"></a>Zarządzanie serwerami przetwarzania

W tym artykule opisano typowe zadania związane z zarządzaniem serwerem przetwarzania Site Recovery.

Serwer przetwarzania służy do odbierania, optymalizowania i wysyłania danych replikacji na platformę Azure. Wykonuje także instalację wypychaną usługi mobilności na maszynach wirtualnych programu VMware i serwerach fizycznych, które mają być replikowane, i wykonuje automatyczne odnajdowanie maszyn lokalnych. W przypadku replikowania lokalnych maszyn wirtualnych VMware lub serwerów fizycznych na platformę Azure serwer przetwarzania jest instalowany domyślnie na komputerze serwera konfiguracji. 

- W przypadku dużych wdrożeń do skalowania pojemności mogą być potrzebne dodatkowe serwery przetwarzania lokalnego.
- W przypadku powrotu po awarii z platformy Azure do lokacji lokalnej należy skonfigurować tymczasowy serwer przetwarzania na platformie Azure. Możesz usunąć tę maszynę wirtualną po zakończeniu powrotu po awarii. 

Dowiedz się więcej o serwerze przetwarzania.


## <a name="upgrade-a-process-server"></a>Uaktualnianie serwera przetwarzania

Po wdrożeniu lokalnego serwera procesów lub jako maszyny wirtualnej platformy Azure na potrzeby powrotu po awarii jest zainstalowana najnowsza wersja serwera przetwarzania. Site Recovery zespoły wprowadzają poprawki i ulepszenia w regularnych odstępach czasu, a firma Microsoft zaleca utrzymywanie Aktualności serwerów przetwarzania. Serwer przetwarzania można uaktualnić w następujący sposób:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Przenoszenie maszyn wirtualnych w celu zrównoważenia obciążenia serwera przetwarzania

Należy zrównoważyć obciążenie przez przeniesienie maszyn wirtualnych między dwoma serwerami przetwarzania w następujący sposób:

1. W magazynie w obszarze **Zarządzaj** kliknij **Site Recovery infrastruktura**. W obszarze **dla maszyn fizycznych VMware &** kliknij pozycję **serwery konfiguracji**.
2. Kliknij serwer konfiguracji, z którym są zarejestrowane serwery przetwarzania.
3. Kliknij serwer przetwarzania, dla którego chcesz równoważyć obciążenie ruchem.

    ![Zrzut ekranu przedstawia serwer przetwarzania, dla którego można równoważyć obciążenie ruchem.](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Kliknij pozycję **równoważenie obciążenia**i wybierz docelowy serwer przetwarzania, do którego chcesz przenieść maszyny. Następnie kliknij przycisk **OK** .

    ![Zrzut ekranu przedstawia okienko równoważenia obciążenia z wybranym docelowym serwerem przetwarzania.](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Kliknij pozycję **Wybierz Maszyny**, a następnie wybierz maszyny, które chcesz przenieść z bieżącego do docelowego serwera przetwarzania. Szczegóły średniej zmiany danych są wyświetlane dla każdej maszyny wirtualnej. Następnie kliknij przycisk **OK**. 
3. W magazynie Monitoruj postęp zadania w obszarze **monitorowanie**  >  **Site Recovery zadań**.

Wprowadzenie zmian w portalu zajmie około 15 minut. Aby uzyskać szybszy efekt, [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Przełączanie całego obciążenia na inny serwer przetwarzania

Przenieś całe obciążenie obsługiwane przez serwer przetwarzania na inny serwer przetwarzania w następujący sposób:

1. W magazynie w obszarze **Zarządzaj** kliknij **Site Recovery infrastruktura**. W obszarze **dla maszyn fizycznych VMware &** kliknij pozycję **serwery konfiguracji**.
2. Kliknij serwer konfiguracji, z którym są zarejestrowane serwery przetwarzania.
3. Kliknij serwer przetwarzania, z którego chcesz przełączyć obciążenie.
4. Kliknij pozycję **przełącznik**, wybierz docelowy serwer przetwarzania, do którego chcesz przenieść obciążenie. Następnie kliknij przycisk **OK** .

    ![Zrzut ekranu przedstawia okienko Wybieranie docelowego serwera przetwarzania.](media/vmware-azure-manage-process-server/Switch.PNG)

5. W magazynie Monitoruj postęp zadania w obszarze **monitorowanie**  >  **Site Recovery zadań**.

Wprowadzenie zmian w portalu zajmie około 15 minut. Aby uzyskać szybszy efekt, [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>Rejestrowanie głównego serwera docelowego

Główny serwer docelowy znajduje się na serwerze konfiguracji i skalowalnych w poziomie serwerach procesów. Musi być zarejestrowany na serwerze konfiguracji. W przypadku awarii tej rejestracji może to mieć wpływ na kondycję chronionych elementów. Aby zarejestrować główny serwer docelowy z serwerem konfiguracji, zaloguj się na konkretnym serwerze konfiguracji/skalowalnym w poziomie serwerze przetwarzania, na którym jest wymagana rejestracja. Przejdź do folderu **%ProgramData%\ASR\Agent**, a następnie uruchom następujące polecenie w wierszu polecenia administratora.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Ponowne zarejestrowanie serwera przetwarzania

Zarejestruj ponownie serwer przetwarzania działający lokalnie lub na maszynie wirtualnej platformy Azure z serwerem konfiguracji w następujący sposób:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Po zapisaniu ustawień wykonaj następujące czynności:

1. Na serwerze przetwarzania Otwórz wiersz polecenia z uprawnieniami administratora.
2. Przejdź do folderu **%ProgramData%\ASR\Agent**i uruchom polecenie:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modyfikowanie ustawień serwera proxy dla lokalnego serwera przetwarzania

Jeśli lokalny serwer przetwarzania używa serwera proxy do łączenia się z platformą Azure, można zmodyfikować ustawienia serwera proxy w następujący sposób:

1. Zaloguj się do maszyny serwera przetwarzania. 
2. Otwórz okno poleceń administracyjnych programu PowerShell i uruchom następujące polecenie:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Przejdź do folderu **%ProgramData%\ASR\Agent**i uruchom następujące polecenie:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Usuń serwer przetwarzania

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Wykluczanie folderów z oprogramowania antywirusowego

Jeśli oprogramowanie antywirusowe jest uruchomione na serwerze przetwarzania skalowalnego w poziomie (lub głównym serwerze docelowym), Wyklucz następujące foldery z operacji programu antywirusowego:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Katalog instalacji serwera przetwarzania. Na przykład: C:\Program Files (x86) \Microsoft Azure Site Recovery