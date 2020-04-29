---
title: Rozwiązywanie problemów z połączeniem usługi Windows Virtual Desktop — Azure
description: Jak rozwiązywać problemy podczas konfigurowania połączeń klienta w środowisku dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127446"
---
# <a name="windows-virtual-desktop-service-connections"></a>Połączenia usługi pulpitu wirtualnego systemu Windows

Ten artykuł służy do rozwiązywania problemów z połączeniami klienta pulpitu wirtualnego systemu Windows.

## <a name="provide-feedback"></a>Przekazywanie opinii

Możesz przekazać nam swoją opinię i omówić usługę pulpitów wirtualnych systemu Windows z zespołem produktu i innymi członkami społeczności w [systemie Windows — społeczność Tech](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Użytkownik nawiązuje połączenie, ale nic nie jest wyświetlane (brak źródła danych)

Użytkownik może uruchomić Pulpit zdalny klientów i może się uwierzytelnić, jednak użytkownik nie zobaczy żadnych ikon w źródle odnajdywania sieci Web.

Upewnij się, że użytkownik zgłaszał problemy, został przypisany do grup aplikacji przy użyciu tego wiersza polecenia:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Upewnij się, że użytkownik loguje się przy użyciu poprawnych poświadczeń.

Jeśli klient sieci Web jest używany, upewnij się, że nie występują problemy z buforowanymi poświadczeniami.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Wielosesyjne maszyny wirtualne z systemem Windows 10 Enterprise nie odpowiadają

Jeśli maszyna wirtualna nie jest w stanie reakcji i nie możesz uzyskać do niej dostępu za pomocą protokołu RDP, musisz rozwiązać ten problem przy użyciu funkcji diagnostyki, sprawdzając stan hosta.

Aby sprawdzić stan hosta, uruchom następujące polecenie cmdlet:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Jeśli stan hosta to `NoHeartBeat`, oznacza to, że maszyna wirtualna nie odpowiada, a agent nie może komunikować się z usługą pulpitu wirtualnego systemu Windows.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Istnieje kilka rzeczy, które można zrobić, aby naprawić stan nopuls.

### <a name="update-fslogix"></a>Aktualizacja FSLogix

Jeśli FSLogix nie jest aktualna, szczególnie jeśli jest to wersja 2.9.7205.27375 frxdrvvt. sys, może to spowodować zakleszczenie. Upewnij się [, że Zaktualizowano FSLogix do najnowszej wersji](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Wyłącz BgTaskRegistrationMaintenanceTask

Jeśli aktualizacja FSLogix nie działa, problem może polegać na tym, że składnik BiSrv powoduje wyczerpanie zasobów systemowych podczas cotygodniowych zadań konserwacyjnych. Tymczasowo wyłącz zadanie obsługi, wyłączając BgTaskRegistrationMaintenanceTask przy użyciu jednej z następujących dwóch metod:

- Przejdź do menu Start i Wyszukaj **harmonogram zadań**. Przejdź do **biblioteki** > **harmonogram zadań Microsoft** > **Windows** > **BrokerInfrastructure**. Poszukaj zadania o nazwie **BgTaskRegistrationMaintenanceTask**. Po znalezieniu go kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Wyłącz** z menu rozwijanego.
- Otwórz menu wiersza polecenia jako administrator i uruchom następujące polecenie:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia dzierżawy i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
