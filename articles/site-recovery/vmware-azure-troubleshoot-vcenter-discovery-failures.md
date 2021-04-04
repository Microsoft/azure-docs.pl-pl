---
title: Rozwiązywanie problemów z błędami odnajdywania programu VMware vCenter w Azure Site Recovery
description: W tym artykule opisano sposób rozwiązywania problemów z błędami odnajdywania programu VMware vCenter w programie Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: 1a8471305af93194ccae7b0928685e10d4d64726
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366664"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Rozwiązywanie problemów z błędami odnajdywania vCenter Server

Ten artykuł pomaga w rozwiązywaniu problemów występujących w wyniku awarii programu VMware vCenter Discovery.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Wartości inne niż liczbowe we właściwości maxSnapShots

W wersjach wcześniejszych niż 9,20 program vCenter rozłącza się, gdy pobiera nieliczbową wartość  `snapshot.maxSnapShots` właściwości właściwości na maszynie wirtualnej.

Ten problem jest identyfikowany przez błąd o IDENTYFIKATORze 95126.

```output
ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
System.FormatException: Input string was not in a correct format.
    at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
    at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
    at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
```

W celu rozwiązania tego problemu:

- Zidentyfikuj maszynę wirtualną i ustaw wartość na wartość liczbową (ustawienia ustawień maszyny wirtualnej w programie vCenter).

Lub

- Uaktualnij serwer konfiguracji do wersji 9,20 lub nowszej.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemy z konfiguracją serwera proxy dla łączności vCenter

Odnajdowanie vCenter uwzględnia domyślne ustawienia serwera proxy systemu skonfigurowane przez użytkownika systemu. Usługa DRA przestrzega ustawień serwera proxy dostarczonych przez użytkownika podczas instalacji serwera konfiguracji za pomocą ujednoliconego Instalatora Instalatora lub szablonu komórki jajowe. 

Ogólnie rzecz biorąc, serwer proxy jest używany do komunikacji z sieciami publicznymi; na przykład komunikacja z platformą Azure. Jeśli serwer proxy jest skonfigurowany i program vCenter znajduje się w środowisku lokalnym, nie będzie mógł komunikować się z agentem DRA.

Podczas napotkania tego problemu występują następujące sytuacje:

- Serwer vCenter \<vCenter> jest nieosiągalny z powodu błędu: serwer zdalny zwrócił błąd: (503) serwer niedostępny
- Serwer vCenter \<vCenter> jest nieosiągalny z powodu błędu: serwer zdalny zwrócił błąd: nie można nawiązać połączenia z serwerem zdalnym.
- Nie można nawiązać połączenia z serwerem vCenter/ESXi.

W celu rozwiązania tego problemu:

Pobierz [Narzędzie PsExec](/sysinternals/downloads/psexec). 

Użyj narzędzia PsExec, aby uzyskać dostęp do kontekstu użytkownika systemu i określić, czy adres serwera proxy jest skonfigurowany. Następnie można dodać program vCenter do listy obejścia przy użyciu poniższych procedur.

W przypadku konfiguracji serwera proxy odnajdywania:

1. Otwórz program IE w kontekście użytkownika systemu przy użyciu narzędzia PsExec.
    
    PsExec-s-i "użycie ścieżki%ProgramFiles%\Internet Explorer\iexplore.exe"

2. Zmodyfikuj ustawienia serwera proxy w programie Internet Explorer, aby pominąć adres IP programu vCenter.
3. Uruchom ponownie usługę tmanssvc.

W przypadku konfiguracji serwera proxy agenta DRA:

1. Otwórz wiersz polecenia i Otwórz folder Microsoft Azure Site Recovery dostawcy.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery Provider**

3. W wierszu polecenia uruchom następujące polecenie.
   
   **DRCONFIGURATOR.EXE/Configure/AddBypassUrls [adres IP/nazwa FQDN vCenter Server podanego w momencie dodania programu vCenter]**

4. Uruchom ponownie usługę dostawcy DRA.

## <a name="next-steps"></a>Następne kroki

[Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii maszyny wirtualnej VMware](./vmware-azure-manage-configuration-server.md#refresh-configuration-server)