---
title: Jak uaktualnić agenta zależności usługi Azure Monitor dla maszyn wirtualnych
description: W tym artykule opisano sposób uaktualniania agenta zależności usługi Azure Monitor dla maszyn wirtualnych przy użyciu wiersza polecenia, kreatora instalacji i innych metod.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: 01dd8422658aa0c8982733e48782efd27c1bf5be
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617846"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Jak uaktualnić agenta zależności usługi Azure Monitor dla maszyn wirtualnych

Po początkowym wdrożeniu agenta zależności usługi Azure Monitor dla maszyn wirtualnych są wydawane aktualizacje, które zawierają poprawki błędów lub obsługę nowych funkcji lub funkcji.  Ten artykuł pomaga zrozumieć dostępne metody i jak przeprowadzić uaktualnienie ręcznie lub za pośrednictwem automatyzacji.

## <a name="upgrade-options"></a>Opcje uaktualnienia 

Agent zależności dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie w zależności od scenariusza wdrażania i środowiska, w które działa komputer. Następujące metody mogą służyć do uaktualnienia agenta.

|Środowisko |Metoda instalacji |Metoda uaktualniania |
|------------|--------------------|---------------|
|Maszyna wirtualna platformy Azure | Rozszerzenie maszyny Wirtualnej agenta zależności dla [systemów Windows](../../virtual-machines/extensions/agent-dependency-windows.md) i [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | Agent jest automatycznie uaktualniany domyślnie, chyba że szablon usługi Azure Resource Manager został skonfigurowany do rezygnacji, ustawiając właściwość *autoUpgradeMinorVersion* na **false**. Uaktualnienie dla wersji pomocniczej, w której automatyczne uaktualnianie jest wyłączone, a uaktualnienie wersji głównej odbywa się tą samą metodą — odinstaluj i zainstaluj ponownie rozszerzenie. |
| Niestandardowe obrazy maszyn wirtualnych platformy Azure | Ręczna instalacja agenta zależności dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta musi być wykonywane z wiersza polecenia z uruchomionym pakietem instalatora Windows lub pakietem skryptów powłoki samorozłącznej i instalowalnej systemu Linux.|
| Maszyny wirtualne spoza platformy Azure | Ręczna instalacja agenta zależności dla systemu Windows/Linux | Aktualizowanie maszyn wirtualnych do najnowszej wersji agenta musi być wykonywane z wiersza polecenia z uruchomionym pakietem instalatora Windows lub pakietem skryptów powłoki samorozłącznej i instalowalnej systemu Linux. |

## <a name="upgrade-windows-agent"></a>Uaktualnienie agenta systemu Windows 

Aby zaktualizować agenta na maszynie Wirtualnej systemu Windows do najnowszej wersji, która nie została zainstalowana przy użyciu rozszerzenia maszyny Wirtualnej agenta zależności, należy uruchomić z wiersza polecenia, skryptu lub innego rozwiązania automatyzacji lub przy użyciu Kreatora instalacji InstallDependencyAgent-Windows.exe.  

Możesz pobrać najnowszą wersję agenta systemu Windows [tutaj](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Korzystanie z Kreatora instalacji

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Uruchom **program InstallDependencyAgent-Windows.exe,** aby uruchomić Kreatora instalacji.
   
3. Postępuj zgodnie z Kreatorem **instalacji agenta zależności,** aby odinstalować poprzednią wersję agenta zależności, a następnie zainstalować najnowszą wersję.


### <a name="from-the-command-line"></a>Z wiersza polecenia

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Uruchom następujące polecenie.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Parametr `/RebootMode=manual` zapobiega automatycznemu ponownemu uruchomieniu komputera przez uaktualnienie, jeśli niektóre procesy używają plików z poprzedniej wersji i mają na nich blokadę. 

3. Aby potwierdzić, że uaktualnienie zakończyło się pomyślnie, sprawdź `install.log` szczegółowe informacje o konfiguracji. Katalog dziennika to *%Programfiles%\Agent zależności firmy Microsoft\logi*.

## <a name="upgrade-linux-agent"></a>Uaktualnienie agenta Linuksa 

Uaktualnienie z wcześniejszych wersji agenta zależności w systemie Linux jest obsługiwane i wykonywane zgodnie z tym samym poleceniem co nowa instalacja.

Możesz pobrać najnowszą wersję agenta Linuksa [stąd](https://aka.ms/dependencyagentlinux).

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Uruchom następujące polecenie`sh InstallDependencyAgent-Linux64.bin -s`jako katalog główny . 

Jeśli agent zależności nie może uruchomić, sprawdź dzienniki szczegółowe informacje o błędzie. W agentach systemu Linux katalog dziennika to */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz zatrzymać monitorowanie maszyn wirtualnych przez pewien czas lub całkowicie usunąć usługę Azure Monitor dla maszyn wirtualnych, zobacz [Wyłączanie monitorowania maszyn wirtualnych w usłudze Azure Monitor dla maszyn wirtualnych.](vminsights-optout.md)
