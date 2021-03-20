---
title: Rozwiązywanie problemów z woluminem Kerberos NFSv 4.1 dla Azure NetApp Files | Microsoft Docs
description: Opisuje komunikaty o błędach i rozwiązania, które mogą pomóc w rozwiązywaniu problemów z woluminem NFSv 4.1 Kerberos dla Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98134317"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>Rozwiązywanie problemów z woluminem Kerberos NFSv 4.1 

W tym artykule opisano rozwiązania przyczyn błędów, które mogą wystąpić podczas tworzenia woluminów Kerberos NFSv 4.1 lub zarządzania nimi. 

## <a name="error-conditions-and-resolutions"></a>Warunki błędów i rozwiązania

|     Warunki błędów    |     Rozwiązania    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files nie obsługuje protokołu Kerberos dla woluminów NFSv3. Protokół Kerberos jest obsługiwany tylko w przypadku protokołu NFSv 4.1.  |
|`This NetApp account has no configured Active Directory   connections`  |  Skonfiguruj Active Directory konta NetApp z polami adres IP i **Nazwa serwera usługi AD** **centrum dystrybucji kluczy** . Aby uzyskać instrukcje [, zobacz konfigurowanie Azure Portal](configure-kerberos-encryption.md#configure-the-azure-portal) . |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files nie obsługuje konwertowania zwykłego woluminu NFSv 4.1 na wolumin Kerberos NFSv 4.1 i na odwrót. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  Przykład: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> Upewnij się, że rekordy A/PTR są prawidłowo skonfigurowane i istnieją w Active Directory dla nazwy serwera `smb-test-64d9.contoso.com` . <br> W kliencie NFS, jeśli `nslookup` `smb-test-64d9.contoso.com` jest rozpoznawany jako adres IP IP1 (to oznacza, `10.1.1.68` ), `nslookup` IP1 musi rozpoznać tylko jeden rekord (to oznacza, `smb-test-64d9.contoso.com` ). `nslookup` elementu IP1 nie *może* rozpoznać wielu nazw. </li>  <li>Ustaw algorytm AES-256 dla konta komputera NFS typu `NFS-<Smb NETBIOS NAME>-<few random characters>` w usłudze AD przy użyciu programu PowerShell lub interfejsu użytkownika. <br> Przykładowe polecenia: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>Upewnij się, że godzina klienta NFS, usług AD i Azure NetApp Files Storage jest zsynchronizowana ze sobą i znajduje się w przedziale pięciu minut. </li>  <li>Pobierz bilet protokołu Kerberos na kliencie NFS przy użyciu polecenia `kinit <administrator>` .</li> <li>Skróć nazwę hosta klienta NFS do mniej niż 15 znaków i ponownie wykonaj sprzężenie obszaru. </li><li>Uruchom ponownie klienta NFS i `rpcgssd` usługę w następujący sposób. Polecenie może się różnić w zależności od systemu operacyjnego.<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu <br> (Uruchom ponownie `rpc-gssd` usługę). <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | Problem może być związany z problemem z klientem systemu plików NFS. Uruchom ponownie klienta systemu plików NFS.    |
|`Hostname lookup failed`   | Należy utworzyć strefę wyszukiwania wstecznego na serwerze DNS, a następnie dodać rekord PTR maszyny hosta usługi AD w tej strefie wyszukiwania wstecznego. <br> Załóżmy na przykład, że adres IP maszyny usługi AD to nazwa `10.1.1.4` hosta maszyny usługi AD (jak znaleziono przy użyciu polecenia hostname) `AD1` , a nazwa domeny to `contoso.com` . Rekord PTR dodany do strefy wyszukiwania wstecznego powinien mieć wartość `10.1.1.4 -> AD1.contoso.com` . |
|`Volume creation fails due to unreachable DNS server`  | Dostępne są dwa możliwe rozwiązania: <br> <ul><li> Ten błąd wskazuje, że usługa DNS jest nieosiągalna. Przyczyną może być nieprawidłowy adres IP DNS lub problem z siecią. Sprawdź adres IP DNS wprowadzony w polu połączenie usługi AD i upewnij się, że adres IP jest prawidłowy. </li> <li> Upewnij się, że usługi AD i wolumin znajdują się w tym samym regionie i w tej samej sieci wirtualnej. Jeśli znajdują się w różnych sieci wirtualnychach, upewnij się, że Komunikacja równorzędna sieci wirtualnej jest ustanowiona między tymi dwoma sieci wirtualnych. </li></ul> |
|Tworzenie woluminu Kerberos NFSv 4.1 kończy się niepowodzeniem z powodu błędu podobnego do poniższego przykładu: <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |Adres IP centrum dystrybucji kluczy jest nieprawidłowy i utworzono wolumin Kerberos. Zaktualizuj adres IP centrum dystrybucji kluczy przy użyciu poprawnego adresu. <br> Po zaktualizowaniu adresu IP centrum dystrybucji kluczy błąd nie zostanie wysunięty. Należy ponownie utworzyć wolumin. |

## <a name="next-steps"></a>Następne kroki  

* [Konfigurowanie szyfrowania Kerberos w systemie plików NFS 4.1 dla usługi Azure NetApp Files](configure-kerberos-encryption.md)
