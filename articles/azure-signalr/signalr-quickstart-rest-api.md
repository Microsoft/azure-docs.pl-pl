---
title: Przewodnik Szybki start — interfejs API REST usługi Azure SignalR Service
description: Dowiedz się, jak korzystać z interfejsu API REST w usłudze Azure Signal Service. Znajdź szczegóły specyfikacji interfejsu API REST.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 85cda8458dca4ce24fed9433af1527f49a2393ad
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150969"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Szybki start: tworzenie przekazów komunikatów w czasie rzeczywistym z poziomu aplikacji konsolowej

Usługa Azure SignalR Service udostępnia [interfejs API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) umożliwiający obsługę scenariuszy dotyczących komunikacji między klientem a serwerem, takich jak tworzenie przekazów. Możesz wybrać dowolny język programowania, który umożliwia wywoływanie interfejsu API REST. Możesz zamieszczać komunikaty do wszystkich połączonych klientów, konkretnych klientów według ich nazwy lub do grupy klientów.

W tym przewodniku Szybki start dowiesz się, jak wysyłać komunikaty z aplikacji wiesza polecenia do aplikacji połączonych klientów w języku C#.

## <a name="prerequisites"></a>Wymagania wstępne

Czynności przedstawione w tym przewodniku Szybki start można wykonywać w systemie macOS, Windows lub Linux.

* [Zestaw .NET Core SDK](https://www.microsoft.com/net/download/core)
* Wybrany edytor tekstu lub edytor kodu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsapi)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com/> przy użyciu danych konta Azure.

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsapi)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsapi)

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

W czasie, gdy usługa jest wdrażana, przejdźmy do przygotowania kodu. Sklonuj [przykładową aplikację z witryny GitHub](https://github.com/aspnet/AzureSignalR-samples.git), ustaw parametry połączenia usługi SignalR Service i uruchom aplikację lokalnie.

1. Otwórz okno terminala usługi Git. Przejdź do folderu, w którym chcesz sklonować przykładowy projekt.

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsapi)

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

W tym przykładzie zastosowano aplikację konsolową pokazującą użycie usługi Azure SignalR Service. Oferuje ona dwa tryby:

- Tryb serwera: wykorzystuje proste polecenia, aby wywołać interfejs API REST usługi Azure SignalR Service.
- Tryb klienta: nawiązuje połączenie z usługą Azure SignalR Service i otrzymuje komunikaty z serwera.

Możesz również dowiedzieć się, w jaki sposób wygenerować tokeny dostępu służące do uwierzytelniania w usłudze Azure SignalR Service.

### <a name="build-the-executable-file"></a>Tworzenie pliku wykonywalnego

Jako przykładu użyjemy systemu macOS osx.10.13-x64. Możesz znaleźć [informacje](/dotnet/core/rid-catalog), w jaki sposób utworzyć odpowiednią aplikację na innych platformach.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Uruchamianie klienta

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Uruchamianie serwera

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsapi)

## <a name="run-the-sample-without-publishing"></a>Uruchamianie aplikacji przykładowej bez publikowania

Możesz również uruchomić poniższe polecenie, aby uruchomić serwer lub klienta.

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Korzystanie z wpisów tajnych użytkowników w celu określenia parametrów połączenia

Możesz uruchomić polecenie `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` w katalogu głównym przykładu. Wtedy opcja `-c "<ConnectionString>"` nie jest już potrzebna.

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsapi)

## <a name="usage"></a>Użycie

Po uruchomieniu serwera użyj tego polecenia, aby wysłać komunikat:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Możesz uruchomić wielu klientów o wielu nazwach.

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsapi)

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> Integracja z usługami innych firm

Usługa Azure SignalR umożliwia usługom innych firm integrację z systemem.

### <a name="definition-of-technical-specifications"></a>Definicja specyfikacji technicznej

W poniższej tabeli przedstawiono wszystkie wersje interfejsów API REST obsługiwane do tej pory. Można tam także znaleźć plik definicji dla każdej konkretnej wersji.

Wersja | Interfejs API stanu | Drzwi | Określona wersja
--- | --- | --- | ---
`1.0-preview` | Dostępne | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Dostępne | Standardowa | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

Lista dostępnych interfejsów API dla każdej konkretnej wersji znajduje się na poniższej liście.

Interfejs API | 1,0 — wersja zapoznawcza | 1.0
--- | --- | ---
[Rozgłoś do wszystkich](#broadcast) | **&#x2713;** | **&#x2713;**
[Rozgłoś do grupy](#broadcast-group) | **&#x2713;** | **&#x2713;**
Rozgłoś do niektórych grup | **&#x2713;** (przestarzałe) | `N / A`
[Wyślij do użytkownika](#send-user) | **&#x2713;** | **&#x2713;**
Wyślij do niektórych użytkowników | **&#x2713;** (przestarzałe) | `N / A`
[Dodawanie użytkownika do grupy](#add-user-to-group) | `N / A` | **&#x2713;**
[Usuwanie użytkownika z grupy](#remove-user-from-group) | `N / A` | **&#x2713;**
[Sprawdzanie istnienia użytkownika](#check-user-existence) | `N / A` | **&#x2713;**
[Usuwanie użytkownika ze wszystkich grup](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[Wyślij do połączenia](#send-connection) | `N / A` | **&#x2713;**
[Dodawanie połączenia z grupą](#add-connection-to-group) | `N / A` | **&#x2713;**
[Usuwanie połączenia z grupy](#remove-connection-from-group) | `N / A` | **&#x2713;**
[Zamykanie połączenia klienta](#close-connection) | `N / A` | **&#x2713;**
[Kondycja usługi](#service-health) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Rozgłoś do wszystkich

Wersja | Metoda HTTP interfejsu API | Adres URL żądania | Treść żądania
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Jak wyżej

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Rozgłoś do grupy

Wersja | Metoda HTTP interfejsu API | Adres URL żądania | Treść żądania
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Jak wyżej

<a name="send-user"> </a>
### <a name="sending-to-a-user"></a>Wysyłanie do użytkownika

Wersja | Metoda HTTP interfejsu API | Adres URL żądania | Treść żądania
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Jak wyżej

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Dodaj użytkownika do grupy

Wersja | Metoda HTTP interfejsu API | Adres URL żądania
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Usuń użytkownika z grupy

Wersja | Metoda HTTP interfejsu API | Adres URL żądania
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>Sprawdzanie istnienia użytkownika w grupie

Wersja interfejsu API | Metoda HTTP interfejsu API | Adres URL żądania
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

Kod stanu odpowiedzi | Opis
---|---
`200` | Użytkownik istnieje
`404` | Użytkownik nie istnieje

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>Usuwanie użytkownika ze wszystkich grup

Wersja interfejsu API | Metoda HTTP interfejsu API | Adres URL żądania
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>Wyślij wiadomość do połączenia

Wersja interfejsu API | Metoda HTTP interfejsu API | Adres URL żądania | Treść żądania
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>Dodawanie połączenia z grupą

Wersja interfejsu API | Metoda HTTP interfejsu API | Adres URL żądania
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>Usuwanie połączenia z grupy

Wersja interfejsu API | Metoda HTTP interfejsu API | Adres URL żądania
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>Zamykanie połączenia klienta

Wersja interfejsu API | Metoda HTTP interfejsu API | Adres URL żądania
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>Service Health

Wersja interfejsu API | Metoda HTTP interfejsu API | Adres URL żądania
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

Kod stanu odpowiedzi | Opis
---|---
`200` | Dobre usługi
`5xx` | Błąd usługi

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsapi)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsapi)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób korzystania z interfejsu API REST w celu emitowania komunikatów w czasie rzeczywistym od usługi sygnalizującej do klientów. Następnie Dowiedz się więcej na temat tworzenia i wdrażania Azure Functions za pomocą powiązania usługi sygnalizującego, który jest oparty na interfejsie API REST.

> [!div class="nextstepaction"]
> [Opracowywanie Azure Functions przy użyciu powiązań usługi platformy Azure](signalr-quickstart-azure-functions-csharp.md)

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsapi)