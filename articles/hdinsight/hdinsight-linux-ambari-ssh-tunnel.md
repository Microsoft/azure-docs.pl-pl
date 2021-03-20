---
title: Korzystanie z tunelowania SSH do uzyskiwania dostępu do usługi Azure HDInsight
description: Dowiedz się, jak bezpiecznie przeglądać zasoby internetowe hostowane w węzłach usługi HDInsight opartej na systemie Linux przy użyciu tunelu SSH.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 5bbc770fa6ae5ac69b2aa939f9d2c70bb01f5403
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945285"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Używanie tunelowania SSH do uzyskiwania dostępu do interfejsu użytkownika, JobHistory, NameNode, Apache Oozie i innych interfejsów użytkownika

Klastry usługi HDInsight zapewniają dostęp do interfejsu użytkownika sieci Web Apache Ambari przez Internet. Niektóre funkcje wymagają tunelu SSH. Na przykład nie można uzyskać dostępu do interfejsu użytkownika sieci Web Apache Oozie za pośrednictwem Internetu bez tunelu SSH.

## <a name="why-use-an-ssh-tunnel"></a>Dlaczego warto używać tunelu SSH

Niektóre menu w programie Ambari działają tylko za pośrednictwem tunelu SSH. Te menu polegają na witrynach sieci Web i usługach działających w innych typach węzłów, takich jak węzły procesu roboczego.

Następujący interfejsów użytkownika sieci Web wymaga tunelu SSH:

* JobHistory
* NameNode
* Stosy wątków
* Interfejs użytkownika sieci Web Oozie
* Interfejs użytkownika HBase Master i dzienników

Usługi zainstalowane z akcjami skryptu, które uwidaczniają usługę sieci Web, będą wymagać tunelu SSH. Odcień instalowany z akcją skryptu wymaga tunelu SSH w celu uzyskania dostępu do interfejsu użytkownika sieci Web.

> [!IMPORTANT]  
> Jeśli masz bezpośredni dostęp do usługi HDInsight za pośrednictwem sieci wirtualnej, nie musisz używać tuneli SSH. Aby zapoznać się z przykładem bezpośredniego dostępu do usługi HDInsight za pomocą sieci wirtualnej, zapoznaj się z dokumentem [Connect HDInsight do dokumentu w sieci lokalnej](connect-on-premises-network.md) .

## <a name="what-is-an-ssh-tunnel"></a>Co to jest tunel SSH

[Tunelowanie Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) łączy port na komputerze lokalnym z węzłem głównym w usłudze HDInsight. Ruch wysyłany do portu lokalnego jest kierowany przez połączenie SSH do węzła głównego. Żądanie jest rozpoznawane tak, jakby pochodziło z węzła głównego. Odpowiedź jest następnie kierowana przez tunel do stacji roboczej.

## <a name="prerequisites"></a>Wymagania wstępne

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Przeglądarka sieci Web, którą można skonfigurować do korzystania z serwera proxy SOCKS5.

    > [!WARNING]  
    > Obsługa serwera proxy SOCKS wbudowana w ustawienia internetowe systemu Windows nie obsługuje SOCKS5 i nie działa z krokami przedstawionymi w tym dokumencie. Poniższe przeglądarki korzystają z ustawień serwera proxy systemu Windows i nie są obecnie wykonywane z krokami przedstawionymi w tym dokumencie:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome korzysta również z ustawień serwera proxy systemu Windows. Można jednak zainstalować rozszerzenia, które obsługują SOCKS5. Zalecamy [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Tworzenie tunelu przy użyciu polecenia SSH

Użyj następującego polecenia, aby utworzyć tunel SSH przy użyciu `ssh` polecenia. Zastąp `sshuser` użytkownikowi SSH dla klastra usługi HDInsight i Zastąp ciąg `CLUSTERNAME` nazwą klastra usługi HDInsight:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

To polecenie tworzy połączenie, które kieruje ruch do lokalnego portu 9876 do klastra za pośrednictwem protokołu SSH. Dostępne opcje to:

|Opcja |Opis |
|---|---|
|D 9876|Port lokalny, który kieruje ruch przez tunel.|
|C|Kompresuj wszystkie dane, ponieważ ruch internetowy jest w większości tekstu.|
|2|Wymuś tylko wypróbowanie protokołu SSH w wersji 2.|
|q|Tryb cichy.|
|T|Wyłącz alokację pseudo-TTY, ponieważ po prostu przekazujesz port.|
|n|Zapobiegaj odczytywaniu STDIN, ponieważ właśnie przekazujesz port.|
|N|Nie wykonuj polecenia zdalnego, ponieważ po prostu przekazujesz port.|
|f|Uruchom w tle.|

Po zakończeniu wykonywania polecenia ruch wysyłany do portu 9876 na komputerze lokalnym jest kierowany do węzła głównego klastra.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Tworzenie tunelu przy użyciu polecenia Wykorzystaj

[](https://www.chiark.greenend.org.uk/~sgtatham/putty) Prezentacja jest graficznym klientem SSH dla systemu Windows. Jeśli nie wiesz, jak to zrobić, zobacz [dokumentację](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Wykonaj następujące kroki, aby utworzyć tunel SSH przy użyciu instrukcji:

### <a name="create-or-load-a-session"></a>Utwórz lub Załaduj sesję

1. Otwórz polecenie Wyróżnij i upewnij się, że w menu po lewej stronie została wybrana **sesja** . Jeśli sesja została już zapisana, wybierz nazwę sesji z listy **zapisane sesje** i wybierz pozycję **Załaduj**.

1. Jeśli nie masz jeszcze zapisanej sesji, wprowadź informacje dotyczące połączenia:

    |Właściwość |Wartość |
    |---|---|
    |Nazwa hosta (lub adres IP)|Adres SSH dla klastra usługi HDInsight. Na przykład **mójklaster-ssh.azurehdinsight.net**.|
    |Port|22|
    |Typ połączenia|Protokół SSH|

1. Wybierz pozycję **Zapisz**

    ![Sesja tworzenia usługi HDInsight](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. W sekcji **Kategoria** po lewej stronie okna dialogowego rozwiń węzeł **połączenie**, rozwiń węzeł **SSH**, a następnie wybierz pozycję **tunele**.

1. Podaj następujące informacje na temat **opcji kontrolujących formularz przekazywania portów SSH** :

    |Właściwość |Wartość |
    |---|---|
    |Port źródłowy|Port na kliencie, który ma zostać przesłany dalej. Na przykład **9876**.|
    |Element docelowy|Adres SSH dla klastra usługi HDInsight. Na przykład **mójklaster-ssh.azurehdinsight.net**.|
    |Dynamiczny|Umożliwia dynamiczne Routing serwerów proxy SOCKS.|

    ![Opcje tunelowania konfiguracji.](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. Wybierz pozycję **Dodaj** , aby dodać ustawienia, a następnie wybierz pozycję **Otwórz** , aby otworzyć połączenie SSH.

1. Po wyświetleniu monitu zaloguj się do serwera programu.

## <a name="use-the-tunnel-from-your-browser"></a>Używanie tunelu z przeglądarki

> [!IMPORTANT]  
> Kroki opisane w tej sekcji korzystają z przeglądarki Mozilla FireFox, ponieważ udostępniają one te same ustawienia serwera proxy na wszystkich platformach. Inne nowoczesne przeglądarki, takie jak Google Chrome, mogą wymagać rozszerzenia, takiego jak FoxyProxy, aby móc korzystać z tunelu.

1. Skonfiguruj przeglądarkę do używania **hosta lokalnego** i portu użytego podczas tworzenia tunelu jako serwera proxy **SOCKS v5** . Oto, jak wyglądają ustawienia w przeglądarce Firefox. Jeśli użyto innego portu niż 9876, zmień port na używany przez Ciebie:

    ![Ustawienia serwera proxy przeglądarki Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > Wybranie opcji **zdalny serwer DNS** rozwiązuje żądania systemu nazw domen (DNS) za pomocą klastra usługi HDInsight. To ustawienie umożliwia rozwiązanie systemu DNS przy użyciu węzła głównego klastra.

2. Sprawdź, czy tunel działa, odwiedzając witrynę taką jak [https://www.whatismyip.com/](https://www.whatismyip.com/) . Zwrócony adres IP powinien być używany przez centrum danych Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Weryfikowanie przy użyciu interfejsu użytkownika sieci Web Ambari

Po ustanowieniu klastra wykonaj następujące kroki, aby sprawdzić, czy można uzyskać dostęp do usługi Web interfejsów użytkownika w sieci Web programu Ambari:

1. W przeglądarce przejdź do adresu `http://headnodehost:8080`. `headnodehost`Adres jest wysyłany przez tunel do klastra i rozwiązywany do węzła głównego, na którym działa Ambari. Po wyświetleniu monitu wprowadź nazwę użytkownika administratora (administratora) i hasło do klastra. Użytkownik może zostać poproszony po raz drugi przez interfejs użytkownika sieci Web Ambari. Jeśli tak, wprowadź ponownie te informacje.

   > [!NOTE]  
   > W przypadku używania `http://headnodehost:8080` adresu do łączenia się z klastrem nawiązuje się połączenie za pośrednictwem tunelu. Komunikacja jest zabezpieczona przy użyciu tunelu SSH zamiast protokołu HTTPS. Aby nawiązać połączenie przez Internet przy użyciu protokołu HTTPS, użyj polecenia `https://clustername.azurehdinsight.net` , gdzie `clustername` jest nazwą klastra.

2. Z poziomu interfejsu użytkownika sieci Web Ambari wybierz opcję HDFS z listy znajdującej się po lewej stronie.

    ![Wybrana usługa Apache Ambari HDFS](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. Po wyświetleniu informacji o usłudze HDFS wybierz pozycję **szybkie linki**. Zostanie wyświetlona lista węzłów głównych klastra. Wybierz jeden z węzłów głównych, a następnie wybierz pozycję **interfejs użytkownika NameNode**.

    ![Obraz z rozwiniętym menu QuickLinks](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > Po wybraniu opcji __szybkie linki__ może zostać wyświetlony wskaźnik oczekiwania. Ten stan może wystąpić, jeśli masz wolne połączenie internetowe. Poczekaj chwilę lub dwie, aby dane były odbierane z serwera, a następnie spróbuj ponownie wykonać listę.
    >
    > Niektóre wpisy w menu **szybkie łącza** mogą być obcinane po prawej stronie ekranu. Jeśli tak, rozwiń menu przy użyciu myszy i użyj klawisza Strzałka w prawo, aby przewinąć ekran w prawo, aby zobaczyć resztę menu.

4. Zostanie wyświetlona strona podobna do następującej:

    ![Obraz interfejsu użytkownika usługi Hadoop NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Zwróć uwagę na adres URL tej strony; powinien być podobny do `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster` . Ten identyfikator URI używa wewnętrznej w pełni kwalifikowanej nazwy domeny (FQDN) węzła i jest dostępny tylko w przypadku korzystania z tunelu SSH.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak utworzyć tunel SSH i korzystać z niego, zapoznaj się z następującym dokumentem, aby poznać inne sposoby korzystania z usługi Ambari:

* [Zarządzanie klastrami usługi HDInsight przy użyciu usługi Apache Ambari](hdinsight-hadoop-manage-ambari.md)
