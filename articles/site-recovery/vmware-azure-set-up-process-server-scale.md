---
title: Skonfiguruj serwer przetwarzania skalowalny w poziomie podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych przy użyciu Azure Site Recovery | Microsoft Docs "
description: W tym artykule opisano sposób konfigurowania serwera przetwarzania skalowalnego w poziomie podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 40f912122e6ffb9cccbd32a747f6f0d46fd6c330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292797"
---
# <a name="scale-with-additional-process-servers"></a>Skalowanie przy użyciu dodatkowych serwerów przetwarzania

Domyślnie podczas replikowania maszyn wirtualnych VMware lub serwerów fizycznych na platformę Azure przy użyciu [Site Recovery](site-recovery-overview.md)serwer przetwarzania jest zainstalowany na komputerze serwera konfiguracji i służy do koordynowania transferu danych między Site Recovery i infrastrukturą lokalną. Aby zwiększyć pojemność i skalować wdrożenie replikacji, można dodać dodatkowe autonomiczne serwery procesów. W tym artykule opisano sposób konfigurowania serwera przetwarzania skalowalnego w poziomie.

## <a name="before-you-start"></a>Przed rozpoczęciem

### <a name="capacity-planning"></a>Planowanie pojemności

Upewnij się, że wykonano [Planowanie pojemności](site-recovery-plan-capacity-vmware.md) na potrzeby replikacji oprogramowania VMware. Dzięki temu można określić, jak i kiedy należy wdrożyć dodatkowe serwery przetwarzania.

W wersji 9,24 wskazówki są dodawane podczas wybierania serwera przetwarzania dla nowych replikacji. Serwer przetwarzania zostanie oznaczony jako zdrowy, ostrzegawczy i krytyczny na podstawie określonych kryteriów. Aby poznać różne scenariusze, które mogą mieć wpływ na stan serwera przetwarzania, przejrzyj [alerty serwera przetwarzania](vmware-physical-azure-monitor-process-server.md#process-server-alerts).

> [!NOTE]
> Użycie składnika sklonowanego serwera przetwarzania nie jest obsługiwane. Wykonaj kroki opisane w tym artykule dla każdej skalowania w poziomie PS.

### <a name="sizing-requirements"></a>Wymagania dotyczące ustalania wielkości 

Sprawdź wymagania dotyczące ustalania wielkości podsumowania w tabeli. Ogólnie rzecz biorąc, jeśli konieczne jest skalowanie wdrożenia na więcej niż 200 maszyn źródłowych lub łączny dzienny współczynnik zmian wynoszący więcej niż 2 TB, potrzebne są dodatkowe serwery przetwarzania do obsługi natężenia ruchu.

| **Dodatkowy serwer przetwarzania** | **Rozmiar dysku pamięci podręcznej** | **Szybkość zmian danych** | **Chronione maszyny** |
| --- | --- | --- | --- |
|4 procesorów wirtualnych vCPU (2 gniazda * 2 rdzenie \@ 2,5 GHz), 8 GB pamięci |300 GB |250 GB lub mniej |Replikacja maszyn z 85 lub mniej. |
|8 procesorów wirtualnych vCPU (2 gniazda * 4 rdzenie \@ 2,5 GHz), 12 GB pamięci |600 GB |250 GB do 1 TB |Replikacja między 85-150 maszynami. |
|12 procesorów wirtualnych vCPU (2 gniazda * 6 rdzeni \@ 2,5 GHz) 24 GB pamięci |1 TB |1 TB do 2 TB |Replikacja między 150-225 maszynami. |

Gdzie każda chroniona maszyna źródłowa jest skonfigurowana z 3 dyskami o 100 GB każdej z nich.

### <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące dodatkowego serwera przetwarzania zostały podsumowane w poniższej tabeli.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Pobierz plik instalacyjny

Pobierz plik instalacyjny dla serwera przetwarzania w następujący sposób:

1. Zaloguj się do Azure Portal i przejdź do magazynu Recovery Services.
2. Otwórz **Site Recovery infrastruktura**  >  **VMware i maszyny fizyczne**  >  **serwery konfiguracji** (w obszarze dla maszyn fizycznych VMware &).
3. Wybierz serwer konfiguracji, aby przejść do szczegółów serwera. Następnie kliknij pozycję **+ serwer przetwarzania**.
4. W obszarze **Dodawanie serwera przetwarzania**  >   **Wybierz lokalizację, w której chcesz wdrożyć serwer przetwarzania**, wybierz pozycję **Wdróż serwer procesów skalowalny w poziomie w środowisku lokalnym**.

   ![Strona dodawania serwerów](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Kliknij pozycję **pobierz Microsoft Azure Site Recovery ujednolicona konfiguracja**. Spowoduje to pobranie najnowszej wersji pliku instalacyjnego.

   > [!WARNING]
   > Wersja instalacji serwera przetwarzania powinna być taka sama, jak wersja serwera konfiguracji, która jest uruchomiona. Prostym sposobem zapewnienia zgodności wersji jest użycie tego samego Instalatora, który ostatnio został użyty do zainstalowania lub zaktualizowania serwera konfiguracji.

## <a name="install-from-the-ui"></a>Instalowanie z interfejsu użytkownika

Zainstaluj program w następujący sposób. Po skonfigurowaniu serwera należy zmigrować maszyny źródłowe, aby z niej korzystać.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Instalowanie z wiersza polecenia

Zainstaluj program, uruchamiając następujące polecenie:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMware/NonVMware>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Parametry wiersza polecenia są następujące:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Na przykład:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMware" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Utwórz plik ustawień serwera proxy

Jeśli trzeba skonfigurować serwer proxy, parametr ProxySettingsFilePath pobiera plik jako dane wejściowe. Plik można utworzyć w następujący sposób i przekazać go jako parametr wejściowy ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Następne kroki
Więcej informacji na temat [zarządzania ustawieniami serwera przetwarzania](vmware-azure-manage-process-server.md)
