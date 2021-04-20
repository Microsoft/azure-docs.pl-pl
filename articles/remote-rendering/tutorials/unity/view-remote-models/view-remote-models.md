---
title: Wyświetlanie zdalnie renderowanego modelu
description: Samouczek "Hello world" Azure Remote Rendering, w jaki sposób wyświetlić model renderowany zdalnie przez platformę Azure
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: f43e5b77580b7071ce48b39190c26a53f99f8cf5
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740164"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Samouczek: wyświetlanie zdalnie renderowanego modelu

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Aprowizuj Azure Remote Rendering (ARR)
> * Tworzenie i zatrzymywanie sesji renderowania
> * Ponowne używanie istniejącej sesji renderowania
> * Nawiązywanie połączenia i rozłączanie z sesjami
> * Ładowanie modeli do sesji renderowania

## <a name="prerequisites"></a>Wymagania wstępne

Do pracy z tym samouczkiem potrzebne są:

* Aktywna subskrypcja platformy Azure z płatnością zgodnie z platformą Azure [Tworzenie konta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(pobierz)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Najnowsza wersja programu Visual Studio 2019 [(pobierz)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(pobierz)](https://git-scm.com/downloads)
* Unity (zobacz [wymagania systemowe dla](../../../overview/system-requirements.md#unity) obsługiwanych wersji)
* Pośrednia znajomość aparatu Unity i języka C# (na przykład: tworzenie skryptów i obiektów, używanie prefabryków, konfigurowanie zdarzeń aparatu Unity itp.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Aprowizuj Azure Remote Rendering (ARR)

Aby uzyskać dostęp do usługi Azure Remote Rendering, najpierw musisz [utworzyć konto](../../../how-tos/create-an-account.md#create-an-account).

## <a name="create-a-new-unity-project"></a>Tworzenie nowego projektu aparatu Unity

> [!TIP]
> Repozytorium [przykładów ARR](https://github.com/Azure/azure-remote-rendering) zawiera projekt z ukończonym wszystkimi samouczkami. Można go użyć jako odwołania. Poszukaj kompletnego projektu aparatu Unity w folderze *Unity\Tutorial-Complete.*

W centrum unity utwórz nowy projekt.
W tym przykładzie przyjęto założenie, że projekt jest tworzony w folderze o nazwie **RemoteRendering**.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Nowy projekt aparatu Unity":::

## <a name="include-the-azure-remote-rendering-package"></a>Dołącz pakiet Azure Remote Rendering aplikacji

[Postępuj zgodnie z instrukcjami](../../../how-tos/unity/install-remote-rendering-unity-package.md) dotyczącymi dodawania pakietu Azure Remote Rendering do projektu aparatu Unity.


## <a name="configure-the-camera"></a>Konfigurowanie aparatu fotograficznego

1. Wybierz węzeł **Kamera główna.**

1. Otwórz menu kontekstowe, klikając  prawym przyciskiem myszy składnik Przekształć i wybierz **opcję Resetuj:**

    ![resetowanie przekształcenia aparatu](./media/camera-reset-transform.png)

1. Ustaw **flagi Clear na** solid color *(Pełny kolor)*

1. Ustaw **tło na** *czarny* (#000000) z w pełni przezroczystym (0) alfa (A)

    ![Koło kolorów](./media/color-wheel-black.png)

1. Ustaw **płaszczyzny przycinania na** *wartość Near = 0,3* i *Far = 20*. Oznacza to, że renderowanie spowoduje obcięcie geometrii, która jest bliżej niż 30 cm lub dalej niż 20 metrów.

    ![Właściwości aparatu Unity](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Dostosowywanie ustawień projektu

1. Otwórz *pozycję Edytuj > ustawienia projektu...*
1. Wybierz **pozycję Jakość** z menu listy po lewej stronie
1. Zmień domyślny **poziom jakości dla** wszystkich platform na *Niski.* To ustawienie umożliwia bardziej wydajne renderowanie zawartości lokalnej i nie wpływa na jakość zdalnie renderowanych zawartości.

    ![zmienianie ustawień jakości projektu](./media/settings-quality.png)

1. Wybieranie **pozycji Grafika** z menu listy po lewej stronie
1. Zmień ustawienie **Potok renderowania skryptowego** na *HybridRenderingPipeline*.\
    ![Zrzut ekranu przedstawiający miejsce zmiany ustawienia Potok renderowania skryptowego na HybridRenderingPipeline.](./media/settings-graphics-render-pipeline.png)\
    Czasami interfejs użytkownika nie wypełnia listy dostępnych typów potoków z pakietów. W takim przypadku należy ręcznie przeciągnąć zasób *HybridRenderingPipeline* do pola:\
    ![zmienianie ustawień grafiki projektu](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Jeśli nie możesz przeciągnąć i upuścić zasobu *HybridRenderingPipeline* w polu Zasób potoku renderowania (prawdopodobnie dlatego, że pole nie istnieje!), upewnij się, że konfiguracja pakietu zawiera `com.unity.render-pipelines.universal` pakiet.

1. Wybierz **pozycję Player** (Odtwarzacz) z menu listy po lewej stronie
1. Wybierz **kartę platforma uniwersalna systemu Windows ustawień** systemu Windows reprezentowaną jako ikona systemu Windows.
1. Zmień ustawienia **XR, aby** obsługiwać Windows Mixed Reality, jak pokazano poniżej:
    1. Włączanie **obsługi rzeczywistości wirtualnej**
    1. Naciśnij przycisk "+" i **dodaj** Windows Mixed Reality
    1. Ustaw **format głębokości** na głębokość *16-bitową*
    1. Upewnij **się, że włączono udostępnianie buforu** głębokości
    1. Ustaw **tryb renderowania stereo na** wystąpienie pojedynczego *przebiegu*

    ![ustawienia odtwarzacza](./media/xr-player-settings.png)

1. W tym samym oknie powyżej okna **Ustawienia XR** rozwiń pozycję **Ustawienia publikowania**
1. Przewiń w dół **do opcji Możliwości** i wybierz:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** *(opcjonalnie*). Wybierz tę opcję, jeśli chcesz połączyć debuger zdalny aparatu Unity z urządzeniem.

1. W **obszarze Obsługiwane rodziny urządzeń** włącz urządzenia **Holographic** i **Desktop**
1. Zamykanie lub **dokowanie panelu Project Settings (Ustawienia** projektu)
1. Otwieranie *ustawień kompilacji >plików*
1. Wybierz **platforma uniwersalna systemu Windows**
1. Skonfiguruj ustawienia tak, aby były zgodne z poniższymi ustawieniami
1. Naciśnij przycisk **Switch Platform(Przełącz** platformę).\
![ustawienia kompilacji](./media/build-settings.png)
1. Gdy unity zmieni platformy, zamknij panel kompilacji.

## <a name="validate-project-setup"></a>Weryfikowanie konfiguracji projektu

Wykonaj poniższe kroki, aby sprawdzić, czy ustawienia projektu są poprawne.

1. Wybierz wpis **ValidateProject z** menu **RemoteRendering** na pasku narzędzi edytora aparatu Unity.
1. Sprawdź, **czy w oknie ValidateProject występują** błędy, i w razie potrzeby napraw ustawienia projektu.

    ![Walidacja projektu edytora aparatu Unity](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Tworzenie skryptu w celu koordynowania Azure Remote Rendering i stanu

Istnieją cztery podstawowe etapy, które pokazują zdalnie renderowane modele, opisane na poniższym schematze blokowego. Każdy etap musi być wykonywany w kolejności. Następnym krokiem jest utworzenie skryptu, który będzie zarządzać stanem aplikacji i przechodzić przez każdy wymagany etap.

![Stos ARR 0](./media/remote-render-stack-0.png)

1. W *okienku Projekt* w obszarze **Zasoby** utwórz nowy folder o nazwie *RemoteRenderingCore.* Następnie wewnątrz *funkcji RemoteRenderingCore* utwórz kolejny folder o nazwie Scripts ( *Skrypty).*

1. Utwórz nowy [skrypt języka C#](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) **o nazwie RemoteRenderingCoordinator.**
Projekt powinien wyglądać tak:

    ![Hierarchia projektu](./media/project-structure.png)

    Ten skrypt koordynatora będzie śledzić stan renderowania zdalnego i zarządzać tym stanem. Należy pamiętać, że niektóre z tych kodów służą do utrzymywania stanu, uwsłaniania funkcjonalności innym  składnikom, wyzwalania zdarzeń i przechowywania danych specyficznych dla aplikacji, które nie są bezpośrednio związane z Azure Remote Rendering. Użyj poniższego kodu jako punktu wyjścia, a w dalszej części tego samouczka zaim <7>mujemy i zaim implementujemy Azure Remote Rendering kodu.

1. Otwórz **remoteRenderingCoordinator** w edytorze kodu i zastąp całą jego zawartość poniższym kodem:

```cs
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // Account
    // RemoteRenderingDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // For most people '<region>' is either 'westus2' or 'westeurope'
    [SerializeField]
    private string remoteRenderingDomain = "westus2.mixedreality.azure.com";
    public string RemoteRenderingDomain
    {
        get => remoteRenderingDomain.Trim();
        set => remoteRenderingDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountDomain = "<enter your account domain here>";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }    

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<SessionConfiguration> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.LogFormat(LogType.Log, LogOption.NoStacktrace, null, "{0}", $"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static RenderingSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<SessionConfiguration> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new SessionConfiguration(AccountDomain, RemoteRenderingDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        bool sessionAvailable = false;
        try
        {
            RenderingSessionPropertiesArrayResult result = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
            if (result.ErrorCode == Result.Success)
            {
                RenderingSessionProperties[] properties = result.SessionProperties;
                if (properties != null)
                {
                    sessionAvailable = properties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
                }
            }
            else
            {
                Debug.LogError($"Failed to get current rendering sessions. Error: {result.Context.ErrorMessage}");
            }
        }
        catch (RRException ex)
        {
            Debug.LogError($"Failed to get current rendering sessions. Error: {ex.Message}");
        }
        return sessionAvailable;
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Connection?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, RenderingSession session)
    {
        var properties = await session.GetPropertiesAsync();

        switch (properties.SessionProperties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>Tworzenie Azure Remote Rendering GameObject

Koordynator zdalnego renderowania i jego wymagany skrypt *(ARRServiceUnity)* są zarówno koordynatorem MonoBehaviour, które muszą być dołączone do projektu GameObject w scenie. Skrypt *ARRServiceUnity* jest dostarczany przez usługę ARR, aby uwidocznić większość funkcji ARR do łączenia się z sesjami zdalnymi i zarządzania nimi.

1. Utwórz nowy projekt GameObject w scenie (Ctrl+Shift+N lub *GameObject->Create Empty*) i nadaj jej nazwę **RemoteRenderingCoordinator.**
1. Dodaj skrypt *RemoteRenderingCoordinator* do **skryptu RemoteRenderingCoordinator** GameObject.\
![Dodawanie składnika RemoteRenderingCoordinator](./media/add-coordinator-script.png)
1. Upewnij *się, że skrypt ARRServiceUnity* wyświetlany jako *Usługa* w inspektorze jest automatycznie dodawany do gameObject. Jeśli się zastanawiasz, jest to wynik w górnej części skryptu `[RequireComponent(typeof(ARRServiceUnity))]` **RemoteRenderingCoordinator.**
1. Dodaj poświadczenia Azure Remote Rendering, domenę konta i domenę Remote Rendering do skryptu koordynatora:\
![Dodawanie poświadczeń](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Inicjowanie Azure Remote Rendering

Teraz, gdy mamy już platformę dla naszego koordynatora, wdrożymy każdy z czterech etapów, rozpoczynając od Remote Rendering **.**

![Stos ARR 1](./media/remote-render-stack-1.png)

**Inicjuj** informuje Azure Remote Rendering obiekt aparatu do renderowania i przekieruje maszynę stanu do stanu **NotAuthorized.** Oznacza to, że został zainicjowany, ale nie ma jeszcze autoryzacji do nawiązywania połączenia z sesją. Ponieważ uruchomienie sesji ARR wiąże się z kosztami, musimy potwierdzić, że użytkownik chce kontynuować.

Podczas wprowadzania stanu **NotAuthorized** jest wywoływana funkcja **CheckAuthorization,** która wywołuje zdarzenie **RequestingAuthorization** i określa poświadczenia konta do użycia **(AccountInfo** jest definiowana w górnej części klasy i używa poświadczeń zdefiniowanych za pośrednictwem inspektora aparatu Unity w powyższym kroku).

   > [!NOTE]
   > Ponowne kompilowanie środowiska uruchomieniowego nie jest obsługiwane przez ARR. Zmodyfikowanie skryptu i zapisanie go, gdy tryb odtwarzania jest aktywny, może spowodować zamarzanie aparatu Unity i konieczność wymusnięcia zamknięcia za pośrednictwem menedżera zadań. Przed rozpoczęciem edytowania skryptów zawsze upewnij się, że tryb odtwarzania został zatrzymany.

1. Zastąp zawartość plików **InitializeARR** i **InitializeSessionService** poniższym ukończonym kodem:

 ```cs
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

Aby przejść z **notauthorized** do **NoSession,** zwykle prezentowaliśmy modalne okno dialogowe użytkownikowi, aby mógł wybrać (i zrobimy to w innym rozdziale). Na razie będziemy automatycznie pomijać sprawdzanie autoryzacji przez wywołanie funkcji **ByPassAuthentication** zaraz po wyzwoleniu zdarzenia **RequestingAuthorization.**

1. Wybierz **element GameObject RemoteRenderingCoordinator** i znajdź zdarzenie **OnRequestingAuthorization aparatu** Unity ujawnione w inspektorze składnika **RemoteRenderingCoordinator.**

1. Dodaj nowe zdarzenie, naciskając klawisz "+" w prawym dolnym rogu.
1. Przeciągnij składnik na element do własnego zdarzenia, aby odwołać się do samego siebie.\
![Pomijanie uwierzytelniania](./media/bypass-authorization-add-event.png)\
1. Z listy rozwijanej wybierz **pozycję RemoteRenderingCoordinator -> BypassAuthorization**.\
![Zrzut ekranu przedstawiający wybraną opcję RemoteRenderingCoordinator.BypassAuthorization.](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Tworzenie lub dołączanie sesji zdalnej

Drugim etapem jest utworzenie lub dołączenie do sesji Remote Rendering (zobacz [Remote Rendering,](../../../concepts/sessions.md) aby uzyskać więcej informacji).

![Stos ARR 2](./media/remote-render-stack-2.png)

Sesja zdalna to miejsce, w którym będą renderowane modele. Metoda **JoinRemoteSession( )** podejmie próbę dołączenia do istniejącej sesji śledzone przy użyciu właściwości **LastUsedSessionID** lub jeśli istnieje przypisany identyfikator aktywnej sesji w metodzie **SessionIDOverride.** **SessionIDOverride** jest przeznaczony tylko do celów debugowania. Powinien być używany tylko wtedy, gdy wiadomo, że sesja istnieje i chcesz jawnie nawiązać z nim połączenie.

Jeśli sesje nie są dostępne, zostanie utworzona nowa sesja. Tworzenie nowej sesji jest jednak czasochłonne. W związku z tym należy próbować tworzyć sesje tylko wtedy, gdy jest to wymagane, i używać ich ponownie zawsze, gdy jest to możliwe (zobacz [Commercial Ready: Session pooling, scheduling, and best practices](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) (Komercyjne gotowe: pule sesji, planowanie i najlepsze rozwiązania), aby uzyskać więcej informacji na temat zarządzania sesjami).

> [!TIP]
> **StopRemoteSession()** spowoduje zakończenie aktywnej sesji. Aby uniknąć niepotrzebnych opłat, zawsze należy zatrzymać sesje, gdy nie są już potrzebne.

Maszyna stanu będzie teraz przejść do **connectingToNewRemoteSession** lub **ConnectingToExistingRemoteSession,** w zależności od dostępnych sesji. Zarówno otwarcie istniejącej sesji, jak i utworzenie nowej sesji spowoduje wyzwolenie zdarzenia **ARRSessionService.OnSessionStatusChanged,** wykonując naszą metodę **OnRemoteSessionStatusChanged.** W idealnym przypadku spowoduje to przejście maszyny stanu do **RemoteSessionReady.**

1. Aby dołączyć do nowej sesji, zmodyfikuj kod, aby zastąpić metody **JoinRemoteSession( )** i **StopRemoteSession( ) poniższymi** ukończonymi przykładami:

```cs
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationOptions(renderingSessionVmSize, (int)maxLeaseHours, (int)maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

Jeśli chcesz zaoszczędzić czas, ponownie korzystając z sesji, pamiętaj, aby dezaktywować opcję **Automatycznie** zatrzymaj sesję w *składniku ARRServiceUnity.* Należy pamiętać, że spowoduje to pozostawienie uruchomionych sesji, nawet jeśli nikt nie jest z nimi połączony. Sesja może być uruchamiana przez okres *MaxLeaseTime* przed jej zamknięciem przez serwer (wartość *MaxLeaseTime* można zmodyfikować w aplikacji Remote Rendering Coordinator w obszarze Nowe wartości *domyślne sesji).* Z drugiej strony, jeśli automatycznie zamkniesz każdą sesję podczas rozłączania, musisz zaczekać na za każdym razem, aż nowa sesja zostanie uruchomiona, co może być dość długotrwałym procesem.

> [!NOTE]
> Zatrzymanie sesji będzie obowiązywać natychmiast i nie można jej cofnąć. Po zatrzymaniu należy utworzyć nową sesję z tym samym obciążeniem uruchomienia.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>Łączenie lokalnego środowiska uruchomieniowego z sesją zdalną

Następnie aplikacja musi połączyć swoje lokalne środowisko uruchomieniowe z sesją zdalną.

![Stos ARR 3](./media/remote-render-stack-3.png)

Aplikacja musi również nasłuchiwać zdarzeń dotyczących połączenia między środowiskiem uruchomieniowym a bieżącą sesją; Te zmiany stanu są obsługiwane w **onLocalRuntimeStatusChanged**. Ten kod spowoduje postęp naszego stanu do **connectingToRuntime**. Po na połączeniu w **lokalizacji OnLocalRuntimeStatusChanged** stan zmieni się na **RuntimeConnected**. Nawiązywanie połączenia ze środowiskiem uruchomieniowym jest ostatnim stanem, którego dotyczy problem koordynatora, co oznacza, że aplikacja jest wykonywana przy użyciu całej typowej konfiguracji i jest gotowa do rozpoczęcia pracy związanej z sesją ładowania i renderowania modeli.

 1. Zastąp metody **ConnectRuntimeToRemoteSession( )** i **DisconnectRuntimeFromRemoteSession( ) ukończonymi** wersjami poniżej.
 1. Ważne jest, aby zanotować metodę aparatu Unity **LateUpdate** i zaktualizować bieżącą aktywną sesję. Dzięki temu bieżąca sesja może wysyłać/odbierać komunikaty i aktualizować bufor ramek przy użyciu ramek odebranych z sesji zdalnej. Ma to kluczowe znaczenie dla prawidłowego działania ARR.

```cs
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectAsync(new RendererInitOptions());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.Disconnect();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Connection?.Update();
}
```

> [!NOTE]
> Łączenie lokalnego środowiska uruchomieniowego z sesją zdalną zależy od **wywoływania** aktualizacji w aktualnie aktywnej sesji. Jeśli znajdziesz, że twoja aplikacja nigdy nie przekroczy stanu **ConnectingToRuntime,** upewnij się, że regularnie wywołując usługę **Update** w aktywnej sesji.

## <a name="load-a-model"></a>Ładowanie modelu

Mając już wymagane podstawy, możesz załadować model do sesji zdalnej i rozpocząć odbieranie ramek.

![Diagram przedstawiający przepływ procesu przygotowania do załadowania i wyświetlenia modelu.](./media/remote-render-stack-4.png)

Metoda **LoadModel** jest przeznaczona do akceptowania ścieżki modelu, procedury obsługi postępu i przekształcenia nadrzędnego. Te argumenty będą używane do ładowania modelu do sesji zdalnej, aktualizowania użytkownika na postęp ładowania i orientacji zdalnie renderowanego modelu na podstawie transformacji nadrzędnej.

1. Zastąp metodę **LoadModel** całkowicie poniższym kodem:

    ```cs
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Connection.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

Powyższy kod wykonuje następujące czynności:

1. Utwórz [jednostkę zdalną](../../../concepts/entities.md).
1. Utwórz lokalny obiekt GameObject reprezentujący jednostkę zdalną.
1. Skonfiguruj lokalny obiekt GameObject, aby zsynchronizować jego stan (tj. Przekształć) z jednostką zdalną w każdej ramce.
1. Ustaw nazwę i dodaj wartość [**WorldAnchor,**](https://docs.unity3d.com/550/Documentation/ScriptReference/VR.WSA.WorldAnchor.html) aby pomóc w ustabilizowaniu.
1. Ładowanie danych modelu z Blob Storage do jednostki zdalnej.
1. Zwróć jednostkę nadrzędną do późniejszego odwołania.

## <a name="view-the-test-model"></a>Wyświetlanie modelu testowego

Mamy teraz cały kod wymagany do wyświetlenia zdalnie renderowanego modelu. Wszystkie cztery z wymaganych etapów renderowania zdalnego zostały ukończone. Teraz musimy dodać nieco kodu, aby rozpocząć proces ładowania modelu.

![Stos ARR 4](./media/remote-render-stack-5.png)

1. Dodaj następujący kod do klasy **RemoteRenderingCoordinator,** tuż poniżej **metody LoadModel:**

    ```cs
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    Ten kod tworzy obiekt GameObject, który będzie działać jako element nadrzędny modelu testowego. Następnie wywołuje metodę **LoadModel,** aby załadować model "builtin://Engine", który jest elementem zawartości wbudowanym w Azure Remote Rendering na potrzeby testowania renderowania.

1. Zapisz kod.
1. Naciśnij przycisk Odtegoń w edytorze aparatu Unity, aby rozpocząć proces nawiązywania połączenia Azure Remote Rendering i tworzenia nowej sesji.
1. W widoku Game (Gra) nie będzie zbyt wiele, jednak w konsoli będzie pokazywany stan aplikacji, który się zmienia. Prawdopodobnie będzie to postęp do `ConnectingToNewRemoteSession` , i pozostanie tam, prawdopodobnie przez maksymalnie pięć minut.
1. Wybierz pozycję **RemoteRenderingCoordinator** GameObject, aby wyświetlić dołączone skrypty w inspektorze. Obserwuj **aktualizację** składnika usługi w trakcie procesu inicjowania i kroków połączenia.
1. Monitoruj dane wyjściowe konsoli — oczekiwanie na zmianę stanu na **RuntimeConnected**.
1. Po na połączeniu środowiska uruchomieniowego kliknij prawym przyciskiem myszy element **RemoteRenderingCoordinator** w inspektorze, aby uwidocznić menu kontekstowe. Następnie kliknij opcję **Load Test Model (Model testu** obciążeniowego) w menu kontekstowym dodanym przez część powyższego `[ContextMenu("Load Test Model")]` kodu.

    ![Ładowanie z menu kontekstowego](./media/load-test-model.png)

1. Obserwuj konsolę, aby uzyskać dane wyjściowe z metody **ProgressHandler** przekazanej do **metody LoadModel.**
1. Zobacz model renderowany zdalnie.

> [!NOTE]
> Model zdalny nigdy nie będzie widoczny w widoku Scene (Scena), tylko w widoku gry. Wynika to z tego, że funkcja ARR zdalnie renderuje ramki specjalnie dla perspektywy kamery widoku gry i nie jest świadoma aparatu edytora (używanego do renderowania widoku sceny).

## <a name="next-steps"></a>Następne kroki

![Załadowany model](./media/test-model-rendered.png)

Gratulacje! Utworzono podstawową aplikację, która umożliwia wyświetlanie zdalnie renderowanych modeli przy użyciu Azure Remote Rendering. W następnym samouczku zintegrujemy usługę MRTK i zaimportujemy własne modele.

> [!div class="nextstepaction"]
> [Dalej: Interfejsy i modele niestandardowe](../custom-models/custom-models.md)
