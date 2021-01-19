---
title: Wewnętrzne elementy usługi Azure SignalR
description: Dowiedz się więcej na temat wewnętrznych usług sygnałów platformy Azure, architektury, połączeń i sposobu przesyłania danych.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: afb63b76666f47217f9c19376d81aa4ed73991bf
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572565"
---
# <a name="azure-signalr-service-internals"></a>Wewnętrzne elementy usługi Azure SignalR

Usługa Azure SignalR Service powstała na podstawie struktury SignalR platformy ASP.NET Core. Obsługuje ona również sygnalizację ASP.NET przez zaimplementowanie protokołu danych sygnalizującego ASP.NET w oparciu o strukturę ASP.NET Core.

Można z łatwością migrować ASP.NET Core lokalną aplikację ASP.NETer lub aplikację sygnalizującą do pracy z usługą sygnalizującego przy użyciu kilku wierszy zmiany kodu.

Na poniższym diagramie opisano typową architekturę stosowaną podczas używania usługi SignalR Service z serwerem aplikacji.

Omówiono także różnice względem samodzielnie hostowanej aplikacji SignalR platformy ASP.NET Core.

![Architektura](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Połączenia z serwerem

Samodzielnie hostowany serwer aplikacji SignalR platformy ASP.NET Core nasłuchuje i łączy klientów bezpośrednio.

W przypadku usługi SignalR Service serwer aplikacji nie przyjmuje już trwałych połączeń klientów, zamiast tego:

1. Punkt końcowy `negotiate` jest uwidaczniany przez zestaw SDK usługi Azure SignalR Service dla każdego centrum.
1. Ten punkt końcowy będzie odpowiadać na żądania negocjacji klienta i przekierowywać klientów do usługi SignalR Service.
1. W końcu klienci zostaną połączeni z usługą SignalR Service.

Aby uzyskać więcej informacji, zobacz [Połączenia klienta](#client-connections).

Po uruchomieniu serwera aplikacji: 
- W przypadku biblioteki SignalR platformy ASP.NET Core zestaw SDK Azure SignalR Service otwiera 5 połączeń protokołu WebSocket do usługi SignalR Service na centrum. 
- W przypadku biblioteki SignalR platformy ASP.NET zestaw SDK Azure SignalR Service otwiera 5 połączeń protokołu WebSocket do usługi SignalR Service na centrum i jedno połączenie WebSocket na aplikację.

Liczba 5 połączeń protokołu WebSocket jest wartością domyślną, którą można zmienić w [konfiguracji](https://github.com/Azure/azure-signalr/blob/dev/docs/run-asp-net-core.md#connectioncount).

Komunikaty do i od klientów będą multipleksowane do tych połączeń.

Te połączenia pozostaną połączone z usługą SignalR Service przez cały czas. W przypadku rozłączenia połączenia z serwerem z powodu problemu z siecią:
- wszyscy klienci obsługiwani przez połączenie tego serwera są rozłączani (aby uzyskać więcej informacji na ten temat, zobacz [Dane przesyłane między klientem a serwerem](#data-transmit-between-client-and-server));
- automatycznie zaczyna się ponowne nawiązywanie połączenia z serwerem.

## <a name="client-connections"></a>Połączenia klienta

Gdy używasz usługi SignalR Service, klienci łączą się z usługą SignalR Service, a nie z serwerem aplikacji.
Ustanowienie trwałego połączenia między klientem a usługą SignalR odbywa się w dwóch krokach.

1. Klient wysyła żądanie negocjowania do serwera aplikacji. Przy użyciu zestawu SDK Azure SignalR Service serwer aplikacji zwraca odpowiedź przekierowania przy użyciu adresu URL usługi SignalR Service i tokenu dostępu.

- W przypadku biblioteki SignalR platformy ASP.NET Core typowa odpowiedź przekierowania wygląda następująco:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- W przypadku biblioteki SignalR platformy ASP.NET typowa odpowiedź przekierowania wygląda następująco:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Po otrzymaniu odpowiedzi przekierowania klient używa nowego adresu URL i tokenu dostępu do rozpoczęcia normalnego procesu nawiązywania połączenia z usługą SignalR Service.

Dowiedz się więcej o [protokołach transportowych](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md) biblioteki SignalR platformy ASP.NET Core.

## <a name="data-transmit-between-client-and-server"></a>Dane przesyłane między klientem a serwerem

Gdy klient jest połączony z usługą SignalR Service, środowisko wykonawcze usługi znajduje połączenie z serwerem w celu obsługi tego klienta
- Ten krok odbywa się tylko raz i polega na mapowaniu jeden do jednego połączeń między klientem a serwerem.
- Mapowanie jest zachowywane w usłudze SignalR Service, dopóki klient lub serwer nie zamknie połączenia.

W tym momencie serwer aplikacji odbiera zdarzenie z informacjami od nowego klienta. Na serwerze aplikacji tworzone jest połączenie logiczne do klienta. Kanał danych jest ustanawiany od klienta do serwera aplikacji za pośrednictwem usługi SignalR Service.

Usługa sygnalizująca przesyła dane z klienta do serwera aplikacji parowania. Natomiast dane z serwera aplikacji będą wysyłane do zamapowanych klientów.

Usługa sygnalizująca nie zapisuje ani nie przechowuje danych klienta, wszystkie odebrane dane klienta są przesyłane do serwera docelowego lub klientów w czasie rzeczywistym.

Jak widać, usługa Azure SignalR Service to zasadniczo logiczna warstwa transportowa między serwerem aplikacji a klientami. Wszystkie trwałe połączenia są przenoszone do usługi SignalR Service.
Serwer aplikacji musi tylko obsługiwać logikę biznesową w klasie centrum bez martwienia się o połączenia klientów.
