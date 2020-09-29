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
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449645"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Konfigurowanie klienta sieciowego systemu plików dla usługi Azure NetApp Files

Konfiguracja klienta NFS opisana w tym artykule jest częścią instalacji podczas [konfigurowania szyfrowania nfsv 4.1 Kerberos](configure-kerberos-encryption.md) lub [tworzenia dwuprotokołowego woluminu](create-volumes-dual-protocol.md). Wiele różnych dystrybucji systemu Linux jest dostępnych do użycia z Azure NetApp Files. W tym artykule opisano konfiguracje dla dwóch najczęściej używanych środowisk: RHEL 8 i Ubuntu 18,04. 

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
W tej sekcji opisano konfigurację Ubuntu dla klientów systemu plików NFS.  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>Jeśli używasz szyfrowania Kerberos w NFSv 4.1 

1. Zainstaluj pakiety:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Skonfiguruj klienta NTP.  
    Domyślnie Ubuntu 18,04 `chrony` .  Zgodnie z zaleceniami dotyczącymi konfiguracji w [Ubuntu Bionic: używanie chrony do KONFIGUROWANIA NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Dołącz do domena usługi Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>Jeśli używasz podwójnego protokołu  

1. Uruchom następujące polecenie, aby uaktualnić zainstalowane pakiety:  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Przykład:   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. Uruchom następujące polecenie, aby ponownie uruchomić i włączyć usługę:   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

Poniższy przykład wysyła zapytanie do serwera LDAP AD z klienta Ubuntu LDAP dla użytkownika LDAP `ldapu1` :   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>Następne kroki  

* [Tworzenie woluminu NFS dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Tworzenie dwuprotokołowego woluminu dla Azure NetApp Files](create-volumes-dual-protocol.md)

