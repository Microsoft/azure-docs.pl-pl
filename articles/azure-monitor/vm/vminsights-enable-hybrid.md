---
title: Włącz Azure Monitor środowiska hybrydowego
description: W tym artykule opisano sposób włączania usługi VM Insights dla hybrydowego środowiska chmury, które zawiera co najmniej jedną maszynę wirtualną.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 6518906f264077ac88a90513a237840f7f814247
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731276"
---
# <a name="enable-vm-insights-for-a-hybrid-virtual-machine"></a>Włączanie usługi VM Insights dla hybrydowej maszyny wirtualnej
W tym artykule opisano sposób włączania usługi VM Insights dla maszyny wirtualnej poza platformą Azure, w tym w środowiskach lokalnych i innych.

> [!IMPORTANT]
> Zalecana metoda włączania hybrydowych maszyn wirtualnych najpierw włącza [usługę Azure ARC dla serwerów](../../azure-arc/servers/overview.md) , dzięki czemu maszyny wirtualne można włączyć dla wglądu w dane maszyn wirtualnych przy użyciu procesów podobnych do maszyn wirtualnych platformy Azure. W tym artykule opisano sposób dołączania hybrydowych maszyn wirtualnych w przypadku zrezygnowania z używania usługi Azure Arc.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz i skonfiguruj obszar roboczy log Analytics](./vminsights-configure-workspace.md).
- Zobacz [obsługiwane systemy operacyjne](./vminsights-enable-overview.md#supported-operating-systems) , aby upewnić się, że jest obsługiwany system operacyjny maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. 


## <a name="overview"></a>Omówienie
Maszyny wirtualne poza platformą Azure wymagają tego samego agenta Log Analytics i agenta zależności, który jest używany na potrzeby maszyn wirtualnych platformy Azure. Ponieważ nie można użyć rozszerzeń maszyny wirtualnej do instalacji agentów, należy ręcznie zainstalować je w systemie operacyjnym gościa lub zainstalować przy użyciu innej metody. 

Aby uzyskać szczegółowe informacje na temat wdrażania agenta Log Analytics, zobacz [łączenie komputerów z systemem Windows w celu Azure monitor](../agents/agent-windows.md) lub [łączenia Azure monitor komputerów z systemem Linux](../agents/agent-linux.md) . Szczegóły dotyczące agenta zależności zostały podane w tym artykule. 

## <a name="firewall-requirements"></a>Wymagania dotyczące zapory
Wymagania dotyczące zapory dla agenta Log Analytics są dostępne w [omówieniu log Analytics agenta](../agents/log-analytics-agent.md#network-requirements). Agent zależności mapy usługi VM Insights nie przesyła samych danych i nie wymaga żadnych zmian w zaporach ani portach. Dane mapy są zawsze przesyłane przez agenta Log Analytics do usługi Azure Monitor, bezpośrednio lub przez [bramę pakietu Operations Management Suite](../../azure-monitor/agents/gateway.md) , jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem.


## <a name="dependency-agent"></a>Agent zależności

>[!NOTE]
>Poniższe informacje opisane w tej sekcji dotyczą również [rozwiązania Service map](./service-map.md).  

Agenta zależności można pobrać z następujących lokalizacji:

| Plik | System operacyjny | Wersja | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.7.12710 | CA29CC328F991D7301FD0360F4F56DF78275545BB8CDA853679899CA885E96F0  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.7.12710 | 98380DBEB2E2A5848F2202BC22422C68B20B62090C1BFC1DECAB37ED5451ED8C |


## <a name="install-the-dependency-agent-on-windows"></a>Zainstaluj agenta zależności w systemie Windows

Agenta zależności można zainstalować ręcznie na komputerach z systemem Windows, uruchamiając program `InstallDependencyAgent-Windows.exe` . Jeśli uruchomisz ten plik wykonywalny bez żadnych opcji, uruchamia Kreatora instalacji, który można wykonać, aby zainstalować agenta interaktywnie. Aby zainstalować lub odinstalować agenta, wymagane są uprawnienia *administratora* w systemie operacyjnym gościa.

W poniższej tabeli przedstawiono parametry, które są obsługiwane przez Instalatora dla agenta z wiersza polecenia.

| Parametr | Opis |
|:--|:--|
| /? | Zwraca listę opcji wiersza polecenia. |
| / S | Wykonuje instalację dyskretną bez interakcji ze strony użytkownika. |

Na przykład, aby uruchomić program instalacyjny z `/?` parametrem, wprowadź **InstallDependencyAgent-Windows.exe/?**.

Pliki dla agenta zależności systemu Windows są domyślnie instalowane w *katalogu C:\Program Files\Microsoft Agent zależności* . Jeśli nie można uruchomić agenta zależności po zakończeniu instalacji, zapoznaj się z dziennikami, aby uzyskać szczegółowe informacje o błędzie. Katalog dziennika to *%ProgramFiles%\Microsoft Dependency Agent\logs*.

### <a name="powershell-script"></a>Skrypt programu PowerShell
Aby pobrać i zainstalować agenta, użyj następującego przykładowego skryptu programu PowerShell:

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```


## <a name="install-the-dependency-agent-on-linux"></a>Instalowanie agenta zależności w systemie Linux

Agent zależności jest instalowany na serwerach z systemem Linux z *InstallDependencyAgent-linux64. bin*, skryptu powłoki z samowyodrębniającym się plikiem binarnym. Możesz uruchomić plik, używając `sh` lub dodając do samego pliku uprawnienia do wykonywania.

>[!NOTE]
> Aby zainstalować lub skonfigurować agenta, wymagany jest dostęp na poziomie administratora.
>

| Parametr | Opis |
|:--|:--|
| — Pomoc | Pobierz listę opcji wiersza polecenia. |
| -s | Przeprowadź instalację cichą bez monitowania użytkownika. |
| --Zaznacz | Sprawdź uprawnienia i system operacyjny, ale nie instaluj agenta. |

Na przykład aby uruchomić program instalacyjny z `-help` parametrem, wprowadź **InstallDependencyAgent-linux64. bin-help**. Zainstaluj agenta zależności systemu Linux jako element główny, uruchamiając polecenie `sh InstallDependencyAgent-Linux64.bin` .

Jeśli uruchomienie agenta zależności nie powiedzie się, Sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. W przypadku agentów systemu Linux katalog dziennika to */var/opt/Microsoft/Dependency-Agent/log*.

Pliki agenta zależności są umieszczane w następujących katalogach:

| Pliki | Lokalizacja |
|:--|:--|
| Pliki jądra | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/opt/microsoft/dependency-agent/log |
| Plik konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/opt/microsoft/dependency-agent/storage |

### <a name="shell-script"></a>Skrypt powłoki 
Aby pobrać i zainstalować agenta, użyj następującego przykładowego skryptu powłoki:

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

Aby wdrożyć agenta zależności przy użyciu konfiguracji żądanego stanu (DSC), można użyć modułu xPSDesiredStateConfiguration z następującym przykładowym kodem:

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```



## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="vm-doesnt-appear-on-the-map"></a>Maszyna wirtualna nie jest wyświetlana na mapie

Jeśli instalacja agenta zależności zakończyła się pomyślnie, ale nie widzisz komputera na mapie, należy zdiagnozować problem, wykonując następujące kroki.

1. Czy agent zależności został zainstalowany pomyślnie? Możesz to zweryfikować, sprawdzając, czy usługa jest zainstalowana i uruchomiona.

    **Windows**: Wyszukaj usługę o nazwie "Microsoft Dependency Agent".

    **Linux**: poszukaj uruchomionego procesu "Microsoft-Dependency-Agent".

2. Czy korzystasz z [warstwy cenowej bezpłatna log Analytics](../insights/solutions.md)? Plan bezpłatny pozwala na maksymalnie pięć unikatowych komputerów. Wszystkie kolejne komputery nie będą widoczne na mapie, nawet jeśli poprzednie pięć nie wyśle już danych.

3. Czy komputer wysyła dane dziennika i wydajności do dzienników Azure Monitor? Wykonaj następujące zapytanie dotyczące komputera:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Czy zwraca co najmniej jeden wynik? Czy są to świeże dane? W takim przypadku Agent Log Analytics działa prawidłowo i komunikuje się z usługą. Jeśli nie, sprawdź, czy Agent na serwerze: [log Analytics Agent na potrzeby rozwiązywania problemów z systemem Windows](../agents/agent-windows-troubleshoot.md) lub [agenta log Analytics do rozwiązywania problemów z systemem Linux](../agents/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Komputer jest wyświetlany na mapie, ale nie ma procesów

Jeśli widzisz serwer na mapie, ale nie ma on danych procesu ani połączenia, oznacza to, że Agent zależności został zainstalowany i uruchomiony, ale Sterownik jądra nie został załadowany.

Sprawdź plik C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) lub /var/opt/microsoft/dependency-agent/log/service.log (Linux). W ostatnich wierszach pliku powinna znajdować się odpowiedź, dlaczego nie załadowano jądra. Na przykład jądro może nie być obsługiwane w systemie Linux, jeśli zostało zaktualizowane.


## <a name="next-steps"></a>Następne kroki

Po włączeniu monitorowania dla maszyn wirtualnych te informacje są dostępne do analizy za pomocą usługi VM Insights.

- Aby wyświetlić odnalezione zależności aplikacji, zobacz [Wyświetlanie mapy usługi VM Insights](vminsights-maps.md).

- Aby identyfikować wąskie gardła i ogólne wykorzystanie z wydajnością maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).