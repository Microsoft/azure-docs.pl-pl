---
title: Szybki start — dołączanie do spotkania usługi Teams z aplikacji dla systemu iOS
description: Z tego samouczka dowiesz się, jak dołączyć do spotkania usługi Teams przy użyciu zestawu SDK Azure Communication Services Calling dla systemu iOS
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 363799cee5d66b718bb8ba06f4afd442add15148
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564521"
---
W tym przewodniku Szybki start dowiesz się, jak dołączyć do spotkania usługi Teams przy użyciu zestawu SDK Azure Communication Services Calling SDK dla systemu iOS.

## <a name="prerequisites"></a>Wymagania wstępne

- Robocza aplikacja [Communication Services wywoływania aplikacji systemu iOS.](../getting-started-with-calling.md)
- Wdrożenie [usługi Teams.](/deployoffice/teams-install)


## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>Dodawanie kontrolek interfejsu użytkownika aplikacji Teams i włączanie kontrolek interfejsu użytkownika aplikacji Teams

Zastąp kod w fragmencie ContentView.swift poniższym fragmentem kodu. Pole tekstowe zostanie użyte do wprowadzenia kontekstu spotkania w u użytkownika Teams, a przycisk zostanie użyty do dołączenia do określonego spotkania:

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

## <a name="get-the-teams-meeting-link"></a>Uzyskiwanie linku do spotkania z zespołem

Link do spotkania usługi Teams można pobrać przy użyciu interfejsów API programu Graph. Jest to szczegółowo opisane w [dokumentacji programu Graph.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)
Wywołanie Communication Services SDK akceptuje pełny link spotkania usługi Teams. Ten link jest zwracany jako część `onlineMeeting` zasobu, dostępny w ramach [ `joinWebUrl` właściwości](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Wymagane informacje o spotkaniu można również uzyskać z adresu **URL** dołączenia do spotkania w samym zaproszeniu do spotkania w aplikacji Teams.

## <a name="launch-the-app-and-join-teams-meeting"></a>Uruchamianie aplikacji i dołączanie do spotkania w aplikacji Teams

Aplikację można skompilować i uruchomić w symulatorze systemu iOS, wybierając pozycję **Uruchomienie** produktu lub używając  >   skrótu klawiaturowego (&#8984;-R).

:::image type="content" source="../media/ios/acs-join-teams-meeting-quickstart.png" alt-text="Zrzut ekranu przedstawiający ukończoną aplikację.":::

Wstaw kontekst aplikacji Teams w polu tekstowym i naciśnij pozycję *Dołącz* do spotkania zespołów, aby dołączyć do spotkania aplikacji Teams z poziomu Communication Services aplikacji.
