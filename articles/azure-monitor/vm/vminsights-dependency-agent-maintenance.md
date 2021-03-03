---
title: Jak uaktualnić agenta zależności usługi VM Insights
description: W tym artykule opisano sposób uaktualniania agenta zależności usługi VM Insights przy użyciu wiersza polecenia, Kreatora instalacji i innych metod.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: 14ca614c1e108ce43f05f98520cff6ff215ec5cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722912"
---
# <a name="how-to-upgrade-the-vm-insights-dependency-agent"></a>Jak uaktualnić agenta zależności usługi VM Insights

Po początkowym wdrożeniu agenta zależności usługi VM Insights zostaną wydane aktualizacje zawierające poprawki błędów lub obsługę nowych funkcji lub funkcji.  Ten artykuł ułatwia zapoznanie się z dostępnymi metodami oraz jak przeprowadzić uaktualnienie ręcznie lub za pomocą automatyzacji.

## <a name="upgrade-options"></a>Opcje uaktualniania 

Agenta zależności dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie, w zależności od scenariusza wdrażania i środowiska, w którym jest uruchomiona maszyna. Do uaktualnienia agenta można użyć poniższych metod.

|Środowisko |Metoda instalacji |Metoda uaktualniania |
|------------|--------------------|---------------|
|Maszyna wirtualna platformy Azure | Rozszerzenie maszyny wirtualnej agenta zależności dla [systemów Windows](../../virtual-machines/extensions/agent-dependency-windows.md) i [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | Agent jest automatycznie uaktualniany, chyba że skonfigurowano szablon Azure Resource Manager, aby zrezygnował z ustawienia właściwości *włączoną flagą autoupgrademinorversion* na **wartość false**. Uaktualnienie wersji pomocniczej, w której funkcja autouaktualnienia jest wyłączona, a uaktualnienie wersji głównej jest zgodne z tą samą metodą — Odinstaluj i ponownie zainstaluj rozszerzenie. |
| Niestandardowe obrazy maszyn wirtualnych platformy Azure | Ręczna instalacja agenta zależności dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta należy wykonać z poziomu wiersza polecenia z uruchomionym pakietem Instalatora Windows lub z samowyodrębniającym się zestawem skryptów powłoki programu Linux.|
| Maszyny wirtualne spoza platformy Azure | Ręczna instalacja agenta zależności dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta należy wykonać z poziomu wiersza polecenia z uruchomionym pakietem Instalatora Windows lub z samowyodrębniającym się zestawem skryptów powłoki programu Linux. |

## <a name="upgrade-windows-agent"></a>Uaktualnij agenta systemu Windows 

Aby zaktualizować agenta na maszynie wirtualnej z systemem Windows do najnowszej wersji, która nie jest zainstalowana przy użyciu rozszerzenia maszyny wirtualnej agenta zależności, można uruchomić polecenie z wiersza polecenia, skryptu lub innego rozwiązania do automatyzacji lub przy użyciu Kreatora instalacji InstallDependencyAgent-Windows.exe.  

W [tym miejscu](https://aka.ms/dependencyagentwindows)możesz pobrać najnowszą wersję agenta systemu Windows.

### <a name="using-the-setup-wizard"></a>Korzystanie z Kreatora instalacji

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Wykonaj **InstallDependencyAgent-Windows.exe** , aby uruchomić Kreatora instalacji.
   
3. Wykonaj czynności opisane w kreatorze **instalacji agent zależności** , aby odinstalować poprzednią wersję agenta zależności, a następnie zainstalować najnowszą wersję.


### <a name="from-the-command-line"></a>Z wiersza polecenia

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Uruchom następujące polecenie.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Ten `/RebootMode=manual` parametr uniemożliwia automatyczne ponowne uruchomienie komputera przez proces uaktualniania, jeśli niektóre procesy używają plików z poprzedniej wersji i mają blokadę. 

3. Aby upewnić się, że uaktualnienie zakończyło się pomyślnie, sprawdź `install.log` szczegółowe informacje dotyczące instalacji. Katalog dziennika to *%ProgramFiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Uaktualnij agenta systemu Linux 

Uaktualnienie z wcześniejszych wersji agenta zależności w systemie Linux jest obsługiwane i wykonywane przy użyciu tego samego polecenia, co w przypadku nowej instalacji.

W [tym miejscu](https://aka.ms/dependencyagentlinux)możesz pobrać najnowszą wersję agenta systemu Linux.

1. Zaloguj się na komputerze przy użyciu konta z uprawnieniami administracyjnymi.

2. Uruchom następujące polecenie jako element główny `sh InstallDependencyAgent-Linux64.bin -s` . 

Jeśli uruchomienie agenta zależności nie powiedzie się, Sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. W przypadku agentów systemu Linux katalog dziennika to */var/opt/Microsoft/Dependency-Agent/log*. 

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz zatrzymać monitorowanie maszyn wirtualnych przez pewien czas lub całkowicie usunąć informacje o maszynie wirtualnej, zobacz temat [wyłączanie monitorowania maszyn wirtualnych w usłudze VM Insights](../vm/vminsights-optout.md).
