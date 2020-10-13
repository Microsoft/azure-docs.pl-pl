---
title: Przyłączanie maszyny wirtualnej RHEL do Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować maszynę wirtualną Red Hat Enterprise Linux i przyłączyć ją do domeny zarządzanej Azure AD Domain Services.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: joflore
ms.openlocfilehash: ffde2317ea66a15936df75c4fe2455f627ea6e98
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962126"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Przyłączanie maszyny wirtualnej Red Hat Enterprise Linux do domeny zarządzanej Azure Active Directory Domain Services

Aby umożliwić użytkownikom logowanie się do maszyn wirtualnych na platformie Azure przy użyciu jednego zestawu poświadczeń, możesz dołączyć maszyny wirtualne do domeny zarządzanej Azure Active Directory Domain Services (AD DS platformy Azure). Po dołączeniu maszyny wirtualnej do domeny zarządzanej AD DS platformy Azure można użyć kont użytkowników i poświadczeń z domeny, aby zalogować się i zarządzać serwerami. Członkostwa w grupach z domeny zarządzanej są również stosowane w celu umożliwienia kontroli dostępu do plików lub usług na maszynie wirtualnej.

W tym artykule opisano sposób przyłączania maszyny wirtualnej z systemem Red Hat Enterprise Linux (RHEL) do domeny zarządzanej.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności pierwszy samouczek [tworzy i konfiguruje domenę zarządzaną Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Konto użytkownika, które jest częścią domeny zarządzanej.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>Tworzenie maszyny wirtualnej z systemem RHEL Linux i nawiązywanie z nią połączenia

Jeśli masz istniejącą maszynę wirtualną z systemem Linux RHEL na platformie Azure, Połącz się z nią przy użyciu protokołu SSH, a następnie przejdź do następnego kroku, aby [rozpocząć konfigurowanie maszyny wirtualnej](#configure-the-hosts-file).

Jeśli musisz utworzyć maszynę wirtualną z systemem Linux RHEL lub chcesz utworzyć testową maszynę wirtualną do użycia z tym artykułem, możesz użyć jednej z następujących metod:

* [Witryna Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Podczas tworzenia maszyny wirtualnej należy zwrócić uwagę na ustawienia sieci wirtualnej, aby upewnić się, że maszyna wirtualna może komunikować się z domeną zarządzaną:

* Wdróż maszynę wirtualną w tej samej lub równorzędnej sieci wirtualnej, w której włączono Azure AD Domain Services.
* Wdróż maszynę wirtualną w innej podsieci niż Azure AD Domain Services domeny zarządzanej.

Po wdrożeniu maszyny wirtualnej postępuj zgodnie z instrukcjami, aby nawiązać połączenie z maszyną wirtualną przy użyciu protokołu SSH.

## <a name="configure-the-hosts-file"></a>Konfigurowanie pliku hosts

Aby upewnić się, że nazwa hosta maszyny wirtualnej jest prawidłowo skonfigurowana dla domeny zarządzanej, edytuj plik */etc/hosts* i Ustaw nazwę hosta:

```console
sudo vi /etc/hosts
```

W pliku *hosts* zaktualizuj adres *localhost* . W poniższym przykładzie:

* *aaddscontoso.com* jest nazwą domeny DNS domeny zarządzanej.
* *RHEL* jest nazwą hosta maszyny wirtualnej RHEL, która jest dołączana do domeny zarządzanej.

Zaktualizuj te nazwy przy użyciu własnych wartości:

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

Po zakończeniu Zapisz i wyjdź z pliku *hosts* za pomocą `:wq` polecenia edytora.

## <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Maszyna wirtualna wymaga dodatkowych pakietów do przyłączenia maszyny wirtualnej do domeny zarządzanej. Aby zainstalować i skonfigurować te pakiety, zaktualizuj i zainstaluj narzędzia do przyłączania do domeny za pomocą programu `yum` . Istnieją pewne różnice między RHEL 7. x i RHEL 6. x, dlatego użyj odpowiednich poleceń dla wersji dystrybucji w pozostałych sekcjach tego artykułu.

**RHEL 7**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**RHEL 6**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Dołącz maszynę wirtualną do domeny zarządzanej

Teraz, gdy wymagane pakiety są zainstalowane na maszynie wirtualnej, Dołącz maszynę wirtualną do domeny zarządzanej. Ponownie Użyj odpowiednich kroków dla wersji RHEL dystrybucji.

### <a name="rhel-7"></a>RHEL 7

1. Użyj `realm discover` polecenia, aby odnaleźć domenę zarządzaną. Poniższy przykład umożliwia odnajdywanie obszaru *AADDSCONTOSO.com*. Określ własną nazwę domeny zarządzanej we wszystkich wielkich liter:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Jeśli `realm discover` polecenie nie może znaleźć domeny zarządzanej, zapoznaj się z poniższymi krokami rozwiązywania problemów:

    * Upewnij się, że domena jest osiągalna z maszyny wirtualnej. Spróbuj `ping aaddscontoso.com` sprawdzić, czy jest zwracana pozytywna odpowiedź.
    * Sprawdź, czy maszyna wirtualna jest wdrożona w tej samej lub równorzędnej sieci wirtualnej, w której jest dostępna domena zarządzana.
    * Upewnij się, że ustawienia serwera DNS dla sieci wirtualnej zostały zaktualizowane w taki sposób, aby wskazywały kontrolery domeny w domenie zarządzanej.

1. Teraz zainicjuj protokół Kerberos za pomocą `kinit` polecenia. Określ użytkownika, który jest częścią domeny zarządzanej. W razie potrzeby [Dodaj konto użytkownika do grupy w usłudze Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Ponownie nazwa domeny zarządzanej musi być wpisana WIELKImi LITERAmi. W poniższym przykładzie konto o nazwie `contosoadmin@aaddscontoso.com` jest używane do inicjowania protokołu Kerberos. Wprowadź własne konto użytkownika, które jest częścią domeny zarządzanej:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Na koniec Dołącz maszynę wirtualną do domeny zarządzanej przy użyciu `realm join` polecenia. Użyj tego samego konta użytkownika, które jest częścią domeny zarządzanej, która została określona w poprzednim `kinit` poleceniu, na przykład `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Przyłączanie maszyny wirtualnej do domeny zarządzanej może chwilę potrwać. Następujące przykładowe dane wyjściowe pokazują, że maszyna wirtualna została pomyślnie dołączona do domeny zarządzanej:

```output
Successfully enrolled machine in realm
```

### <a name="rhel-6"></a>RHEL 6

1. Użyj `adcli info` polecenia, aby odnaleźć domenę zarządzaną. Poniższy przykład umożliwia odnajdywanie obszaru *ADDDSCONTOSO.com*. Określ własną nazwę domeny zarządzanej we wszystkich wielkich liter:

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Jeśli `adcli info` polecenie nie może znaleźć domeny zarządzanej, zapoznaj się z poniższymi krokami rozwiązywania problemów:

    * Upewnij się, że domena jest osiągalna z maszyny wirtualnej. Spróbuj `ping aaddscontoso.com` sprawdzić, czy jest zwracana pozytywna odpowiedź.
    * Sprawdź, czy maszyna wirtualna jest wdrożona w tej samej lub równorzędnej sieci wirtualnej, w której jest dostępna domena zarządzana.
    * Upewnij się, że ustawienia serwera DNS dla sieci wirtualnej zostały zaktualizowane w taki sposób, aby wskazywały kontrolery domeny w domenie zarządzanej.

1. Najpierw Przyłącz się do domeny za pomocą `adcli join` polecenia, to polecenie spowoduje również utworzenie plik KEYTAB w celu uwierzytelnienia maszyny. Użyj konta użytkownika, które jest częścią domeny zarządzanej.

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Teraz Skonfiguruj `/ect/krb5.conf` i Utwórz pliki, `/etc/sssd/sssd.conf` Aby użyć `aaddscontoso.com` domeny Active Directory.
   Upewnij się, że `AADDSCONTOSO.COM` jest zastępowana nazwą swojej domeny:

    Otwórz `/ect/krb5.conf` plik z edytorem:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Zaktualizuj `krb5.conf` plik, aby dopasować go do poniższego przykładu:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = AADDSCONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     AADDSCONTOSO.COM = {
     kdc = AADDSCONTOSO.COM
     admin_server = AADDSCONTOSO.COM
     }
    
    [domain_realm]
     .AADDSCONTOSO.COM = AADDSCONTOSO.COM
     AADDSCONTOSO.COM = AADDSCONTOSO.COM
    ```
    
   Utwórz `/etc/sssd/sssd.conf` plik:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Zaktualizuj `sssd.conf` plik, aby dopasować go do poniższego przykładu:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Upewnij się `/etc/sssd/sssd.conf` , że uprawnienia są 600 i należą do użytkownika root:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. Użyj polecenia `authconfig` , aby nakazać maszynie wirtualnej integrację z usługą AD systemu Linux:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Uruchom i Włącz usługę SSSD:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Jeśli maszyna wirtualna nie może pomyślnie ukończyć procesu przyłączania do domeny, upewnij się, że sieciowa Grupa zabezpieczeń maszyny wirtualnej zezwala na ruch wychodzący Kerberos na porcie TCP + UDP 464 do podsieci sieci wirtualnej dla domeny zarządzanej.

Sprawdź teraz, czy możesz wysyłać zapytania o informacje o użytkowniku przy użyciu usługi `getent`

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>Zezwalaj na uwierzytelnianie za pośrednictwem protokołu SSH

Domyślnie użytkownicy mogą logować się tylko do maszyny wirtualnej przy użyciu uwierzytelniania opartego na kluczu publicznym SSH. Uwierzytelnianie oparte na hasłach kończy się niepowodzeniem. Po dołączeniu maszyny wirtualnej do domeny zarządzanej te konta domeny muszą używać uwierzytelniania opartego na hasłach. Zaktualizuj konfigurację protokołu SSH, aby zezwolić na uwierzytelnianie oparte na hasłach w następujący sposób.

1. Otwórz plik *sshd_conf* z edytorem:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Zaktualizuj wiersz dla elementu *PasswordAuthentication* do *tak*:

    ```console
    PasswordAuthentication yes
    ```

    Po zakończeniu Zapisz i zamknij plik *sshd_conf* za pomocą `:wq` polecenia edytora.

1. Aby zastosować zmiany i umożliwić użytkownikom logowanie się przy użyciu hasła, należy ponownie uruchomić usługę SSH dla swojej wersji RHEL dystrybucji:

   **RHEL 7**

    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Przyznaj grupie "Administratorzy usługi AAD DC" uprawnienia sudo

Aby udzielić członkom uprawnień administracyjnych grupy *administratorów usługi AAD* na maszynie wirtualnej RHEL, należy dodać wpis do */etc/sudoers*. Po dodaniu członkowie grupy *administratorów domeny usługi AAD* mogą używać `sudo` polecenia na maszynie wirtualnej RHEL.

1. Otwórz plik *sudo* do edycji:

    ```console
    sudo visudo
    ```

1. Dodaj następujący wpis na końcu pliku */etc/sudoers* . Grupa *Administratorzy domeny usługi AAD* zawiera odstępy w nazwie, dlatego w nazwie grupy należy umieścić znak ucieczki odwrotnej kreski ułamkowej. Dodaj własną nazwę domeny, taką jak *aaddscontoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Po zakończeniu Zapisz i wyjdź z edytora przy użyciu `:wq` polecenia edytora.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logowanie się do maszyny wirtualnej przy użyciu konta domeny

Aby sprawdzić, czy maszyna wirtualna została pomyślnie dołączona do domeny zarządzanej, należy uruchomić nowe połączenie SSH przy użyciu konta użytkownika domeny. Upewnij się, że katalog macierzysty został utworzony, a członkostwo w grupie jest stosowane.

1. Utwórz nowe połączenie SSH z poziomu konsoli. Użyj konta domeny, które należy do domeny zarządzanej przy użyciu `ssh -l` polecenia, na przykład, `contosoadmin@aaddscontoso.com` a następnie wprowadź adres maszyny wirtualnej, na przykład *RHEL.aaddscontoso.com*. Jeśli używasz Azure Cloud Shell, użyj publicznego adresu IP maszyny wirtualnej, a nie wewnętrznej nazwy DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
    ```

1. Po pomyślnym nawiązaniu połączenia z maszyną wirtualną Sprawdź, czy katalog macierzysty został zainicjowany prawidłowo:

    ```console
    pwd
    ```

    Należy mieć katalog */Home* z własnym katalogiem, który odpowiada kontu użytkownika.

1. Teraz sprawdź, czy członkostwa w grupach są poprawnie rozwiązane:

    ```console
    id
    ```

    Członkostwa w grupach powinny być widoczne z domeny zarządzanej.

1. Jeśli zalogowano się do maszyny wirtualnej jako członek grupy *administratorów domeny usługi AAD* , sprawdź, czy można prawidłowo użyć `sudo` polecenia:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z łączeniem maszyny wirtualnej z domeną zarządzaną lub logowanie przy użyciu konta domeny, zobacz [Rozwiązywanie problemów z przyłączaniem do domeny](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
