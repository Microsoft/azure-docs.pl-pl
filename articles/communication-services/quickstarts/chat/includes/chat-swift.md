---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 5bf4bbe2c8dc863f67dffb50609f7775a4499e3a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073671"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że:

- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Zainstaluj [Xcode](https://developer.apple.com/xcode/) i [CocoaPods](https://cocoapods.org/). Za pomocą Xcode można utworzyć aplikację dla systemu iOS dla przewodnika Szybki Start i CocoaPods, aby zainstalować zależności.
- Utwórz zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Szybki Start: Tworzenie zasobów usług komunikacyjnych i zarządzanie nimi](../../create-communication-resource.md). W tym przewodniku Szybki Start należy zarejestrować punkt końcowy zasobu.
- Utwórz dwóch użytkowników w usłudze Azure Communications Services i wystawiaj im [token dostępu użytkownika](../../access-tokens.md). Upewnij się, że ustawisz zakres na `chat` , i zanotuj ciąg, a `token` także ciąg `userId` . W tym przewodniku szybki start utworzysz wątek z początkowym uczestnikiem, a następnie dodasz drugiego uczestnika do wątku.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-ios-application"></a>Tworzenie nowej aplikacji dla systemu iOS

Otwórz Xcode i wybierz pozycję **Utwórz nowy projekt Xcode**. Następnie wybierz pozycję **iOS** jako platformę i **aplikację** dla szablonu.

W polu Nazwa projektu wprowadź **ChatQuickstart**. Następnie wybierz **scenorys** jako interfejs, **strukturze UIKit aplikację** jako cykl życia i **SWIFT** jako język.

Wybierz pozycję **dalej**, a następnie wybierz katalog, w którym ma zostać utworzony projekt.

### <a name="install-the-libraries"></a>Instalowanie bibliotek

Użyj CocoaPods, aby zainstalować wymagane zależności usług komunikacyjnych.

W wierszu polecenia przejdź do katalogu głównego `ChatQuickstart` projektu systemu iOS. Utwórz plik podfile za pomocą następującego polecenia: `pod init` .

Otwórz plik podfile i Dodaj następujące zależności do `ChatQuickstart` obiektu docelowego:

```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

Zainstaluj zależności przy użyciu następującego polecenia: `pod install` . Należy zauważyć, że spowoduje to również utworzenie obszaru roboczego Xcode.

Po uruchomieniu `pod install` ponownie otwórz projekt w Xcode, wybierając nowo utworzony `.xcworkspace` .

### <a name="set-up-the-placeholders"></a>Konfigurowanie symboli zastępczych

Otwórz obszar roboczy `ChatQuickstart.xcworkspace` w Xcode, a następnie otwórz `ViewController.swift` .

W tym przewodniku szybki start dowiesz się, jak dodać swój kod do `viewController` i wyświetlić dane wyjściowe w konsoli Xcode. Ten przewodnik Szybki Start nie dotyczy kompilowania interfejsu użytkownika w systemie iOS. 

W górnej części programu `viewController.swift` zaimportuj `AzureCommunication` `AzureCommunicatonChat` biblioteki i:

```
import AzureCommunication
import AzureCommunicationChat
```

Skopiuj następujący kod do `viewDidLoad()` metody `ViewController` :

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

Do celów demonstracyjnych będziemy synchronizować kod przy użyciu semafora. W poniższych krokach zastąpisz symbole zastępcze z przykładowym kodem przy użyciu biblioteki czatu usługi Azure Communication Services.


### <a name="create-a-chat-client"></a>Tworzenie klienta czatu

Zastąp komentarz `<CREATE A CHAT CLIENT>` następującym kodem:

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
    let credential =
    try CommunicationTokenCredential(
        token: "<ACCESS_TOKEN>"
    )
    let options = AzureCommunicationChatClientOptions()

    let chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: options
    )
```

Zamień `<ACS_RESOURCE_ENDPOINT>` na punkt końcowy zasobu usługi Azure Communications Services. Zamień `<ACCESS_TOKEN>` na prawidłowy token dostępu usług komunikacyjnych.

Ten przewodnik Szybki Start nie obejmuje tworzenia warstwy usług w celu zarządzania tokenami dla aplikacji czatu, ale jest to zalecane. Aby uzyskać więcej informacji, zobacz sekcję "architektura rozmowy" [koncepcji rozmowy](../../../concepts/chat/concepts.md).

Aby uzyskać więcej informacji o tokenach dostępu użytkowników, zobacz [Szybki Start: tworzenie tokenów dostępu i zarządzanie nimi](../../access-tokens.md).

## <a name="object-model"></a>Model obiektów 

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu Azure Communications Services Chat SDK dla języka JavaScript.

| Nazwa                                   | Opis                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | Ta klasa jest wymagana dla funkcji rozmowy. Tworzysz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do tworzenia, pobierania i usuwania wątków. |
| `ChatThreadClient` | Ta klasa jest wymagana dla funkcjonalności wątku rozmowy. Możesz uzyskać wystąpienie za pośrednictwem programu `ChatClient` i używać go do wysyłania, odbierania, aktualizowania i usuwania komunikatów. Można go również użyć do dodawania, usuwania i pobierania użytkowników, wysyłania powiadomień o wpisywaniu i potwierdzeń odczytu oraz subskrybowania zdarzeń rozmowy. |

## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Teraz możesz `ChatClient` utworzyć nowy wątek z użytkownikiem początkowym.

Zastąp komentarz `<CREATE A CHAT THREAD>` następującym kodem:

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Zamień `<USER_ID>` na prawidłowy identyfikator użytkownika usług komunikacyjnych.

Używasz semafora tutaj, aby poczekać na procedurę obsługi zakończenia przed kontynuowaniem. W kolejnych krokach użyjemy `threadId` od odpowiedzi zwróconej do procedury obsługi zakończenia.

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy

Teraz, po utworzeniu wątku czatu, można uzyskać `ChatThreadClient` wykonywanie operacji w ramach wątku.

Zastąp komentarz `<CREATE A CHAT THREAD CLIENT>` następującym kodem:

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>Wyślij wiadomość do wątku rozmowy

Zastąp komentarz `<SEND A MESSAGE>` następującym kodem:

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
)

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Najpierw należy skonstruować `SendChatMessageRequest` , która zawiera nazwę wyświetlaną zawartości i nadawcy. To żądanie może również zawierać czas historii udziału, jeśli chcesz go uwzględnić. Odpowiedź zwrócona do procedury obsługi zakończenia zawiera identyfikator wysyłanego komunikatu.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Dodawanie użytkownika jako uczestnika do wątku czatu

Zastąp komentarz `<ADD A USER>` następującym kodem:

```
let user = Participant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

Zastąp ciąg `<USER_ID>` identyfikatorem użytkownika usług komunikacyjnych, który ma zostać dodany.

Gdy dodajesz uczestnika do wątku, zwrócona odpowiedź może zawierać błędy. Te błędy reprezentują Niepowodzenie dodawania określonych uczestników.

## <a name="list-users-in-a-thread"></a>Wyświetlanie listy użytkowników w wątku

Zastąp komentarz `<LIST USERS>` następującym kodem:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print(user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>Usuwanie użytkownika z wątku rozmowy

Zastąp komentarz `<REMOVE A USER>` następującym kodem:

```
chatThreadClient
    .remove(
        participant: CommunicationUserIdentifier("<USER_ID>")
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

Zamień na `<USER ID>` Identyfikator użytkownika usług komunikacyjnych, który ma zostać usunięty przez uczestnika.

## <a name="run-the-code"></a>Uruchamianie kodu

W Xcode wybierz pozycję **Uruchom** , aby skompilować i uruchomić projekt. W konsoli programu można wyświetlić dane wyjściowe z kodu i wyjścia rejestratora z klienta czatu.

