---
title: Azure Relay Połączenia hybrydowe — obiekty WebSockets w węźle
description: Napisz aplikację konsolową w środowisku Node.js do obsługi obiektów WebSocket połączeń hybrydowych usługi Azure Relay
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: b362caa6570d4a8e212ff7adf4310a0c63e8b755
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263712"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Wprowadzenie do obiektów WebSocket połączeń hybrydowych usługi Relay w środowisku Node.js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

W tym przewodniku Szybki start utworzysz aplikacje nadawcy i odbiorcy w środowisku Node.js umożliwiające wysyłanie i odbieranie komunikatów przy użyciu obiektów WebSocket połączeń hybrydowych w usłudze Azure Relay. Aby uzyskać więcej ogólnych informacji o usłudze Azure Relay, zobacz [Azure Relay](relay-what-is-it.md). 

W tym przewodniku Szybki start wykonasz następujące kroki: 

1. Utworzenie przestrzeni nazw usługi Relay za pomocą witryny Azure Portal.
2. Utworzenie połączenia hybrydowego w tej przestrzeni nazw za pomocą witryny Azure Portal.
3. Napisanie aplikacji konsolowej serwera (odbiornika) służącej do odbierania komunikatów.
4. Napisanie aplikacji konsolowej klienta (nadawcy) służącej do wysyłania komunikatów.
5. Uruchamianie aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

- [Node.js](https://nodejs.org/en/).
- Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="create-a-namespace"></a>Tworzenie przestrzeni nazw
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Tworzenie połączenia hybrydowego
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Tworzenie aplikacji serwera (odbiornika)
Aby nasłuchiwać i odbierać komunikaty z usługi Relay, napisz aplikację konsolową Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Tworzenie aplikacji klienta (nadawcy)
Aby wysyłać komunikaty do usługi Relay, napisz aplikację konsolową Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>Uruchamianie aplikacji

1. Uruchom aplikację serwera: w wierszu polecenia Node.js wpisz `node listener.js`.
2. Uruchom aplikację klienta: w wiersza polecenia Node.js wpisz `node sender.js`, a następnie wpisz dowolny tekst.
3. Upewnij się, że w konsoli aplikacji serwera pojawia się tekst wprowadzony w aplikacji klienta.

    ![Okna konsoli przetestuje zarówno aplikacje serwera, jak i klienta.](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Gratulacje! Aplikacja typu end-to-end do obsługi połączeń hybrydowych korzystająca ze środowiska Node.js jest gotowa.

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono aplikacje klienta i serwera w środowisku Node.js służące do wysyłania i odbierania komunikatów za pomocą obiektów WebSocket. Funkcja połączeń hybrydowych usługi Azure Relay obsługuje również wysyłanie i odbieranie komunikatów przy użyciu protokołu HTTP. Aby dowiedzieć się, jak używać protokołu HTTP z funkcją połączeń hybrydowych usługi Azure Relay, zobacz [Przewodnik Szybki start dotyczący protokołu HTTP w środowisku Node.js](relay-hybrid-connections-http-requests-node-get-started.md).

W tym przewodniku Szybki start za pomocą środowiska Node.js utworzono aplikacje klienta i serwera. Aby dowiedzieć się, jak pisać aplikacje klienta i serwera na platformie .NET Framework, zobacz [Przewodnik Szybki start dotyczący obiektów WebSocket na platformie .NET](relay-hybrid-connections-dotnet-get-started.md) lub [Przewodnik Szybki start dotyczący protokołu HTTP na platformie .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md).


