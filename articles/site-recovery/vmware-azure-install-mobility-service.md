---
title: Przygotuj maszyny źródłowe, aby zainstalować usługę mobilności za pośrednictwem instalacji wypychanej w celu odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure | Microsoft Docs
description: Dowiedz się, jak przygotować serwer do instalacji agenta mobilności za pośrednictwem instalacji wypychanej na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 78fddb5b4512883f8e78d6ed53f6e3dbbeba0e4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90525001"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Przygotuj maszynę źródłową do instalacji wypychanej agenta mobilności

Po skonfigurowaniu odzyskiwania po awarii dla maszyn wirtualnych VMware i serwerów fizycznych przy użyciu [Azure Site Recovery](site-recovery-overview.md)należy zainstalować [usługę mobilności Site Recovery](vmware-physical-mobility-service-overview.md) na wszystkich lokalnych maszynach wirtualnych VMware i na serwerze fizycznym.  Usługa mobilności przechwytuje operacje zapisu danych na komputerze i przekazuje je do serwera przetwarzania Site Recovery.

## <a name="install-on-windows-machine"></a>Zainstaluj na komputerze z systemem Windows

Na każdej maszynie z systemem Windows, która ma być chroniona, wykonaj następujące czynności:

1. Upewnij się, że istnieje połączenie sieciowe między komputerem a serwerem przetwarzania. Jeśli nie skonfigurowano oddzielnego serwera przetwarzania, domyślnie jest on uruchomiony na serwerze konfiguracji.
1. Utwórz konto, za pomocą którego serwer przetwarzania będzie mógł uzyskać dostęp do komputera. Konto powinno mieć uprawnienia administratora, lokalne lub domeny. Użyj tego konta tylko w przypadku instalacji wypychanej i aktualizacji agenta.
2. Jeśli nie korzystasz z konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na komputerze lokalnym w następujący sposób:
    - W obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System klucz rejestru Dodaj nową wartość DWORD: **LocalAccountTokenFilterPolicy**. Ustaw wartość na **1**.
    -  Aby to zrobić, w wierszu polecenia Uruchom następujące polecenie:
    
       ```
       REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
       ```

3. W zaporze systemu Windows na komputerze, który chcesz chronić, wybierz opcję **Zezwalaj aplikacji lub funkcji przez zaporę**. Włącz **udostępnianie plików i drukarek** oraz **Instrumentacja zarządzania Windows (WMI)**. W przypadku komputerów należących do domeny możesz skonfigurować ustawienia zapory przy użyciu obiektu zasady grupy (GPO).

   ![Ustawienia zapory](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Dodaj konto utworzone w narzędziu CSPSConfigtool. W tym celu zaloguj się do serwera konfiguracji.
5. Otwórz plik **cspsconfigtool.exe**. Jest on dostępny jako skrót na pulpicie i w folderze%ProgramData%\ASR\home\svsystems\bin.
6. Na karcie **Zarządzanie kontami** wybierz pozycję **Dodaj konto**.
7. Dodaj utworzone konto.
8. Wprowadź używane poświadczenia po włączeniu replikacji dla komputera.

## <a name="install-on-linux-machine"></a>Zainstaluj na komputerze z systemem Linux

Na każdym komputerze z systemem Linux, który ma zostać objęty ochroną, wykonaj następujące czynności:

1. Upewnij się, że istnieje połączenie sieciowe między komputerem z systemem Linux a serwerem przetwarzania.
2. Utwórz konto, za pomocą którego serwer przetwarzania będzie mógł uzyskać dostęp do komputera. Konto powinno być użytkownikiem **root** na źródłowym serwerze z systemem Linux. Użyj tego konta tylko w przypadku instalacji wypychanej i aktualizacji.
3. Sprawdź, czy plik /etc/hosts na źródłowym serwerze z systemem Linux zawiera wpisy mapujące lokalną nazwę hosta na adres IP skojarzony ze wszystkimi kartami sieciowymi.
4. Zainstaluj najnowsze pakiety openssh, openssh-server i openssl na komputerze, który chcesz replikować.
5. Upewnij się, że protokół Secure Shell (SSH) jest włączony i uruchomiony na porcie 22.
4. Włącz podsystem SFTP i uwierzytelnianie hasłem w pliku sshd_config. W tym celu zaloguj się jako **element główny**.
5. W pliku **sshd_config/etc/ssh/** Znajdź wiersz zaczynający się od **PasswordAuthentication**.
6. Usuń komentarz z wiersza i zmień wartość na **tak**.
7. Znajdź wiersz zaczynający się od **podsystemu**i usuń znaczniki komentarza z wiersza.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Uruchom ponownie usługę **sshd**.
9. Dodaj konto utworzone w narzędziu CSPSConfigtool. W tym celu zaloguj się do serwera konfiguracji.
10. Otwórz plik **cspsconfigtool.exe**. Jest on dostępny jako skrót na pulpicie i w folderze%ProgramData%\home\svsystems\bin.
11. Na karcie **Zarządzanie kontami** wybierz pozycję **Dodaj konto**.
12. Dodaj utworzone konto.
13. Wprowadź używane poświadczenia po włączeniu replikacji dla komputera.
1. Dodatkowy krok dotyczący aktualizowania lub ochrony SUSE Linux Enterprise Server 11 z dodatkiem SP3 lub RHEL 5 lub CentOS 5 lub Debian 7. Upewnij się, że [Najnowsza wersja jest dostępna na serwerze konfiguracji](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).

## <a name="anti-virus-on-replicated-machines"></a>Oprogramowanie antywirusowe na replikowanych maszynach

Jeśli na maszynach, które mają być replikowane, działa aktywne oprogramowanie chroniące przed wirusami, upewnij się, że folder instalacji usługi mobilności został wykluczony z operacji programu antywirusowego (*C:\ProgramData\ASR\agent*). Dzięki temu replikacja działa zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu usługi mobilności w Azure Portal wybierz pozycję **+ Replikuj** , aby rozpocząć ochronę tych maszyn wirtualnych. Dowiedz się więcej na temat włączania replikacji dla [maszyn wirtualnych VMware](vmware-azure-enable-replication.md) i [serwerów fizycznych](physical-azure-disaster-recovery.md#enable-replication).


