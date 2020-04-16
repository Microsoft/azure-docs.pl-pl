---
title: Pojęcia dotyczące usługi Azure Event Grid
description: Opis usługi Azure Event Grid i pojęć z nią związanych. Definiuje kilka kluczowych składników siatki zdarzeń.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 348d82f704b89b97e11a09b8f88e92831901b3bf
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393458"
---
# <a name="concepts-in-azure-event-grid"></a>Pojęcia w usłudze Azure Event Grid

W tym artykule opisano główne pojęcia w usłudze Azure Event Grid.

## <a name="events"></a>Zdarzenia

Zdarzenie jest najmniejszą ilością informacji, które w pełni opisuje coś, co wydarzyło się w systemie. Każde zdarzenie ma wspólne informacje, takie jak: źródło zdarzenia, czas zdarzenia i unikatowy identyfikator. Każde zdarzenie ma również określone informacje, które są istotne tylko dla określonego typu zdarzenia. Na przykład zdarzenie dotyczące tworzenia nowego pliku w usłudze Azure Storage zawiera szczegóły pliku, takie jak wartość `lastTimeModified`. Zdarzenie usługi Event Hubs ma adres URL pliku funkcji Capture. 

Zdarzenie o rozmiarze do 64 KB jest objęte umową dotyczącą poziomu usług ogólnej dostępności (GA). Obsługa zdarzenia o rozmiarze do 1 MB jest obecnie w wersji zapoznawczej. Zdarzenia o masie powyżej 64 KB są naliczane w przyrostach o 64 KB. 


Właściwości wysyłane w zdarzeniu można znaleźć w [schemacie zdarzeń usługi Azure Event Grid](event-schema.md).

## <a name="publishers"></a>Wydawcy

Wydawcą jest użytkownik lub organizacja, która decyduje się na wysyłanie zdarzeń do usługi Event Grid. Firma Microsoft publikuje zdarzenia dla kilku usług platformy Azure. Możesz publikować zdarzenia z własnej aplikacji. Organizacje, które hostują usługi poza platformą Azure, mogą publikować zdarzenia przy użyciu usługi Event Grid.

## <a name="event-sources"></a>Źródła zdarzeń

Źródłem zdarzenia jest miejsce, w którym zdarzenie się dzieje. Każde źródło zdarzenia jest powiązane z co najmniej jednym typem zdarzenia. Na przykład usługa Azure Storage jest źródłem zdarzeń tworzonych przez obiekty blob. Usługa IoT Hub jest źródłem zdarzeń tworzonych przez urządzenia. Aplikacja jest źródłem definiowanych przez Ciebie zdarzeń niestandardowych. Źródła zdarzeń służą do wysyłania zdarzeń do usługi Event Grid.

Aby uzyskać informacje dotyczące implementowania dowolnego z obsługiwanych źródeł usługi Event Grid, zobacz [Źródła zdarzeń w usłudze Azure Event Grid](overview.md#event-sources).

## <a name="topics"></a>Tematy

Temat siatki zdarzeń zawiera punkt końcowy, w którym źródło wysyła zdarzenia. Wydawca tworzy temat siatki zdarzeń i decyduje, czy źródło zdarzeń potrzebuje jednego tematu, czy więcej niż jednego tematu. Temat jest używany do kolekcji powiązanych zdarzeń. Aby odpowiedzieć na określone typy zdarzeń, subskrybenci decydują, które tematy mają być subskrybowanych.

Tematy systemowe to wbudowane tematy udostępniane przez usługi platformy Azure. Tematy systemowe nie są widoczne w ramach subskrypcji platformy Azure, ponieważ wydawca jest właścicielem tematów, ale można je subskrybować. Aby zasubskrybować temat, podaj informacje o zasobie, z którego chcesz odbierać zdarzenia. Możesz subskrybować zdarzenia, dopóki masz dostęp do ich zasobu.

Tematy niestandardowe to tematy aplikacji i innych firm. Gdy temat niestandardowy zostanie utworzony lub zostanie przypisany do niego dostęp, będzie on widoczny w ramach subskrypcji.

Podczas projektowania aplikacji, masz elastyczność przy podejmowaniu decyzji, ile tematów do utworzenia. W przypadku dużych rozwiązań utwórz temat niestandardowy dla każdej kategorii powiązanych zdarzeń. Może to na przykład być aplikacja, która wysyła zdarzenia powiązane z modyfikowaniem kont użytkowników i przetwarzaniem zamówień. Istnieje małe prawdopodobieństwo, że procedura obsługi zdarzeń oczekuje obu kategorii zdarzeń. Utwórz dwa tematy niestandardowe, a procedury obsługi zdarzeń subskrybują temat, którymi są zainteresowane. W przypadku małych rozwiązań możesz chcieć wysłać wszystkie zdarzenia do jednego tematu. Subskrybenci zdarzeń mogą filtrować typy zdarzeń, które chcą.

## <a name="event-subscriptions"></a>Subskrypcje zdarzeń

Subskrypcja informuje o tym, które zdarzenia na temat cię interesują. Podczas tworzenia subskrypcji, należy podać punkt końcowy do obsługi zdarzenia. Można filtrować zdarzenia, które są wysyłane do punktu końcowego. Można filtrować według typu zdarzenia lub wzorca obiektu. Aby uzyskać więcej informacji, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).

Przykłady tworzenia subskrypcji można znaleźć w części:

* [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Event Grid](cli-samples.md)
* [Przykłady programu Azure PowerShell dla usługi Event Grid](powershell-samples.md)
* [Szablony usługi Azure Resource Manager dla usługi Event Grid](template-samples.md)

Aby uzyskać informacje na temat uzyskiwania bieżących subskrypcji siatki zdarzeń, zobacz [Subskrypcje usługi Query Event Grid](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Wygaśnięcie subskrypcji zdarzeń
Subskrypcja zdarzeń automatycznie wygaśnie po tej dacie. Ustaw wygaśnięcie subskrypcji zdarzeń, które są potrzebne tylko przez ograniczony czas i nie chcesz się martwić o czyszczenie tych subskrypcji. Na przykład podczas tworzenia subskrypcji zdarzeń w celu przetestowania scenariusza można ustawić wygaśnięcia. 

Aby uzyskać przykład ustawienia wygaśnięcia, zobacz [Subskrybuj filtry zaawansowane](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Procedury obsługi zdarzeń

Z punktu widzenia siatki zdarzeń program obsługi zdarzeń jest miejscem, w którym jest wysyłane zdarzenie. Program obsługi podejmuje kilka dalszych działań do przetwarzania zdarzenia. Usługa Event Grid obsługuje kilka typów programów obsługi. Można użyć obsługiwanej usługi platformy Azure lub własnego elementu webhook jako programu obsługi. W zależności od typu programu obsługi usługa Event Grid następuje różne mechanizmy, aby zagwarantować dostarczanie zdarzenia. W przypadku programów obsługi zdarzeń elementu webhook HTTP zdarzenie jest `200 – OK`ponawiane, dopóki program obsługi nie zwróci kodu stanu . W przypadku kolejki usługi Azure Storage zdarzenia są ponawiane, dopóki usługa Queue pomyślnie nie przetwarza wypychania wiadomości do kolejki.

Aby uzyskać informacje dotyczące implementowania dowolnych obsługiwanych programów obsługi usługi Event Grid, zobacz [Programy obsługi zdarzeń w usłudze Azure Event Grid](event-handlers.md).

## <a name="security"></a>Zabezpieczenia

Usługa Event Grid zapewnia bezpieczeństwo subskrybowania tematów i publikowania tematów. Podczas subskrybowania, musi mieć odpowiednie uprawnienia do zasobu lub zdarzenia w temacie siatki. Podczas publikowania, musi mieć token sygnatury dostępu Współdzielonego lub uwierzytelnianie klucza dla tematu. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](security-authentication.md).

## <a name="event-delivery"></a>Dostarczanie zdarzeń

Jeśli usługa Event Grid nie może potwierdzić, że zdarzenie zostało odebrane przez punkt końcowy subskrybenta, ponownie likwia zdarzenie. Aby uzyskać więcej informacji, zobacz [Dostarczanie wiadomości w ucho i ponowie próbę](delivery-and-retry.md).

## <a name="batching"></a>Tworzenie partii

Podczas korzystania z tematu niestandardowego zdarzenia muszą być zawsze publikowane w tablicy. Może to być partia jednego dla scenariuszy niskiej przepływności, jednak w przypadkach użycia dużej ilości zaleca się, aby wsadować kilka zdarzeń razem na publikowanie, aby osiągnąć wyższą wydajność. Partie mogą mieć maksymalnie 1 MB. Każde zdarzenie nadal nie powinno być większe niż 64 KB (ogólna dostępność) lub 1 MB (wersja zapoznawcza).

> [!NOTE]
> Zdarzenie o rozmiarze do 64 KB jest objęte umową dotyczącą poziomu usług ogólnej dostępności (GA). Obsługa zdarzenia o rozmiarze do 1 MB jest obecnie w wersji zapoznawczej. Zdarzenia o masie powyżej 64 KB są naliczane w przyrostach o 64 KB. 

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z usługi Event Grid, zobacz [Tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
