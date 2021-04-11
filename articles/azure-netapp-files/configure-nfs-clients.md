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
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: e0b86a7014af42f2ffb067c2de797f270a5b1855
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967476"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Konfigurowanie klienta sieciowego systemu plików dla usługi Azure NetApp Files

Konfiguracja klienta NFS opisana w tym artykule jest częścią instalacji podczas [konfigurowania szyfrowania nfsv 4.1 Kerberos](configure-kerberos-encryption.md) lub [tworzenia dwuprotokołowego woluminu](create-volumes-dual-protocol.md). Wiele różnych dystrybucji systemu Linux jest dostępnych do użycia z Azure NetApp Files. W tym artykule opisano konfiguracje dla dwóch najczęściej używanych środowisk: RHEL 8 i Ubuntu 18,04. 

## <a name="requirements-and-considerations"></a>Wymagania i zagadnienia  

Niezależnie od używanej wersji systemu Linux wymagane są następujące konfiguracje:

* Skonfiguruj klienta NTP, aby uniknąć problemów z przechyleniem czasu.
* Skonfiguruj wpisy DNS klienta systemu Linux na potrzeby rozpoznawania nazw.  
    Ta konfiguracja musi zawierać rekord "A" (dalej) oraz rekord PTR (reverse). 
* W przypadku przyłączania do domeny Utwórz konto komputera dla klienta systemu Linux w Active Directory docelowej (który jest tworzony podczas przyłączania do obszaru). 
    > [!NOTE] 
    > `$SERVICEACCOUNT`Zmienna użyta w poniższych poleceniach powinna być kontem użytkownika z uprawnieniami lub delegowaniem w celu utworzenia konta komputera w jednostce organizacyjnej.

## <a name="rhel-8-configuration"></a>Konfiguracja RHEL 8 

W tej sekcji opisano konfiguracje RHEL wymagane w przypadku szyfrowania Kerberos NFSv 4.1 i Dual Protocol.  

W przykładach w tej sekcji użyto następującej nazwy domeny i adresu IP:  

* Nazwa domeny: `contoso.com`
* Prywatny adres IP: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>Konfiguracja RHEL 8 w przypadku korzystania z szyfrowania Kerberos NFSv 4.1

1. Skonfiguruj `/etc/resolv.conf` przy użyciu odpowiedniego serwera DNS.  

    Na przykład:  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. Dodaj rekord klienta NFS na serwerze DNS dla strefy wyszukiwania do przodu i do tyłu DNS.

3. Aby sprawdzić system DNS, użyj następujących poleceń z klienta NFS:   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. Zainstaluj pakiety:   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  Skonfiguruj klienta NTP.  

    RHEL 8 domyślnie używa chrony. Postępując zgodnie z wytycznymi dotyczącymi konfiguracji w [ramach `Chrony` zestawu, aby skonfigurować NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

6.  Dołącz do domeny Active Directory:  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    Na przykład: 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    Upewnij się, że `default_realm` jest ustawiony na podany obszar w `/etc/krb5.conf` .  Jeśli nie, Dodaj ją do `[libdefaults]` sekcji w pliku, jak pokazano w następującym przykładzie:
    
    `default_realm = CONTOSO.COM`

7. Uruchom ponownie wszystkie usługi NFS:  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    Ponowne uruchomienie zapobiega warunkowi błędu `“mount.nfs: an incorrect mount option was specified”` podczas instalacji protokołu Kerberos.

8. Uruchom `kinit` polecenie przy użyciu konta użytkownika, aby uzyskać bilety: 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    Na przykład:   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>Konfiguracja RHEL 8 w przypadku korzystania z podwójnego protokołu

Poniższe kroki są opcjonalne. Kroki te należy wykonać tylko w przypadku użycia mapowania użytkownika na kliencie NFS: 

1. Wykonaj wszystkie kroki opisane w [konfiguracji RHEL 8, jeśli używasz sekcji Szyfrowanie Kerberos nfsv 4.1](#rhel8_nfsv41_kerberos) .   

2. Dodaj statyczny rekord DNS w pliku/etc/hosts, aby użyć w pełni kwalifikowanej nazwy domeny (FQDN) dla usługi AD, zamiast korzystać z adresu IP w pliku konfiguracji SSSD:  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. Dodaj dodatkową sekcję dla domen w celu rozpoznania identyfikatorów z serwera usługi AD LDAP:    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   

4. Upewnij `/etc/nsswitch.conf` się, że masz `sss` wpis:   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. Uruchom ponownie `sssd` usługę i wyczyść pamięć podręczną:   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. Przetestuj, aby upewnić się, że klient jest zintegrowany z serwerem LDAP:   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Konfiguracja Ubuntu   

W tej sekcji opisano konfiguracje Ubuntu wymagane w przypadku szyfrowania Kerberos NFSv 4.1 i Dual Protocol. 

W przykładach w tej sekcji użyto następującej nazwy domeny i adresu IP:  

* Nazwa domeny: `contoso.com`
* Prywatny adres IP: `10.6.1.4`

1. Skonfiguruj `/etc/resolv.conf` przy użyciu odpowiedniego serwera DNS:

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. Dodaj rekord klienta systemu plików NFS na serwerze DNS dla strefy wyszukiwania do przodu i do tyłu DNS.
 
    Aby sprawdzić system DNS, użyj następujących poleceń z klienta NFS:

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. Zainstaluj pakiety:
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    Po wyświetleniu monitu wprowadź wartość wejściową `$DOMAIN.NAME` (na przykład używając wielkiej litery `CONTOSO.COM` ) jako domyślnego obszaru protokołu Kerberos.

4. Uruchom ponownie usługę `rpc-gssd.service` : 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18,04 domyślnie używa chrony. Zgodnie z zaleceniami dotyczącymi konfiguracji w [Ubuntu Bionic: używanie chrony do KONFIGUROWANIA NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

6. Dołącz do domeny Active Directory:   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    Na przykład:    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. Wykonaj `kinit` z użytkownikiem, aby uzyskać bilety: 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    Na przykład:    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>Konfiguracja Ubuntu w przypadku korzystania z podwójnego protokołu  

Poniższe kroki są opcjonalne.  Kroki te należy wykonać tylko wtedy, gdy chcesz użyć mapowania użytkownika na kliencie NFS:  

1. Uruchom następujące polecenie, aby uaktualnić zainstalowane pakiety:   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    W poniższym przykładzie zastosowano przykładowe wartości. Gdy polecenie monituje o dane wejściowe, należy podać dane wejściowe w oparciu o środowisko. 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. Uruchom następujące polecenie, aby ponownie uruchomić i włączyć usługę:

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

Poniższy przykład wysyła zapytanie do serwera LDAP AD z klienta Ubuntu LDAP dla użytkownika LDAP `‘hari1’` : 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>Następne kroki  

* [Tworzenie woluminu NFS dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Tworzenie dwuprotokołowego woluminu dla Azure NetApp Files](create-volumes-dual-protocol.md)

