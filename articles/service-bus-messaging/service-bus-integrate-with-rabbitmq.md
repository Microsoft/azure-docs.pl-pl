---
title: Jak zintegrować RabbitMQ z Azure Service Bus
description: Przewodnik krok po kroku dotyczący integracji RabbitMQ z usługą Azure Service Bus
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 71ea4dfcc164d5b8a8ba8bb411d529ce58a68f4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933742"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>Jak zintegrować RabbitMQ z Azure Service Bus

W tym przewodniku dowiesz się, jak wysyłać komunikaty z usługi RabbitMQ do Azure Service Bus.

Poniżej przedstawiono kilka scenariuszy, w których firma Microsoft może korzystać z tych możliwości:

- **Ustawienia programu Edge**: mamy konfigurację graniczną, w której wysyłamy wiadomości do RabbitMQ, ale chcemy przesłać te komunikaty do [Azure Service Bus](./service-bus-messaging-overview.md) do dalszej obróbki, dzięki czemu możemy korzystać z wielu [możliwości usługi Azure Big Data](/azure/architecture/guide/architecture-styles/big-data).
- **Chmura hybrydowa**: Firma właśnie nabyła inną firmę, która używa RabbitMQ do potrzeb obsługi komunikatów. Znajdują się one w innej chmurze. Podczas przejścia na platformę Azure możesz już rozpocząć udostępnianie danych przez mostkowanie RabbitMQ z Azure Service Bus.
- **Integracja z innymi** firmami: inna osoba korzysta z RabbitMQ jako brokera i chce wysłać do nas swoje dane, ale nie są one poza naszą organizacją. Możemy zapewnić im klucz sygnatury dostępu współdzielonego, który umożliwia im dostęp do ograniczonego zestawu Azure Service Busych kolejek, gdzie mogą przekazywać swoje wiadomości do programu.

Lista znajduje się na liście, ale możemy rozwiązać większość tych przypadków użycia, łącząc RabbitMQ z platformą Azure.

Najpierw musisz utworzyć bezpłatne konto platformy Azure, rejestrując się w [tym miejscu](https://azure.microsoft.com/free/)

Po zalogowaniu się do konta przejdź do [Azure Portal](https://portal.azure.com/) i Utwórz nową [przestrzeń nazw](./service-bus-create-namespace-portal.md)Azure Service Bus. Przestrzenie nazw to kontenery określania zakresu, w których będą się znajdować składniki obsługi komunikatów, takie jak kolejki i tematy.

## <a name="adding-a-new-azure-service-bus-namespace"></a>Dodawanie nowej przestrzeni nazw Azure Service Bus

W Azure Portal kliknij przycisk dużego znaku plus, aby dodać nowy zasób

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="Tworzenie zasobu":::

Następnie wybierz pozycję integracja i kliknij pozycję Azure Service Bus, aby utworzyć przestrzeń nazw obsługi komunikatów:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="Wybieranie usługi Azure Service Bus":::

Zostanie wyświetlony monit o wprowadzenie informacji o przestrzeni nazw. Wybierz subskrypcję platformy Azure, której chcesz użyć. Jeśli nie masz [grupy zasobów](../azure-resource-manager/management/manage-resource-groups-portal.md), możesz utworzyć nową.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="Create namespace":::

Używać `rabbitmq` dla `Namespace name` , ale może to być dowolne. Następnie ustaw `East US` dla lokalizacji. Wybierz `Basic` jako warstwę cenową.

Jeśli wszystko poszło dobrze, powinien zostać wyświetlony następujący ekran potwierdzenia:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="Potwierdzenie tworzenia przestrzeni nazw":::

Po powrocie do Azure Portal zobaczysz `rabbitmq` tam nową przestrzeń nazw. Kliknij go, aby uzyskać dostęp do zasobu, aby można było dodać do niego kolejkę.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="Lista zasobów z nową przestrzenią nazw":::

## <a name="creating-our-azure-service-bus-queue"></a>Tworzenie naszej kolejki Azure Service Bus

Teraz, gdy masz Azure Service Bus przestrzeń nazw, kliknij `Queues` przycisk po lewej stronie, `Entities` Aby można było dodać nową kolejkę:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="Utwórz kolejkę":::

Nazwa kolejki będzie `from-rabbitmq` zaraz przypominać o miejscu, z którego pochodzą komunikaty. Wszystkie inne opcje można pozostawić jako domyślne, ale można je zmienić, aby odpowiadały potrzebom aplikacji.

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>Włączanie wtyczki RabbitMQ Shovel

Aby dostarczać komunikaty z usługi RabbitMQ do Azure Service Bus, będziemy używać [wtyczki Shovel](https://www.rabbitmq.com/shovel.html) , która jest spakowana z RabbitMQ. Możesz włączyć wtyczkę i jej interfejs wizualny za pomocą tego polecenia:

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>Może być konieczne uruchomienie tego polecenia jako katalogu głównego.

Teraz możesz uzyskać poświadczenia wymagane do łączenia RabbitMQ z platformą Azure.

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>Łączenie RabbitMQ z Azure Service Bus

Należy utworzyć [zasady dostępu współdzielonego](../storage/common/storage-sas-overview.md) (SAS) dla kolejki, aby RabbitMQ mogły publikować w niej komunikaty. Zasady sygnatury dostępu współdzielonego umożliwiają określenie, które strony zewnętrznej mogą robić z Twoim zasobem. Pomysłem jest to, że RabbitMQ może wysyłać komunikaty, ale nie nasłuchują kolejki ani nie zarządzają nią.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="Dodawanie zasad SAS":::

Zaznacz `Send` pole, a następnie kliknij, `Create` Aby zachować nasze zasady SAS.

Po utworzeniu zasad kliknij je, aby wyświetlić **podstawowe parametry połączenia**. Będziemy korzystać z tej RabbitMQ, aby porozmawiać Azure Service Bus:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="Pobieranie zasad SAS":::

Aby można było używać tych parametrów połączenia, należy przekonwertować go na format połączenia AMQP RabbitMQ. W tym celu przejdź do [Narzędzia konwerter parametrów połączenia](https://red-mushroom-0f7446a0f.azurestaticapps.net/) i wklej parametry połączenia w formularzu, a następnie kliknij przycisk Konwertuj. Otrzymasz parametry połączenia, które RabbitMQ gotowe. (Ta witryna sieci Web uruchamia wszystkie elementy lokalne w przeglądarce, dzięki czemu dane nie są wysyłane przez sieć). Możesz uzyskać dostęp do jego kodu źródłowego w serwisie [GitHub](https://github.com/videlalvaro/connstring_to_amqp).

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="Konwertuj parametry połączenia":::

Teraz otwórz wtyczkę zarządzania RabbitMQ w naszych przeglądarkach `http://localhost:15672/#/dynamic-shovels` i przejdź do `Admin -> Shovel Management` lokalizacji, w której możesz dodać nowe Shovel, które będą wymagać wysłania komunikatów z kolejki RabbitMQ do kolejki Azure Service Bus.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="Dodaj RabbitMQ Shovel":::

W tym miejscu Zadzwoń do Shovel `azure` i wybierz `AMQP 0.9.1` jako protokół źródłowy. Na zrzucie ekranu `amqp://` jest to domyślny identyfikator URI, który łączy nas z lokalnym serwerem RabbitMQ. Upewnij się, że to dostosowanie zostało wprowadzone do bieżącego wdrożenia.

Po stronie kolejki rzeczy można użyć `azure` jako nazwy kolejki. Jeśli ta kolejka nie istnieje, RabbitMQ utworzy ją. Możesz również wybrać nazwę kolejki, która już istnieje. Pozostałe opcje można pozostawić domyślnie.

Następnie po `destination` stronie elementów wybierz opcję `AMQP 1.0` jako protokół. W `URI` polu wprowadź ciąg łączący, który został uzyskany z poprzedniego kroku, przeprowadzono konwersję parametrów połączenia platformy Azure do formatu RabbitMQ. Powinien on wyglądać następująco:

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

W `Address` tym polu wprowadzimy nazwę **kolejki Azure Service Bus**, w tym przypadku została wywołana `from-rabbitmq` . Kliknij przycisk `Add Shovel` , a Twoja konfiguracja powinna być gotowa do rozpoczęcia otrzymywania wiadomości.

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>Publikowanie komunikatów z RabbitMQ do Azure Service Bus

W interfejsie RabbitMQ Management można przejść do obszaru `Queues` , wybrać `azure` kolejkę i wyszukać `Publish message` panel. Zostanie wyświetlony formularz, który umożliwi Publikowanie wiadomości bezpośrednio w kolejce. W naszym przykładzie dopiero zaczynamy dodać `fist message` jako `Payload` i `Publish Message` :

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="Opublikuj pierwszy komunikat":::

Wróć do platformy Azure i sprawdź kolejkę. Kliknij `Service Bus Explorer` w lewym panelu, a następnie kliknij przycisk _wgląd_ . Jeśli wszystko poszło dobrze, zobaczysz, że kolejka zawiera jeden komunikat. Yay, gratulacje!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Kolejka Azure Service Bus":::

Upewnij się, że wiadomość jest taka, którą wysłałeś z RabbitMQ. Wybierz `Peek` kartę i kliknij przycisk, `Peek` Aby pobrać ostatnie wiadomości w kolejce. Kliknij komunikat, aby sprawdzić jego zawartość. Powinieneś wyglądać podobnie jak na poniższej ilustracji `first message` .

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="Wgląd w kolejki":::

## <a name="lets-recap"></a>Podsumowaniemy

Gratulacje! Osiągnięto dużo! Możesz zarządzać komunikatami od RabbitMQ do Azure Service Bus, podsumowaniemy kroki:

1. Tworzenie przestrzeni nazw Azure Service Bus
2. Dodawanie kolejki do przestrzeni nazw
3. Dodawanie zasad sygnatury dostępu współdzielonego do kolejki
4. Pobierz parametry połączenia kolejki
5. Włączanie wtyczki RabbitMQ Shovel & interfejsie zarządzania
6. Konwertuj Azure Service Bus parametry połączenia na format AMQP RabbitMQ
7. Dodaj nową Shovel do RabbitMQ & połączyć ją z Azure Service Bus
8. Publikowanie komunikatów

Zgodnie z poprzednimi krokami zintegrowane obszary organizacji, które znajdowały się poza platformą Azure. Wtyczka Shovel zezwala na wysyłanie komunikatów z usługi RabbitMQ do Azure Service Bus. Ma to olbrzymią zaletę, ponieważ teraz można umożliwić zaufanym podmiotom trzecim łączenie ich aplikacji z wdrożeniem platformy Azure.

Na końcu wiadomości dotyczące włączania połączeń i z tą techniką właśnie otwieramy nową.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Azure Service Bus](./service-bus-messaging-overview.md)
- Dowiedz się więcej o [obsłudze AMQP 1,0 w Service Bus](./service-bus-amqp-overview.md)
