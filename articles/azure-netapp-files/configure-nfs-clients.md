---
title: Konfigurowanie klienta NFS dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób konfigurowania klientów NFS do użycia z Azure NetApp Files.
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
ms.topic: how-to
ms.date: 07/27/2020
ms.author: b-juche
ms.openlocfilehash: bdcffcea8d695b6a3d49272ffa135187d77aae1e
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535482"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Konfigurowanie klienta NFS dla Azure NetApp Files

Wiele różnych dystrybucji systemu Linux jest dostępnych do użycia z Azure NetApp Files. W tym artykule opisano konfiguracje dla dwóch najczęściej używanych środowisk: RHEL 8 i Ubuntu 18,04.

Niezależnie od używanej wersji systemu Linux wymagane są następujące konfiguracje:
* Skonfiguruj klienta NTP, aby uniknąć problemów z przechyleniem czasu.
* Skonfiguruj wpisy DNS klienta systemu Linux na potrzeby rozpoznawania nazw.  
    Ta konfiguracja obejmuje rekord "A" (dalej) oraz rekord PTR (reverse). 
* W przypadku przyłączania do domeny Utwórz konto komputera w Active Directory docelowym (które jest tworzone podczas przyłączania do obszaru). 
    > [!NOTE] 
    > `$SERVICEACCOUNT`Zmienna użyta w poniższych poleceniach powinna być kontem użytkownika z uprawnieniami lub delegowaniem w celu utworzenia konta komputera w jednostce organizacyjnej.
* Umożliwienie Klientowi instalowania woluminów NFS i innych odpowiednich narzędzi do monitorowania.

## <a name="rhel-8-configuration"></a>Konfiguracja RHEL 8 

1. Zainstaluj pakiety:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. Skonfiguruj klienta NTP:  
    RHEL 8 `chrony` domyślnie używa.  [Aby skonfigurować NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp), należy przestrzegać wytycznych dotyczących konfiguracji w temacie using the chrony Suite.

3. Dołącz do domeny Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Konfiguracja Ubuntu 

1. Zainstaluj pakiety:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Skonfiguruj klienta NTP.  
    Domyślnie Ubuntu 18,04 `chrony` .  Zgodnie z zaleceniami dotyczącymi konfiguracji w [Ubuntu Bionic: używanie chrony do KONFIGUROWANIA NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Dołącz do domena usługi Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>Następne kroki  

* [Tworzenie woluminu NFS dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Tworzenie dwuprotokołowego woluminu dla Azure NetApp Files](create-volumes-dual-protocol.md)

