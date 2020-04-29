---
title: Koncepcje Azure Event Grid
description: Opis usługi Azure Event Grid i pojęć z nią związanych. Definiuje kilka najważniejszych składników Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 348d82f704b89b97e11a09b8f88e92831901b3bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393458"
---
# <a name="concepts-in-azure-event-grid"></a>Pojęcia w Azure Event Grid

W tym artykule opisano główne koncepcje w Azure Event Grid.

## <a name="events"></a>Zdarzenia

Wydarzenie jest najmniejszą ilością informacji, które w pełni opisują coś, co się stało w systemie. Każde zdarzenie ma typowe informacje, takie jak: Źródło zdarzenia, czas trwania zdarzenia i unikatowy identyfikator. Każde zdarzenie ma także określone informacje, które mają zastosowanie tylko do określonego typu zdarzenia. Na przykład zdarzenie dotyczące tworzenia nowego pliku w usłudze Azure Storage zawiera szczegóły pliku, takie jak wartość `lastTimeModified`. Zdarzenie usługi Event Hubs ma adres URL pliku funkcji Capture. 

Zdarzenie o rozmiarze do 64 KB jest objęte ogólnym dostępnością Umowa dotycząca poziomu usług (SLA). Obsługa zdarzeń o rozmiarze do 1 MB jest obecnie dostępna w wersji zapoznawczej. Zdarzenia powyżej 64 KB są naliczone w przyrostach 64 KB. 


Dla właściwości, które są wysyłane w zdarzeniu, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md).

## <a name="publishers"></a>Wydawcy

Wydawca to użytkownik lub organizacja, która podejmuje decyzję o wysłaniu zdarzeń do Event Grid. Firma Microsoft publikuje zdarzenia dla kilku usług platformy Azure. Możesz publikować zdarzenia z własnej aplikacji. Organizacje, które hostują usługi poza platformą Azure, mogą publikować zdarzenia przy użyciu usługi Event Grid.

## <a name="event-sources"></a>Źródła zdarzeń

Źródło zdarzenia to miejsce, w którym występuje zdarzenie. Każde źródło zdarzenia jest powiązane z co najmniej jednym typem zdarzenia. Na przykład usługa Azure Storage jest źródłem zdarzeń tworzonych przez obiekty blob. Usługa IoT Hub jest źródłem zdarzeń tworzonych przez urządzenia. Aplikacja jest źródłem definiowanych przez Ciebie zdarzeń niestandardowych. Źródła zdarzeń służą do wysyłania zdarzeń do usługi Event Grid.

Aby uzyskać informacje na temat implementowania dowolnego z obsługiwanych źródeł Event Grid, zobacz [źródła zdarzeń w Azure Event Grid](overview.md#event-sources).

## <a name="topics"></a>Tematy

Temat GridY zdarzeń zawiera punkt końcowy, w którym źródło wysyła zdarzenia. Wydawca tworzy temat siatka zdarzeń i decyduje o tym, czy źródło zdarzenia wymaga jednego tematu, czy też więcej niż jednego tematu. Temat służy do zbierania powiązanych zdarzeń. Aby odpowiedzieć na określone typy zdarzeń, subskrybenci decydują, które tematy zasubskrybować.

Tematy systemowe to wbudowane tematy udostępniane przez usługi platformy Azure. Tematy systemowe nie są widoczne w ramach subskrypcji platformy Azure, ponieważ wydawca jest właścicielem tematów, ale można je subskrybować. Aby zasubskrybować temat, podaj informacje o zasobie, z którego chcesz odbierać zdarzenia. Możesz subskrybować zdarzenia, dopóki masz dostęp do ich zasobu.

Tematy niestandardowe to tematy aplikacji i innych firm. Gdy temat niestandardowy zostanie utworzony lub zostanie przypisany do niego dostęp, będzie on widoczny w ramach subskrypcji.

Podczas projektowania aplikacji masz elastyczność podczas decydowania o liczbie tematów do utworzenia. W przypadku dużych rozwiązań Utwórz niestandardowy temat dla każdej kategorii powiązanych zdarzeń. Może to na przykład być aplikacja, która wysyła zdarzenia powiązane z modyfikowaniem kont użytkowników i przetwarzaniem zamówień. Istnieje małe prawdopodobieństwo, że procedura obsługi zdarzeń oczekuje obu kategorii zdarzeń. Utwórz dwa tematy niestandardowe, a procedury obsługi zdarzeń subskrybują temat, którymi są zainteresowane. W przypadku małych rozwiązań można chcieć wysłać wszystkie zdarzenia do jednego tematu. Subskrybenci zdarzeń mogą odfiltrować żądane typy zdarzeń.

## <a name="event-subscriptions"></a>Subskrypcje zdarzeń

Subskrypcja informuje Event Grid, które zdarzenia zostały odebrane w temacie. Podczas tworzenia subskrypcji jest udostępniany punkt końcowy do obsługi zdarzenia. Można filtrować zdarzenia, które są wysyłane do punktu końcowego. Można filtrować według typu zdarzenia lub wzorca podmiotu. Aby uzyskać więcej informacji, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).

Przykłady tworzenia subskrypcji można znaleźć w temacie:

* [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Event Grid](cli-samples.md)
* [Przykłady programu Azure PowerShell dla usługi Event Grid](powershell-samples.md)
* [Szablony usługi Azure Resource Manager dla usługi Event Grid](template-samples.md)

Aby uzyskać informacje na temat pobierania bieżących subskrypcji usługi Event Grid, zobacz temat [zapytanie Event Grid subskrypcje](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Wygaśnięcie subskrypcji zdarzeń
Subskrypcja zdarzeń automatycznie wygaśnie po tej dacie. Ustaw wygasanie dla subskrypcji zdarzeń, które są wymagane tylko przez ograniczony czas i nie chcesz martwić się o czyszczenie tych subskrypcji. Na przykład podczas tworzenia subskrypcji zdarzeń do testowania scenariusza może zajść potrzeba ustawienia wygaśnięcia. 

Aby zapoznać się z przykładem ustawiania wygaśnięcia, zobacz [Subskrybuj with Advanced Filters](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Procedury obsługi zdarzeń

W perspektywie Event Grid program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Procedura obsługi wykonuje kilka dalszych działań w celu przetworzenia zdarzenia. Event Grid obsługuje kilka typów obsługi. Możesz użyć obsługiwanej usługi platformy Azure lub własnego elementu webhook jako procedury obsługi. W zależności od typu procedury obsługi Event Grid są zgodne z różnymi mechanizmami w celu zagwarantowania dostarczania zdarzenia. W przypadku obsługi zdarzeń elementu webhook protokołu HTTP zdarzenie jest ponawiane do momentu, gdy procedura obsługi zwróci `200 – OK`kod stanu. W przypadku kolejki usługi Azure Storage zdarzenia są ponawiane, dopóki usługa kolejki pomyślnie przetworzy komunikat wypchnięcie do kolejki.

Aby uzyskać informacje dotyczące implementowania dowolnego obsługiwanego programu obsługi Event Grid, zobacz [programy obsługi zdarzeń w programie Azure Event Grid](event-handlers.md).

## <a name="security"></a>Zabezpieczenia

Event Grid zapewnia zabezpieczenia subskrybowania tematów i publikowania tematów. Podczas subskrybowania musisz mieć odpowiednie uprawnienia do tematu zasobów lub siatki zdarzeń. Podczas publikowania należy mieć token SAS lub uwierzytelnianie klucza dla tematu. Aby uzyskać więcej informacji, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](security-authentication.md).

## <a name="event-delivery"></a>Dostarczanie zdarzeń

Jeśli Event Grid nie może potwierdzić, że zdarzenie zostało odebrane przez punkt końcowy subskrybenta, ponownie dostarcza zdarzenie. Aby uzyskać więcej informacji, zobacz [Event Grid dostarczania komunikatów i ponów próbę](delivery-and-retry.md).

## <a name="batching"></a>Przetwarzanie wsadowe

W przypadku korzystania z tematu niestandardowego zdarzenia muszą być zawsze publikowane w tablicy. Może to być partia dla scenariuszy o niskiej przepływności, jednak w przypadku dużych przypadków użycia należy wykonać wiele zadań wsadowych na potrzeby publikowania, aby osiągnąć wyższą wydajność. Liczba partii może wynosić do 1 MB. Każde zdarzenie nie powinno być większe niż 64 KB (ogólna dostępność) lub 1 MB (wersja zapoznawcza).

> [!NOTE]
> Zdarzenie o rozmiarze do 64 KB jest objęte ogólnym dostępnością Umowa dotycząca poziomu usług (SLA). Obsługa zdarzeń o rozmiarze do 1 MB jest obecnie dostępna w wersji zapoznawczej. Zdarzenia powyżej 64 KB są naliczone w przyrostach 64 KB. 

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
