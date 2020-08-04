---
title: Korzystanie z centrum zdarzeń w programie Apache Kafka App — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera informacje na temat obsługi Apache Kafka przez usługę Azure Event Hubs.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ab29c9c4270514e95752ab2bbd085ffe1b0a2fb0
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534876"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Korzystanie z Event Hubs platformy Azure z aplikacji Apache Kafka
Event Hubs udostępnia punkt końcowy Kafka, który może być używany przez istniejące aplikacje oparte na Kafka jako alternatywę dla uruchamiania własnego klastra Kafka. Event Hubs obsługuje [protokół Apache Kafka 1,0 i nowsze](https://kafka.apache.org/documentation/)i współpracuje z istniejącymi aplikacjami Kafka, w tym narzędzia MirrorMaker.  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Co zapewnia Event Hubs Kafka?

Funkcja Event Hubs for Kafka udostępnia nagłówek protokołu w oparciu o usługę Azure Event Hubs, która jest binarna zgodna z Kafka wersjami 1,0 i nowszymi w przypadku odczytywania i zapisywania w tematach Kafka. Możesz rozpocząć korzystanie z punktu końcowego Kafka z aplikacji bez zmiany kodu, ale minimalnej zmiany konfiguracji. Należy zaktualizować parametry połączenia w konfiguracjach, aby wskazywały punkt końcowy Kafka uwidoczniony przez centrum zdarzeń zamiast wskazywać klaster Kafka. Następnie można rozpocząć przesyłanie strumieniowe zdarzeń z aplikacji korzystających z protokołu Kafka do Event Hubs. Ta integracja obsługuje również platformy, takie jak [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), które są obecnie dostępne w wersji zapoznawczej. 

Kafka koncepcyjnie i Event Hubs są niemal identyczne: są to dzienniki partycjonowane dla danych przesyłanych strumieniowo. W poniższej tabeli przedstawiono koncepcje między Kafka i Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapowanie koncepcyjne Kafka i centrum zdarzeń

| Koncepcja Kafka | Koncepcja Event Hubs|
| --- | --- |
| Klaster | Przestrzeń nazw |
| Temat | Centrum zdarzeń |
| Partycja | Partycja|
| Grupa konsumentów | Grupa konsumentów |
| Przesunięcie | Przesunięcie|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kluczowe różnice między Kafka i Event Hubs

Gdy [Apache Kafka](https://kafka.apache.org/) to oprogramowanie, które można uruchomić w dowolnym miejscu, Event Hubs jest usługą w chmurze podobną do BLOB Storage platformy Azure. Brak serwerów lub sieci do zarządzania i nie ma żadnych brokerów do skonfigurowania. Tworzysz przestrzeń nazw, która jest nazwą FQDN, w której znajdują się tematy, a następnie utwórz Event Hubs lub tematy w tej przestrzeni nazw. Aby uzyskać więcej informacji na temat Event Hubs i przestrzeni nazw, zobacz [Event Hubs Features](event-hubs-features.md#namespace). Jako usługa w chmurze Event Hubs używa jednego stabilnego wirtualnego adresu IP jako punktu końcowego, dlatego klienci nie muszą znać brokerów ani maszyn w klastrze. 

Skalowanie w Event Hubs jest kontrolowane przez liczbę jednostek przepływności, które można kupić, a każda jednostka przepływności entitling do 1 MB na sekundę lub 1000 zdarzeń na sekundę. Domyślnie Event Hubs skaluje jednostki przepływności po osiągnięciu limitu przez funkcję [autostartu](event-hubs-auto-inflate.md) . Ta funkcja działa również z funkcją Event Hubs for Kafka. 

### <a name="security-and-authentication"></a>Zabezpieczenia i uwierzytelnianie
Za każdym razem, gdy publikujesz lub zużywasz zdarzenia z Event Hubs Kafka, klient próbuje uzyskać dostęp do zasobów Event Hubs. Chcesz się upewnić, że zasoby są dostępne za pomocą autoryzowanej jednostki. W przypadku korzystania z protokołu Apache Kafka z klientami można ustawić konfigurację uwierzytelniania i szyfrowania przy użyciu mechanizmów SASL. W przypadku korzystania z Event Hubs dla Kafka wymagane jest szyfrowanie TLS (ponieważ wszystkie dane przesyłane za pomocą Event Hubs są szyfrowane przy użyciu protokołu TLS). Można to zrobić, określając opcję SASL_SSL w pliku konfiguracji. 

Usługa Azure Event Hubs udostępnia wiele opcji autoryzacji dostępu do bezpiecznych zasobów. 

- OAuth
- Sygnatura dostępu współdzielonego (SAS)

#### <a name="oauth"></a>OAuth
Event Hubs integruje się z usługą Azure Active Directory (Azure AD), która zapewnia scentralizowany serwer autoryzacji zgodny z protokołem **OAuth** 2,0. W usłudze Azure AD można używać kontroli dostępu opartej na rolach (RBAC) do udzielania szczegółowych uprawnień do tożsamości klientów. Tej funkcji można używać z klientami Kafka przez określenie **SASL_SSL** protokołu i **OAUTHBEARER** dla mechanizmu. Aby uzyskać szczegółowe informacje o rolach i poziomach platformy Azure w celu uzyskania dostępu do zakresu, zobacz [Autoryzuj dostęp za pomocą usługi Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Sygnatura dostępu współdzielonego
Event Hubs zapewnia także **sygnatury dostępu współdzielonego (SAS)** dla delegowanego dostępu do Event Hubs dla zasobów Kafka. Autoryzowanie dostępu przy użyciu mechanizmu uwierzytelniania OAuth 2,0 zapewnia doskonałe zabezpieczenia i łatwość użycia w porównaniu z SAS. Wbudowane role mogą również wyeliminować potrzebę autoryzacji opartej na listach ACL, która musi być utrzymywana i zarządzana przez użytkownika. Tej funkcji można używać z klientami Kafka przez określenie **SASL_SSL** dla protokołu i **zwykłego** dla mechanizmu. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!NOTE]
> W przypadku korzystania z uwierzytelniania SAS z klientami Kafka ustanowione połączenia nie są rozłączane po ponownym wygenerowaniu klucza SAS. 

#### <a name="samples"></a>Samples 
**Samouczek** zawierający instrukcje krok po kroku dotyczące tworzenia centrum zdarzeń i uzyskiwania dostępu do niego przy użyciu SAS lub OAuth zawiera temat [Szybki Start: przesyłanie strumieniowe danych za pomocą Event Hubs przy użyciu protokołu Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Aby uzyskać więcej **przykładów** , które pokazują, jak używać protokołu OAuth z Event Hubs for Kafka, zobacz [przykłady w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Inne funkcje Event Hubs dostępne dla Kafka

Funkcja Event Hubs for Kafka umożliwia pisanie przy użyciu jednego protokołu i odczytywanie go w inny sposób, dzięki czemu aktualni producenci Kafka mogą kontynuować publikowanie za pośrednictwem Kafkau i można dodać czytelników z Event Hubs, takimi jak Azure Stream Analytics lub Azure Functions. Ponadto funkcje Event Hubs, takie jak [przechwytywanie](event-hubs-capture-overview.md) i replikacja [geograficzna](event-hubs-geo-dr.md) , działają również z funkcją Event Hubs for Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funkcje, które nie są jeszcze obsługiwane 

Poniżej znajduje się lista funkcji Kafka, które nie są jeszcze obsługiwane:

*   Transakcja
*   Kompresja
*   Przechowywanie na podstawie rozmiaru
*   Kompaktowanie dziennika
*   Obsługa interfejsu API HTTP Kafka
*   Kafka strumienie

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono wprowadzenie do Event Hubs Kafka. Aby dowiedzieć się więcej, zobacz [Apache Kafka Przewodnik dla deweloperów dla Event Hubs platformy Azure](apache-kafka-developer-guide.md).


