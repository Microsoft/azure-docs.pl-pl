---
title: Rejestr schematów platformy Azure w usłudze Event Hubs (wersja zapoznawcza)
description: Ten artykuł zawiera omówienie obsługi rejestru schematu w usłudze Azure Event Hubs (wersja zapoznawcza).
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: a0cc54f6ada1a5900e817c8e22fc192cd73f6550
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91653574"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>Rejestr schematów platformy Azure w usłudze Event Hubs (wersja zapoznawcza)
W wielu scenariuszach przesyłania strumieniowego zdarzeń i przesyłania komunikatów zdarzenie lub komunikat zawiera dane strukturalne, które są serializowane lub deserializowane przy użyciu formatu opartego na schemacie, takiego jak Apache Avro. Zarówno nadawcy, jak i odbiorcy mogą chcieć sprawdzić integralność danych za pomocą dokumentu schematu, jak w schemacie JSON. W przypadku formatów opartych na schemacie udostępnianie schematu dla konsumenta wiadomości jest wymagane przez odbiorcę do deserializacji danych. 

**Usługa Azure Schema Registry** jest funkcją Event Hubs, która udostępnia centralne repozytorium dla dokumentów schematów dla aplikacji opartych na zdarzeniach i obsługujących komunikaty. Zapewnia ona elastyczność dla aplikacji producentów i konsumentów do wymiany danych bez konieczności zarządzania i udostępniania schematu między nimi, a także do rozwijania z różnymi stawkami. Rejestr schematu udostępnia także prostą strukturę ładu dla schematów wielokrotnego użytku i definiuje relację między schematami za pomocą konstrukcji grupującej (grup schematów).

> [!NOTE]
> - Funkcja **Rejestr schematu** jest obecnie w **wersji zapoznawczej**i nie jest zalecana w przypadku obciążeń produkcyjnych.
> - Funkcja jest dostępna tylko w warstwach **standardowa** i **dedykowana** , a nie w warstwie **podstawowa** .

W przypadku platform serializacji opartych na schemacie, takich jak Apache Avro, metadane serializacji eksternalizacji do współużytkowanych schematów mogą również pomóc w znaczącym zmniejszeniu obciążenia dla poszczególnych komunikatów typu i nazw pól zawartych w każdym zestawie danych, tak jak w przypadku formatów oznakowanych, takich jak JSON. Schematy przechowywane wraz ze zdarzeniami i wewnątrz infrastruktury zdarzeń zapewniają, że metadane wymagane do serializacji/deserializacji są zawsze dostępne, a schematy nie mogą być umieszczane. 

## <a name="event-hubs-namespace"></a>Przestrzeń nazw usługi Event Hubs
Przestrzeń nazw Event Hubs teraz może hostować grupy schematów obok centrów zdarzeń (lub tematów Kafka). Hostuje rejestr schematu i może mieć wiele grup schematów. Mimo że nie są hostowane w usłudze Azure Event Hubs, rejestr schematu może być używany uniwersalnie ze wszystkimi usługami Azure Messaging i innymi komunikatami lub brokerem zdarzeń. Każda z tych grup schematu to osobne, zabezpieczone repozytorium dla zestawu schematów. Grupy mogą być wyrównane z określoną aplikacją lub jednostką organizacyjną. 

## <a name="schema-groups"></a>Grupy schematów
Grupa schematów to logiczna Grupa podobnych schematów na podstawie kryteriów firmy. Grupa schematów może zawierać wiele wersji schematu. Ustawienie wymuszania zgodności w grupie schematów może pomóc w zapewnieniu zgodności z poprzednimi wersjami schematu.

Granica zabezpieczeń nałożona przez mechanizm grupowania pomaga zapewnić, że tajemnice handlowe nie są przypadkowo wyciekiem za pomocą metadanych w sytuacjach, gdy przestrzeń nazw jest współdzielona przez wielu partnerów. Umożliwia także właścicielom aplikacji Zarządzanie schematami niezależnymi od innych aplikacji, które współużytkują tę samą przestrzeń nazw.


## <a name="schemas"></a>Schematy
Schematy definiują umowę między producentami i użytkownikami. Schemat zdefiniowany w rejestrze schematu Event Hubs ułatwia zarządzanie kontraktem poza danymi zdarzenia, co powoduje usunięcie obciążenia ładunku. Schemat ma nazwę, typ (przykład: Record, Array itd.), tryb zgodności (brak, do przodu, do tyłu, pełny) i typ serializacji (tylko Avro teraz). Można utworzyć wiele wersji schematu i pobrać i użyć określonej wersji schematu. 

## <a name="client-sdks"></a>Zestawy SDK klienta
Można użyć jednej z następujących bibliotek, które zawierają serializator Avro, którego można użyć do serializacji i deserializacji ładunków zawierających identyfikatory schematu rejestru schematu i dane zakodowane w Avro.

- [.NET — Microsoft. Azure. Data. SchemaRegistry. ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java-Azure-Data-schemaregistry-Avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python-Azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/avro/samples) -Kafka i deserializacji platformy Apache Avro, których kopie zapasowe zostały wykonane przez usługę Azure Schema Registry. Serializator klienta Apache Kafka klienta Java dla rejestru schematu platformy Azure może być używany w dowolnym scenariuszu Apache Kafka i z dowolnym® Apache Kafkam wdrożenia lub usługą w chmurze. 

Na poniższej ilustracji przedstawiono przepływ informacji rejestru schematu z Event Hubs: 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="Diagram przepływu":::

## <a name="standard-vs-dedicated-limits"></a>Limity standardowe a dedykowane
Dla limitów (na przykład: liczba grup schematu w przestrzeni nazw), które są takie same i różne dla warstw standardowa i dedykowana Event Hubs, zobacz [limity rejestru schematu](../azure-resource-manager/management/azure-subscription-service-limits.md#schema-registry-limitations)

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
W przypadku programistycznego uzyskiwania dostępu do rejestru schematu należy zarejestrować aplikację w usłudze Azure Active Directory (Azure AD) i dodać podmiot zabezpieczeń aplikacji do jednej z ról kontroli dostępu opartej na rolach (RBAC):

| Rola | Opis | 
| ---- | ----------- | 
| Właściciel | Odczytuj, zapisuj i usuwaj grupy i schematy rejestru schematu. |
| Współautor | Odczytuj, zapisuj i usuwaj grupy i schematy rejestru schematu. |
| [Czytnik rejestru schematu (wersja zapoznawcza)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Odczytuj i wyświetlaj listę grup i schematów rejestru schematu. |
| [Współautor rejestru schematu (wersja zapoznawcza)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Odczytuj, zapisuj i usuwaj grupy i schematy rejestru schematu. |

Aby uzyskać instrukcje dotyczące tworzenia rejestrowania aplikacji przy użyciu Azure Portal, zobacz [Rejestrowanie aplikacji w usłudze Azure AD](../active-directory/develop/quickstart-register-app.md). Zanotuj identyfikator klienta (Identyfikator aplikacji), identyfikator dzierżawy i klucz tajny do użycia w kodzie. 

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć rejestr schematu przy użyciu Azure Portal, zobacz [Tworzenie rejestru schematu Event Hubs przy użyciu Azure Portal](create-schema-registry.md).
- Zobacz następujący **schemat schematu Avro przykłady biblioteki klienta** .
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Kafka Avro, integracja z usługą Azure Schema Registry](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/avro/samples)
