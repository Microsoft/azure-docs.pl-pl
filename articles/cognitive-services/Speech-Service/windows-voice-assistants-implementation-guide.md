---
title: Asystenci głosowi w wytycznych dotyczących implementacji blokady systemu Windows
titleSuffix: Azure Cognitive Services
description: Instrukcje dotyczące implementowania aktywacji głosu i nowszych funkcji dla aplikacji agenta głosowego.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: 92ab043d4fccbe0764e361eac6f71ef69a5963cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939853"
---
# <a name="implementing-voice-assistants-on-windows"></a>Implementowanie asystentów głosowych w systemie Windows

Ten przewodnik zawiera szczegółowe informacje dotyczące tworzenia asystenta głosowego w systemie Windows.

## <a name="implementing-voice-activation"></a>Implementowanie aktywacji głosu

Po [skonfigurowaniu środowiska](how-to-windows-voice-assistants-get-started.md) i dowiesz się, [jak działa Aktywacja głosowa](windows-voice-assistants-overview.md#how-does-voice-activation-work), możesz rozpocząć wdrażanie aktywacji głosowej dla własnej aplikacji asystenta głosowego.

### <a name="registration"></a>Rejestracja

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Upewnij się, że mikrofon jest dostępny i dostępny, a następnie monitoruj jego stan

Aby można było wykryć aktywację głosową, w MVA musi być obecny mikrofon. Użyj klas [AppCapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability), [DeviceWatcher](/uwp/api/windows.devices.enumeration.devicewatcher)i [MediaCapture](/uwp/api/windows.media.capture.mediacapture) , aby sprawdzić, czy są dostępne odpowiednio ustawienia ochrony prywatności mikrofonu, obecności urządzenia i stanu urządzenia (np. głośności i wyciszenie).

### <a name="register-the-application-with-the-background-service"></a>Rejestrowanie aplikacji w usłudze w tle

Aby można było uruchomić aplikację w tle przez MVA, aplikacja musi zostać zarejestrowana w usłudze w tle. Zobacz pełny Przewodnik [po rejestracji usługi](/windows/uwp/launch-resume/register-a-background-task)w tle.

### <a name="unlock-the-limited-access-feature"></a>Odblokuj funkcję ograniczonego dostępu

Aby odblokować funkcję asystenta głosowego, należy użyć klucza funkcji ograniczonego dostępu zapewnianego przez firmę Microsoft. Użyj klasy [LimitedAccessFeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures) z Windows SDK, aby to zrobić.

### <a name="register-the-keyword-for-the-application"></a>Zarejestruj słowo kluczowe dla aplikacji

Aplikacja musi zarejestrować się, jego model słów kluczowych i jego język w systemie Windows.

Zacznij od pobrania detektora słów kluczowych. W tym przykładowym kodzie pobieramy pierwszy detektor, ale można wybrać konkretny detektor, wybierając go z `configurableDetectors` .

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

Po pobraniu obiektu ActivationSignalDetector Wywołaj jego `ActivationSignalDetector.CreateConfigurationAsync` metodę z identyfikatorem sygnału, identyfikatorem modelu i nazwą wyświetlaną, aby zarejestrować słowo kluczowe i pobrać aplikację `ActivationSignalDetectionConfiguration` . Identyfikatory sygnałów i modeli powinny być identyfikatorami GUID, które są określane przez dewelopera i pozostają spójne dla tego samego słowa kluczowego.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Sprawdź, czy ustawienie aktywacji głosu jest włączone

Aby można było korzystać z aktywacji głosowej, użytkownik musi włączyć aktywację głosu dla swojego systemu i włączyć aktywację głosową dla swojej aplikacji. Ustawienie to można znaleźć w obszarze "Ustawienia prywatności aktywacji głosu" w ustawieniach systemu Windows. Aby sprawdzić stan ustawienia aktywacji głosu w aplikacji, użyj wystąpienia `ActivationSignalDetectionConfiguration` ze słowa kluczowego from. Pole [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) `ActivationSignalDetectionConfiguration` zawiera wartość wyliczenia opisującą stan ustawienia aktywacji głosowej.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>Pobierz ConversationalAgentSession, aby zarejestrować aplikację w systemie MVA

`ConversationalAgentSession`Jest klasą w Windows SDK, która umożliwia aplikacji aktualizowanie systemu Windows przy użyciu stanu aplikacji (bezczynne, wykrywanie, nasłuchiwanie, praca, mówienie) i odbieranie zdarzeń, takich jak wykrywanie aktywacji i zmiany stanu systemu, takie jak blokowanie ekranu. Pobieranie wystąpienia AgentSession służy również do rejestrowania aplikacji w systemie Windows jako aktywowalnej przez głos. Najlepszym rozwiązaniem jest zachowanie jednego odwołania do `ConversationalAgentSession` . Aby pobrać sesję, użyj `ConversationalAgentSession.GetCurrentSessionAsync` interfejsu API.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>Nasłuchiwanie dwóch sygnałów aktywacji: OnBackgroundActivated i OnSignalDetected

System Windows będzie sygnalizować aplikacji po wykryciu słowa kluczowego na jeden z dwóch sposobów. Jeśli aplikacja nie jest aktywna (oznacza to, że nie masz odwołania do nieusuniętego wystąpienia `ConversationalAgentSession` ), spowoduje to uruchomienie aplikacji i wywołanie metody OnBackgroundActivated w pliku App. XAML. cs aplikacji. Jeśli pole argumenty zdarzenia jest `BackgroundActivatedEventArgs.TaskInstance.Task.Name` zgodne z ciągiem "AgentBackgroundTrigger", uruchomienie aplikacji zostało wyzwolone przez aktywację głosową. Aplikacja musi przesłonić tę metodę i pobrać wystąpienie ConversationalAgentSession, aby sygnalizować system Windows, który jest teraz aktywny. Gdy aplikacja jest aktywna, system Windows będzie sygnalizować wystąpieniem aktywacji głosowej przy użyciu `ConversationalAgentSession.OnSignalDetected` zdarzenia. Dodaj obsługę zdarzeń do tego zdarzenia zaraz po pobraniu `ConversationalAgentSession` .

## <a name="keyword-verification"></a>Weryfikacja słowa kluczowego

Gdy aplikacja agenta głosowego zostanie aktywowana za pomocą głosu, następnym krokiem jest sprawdzenie, czy wykrycie słowa kluczowego było prawidłowe. System Windows nie udostępnia rozwiązania do weryfikacji słowa kluczowego, ale umożliwia asystentom głosowym dostęp do dźwięku z aktywacji Różnica średnich hipotetycznych i pełną weryfikację.

### <a name="retrieve-activation-audio"></a>Pobierz dźwięk aktywacji

Utwórz element [AudioGraph](/uwp/api/windows.media.audio.audiograph) i przekaż go do `CreateAudioDeviceInputNodeAsync` elementu `ConversationalAgentSession` . Spowoduje to załadowanie buforu audio wykresu przy użyciu dźwięku *rozpoczynającego się około 3 sekund przed wykryciem słowa kluczowego*. Ten dodatkowy dźwięk wiodący jest uwzględniany w celu dopasowania do szerokiego zakresu długości słów kluczowych i szybkości głośników. Następnie należy obsłużyć zdarzenie [QuantumStarted](/uwp/api/windows.media.audio.audiograph.quantumstarted) z grafu audio, aby pobrać dane audio.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Uwaga: wiodący dźwięk zawarty w buforze audio może spowodować niepowodzenie weryfikacji słowa kluczowego. Aby rozwiązać ten problem, Przytnij początek buforu audio przed wysłaniem dźwięku do weryfikacji słowa kluczowego. Ta początkowa przycinanie powinno być dostosowane do każdego asystenta.

### <a name="launch-in-the-foreground"></a>Uruchom na pierwszym planie

Po pomyślnym zakończeniu weryfikacji słowa kluczowego aplikacja musi zostać przeniesiona na pierwszy plan, aby wyświetlić interfejs użytkownika. Wywołaj `ConversationalAgentSession.RequestForegroundActivationAsync` interfejs API, aby przenieść aplikację na pierwszy plan.

### <a name="transition-from-compact-view-to-full-view"></a>Przejście z widoku zwartego do pełnego widoku

Gdy aplikacja jest uruchamiana po raz pierwszy, zostanie uruchomiona w widoku kompaktowym. Zapoznaj się z tematem [wskazówki dotyczące projektowania w wersji zapoznawczej aktywacji głosu](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) , aby uzyskać wskazówki dotyczące różnych widoków i przejść między nimi w przypadku asystentów głosowych w systemie Windows.

Aby przejść z widoku zwartego do pełnego widoku aplikacji, użyj interfejsu API ApplicationView `TryEnterViewModeAsync` :

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>Implementacja powyżej aktywacji blokady

Poniższe kroki obejmują wymagania umożliwiające włączenie asystenta głosowego w systemie Windows powyżej blokady, w tym odwołań do przykładowego kodu i wytycznych dotyczących zarządzania cyklem życia aplikacji.

Aby uzyskać wskazówki dotyczące projektowania powyżej środowiska blokady, zapoznaj się z [przewodnikiem najlepszymi rozwiązaniami](windows-voice-assistants-best-practices.md).

Gdy aplikacja pokazuje widok powyżej blokady, jest uznawana za w trybie kiosku. Aby uzyskać więcej informacji na temat implementowania aplikacji korzystającej z trybu kiosku, zobacz [dokumentację trybu kiosku](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access).

### <a name="transitioning-above-lock"></a>Przejście powyżej blokady

Aktywacja powyżej blokady jest podobna do aktywacji poniżej blokady. Jeśli nie ma aktywnych wystąpień aplikacji, nowe wystąpienie zostanie uruchomione w tle, a `OnBackgroundActivated` w pliku App. XAML. cs zostanie wywołana. Jeśli istnieje wystąpienie aplikacji, to wystąpienie otrzyma powiadomienie za pomocą `ConversationalAgentSession.SignalDetected` zdarzenia.

Jeśli aplikacja nie jest jeszcze wyświetlana powyżej blokady, musi wywołać `ConversationalAgentSession.RequestForegroundActivationAsync` . Powoduje to wyzwolenie `OnLaunched` metody w App. XAML. cs, która powinna przejść do widoku, który będzie wyświetlany powyżej blokady.

### <a name="detecting-lock-screen-transitions"></a>Wykrywanie przejść ekranu blokady

Biblioteka ConversationalAgent w Windows SDK udostępnia interfejs API umożliwiający łatwe dostęp do stanu ekranu blokady i zmian stanu ekranu blokady. Aby wykryć bieżący stan ekranu blokady, zaznacz `ConversationalAgentSession.IsUserAuthenticated` pole. Aby wykryć zmiany w stanie blokady, Dodaj procedurę obsługi zdarzeń do `ConversationalAgentSession` `SystemStateChanged` zdarzenia obiektu. Zostanie uruchomiony za każdym razem, gdy zmieni się ekran z odblokowany na zablokowany lub odwrotnie. Jeśli wartość argumentów zdarzenia to `ConversationalAgentSystemStateChangeType.UserAuthentication` , stan ekranu blokady został zmieniony.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>Wykrywanie powyżej preferencji użytkownika aktywacji blokady

Wpis aplikacji na stronie Ustawienia prywatności aktywacji głosowej ma przełącznik z wyższą funkcją blokady. Aby aplikacja mogła uruchomić się z poziomu blokady, użytkownik będzie musiał włączyć to ustawienie. Stan powyższych uprawnień blokady również jest przechowywany w obiekcie ActivationSignalDetectionConfiguration. Stan AvailabilityInfo. HasLockScreenPermission wskazuje, czy użytkownik udzielił powyższego uprawnienia do blokady. Jeśli to ustawienie jest wyłączone, aplikacja głosowa może monitować użytkownika o przechodzenie do odpowiedniej strony ustawień w linku "MS-Settings: privacy-voiceactivation" z instrukcjami dotyczącymi włączania aktywacji wyższego poziomu dla aplikacji.

## <a name="closing-the-application"></a>Zamykanie aplikacji

Aby program programowo zamknąć aplikację w czasie powyżej lub poniżej blokady, użyj `WindowService.CloseWindow()` interfejsu API. Powoduje to wyzwolenie wszystkich metod cyklu życia platformy UWP, w tym onsuspend, umożliwiając aplikacji usuwanie jej `ConversationalAgentSession` wystąpienia przed zamknięciem.

> [!NOTE]
> Aplikacja może zostać ZAMKNIĘTA bez zamykania [poniższego wystąpienia blokady](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-). W takim przypadku powyższym widok blokady musi być "oczyszczanie", upewniając się, że po odblokowaniu ekranu nie ma obsługi zdarzeń ani zadań, które próbują manipulować powyższym widokiem blokady.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwiedź przykładową aplikację asystenta głosowego platformy UWP, aby zapoznać się z przykładami i przechodzeniami kodu](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
