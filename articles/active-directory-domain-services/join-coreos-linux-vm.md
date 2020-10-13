---
title: Przyłączanie maszyny wirtualnej CoreOS do Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować maszynę wirtualną CoreOS i przyłączyć ją do domeny zarządzanej Azure AD Domain Services.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: joflore
ms.openlocfilehash: c3a88f96f7391fedd973b7965a7c469ce1805d76
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962415"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Przyłączanie maszyny wirtualnej CoreOS do domeny zarządzanej Azure Active Directory Domain Services

Aby umożliwić użytkownikom logowanie się do maszyn wirtualnych na platformie Azure przy użyciu jednego zestawu poświadczeń, możesz dołączyć maszyny wirtualne do domeny zarządzanej Azure Active Directory Domain Services (AD DS platformy Azure). Po dołączeniu maszyny wirtualnej do domeny zarządzanej AD DS platformy Azure można użyć kont użytkowników i poświadczeń z domeny, aby zalogować się i zarządzać serwerami. Członkostwa w grupach z domeny zarządzanej są również stosowane w celu umożliwienia kontroli dostępu do plików lub usług na maszynie wirtualnej.

W tym artykule opisano sposób przyłączania maszyny wirtualnej CoreOS do domeny zarządzanej.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności pierwszy samouczek [tworzy i konfiguruje domenę zarządzaną Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Konto użytkownika, które jest częścią domeny zarządzanej.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Tworzenie maszyny wirtualnej z systemem CoreOS Linux i nawiązywanie z nią połączenia

Jeśli masz istniejącą maszynę wirtualną z systemem Linux CoreOS na platformie Azure, Połącz się z nią przy użyciu protokołu SSH, a następnie przejdź do następnego kroku, aby [rozpocząć konfigurowanie maszyny wirtualnej](#configure-the-hosts-file).

Jeśli musisz utworzyć maszynę wirtualną z systemem Linux CoreOS lub chcesz utworzyć testową maszynę wirtualną do użycia z tym artykułem, możesz użyć jednej z następujących metod:

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
* *CoreOS* jest nazwą hosta maszyny wirtualnej CoreOS, która jest dołączana do domeny zarządzanej.

Zaktualizuj te nazwy przy użyciu własnych wartości:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Po zakończeniu Zapisz i wyjdź z pliku *hosts* za pomocą `:wq` polecenia edytora.

## <a name="configure-the-sssd-service"></a>Konfigurowanie usługi SSSD

Zaktualizuj konfigurację */etc/SSSD/SSSD.conf* SSSD.

```console
sudo vi /etc/sssd/sssd.conf
```

Określ własną nazwę domeny zarządzanej dla następujących parametrów:

* *domeny* w Wielkiej wielkości liter
* *[domena/AADDSCONTOSO]* gdzie AADDSCONTOSO jest we wszystkich wielkich liter
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* WIELKImi literami

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Przyłączanie maszyny wirtualnej do domeny zarządzanej

Po zaktualizowaniu pliku konfiguracji SSSD przyłączaj maszynę wirtualną do domeny zarządzanej.

1. Najpierw użyj polecenia, `adcli info` Aby sprawdzić, czy są widoczne informacje o domenie zarządzanej. Poniższy przykład pobiera informacje dla domeny *AADDSCONTOSO.com*. Określ własną nazwę domeny zarządzanej we wszystkich wielkich liter:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Jeśli `adcli info` polecenie nie może znaleźć domeny zarządzanej, zapoznaj się z poniższymi krokami rozwiązywania problemów:

    * Upewnij się, że domena jest osiągalna z maszyny wirtualnej. Spróbuj `ping aaddscontoso.com` sprawdzić, czy jest zwracana pozytywna odpowiedź.
    * Sprawdź, czy maszyna wirtualna jest wdrożona w tej samej lub równorzędnej sieci wirtualnej, w której jest dostępna domena zarządzana.
    * Upewnij się, że ustawienia serwera DNS dla sieci wirtualnej zostały zaktualizowane w taki sposób, aby wskazywały kontrolery domeny w domenie zarządzanej.

1. Teraz Dołącz maszynę wirtualną do domeny zarządzanej przy użyciu `adcli join` polecenia. Określ użytkownika, który jest częścią domeny zarządzanej. W razie potrzeby [Dodaj konto użytkownika do grupy w usłudze Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Ponownie nazwa domeny zarządzanej musi być wpisana WIELKImi LITERAmi. W poniższym przykładzie konto o nazwie `contosoadmin@aaddscontoso.com` jest używane do inicjowania protokołu Kerberos. Wprowadź własne konto użytkownika, które jest częścią domeny zarządzanej.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    `adcli join`Polecenie nie zwraca żadnych informacji w przypadku pomyślnego dołączenia maszyny wirtualnej do domeny zarządzanej.

1. Aby zastosować konfigurację przyłączania do domeny, uruchom usługę SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logowanie się do maszyny wirtualnej przy użyciu konta domeny

Aby sprawdzić, czy maszyna wirtualna została pomyślnie dołączona do domeny zarządzanej, należy uruchomić nowe połączenie SSH przy użyciu konta użytkownika domeny. Upewnij się, że katalog macierzysty został utworzony, a członkostwo w grupie jest stosowane.

1. Utwórz nowe połączenie SSH z poziomu konsoli. Użyj konta domeny, które należy do domeny zarządzanej przy użyciu `ssh -l` polecenia, na przykład, `contosoadmin@aaddscontoso.com` a następnie wprowadź adres maszyny wirtualnej, na przykład *CoreOS.aaddscontoso.com*. Jeśli używasz Azure Cloud Shell, użyj publicznego adresu IP maszyny wirtualnej, a nie wewnętrznej nazwy DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Teraz sprawdź, czy członkostwa w grupach są poprawnie rozwiązane:

    ```console
    id
    ```

    Członkostwa w grupach powinny być widoczne z domeny zarządzanej.

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z łączeniem maszyny wirtualnej z domeną zarządzaną lub logowanie przy użyciu konta domeny, zobacz [Rozwiązywanie problemów z przyłączaniem do domeny](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
