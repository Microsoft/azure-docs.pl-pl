---
title: Azure Relay Połączenia hybrydowe — obiekty WebSockets w programie .NET
description: Napisz aplikację konsolową w języku C# dla Azure Relay Połączenia hybrydowe WebSockets.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: bf22b8b11dc386644803b43ee4e3a51d04b70419
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90527432"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Wprowadzenie do obiektów WebSocket połączeń hybrydowych usługi Relay na platformie .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

W tym przewodniku Szybki start utworzysz aplikacje nadawcy i odbiorcy w środowisku .NET umożliwiające wysyłanie i odbieranie komunikatów przy użyciu obiektów WebSocket połączeń hybrydowych w usłudze Azure Relay. Aby uzyskać więcej ogólnych informacji o usłudze Azure Relay, zobacz [Azure Relay](relay-what-is-it.md). 

W tym przewodniku Szybki start wykonasz następujące kroki:

1. Utworzenie przestrzeni nazw usługi Relay za pomocą witryny Azure Portal.
2. Utworzenie połączenia hybrydowego w tej przestrzeni nazw za pomocą witryny Azure Portal.
3. Napisanie aplikacji konsolowej serwera (odbiornika) służącej do odbierania komunikatów.
4. Napisanie aplikacji konsolowej klienta (nadawcy) służącej do wysyłania komunikatów.
5. Uruchamianie aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* [Program Visual Studio 2015 lub nowszy](https://www.visualstudio.com). W przykładach znajdujących się w tym samouczku używany jest program Visual Studio 2017.
* Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="create-a-namespace"></a>Tworzenie przestrzeni nazw
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Tworzenie połączenia hybrydowego
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Tworzenie aplikacji serwera (odbiornika)
W programie Visual Studio napisz aplikację konsoli w języku C#, aby nasłuchiwać i odbierać komunikaty z usługi Relay.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Tworzenie aplikacji klienta (nadawcy)
W programie Visual Studio napisz aplikację konsoli w języku C#, aby wysyłać komunikaty do usługi Relay.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Uruchamianie aplikacji
1. Uruchom aplikację serwera.
2. Uruchom aplikację klienta i wprowadź jakiś tekst.
3. Upewnij się, że w konsoli aplikacji serwera jest wyświetlany tekst wprowadzony w aplikacji klienta.

    ![Okna konsoli przetestuje zarówno aplikacje serwera, jak i klienta.](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Gratulacje, utworzono kompletną aplikację Połączenia hybrydowe.

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono aplikacje klienta i serwera w środowisku .NET służące do wysyłania i odbierania komunikatów za pomocą obiektów WebSocket. Funkcja połączeń hybrydowych usługi Azure Relay obsługuje również wysyłanie i odbieranie komunikatów przy użyciu protokołu HTTP. Aby dowiedzieć się, jak używać protokołu HTTP z funkcją połączeń hybrydowych usługi Azure Relay, zobacz [Przewodnik Szybki start dotyczący protokołu HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).

W tym przewodniku Szybki start za pomocą programu .NET Framework utworzono aplikacje klienta i serwera. Aby dowiedzieć się, jak pisać aplikacje klienta i serwera w środowisku Node.js, zapoznaj się z [przewodnikiem Szybki start dotyczącym obiektów WebSocket w środowisku Node.js](relay-hybrid-connections-node-get-started.md) lub [przewodnikiem Szybki start dotyczącym protokołu HTTP w środowisku Node.js](relay-hybrid-connections-http-requests-dotnet-get-started.md).

