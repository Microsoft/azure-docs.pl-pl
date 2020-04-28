---
title: Microsoft Teams na pulpicie wirtualnym systemu Windows — Azure
description: Jak korzystać z programu Microsoft Teams na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187532"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Korzystanie z programu Microsoft Teams na pulpicie wirtualnym systemu Windows

> Dotyczy: Windows 10 i Windows 10 IoT Enterprise

Zwirtualizowane środowiska przedstawiają unikatowy zestaw wyzwań dla aplikacji do współpracy, takich jak Microsoft Teams, w tym zwiększone opóźnienia, wysokie użycie procesora CPU hosta oraz niska wydajność dźwięku i wideo. Aby dowiedzieć się więcej o korzystaniu z zespołów Microsoft Teams w środowiskach VDI, zapoznaj się z [zespołami dla zwirtualizowanej infrastruktury klasycznej](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z programu Microsoft Teams na pulpicie wirtualnym systemu Windows, należy wykonać następujące czynności:

- Zainstaluj [klienta klasycznego systemu Windows](connect-windows-7-and-10.md) na urządzeniu z systemem Windows 10, które spełnia [wymagania sprzętowe](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)zespołów Microsoft Teams.
- Nawiązywanie połączenia z maszyną wirtualną z systemem Windows 10 lub systemem Windows 10 Enterprise.
- [Przygotuj sieć](https://docs.microsoft.com/microsoftteams/prepare-network) dla programu Microsoft Teams.

## <a name="use-unoptimized-microsoft-teams"></a>Korzystanie z niezoptymalizowanych zespołów firmy Microsoft

Możesz użyć zespołów Microsoft Teams w swoich środowiskach pulpitu wirtualnego systemu Windows, aby skorzystać z funkcji rozmowy i współpracy w usłudze Microsoft Teams. Pulpit wirtualny systemu Windows nie obsługuje zespołów w przypadku optymalizacji audio/wideo (AV) infrastruktury VDI. Wywołania i spotkania nie są obsługiwane. Jeśli zasady organizacji zezwalają na to, można nadal wykonywać wywołania audio i dołączać do spotkań z dźwiękiem, ale niezoptymalizowana jakość dźwięku jest różna w zależności od konfiguracji hosta i może nie być niezawodna. Aby dowiedzieć się więcej, zobacz [zespoły z uwzględnieniem wydajności infrastruktury VDI](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations).

### <a name="prepare-your-image-for-teams"></a>Przygotowanie obrazu dla zespołów

Aby włączyć instalację zespołów dla poszczególnych komputerów, należy dla hosta ustawić następujący klucz rejestru:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Zainstaluj program Microsoft Teams

Aplikację Team Desktop można wdrożyć przy użyciu instalacji na komputerze. Aby zainstalować program Microsoft Teams w środowisku pulpitu wirtualnego systemu Windows:

1. Pobierz [pakiet MSI Teams](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) , który jest zgodny z Twoim środowiskiem. Zalecamy używanie Instalatora 64-bitowego w 64-bitowym systemie operacyjnym.
2. Uruchom to polecenie, aby zainstalować plik MSI na maszynie wirtualnej hosta.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Spowoduje to zainstalowanie zespołów do plików programu lub plików programu (x86). Przy następnym zalogowaniu i rozpoczęciu zespołów aplikacja będzie pytać o Twoje poświadczenia.

      > [!NOTE]
      > Użytkownicy i Administratorzy nie mogą wyłączyć automatycznego uruchamiania dla zespołów podczas logowania.

      Aby odinstalować plik MSI z maszyny wirtualnej hosta, uruchom następujące polecenie:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Jeśli instalujesz zespoły z ustawieniem MSI ALLUSER = 1, aktualizacje automatyczne zostaną wyłączone. Zalecamy konieczność aktualizacji zespołów co najmniej raz w miesiącu. Aby dowiedzieć się więcej o wdrażaniu aplikacji klasycznych zespołów, zapoznaj się z tematem [Wdróż aplikację Team Desktop na maszynie wirtualnej](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Dostosowywanie Remote Desktop Protocol właściwości dla puli hostów
Dostosowanie właściwości Remote Desktop Protocol puli hostów (RDP), takich jak środowisko monitorowania z obsługą połączeń mikrofonu i audio, pozwala zapewnić użytkownikom optymalne środowisko na podstawie ich potrzeb. Właściwości protokołu RDP można dostosować na pulpicie wirtualnym systemu Windows przy użyciu parametru **-CustomRdpProperty** w poleceniu cmdlet **Set-RdsHostPool** .
Zobacz [obsługiwane ustawienia plików RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) , aby uzyskać pełną listę obsługiwanych właściwości i ich wartości domyślne.
