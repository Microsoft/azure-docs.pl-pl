---
title: Migrowanie do usługi Azure Event Hubs dla Apache Kafka
description: W tym artykule wyjaśniono, jak migrować klientów z Apache Kafka na platformę Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d9f3775a85df5a881c2c38566628e4e1d4d8c40e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90061448"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migracja do usługi Azure Event Hubs z ekosystemu Apache Kafka
Usługa Azure Event Hubs udostępnia punkt końcowy Apache Kafka, który umożliwia nawiązywanie połączenia z Event Hubs przy użyciu protokołu Kafka. Wprowadzając minimalne zmiany w istniejącej aplikacji Kafka, możesz połączyć się z usługą Azure Event Hubs i skorzystać korzyści z ekosystemu platformy Azure. Event Hubs współpracuje z wieloma istniejącymi aplikacjami Kafka, w tym narzędzia MirrorMaker. Aby uzyskać więcej informacji, zobacz [Event Hubs dla Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="pre-migration"></a>Przed migracją 

### <a name="create-an-azure-account"></a>Tworzenie konta platformy Azure
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

### <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs
Wykonaj instrukcje krok po kroku w artykule [Tworzenie centrum zdarzeń](event-hubs-create.md) , aby utworzyć Event Hubs przestrzeni nazw i centrum zdarzeń. 

### <a name="connection-string"></a>Parametry połączenia
Wykonaj kroki opisane w artykule [pobieranie parametrów połączenia z portalu](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) . I zanotuj parametry połączenia w celu późniejszego użycia. 

### <a name="fully-qualified-domain-name-fqdn"></a>W pełni kwalifikowana nazwa domeny (FQDN)
Może być również potrzebna nazwa FQDN wskazująca na przestrzeń nazw centrum zdarzeń. Nazwę FQDN można znaleźć w parametrach połączenia w następujący sposób:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Jeśli przestrzeń nazw Event Hubs została wdrożona w chmurze innej niż publiczna, nazwa domeny może się różnić (na przykład \* . ServiceBus.chinacloudapi.CN, \* . ServiceBus.usgovcloudapi.NET lub \* . ServiceBus.cloudapi.de).

## <a name="migration"></a>Migracja 

### <a name="update-your-kafka-client-configuration"></a>Aktualizowanie konfiguracji klienta Kafka

Aby nawiązać połączenie z centrum zdarzeń z obsługą Kafka, należy zaktualizować konfiguracje klienta Kafka. Jeśli masz problemy z znalezieniem Twojego rozwiązania, spróbuj wyszukać miejsce, `bootstrap.servers` w którym jest ustawiony w aplikacji.

Wstaw następujące konfiguracje, wszędzie tam, gdzie jest to zrozumiałe dla aplikacji. Upewnij się, że Zaktualizowano `bootstrap.servers` wartości i, `sasl.jaas.config` Aby skierować klienta do Event Hubs punktu końcowego Kafka z prawidłowym uwierzytelnianiem. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Jeśli `sasl.jaas.config` w Twojej strukturze nie jest obsługiwana konfiguracja, Znajdź konfiguracje, które są używane do ustawiania nazwy użytkownika i hasła SASL, i Użyj zamiast nich. Ustaw nazwę użytkownika na `$ConnectionString` i hasło do parametrów połączenia Event Hubs.

## <a name="post-migration"></a>Po migracji
Uruchom aplikację Kafka, która wysyła zdarzenia do centrum zdarzeń. Następnie sprawdź, czy centrum zdarzeń odbiera zdarzenia przy użyciu Azure Portal. Na stronie **Przegląd** w obszarze nazw Event Hubs przejdź do widoku **komunikaty** w sekcji **metryki** . Odśwież stronę, aby zaktualizować wykres. Wyświetlenie komunikatów zostało odebrane może potrwać kilka sekund. 

[![Sprawdź, czy centrum zdarzeń odebrało komunikaty](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Event Hubs i Event Hubs dla Kafka, zobacz następujące artykuły:  

- [Apache Kafka Przewodnik rozwiązywania problemów dla Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Często zadawane pytania — Event Hubs dla Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Przewodnik dla deweloperów Apache Kafka dla platformy Azure Event Hubs](apache-kafka-developer-guide.md)
- [Zalecane konfiguracje](apache-kafka-configurations.md)