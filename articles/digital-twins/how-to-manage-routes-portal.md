---
title: Zarządzanie punktami końcowymi i trasami (Portal)
titleSuffix: Azure Digital Twins
description: Zobacz, jak skonfigurować punkty końcowe i trasy zdarzeń dla danych Digital bliźniaczych reprezentacji systemu Azure przy użyciu Azure Portal.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8a8cc6563d8e3cae942c4dfb43afa3d05f6f2b75
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257311"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Zarządzanie punktami końcowymi i trasami w usłudze Azure Digital bliźniaczych reprezentacji (Portal)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

W usłudze Azure Digital bliźniaczych reprezentacji można kierować [powiadomienia o zdarzeniach](concepts-event-notifications.md) do usług podrzędnych lub podłączonych zasobów obliczeniowych. W tym celu należy najpierw skonfigurować **punkty końcowe**, które mogą odbierać zdarzenia. Następnie można utworzyć [**trasy zdarzeń**](concepts-route-events.md) , które określają, które zdarzenia generowane przez usługę Azure Digital bliźniaczych reprezentacji są dostarczane do których punktów końcowych.

Ten artykuł przeprowadzi Cię przez proces tworzenia punktów końcowych i tras przy użyciu [Azure Portal](https://portal.azure.com).

Alternatywnie można także zarządzać punktami końcowymi i trasami przy użyciu [interfejsów API tras](/rest/api/digital-twins/dataplane/eventroutes), [zestawów SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)lub [interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md). Aby uzyskać wersję tego artykułu, która używa tych mechanizmów zamiast portalu, zobacz [*How to: Manage Endpoints and Routes (interfejsy API i CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć **konto platformy Azure** (możesz skonfigurować je bezpłatnie w [tym miejscu](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* W subskrypcji platformy Azure będzie potrzebne **wystąpienie usługi Azure Digital bliźniaczych reprezentacji** . Jeśli nie masz już wystąpienia, możesz je utworzyć, wykonując kroki opisane w temacie [*jak to zrobić: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-portal.md). Skorzystaj z następujących wartości z Instalatora, które są przydatne w dalszej części tego artykułu:
    - Nazwa wystąpienia
    - Grupa zasobów

Te szczegóły można znaleźć w [Azure Portal](https://portal.azure.com) po skonfigurowaniu wystąpienia. Zaloguj się do portalu i wyszukaj nazwę wystąpienia na pasku wyszukiwania portalu.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Zrzut ekranu przedstawiający pasek wyszukiwania Azure Portal." lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

Wybierz wystąpienie z wyników, aby wyświetlić te szczegóły w omówieniu dla danego wystąpienia:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Zrzut ekranu przedstawiający stronę przeglądową wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Zostanie wyróżniona nazwa i Grupa zasobów.":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Tworzenie punktu końcowego dla usługi Azure Digital bliźniaczych reprezentacji

Są to obsługiwane typy punktów końcowych, które można utworzyć dla danego wystąpienia:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Aby uzyskać więcej informacji na temat różnych typów punktów końcowych, zobacz [*Wybieranie między usługami Azure Messaging*](../event-grid/compare-messaging-services.md).

W tej sekcji opisano sposób tworzenia jednego z tych punktów końcowych w [Azure Portal](https://portal.azure.com).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Tworzenie punktu końcowego 

Po utworzeniu zasobów punktu końcowego można używać ich na potrzeby punktu końcowego usługi Azure Digital bliźniaczych reprezentacji. Aby utworzyć nowy punkt końcowy, przejdź do strony wystąpienia w [Azure Portal](https://portal.azure.com) (można znaleźć wystąpienie, wprowadzając jego nazwę na pasku wyszukiwania portalu).

1. Z menu wystąpienie wybierz pozycję _punkty końcowe_. Następnie na stronie *punkty końcowe* wybierz pozycję *+ Utwórz punkt końcowy*. Spowoduje to otwarcie strony *Tworzenie punktu końcowego* , na której należy wypełnić pola w poniższych krokach.

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Zrzut ekranu przedstawiający tworzenie punktu końcowego typu Event Grid." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. Wprowadź **nazwę** punktu końcowego i wybierz **Typ punktu końcowego**.

1. Uzupełnij inne szczegóły, które są wymagane dla danego typu punktu końcowego, łącznie z subskrypcją i opisanymi [powyżej](#prerequisite-create-endpoint-resources)zasobami punktów końcowych.
    1. W przypadku tylko centrów zdarzeń i Service Busych punktów końcowych należy wybrać **Typ uwierzytelniania**. Można użyć uwierzytelniania opartego na kluczach ze wstępnie utworzoną regułą autoryzacji lub uwierzytelniania opartego na tożsamości, jeśli będziesz używać punktu końcowego z [tożsamością zarządzaną](concepts-security.md#managed-identity-for-accessing-other-resources-preview) dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Zrzut ekranu przedstawiający tworzenie punktu końcowego typu centrum zdarzeń." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. Zakończ tworzenie punktu końcowego, wybierając pozycję _Zapisz_.

>[!IMPORTANT]
> W celu pomyślnego użycia uwierzytelniania opartego na tożsamościach dla punktu końcowego należy utworzyć tożsamość zarządzaną dla swojego wystąpienia, wykonując czynności opisane w temacie [*How to: Enable Managed Identity for Routing Events (wersja zapoznawcza)*](./how-to-enable-managed-identities-portal.md).

Po utworzeniu punktu końcowego możesz sprawdzić, czy punkt końcowy został pomyślnie utworzony, sprawdzając ikonę powiadomienia na górnym pasku Azure Portal: 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Zrzut ekranu przedstawiający powiadomienie w celu zweryfikowania utworzenia punktu końcowego. Zaznaczona jest ikona dzwonka z górnego paska portalu i istnieje powiadomienie informujące o tym, że punkt końcowy ADT-EH-Endpoint został pomyślnie utworzony.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Jeśli Tworzenie punktu końcowego nie powiedzie się, obserwuj komunikat o błędzie i spróbuj ponownie za kilka minut.

Możesz również wyświetlić punkt końcowy, który został utworzony ponownie na stronie *punkty końcowe* wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Teraz usługa Event Grid, centrum zdarzeń lub Service Bus jest dostępna jako punkt końcowy w ramach usługi Azure Digital bliźniaczych reprezentacji pod nazwą wybraną dla punktu końcowego. Ta nazwa będzie używana zazwyczaj jako obiekt docelowy **trasy zdarzenia**, którą utworzysz [w dalszej części tego artykułu](#create-an-event-route).

### <a name="create-an-endpoint-with-dead-lettering"></a>Tworzenie punktu końcowego z utraconymi wiadomościami

Gdy punkt końcowy nie może dostarczyć zdarzenia w określonym czasie lub po próbie dostarczenia zdarzenia przez określoną liczbę razy, może wysłać niedostarczone zdarzenie do konta magazynu. Ten proces jest znany jako **utracony**.

Aby można było utworzyć punkt końcowy z włączoną obsługą utraconych danych, należy użyć [poleceń interfejsu wiersza polecenia](how-to-use-cli.md) lub [interfejsów API płaszczyzny kontroli](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) , aby utworzyć punkt końcowy, a nie Azure Portal.

Aby uzyskać instrukcje, jak to zrobić za pomocą tych narzędzi, zobacz [*interfejsy API i wersja interfejsu wiersza polecenia*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) tego artykułu.

## <a name="create-an-event-route"></a>Tworzenie trasy zdarzeń

Aby faktycznie wysyłać dane z usługi Azure Digital bliźniaczych reprezentacji do punktu końcowego, należy zdefiniować **trasę zdarzeń**. Te trasy umożliwiają deweloperom tworzenie przepływów zdarzeń w całym systemie i w ramach usług podrzędnych. Przeczytaj więcej na temat tras zdarzeń w temacie [*pojęcia: Routing Digital bliźniaczych reprezentacji Events*](concepts-route-events.md).

**Wymaganie wstępne**: należy utworzyć punkty końcowe zgodnie z wcześniejszym opisem w tym artykule przed rozpoczęciem tworzenia trasy. Po zakończeniu konfigurowania punktów końcowych można utworzyć trasę zdarzenia.

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

>[!NOTE]
> W filtrach jest **rozróżniana wielkość** liter i musi być zgodna z wielkością ładunku. 
>
> W przypadku filtrów telemetrii oznacza to, że wielkość liter musi być zgodna z wielkością liter w telemetrii wysyłanej przez urządzenie, a nie do wielkości liter zdefiniowanych w modelu sznurka.

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
* [*Koncepcje: powiadomienia o zdarzeniach*](concepts-event-notifications.md)