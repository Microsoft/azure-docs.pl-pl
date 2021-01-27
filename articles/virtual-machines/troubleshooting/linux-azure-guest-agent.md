---
title: Rozwiązywanie problemów z agentem systemu Azure Linux
description: Rozwiązywanie problemów z agentem systemu Azure Linux.
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
ms.openlocfilehash: 62b462d8e75fc291ac599ac99dbe4fb3a74fde2b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878701"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Rozwiązywanie problemów z agentem systemu Azure Linux

[Agent systemu Azure Linux](../extensions/agent-linux.md) umożliwia maszynie wirtualnej komunikację z kontrolerem sieci szkieletowej (na serwerze fizycznym, na którym jest HOSTOWANA maszyna wirtualna) na adresie IP 168.63.129.16.

>[!NOTE]
>Ten adres IP jest wirtualnym publicznym adresem IP, który ułatwia komunikację i nie powinien być blokowany. Aby uzyskać więcej informacji, zobacz [co to jest adres IP 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Sprawdź stan i wersję agenta, aby upewnić się, że jest ono nadal obsługiwane. Zobacz [minimalną obsługę wersji dla agentów maszyny wirtualnej na platformie Azure](/troubleshoot/azure/virtual-machines/support-extensions-agent-version) , aby sprawdzić obsługę wersji, lub zobacz temat [WALinuxAgent — często zadawane pytania](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) , aby znaleźć informacje o stanie i wersji.

## <a name="troubleshoot-a-not-ready-status"></a>Rozwiązywanie problemów z niegotowym stanem

1. Sprawdź stan usługi agenta systemu Azure Linux, aby upewnić się, że jest uruchomiony. Nazwa usługi może być **walinuxagent** lub **waagent**.

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

   Jeśli usługa jest uruchomiona, uruchom ją ponownie, aby rozwiązać problem. Jeśli usługa jest zatrzymana, uruchom ją, odczekaj kilka minut, a następnie sprawdź stan ponownie.

1. Upewnij się, że funkcja autoaktualizacji jest włączona. Sprawdź ustawienie Autoaktualizacja w **/etc/waagent.conf**.

   ```
   AutoUpdate.Enabled=y
   ```

   Aby uzyskać więcej informacji na temat aktualizowania agenta systemu Azure Linux, zobacz [jak zaktualizować agenta systemu Linux platformy Azure na maszynie wirtualnej](../extensions/update-linux-agent.md).

1. Upewnij się, że maszyna wirtualna może połączyć się z kontrolerem sieci szkieletowej. Użyj narzędzia, takiego jak zwinięcie, aby sprawdzić, czy maszyna wirtualna może się połączyć z 168.63.129.16 na portach 80, 443 i 32526. Jeśli maszyna wirtualna nie nawiąże połączenia zgodnie z oczekiwaniami, sprawdź, czy w lokalnej zaporze na maszynie wirtualnej jest otwarta komunikacja wychodząca przez porty 80, 443 i 32526. Jeśli ten adres IP jest zablokowany, Agent maszyny wirtualnej może wyświetlić nieoczekiwane zachowanie.

## <a name="advanced-troubleshooting"></a>Zaawansowane rozwiązywanie problemów

Zdarzenia dotyczące rozwiązywania problemów z agentem systemu Azure Linux są rejestrowane w pliku **/var/log/waagent.log** .

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Nie można nawiązać połączenia z adresem IP WireServer (adres IP hosta)

Następujący błąd pojawia się w pliku **/var/log/waagent.log** , jeśli maszyna wirtualna nie może nawiązać połączenia z adresem IP WireServer na serwerze hosta.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

Aby rozwiązać ten problem:

* Połącz się z maszyną wirtualną przy użyciu protokołu SSH, a następnie spróbuj uzyskać dostęp do następującego adresu URL z programu zwinięcie: http://168.63.129.16/?comp=versions .
* Sprawdź, czy występują problemy, które mogą być spowodowane przez zaporę, serwer proxy lub inne źródło, które może blokować dostęp do adresu IP 168.63.129.16.
* Sprawdź, czy dołączenie iptables systemu Linux lub zapora innej firmy blokuje dostęp do portów 80, 443 i 32526.

## <a name="next-steps"></a>Następne kroki

Aby kontynuować rozwiązywanie problemów z agentem systemu Linux Azure, [skontaktuj się z pomocą techniczną firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).