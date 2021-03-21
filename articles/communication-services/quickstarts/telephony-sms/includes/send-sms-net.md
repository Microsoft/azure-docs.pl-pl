---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: peiliu
manager: rejooyan
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 96cdeb7c35cd1ccd503f7ce01e1098a6b83884c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622016"
---
Rozpocznij pracę z usługami Azure Communications Services przy użyciu biblioteki klienta SMS usług komunikacyjnych w języku C# do wysyłania wiadomości SMS.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Najnowsza wersja [programu .NET Core Client Library](https://dotnet.microsoft.com/download/dotnet-core) dla danego systemu operacyjnego.
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- Numer telefonu z włączoną funkcją SMS. [Pobierz numer telefonu](../get-phone-number.md).

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

- W terminalu lub oknie poleceń Uruchom `dotnet` polecenie, aby sprawdzić, czy Biblioteka klienta .NET jest zainstalowana.
- Aby wyświetlić numery telefonów skojarzone z zasobem usług komunikacyjnych, zaloguj się do [Azure Portal](https://portal.azure.com/), Znajdź zasób usługi komunikacyjnej i Otwórz kartę **numery telefonów** w okienku nawigacji po lewej stronie.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `SmsQuickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: **program. cs**.

```console
dotnet new console -o SmsQuickstart
```

Zmień katalog na nowo utworzony folder aplikacji i Użyj `dotnet build` polecenia, aby skompilować aplikację.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu aplikacji, zainstaluj bibliotekę klienta programu SMS dla usług Azure Communication Services dla platformy .NET za pomocą `dotnet add package` polecenia.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.4
```

Dodaj `using` dyrektywę w górnej części **programu program. cs** , aby uwzględnić `Azure.Communication` przestrzeń nazw.

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta SMS usługi Azure Communication Services dla języka C#.

| Nazwa                                       | Opis                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Ta klasa jest wymagana dla wszystkich funkcji programu SMS. Utwórz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do wysyłania wiadomości SMS.                           |
| SmsSendResult               | Ta klasa zawiera wynik z usługi SMS.                                          |
| SmsSendOptions | Ta klasa zawiera opcje konfigurowania raportowania dostarczania. Jeśli enable_delivery_report ma wartość true, zdarzenie będzie emitowane po pomyślnym dostarczeniu |

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

 Otwórz **program. cs** w edytorze tekstów i Zastąp treść `Main` metody kodem, aby zainicjować `SmsClient` za pomocą parametrów połączenia. Poniższy kod pobiera parametry połączenia dla zasobu ze zmiennej środowiskowej o nazwie `COMMUNICATION_SERVICES_CONNECTION_STRING` . Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-a-11-sms-message"></a>Wyślij wiadomość SMS z systemem 1:1

Aby wysłać wiadomość SMS do pojedynczego adresata, wywołaj `Send` funkcję lub `SendAsync` z SmsClient. Dodaj ten kod na końcu `Main` metody w **programie program. cs**:

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>", // Your E.164 formatted from phone number used to send SMS
    to: "<to-phone-number>", // E.164 formatted recipient phone number
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
Należy zastąpić `<from-phone-number>` numerem telefonu z obsługą programu SMS skojarzonym z zasobem usługi komunikacyjnej oraz `<to-phone-number>` numerem telefonu, na który chcesz wysłać wiadomość.

## <a name="send-a-1n-sms-message-with-options"></a>Wyślij wiadomość SMS z opcją 1: N z opcjami
Aby wysłać wiadomość SMS do listy adresatów, wywołaj `Send` `SendAsync` funkcję lub z SmsClient z listą numerów telefonów adresatów. Możesz również przekazać parametry opcjonalne, aby określić, czy raport dostarczania powinien być włączony, i ustawić Tagi niestandardowe.

```csharp
Response<IEnumerable<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>", // Your E.164 formatted from phone number used to send SMS
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" }, // E.164 formatted recipient phone numbers
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

`enableDeliveryReport`Parametr jest opcjonalnym parametrem, którego można użyć w celu skonfigurowania raportowania dostarczania. Jest to przydatne w scenariuszach, w których chcesz emitować zdarzenia podczas dostarczania wiadomości SMS. Zapoznaj się z tematem [Obsługa zdarzeń programu SMS](../handle-sms-events.md) — Szybki Start, aby skonfigurować dostarczanie raportów dla wiadomości SMS.

## <a name="run-the-code"></a>Uruchamianie kodu

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```console
dotnet run
```

## <a name="sample-code"></a>Przykładowy kod

Możesz pobrać przykładową aplikację z usługi [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS)
