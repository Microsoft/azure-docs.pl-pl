---
title: Komunikaty i połączenia w usłudze Azure SignalR Service
description: Omówienie kluczowych założeń dotyczących komunikatów i połączeń w usłudze Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 3c4d28addac0ecfc9605678582562550a1c96b8d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491949"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Komunikaty i połączenia w usłudze Azure SignalR Service

Model rozliczania usługi Azure SignalR Service jest oparty na liczbie połączeń i liczbie komunikatów. W tym artykule wyjaśniono, w jaki sposób komunikaty i połączenia są definiowane i zliczane w celach rozliczeniowych.


## <a name="message-formats"></a>Formaty komunikatów 

Usługa sygnałów platformy Azure obsługuje te same formaty co ASP.NET Core sygnalizujący: [JSON](https://www.json.org/) i [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Rozmiar komunikatu

Usługa Azure SignalR Service nie ma limitu rozmiaru komunikatu.

Duże komunikaty są dzielone na mniejsze, o rozmiarze nie większym niż 2 KB, które są przekazywane oddzielnie. Dzielenie i składanie komunikatów jest obsługiwane przez zestawy SDK. Nie wymaga to żadnych prac deweloperskich.

Duże komunikaty negatywnie wpływają na wydajność przesyłania komunikatów. Zawsze, gdy jest to możliwe, używaj mniejszego rozmiaru komunikatu i dla każdego scenariusza przypadku użycia przetestuj, jaki jest dla niego optymalny rozmiar komunikatu.

## <a name="how-messages-are-counted-for-billing"></a>Jak komunikaty są liczone na potrzeby rozliczeń

Na potrzeby rozliczeń są uwzględniane tylko komunikaty wychodzące usługi Azure SignalR Service. Komunikaty ping między klientami a serwerami są ignorowane.

Komunikaty o rozmiarze przekraczającym 2 KB są liczone jako większa liczba komunikatów, z których każdy ma rozmiar 2 KB. Wykres liczby komunikatów w witrynie Azure Portal jest aktualizowany co 100 komunikatów dla każdego centrum.

Załóżmy na przykład, że masz jeden serwer aplikacji i trzech klientów:

Serwer aplikacji emituje komunikat o wymiarze 1 KB do wszystkich połączonych klientów. komunikat z serwera aplikacji do usługi jest traktowany jako bezpłatny komunikat przychodzący. Tylko trzy komunikaty wysyłane z usługi do każdego klienta są rozliczane jako wiadomości wychodzące.

Klient A wysyła komunikat o wymiarze 1 KB do innego klienta B, bez przechodzenia przez serwer aplikacji. Komunikat od klienta do usługi jest bezpłatny. Komunikat od usługi do klienta B jest rozliczany jako komunikat wychodzący.

Jeśli masz trzech klientów i jeden serwer aplikacji. Jeden klient wysyła komunikat o rozmiarze 4 KB, a serwer rozgłasza go do wszystkich klientów. Liczba rozliczonych komunikatów to osiem: jeden komunikat z usługi do serwera aplikacji oraz trzy komunikaty z usługi do klientów. Każdy komunikat jest liczony jako dwa komunikaty o rozmiarze 2 KB.

## <a name="how-connections-are-counted"></a>Jak liczone są połączenia

Istnieją połączenia z serwerem i połączenia klienckie z usługą Azure Signal Service. Domyślnie każdy serwer aplikacji jest uruchamiany z pięciu początkowych połączeń na koncentrator, a każdy klient ma jedno połączenie z klientem.

Załóżmy na przykład, że masz dwa serwery aplikacji i definiujesz pięć centrów w kodzie. Liczba połączeń serwera będzie 50:2 serwery aplikacji * 5 centrów * 5 połączeń na koncentrator.

Liczba połączeń pokazana w Azure Portal obejmuje połączenia z serwerem, połączenia klientów, połączenia diagnostyczne i połączenia śledzenia na żywo. Typy połączeń są zdefiniowane na poniższej liście:

- **Połączenie z serwerem**: łączy usługę Azure sygnalizującą i serwer aplikacji.
- **Połączenie klienta**: łączy usługę Azure sygnalizującą i aplikację kliencką.
- **Połączenie diagnostyczne**: specjalne połączenie z klientem, które może generować bardziej szczegółowy dziennik, co może wpłynąć na wydajność. Ten rodzaj klienta jest przeznaczony do rozwiązywania problemów.
- **Połączenie śledzenia na żywo**: łączy się z punktem końcowym śledzenia na żywo i odbiera dynamiczne ślady usługi Azure Signal Service. 
 
Należy zauważyć, że połączenie na żywo nie jest zliczane jako połączenie klienta lub jako połączenie z serwerem. 

Biblioteka SignalR platformy ASP.NET inaczej oblicza liczbę połączeń serwera. Uwzględnia ona jedno centrum domyślne w dodatku do zdefiniowanych centrów. Domyślnie każdy serwer aplikacji wymaga pięciu dodatkowych połączeń z serwerem początkowym. Początkowa liczba połączeń dla domyślnego centrum pozostaje spójna z innymi centrami.

Usługa i serwer aplikacji synchronizują stan połączenia i dostosowując do połączeń z serwerem w celu uzyskania lepszej wydajności i stabilności usług.  W związku z tym może pojawić się zmiana numeru połączenia serwera od czasu do czasu.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Jak jest liczony ruch przychodzący/wychodzący

Komunikat wysłany do usługi jest komunikatem przychodzącym. Komunikat wysłany z usługi jest komunikatem wychodzącym. Ruch jest obliczany w bajtach.

## <a name="related-resources"></a>Powiązane zasoby

- [Aggregation types in Azure Monitor (Typy agregacji w usłudze Azure Monitor)](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr )
- [Konfiguracja biblioteki SignalR platformy ASP.NET Core](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
