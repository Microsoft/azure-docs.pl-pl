---
title: Serwer licencji Pulpit zdalny nie jest dostępny po nawiązaniu połączenia z maszyną wirtualną platformy Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z błędami protokołu RDP, ponieważ nie jest dostępny żaden serwer licencji Pulpit zdalny | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 276be92ea4c03b9ebeb3e13df69ce1b10328dcaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526473"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Serwer licencji Pulpit zdalny nie jest dostępny w przypadku łączenia się z maszyną wirtualną platformy Azure

Ten artykuł pomaga w rozwiązaniu problemu, gdy nie można nawiązać połączenia z maszyną wirtualną platformy Azure (VM), ponieważ nie jest dostępny żaden serwer licencji Pulpit zdalny w celu udostępnienia licencji.

## <a name="symptoms"></a>Objawy

Podczas próby nawiązania połączenia z maszyną wirtualną (VM) występują następujące scenariusze:

- Zrzut ekranu maszyny wirtualnej pokazuje, że system operacyjny jest w pełni załadowany i oczekuje na poświadczenia.
- Podczas próby nawiązania połączenia z protokołem Pulpit zdalny Microsoft (RDP) pojawia się następujący komunikat o błędzie:

  - Sesja zdalna została rozłączona z powodu braku dostępnych serwerów licencji Pulpit zdalny w celu udostępnienia licencji.

  - Nie jest dostępny żaden serwer licencji Pulpit zdalny. Usługi pulpitu zdalnego przestanie działać, ponieważ ten komputer osiągnął okres prolongaty i nie skontaktował się z co najmniej prawidłowym serwerem licencji systemu Windows Server 2008. Wybierz ten komunikat, aby otworzyć konfigurację serwera hosta sesji usług pulpitu zdalnego w celu użycia diagnostyki licencjonowania.

Można jednak nawiązać połączenie z maszyną wirtualną normalnie przy użyciu sesji administracyjnej:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Przyczyna

Ten problem występuje, gdy serwer licencji Pulpit zdalny jest niedostępny, aby zapewnić licencję na uruchomienie sesji zdalnej. Może to być spowodowane przez kilka scenariuszy, mimo że na maszynie wirtualnej skonfigurowano rolę hosta sesji Pulpit zdalny:

- W środowisku nigdy nie ma roli licencjonowania Pulpit zdalny i okres prolongaty 180 dni przekracza.
- Licencja na Pulpit zdalny została zainstalowana w środowisku, ale nigdy nie jest aktywowana.
- Licencja na Pulpit zdalny w środowisku nie ma licencji dostępu klienta (CAL) wprowadzonych w celu skonfigurowania połączenia.
- W środowisku zainstalowano licencję Pulpit zdalnyową. Dostępne są licencje CAL, ale nie zostały one prawidłowo skonfigurowane.
- Licencja na Pulpit zdalny ma licencje CAL i została aktywowana. Jednak niektóre inne problemy na serwerze licencji Pulpit zdalny uniemożliwiają mu udostępnianie licencji w środowisku programu.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj [kopię zapasową dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md) i wykonaj następujące kroki:

1. Połącz się z maszyną wirtualną przy użyciu sesji administracyjnej:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Jeśli nie możesz połączyć się z maszyną wirtualną przy użyciu sesji administracyjnej, możesz użyć [konsoli szeregowej maszyny wirtualnej na platformie Azure](serial-console-windows.md) , aby uzyskać dostęp do maszyny wirtualnej w następujący sposób:

    1. Uzyskaj dostęp do konsoli szeregowej, wybierając pozycję **Obsługa & Rozwiązywanie problemów**  >  **konsola szeregowa (wersja zapoznawcza)**. Jeśli funkcja jest włączona na maszynie wirtualnej, można połączyć maszynę wirtualną pomyślnie.

    2. Utwórz nowy kanał dla wystąpienia CMD. Wprowadź **polecenie cmd** w celu uruchomienia kanału i pobrania nazwy kanału.

    3. Przejdź do kanału, na którym jest uruchomione wystąpienie CMD. W takim przypadku powinna to być kanał 1:

       ```
       ch -si 1
       ```

    4. Wybierz pozycję **wprowadź** ponownie i Wprowadź prawidłową nazwę użytkownika i hasło, lokalny lub identyfikator domeny dla maszyny wirtualnej.

2. Sprawdź, czy maszyna wirtualna ma włączoną rolę hosta sesji Pulpit zdalny. Jeśli rola jest włączona, upewnij się, że działa prawidłowo. Otwórz wystąpienie CMD z podwyższonym poziomem uprawnień i wykonaj następujące kroki:

    1. Aby sprawdzić stan roli hosta sesji Pulpit zdalny, użyj następującego polecenia:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Jeśli to polecenie zwróci wartość 0, oznacza to, że rola jest wyłączona, a następnie można przejść do kroku 3.

    2. Użyj następującego polecenia, aby sprawdzić zasady i skonfigurować je ponownie w razie potrzeby:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Jeśli wartość **LicensingMode** jest ustawiona na dowolną wartość inną niż 4, na użytkownika, ustaw wartość na 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Jeśli wartość **SpecifiedLicenseServers** nie istnieje lub zawiera nieprawidłowe informacje o serwerze licencji, należy zmienić ją w następujący sposób:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Po wprowadzeniu zmian w rejestrze należy ponownie uruchomić maszynę wirtualną.

    4. Jeśli nie masz licencji CAL, Usuń rolę hosta sesji Pulpit zdalny. Następnie protokół RDP zostanie ustawiony na wartość normalny. Umożliwia tylko dwa współbieżne połączenia RDP z maszyną wirtualną:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Jeśli maszyna wirtualna ma rolę licencjonowania Pulpit zdalny i nie jest używana, można również usunąć tę rolę:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Upewnij się, że maszyna wirtualna może nawiązać połączenie z serwerem licencji Pulpit zdalny. Można przetestować połączenie z portem 135 między maszyną wirtualną a serwerem licencji: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Jeśli w środowisku nie ma Pulpit zdalny serwera licencji, możesz [zainstalować usługę roli licencjonowanie pulpit zdalny](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc731765(v=ws.11)). Następnie [Skonfiguruj Licencjonowanie usług pulpitu zdalnego](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Jeśli serwer licencji Pulpit zdalny jest skonfigurowany i jest w dobrej kondycji, upewnij się, że serwer licencji Pulpit zdalny został aktywowany z licencjami CAL.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby rozwiązać problem.
