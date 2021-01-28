---
title: Rozwiązywanie problemów z PRZĘDZą w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z Apache Hadoop PRZĘDZą i usługą Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: b5b8ccbfd3e39415416cfc8fabcf9614f5934042
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931407"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z platformą YARN usługi Apache Hadoop za pomocą usługi Azure HDInsight

Poznaj najważniejsze problemy i ich rozwiązania podczas pracy z Apache Hadoop ładunku PRZĘDZy w Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Jak mogę utworzyć nową kolejkę PRZĘDZy w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązania

Wykonaj następujące kroki w programie Ambari, aby utworzyć nową kolejkę PRZĘDZy, a następnie Zrównoważ alokację pojemności między wszystkimi kolejkami.

W tym przykładzie dwie istniejące kolejki (**domyślne** i **thriftsvr**) są zmieniane z pojemności 50% na 25% pojemności, która zapewnia nową kolejkę (Spark) 50% pojemności.

| Kolejka | Pojemność | Maksymalna pojemność |
| --- | --- | --- |
| default | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Wybierz ikonę **widoki Ambari** , a następnie wybierz wzorzec siatki. Następnie wybierz pozycję **Menedżer kolejki przędzy**.

    ![Menedżer kolejki PRZĘDZy Ambari pulpitu nawigacyjnego Apache](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Wybierz kolejkę **domyślną** .

    ![Apache Ambari — wybór kolejki domyślnej](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. Dla kolejki **domyślnej** Zmień **pojemność** z 50% na 25%. W przypadku kolejki **thriftsvr** Zmień **pojemność** na 25%.

    ![Zmień pojemność na 25% dla kolejek domyślnych i thriftsvr](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Aby utworzyć nową kolejkę, wybierz pozycję **Dodaj kolejkę**.

    ![Kolejka dodawania pulpitu nawigacyjnego PRZĘDZy Ambari Apache](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Nazwij nową kolejkę.

    ![Kolejka nazw pulpitu nawigacyjnego PRZĘDZy Ambari](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Pozostaw wartości **wydajności** o 50%, a następnie wybierz przycisk **Akcje** .

    ![Akcja wybierania nici Ambari Apache](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Wybierz pozycję **Zapisz i Odśwież kolejki**.

    ![Wybierz kolejno pozycje Zapisz i Odśwież kolejki](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Te zmiany są natychmiast widoczne w interfejsie użytkownika harmonogramu PRZĘDZy.

### <a name="additional-reading"></a>Materiały uzupełniające

- [Apache Hadoop PRZĘDZy CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Jak mogę pobrać dzienników PRZĘDZy z klastra?

### <a name="resolution-steps"></a>Kroki rozwiązania

1. Połącz się z klastrem usługi HDInsight przy użyciu klienta Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [dodatkowy odczyt](#additional-reading-2).

1. Aby wyświetlić listę wszystkich identyfikatorów aplikacji, które są aktualnie uruchomione, uruchom następujące polecenie:

    ```apache
    yarn top
    ```

    Identyfikatory są wymienione w kolumnie Identyfikator **aplikacji** . Dzienniki można pobrać z kolumny Identyfikator **aplikacji** .

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Aby pobrać dzienniki kontenerów PRZĘDZy dla wszystkich wzorców aplikacji, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie amlogs.txt.

1. Aby pobrać dzienniki kontenerów PRZĘDZy tylko dla najnowszego wzorca aplikacji, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie latestamlogs.txt.

1. Aby pobrać dzienniki kontenerów PRZĘDZy dla pierwszych dwóch wzorców aplikacji, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie first2amlogs.txt.

1. Aby pobrać wszystkie dzienniki kontenerów PRZĘDZy, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie logs.txt.

1. Aby pobrać dziennik kontenera PRZĘDZy dla określonego kontenera, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Materiały uzupełniające

- [Nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md)
- [Apache Hadoop koncepcji i aplikacji PRZĘDZy](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]