---
title: Zarządzanie punktami końcowymi i trasami (interfejsy API i interfejs wiersza polecenia)
titleSuffix: Azure Digital Twins
description: Zobacz, jak skonfigurować punkty końcowe i trasy zdarzeń dla danych Digital bliźniaczych reprezentacji systemu Azure oraz zarządzać nimi.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c7efebaf23bd8e897243f6ee12b23d3821a4c033
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257334"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Zarządzanie punktami końcowymi i trasami w usłudze Azure Digital bliźniaczych reprezentacji (interfejsy API i interfejs wiersza polecenia)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

W usłudze Azure Digital bliźniaczych reprezentacji można kierować [powiadomienia o zdarzeniach](concepts-event-notifications.md) do usług podrzędnych lub podłączonych zasobów obliczeniowych. W tym celu należy najpierw skonfigurować **punkty końcowe**, które mogą odbierać zdarzenia. Następnie można utworzyć  [**trasy zdarzeń**](concepts-route-events.md) , które określają, które zdarzenia generowane przez usługę Azure Digital bliźniaczych reprezentacji są dostarczane do których punktów końcowych.

W tym artykule omówiono proces tworzenia punktów końcowych i tras za pomocą [interfejsów API REST](/rest/api/azure-digitaltwins/), [zestawu SDK platformy .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client)i [interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md).

Alternatywnie można także zarządzać punktami końcowymi i trasami przy użyciu [Azure Portal](https://portal.azure.com). Aby uzyskać wersję tego artykułu, która używa portalu, zobacz [*How to: Manage Endpoints and Routes (Portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć **konto platformy Azure** (możesz skonfigurować je bezpłatnie w [tym miejscu](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- W subskrypcji platformy Azure będzie potrzebne **wystąpienie usługi Azure Digital bliźniaczych reprezentacji** . Jeśli nie masz już wystąpienia, możesz je utworzyć, wykonując kroki opisane w temacie [*jak to zrobić: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-cli.md). Skorzystaj z następujących wartości z Instalatora, które są przydatne w dalszej części tego artykułu:
    - Nazwa wystąpienia
    - Grupa zasobów

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Tworzenie punktu końcowego dla usługi Azure Digital bliźniaczych reprezentacji

Są to obsługiwane typy punktów końcowych, które można utworzyć dla danego wystąpienia:
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Aby uzyskać więcej informacji na temat różnych typów punktów końcowych, zobacz [*Wybieranie między usługami Azure Messaging*](../event-grid/compare-messaging-services.md).

W tej sekcji opisano sposób tworzenia tych punktów końcowych przy użyciu interfejsu wiersza polecenia platformy Azure. Punktami końcowymi można także zarządzać za pomocą [interfejsów API płaszczyzny kontroli DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Tworzenie punktu końcowego

Po utworzeniu zasobów punktu końcowego można używać ich na potrzeby punktu końcowego usługi Azure Digital bliźniaczych reprezentacji. W poniższych przykładach pokazano, jak utworzyć punkty końcowe za pomocą polecenia [AZ DT Endpoint Create](/cli/azure/dt/endpoint/create) dla [interfejsu CLI usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md). Zastąp symbole zastępcze w poleceniach szczegółowymi informacjami dotyczącymi własnych zasobów.

Aby utworzyć punkt końcowy Event Grid:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Aby utworzyć punkt końcowy Event Hubs (uwierzytelnianie oparte na kluczach):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Aby utworzyć punkt końcowy tematu Service Bus (uwierzytelnianie oparte na kluczach):
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Po pomyślnym uruchomieniu tych poleceń, Siatka zdarzeń, centrum zdarzeń lub Service Bus będzie dostępny jako punkt końcowy w ramach bliźniaczych reprezentacji cyfrowych platformy Azure, pod nazwą podaną z `--endpoint-name` argumentem. Ta nazwa będzie używana zazwyczaj jako obiekt docelowy **trasy zdarzenia**, którą utworzysz [w dalszej części tego artykułu](#create-an-event-route).

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Tworzenie punktu końcowego z uwierzytelnianiem na podstawie tożsamości

Możesz również utworzyć punkt końcowy z uwierzytelnianiem na podstawie tożsamości, aby użyć punktu końcowego z [tożsamością zarządzaną](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Ta opcja jest dostępna tylko dla punktów końcowych centrum zdarzeń i typu Service Bus (nie jest to obsługiwane w przypadku Event Grid).

Polecenie interfejsu wiersza polecenia do utworzenia tego typu punktu końcowego jest poniżej. Aby podłączyć symbole zastępcze w poleceniu, potrzebne są następujące wartości:
* Identyfikator zasobu platformy Azure wystąpienia usługi Azure Digital bliźniaczych reprezentacji
* Nazwa punktu końcowego
* Typ punktu końcowego
* Przestrzeń nazw zasobu punktu końcowego
* Nazwa centrum zdarzeń lub tematu Service Bus
* Lokalizacja wystąpienia usługi Azure Digital bliźniaczych reprezentacji

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Tworzenie punktu końcowego z utraconymi wiadomościami

Gdy punkt końcowy nie może dostarczyć zdarzenia w określonym czasie lub po próbie dostarczenia zdarzenia przez określoną liczbę razy, może wysłać niedostarczone zdarzenie do konta magazynu. Ten proces jest znany jako **utracony**.

Punkty końcowe z włączoną obsługą utraconych wiadomości można skonfigurować przy użyciu interfejsu [wiersza polecenia](how-to-use-cli.md) lub [interfejsu API](how-to-use-apis-sdks.md#overview-control-plane-apis)Digital bliźniaczych reprezentacji platformy Azure.

Aby dowiedzieć się więcej o utraconych wiadomościach, zobacz [*pojęcia: trasy zdarzeń*](concepts-route-events.md#dead-letter-events). Aby uzyskać instrukcje dotyczące sposobu konfigurowania punktu końcowego z utraconymi wiadomościami, przejdź do dalszej części tej sekcji.

#### <a name="set-up-storage-resources"></a>Konfigurowanie zasobów magazynu

Przed ustawieniem lokalizacji utraconych wiadomości musisz mieć [konto magazynu](../storage/common/storage-account-create.md?tabs=azure-portal) z [kontenerem](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) skonfigurowanym na koncie platformy Azure. 

Podaj identyfikator URI dla tego kontenera podczas późniejszego tworzenia punktu końcowego. Lokalizacja utraconych wiadomości zostanie dostarczona do punktu końcowego jako identyfikator URI kontenera z [tokenem sygnatury dostępu współdzielonego](../storage/common/storage-sas-overview.md). Ten token wymaga `write` uprawnień do kontenera docelowego na koncie magazynu. W pełni sformułowany **Identyfikator URI sygnatury dostępu współdzielonego z utraconymi wiadomościami** będzie miał format: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>` .

Wykonaj poniższe kroki, aby skonfigurować te zasoby magazynu na koncie platformy Azure, aby przygotować się do skonfigurowania połączenia punktu końcowego w następnej sekcji.

1. Postępuj zgodnie z instrukcjami w temacie [*Tworzenie konta magazynu*](../storage/common/storage-account-create.md?tabs=azure-portal) , aby utworzyć **konto magazynu** w ramach subskrypcji platformy Azure. Zanotuj nazwę konta magazynu, aby użyć jej później.
2. Wykonaj kroki opisane w temacie [*Tworzenie kontenera*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) , aby utworzyć **kontener** w ramach nowego konta magazynu. Zanotuj nazwę kontenera, aby użyć go później.
3. Następnie Utwórz **token sygnatury dostępu współdzielonego** dla konta magazynu, którego punkt końcowy może użyć, aby uzyskać do niego dostęp. Zacznij od przechodzenia do konta magazynu w [Azure Portal](https://ms.portal.azure.com/#home) (można go znaleźć za pomocą nazwy na pasku wyszukiwania portalu).
4. Na stronie konto magazynu wybierz łącze _sygnatura dostępu współdzielonego_ na lewym pasku nawigacyjnym, aby rozpocząć konfigurowanie tokenu SAS.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Strona konta magazynu w Azure Portal" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. Na *stronie sygnatura dostępu współdzielonego* w obszarze *dozwolone usługi* i *dozwolone typy zasobów* Wybierz dowolne ustawienia, które chcesz. Musisz wybrać co najmniej jedno pole w każdej kategorii. W obszarze *dozwolone uprawnienia* wybierz pozycję **Zapisz** (Jeśli chcesz, możesz również wybrać inne uprawnienia).
1. Ustaw dowolne wartości dla pozostałych ustawień.
1. Po zakończeniu wybierz przycisk _Generuj sygnaturę dostępu współdzielonego i parametry połączenia_ , aby wygenerować token sygnatury dostępu współdzielonego. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Strona konta magazynu w Azure Portal pokazywania wszystkich opcji wyboru w celu wygenerowania tokenu sygnatury dostępu współdzielonego oraz wyróżniania przycisku &quot;Generuj sygnaturę dostępu współdzielonego i parametrów połączenia&quot;" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. Spowoduje to wygenerowanie kilku wartości sygnatury dostępu współdzielonego i parametrów połączenia w dolnej części tej samej strony, poniżej ustawienia wyboru. Przewiń w dół, aby wyświetlić wartości, a następnie skopiuj wartość **tokena SAS** za pomocą ikony *Kopiuj do schowka* . Zapisz go do późniejszego użycia.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="Skopiuj token sygnatury dostępu współdzielonego, aby użyć go w kluczu tajnym utraconych wiadomości." lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="create-the-dead-letter-endpoint"></a>Tworzenie punktu końcowego utraconych wiadomości

Aby utworzyć punkt końcowy, w którym włączono obsługę utraconych wiadomości, Dodaj następujący parametr utraconych wiadomości do polecenia [AZ DT Endpoint Create](/cli/azure/dt/endpoint/create) dla [interfejsu CLI usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md).

Wartość parametru to **utracony identyfikator URI sygnatury dostępu współdzielonego** , składający się z nazwy konta magazynu, nazwy kontenera i tokenu sygnatury dostępu współdzielonego zebranego w [poprzedniej sekcji](#set-up-storage-resources). Ten parametr umożliwia utworzenie punktu końcowego przy użyciu uwierzytelniania opartego na kluczach.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

Dodaj ten parametr na końcu poleceń tworzenia punktu końcowego ze strony [*Utwórz punkt końcowy*](#create-the-endpoint) , aby utworzyć punkt końcowy żądanego typu z włączonymi utraconymi komunikatami.

Alternatywnie możesz tworzyć utracone punkty końcowe na [platformie Azure](how-to-use-apis-sdks.md#overview-control-plane-apis) , a nie interfejs wiersza polecenia. Aby to zrobić, zapoznaj się z [dokumentacją DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) , aby zobaczyć, jak określić strukturę żądania i dodać parametry utraconych wiadomości.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Tworzenie punktu końcowego utraconych wiadomości za pomocą uwierzytelniania opartego na tożsamościach

Możesz również utworzyć punkt końcowy utraconych wiadomości, który ma uwierzytelnianie oparte na tożsamościach, aby użyć punktu końcowego z [tożsamością zarządzaną](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Ta opcja jest dostępna tylko dla punktów końcowych centrum zdarzeń i typu Service Bus (nie jest to obsługiwane w przypadku Event Grid).

Aby utworzyć ten typ punktu końcowego, użyj tego samego polecenia CLI ze starszej wersji, aby [utworzyć punkt końcowy z uwierzytelnianiem na podstawie tożsamości](#create-an-endpoint-with-identity-based-authentication), z dodatkowym polem w ładunku JSON dla `deadLetterUri` .

Poniżej znajdują się wartości, które należy podłączyć do symboli zastępczych w poleceniu:
* Identyfikator zasobu platformy Azure wystąpienia usługi Azure Digital bliźniaczych reprezentacji
* Nazwa punktu końcowego
* Typ punktu końcowego
* Przestrzeń nazw zasobu punktu końcowego
* Nazwa centrum zdarzeń lub tematu Service Bus
* Szczegóły **utraconego identyfikatora URI sygnatury dostępu współdzielonego** : nazwa konta magazynu, nazwa kontenera
* Lokalizacja wystąpienia usługi Azure Digital bliźniaczych reprezentacji

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>Schemat magazynu komunikatów

Po skonfigurowaniu punktu końcowego z utraconymi komunikatami wiadomości utracone będą przechowywane w następującym formacie na koncie magazynu:

`{container}/{endpoint-name}/{year}/{month}/{day}/{hour}/{event-ID}.json`

Wiadomości utracone będą zgodne ze schematem oryginalnego zdarzenia, które było przeznaczone do dostarczenia do oryginalnego punktu końcowego.

Poniżej znajduje się przykład wiadomości utraconej dla [powiadomienia o tworzeniu sznurka](concepts-event-notifications.md#digital-twin-lifecycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Tworzenie trasy zdarzeń

**Wymaganie wstępne**: należy utworzyć punkty końcowe zgodnie z wcześniejszym opisem w tym artykule przed rozpoczęciem tworzenia trasy. Po zakończeniu konfigurowania punktów końcowych można utworzyć trasę zdarzenia.

> [!NOTE]
> Jeśli ostatnio wdrożono punkty końcowe, sprawdź, czy są one gotowe do wdrożenia, **przed** podjęciem próby użycia ich dla nowej trasy zdarzeń. Jeśli wdrożenie trasy nie powiedzie się, ponieważ punkty końcowe nie są gotowe, odczekaj kilka minut i spróbuj ponownie.
>
> W przypadku wykonywania skryptów w tym przepływie warto uwzględnić to przez utworzenie w ciągu 2-3 minut czasu oczekiwania na zakończenie wdrażania usługi punktu końcowego przed przejściem do konfiguracji trasy.

Aby faktycznie wysyłać dane z usługi Azure Digital bliźniaczych reprezentacji do punktu końcowego, należy zdefiniować **trasę zdarzeń**. Trasy zdarzeń służą do nawiązywania połączenia z przepływem zdarzeń w całym systemie i usług podrzędnych.

Definicja trasy może zawierać następujące elementy:
* Nazwa trasy, która ma być używana
* Nazwa punktu końcowego, który ma być używany
* Filtr określający, które zdarzenia są wysyłane do punktu końcowego. 

Jeśli nie ma nazwy trasy, żadne komunikaty nie są kierowane poza usługę Azure Digital bliźniaczych reprezentacji. Jeśli istnieje Nazwa trasy i filtr jest `true` , wszystkie komunikaty są kierowane do punktu końcowego. Jeśli istnieje Nazwa trasy i zostanie dodany inny filtr, komunikaty będą filtrowane zgodnie z filtrem.

Jedna trasa powinna zezwalać na wybranie wielu powiadomień i typów zdarzeń. 

Trasy zdarzeń można tworzyć za pomocą [  interfejsów API płaszczyzny danych](/rest/api/digital-twins/dataplane/eventroutes) Digital bliźniaczych reprezentacji EventRoutes lub [ **AZ DT Route** interfejsu wiersza polecenia](/cli/azure/dt/route). Pozostała część tej sekcji przeprowadzi proces tworzenia.

### <a name="create-routes-with-the-apis-and-c-sdk"></a>Tworzenie tras za pomocą interfejsów API i zestawu C# SDK

Jednym ze sposobów definiowania tras zdarzeń jest współdziałanie z [interfejsami API płaszczyzny danych](how-to-use-apis-sdks.md#overview-data-plane-apis). W przykładach w tej sekcji użyto [zestawu SDK platformy .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client).

`CreateOrReplaceEventRouteAsync` to wywołanie zestawu SDK, które służy do dodawania trasy zdarzenia. Oto przykład użycia:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Wszystkie funkcje zestawu SDK są w wersji synchronicznej i asynchronicznej.

#### <a name="event-route-sample-sdk-code"></a>Przykładowy kod SDK dla trasy zdarzeń

Poniższa metoda Przykładowa przedstawia sposób tworzenia, wyświetlania i usuwania trasy zdarzeń przy użyciu zestawu C# SDK:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>Tworzenie tras za pomocą interfejsu wiersza polecenia

Marszruty mogą być również zarządzane przy użyciu poleceń [AZ DT Route](/cli/azure/dt/route) dla interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji. 

Aby uzyskać więcej informacji o korzystaniu z interfejsu wiersza polecenia i dostępnych poleceń, zobacz [*How to: Use the Azure Digital bliźniaczych reprezentacji CLI*](how-to-use-cli.md).

## <a name="filter-events"></a>Filtrowanie zdarzeń

Bez filtrowania punkty końcowe otrzymują wiele zdarzeń z usługi Azure Digital bliźniaczych reprezentacji:
* Dane telemetryczne wywoływane przez [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) przy użyciu interfejsu API usługi Digital bliźniaczych reprezentacji platformy Azure
* Powiadomienia o zmianie właściwości przędzy, generowane dla zmian właściwości dla dowolnej przędzy w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji
* Zdarzenia cyklu życia wyzwalane podczas tworzenia lub usuwania bliźniaczych reprezentacji lub relacji

Można ograniczyć wysyłane zdarzenia, dodając **Filtr** dla punktu końcowego do trasy zdarzenia.

>[!NOTE]
> W filtrach jest **rozróżniana wielkość** liter i musi być zgodna z wielkością ładunku. 
>
> W przypadku filtrów telemetrii oznacza to, że wielkość liter musi być zgodna z wielkością liter w telemetrii wysyłanej przez urządzenie, a nie do wielkości liter zdefiniowanych w modelu sznurka. 

Aby dodać filtr, można użyć żądania PUT do *protokołu https://{The-Azure-Digital-bliźniaczych reprezentacji-hostname}/eventRoutes/{Event-Route-Name}? API-Version = 2020-10-31* z następującą treścią:

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

Poniżej przedstawiono obsługiwane filtry tras. Użyj szczegółów w kolumnie *Filtruj schemat tekstu* , aby zastąpić `<filter-text>` symbol zastępczy w powyższej treści żądania.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Następne kroki

Przeczytaj o różnych typach komunikatów zdarzeń, które można odbierać:
* [*Koncepcje: powiadomienia o zdarzeniach*](concepts-event-notifications.md)
