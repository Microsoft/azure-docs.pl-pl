---
title: Zaawansowane ograniczanie żądań za pomocą usługi Azure API Management
description: Dowiedz się, jak tworzyć i stosować elastyczne zasady ograniczania limitów przydziału i szybkości za pomocą usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: fc36211eeb58f18546e4eae24ad003c6b2ae761b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243175"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Zaawansowane ograniczanie żądań za pomocą usługi Azure API Management
Możliwość ograniczania żądań przychodzących jest kluczową rolą usługi Azure API Management. Kontrolując częstotliwość żądań lub łączne żądania/przesyłane dane, API Management umożliwia dostawcom interfejsu API ochronę swoich interfejsów API przed nadużyciami i tworzenie wartości dla różnych warstw produktu interfejsu API.

## <a name="product-based-throttling"></a>Ograniczanie na podstawie produktu
Do tej pory możliwości ograniczania przepustowości zostały ograniczone do zakresu określonej subskrypcji produktu zdefiniowanej w Azure Portal. Jest to przydatne w przypadku, gdy dostawca interfejsu API stosuje limity dla deweloperów, którzy zarejestrowali się w celu korzystania z interfejsu API, ale nie jest to pomocne, na przykład w przypadku ograniczania indywidualnych użytkowników końcowych interfejsu API. Istnieje możliwość, że dla jednego użytkownika aplikacji dewelopera będzie można wykorzystać cały przydział, a następnie uniemożliwić innym klientom dewelopera korzystanie z aplikacji. Ponadto kilku klientów, którzy mogą generować duże ilości żądań, mogą ograniczyć dostęp do użytkowników okazjonalnych.

## <a name="custom-key-based-throttling"></a>Ograniczanie oparte na kluczach niestandardowych

> [!NOTE]
> `rate-limit-by-key`Zasady i `quota-by-key` nie są dostępne w ramach warstwy zużycia usługi Azure API Management. 

Nowe zasady [Rate-limit-by-Key](./api-management-access-restriction-policies.md#LimitCallRateByKey) i [Quote według klucza](./api-management-access-restriction-policies.md#SetUsageQuotaByKey) zapewniają bardziej elastyczne rozwiązanie do kontroli ruchu. Te nowe zasady umożliwiają definiowanie wyrażeń w celu identyfikowania kluczy, które są używane do śledzenia użycia ruchu sieciowego. Sposób, w jaki to działa, jest najłatwiejszym zilustrowanym przykładem. 

## <a name="ip-address-throttling"></a>Ograniczanie adresów IP
Poniższe zasady ograniczają pojedynczy adres IP klienta tylko do 10 wywołań co minutę z łączną liczbą wywołań 1 000 000 i 10 000 KB przepustowości miesięcznie. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Jeśli wszyscy klienci w Internecie użyły unikatowego adresu IP, może to być efektywny sposób ograniczania użycia przez użytkownika. Jednak prawdopodobnie wielu użytkowników współużytkuje jeden publiczny adres IP z powodu uzyskiwania dostępu do Internetu za pośrednictwem urządzenia NAT. Pomimo tego dla interfejsów API, które zezwalają na dostęp nieuwierzytelnionym, `IpAddress` może być najlepszą opcją.

## <a name="user-identity-throttling"></a>Ograniczanie tożsamości użytkownika
W przypadku uwierzytelnienia użytkownika końcowego klucz ograniczenia przepustowości może zostać wygenerowany na podstawie informacji, które jednoznacznie identyfikują tego użytkownika.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

W tym przykładzie pokazano, jak wyodrębnić nagłówek autoryzacji, przekonwertować go na `JWT` obiekt i użyć podmiotu tokenu, aby zidentyfikować użytkownika i użyć go jako klucza ograniczającego szybkość. Jeśli tożsamość użytkownika jest przechowywana w `JWT` postaci jako jednego z innych oświadczeń, ta wartość może zostać użyta w swoim miejscu.

## <a name="combined-policies"></a>Połączone zasady
Mimo że nowe zasady ograniczania przepustowości zapewniają większą kontrolę niż istniejące zasady ograniczania przepustowości, wciąż łączą się funkcje. Ograniczanie według klucza subskrypcji produktu ([ograniczanie liczby wywołań przez subskrypcję](./api-management-access-restriction-policies.md#LimitCallRate) i [Ustawianie przydziału użycia przez subskrypcję](./api-management-access-restriction-policies.md#SetUsageQuota)) to doskonały sposób na umożliwienie zarabiając interfejsu API przez naliczanie opłat na podstawie poziomów użycia. Dokładniejsza kontrola nad możliwością ograniczania przez użytkownika jest uzupełniana i uniemożliwia zachowanie jednego użytkownika z obniżania wydajności innego. 

## <a name="client-driven-throttling"></a>Ograniczanie przepustowości przez klienta
Gdy klucz ograniczenia jest zdefiniowany przy użyciu [wyrażenia zasad](./api-management-policy-expressions.md), jest to dostawca interfejsu API, który wybiera zakres ograniczania. Jednak deweloper może chcieć kontrolować sposób, w jaki klasyfikują swoich klientów. Może to być możliwe przez dostawcę interfejsu API przez wprowadzenie niestandardowego nagłówka, aby umożliwić aplikacji klienckiej dewelopera komunikowanie się z kluczem do interfejsu API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Dzięki temu aplikacja kliencka programisty może wybrać, w jaki sposób chcesz utworzyć klucz ograniczający szybkość. Deweloperzy klientów mogą tworzyć własne warstwy stawki, przydzielając zestawy kluczy do użytkowników i obracając użycie klucza.

## <a name="summary"></a>Podsumowanie
Usługa Azure API Management oferuje funkcję ograniczania szybkości i oferty do ochrony i dodawania wartości do usługi interfejsu API. Nowe zasady ograniczania przepustowości z niestandardowymi regułami określania zakresu umożliwiają dokładniejszą kontrolę nad tymi zasadami, aby umożliwić klientom tworzenie jeszcze lepszych aplikacji. W przykładach w tym artykule przedstawiono sposób korzystania z tych nowych zasad przez klucze ograniczające szybkość produkcji z wykorzystaniem adresów IP klientów, tożsamości użytkowników i wartości generowanych przez klientów. Istnieje jednak wiele innych części komunikatu, których można użyć, takich jak agent użytkownika, fragmenty ścieżek adresów URL, rozmiar wiadomości.

## <a name="next-steps"></a>Następne kroki
Przekaż nam swoją opinię jako problem z usługą GitHub dotyczącą tego tematu. Warto dowiedzieć się więcej na temat innych potencjalnych wartości kluczowych, które były logiczną wyborem w scenariuszach.
