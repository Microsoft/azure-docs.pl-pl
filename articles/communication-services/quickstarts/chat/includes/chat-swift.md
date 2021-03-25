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
ms.openlocfilehash: ddc43d9a030b85f8a1becd4be2573303207ad345
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107077"
---
## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że:

- Utwórz konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Zainstaluj [Xcode](https://developer.apple.com/xcode/) i [Cocoapods](https://cocoapods.org/), będziemy używać usługi Xcode do tworzenia aplikacji dla systemu iOS dla przewodnika Szybki Start i Cocoapods, aby zainstalować zależności.
- Utwórz zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../../create-communication-resource.md). Musisz **zarejestrować punkt końcowy zasobu** dla tego przewodnika Szybki Start.
- Utwórz **dwóch** użytkowników usługi ACS i wystawiaj im [token dostępu użytkownika](../../access-tokens.md)tokenu dostępu użytkownika. Pamiętaj, aby ustawić zakres do **rozmowy** i **zanotować ciąg tokenu oraz ciąg identyfikatora użytkownika**. W tym przewodniku szybki start utworzymy wątek z początkowym uczestnikiem, a następnie dodasz drugiego uczestnika do wątku.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-ios-application"></a>Tworzenie nowej aplikacji dla systemu iOS

Otwórz Xcode i wybierz opcję `Create a new Xcode project` .

W następnym oknie Wybierz `iOS` jako platformę i `App` dla szablonu.

W przypadku wybrania opcji wprowadź `ChatQuickstart` nazwę projektu. Wybierz `Storyboard` jako interfejs jako `UIKit App Delegate` cykl życia i `Swift` jako język.

Kliknij przycisk Dalej i wybierz katalog, w którym ma zostać utworzony projekt.

### <a name="install-the-libraries"></a>Instalowanie bibliotek

Będziemy używać Cocoapods, aby zainstalować wymagane zależności usług komunikacyjnych.

W wierszu polecenia przejdź do katalogu głównego `ChatQuickstart` projektu systemu iOS.

Utwórz plik podfile: `pod init`

Otwórz plik podfile i Dodaj następujące zależności do `ChatQuickstart` obiektu docelowego:
```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

Zainstaluj zależności, co spowoduje również utworzenie obszaru roboczego Xcode: `pod install`

**Po zainstalowaniu programu Otwórz projekt ponownie w Xcode, wybierając nowo utworzony `.xcworkspace` .**

### <a name="setup-the-placeholders"></a>Skonfiguruj symbole zastępcze

Otwórz obszar roboczy `ChatQuickstart.xcworkspace` w Xcode, a następnie otwórz `ViewController.swift` .

W tym przewodniku szybki start dodamy nasz kod do i zobaczysz `viewController` dane wyjściowe w konsoli Xcode. Ten przewodnik Szybki Start nie rozsłuży do tworzenia interfejsu użytkownika w systemie iOS. 

W górnej części `viewController.swift` Importuj `AzureCommunication` `AzureCommunicatonChat` biblioteki i:

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

Będziemy używać semafora do synchronizowania kodu w celach demonstracyjnych. W poniższych krokach zamienimy symbole zastępcze z przykładowym kodem przy użyciu biblioteki czatu usługi Azure Communication Services.


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

Zamień `<ACS_RESOURCE_ENDPOINT>` na punkt końcowy zasobu ACS.
Zamień `<ACCESS_TOKEN>` na prawidłowy token dostępu ACS.

Ten przewodnik Szybki Start nie obejmuje tworzenia warstwy usług w celu zarządzania tokenami dla aplikacji czatu, chociaż jest to zalecane. Więcej szczegółowych informacji o [architekturze rozmowy](../../../concepts/chat/concepts.md) można znaleźć w następującej dokumentacji

Dowiedz się więcej o [tokenach dostępu użytkowników](../../access-tokens.md).

## <a name="object-model"></a>Model obiektów 
Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu Azure Communications Services Chat SDK dla języka JavaScript.

| Nazwa                                   | Opis                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Ta klasa jest wymagana dla funkcji rozmowy. Tworzysz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do tworzenia, pobierania i usuwania wątków. |
| ChatThreadClient | Ta klasa jest wymagana dla funkcjonalności wątku rozmowy. Możesz uzyskać wystąpienie za pośrednictwem ChatClientu i używać go do wysyłania/odbierania/aktualizowania/usuwania komunikatów, dodawania/usuwania/pobierania użytkowników, wysyłania powiadomień o wpisywaniu i odczytywania, subskrybowania zdarzeń rozmowy. |

## <a name="start-a-chat-thread"></a>Rozpocznij wątek rozmowy

Teraz będziemy używać naszego `ChatClient` do utworzenia nowego wątku z użytkownikiem początkowym.

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

Używamy semafora tutaj, aby poczekać na procedurę obsługi zakończenia przed kontynuowaniem. Użyjemy `threadId` od odpowiedzi zwróconej do procedury obsługi zakończenia w dalszych krokach.

## <a name="get-a-chat-thread-client"></a>Pobierz klienta wątku rozmowy

Teraz, gdy utworzyliśmy wątek rozmowy, uzyskamy `ChatThreadClient` do wykonania operacji w wątku.

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

Najpierw tworzymy, `SendChatMessageRequest` która zawiera nazwę wyświetlaną zawartości i nadawców (opcjonalnie także może zawierać czas historii udziału). Odpowiedź zwrócona do procedury obsługi zakończenia zawiera identyfikator wysyłanego komunikatu.

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

Zamień na `<USER_ID>` Identyfikator użytkownika usługi ACS, który ma zostać dodany.

Podczas dodawania uczestnika do wątku odpowiedź zwrócona przez zakończenie może zawierać błędy. Te błędy reprezentują Niepowodzenie dodawania określonych uczestników.

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

W Xcode naciśnij przycisk Run (Uruchom), aby skompilować i uruchomić projekt. W konsoli programu można wyświetlić dane wyjściowe z kodu i wyjścia rejestratora z ChatClient.


