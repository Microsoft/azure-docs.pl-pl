---
title: Rozwiązywanie problemów z agentem gościa platformy Azure Linux
description: Rozwiązywanie problemów z agentem gościa systemu Azure Linux nie działa
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: fc609b60c9d5d4d4734c3d73cbda87935b533caf
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500261"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Rozwiązywanie problemów z agentem gościa platformy Azure Linux

[Agent gościa platformy Azure dla systemu Linux](../extensions/agent-linux.md) jest agentem maszyny wirtualnej. Umożliwia ona MASZYNom wirtualnym komunikowanie się z kontrolerem sieci szkieletowej (podstawowy serwer fizyczny, na którym jest hostowana maszyna wirtualna) na adresie IP 168.63.129.16. Ten adres IP jest wirtualnym publicznym adresem IP, który ułatwia komunikację. Aby uzyskać więcej informacji, zobacz [co to jest adres IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="checking-agent-status-and-version"></a>Sprawdzanie stanu i wersji agenta

Zobacz https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Rozwiązywanie problemów z agentem maszyny wirtualnej w stanie niegotowości

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>Krok 1 Sprawdź, czy usługa agenta gościa platformy Azure dla systemu Linux jest uruchomiona

W zależności od dystrybucji nazwa usługi może mieć wartość walinuxagent lub waagent:

```
root@nam-u18:/home/nam# service walinuxagent status
● walinuxagent.service - Azure Linux Agent
   Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
 Main PID: 1036 (python3)
    Tasks: 4 (limit: 4915)
   CGroup: /system.slice/walinuxagent.service
           ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
           └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers

Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
root@nam-u18:/home/nam#
```


Jeśli są widoczne usługi i są uruchomione, należy ponownie uruchomić usługę, aby sprawdzić, czy problem został rozwiązany. Jeśli usługi są zatrzymane, uruchom je i poczekaj kilka minut. Następnie sprawdź, czy **stan agenta** to raportowanie jako **gotowe**. Aby rozwiązać te problemy, skontaktuj się z firmą [Pomoc techniczna firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-enabled"></a>Krok 2 Sprawdź, czy włączono funkcję autoaktualizacji

Zaznacz to ustawienie w/etc/waagent.conf:

```
AutoUpdate.Enabled=y
```

Aby uzyskać więcej informacji na temat aktualizowania agenta systemu Azure Linux, zapoznaj się z tematem https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Krok 3 Sprawdź, czy maszyna wirtualna może nawiązać połączenie z kontrolerem sieci szkieletowej

Użyj narzędzia takiego jak zwinięcie, aby sprawdzić, czy maszyna wirtualna może nawiązać połączenie z usługą 168.63.129.16 na portach 80, 32526 i 443. Jeśli maszyna wirtualna nie nawiąże połączenia zgodnie z oczekiwaniami, sprawdź, czy w lokalnej zaporze na maszynie wirtualnej jest otwarta komunikacja wychodząca przez porty 80, 443 i 32526. Jeśli ten adres IP jest zablokowany, agent maszyny wirtualnej może wyświetlać nieoczekiwane zachowanie w różnych scenariuszach.

## <a name="advanced-troubleshooting"></a>Zaawansowane rozwiązywanie problemów

Zdarzenia dotyczące rozwiązywania problemów z agentem gościa platformy Azure w systemie Linux są rejestrowane w następujących plikach dziennika:

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Nie można nawiązać połączenia z adresem IP WireServer (adres IP hosta) 

Zauważysz następujące wpisy błędów w programie/var/log/waagent.log:

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**Analizy**

Maszyna wirtualna nie może nawiązać połączenia z adresem IP WireServer na serwerze hosta.

**Rozwiązanie**

1. Ponieważ adres IP WireServer jest nieosiągalny, Połącz się z maszyną wirtualną przy użyciu protokołu SSH, a następnie spróbuj uzyskać dostęp do następującego adresu URL z programu zwinięcie: http://168.63.129.16/?comp=versions 
1. Sprawdź, czy występują problemy, które mogą być spowodowane przez zaporę, serwer proxy lub inne źródło, które mogłyby blokować dostęp do adresu IP 168.63.129.16.
1. Sprawdź, czy dołączenie iptables systemu Linux lub zapora innej firmy blokuje dostęp do portów 80, 443 i 32526. Aby uzyskać więcej informacji o tym, dlaczego ten adres nie powinien być zablokowany, zobacz [co to jest adres IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="next-steps"></a>Następne kroki

Aby jeszcze bardziej rozwiązać problem, [skontaktuj się z pomocą techniczną firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).