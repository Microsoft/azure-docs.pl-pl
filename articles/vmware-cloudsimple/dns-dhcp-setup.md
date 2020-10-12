---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Konfigurowanie obciążeń DNS i DHCP dla chmury prywatnej
description: Opisuje sposób konfigurowania usług DNS i DHCP dla aplikacji i obciążeń działających w środowisku chmury prywatnej CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d7531a22d5b0f44fdaae837a75d17ea18852acf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87077289"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Konfigurowanie aplikacji DNS i DHCP oraz obciążeń w prywatnej chmurze CloudSimple

Aplikacje i obciążenia działające w środowisku chmury prywatnej wymagają rozpoznawania nazw i usług DHCP do wyszukiwania i przypisywania adresów IP.  Aby zapewnić te usługi, wymagana jest właściwa infrastruktura DHCP i DNS.  Można skonfigurować maszynę wirtualną, aby udostępnić te usługi w środowisku chmury prywatnej.  

## <a name="prerequisites"></a>Wymagania wstępne

* Grupa portów rozproszonych z konfiguracją sieci VLAN
* Kierowanie instalacji do lokalnych lub internetowych serwerów DNS
* Szablon maszyny wirtualnej lub plik ISO do utworzenia maszyny wirtualnej

## <a name="linux-based-dns-server-setup"></a>Konfiguracja serwera DNS z systemem Linux

System Linux oferuje różne pakiety do konfigurowania serwerów DNS.  Oto [Przykładowa konfiguracja z DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) z instrukcjami dotyczącymi KONFIGUROWANIA serwera DNS bind "open source".

## <a name="windows-based-setup"></a>Konfiguracja oparta na systemie Windows

W tych tematach firmy Microsoft opisano sposób konfigurowania serwera z systemem Windows jako serwera DNS i serwera DHCP.

* [Serwer z systemem Windows jako serwer DNS](/windows-server/networking/dns/dns-top)
* [Serwer z systemem Windows jako serwer DHCP](/windows-server/networking/technologies/dhcp/dhcp-top)
