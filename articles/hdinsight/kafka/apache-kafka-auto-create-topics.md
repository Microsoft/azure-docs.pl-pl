---
title: Włącz automatyczne tworzenie tematów w Apache Kafka — Azure HDInsight
description: Dowiedz się, jak skonfigurować Apache Kafka w usłudze HDInsight, aby automatycznie tworzyć tematy. Można skonfigurować Kafka przez ustawienie `auto.create.topics.enable` wartości true w Ambari. Lub podczas tworzenia klastra za poorednictwem programu PowerShell lub szablonów Menedżer zasobów.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: b416623d6637cfe8e2c1cd795dd62553f8c0aed4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933214"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Jak skonfigurować Apache Kafka w usłudze HDInsight w celu automatycznego tworzenia tematów

Domyślnie Apache Kafka w usłudze HDInsight nie włącza automatycznego tworzenia tematu. Możesz włączyć tworzenie opcji autotemat dla istniejących klastrów przy użyciu platformy Apache Ambari. Podczas tworzenia nowego klastra Kafka przy użyciu szablonu Azure Resource Manager można również włączyć funkcję tworzenia autotematu.

## <a name="apache-ambari-web-ui"></a>Interfejs użytkownika sieci Web Apache Ambari

Aby włączyć automatyczne tworzenie tematów w istniejącym klastrze za pomocą interfejsu użytkownika sieci Web Ambari, wykonaj następujące czynności:

1. Na [Azure Portal](https://portal.azure.com)wybierz swój klaster Kafka.

1. Z **pulpitów nawigacyjnych klastra** wybierz pozycję **Ambari Home**.

    ![Obraz portalu z wybranym pulpitem nawigacyjnym klastra](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Po wyświetleniu monitu Uwierzytelnij się przy użyciu poświadczeń logowania (administratora) dla klastra. Zamiast tego można nawiązać połączenie z usługą Amabri bezpośrednio z lokalizacji, w `https://CLUSTERNAME.azurehdinsight.net/` której `CLUSTERNAME` jest nazwą klastra Kafka.

1. Wybierz usługę Kafka z listy znajdującej się po lewej stronie.

    ![Karta listy usługi Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Wybierz pozycję konfiguracje w środku strony.

    ![Karta konfiguracje usługi Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. W polu Filtr wprowadź wartość `auto.create` .

    ![Pole filtru wyszukiwania Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    To ustawienie filtruje listę właściwości i wyświetla `auto.create.topics.enable` ustawienie.

1. Zmień wartość `auto.create.topics.enable` na `true` , a następnie wybierz pozycję **Zapisz**. Dodaj notatkę, a następnie wybierz pozycję **Zapisz** ponownie.

    ![Obraz przedstawiający wpis Auto. Create. temats. Enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Wybierz usługę Kafka, wybierz pozycję __Uruchom ponownie__, a następnie wybierz pozycję __Uruchom ponownie wszystkie uwzględnione__. Po wyświetleniu monitu wybierz pozycję __Potwierdź ponowne uruchomienie wszystkich__.

    !["Apache Ambari restart wszystkie zmiany"](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Możesz również ustawić Ambari wartości za pomocą interfejsu API REST Ambari. Jest to zazwyczaj trudniejsze, ponieważ trzeba wykonać wiele wywołań REST, aby pobrać bieżącą konfigurację, zmodyfikować ją itp. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu dokumentu interfejsu API REST usługi Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Podczas tworzenia klastra Kafka przy użyciu szablonu Azure Resource Manager można bezpośrednio ustawić `auto.create.topics.enable` przez dodanie go w `kafka-broker` . Poniższy fragment kodu JSON pokazuje, jak ustawić tę wartość na `true` :

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób włączania automatycznego tworzenia tematu dla Apache Kafka w usłudze HDInsight. Aby dowiedzieć się więcej na temat pracy z usługą Kafka, zobacz następujące linki:

* [Analizowanie dzienników platformy Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikowanie danych między klastrami Apache Kafka](apache-kafka-mirroring.md)
