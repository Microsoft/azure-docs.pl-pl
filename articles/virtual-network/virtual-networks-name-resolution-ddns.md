---
title: Rejestrowanie nazw hostów na platformie Azure przy użyciu dynamicznego systemu DNS | Microsoft Docs
description: Informacje na temat konfigurowania dynamicznego systemu DNS w celu rejestrowania nazw hostów na własnych serwerach DNS.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: ad91eb94aedcdd0e4e715162e3ae064a1d2fb1ea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220426"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Używanie dynamicznej usługi DNS do rejestrowania nazw hostów na własnym serwerze DNS

[Platforma Azure udostępnia rozpoznawanie nazw](virtual-networks-name-resolution-for-vms-and-role-instances.md) dla maszyn wirtualnych i wystąpień ról. Jeśli rozpoznawanie nazw wymaga przekroczenia możliwości udostępnionych przez domyślny system DNS platformy Azure, możesz udostępnić własne serwery DNS. Użycie własnych serwerów DNS pozwala dostosować rozwiązanie DNS do własnych potrzeb. Na przykład może być konieczne uzyskanie dostępu do zasobów lokalnych za pośrednictwem Active Directory kontrolera domeny.

Gdy niestandardowe serwery DNS są hostowane jako maszyny wirtualne platformy Azure, można przesłać zapytania hosta dla tej samej sieci wirtualnej na platformę Azure w celu rozpoznania nazw hostów. Jeśli nie chcesz używać tej opcji, możesz zarejestrować nazwy hostów maszyn wirtualnych na serwerze DNS przy użyciu dynamicznego systemu DNS (DDNS). Platforma Azure nie ma poświadczeń do bezpośredniego tworzenia rekordów na serwerach DNS, więc często potrzebne są alternatywne rozwiązania. Niektóre typowe scenariusze z alternatywą są następujące:

## <a name="windows-clients"></a>Klienci z systemem Windows
Klienci z systemem Windows przyłączeni do domeny próbują wykonać niezabezpieczone aktualizacje DDNS podczas rozruchu lub w przypadku zmiany ich adresów IP. Nazwa DNS jest nazwą hosta i sufiksem podstawowej domeny DNS. Platforma Azure pozostawia sufiks podstawowej domeny DNS pustej, ale można ustawić sufiks w maszynie wirtualnej za pomocą [interfejsu użytkownika](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794784(v=ws.10)) lub [programu PowerShell](/powershell/module/dnsclient/set-dnsclient).

Przyłączone do domeny klienci systemu Windows rejestrują adresy IP przy użyciu kontrolera domeny za pomocą protokołu Secure DDNS. Proces przyłączania do domeny ustawia sufiks podstawowej domeny DNS na kliencie i tworzy i utrzymuje relację zaufania.

## <a name="linux-clients"></a>Klienci z systemem Linux
Klienci z systemem Linux zazwyczaj nie rejestrują się na serwerze DNS podczas uruchamiania, zakładają, że serwer DHCP go wykonuje. Serwery DHCP platformy Azure nie mają poświadczeń do rejestrowania rekordów na serwerze DNS. Do wysyłania aktualizacji DDNS można użyć narzędzia o nazwie `nsupdate` , które jest zawarte w pakiecie bind. Ponieważ protokół DDNS jest ustandaryzowany, można go używać `nsupdate` nawet wtedy, gdy nie używasz funkcji bind na serwerze DNS.

Możesz użyć punktów zaczepienia dostarczonych przez klienta DHCP, aby utworzyć i zachować wpis nazwy hosta na serwerze DNS. Podczas cyklu DHCP Klient wykonuje skrypty w */etc/DHCP/dhclient-Exit-Hooks.d/*. Możesz użyć punktów zaczepienia, aby zarejestrować nowy adres IP przy użyciu `nsupdate` . Na przykład:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

Możesz również użyć polecenia, `nsupdate` Aby wykonać bezpieczne aktualizacje DDNS. Na przykład podczas korzystania z serwera DNS BIND jest generowana pary kluczy publiczny-prywatny ( `http://linux.yyz.us/nsupdate/` ). Serwer DNS jest skonfigurowany ( `http://linux.yyz.us/dns/ddns-server.html` ) z publiczną częścią klucza, dzięki czemu może weryfikować podpis w żądaniu. Aby zapewnić parę kluczy `nsupdate` , użyj `-k` opcji, aby żądanie DDNS Update było podpisane.

Jeśli używasz serwera DNS systemu Windows, możesz użyć uwierzytelniania Kerberos z `-g` parametrem w `nsupdate` , ale nie jest on dostępny w wersji systemu Windows `nsupdate` . Aby użyć protokołu Kerberos, użyj, `kinit` Aby załadować poświadczenia. Na przykład można załadować poświadczenia z [pliku plik KEYTAB](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), a następnie pobrać `nsupdate -g` poświadczenia z pamięci podręcznej.

W razie potrzeby można dodać sufiks wyszukiwania DNS do maszyn wirtualnych. Sufiks DNS jest określony w pliku */etc/resolv.conf* . Większość dystrybucje systemu Linux zarządza zawartością tego pliku, więc zazwyczaj nie można go edytować. Można jednak zastąpić sufiks przy użyciu polecenia klienta DHCP `supersede` . Aby zastąpić sufiks, Dodaj następujący wiersz do pliku */etc/DHCP/dhclient.conf* :

```
supersede domain-name <required-dns-suffix>;
```