---
title: Utwórz niestandardowe wyzwalacze zdarzeń w Azure Data Factory
description: Dowiedz się, jak utworzyć niestandardowy wyzwalacz w Azure Data Factory, który uruchamia potok w odpowiedzi na niestandardowe zdarzenie opublikowane w Event Grid.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2d2f26b24e7fa10d9244de8f99d78c64a44b3d61
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785652"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>Tworzenie wyzwalacza uruchamiającego potok w odpowiedzi na zdarzenie niestandardowe (wersja zapoznawcza)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano wyzwalacze zdarzeń niestandardowych, które można utworzyć w potokach Data Factory.

Architektura sterowana zdarzeniami (EDA) to typowy wzorzec integracji danych, który obejmuje produkcję, wykrywanie, użycie i reagowanie na zdarzenia. Scenariusze integracji danych często wymagają od klientów Data Factory do wyzwalania potoków na podstawie określonych zdarzeń. Data Factory natywną integrację z usługą [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) teraz obejmują [zdarzenia niestandardowe](../event-grid/custom-topics.md): klienci wysyłają dowolne zdarzenia do tematu usługi Event Grid i Data Factory subskrybują temat i nasłuchują potoków odpowiednio.

> [!NOTE]
> Integracja opisana w tym artykule zależy od [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Upewnij się, że subskrypcja została zarejestrowana przy użyciu dostawcy zasobów Event Grid. Aby uzyskać więcej informacji, zobacz [dostawcy zasobów i ich typy](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Musisz mieć możliwość wykonania akcji *Microsoft. EventGrid/eventSubscriptions/**. Ta akcja jest częścią wbudowanej roli współautor EventGrid EventSubscription.

Ponadto łączenie parametrów potoku i niestandardowego wyzwalacza zdarzeń pozwala klientom analizować i odwoływać się do niestandardowego ładunku _danych_ w uruchomieniach potoków. pole _danych_ w niestandardowym ładunku zdarzeń to niezależna struktura klucza JSON, która zapewnia klientom maksymalną kontrolę nad uruchomieniami potoku sterowanego zdarzeniami.

> [!IMPORTANT]
> Co więcej, w niestandardowym ładunku zdarzenia może brakować klucza, do którego istnieje odwołanie w parametryzacja. _Uruchomienie wyzwalacza_ zakończy się niepowodzeniem z powodu błędu, co oznacza, że nie można obliczyć wyrażenia, ponieważ właściwość _KeyName_ nie istnieje. Zdarzenie nie zostanie wyzwolone w __żadnym__ _przebiegu potoku_ .

## <a name="setup-event-grid-custom-topic"></a>Konfiguracja Event Grid tematu niestandardowego

Aby użyć niestandardowego wyzwalacza zdarzeń w Data Factory, należy _najpierw_ skonfigurować [temat niestandardowy w programie Event Grid](../event-grid/custom-topics.md). Przepływ pracy różni się od wyzwalacza zdarzeń magazynu, gdzie Data Factory skonfiguruje temat. Tutaj musisz przejść do Azure Event Grid i samodzielnie utworzyć temat. Aby uzyskać więcej informacji na temat tworzenia tematu niestandardowego, zobacz [samouczki dotyczące portalu](../event-grid/custom-topics.md#azure-portal-tutorials) Azure Event Grid i [samouczki interfejsu wiersza polecenia](../event-grid/custom-topics.md#azure-cli-tutorials)

Fabryki danych oczekują zdarzeń do wykonania [Event Grid schemacie zdarzeń](../event-grid/event-schema.md). Upewnij się, że ładunki zdarzeń zawierają następujące pola.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

W tej sekcji pokazano, jak utworzyć wyzwalacz zdarzeń magazynu w Azure Data Factory interfejsie użytkownika.

1. Przejdź do karty **Edycja** , która jest wyświetlana z symbolem ołówka.

1. Wybierz pozycję **wyzwalacz** w menu, a następnie wybierz pozycję **Nowy/Edytuj**.

1. Na stronie **Dodawanie wyzwalaczy** wybierz pozycję **Wybierz wyzwalacz...**, a następnie wybierz pozycję **+ Nowy**.

1. Wybieranie **zdarzeń niestandardowych** typu wyzwalacza

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Zrzut ekranu przedstawiający stronę autor, aby utworzyć nowy wyzwalacz zdarzeń niestandardowych w interfejsie użytkownika Data Factory." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Wybierz niestandardowy temat z listy rozwijanej subskrypcja platformy Azure lub ręcznie wprowadź zakres tematu zdarzenia.

   > [!NOTE]
   > Aby utworzyć nowy lub zmodyfikować istniejący wyzwalacz zdarzeń niestandardowych, konto platformy Azure używane do logowania się do Data Factory i publikowania wyzwalacza zdarzenia magazynu musi mieć odpowiednie uprawnienie kontroli dostępu opartej na rolach (RBAC) w temacie. Nie są wymagane żadne dodatkowe uprawnienia: Nazwa główna usługi dla Azure Data Factory _nie wymaga specjalnego_ uprawnienia do Event Grid. Aby uzyskać więcej informacji na temat kontroli dostępu, zobacz sekcję [Kontrola dostępu oparta na rolach](#role-based-access-control) .

1. **Temat rozpoczyna się od** i **następuje zakończenie z** właściwościami, co umożliwia filtrowanie zdarzeń, dla których ma być wyzwalany potok. Obie właściwości są opcjonalne.

1. Użyj **+ New** , aby dodać **typy zdarzeń** , według których chcesz filtrować. Niestandardowy wyzwalacz zdarzeń pracownika lub relacji dla listy: Jeśli zdarzenie niestandardowe ma właściwość _EventType_ , która pasuje do któregokolwiek z wymienionych w tym miejscu, wyzwoli uruchomienie potoku. W typie zdarzenia nie jest rozróżniana wielkość liter. Na przykład na poniższym zrzucie ekranu wyzwalacz dopasowuje wszystkie zdarzenia _copycompleted_ lub _copysucceeded_ z podmiotem rozpoczynającym się od _fabryk_

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Zrzut ekranu przedstawiający stronę Edytowanie wyzwalacza w celu wyjaśnienia typów zdarzeń i filtrowania podmiotu w interfejsie użytkownika Data Factory.":::

1. Wyzwalacz zdarzeń niestandardowych może analizować i wysyłać niestandardowy ładunek _danych_ do potoku. Najpierw utwórz parametry potoku i wypełnij wartości na stronie **parametrów** . Użyj formatu **@triggerBody (). Event. Data._NazwaKlucza_** , aby przeanalizować ładunek danych i przekazać wartości do parametrów potoku. Aby uzyskać szczegółowe wyjaśnienie, zobacz [odwołania do metadanych wyzwalacza w potokach](how-to-use-trigger-parameterization.md) i [zmiennych systemowych w wyzwalaczu zdarzenia niestandardowego](control-flow-system-variables.md#custom-event-trigger-scope)

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Zrzut ekranu przedstawiający ustawienie parametrów potoku.":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Zrzut ekranu przedstawiający stronę parametrów, aby odwołać się do ładunku danych w zdarzeniu niestandardowym.":::

1. Po zakończeniu kliknij przycisk **OK** .

## <a name="json-schema"></a>Schemat JSON

Poniższa tabela zawiera omówienie elementów schematu, które są powiązane z wyzwalaczami zdarzeń niestandardowych:

| **Element JSON** | **Opis** | **Typ** | **Dozwolone wartości** | **Wymagane** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Scope** | Identyfikator zasobu Azure Resource Manager tematu siatki zdarzeń. | Ciąg | Identyfikator Azure Resource Manager | Tak |
| **wydarzeniach** | Typ zdarzeń, które powodują uruchomienie tego wyzwalacza. | Tablica ciągów    |  | Tak, oczekiwano co najmniej jednej wartości |
| **subjectBeginsWith** | Pole podmiotu musi rozpoczynać się od wzorca podanego dla wyzwalacza, który ma zostać wywołany. Na przykład uruchamia `factories` wyzwalacz dla podmiotu zdarzenia rozpoczynającego się od `factories` . | Ciąg   | | Nie |
| **subjectEndsWith** | Pole podmiotu musi kończyć się wzorcem podanym dla wyzwalacza. | Ciąg   | | Nie |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Azure Data Factory używa kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby mieć pewność, że nieautoryzowany dostęp do nasłuchiwania, subskrybowania aktualizacji i potoków wyzwalaczy połączonych ze zdarzeniami niestandardowymi, są absolutnie zabronione.

* Aby pomyślnie utworzyć nowy lub zaktualizować istniejący wyzwalacz zdarzeń niestandardowych, konto platformy Azure zalogowane do Data Factory musi mieć odpowiedni dostęp do odpowiedniego konta magazynu. W przeciwnym razie operacja z niepowodzeniem z powodu _odmowy dostępu_.
* Data Factory nie musi mieć specjalnych uprawnień do Event Grid i _nie_ trzeba przypisywać specjalnych uprawnień kontroli RBAC platformy Azure Data Factory do nazwy głównej usługi dla tej operacji.

W odniesieniu do klienta wymagane jest uprawnienie _Microsoft. EventGrid/EventSubscriptions/zapis_ na _/subscriptions/# # # #/resourceGroups//# # # #/Providers/Microsoft.EventGrid/topics/someTopics_

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [wykonywanie i wyzwalacze potoku](concepts-pipeline-execution-triggers.md#trigger-execution).
* Dowiedz się, jak odwoływać się do metadanych wyzwalacza w potoku, zobacz [metadane wyzwalacza odwołań w uruchomieniach potoków](how-to-use-trigger-parameterization.md)
