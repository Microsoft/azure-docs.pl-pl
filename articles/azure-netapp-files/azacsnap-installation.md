---
title: Zainstaluj narzędzie migawek spójnych w aplikacji Azure dla Azure NetApp Files | Microsoft Docs
description: Zawiera Przewodnik dotyczący instalacji narzędzia migawek spójnej z aplikacją platformy Azure, którego można używać z Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 458f4d3f29cb08a94095167ed45133f5cd70f5f4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869195"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>Zainstaluj narzędzie do tworzenia migawek spójnych w aplikacji Azure (wersja zapoznawcza)

Ten artykuł zawiera Przewodnik dotyczący instalacji narzędzia migawek spójnej na poziomie aplikacji platformy Azure, którego można używać z Azure NetApp Files.

## <a name="introduction"></a>Wprowadzenie

Możliwość pobrania samodzielnego instalatora umożliwia łatwe konfigurowanie i uruchamianie narzędzi do tworzenia migawek przy użyciu uprawnień użytkowników innych niż główne (na przykład azacsnap). Instalator skonfiguruje użytkownika i umieści narzędzia migawek w `$HOME/bin` podkatalogu użytkownicy (domyślnie = `/home/azacsnap/bin` ).
Instalator samoinstalujący próbuje określić poprawne ustawienia i ścieżki dla wszystkich plików na podstawie konfiguracji użytkownika wykonującego instalację (na przykład root). Jeśli wstępnie wymagane kroki (Włącz komunikację z magazynem i SAP HANA) zostały uruchomione jako główne, wówczas instalacja spowoduje skopiowanie klucza prywatnego i `hdbuserstore` lokalizacji użytkownika kopii zapasowej. Istnieje jednak możliwość wykonania czynności, które umożliwiają komunikację z zapleczem magazynu i SAP HANA być wykonywane ręcznie przez administratora z możliwością wiedzy po instalacji.

## <a name="prerequisites-for-installation"></a>Wymagania wstępne dotyczące instalacji

Postępuj zgodnie z instrukcjami w celu skonfigurowania i wykonania migawek i poleceń odzyskiwania po awarii. Przed zainstalowaniem i użyciem narzędzi do tworzenia migawek zaleca się wykonanie następujących kroków jako katalog główny.

1. **Poprawka systemu operacyjnego**: Zobacz Instalator poprawek i SMT w artykule [jak zainstalować i skonfigurować SAP HANA (duże wystąpienia) na platformie Azure](../virtual-machines/workloads/sap/hana-installation.md#operating-system).
1. **Synchronizacja czasu została skonfigurowana**. Klient będzie musiał podać serwer czasu zgodnego z NTP i odpowiednio skonfigurować system operacyjny.
1. Platforma **Hana jest zainstalowana** : Zobacz instrukcje dotyczące instalacji platformy Hana w [instalacji oprogramowania SAP NetWeaver w bazie danych Hana](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database).
1. **[Włącz komunikację z magazynem](#enable-communication-with-storage)** (więcej informacji można znaleźć w oddzielnej sekcji): Klient musi skonfigurować protokół SSH za pomocą pary kluczy prywatnych/publicznych i podać klucz publiczny dla każdego węzła, w którym narzędzia migawek są planowane do wykonania w ramach operacji firmy Microsoft na potrzeby instalacji na zapleczu magazynu.
   1. **Aby uzyskać Azure NetApp Files (szczegółowe informacje znajdują się w osobnej sekcji)**: Klient musi wygenerować plik uwierzytelniania nazwy głównej usługi.
   1. **W przypadku dużego wystąpienia platformy Azure (szczegółowe informacje znajdują się w osobnej sekcji)**: Klient musi skonfigurować protokół SSH za pomocą pary kluczy prywatnych/publicznych i podać klucz publiczny dla każdego węzła, w którym planowane są narzędzia migawek do wykonania na potrzeby instalacji na zapleczu magazynu.

      Przetestuj to przy użyciu protokołu SSH, aby nawiązać połączenie z jednym z węzłów (na przykład `ssh -l <Storage UserName> <Storage IP Address>` ).
      Wpisz, `exit` Aby wylogować wiersz magazynu.

      Firma Microsoft będzie udostępniać użytkownikom magazynu i adres IP magazynu podczas aprowizacji.
  
1. **[Włącz komunikację z SAP HANA](#enable-communication-with-sap-hana)** (więcej informacji można znaleźć w oddzielnej sekcji): Klient musi skonfigurować odpowiedniego użytkownika SAP HANA z uprawnieniami wymaganymi do wykonania migawki.
   1. To ustawienie można przetestować z poziomu wiersza polecenia w następujący sposób przy użyciu tekstu w `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - Powyższe przykłady dotyczą komunikacji bez protokołu SSL do SAP HANA.

## <a name="enable-communication-with-storage"></a>Włączanie komunikacji z magazynem

W tej sekcji opisano sposób włączania komunikacji z magazynem.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Utwórz nazwę główną usługi RBAC

1. W ramach sesji Azure Cloud Shell upewnij się, że użytkownik jest zalogowany w subskrypcji, która ma być domyślnie skojarzona z jednostką usługi:

    ```azurecli-interactive
    az account show
    ```

1. Jeśli subskrypcja jest niepoprawna, użyj

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. Tworzenie jednostki usługi przy użyciu interfejsu wiersza polecenia platformy Azure na następujący przykład

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. Powinno to spowodować wygenerowanie danych wyjściowych, takich jak Poniższy przykład:

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > To polecenie spowoduje automatyczne przypisanie roli współautor RBAC do jednostki usługi na poziomie subskrypcji, ale zakres do konkretnej grupy zasobów, w której testy będą tworzyć zasoby.

1. Wytnij i wklej zawartość wyjściową do pliku o nazwie `azureauth.json` przechowywanej w tym samym systemie co `azacsnap` polecenie i Zabezpiecz plik z odpowiednimi uprawnieniami systemowymi.

### <a name="azure-large-instance"></a>Duże wystąpienie platformy Azure

Komunikacja z zapleczem magazynu jest wykonywana za pośrednictwem szyfrowanego kanału SSH. Poniższe przykładowe kroki mają na celu zapewnienie wskazówek dotyczących konfiguracji protokołu SSH dla tej komunikacji.

1. Modyfikuj `/etc/ssh/ssh_config` plik

    Zapoznaj się z poniższymi danymi wyjściowymi, w których `MACs hmac-sha1` wiersz został dodany:

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Tworzenie pary kluczy prywatnych/publicznych

    Aby wygenerować parę kluczy przy użyciu następującego przykładowego polecenia, nie należy wprowadzać hasła podczas generowania klucza.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Wyślij klucz publiczny do operacji firmy Microsoft

    Wyślij dane wyjściowe `cat /root/.ssh/id_rsa.pub` polecenia (przykład poniżej) do operacji firmy Microsoft, aby umożliwić narzędziom migawek komunikowanie się z podsystemem magazynowania.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>Włącz komunikację z SAP HANA

Narzędzia migawek komunikują się z SAP HANA i potrzebują użytkownika z odpowiednimi uprawnieniami do inicjowania i zwalniania punktu zapisu bazy danych. W poniższym przykładzie przedstawiono konfigurację użytkownika SAP HANA v2 oraz `hdbuserstore` do komunikacji z bazą danych SAP HANA.

Następujące przykładowe polecenia konfigurują użytkownika (AZACSNAP) w SYSTEMDB na SAP HANA 2.
Baza danych, zmiana adresu IP, nazw użytkowników i haseł w odpowiedni sposób:

1. Nawiązywanie połączenia z usługą SYSTEMDB w celu utworzenia użytkownika

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. Utwórz użytkownika

    Ten przykład tworzy użytkownika AZACSNAP w SYSTEMDB.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Przyznaj uprawnienia użytkownikowi

    Ten przykład ustawia uprawnienie dla użytkownika AZACSNAP, aby umożliwić wykonywanie migawek magazynu spójnego z bazą danych.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *Opcjonalne* — Zapobiegaj wygaśnięciu hasła użytkownika

    > [!NOTE]
    > Przed wprowadzeniem tej zmiany Sprawdź zasady firmowe.

   Ten przykład powoduje wyłączenie wygaśnięcia hasła dla użytkownika AZACSNAP bez zmiany hasła użytkownika wygaśnie, aby migawki zostały wykonane prawidłowo.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. Skonfiguruj bezpieczny magazyn użytkowników SAP HANA (zmiana hasła) w tym przykładzie używa `hdbuserstore` polecenia z powłoki systemu Linux w celu skonfigurowania SAP HANA bezpiecznego magazynu użytkowników.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Sprawdź, SAP HANA bezpieczny magazyn użytkowników, aby sprawdzić, czy bezpieczny magazyn użytkowników jest skonfigurowany prawidłowo, użyj `hdbuserstore` polecenia, aby wyświetlić dane wyjściowe podobne do poniższego przykładu. Więcej informacji na temat używania programu `hdbuserstore` można znaleźć w witrynie sieci Web SAP.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>Używanie protokołu SSL na potrzeby komunikacji z usługą SAP HANA

`azacsnap`Narzędzie wykorzystuje SAP HANA `hdbsql` polecenie, aby komunikować się z SAP HANA. Obejmuje to użycie opcji SSL podczas szyfrowania komunikacji z SAP HANA. `azacsnap` używa `hdbsql` opcji protokołu SSL dla polecenia w następujący sposób.

Następujące elementy są zawsze używane podczas korzystania z `azacsnap --ssl` opcji:

- `-e` -Włącza szyfrowanie TLS encryptionTLS/SSL. Serwer wybiera najwyższy dostępny.
- `-ssltrustcert` -Określa, czy certyfikat serwera ma zostać zweryfikowany.
- `-sslhostnameincert "*"` -Określa nazwę hosta używaną do zweryfikowania tożsamości serwera. Określając `"*"` jako nazwę hosta, nazwa hosta serwera nie jest weryfikowana.

Komunikacja SSL wymaga również plików magazynu kluczy i magazynu zaufania.  Chociaż jest możliwe, aby te pliki były przechowywane w domyślnych lokalizacjach w instalacji systemu Linux, w celu zapewnienia poprawnego materiału klucza w różnych systemach SAP HANA (to oznacza, że w przypadkach, gdy do każdego systemu SAP HANA jest używany inny plik magazynu kluczy i magazynów zaufania), `azacsnap` oczekuje, że pliki magazynu kluczy i magazynu zaufania mają być przechowywane w `securityPath` lokalizacji określonej w `azacsnap` pliku konfiguracji.

#### <a name="key-store-files"></a>Pliki magazynu kluczy

- Jeśli używasz wielu identyfikatorów SID z tym samym materiałem kluczowym, łatwiej jest utworzyć linki do lokalizacji securityPath zgodnie z definicją w `azacsnap` pliku konfiguracyjnym.  Upewnij się, że te wartości istnieją dla każdego identyfikatora SID przy użyciu protokołu SSL.
  - Dla OpenSSL:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - Dla commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- Jeśli używasz wielu identyfikatorów SID z różnymi materiałami kluczowymi na SID, skopiuj (lub Przenieś i Zmień nazwę) plików do lokalizacji securityPath zgodnie z definicją w `azacsnap` pliku konfiguracyjnym identyfikatorów SID.
  - Dla OpenSSL:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - Dla commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

W przypadku `azacsnap` wywołań `hdbsql` zostanie ono dodane `-sslkeystore=<securityPath>/<SID>_keystore` do wiersza polecenia.

#### <a name="trust-store-files"></a>Pliki magazynu zaufania

- Jeśli używasz wielu identyfikatorów SID z tym samym materiałem kluczowym, Utwórz twarde linki do lokalizacji securityPath zgodnie z definicją w `azacsnap` pliku konfiguracyjnym.  Upewnij się, że te wartości istnieją dla każdego identyfikatora SID przy użyciu protokołu SSL.
  - Dla OpenSSL:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - Dla commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- Jeśli używasz wielu identyfikatorów SID z różnymi materiałami kluczowymi na SID, skopiuj (lub Przenieś i Zmień nazwę) plików do lokalizacji securityPath zgodnie z definicją w `azacsnap` pliku konfiguracyjnym identyfikatorów SID.
  - Dla OpenSSL:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - Dla commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> `<SID>`Składnik nazw plików musi być identyfikatorem systemu SAP HANA wszystkie w Wielkiej litery (na przykład `H80` `PR1` itd.)

W przypadku `azacsnap` wywołań `hdbsql` zostanie ono dodane `-ssltruststore=<securityPath>/<SID>_truststore` do wiersza polecenia.

W związku z tym, `azacsnap -c test --test hana --ssl openssl` gdy `SID` `H80` plik jest w pliku konfiguracji, będzie wykonywał `hdbsql` połączenia w następujący sposób:

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> `\`Znak jest wierszem wiersza polecenia — zawiń, aby poprawić czytelność wielu parametrów przesłanych w wierszu polecenia.

## <a name="installing-the-snapshot-tools"></a>Instalowanie narzędzi migawek

Możliwość pobrania samodzielnego instalatora umożliwia łatwe konfigurowanie i uruchamianie narzędzi do tworzenia migawek przy użyciu uprawnień użytkowników innych niż główne (na przykład azacsnap). Instalator skonfiguruje użytkownika i umieści narzędzia migawek w `$HOME/bin` podkatalogu użytkownicy (domyślnie = `/home/azacsnap/bin` ).

Instalator samoinstalujący próbuje określić poprawne ustawienia i ścieżki dla wszystkich plików na podstawie konfiguracji użytkownika wykonującego instalację (na przykład root). Jeśli poprzednie kroki instalacji (Włącz komunikację z magazynem i SAP HANA) zostały uruchomione jako główne, wówczas instalacja spowoduje skopiowanie klucza prywatnego i `hdbuserstore` lokalizacji użytkownika kopii zapasowej. Istnieje jednak możliwość wykonania czynności, które umożliwiają komunikację z zapleczem magazynu i SAP HANA być wykonywane ręcznie przez administratora z możliwością wiedzy po instalacji.

> [!NOTE]
> W przypadku wcześniejszych SAP HANA w instalacjach z dużymi wystąpieniami platformy Azure katalog wstępnie zainstalowanych narzędzi migawek był `/hana/shared/<SID>/exe/linuxx86_64/hdb` .

Po ukończeniu [kroków wstępnych](#prerequisites-for-installation) można zainstalować narzędzia migawek przy użyciu samoobsługowego Instalatora w następujący sposób:

1. Skopiuj pobrany Instalator samoinstalujący do systemu docelowego.
1. Wykonaj samoobsługowy Instalator jako `root` użytkownik, zobacz Poniższy przykład. W razie potrzeby utwórz plik wykonywalny przy użyciu `chmod +x *.run` polecenia.

Uruchomienie polecenia samoobsługowego bez żadnych argumentów spowoduje wyświetlenie pomocy dotyczącej korzystania z Instalatora w celu zainstalowania narzędzi migawek w następujący sposób:

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> Samodzielny Instalator zawiera opcję wyodrębniania (-X) narzędzi migawek z pakietu bez wykonywania żadnych operacji tworzenia i konfigurowania przez użytkownika. Dzięki temu doświadczony administrator może ręcznie wykonać czynności konfiguracyjne lub skopiować polecenia w celu uaktualnienia istniejącej instalacji.

### <a name="easy-installation-of-snapshot-tools-default"></a>Łatwa instalacja narzędzi migawek (domyślnie)

Instalator został zaprojektowany, aby szybko zainstalować narzędzia migawek dla SAP HANA na platformie Azure. Domyślnie, jeśli Instalator jest uruchamiany tylko z opcją-I, wykona następujące czynności:

1. Utwórz migawkę użytkownika "azacsnap", katalog macierzysty i ustaw członkostwo w grupie.
1. Skonfiguruj Logowanie użytkownika azacsnap `~/.profile` .
1. Wyszukaj w systemie plików katalogi, które mają zostać dodane do azacsnap `$PATH` , są zwykle ścieżkami do narzędzi SAP HANA, takich jak `hdbsql` i `hdbuserstore` .
1. Wyszukaj w systemie plików katalogi, które mają zostać dodane do azacsnap `$LD_LIBRARY_PATH` . Wiele poleceń wymaga ustawienia ścieżki biblioteki w celu poprawnego wykonania. spowoduje to skonfigurowanie dla zainstalowanego użytkownika.
1. Skopiuj klucze SSH dla magazynu zaplecza azacsnap z użytkownika "root" (użytkownik, który uruchomił instalację). Przyjęto założenie, że użytkownik "root" już skonfigurował połączenie z magazynem
    - Zobacz sekcję "[Włączanie komunikacji z magazynem](#enable-communication-with-storage)".
1. Skopiuj bezpieczny magazyn użytkowników połączenia SAP HANA dla użytkownika docelowego azacsnap. Przyjęto założenie, że użytkownik "root" skonfigurował już bezpieczny magazyn użytkowników — Zobacz sekcję "Włączanie komunikacji z SAP HANA".
1. Narzędzia migawek są wyodrębniane do programu `/home/azacsnap/bin/` .
1. Polecenia w programie `/home/azacsnap/bin/` mają ustawione uprawnienia (bit własności i plik wykonywalny itp.).

Poniższy przykład pokazuje prawidłowe dane wyjściowe Instalatora w przypadku uruchomienia z domyślną opcją instalacji.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>Odinstaluj narzędzia migawek

Jeśli narzędzia migawek zostały zainstalowane przy użyciu ustawień domyślnych, dezinstalacja wymaga tylko usunięcia użytkownika, dla którego zostały zainstalowane polecenia (domyślnie = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>Ręczna instalacja narzędzi migawek

W niektórych przypadkach konieczne jest ręczne zainstalowanie narzędzi, ale zaleca się użycie domyślnej opcji Instalatora, aby uprościć ten proces.

Każdy wiersz rozpoczynający się od `#` znaku pokazuje przykładowe polecenia po znaku są uruchamiane przez użytkownika root. `\`Na końcu wiersza jest standardowy znak kontynuacji wiersza polecenia powłoki.

Jako główny administratora można uzyskać instalację ręczną w następujący sposób:

1. Pobierz identyfikator grupy "sapsys", w tym przypadku identyfikator grupy = 1010

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. Utwórz migawkę użytkownika "azacsnap", katalog macierzysty i ustaw członkostwo w grupie przy użyciu identyfikatora grupy z kroku 1.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Upewnij się, że logowanie użytkownika azacsnap `.profile` istnieje.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Przeszukaj system plików w poszukiwaniu katalogów do dodania do $PATH azacsnap, są to zazwyczaj ścieżki do narzędzi SAP HANA, takich jak `hdbsql` i `hdbuserstore` .

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Dodaj zaktualizowaną ścieżkę do profilu użytkownika

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Wyszukaj w systemie plików katalogi, które mają zostać dodane do $LD _LIBRARY_PATH azacsnap.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Dodaj zaktualizowaną ścieżkę biblioteki do profilu użytkownika

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. W dużych wystąpieniach platformy Azure
    1. Skopiuj klucze SSH dla magazynu zaplecza azacsnap z użytkownika "root" (użytkownik, który uruchomił instalację). Przyjęto założenie, że użytkownik "root" już skonfigurował połączenie z magazynem
       > Zobacz sekcję "[Włączanie komunikacji z magazynem](#enable-communication-with-storage)".

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. Ustawianie uprawnień użytkownika poprawnie dla plików SSH

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. Na Azure NetApp Files
    1. Skonfiguruj `DOTNET_BUNDLE_EXTRACT_BASE_DIR` ścieżkę użytkownika na wskazówki dotyczące wyodrębnienia pojedynczego pliku w ramach platformy .NET Core.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Skopiuj bezpieczny magazyn użytkowników połączenia SAP HANA dla użytkownika docelowego azacsnap. Przyjęto założenie, że użytkownik "root" skonfigurował już bezpieczny magazyn użytkowników.
    > Zobacz sekcję "[Włączanie komunikacji z SAP HANA](#enable-communication-with-sap-hana)".

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. Ustaw prawidłowo uprawnienia użytkownika dla `hdbuserstore` plików

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. Wyodrębnij narzędzia migawek do/Home/azacsnap/bin/.

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. Utwórz plik wykonywalny poleceń

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. Upewnij się, że uprawnienia własności są ustawione w katalogu macierzystym użytkownika

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>Ukończ instalację narzędzi migawek

Instalator zawiera kroki, które należy wykonać po zakończeniu instalacji narzędzi do tworzenia migawek.
Wykonaj następujące kroki, aby skonfigurować i przetestować narzędzia migawek.  Po pomyślnym zakończeniu testowania wykonaj pierwszą migawkę magazynu spójną z bazą danych.

Poniższe dane wyjściowe pokazują kroki, które należy wykonać po uruchomieniu Instalatora z domyślnymi opcjami instalacji:

1. Przejdź do konta użytkownika Snapshot
    1. `su - azacsnap`
1. Konfigurowanie bezpiecznego magazynu użytkowników platformy HANA
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Zmień na lokalizację poleceń
   1. `cd /home/azacsnap/bin/`
1. Konfigurowanie pliku szczegółów klienta
   1. `azacsnap -c configure –-configuration new`
1. Testuj połączenie z magazynem......
   1. `azacsnap -c test –-test storage`
1. Testuj połączenie z platformą HANA....
    1. bez protokołu SSL
       1. `azacsnap -c test –-test hana`
    1. przy użyciu protokołu SSL należy wybrać poprawną opcję SSL
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. Uruchamianie pierwszej kopii zapasowej migawki
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

Krok 2 będzie konieczny, jeśli przed instalacją nie wykonano operacji "[Włącz komunikację z SAP HANA](#enable-communication-with-sap-hana)".

> [!NOTE]
> Polecenia testowe powinny być wykonywane poprawnie. W przeciwnym razie polecenia mogą zakończyć się niepowodzeniem.

## <a name="configuring-the-database"></a>Konfigurowanie bazy danych

W tej sekcji wyjaśniono, jak skonfigurować bazę danych.

### <a name="sap-hana-configuration"></a>Konfiguracja SAP HANA

Istnieją pewne zalecane zmiany, które mają zostać zastosowane do SAP HANA w celu zapewnienia ochrony kopii zapasowych i wykazu dzienników. Domyślnie program `basepath_logbackup` i `basepath_catalogbackup` będzie wyprowadzać pliki do `$(DIR_INSTANCE)/backup/log` katalogu i jest mało prawdopodobne, że ta ścieżka znajduje się na woluminie, który `azacsnap` jest skonfigurowany do tworzenia migawek te pliki nie będą chronione za pomocą migawek magazynu.

Poniższe `hdbsql` przykłady poleceń mają na celu zaprezentowanie ścieżki dzienników i wykazu do lokalizacji, które znajdują się na woluminach magazynu, które mogą być migawkami `azacsnap` . Upewnij się, że wartości w wierszu polecenia pasują do konfiguracji SAP HANA lokalnej.

### <a name="configure-log-backup-location"></a>Konfigurowanie lokalizacji kopii zapasowej dziennika

W tym przykładzie zmiana jest wprowadzana do `basepath_logbackup` parametru.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>Konfigurowanie lokalizacji kopii zapasowej katalogu

W tym przykładzie zmiana jest wprowadzana do `basepath_catalogbackup` parametru. Najpierw upewnij się, że `basepath_catalogbackup` ścieżka istnieje w systemie plików, jeśli nie utworzysz ścieżki o takiej samej własności jak katalog.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Jeśli trzeba utworzyć ścieżkę, w poniższym przykładzie zostanie utworzona ścieżka i zostanie ustawiony prawidłowy własność i uprawnienia. Te polecenia będą musiały zostać uruchomione jako główne.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Poniższy przykład zmieni ustawienie SAP HANA.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>Sprawdź lokalizacje kopii zapasowej dzienników i katalogu

Po wprowadzeniu powyższych zmian upewnij się, że ustawienia są poprawne przy użyciu poniższego polecenia.
W tym przykładzie ustawienia, które zostały ustawione zgodnie z powyższymi wskazówkami, będą wyświetlane jako ustawienia systemowe.

> To zapytanie zwraca również domyślne ustawienia dla porównania.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>Skonfiguruj limit czasu tworzenia kopii zapasowej dziennika

Domyślne ustawienie SAP HANA wykonywania kopii zapasowej dziennika wynosi 900 sekund (15 minut). Zaleca się zredukowanie tej wartości do 300 sekund (czyli 5 minut).  Następnie można uruchamiać regularne kopie zapasowe (na przykład co 10 minut), dodając wolumin log_backups do sekcji inne woluminy w pliku konfiguracji.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>Sprawdź limit czasu kopii zapasowej dziennika

Po wprowadzeniu zmiany limitu czasu kopii zapasowej dziennika upewnij się, że została ona ustawiona w następujący sposób.
W tym przykładzie ustawienia, które zostały ustawione, będą wyświetlane jako ustawienia systemowe, ale to zapytanie zwraca również domyślne ustawienia dla porównania.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj narzędzie do tworzenia migawek spójnych w aplikacji platformy Azure](azacsnap-cmd-ref-configure.md)
