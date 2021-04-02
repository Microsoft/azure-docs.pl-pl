---
title: Przyłączanie maszyny wirtualnej SLE do Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować maszynę wirtualną z systemem SUSE Linux Enterprise i przyłączyć ją do domeny zarządzanej Azure AD Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: justinha
ms.openlocfilehash: f2f421d95dfc376aed373c718198db33a870d9dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619610"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Przyłączanie maszyny wirtualnej z systemem SUSE Linux Enterprise do Azure Active Directory Domain Services domeny zarządzanej

Aby umożliwić użytkownikom logowanie się do maszyn wirtualnych na platformie Azure przy użyciu jednego zestawu poświadczeń, możesz dołączyć maszyny wirtualne do domeny zarządzanej Azure Active Directory Domain Services (AD DS platformy Azure). Po dołączeniu maszyny wirtualnej do domeny zarządzanej AD DS platformy Azure można użyć kont użytkowników i poświadczeń z domeny, aby zalogować się i zarządzać serwerami. Członkostwa w grupach z domeny zarządzanej są również stosowane w celu umożliwienia kontroli dostępu do plików lub usług na maszynie wirtualnej.

W tym artykule opisano sposób przyłączania maszyny wirtualnej w systemie SUSE Linux Enterprise (SLE) do domeny zarządzanej.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności pierwszy samouczek [tworzy i konfiguruje domenę zarządzaną Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Konto użytkownika, które jest częścią domeny zarządzanej.

## <a name="create-and-connect-to-a-sle-linux-vm"></a>Tworzenie maszyny wirtualnej z systemem SLE Linux i nawiązywanie z nią połączenia

Jeśli masz istniejącą maszynę wirtualną z systemem Linux SLE na platformie Azure, Połącz się z nią przy użyciu protokołu SSH, a następnie przejdź do następnego kroku, aby [rozpocząć konfigurowanie maszyny wirtualnej](#configure-the-hosts-file).

Jeśli musisz utworzyć maszynę wirtualną z systemem Linux SLE lub chcesz utworzyć testową maszynę wirtualną do użycia z tym artykułem, możesz użyć jednej z następujących metod:

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
* *Linux-q2gr* jest nazwą hosta maszyny wirtualnej SLE, która jest dołączana do domeny zarządzanej.

Zaktualizuj te nazwy przy użyciu własnych wartości:

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

Po zakończeniu Zapisz i wyjdź z pliku *hosts* za pomocą `:wq` polecenia edytora.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>Dołącz maszynę wirtualną do domeny zarządzanej przy użyciu SSSD

Aby przyłączyć się do domeny zarządzanej za pomocą **SSSD** oraz modułu *zarządzania logowaniem użytkowników* YaST, wykonaj następujące czynności:

1. Zainstaluj moduł YaST *zarządzania logowaniem użytkowników* :

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. Otwórz YaST.

1. Aby pomyślnie użyć autowykrywania DNS później, należy skonfigurować adresy IP domeny zarządzanej ( *serwer Active Directory*) jako serwer nazw dla klienta.

    W YaST wybierz pozycję **System > ustawienia sieci**.

1. Wybierz kartę *Nazwa hosta/DNS* , a następnie wprowadź adresy IP domeny zarządzanej w polu tekstowym *Nazwa serwera 1*. Te adresy IP są wyświetlane w oknie *Właściwości* w Azure Portal dla domeny zarządzanej, takie jak *10.0.2.4* i *10.0.2.5*.

    Dodaj własne adresy IP domeny zarządzanej, a następnie wybierz przycisk **OK**.

1. W oknie głównym YaST wybierz pozycję *Network Services*  >  *Zarządzanie logowaniem użytkowników*.

    Moduł zostanie otwarty z omówieniem zawierającym różne właściwości sieci komputera i metodę uwierzytelniania, która jest aktualnie używana, jak pokazano na poniższym przykładzie zrzutu ekranu:

    ![Przykład zrzutu ekranu okna Zarządzanie logowaniem użytkownika w YaST](./media/join-suse-linux-vm/overview-window.png)

    Aby rozpocząć edycję, wybierz pozycję **Zmień ustawienia**.

Aby dołączyć maszynę wirtualną do domeny zarządzanej, wykonaj następujące czynności:

1. W oknie dialogowym wybierz pozycję **Dodaj domenę**.

1. Określ poprawną *nazwę domeny*, na przykład *aaddscontoso.com*, a następnie określ usługi, które mają być używane na potrzeby danych tożsamości i uwierzytelniania. Wybierz pozycję *Microsoft Active Directory* dla obu.

    Upewnij się, że wybrano opcję *Włącz domenę* .

1. Gdy wszystko będzie gotowe, wybierz **przycisk OK**.

1. Zaakceptuj ustawienia domyślne w następującym oknie dialogowym, a następnie wybierz przycisk **OK**.

1. Maszyna wirtualna instaluje dodatkowe oprogramowanie zgodnie z wymaganiami, a następnie sprawdza, czy jest dostępna domena zarządzana.

    Jeśli wszystkie elementy są poprawne, wyświetlane jest następujące przykładowe okno dialogowe, aby wskazać, że maszyna wirtualna wykryła domenę zarządzaną, ale *nie została jeszcze zarejestrowana*.

    ![Przykład zrzutu ekranu okna rejestracji Active Directory w YaST](./media/join-suse-linux-vm/enroll-window.png)

1. W oknie dialogowym Określ *nazwę* użytkownika i *hasło* , które są częścią domeny zarządzanej. W razie potrzeby [Dodaj konto użytkownika do grupy w usłudze Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Aby upewnić się, że bieżąca domena jest włączona dla programu Samba, Aktywuj *Zastąp konfigurację programu Samba, aby współdziałać z tą usługą AD*.

1. Aby zarejestrować się, wybierz **przycisk OK**.

1. Zostanie wyświetlony komunikat potwierdzający pomyślne zarejestrowanie się. Aby zakończyć, wybierz **przycisk OK**.

Po zarejestrowaniu maszyny wirtualnej w domenie zarządzanej Skonfiguruj klienta przy użyciu polecenia *Zarządzaj logowaniem użytkownika domeny*, jak pokazano na poniższym przykładowym zrzucie ekranu:

![Przykład zrzutu ekranu okna Zarządzanie logowaniem użytkownika domeny w YaST](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. Aby zezwolić na logowanie przy użyciu danych dostarczanych przez domenę zarządzaną, zaznacz pole wyboru *Zezwalaj na logowanie użytkownika domeny*.

1. Opcjonalnie w obszarze *Włączanie źródła danych domeny* Sprawdź dodatkowe źródła danych zgodnie z wymaganiami środowiska. Te opcje obejmują użytkowników, którzy mogą korzystać z **sudo** lub dostępnych dysków sieciowych.

1. Aby umożliwić użytkownikom w domenie zarządzanej posiadanie katalogów macierzystych na maszynie wirtualnej, zaznacz pole wyboru *Utwórz katalogi macierzyste*.

1. Na pasku bocznym wybierz pozycję **Opcje usługi › przełącznika nazw**, a następnie *Opcje rozszerzone*. W tym oknie Wybierz opcję *fallback_homedir* lub *override_homedir*, a następnie wybierz pozycję **Dodaj**.

1. Określ wartość lokalizacji katalogu macierzystego. Aby katalogi macierzyste były zgodne z formatem */home/user_name*, użyj */Home/%u*. Aby uzyskać więcej informacji na temat możliwych zmiennych, zobacz SSSD. conf Man ( `man 5 sssd.conf` ), sekcja *override_homedir*.

1. Wybierz przycisk **OK**.

1. Aby zapisać zmiany, wybierz pozycję **OK**. Następnie upewnij się, że wyświetlane wartości są poprawne. Aby opuścić okno dialogowe, wybierz pozycję **Anuluj**.

1. Jeśli zamierzasz uruchamiać jednocześnie SSSD i winbind (na przykład podczas dołączania przez SSSD, a następnie uruchamiając serwer plików Samba), należy ustawić dla metody Samba wartość *tajne i plik KEYTAB* w *protokole* SMB. conf. Opcja SSSD *ad_update_samba_machine_account_password* powinna również mieć wartość *true* w SSSD. conf. Te opcje uniemożliwiają przeprowadzenie synchronizacji przez system plik KEYTAB.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Dołącz maszynę wirtualną do domeny zarządzanej przy użyciu winbind

Aby przyłączyć się do domeny zarządzanej za pomocą **winbind** oraz modułu *członkostwa w domenie systemu Windows* YaST, wykonaj następujące czynności:

1. W YaST wybierz pozycję **Network Services > członkostwo w domenie systemu Windows**.

1. Wprowadź domenę do przyłączenia do *domeny lub grupy roboczej* na ekranie *członkostwo w domenie systemu Windows* . Wprowadź nazwę domeny zarządzanej, taką jak *aaddscontoso.com*.

    ![Przykład zrzutu ekranu okna członkostwa domen systemu Windows w YaST](./media/join-suse-linux-vm/samba-client-window.png)

1. Aby użyć źródła SMB do uwierzytelniania systemu Linux, zaznacz opcję *Użyj informacji o protokole SMB na potrzeby uwierzytelniania systemu Linux*.

1. Aby automatycznie utworzyć lokalny katalog macierzysty dla użytkowników domeny zarządzanej na maszynie wirtualnej, zaznacz opcję *Utwórz katalog macierzysty podczas logowania*.

1. Zaznacz opcję uwierzytelnianie w *trybie offline* , aby umożliwić użytkownikom domeny logowanie się nawet wtedy, gdy domena zarządzana jest tymczasowo niedostępna.

1. Jeśli chcesz zmienić zakresy UID i GID dla użytkowników i grup programu Samba, wybierz pozycję *Ustawienia ekspercki*.

1. Skonfiguruj synchronizację czasu protokołu NTP (Network Time Protocol) dla domeny zarządzanej, wybierając pozycję *Konfiguracja NTP*. Wprowadź adresy IP domeny zarządzanej. Te adresy IP są wyświetlane w oknie *Właściwości* w Azure Portal dla domeny zarządzanej, takie jak *10.0.2.4* i *10.0.2.5*.

1. Wybierz **przycisk OK** i Potwierdź przyłączenie do domeny, gdy zostanie wyświetlony odpowiedni monit.

1. Podaj hasło administratora w domenie zarządzanej, a następnie wybierz **przycisk OK**.

    ![Przykładowy zrzut ekranu przedstawiający monit okna dialogowego uwierzytelniania podczas przyłączania maszyny wirtualnej SLE do domeny zarządzanej](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

Po dołączeniu do domeny zarządzanej możesz zalogować się do niej z poziomu stacji roboczej za pomocą Menedżera wyświetlania pulpitu lub konsoli programu.

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>Przyłączanie maszyny wirtualnej do domeny zarządzanej przy użyciu winbind z interfejsu wiersza polecenia YaST

Aby przyłączyć się do domeny zarządzanej przy użyciu **winbind** i *interfejsu wiersza polecenia YaST*:

* Dołącz do domeny:

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>Dołącz maszynę wirtualną do domeny zarządzanej przy użyciu winbind z terminalu

Aby przyłączyć się do domeny zarządzanej za pomocą **winbind** i *`samba net` polecenia*:

1. Zainstaluj klienta protokołu Kerberos i Samba-winbind:

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. Edytuj pliki konfiguracji:

   * /etc/samba/smb.conf
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/krb5.conf
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /etc/Security/pam_winbind. conf
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /etc/nsswitch.conf
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. Sprawdź, czy data i godzina w usłudze Azure AD i Linux są zsynchronizowane. W tym celu można dodać serwer usługi Azure AD do usługi NTP:
   
   1. Dodaj następujący wiersz do/etc/ntp.conf:
     
      ```console
      server aaddscontoso.com
      ```

   1. Uruchom ponownie usługę NTP:
     
      ```console
      sudo systemctl restart ntpd
      ```

4. Dołącz do domeny:

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. Włącz winbind jako źródło logowania w modułach uwierzytelniania (PAM) systemu Linux:

   ```console
   pam-config --add --winbind
   ```

6. Włącz automatyczne tworzenie katalogów macierzystych, aby użytkownicy mogli się zalogować:

   ```console
   pam-config -a --mkhomedir
   ```

7. Uruchom i Włącz usługę winbind:

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
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

1. Aby zastosować zmiany i umożliwić użytkownikom logowanie się przy użyciu hasła, należy ponownie uruchomić usługę SSH:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Przyznaj grupie "Administratorzy usługi AAD DC" uprawnienia sudo

Aby udzielić członkom uprawnień administracyjnych grupy *administratorów usługi AAD* na maszynie wirtualnej SLE, Dodaj wpis do */etc/sudoers*. Po dodaniu członkowie grupy *administratorów domeny usługi AAD* mogą używać `sudo` polecenia na maszynie wirtualnej SLE.

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

1. Utwórz nowe połączenie SSH z poziomu konsoli. Użyj konta domeny, które należy do domeny zarządzanej przy użyciu `ssh -l` polecenia, na przykład, `contosoadmin@aaddscontoso.com` a następnie wprowadź adres maszyny wirtualnej, na przykład *Linux-q2gr.aaddscontoso.com*. Jeśli używasz Azure Cloud Shell, użyj publicznego adresu IP maszyny wirtualnej, a nie wewnętrznej nazwy DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. Po pomyślnym nawiązaniu połączenia z maszyną wirtualną Sprawdź, czy katalog macierzysty został zainicjowany prawidłowo:

    ```console
    pwd
    ```

    Należy mieć katalog */Home* z własnym katalogiem, który odpowiada kontu użytkownika.

3. Teraz sprawdź, czy członkostwa w grupach są poprawnie rozwiązane:

    ```console
    id
    ```

    Członkostwa w grupach powinny być widoczne z domeny zarządzanej.

4. Jeśli zalogowano się do maszyny wirtualnej jako członek grupy *administratorów domeny usługi AAD* , sprawdź, czy można prawidłowo użyć `sudo` polecenia:

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z łączeniem maszyny wirtualnej z domeną zarządzaną lub logowanie przy użyciu konta domeny, zobacz [Rozwiązywanie problemów z przyłączaniem do domeny](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
