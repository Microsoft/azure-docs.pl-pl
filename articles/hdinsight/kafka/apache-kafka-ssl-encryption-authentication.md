---
title: Apache Kafka uwierzytelnianie & szyfrowania TLS — Azure HDInsight
description: Skonfiguruj szyfrowanie TLS na potrzeby komunikacji między klientami Kafka i brokerami Kafka, a także między brokerami Kafka. Skonfiguruj uwierzytelnianie SSL klientów.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: 9a95970647a26ea80db9f63fb8523c6a65cc5e06
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012309"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Konfigurowanie szyfrowania i uwierzytelniania TLS dla Apache Kafka w usłudze Azure HDInsight

W tym artykule opisano sposób konfigurowania szyfrowania Transport Layer Security (TLS), znanego wcześniej jako szyfrowanie SSL (SSL) między klientami Apache Kafka i Apache Kafkami brokerami. Pokazano w nim także, jak skonfigurować uwierzytelnianie klientów (czasami określana jako dwukierunkowa Metoda TLS).

> [!Important]
> Istnieją dwa klientów, których można używać do obsługi aplikacji Kafka: klient Java i klient konsoli. Tylko klient Java `ProducerConsumer.java` może korzystać z protokołu TLS zarówno do tworzenia, jak i zużywania. Klient producenta konsoli nie `console-producer.sh` współpracuje z protokołem TLS.

> [!Note]
> Producent konsoli usługi HDInsight Kafka z wersją 1,1 nie obsługuje protokołu SSL.

## <a name="apache-kafka-broker-setup"></a>Konfiguracja brokera Apache Kafka

Konfiguracja brokera Kafka TLS będzie używać czterech maszyn wirtualnych klastra usługi HDInsight w następujący sposób:

* węzła głównego 0 — urząd certyfikacji
* węzeł procesu roboczego 0, 1 i 2-brokerzy

> [!Note] 
> W tym przewodniku zastosowano certyfikaty z podpisem własnym, ale najbardziej bezpiecznym rozwiązaniem jest użycie certyfikatów wystawionych przez zaufane urzędy certyfikacji.

Podsumowanie procesu instalacji brokera przebiega następująco:

1. Następujące kroki są powtórzone w każdym z trzech węzłów procesu roboczego:

    1. Wygeneruj certyfikat.
    1. Utwórz żądanie podpisania certyfikatu.
    1. Wyślij żądanie podpisania certyfikatu do urzędu certyfikacji.
    1. Zaloguj się do urzędu certyfikacji i podpisz żądanie.
    1. Podpunkt połączenia z podpisanym certyfikatem z powrotem do węzła procesu roboczego.
    1. Punkt SCP certyfikatu publicznego urzędu certyfikacji z węzłem procesu roboczego.

1. Po uzyskaniu wszystkich certyfikatów należy umieścić certyfikaty w magazynie certyfikatów.
1. Przejdź do Ambari i Zmień konfiguracje.

Aby ukończyć konfigurowanie brokera, Skorzystaj z poniższych szczegółowych instrukcji:

> [!Important]
> W poniższych fragmentach kodu wnX jest skrót jednego z trzech węzłów procesu roboczego i powinien zostać zastąpiony `wn0` `wn1` lub `wn2` w zależności od potrzeb. `WorkerNode0_Name` i `HeadNode0_Name` powinny być zastąpione nazwami odpowiednich maszyn.

1. Wykonaj konfigurację początkową w węźle głównym 0, dla którego Usługa HDInsight wypełni rolę urzędu certyfikacji.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Wykonaj tę samą konfigurację początkową dla każdego z brokerów (węzły procesu roboczego 0, 1 i 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. W każdym z węzłów procesu roboczego wykonaj następujące kroki, korzystając z poniższego fragmentu kodu.
    1. Utwórz magazyn kluczy i wypełnij go nowym certyfikatem prywatnym.
    1. Utwórz żądanie podpisania certyfikatu.
    1. Punkt SCP żądania podpisania certyfikatu do urzędu certyfikacji (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Na komputerze urzędu certyfikacji Uruchom następujące polecenie, aby utworzyć pliki kluczy urzędu certyfikacji i urzędu certyfikacji:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Przejdź do komputera urzędu certyfikacji i podpisz wszystkie odebrane żądania podpisania certyfikatu:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Wyślij podpisane certyfikaty z powrotem do węzłów procesu roboczego z urzędu certyfikacji (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Wyślij certyfikat publiczny urzędu certyfikacji do każdego węzła procesu roboczego.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. W każdym węźle procesu roboczego Dodaj certyfikat publiczny urzędu certyfikacji do truststore i magazynu kluczy. Następnie Dodaj własny podpisany certyfikat węzła procesu roboczego do magazynu kluczy

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Zaktualizuj konfigurację Kafka, aby używać protokołu TLS i ponownie wybrokerów

Teraz można skonfigurować każdego brokera Kafka z magazynem kluczy i truststore i zaimportować odpowiednie certyfikaty. Następnie zmodyfikuj powiązane właściwości konfiguracji Kafka za pomocą Ambari, a następnie ponownie uruchom brokerów Kafka.

Aby ukończyć modyfikację konfiguracji, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal i wybierz klaster Apache Kafka usługi Azure HDInsight.
1. Przejdź do interfejsu użytkownika Ambari, klikając pozycję **Strona główna** w obszarze **pulpity nawigacyjne klastra**.
1. W obszarze **Broker Kafka** ustaw właściwość **detektory** na `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. W obszarze **Advanced Kafka-Broker** ustaw właściwość **Security. Inter. Broker. Protocol** na `SSL`

    ![Edytowanie właściwości konfiguracji protokołu SSL Kafka w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. W obszarze **Custom Kafka-Broker** ustaw właściwość **SSL. Client. auth** na `required` . Ten krok jest wymagany tylko w przypadku konfigurowania uwierzytelniania i szyfrowania.

    ![Edytowanie właściwości konfiguracji protokołu SSL Kafka w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. W przypadku HDI w wersji 3,6 Przejdź do interfejsu użytkownika Ambari i Dodaj następujące konfiguracje w obszarze **Advanced Kafka-ENV** i Właściwość **szablonu Kafka-ENV** .

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Poniżej znajduje się zrzut ekranu przedstawiający interfejs użytkownika konfiguracji Ambari z tymi zmianami.

    Dla HDI w wersji 3,6:

    ![Edytowanie właściwości szablonu Kafka-ENV w Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Dla HDI w wersji 4,0:

     ![Edytowanie właściwości szablonu Kafka-ENV w Ambari cztery](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)

1. Uruchom ponownie wszystkie brokerów Kafka.

## <a name="client-setup-without-authentication"></a>Instalacja klienta (bez uwierzytelniania)

Jeśli nie potrzebujesz uwierzytelniania, Podsumowanie kroków konfigurowania tylko szyfrowania TLS to:

1. Zaloguj się do urzędu certyfikacji (aktywny węzeł główny).
1. Skopiuj certyfikat urzędu certyfikacji na komputer kliencki z komputera urzędu certyfikacji (wn0).
1. Zaloguj się do komputera klienckiego (hn1) i przejdź do `~/ssl` folderu.
1. Zaimportuj certyfikat urzędu certyfikacji do truststore.
1. Zaimportuj certyfikat urzędu certyfikacji do magazynu kluczy.

Te kroki opisano szczegółowo w poniższych fragmentach kodu.

1. Zaloguj się do węzła urzędu certyfikacji.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Kopiowanie certyfikatu urzędu certyfikacji do komputera klienckiego

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Zaloguj się do komputera klienckiego (węzeł główny w stanie wstrzymania).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Zaimportuj certyfikat urzędu certyfikacji do truststore.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Zaimportuj certyfikat urzędu certyfikacji do magazynu kluczy.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Utwórz plik `client-ssl-auth.properties` na komputerze klienckim (hn1). Powinien on zawierać następujące wiersze:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Uruchom klienta administracyjnego z opcjami producent i klient, aby sprawdzić, czy zarówno producenci, jak i konsumenci pracują na porcie 9093. Zapoznaj się z sekcją [weryfikacja](apache-kafka-ssl-encryption-authentication.md#verification) poniżej, aby zapoznać się z krokami, które należy wykonać w celu zweryfikowania konfiguracji przy użyciu producenta/klienta

## <a name="client-setup-with-authentication"></a>Konfiguracja klienta (z uwierzytelnianiem)

> [!Note]
> Poniższe kroki są wymagane tylko w przypadku konfigurowania szyfrowania **i** uwierzytelniania TLS. Jeśli konfigurujesz tylko szyfrowanie, zobacz [Instalacja klienta bez uwierzytelniania](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

Poniższe cztery kroki podsumowują zadania niezbędne do ukończenia instalacji klienta:

1. Zaloguj się do komputera klienckiego (węzeł główny w stanie wstrzymania).
1. Utwórz magazyn kluczy języka Java i uzyskaj podpisany certyfikat dla brokera. Następnie skopiuj certyfikat na maszynę wirtualną, na której działa urząd certyfikacji.
1. Przejdź do komputera urzędu certyfikacji (aktywny węzeł główny), aby podpisać certyfikat klienta.
1. Przejdź do komputera klienckiego (węzeł główny w stanie wstrzymania) i przejdź do `~/ssl` folderu. Skopiuj podpisany certyfikat na komputer kliencki.

Poniżej przedstawiono szczegóły każdego kroku.

1. Zaloguj się do komputera klienckiego (węzeł główny w stanie wstrzymania).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Usuń istniejący katalog SSL.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Utwórz magazyn kluczy Java i Utwórz żądanie podpisania certyfikatu. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Kopiuj żądanie podpisania certyfikatu do urzędu certyfikacji

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Przejdź do komputera urzędu certyfikacji (aktywny węzeł główny) i podpisz certyfikat klienta.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Skopiuj podpisany certyfikat klienta z urzędu certyfikacji (aktywny węzeł główny) do komputera klienckiego.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Kopiowanie certyfikatu urzędu certyfikacji do komputera klienckiego

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Zaloguj się do komputera klienckiego (węzeł główny w stanie wstrzymania) i przejdź do katalogu protokołu SSL.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Utwórz magazyn klienta z podpisanym certyfikatem i zaimportuj certyfikat urzędu certyfikacji do magazynu kluczy i truststore na komputerze klienckim (hn1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Utwórz plik `client-ssl-auth.properties` na komputerze klienckim (hn1). Powinien on zawierać następujące wiersze:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Weryfikacja

Wykonaj te kroki na komputerze klienckim.

> [!Note]
> Jeśli zainstalowano usługi HDInsight 4,0 i Kafka 2,1, można użyć producentów/użytkowników konsoli do zweryfikowania instalacji. W przeciwnym razie uruchom producenta Kafka na porcie 9092 i Wyślij komunikaty do tematu, a następnie użyj klienta Kafka na porcie 9093, który korzysta z protokołu TLS.

### <a name="kafka-21-or-above"></a>Kafka 2,1 lub nowszy

1. Utwórz temat, jeśli jeszcze nie istnieje.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Uruchom producenta konsoli i podaj ścieżkę `client-ssl-auth.properties` jako plik konfiguracji dla producenta.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Otwórz inne połączenie SSH z maszyną klienta i uruchom użytkownika konsoli i podaj ścieżkę `client-ssl-auth.properties` jako plik konfiguracji dla konsumenta.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1,1

1. Utwórz temat, jeśli jeszcze nie istnieje.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Uruchom producenta konsoli i podaj ścieżkę do elementu Client-SSL-auth. Properties jako plik konfiguracyjny producenta.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Otwórz inne połączenie SSH z maszyną klienta i uruchom użytkownika konsoli i podaj ścieżkę `client-ssl-auth.properties` jako plik konfiguracji dla konsumenta.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Następne kroki

* [Co to jest platforma Apache Kafka w usłudze HDInsight?](apache-kafka-introduction.md)
