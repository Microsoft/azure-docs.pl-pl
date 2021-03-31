---
title: Porównanie usług obsługi komunikatów platformy Azure
description: W tym artykule opisano trzy usługi do obsługi wiadomości na platformie Azure — Azure Event Grid, Event Hubs i Service Bus. Zalecenia dotyczące tego, której usługi używać w różnych scenariuszach.
ms.topic: overview
ms.date: 07/07/2020
ms.openlocfilehash: 7a3a0cd7f63a67206053ae55f33bd71aee2c19c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460409"
---
# <a name="choose-between-azure-messaging-services---event-grid-event-hubs-and-service-bus"></a>Wybór usługi do obsługi wiadomości na platformie Azure — Event Grid, Event Hubs i Service Bus

Platforma Azure oferuje 3 usługi, które pomagają dostarczać komunikaty o zdarzeniach w obrębie rozwiązania. Te usługi to:

* [Event Grid](./index.yml)
* [Event Hubs](../event-hubs/index.yml)
* [Service Bus](../service-bus-messaging/index.yml)

Mimo iż pod niektórymi względami są one do siebie podobne, każdą z tych usług zaprojektowano z myślą o konkretnych scenariuszach. W tym artykule opisano różnice między tymi usługami w celu ułatwienia wyboru odpowiedniej usługi dla aplikacji. W wielu przypadkach usługi obsługi komunikatów uzupełniają się wzajemnie i można ich używać razem.

## <a name="event-vs-message-services"></a>Zdarzenia a usługi obsługi komunikatów

Należy pamiętać o istotnej różnicy między usługami dostarczającymi zdarzenie a usługami dostarczającymi komunikat.

### <a name="event"></a>Zdarzenie

Zdarzenie to lekkie powiadomienie z informacją o zmianie stanu lub warunku. Wydawca zdarzenia nie ma żadnych oczekiwań dotyczących sposobu obsługi zdarzenia. Odbiorca zdarzenia decyduje o tym, co zrobić z powiadomieniem. Zdarzenia mogą być odrębnymi jednostkami lub częścią serii.

Zdarzenia odrębne zgłaszają zmianę stanu i umożliwiają wykonanie akcji. Aby wykonać kolejny krok, odbiorca musi tylko wiedzieć, że coś się wydarzyło. Dane zdarzenia zawierają informacje o tym, co się stało, ale nie zawierają danych, które wyzwoliły zdarzenie. Na przykład zdarzenie powiadamia odbiorców, że utworzono plik. Może ono zawierać ogólne informacje o pliku, ale nie zawiera samego pliku. Zdarzenia odrębne doskonale sprawdzają się w rozwiązaniach [bez serwera](https://azure.com/serverless) wymagających skalowania.

Zdarzenia w serii zgłaszają warunek i nadają się do analizy. Zdarzenia są uporządkowane według czasu i powiązane. Odbiorca potrzebuje sekwencyjnej serii zdarzeń, aby móc przeanalizować, co się zdarzyło.

### <a name="message"></a>Komunikat

Komunikat to nieprzetworzone dane utworzone przez usługę, które mają zostać użyte lub przechowane w innej lokalizacji. Komunikat zawiera dane, które spowodowały wyzwolenie potoku komunikatów. Wydawca komunikatu ma oczekiwanie dotyczące sposobu obsługi komunikatu przez odbiorcę. Pomiędzy obiema stronami obowiązuje kontrakt. Na przykład wydawca wysyła komunikat z nieprzetworzonymi danymi i oczekuje, że odbiorca utworzy plik na podstawie tych danych i wyśle odpowiedź po zakończeniu pracy.

## <a name="comparison-of-services"></a>Porównanie usług

| Usługa | Przeznaczenie | Typ | Kiedy stosować |
| ------- | ------- | ---- | ----------- |
| Event Grid | Programowanie reaktywne | Dystrybucja zdarzeń (odrębne) | Reagowanie na zmiany stanu |
| Event Hubs | Potok danych big data | Przesyłanie strumieniowe zdarzeń (serie) | Przesyłanie strumieniowe rozproszonych danych i telemetrii |
| Service Bus | Obsługa komunikatów o wysokiej wartości w przedsiębiorstwie | Komunikat | Przetwarzanie zamówień i transakcje finansowe |

### <a name="event-grid"></a>Event Grid

Usługa Event Grid to płyta montażowa obsługi zdarzeń umożliwiająca sterowane zdarzeniami programowanie reaktywne. Używa modelu publikuj-subskrybuj. Wydawcy emitują zdarzenia, ale nie mają żadnych oczekiwań dotyczących tego, które zdarzenia zostaną obsłużone. Subskrybenci decydują, które zdarzenia chcą obsłużyć.

Usługa Event Grid jest ściśle zintegrowana z usługami platformy Azure i można ją zintegrować z usługami innych firm. Upraszcza ona używanie zdarzeń i obniża koszty przez wyeliminowanie potrzeby ciągłego sondowania. Usługa Event Grid skutecznie i niezawodnie kieruje zdarzenia z platformy Azure i zasobów spoza platformy Azure. Dystrybuuje zdarzenia do punktów końcowych zarejestrowanych subskrybentów. Komunikat zdarzenia zawiera informacje potrzebne do zareagowania na zmiany w usługach i aplikacjach. Usługa Event Grid nie jest potokiem danych i nie dostarcza rzeczywistego obiektu, który został zaktualizowany.

Usługa Event Grid obejmuje obsługę utraconych komunikatów dla zdarzeń, które nie są dostarczane do punktu końcowego.

Ma następujące cechy:

* dynamiczna skalowalność
* niski koszt
* praca bezserwerowa
* co najmniej jednokrotne dostarczanie

### <a name="event-hubs"></a>Event Hubs

Usługa Azure Event Hubs to potok danych big data. Ułatwia przechwytywanie, przechowywanie i ponowne odtwarzanie danych telemetrycznych i danych strumienia zdarzeń. Dane mogą pochodzić z wielu równoczesnych źródeł. Usługa Event Hubs umożliwia udostępnianie danych telemetrycznych i danych zdarzeń różnym infrastrukturom przetwarzania strumieni oraz usługom analizy. Jest dostępna jako strumienie danych albo partie powiązanych zdarzeń. Ta usługa zapewnia pojedyncze rozwiązanie umożliwiające szybkie pobieranie danych na potrzeby przetwarzania w czasie rzeczywistym oraz powtarzane ponowne odtwarzanie przechowywanych nieprzetworzonych danych. Może przechwycić dane przesyłane strumieniowo do pliku na potrzeby przetwarzania i analizy.

Ma następujące cechy:

* małe opóźnienie
* możliwość odbieranie i przetwarzania milionów zdarzeń na sekundę
* co najmniej jednokrotne dostarczanie

### <a name="service-bus"></a>Service Bus

Usługa Service Bus jest przeznaczona dla tradycyjnych aplikacji dla przedsiębiorstw. Te aplikacje dla przedsiębiorstw wymagają obsługi transakcji, zamawiania, wykrywania duplikatów oraz natychmiastowej spójności. Usługa Service Bus umożliwia aplikacjom [natywnym dla chmury](https://azure.microsoft.com/overview/cloudnative/) zapewnianie niezawodnego zarządzania zmianami stanu dla procesów biznesowych. Usługi Azure Service Bus należy użyć w przypadku obsługi komunikatów o wysokiej wartości, które nie mogą być utracone ani zduplikowane. Usługa Service Bus ułatwia także bardzo bezpieczną komunikację między hybrydowymi rozwiązaniami w chmurze oraz może połączyć istniejące systemy lokalne z rozwiązaniami w chmurze.

Usługa Service Bus jest systemem komunikatów obsługiwanych przez brokera. W niezawodny sposób przechowuje komunikaty w „brokerze” (np. w kolejce) do momentu, w którym strona odbierająca będzie gotowa do odebrania komunikatów.

Ma następujące cechy:

* niezawodne asynchroniczne dostarczanie komunikatów (komunikaty w przedsiębiorstwie jako usługa) wymagające sondowania
* zaawansowane funkcje obsługi wiadomości, takie jak FIFO, tworzenie partii / sesje, transakcje, obsługa utraconych komunikatów, kontrola czasowa, routing i filtrowanie oraz wykrywanie duplikatów
* co najmniej jednokrotne dostarczanie
* opcjonalne dostarczanie w określonej kolejności

## <a name="use-the-services-together"></a>Jednoczesne używanie usług

W niektórych przypadkach usług można używać obok siebie w celu spełniania unikatowych ról. Na przykład witryna handlu elektronicznego może używać Service Bus, aby przetwarzać zamówienie, Event Hubs do przechwytywania danych telemetrycznych lokacji i Event Grid reagować na zdarzenia, takie jak element został wysłany.

W innych przypadkach można je połączyć ze sobą w celu utworzenia potoku zdarzeń i danych. Usługa Event Grid służy wtedy do reagowania na zdarzenia w innych usługach. Aby zapoznać się z przykładem migrowania danych do magazynu danych za pomocą usług Event Grid i Event Hubs, zobacz [Przesyłanie strumieniowe danych big data do magazynu danych](event-grid-event-hubs-integration.md). Na poniższej ilustracji przedstawiono przepływ pracy przesyłania strumieniowego danych.

![Omówienie przesyłania strumieniowego danych](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 
- [Możliwości asynchronicznej obsługi komunikatów na platformie Azure](/azure/architecture/guide/technology-choices/messaging)
- [Zdarzenia, punkty danych i komunikaty — wybieranie usługi platformy Azure do obsługi wiadomości właściwej dla Twoich danych](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/).
- [Porównanie kolejek magazynu i kolejek usługi Service Bus](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- Aby rozpocząć pracę z usługą Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
- Aby rozpocząć pracę z usługą Event Hubs, zobacz [Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń za pomocą witryny Azure Portal](../event-hubs/event-hubs-create.md).
- Aby rozpocząć pracę z usługą Service Bus, zobacz [Tworzenie przestrzeni nazw usługi Service Bus za pomocą witryny Azure Portal](../service-bus-messaging/service-bus-create-namespace-portal.md).
