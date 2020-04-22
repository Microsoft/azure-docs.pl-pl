---
title: Migrowanie do centrów zdarzeń platformy Azure dla platformy Apache Kafka
description: W tym artykule pokazano, jak konsumenci i producenci korzystający z różnych protokołów (AMQP, Apache Kafka i HTTPS) mogą wymieniać zdarzenia podczas korzystania z usługi Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 32b08e565b86af8f6373c9848211646128bb346d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677368"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migrowanie do centrów zdarzeń platformy Azure dla ekosystemów platformy Apache Kafka
Usługa Azure Event Hubs udostępnia punkt końcowy platformy Apache Kafka, który umożliwia łączenie się z centrum zdarzeń przy użyciu protokołu Platformy Kafka. W celu wniesienia minimalnych zmian w istniejącej aplikacji platformy Kafka można połączyć się z usługą Azure Event Hubs i czerpać korzyści z ekosystemu platformy Azure. Centra zdarzeń dla platformy Kafka obsługują [apache kafka w wersji 1.0](https://kafka.apache.org/10/documentation.html) i nowszych.

## <a name="pre-migration"></a>Czynności przed migracją 

### <a name="create-an-azure-account"></a>Tworzenie konta platformy Azure
Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed rozpoczęciem.

### <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs
Postępuj zgodnie z instrukcjami krok po kroku w artykule [Tworzenie centrum zdarzeń,](event-hubs-create.md) aby utworzyć obszar nazw centrum zdarzeń i centrum zdarzeń. 

### <a name="connection-string"></a>Parametry połączenia
Wykonaj kroki z pobierz ciąg połączenia z artykułu [portalu.](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) I zanotuj w dół parametry połączenia do późniejszego użycia. 

### <a name="fully-qualified-domain-name-fqdn"></a>W pełni kwalifikowana nazwa domeny (FQDN)
Może być również konieczne FQDN, który wskazuje obszar nazw Centrum zdarzeń. FQDN można znaleźć w ciągu połączenia w następujący sposób:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Jeśli obszar nazw Centrum zdarzeń jest wdrażany w chmurze niepublicznej, nazwa \*domeny może się \*różnić (na przykład \*.servicebus.chinacloudapi.cn, .servicebus.usgovcloudapi.net lub .servicebus.cloudapi.de).

## <a name="migration"></a>Migracja 

### <a name="update-your-kafka-client-configuration"></a>Aktualizowanie konfiguracji klienta platformy Kafka

Aby połączyć się z Centrum zdarzeń z obsługą platformy Kafka, musisz zaktualizować konfiguracje klienta platformy Kafka. Jeśli masz problemy ze znalezieniem swojego, `bootstrap.servers` spróbuj wyszukać, gdzie jest ustawiony w aplikacji.

Wstaw następujące configs wszędzie tam, gdzie ma sens w aplikacji. Upewnij się, `bootstrap.servers` aby `sasl.jaas.config` zaktualizować i wartości, aby skierować klienta do punktu końcowego usługi Event Hubs Kafka z poprawnym uwierzytelnianiem. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Jeśli `sasl.jaas.config` nie jest obsługiwana konfiguracja w ramach, znajdź konfiguracje, które są używane do ustawiania nazwy użytkownika i hasła SASL i zamiast nich używać. Ustaw nazwę `$ConnectionString` użytkownika i hasło do ciągu połączenia usługi Event Hubs.

## <a name="post-migration"></a>Czynności po migracji
Uruchom aplikację platformy Kafka, która wysyła zdarzenia do centrum zdarzeń. Następnie sprawdź, czy centrum zdarzeń odbiera zdarzenia przy użyciu witryny Azure portal. Na stronie **Przegląd** obszaru nazw centrum zdarzeń przełącz się do widoku **Wiadomości** w sekcji **Metryki.** Odśwież stronę, aby zaktualizować wykres. Może upłynąć kilka sekund, aby pokazać, że wiadomości zostały odebrane. 

[![Sprawdź, czy centrum zdarzeń odebrało wiadomości](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o centrach zdarzeń i centrach zdarzeń dla platformy Kafka, zobacz następujące artykuły:  

- [Apache Kafka przewodnik rozwiązywania problemów dla Centrum zdarzeń](apache-kafka-troubleshooting-guide.md)
- [Często zadawane pytania - Event Hubs for Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Apache Kafka — przewodnik dla deweloperów usługi Azure Event Hubs](apache-kafka-developer-guide.md)
- [Zalecane konfiguracje](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)