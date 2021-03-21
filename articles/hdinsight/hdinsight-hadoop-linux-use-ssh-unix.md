---
title: Korzystanie z protokołu SSH w usłudze Hadoop — Azure HDInsight
description: Do usługi HDInsight można uzyskać dostęp przy użyciu protokołu Secure Shell (SSH). Ten dokument zawiera informacje dotyczące łączenia się z usługą HDInsight przy użyciu poleceń SSH z klientów z systemem Windows, Linux, UNIX lub macOS.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 02/28/2020
ms.openlocfilehash: a649c448e4d51929421a8dadbcd1948d7e130df1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945659"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH

Dowiedz się, jak bezpiecznie łączyć się z Apache Hadoop w usłudze Azure HDInsight za pomocą [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) . Aby uzyskać informacje na temat nawiązywania połączenia za pomocą sieci wirtualnej, zobacz [Architektura sieci wirtualnej usługi Azure HDInsight](./hdinsight-virtual-network-architecture.md). Zobacz również [Planowanie wdrożenia sieci wirtualnej dla klastrów usługi Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

Poniższa tabela zawiera informacje o adresie i porcie, które są konieczne podczas nawiązywania połączenia z usługą HDInsight przy użyciu klienta SSH:

| Adres | Port | Element docelowy połączenia |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Podstawowy węzeł główny |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Dodatkowy węzeł główny |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | węzeł krawędzi (usługi w usłudze ML w usłudze HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | węzeł krawędzi (dowolny inny typ klastra, jeśli istnieje węzeł krawędzi) |

Zastąp ciąg `<clustername>` nazwą klastra. Element `<edgenodename>` należy zastąpić nazwą węzła krawędzi.

Jeśli klaster zawiera węzeł krawędzi, zalecamy, aby __zawsze łączyć się z węzłem krawędzi__ przy użyciu protokołu SSH. Węzły główne hostują usługi o krytycznym znaczeniu dla kondycji usługi Hadoop. W węźle krawędzi działają tylko obciążenia umieszczone przez użytkownika. Więcej informacji dotyczących używania węzłów krawędzi można znaleźć w temacie [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node) (Używanie węzłów krawędzi w usłudze HDInsight).

> [!TIP]  
> Po pierwszym połączeniu z usługą HDInsight Twój klient SSH może wyświetlić ostrzeżenie, że nie można ustalić autentyczności hosta. Po wyświetleniu monitu wybierz opcję „tak”, aby dodać hosta do listy zaufanych serwerów klienta SSH.
>
> Jeśli wcześniej miało miejsce połączenie z serwerem o takiej samej nazwie, może pojawić się ostrzeżenie, że przechowywany klucz hosta nie odpowiada kluczowi hosta serwera. W dokumentacji klienta SSH można znaleźć informacje, jak usunąć istniejący wpis dla nazwy serwera.

## <a name="ssh-clients"></a>Klienci SSH

Systemy Linux, Unix i macOS obejmują polecenia `ssh` i `scp`. Klient `ssh` jest najczęściej używany do tworzenia zdalnej sesji wiersza polecenia za pomocą systemu Linux lub Unix. Klient `scp` służy do bezpiecznego kopiowania plików między klientem użytkownika a systemem zdalnym.

System Microsoft Windows domyślnie nie instaluje żadnych klientów SSH. Klienci `ssh` i `scp` są dostępni dla systemu Windows za pośrednictwem następujących pakietów:

* [Klient OpenSSH](/windows-server/administration/openssh/openssh_install_firstuse). Ten klient jest opcjonalną funkcją wprowadzoną w aktualizacji Windows 10 dla twórców.

* [Bash na Ubuntu w systemie Windows 10](/windows/wsl/about).

* [Azure Cloud Shell](../cloud-shell/quickstart.md). Cloud Shell zapewnia środowisko bash w przeglądarce.

* Usługi [git](https://git-scm.com/).

Istnieje również kilka graficznych klientów SSH, takich jak [MobaXterm](https://mobaxterm.mobatek.net/) [i.](https://www.chiark.greenend.org.uk/~sgtatham/putty/) Mimo że ci klienci umożliwiają nawiązywanie połączeń z usługą HDInsight, proces łączenia się jest inny niż w przypadku narzędzia `ssh`. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją używanego klienta graficznego.

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>Uwierzytelnianie: klucze SSH

Klucze SSH używają [kryptografii klucza publicznego](https://en.wikipedia.org/wiki/Public-key_cryptography) do uwierzytelniania sesji SSH. Klucze SSH są bezpieczniejsze niż hasła i umożliwiają łatwe zabezpieczenie dostępu do klastra usługi Hadoop.

Jeśli konto SSH zostanie zabezpieczone za pomocą klucza, podczas nawiązywania połączenia klient musi podać zgodny klucz prywatny:

* Większość klientów można skonfigurować do używania __domyślnego klucza__. Na przykład w środowiskach Linux i Unix klient `ssh` szuka klucza prywatnego w lokalizacji `~/.ssh/id_rsa`.

* Można określić __ścieżkę do klucza prywatnego__. W przypadku klienta `ssh` do określenia ścieżki do klucza prywatnego służy parametr `-i`. Na przykład `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Jeśli masz __wiele kluczy prywatnych__, których używasz na różnych serwerach, rozważ użycie takiego narzędzia jak [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent)). Narzędzie `ssh-agent` może służyć do automatycznego wybierania klucza do użycia podczas ustanawiania sesji SSH.

> [!IMPORTANT]  
> W przypadku zabezpieczenia klucza prywatnego za pomocą hasła musisz je podać przed użyciem klucza. Takie narzędzia, jak `ssh-agent`, umożliwiają wygodne przechowywanie haseł w pamięci podręcznej.

### <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Do tworzenia plików klucza publicznego i prywatnego służy polecenie `ssh-keygen`. Następujące polecenie generuje parę 2048-bitowych kluczy RSA, która pozwala połączyć się z usługą HDInsight:

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

Zostanie wyświetlony monit o podanie informacji podczas procesu tworzenia klucza. Mogą one dotyczyć na przykład hasła lub lokalizacji przechowywania kluczy. Po zakończeniu tego procesu zostaną utworzone dwa pliki: klucz publiczny i klucz prywatny.

* __Klucz publiczny__ służy do tworzenia klastra usługi HDInsight i ma rozszerzenie `.pub`.

* __Klucz prywatny__ służy do uwierzytelniania klienta w klastrze usługi HDInsight.

> [!IMPORTANT]  
> Klucze można zabezpieczyć przy użyciu hasła. W praktyce hasłem zabezpiecza się klucz prywatny. Nawet w przypadku uzyskania klucza prywatnego przez inną osobę do jego użycia jest potrzebne hasło.

### <a name="create-hdinsight-using-the-public-key"></a>Tworzenie klastrów usługi HDInsight przy użyciu klucza publicznego

| Metoda tworzenia | Sposób użycia klucza publicznego |
| ------- | ------- |
| Azure Portal | Usuń zaznaczenie pola __Użyj hasła logowania klastra dla protokołu SSH__, a następnie wybierz opcję __klucz publiczny__ jako typ uwierzytelniania SSH. Na koniec wybierz plik klucza publicznego lub wklej zawartość tekstową pliku w polu __Klucz publiczny SSH__.</br>![Okno dialogowe dotyczące klucza publicznego SSH w procesie tworzenia klastra usługi HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| Azure PowerShell | Użyj `-SshPublicKey` parametru polecenia cmdlet [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) i przekaż zawartość klucza publicznego jako ciąg.|
| Interfejs wiersza polecenia platformy Azure | Użyj `--sshPublicKey` parametru [`az hdinsight create`](/cli/azure/hdinsight#az-hdinsight-create) polecenia i przekaż zawartość klucza publicznego jako ciąg. |
| Szablon usługi Resource Manager | Przykład użycia kluczy SSH razem z szablonem można znaleźć w temacie [Deploy HDInsight on Linux with SSH key](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) (Wdrażanie usługi HDInsight w systemie Linux przy użyciu klucza SSH). Element `publicKeys` w pliku [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) umożliwia przekazanie kluczy do platformy Azure podczas tworzenia klastra. |

## <a name="authentication-password"></a>Uwierzytelnianie: hasło

Konta SSH mogą być chronione przy użyciu hasła. Po nawiązaniu połączenia z usługą HDInsight przy użyciu protokołu SSH zostanie wyświetlony monit o wprowadzenie hasła.

> [!WARNING]  
> W przypadku protokołu SSH firma Microsoft nie zaleca korzystania z uwierzytelniania za pomocą hasła. Hasła można złamać i są one podatne na ataki siłowe. Zamiast tego zalecamy używanie [kluczy SSH w celu uwierzytelniania](#sshkey).

> [!IMPORTANT]  
> Hasło do konta SSH wygasa 70 dni po utworzeniu klastra usługi HDInsight. Jeśli hasło wygaśnie, można je zmienić przy użyciu informacji zawartych w dokumencie [Zarządzanie usługą HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

### <a name="create-hdinsight-using-a-password"></a>Tworzenie klastrów usługi HDInsight przy użyciu hasła

| Metoda tworzenia | Sposób określenia hasła |
| --------------- | ---------------- |
| Azure Portal | Domyślnie hasło do konta użytkownika SSH jest takie samo jak hasło do konta umożliwiającego logowanie do klastra. Aby użyć innego hasła, usuń zaznaczenie pola __Użyj hasła logowania klastra dla protokołu SSH__, a następnie wprowadź hasło w polu __hasło ssh__ .</br>![Okno dialogowe dotyczące hasła SSH w procesie tworzenia klastra usługi HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| Azure PowerShell | Użyj `--SshCredential` parametru polecenia cmdlet [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) i przekaż `PSCredential` obiekt, który zawiera nazwę konta użytkownika ssh i hasło. |
| Interfejs wiersza polecenia platformy Azure | Użyj `--ssh-password` parametru [`az hdinsight create`](/cli/azure/hdinsight#az-hdinsight-create) polecenia i podaj wartość hasła. |
| Szablon usługi Resource Manager | Przykład użycia hasła razem z szablonem można znaleźć w temacie [Deploy HDInsight on Linux with SSH password](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) (Wdrażanie usługi HDInsight w systemie Linux przy użyciu hasła SSH). Element `linuxOperatingSystemProfile` w pliku [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) umożliwia przekazanie nazwy konta SSH i hasła do platformy Azure podczas tworzenia klastra.|

### <a name="change-the-ssh-password"></a>Zmienianie hasła SSH

Informacje dotyczące zmiany hasła użytkownika konta SSH można znaleźć w sekcji __Change passwords__ (Zmienianie haseł) dokumentu [Manage HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords) (Zarządzanie usługą HDInsight).

## <a name="authentication-domain-joined-hdinsight"></a>Przyłączony do domeny uwierzytelniania Usługa HDInsight

Jeśli korzystasz z __klastra usługi HDInsight przyłączonego do domeny__, musisz użyć `kinit` polecenia po nawiązaniu połączenia z użytkownikiem lokalnym SSH. Spowoduje to wyświetlenie monitu o podanie nazwy użytkownika domeny i hasła oraz uwierzytelnienie sesji w domenie usługi Azure Active Directory skojarzonej z klastrem.

Można również włączyć uwierzytelnianie Kerberos w każdym węźle przyłączonym do domeny (na przykład węzeł główny, węzeł krawędzi) do protokołu SSH przy użyciu konta domeny. Aby to zrobić, należy edytować plik konfiguracji sshd:

```bash
sudo vi /etc/ssh/sshd_config
```

usuń znaczniki komentarza i zmień `KerberosAuthentication` na `yes`

```bash
sudo service sshd restart
```

Użyj `klist` polecenia, aby sprawdzić, czy uwierzytelnianie Kerberos zakończyło się pomyślnie.

Aby uzyskać więcej informacji, zobacz [Configure domain-joined HDInsight](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) (Konfigurowanie przyłączonej do domeny usługi HDInsight).

## <a name="connect-to-nodes"></a>Nawiązywanie połączeń z węzłami

Węzły główne i węzeł brzegowy (jeśli taki istnieje) są dostępne przez Internet na portach 22 i 23.

* Podczas nawiązywania połączenia z __węzłami głównymi__ użyj portu __22__, aby łączyć się z podstawowym węzłem głównym, oraz portu __23__, aby łączyć się z pomocniczym węzłem głównym. W pełni kwalifikowana nazwa domeny do zastosowania to `clustername-ssh.azurehdinsight.net`, gdzie `clustername` jest nazwą Twojego klastra.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```

* W przypadku nawiązywania połączenia z __węzłem brzegowym__ użyj portu 22. W pełni kwalifikowana nazwa domeny to `edgenodename.clustername-ssh.azurehdinsight.net`, gdzie `edgenodename` jest nazwą podaną podczas tworzenia węzła brzegowego. `clustername` jest nazwą klastra.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]  
> Poprzednie przykłady zakładają, że korzystasz z uwierzytelniania przy użyciu hasła lub że uwierzytelnianie certyfikatu odbywa się automatycznie. Jeśli korzystasz z pary kluczy SSH do uwierzytelniania, a certyfikat nie jest używany automatycznie, skorzystaj z parametru `-i`, aby określić klucz prywatny. Na przykład `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

Po nawiązaniu połączenia zostanie wyświetlony monit o podanie nazwy użytkownika SSH i węzła, z którym nawiązano połączenie. Na przykład w przypadku połączenia z podstawowym węzłem głównym jako użytkownik `sshuser` wiersz polecenia będzie wyglądać następująco: `sshuser@<active-headnode-name>:~$`.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Łączenie z węzłami procesu roboczego i Apache dozorcy

Węzły procesu roboczego i węzły dozorcy nie są bezpośrednio dostępne z Internetu. Można uzyskać do nich dostęp z węzłów głównych lub węzłów krawędzi klastra. Poniżej przedstawiono ogólny zarys czynności, które należy wykonać w celu nawiązania połączenia z innymi węzłami:

1. Połącz się z węzłem głównym lub węzłem krawędzi za pomocą protokołu SSH:

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. Z poziomu połączenia SSH z węzłem głównym lub węzłem krawędzi połącz się z węzłem procesu roboczego w klastrze za pomocą polecenia `ssh`:

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    Aby pobrać listę nazw węzłów, zobacz [Zarządzanie usługą HDInsight przy użyciu dokumentu interfejsu API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) .

Jeśli konto SSH jest zabezpieczone przy użyciu __hasła__, wprowadź hasło podczas nawiązywania połączenia.

Jeśli konto SSH jest zabezpieczone przy użyciu __kluczy SSH__, upewnij się, że na kliencie jest włączone przekazywanie SSH.

> [!NOTE]  
> Innym sposobem uzyskiwania bezpośredniego dostępu do wszystkich węzłów w klastrze jest zainstalowanie usługi HDInsight w usłudze Azure Virtual Network. Pozwoli to na przyłączenie komputera zdalnego do tej samej sieci wirtualnej i uzyskiwanie bezpośredniego dostępu do wszystkich węzłów w klastrze.
>
> Aby uzyskać więcej informacji, zobacz [Planowanie sieci wirtualnej dla usługi HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="configure-ssh-agent-forwarding"></a>Konfigurowanie przekazywania przez agenta SSH

> [!IMPORTANT]  
> W poniższej procedurze założono korzystanie z systemu opartego na systemie Linux lub UNIX, ale działa ona również w przypadku powłoki Bash w systemie Windows 10. Jeśli te czynności nie działają w Twoim systemie, zapoznaj się z dokumentacją używanego klienta SSH.

1. Za pomocą edytora tekstów otwórz plik `~/.ssh/config`. Jeśli ten plik nie istnieje, możesz go utworzyć, wprowadzając polecenie `touch ~/.ssh/config` w wierszu polecenia.

2. Dodaj następujący tekst do pliku `config`.

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    Informacje w sekcji __Host__ zastąp adresem węzła, z którym łączysz się przy użyciu protokołu SSH. W poprzednim przykładzie użyto węzła krawędzi. Wpis ten umożliwia skonfigurowanie przekazywania przez agenta SSH dla określonego węzła.

3. Przetestuj agenta przekazywania SSH za pomocą następującego polecenia z terminala:

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    To polecenie zwraca informacje podobne do następującego tekstu:

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    Jeśli nic nie zostanie zwrócone, a następnie `ssh-agent` nie jest uruchomiony. Aby dowiedzieć się więcej, zapoznaj się z informacjami na temat skryptów uruchamiania agenta na stronie [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Korzystanie z agenta SSH z protokołem SSH) lub zajrzyj do dokumentacji używanego klienta SSH.

4. Po sprawdzeniu, że **Agent SSH** jest uruchomiony, użyj następującego programu, aby dodać klucz prywatny SSH do agenta:

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    Jeśli klucz prywatny jest przechowywany w innym pliku, zastąp `~/.ssh/id_rsa` ścieżką do pliku.

5. Połącz się z węzłem krawędzi lub węzłami głównymi klastra przy użyciu protokołu SSH. Następnie połącz się z węzłem procesu roboczego lub węzłem dozorcy przy użyciu polecenia SSH. Połączenie zostanie nawiązane przy użyciu przekazanego klucza.

## <a name="next-steps"></a>Następne kroki

* [Use SSH tunneling with HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) (Używanie tunelowania SSH z usługą HDInsight)
* [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node) (Używanie węzłów krawędzi w usłudze HDInsight)
* [Korzystanie z polecenia SCP z usługą HDInsight](./use-scp.md)