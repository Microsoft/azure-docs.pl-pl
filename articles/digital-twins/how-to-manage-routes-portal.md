---
title: Zarządzanie punktami końcowymi i trasami (Portal)
titleSuffix: Azure Digital Twins
description: Zobacz, jak skonfigurować punkty końcowe i trasy zdarzeń dla danych Digital bliźniaczych reprezentacji systemu Azure przy użyciu Azure Portal.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 21188f473cbd5a6fd2a1ee549f47ad9b0e5b8af3
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279483"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Zarządzanie punktami końcowymi i trasami w usłudze Azure Digital bliźniaczych reprezentacji (Portal)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

W usłudze Azure Digital bliźniaczych reprezentacji można kierować [powiadomienia o zdarzeniach](how-to-interpret-event-data.md) do usług podrzędnych lub podłączonych zasobów obliczeniowych. W tym celu należy najpierw skonfigurować **punkty końcowe** , które mogą odbierać zdarzenia. Następnie można utworzyć [**trasy zdarzeń**](concepts-route-events.md) , które określają, które zdarzenia generowane przez usługę Azure Digital bliźniaczych reprezentacji są dostarczane do których punktów końcowych.

Ten artykuł przeprowadzi Cię przez proces tworzenia punktów końcowych i tras przy użyciu [Azure Portal](https://portal.azure.com).

Punkty końcowe i trasy można także zarządzać za pomocą [interfejsów API tras](/rest/api/digital-twins/dataplane/eventroutes), [zestawu .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)lub [interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md). Aby uzyskać wersję tego artykułu, która używa tych mechanizmów zamiast portalu, zobacz [*How to: Manage Endpoints and Routes (interfejsy API i CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć **konto platformy Azure** (możesz skonfigurować je bezpłatnie w [tym miejscu](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* W subskrypcji platformy Azure będzie potrzebne **wystąpienie usługi Azure Digital bliźniaczych reprezentacji** . Jeśli nie masz już wystąpienia, możesz je utworzyć, wykonując kroki opisane w temacie [*jak to zrobić: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-portal.md). Skorzystaj z następujących wartości z Instalatora, które są przydatne w dalszej części tego artykułu:
    - Nazwa wystąpienia
    - Grupa zasobów

Te szczegóły można znaleźć w [Azure Portal](https://portal.azure.com) po skonfigurowaniu wystąpienia. Zaloguj się do portalu i wyszukaj nazwę wystąpienia na pasku wyszukiwania portalu.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Zrzut ekranu przedstawiający pasek wyszukiwania Azure Portal.":::

Wybierz wystąpienie z wyników, aby wyświetlić stronę szczegółów wystąpienia:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Zrzut ekranu przedstawiający szczegóły wystąpienia ADT." border="false":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Tworzenie punktu końcowego dla usługi Azure Digital bliźniaczych reprezentacji

Są to obsługiwane typy punktów końcowych, które można utworzyć dla danego wystąpienia:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Aby uzyskać więcej informacji na temat różnych typów punktów końcowych, zobacz [*Wybieranie między usługami Azure Messaging*](../event-grid/compare-messaging-services.md).

Aby można było połączyć punkt końcowy z usługą Azure Digital bliźniaczych reprezentacji, należy już użyć tematu usługi Event Grid, centrum zdarzeń lub Service Bus, które są używane dla punktu końcowego. 

### <a name="create-an-event-grid-endpoint"></a>Tworzenie punktu końcowego Event Grid

**Warunek wstępny** : Tworzenie tematu siatki zdarzeń, wykonując czynności opisane w [sekcji *Tworzenie niestandardowego tematu*](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) Event Grid *niestandardowych zdarzeń* przewodnika Szybki Start.

Po utworzeniu tematu można połączyć go z usługą Azure Digital bliźniaczych reprezentacji na stronie swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji w [Azure Portal](https://portal.azure.com) (można znaleźć wystąpienie, wprowadzając jego nazwę na pasku wyszukiwania portalu).

Z menu wystąpienie wybierz pozycję _punkty końcowe_. Następnie na stronie *punkty końcowe* wybierz pozycję *+ Utwórz punkt końcowy*. 

Na stronie *Tworzenie punktu końcowego* , który zostanie otwarty, można utworzyć punkt końcowy typu _Event Grid_ , wybierając odpowiedni przycisk radiowy. Uzupełnij inne szczegóły: Wprowadź nazwę punktu końcowego w polu _Nazwa_ , wybierz swoją _subskrypcję_ z listy rozwijanej i wybierz wstępnie utworzony  _temat Event Grid_ z trzeciej listy rozwijanej.

Następnie Utwórz punkt końcowy, naciskając pozycję _Zapisz_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Zrzut ekranu przedstawiający tworzenie punktu końcowego typu Event Grid.":::

Możesz sprawdzić, czy punkt końcowy został pomyślnie utworzony, sprawdzając ikonę powiadomienia na górnym pasku Azure Portal: 

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Zrzut ekranu przedstawiający powiadomienie w celu zweryfikowania utworzenia punktu końcowego." border="false":::

Możesz również wyświetlić punkt końcowy, który został utworzony ponownie na stronie *punkty końcowe* wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Jeśli Tworzenie punktu końcowego nie powiedzie się, obserwuj komunikat o błędzie i spróbuj ponownie za kilka minut.

Teraz temat usługi Event Grid jest dostępny jako punkt końcowy w programie Azure Digital bliźniaczych reprezentacji, pod nazwą określoną w polu _Nazwa_ . Ta nazwa będzie używana zazwyczaj jako obiekt docelowy **trasy zdarzenia** , którą utworzysz [w dalszej części tego artykułu](#create-an-event-route).

### <a name="create-an-event-hubs-endpoint"></a>Tworzenie punktu końcowego Event Hubs

**Wymagania wstępne** : 
* Potrzebujesz _Event Hubs przestrzeni nazw_ i _centrum zdarzeń_. Aby wykonać te czynności, wykonaj kroki opisane w Event Hubs [*utworzyć centrum zdarzeń*](../event-hubs/event-hubs-create.md) — Szybki Start.
* Wymagana jest _reguła autoryzacji_. Aby to zrobić, zapoznaj się z artykułem Event Hubs [*Autoryzuj dostęp do zasobów Event Hubs za pomocą sygnatury dostępu współdzielonego*](../event-hubs/authorize-access-shared-access-signature.md) .

Przejdź do strony szczegółów wystąpienia usługi Azure Digital bliźniaczych reprezentacji w [Azure Portal](https://portal.azure.com) (można go znaleźć, wprowadzając nazwę na pasku wyszukiwania portalu).

Z menu wystąpienie wybierz pozycję _punkty końcowe_. Następnie na stronie *punkty końcowe* wybierz pozycję *+ Utwórz punkt końcowy*. 

Na stronie *Tworzenie punktu końcowego* , który zostanie otwarty, można utworzyć punkt końcowy typu _centrum zdarzeń_ , wybierając odpowiedni przycisk radiowy. Wprowadź nazwę punktu końcowego w polu _Nazwa_ . Następnie wybierz swoją _subskrypcję_ i wstępnie utworzoną _przestrzeń nazw centrum zdarzeń_ , _centrum zdarzeń_ i _regułę autoryzacji_ z odpowiednich list rozwijanych.

Następnie Utwórz punkt końcowy, naciskając pozycję _Zapisz_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub.png" alt-text="Zrzut ekranu przedstawiający tworzenie punktu końcowego typu Event Hubs.":::

Możesz sprawdzić, czy punkt końcowy został pomyślnie utworzony, sprawdzając ikonę powiadomienia na górnym pasku Azure Portal. 

Jeśli Tworzenie punktu końcowego nie powiedzie się, obserwuj komunikat o błędzie i spróbuj ponownie za kilka minut.

Teraz centrum zdarzeń jest dostępne jako punkt końcowy wewnątrz Digital bliźniaczych reprezentacji systemu Azure, pod nazwą określoną w polu _Nazwa_ . Ta nazwa będzie używana zazwyczaj jako obiekt docelowy **trasy zdarzenia** , którą utworzysz [w dalszej części tego artykułu](#create-an-event-route).

### <a name="create-a-service-bus-endpoint"></a>Tworzenie punktu końcowego Service Bus

**Wymagania wstępne** : 
* Potrzebujesz _Service Bus przestrzeni nazw_ i _tematu Service Bus_. Utwórz oba te elementy, wykonując kroki opisane w temacie Service Bus [*Tworzenie tematów i subskrypcji*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) przewodnika Szybki Start. Nie musisz kończyć sekcji [*Tworzenie subskrypcji w temacie*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md#create-subscriptions-to-the-topic) .
* Wymagana jest _reguła autoryzacji_. Aby go utworzyć, zapoznaj się z artykułem [*uwierzytelnianie i autoryzacja*](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) Service Bus.

Przejdź do strony szczegółów wystąpienia usługi Azure Digital bliźniaczych reprezentacji w [Azure Portal](https://portal.azure.com) (można go znaleźć, wprowadzając nazwę na pasku wyszukiwania portalu).

Z menu wystąpienie wybierz pozycję _punkty końcowe_. Następnie na stronie *punkty końcowe* wybierz pozycję *+ Utwórz punkt końcowy*. 

Na stronie *Tworzenie punktu końcowego* , który zostanie otwarty, można utworzyć punkt końcowy typu _Service Bus_ , wybierając odpowiedni przycisk radiowy. Wprowadź nazwę punktu końcowego w polu _Nazwa_ . Następnie wybierz swoją _subskrypcję_ i wstępnie utworzoną _przestrzeń nazw Service Bus_ , _temat Service Bus_ i _regułę autoryzacji_ z odpowiednich list rozwijanych.

Następnie Utwórz punkt końcowy, naciskając pozycję _Zapisz_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-service-bus.png" alt-text="Zrzut ekranu przedstawiający tworzenie punktu końcowego typu Service Bus.":::

Możesz sprawdzić, czy punkt końcowy został pomyślnie utworzony, sprawdzając ikonę powiadomienia na górnym pasku Azure Portal. 

Jeśli Tworzenie punktu końcowego nie powiedzie się, obserwuj komunikat o błędzie i spróbuj ponownie za kilka minut.

Teraz temat Service Bus jest dostępny jako punkt końcowy wewnątrz Digital bliźniaczych reprezentacji systemu Azure, pod nazwą określoną w polu _Nazwa_ . Ta nazwa będzie używana zazwyczaj jako obiekt docelowy **trasy zdarzenia** , którą utworzysz [w dalszej części tego artykułu](#create-an-event-route).

### <a name="create-an-endpoint-with-dead-lettering"></a>Tworzenie punktu końcowego z utraconymi wiadomościami

Gdy punkt końcowy nie może dostarczyć zdarzenia w określonym czasie lub po próbie dostarczenia zdarzenia przez określoną liczbę razy, może wysłać niedostarczone zdarzenie do konta magazynu. Ten proces jest znany jako **utracony**.

Aby można było utworzyć punkt końcowy z włączoną obsługą utraconych wiadomości, należy użyć [interfejsów API ARM](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) do utworzenia punktu końcowego, a nie Azure Portal.

Aby uzyskać instrukcje, jak to zrobić za pomocą interfejsów API, zobacz [*interfejsy API i wersja interfejsu wiersza polecenia*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) tego artykułu.

## <a name="create-an-event-route"></a>Tworzenie trasy zdarzeń

Aby faktycznie wysyłać dane z usługi Azure Digital bliźniaczych reprezentacji do punktu końcowego, należy zdefiniować **trasę zdarzeń**. Te trasy umożliwiają deweloperom tworzenie przepływów zdarzeń w całym systemie i w ramach usług podrzędnych. Przeczytaj więcej na temat tras zdarzeń w temacie [*pojęcia: Routing Digital bliźniaczych reprezentacji Events*](concepts-route-events.md).

**Wymaganie wstępne** : należy utworzyć punkty końcowe zgodnie z wcześniejszym opisem w tym artykule przed rozpoczęciem tworzenia trasy. Po zakończeniu konfigurowania punktów końcowych można utworzyć trasę zdarzenia.

>[!NOTE]
>Jeśli ostatnio wdrożono punkty końcowe, sprawdź, czy są one gotowe do wdrożenia, **przed** podjęciem próby użycia ich dla nowej trasy zdarzeń. Jeśli nie możesz skonfigurować trasy, ponieważ punkty końcowe nie są gotowe, odczekaj kilka minut i spróbuj ponownie.

### <a name="creation-steps-with-the-azure-portal"></a>Kroki tworzenia z Azure Portal

Definicja trasy zdarzeń zawiera następujące elementy:
* Nazwa trasy, która ma być używana
* Nazwa punktu końcowego, który ma być używany
* Filtr określający, które zdarzenia są wysyłane do punktu końcowego.
    - Aby wyłączyć trasę, aby nie były wysyłane żadne zdarzenia, użyj wartości filtru `false`
    - Aby włączyć trasę, która nie ma określonego filtrowania, użyj wartości filtru `true`
    - Aby uzyskać szczegółowe informacje na temat dowolnego innego typu filtru, zobacz sekcję [*filtrowanie zdarzeń*](#filter-events) poniżej.

Pojedyncza trasa może zezwalać na wybranie wielu powiadomień i typów zdarzeń.

Aby utworzyć trasę zdarzeń, przejdź do strony szczegółów wystąpienia usługi Azure Digital bliźniaczych reprezentacji w [Azure Portal](https://portal.azure.com) (można znaleźć wystąpienie, wprowadzając jego nazwę na pasku wyszukiwania portalu).

Z menu wystąpienie wybierz pozycję _trasy zdarzeń_. Następnie na poniższej stronie *trasy zdarzeń* wybierz pozycję *+ Utwórz trasę zdarzenia*. 

Na stronie *Tworzenie trasy zdarzenia* , która zostanie otwarta, wybierz co najmniej:
* Nazwa trasy w polu _Nazwa_
* _Punkt końcowy_ , którego chcesz użyć do utworzenia trasy 

Aby można było włączyć trasę, należy również **dodać filtr trasy zdarzeń** o wartości co najmniej `true` . (Pozostawienie wartości domyślnej `false` spowoduje utworzenie trasy, ale żadne zdarzenia nie będą wysyłane do niego). W tym celu Przełącz przełącznik dla _edytora zaawansowanego_ , aby go włączyć, i wpisz `true` w polu *filtru* .

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Zrzut ekranu przedstawiający tworzenie trasy zdarzeń dla danego wystąpienia." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

Po zakończeniu kliknij przycisk _Zapisz_ , aby utworzyć trasę zdarzenia.

## <a name="filter-events"></a>Filtrowanie zdarzeń

Jak opisano powyżej, trasy mają pole **filtru** . Jeśli wartość filtru w marszrucie to `false` ., żadne zdarzenia nie będą wysyłane do punktu końcowego. 

Po włączeniu minimalnego filtru `true` , punkty końcowe będą otrzymywać różne zdarzenia z usługi Azure Digital bliźniaczych reprezentacji:
* Dane telemetryczne wywoływane przez [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) przy użyciu interfejsu API usługi Digital bliźniaczych reprezentacji platformy Azure
* Powiadomienia o zmianie właściwości przędzy, generowane dla zmian właściwości dla dowolnej przędzy w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji
* Zdarzenia cyklu życiowego, wywoływane podczas tworzenia lub usuwania bliźniaczych reprezentacji lub relacji

Można ograniczyć typy wysyłanych zdarzeń, definiując filtr bardziej specyficzny.

Aby dodać filtr zdarzeń podczas tworzenia trasy zdarzeń, użyj sekcji _Dodawanie filtru trasy zdarzeń_ na stronie *Tworzenie trasy zdarzeń* . 

Możesz wybrać jedną z podstawowych opcji standardowych filtrów lub użyć zaawansowanych opcji filtru, aby napisać własne filtry niestandardowe.

#### <a name="use-the-basic-filters"></a>Korzystanie z filtrów podstawowych

Aby użyć filtrów podstawowych, rozwiń opcję _typy zdarzeń_ i zaznacz pola wyboru odpowiadające zdarzeniom, które chcesz wysłać do punktu końcowego. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Zrzut ekranu przedstawiający tworzenie trasy zdarzeń z filtrem podstawowym. Zaznaczanie pól wyboru zdarzeń.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Spowoduje to wypełnienie pola tekstowego filtru tekstem, który został wybrany:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Zrzut ekranu przedstawiający tworzenie trasy zdarzeń z filtrem podstawowym. Wyświetlanie automatycznie wypełnionego tekstu filtru po zaznaczeniu zdarzeń.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Korzystanie z filtrów zaawansowanych

Alternatywnie możesz użyć opcji filtru zaawansowanego, aby napisać własne filtry niestandardowe.

Aby utworzyć trasę zdarzeń z zaawansowanymi opcjami filtru, Przełącz przełącznik dla _edytora zaawansowanego_ , aby go włączyć. W polu *filtru* można następnie napisać własne filtry zdarzeń:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Zrzut ekranu przedstawiający tworzenie trasy zdarzeń z filtrem zaawansowanym.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Poniżej przedstawiono obsługiwane filtry tras. Szczegóły w kolumnie *Filtruj schemat tekstu* to tekst, który można wpisać w polu Filtr.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Następne kroki

Przeczytaj o różnych typach komunikatów zdarzeń, które można odbierać:
* [*Instrukcje: interpretowanie danych zdarzenia*](how-to-interpret-event-data.md)
