---
title: Szybki Start — Dołącz do spotkania zespołów z poziomu aplikacji systemu iOS
description: W tym samouczku dowiesz się, jak przyłączyć się do spotkania zespołów przy użyciu biblioteki klienta wywołującego usługi Azure Communications Services dla systemu iOS
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 68f1abb5335386634874d9696738a8e552ebbe9c
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488009"
---
W tym przewodniku szybki start dowiesz się, jak przyłączyć się do spotkania zespołów przy użyciu biblioteki klienta wywołującego usługi Azure Communications Services dla systemu iOS.

## <a name="prerequisites"></a>Wymagania wstępne

- Działająca [usługa komunikacyjna wywołująca aplikację dla systemu iOS](../getting-started-with-calling.md).
- [Wdrożenie zespołów](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>Dodawanie formantów interfejsu użytkownika zespołów i włączanie formantów interfejsu użytkownika zespołów

Zastąp kod w ContentView. Swift następującym fragmentem kodu. Pole tekstowe zostanie użyte do wprowadzenia kontekstu spotkania zespołów, a przycisk zostanie użyty w celu dołączenia do określonego spotkania:

```swift

import SwiftUI
import AzureCommunicationCalling
import AVFoundation

struct ContentView: View {
    @State var meetingLink: String = ""
    @State var callStatus: String = ""
    @State var message: String = ""
    @State var recordingStatus: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var callObserver: CallObserver?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Teams meeting link", text: $meetingLink)
                    Button(action: joinTeamsMeeting) {
                        Text("Join Teams Meeting")
                    }.disabled(callAgent == nil)
                    Button(action: leaveMeeting) {
                        Text("Leave Meeting")
                    }.disabled(call == nil)
                    Text(callStatus)
                    Text(message)
                    Text(recordingStatus)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
            var userCredential: CommunicationTokenCredential?
            do {
                userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
            } catch {
                print("ERROR: It was not possible to create user credential.")
                self.message = "Please enter your token in source code"
                return
            }

            self.callClient = CallClient()

            // Creates the call agent
            self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
                if error == nil {
                    guard let agent = agent else {
                        self.message = "Failed to create CallAgent"
                        return
                    }

                    self.callAgent = agent
                    self.message = "Call agent successfully created."
                } else {
                    self.message = "Failed to create CallAgent with error"
                }
            }
        }
    }

    func joinTeamsMeeting() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                let joinCallOptions = JoinCallOptions()
                let teamsMeetingLinkLocator = TeamsMeetingLinkLocator(meetingLink: self.meetingLink);
                guard let call = self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) else {
                    self.message = "Failed to join Teams meeting"
                    return
                }

                self.call = call
                self.callObserver = CallObserver(self)
                self.call!.delegate = self.callObserver
                self.message = "Teams meeting joined successfully"
            }
        }
    }

    func leaveMeeting() {
        if let call = call {
            call.hangup(options: nil, completionHandler: { (error) in
                if error == nil {
                    self.message = "Leaving Teams meeting was successful"
                } else {
                    self.message = "Leaving Teams meeting failed"
                }
            })
        } else {
            self.message = "No active call to hanup"
        }
    }
}

class CallObserver : NSObject, CallDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func onCallStateChanged(_ call: Call!,
                                   args: PropertyChangedEventArgs!) {
        owner.callStatus = CallObserver.callStateToString(state: call.state)
        if call.state == .disconnected {
            owner.call = nil
            owner.message = "Left Meeting"
        } else if call.state == .inLobby {
            owner.message = "Waiting in lobby !!"
        } else if call.state == .connected {
            owner.message = "Joined Meeting !!"
        }
    }
    
    public func onIsRecordingActiveChanged(_ call: Call!, args: PropertyChangedEventArgs!) {
        if (call.isRecordingActive == true) {
            owner.recordingStatus = "This call is being recorded"
        }
        else {
            owner.recordingStatus = ""
        }
    }

    private static func callStateToString(state: CallState) -> String {
        switch state {
        case .connected: return "Connected"
        case .connecting: return "Connecting"
        case .disconnected: return "Disconnected"
        case .disconnecting: return "Disconnecting"
        case .earlyMedia: return "EarlyMedia"
        case .hold: return "Hold"
        case .incoming: return "Incoming"
        case .none: return "None"
        case .ringing: return "Ringing"
        case .inLobby: return "InLobby"
        default: return "Unknown"
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

## <a name="get-the-teams-meeting-link"></a>Pobierz link do spotkania dla zespołów

Link do spotkania zespołów można pobrać przy użyciu interfejsów API programu Graph. Jest to szczegółowo opisany w [dokumentacji programu Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).
Biblioteka klienta wywołującego usługę komunikacyjną akceptuje łącze do spotkania z pełnymi zespołami. Ten link jest zwracany jako część `onlineMeeting` zasobu, który jest dostępny w ramach [ `joinWebUrl` Właściwości](/graph/api/resources/onlinemeeting?view=graph-rest-beta). Wymagane informacje o spotkaniu można również uzyskać od adresu URL **spotkania przyłączenia** do zespołu, który zaprosił się w sobie.

## <a name="launch-the-app-and-join-teams-meeting"></a>Uruchom aplikację i Dołącz do spotkania zespołów

Możesz skompilować i uruchomić aplikację w symulatorze systemu iOS, wybierając opcję  >  **Uruchom** produkt lub używając skrótu klawiaturowego (&#8984;-R).

:::image type="content" source="../media/ios/acs-join-teams-meeting-quickstart.png" alt-text="Zrzut ekranu przedstawiający ukończoną aplikację.":::

Wstaw kontekst zespołów do pola tekstowego i naciśnij pozycję *Dołącz zespoły spotkanie* , aby dołączyć zespoły do spotkania z poziomu aplikacji usług komunikacyjnych.
