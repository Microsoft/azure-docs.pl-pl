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
ms.date: 08/19/2020
ms.author: b-juche
ms.openlocfilehash: 4a54858f4a09cd62bb555a6df2c12efa2290dd69
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653175"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Konfigurowanie klienta sieciowego systemu plików dla usługi Azure NetApp Files

Wiele różnych dystrybucji systemu Linux jest dostępnych do użycia z Azure NetApp Files. W tym artykule opisano konfiguracje dla dwóch najczęściej używanych środowisk: RHEL 8 i Ubuntu 18,04. Konfiguracja klienta NFS opisana w tym artykule jest częścią instalacji podczas [konfigurowania szyfrowania nfsv 4.1 Kerberos](configure-kerberos-encryption.md) lub [tworzenia dwuprotokołowego woluminu](create-volumes-dual-protocol.md).  

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

