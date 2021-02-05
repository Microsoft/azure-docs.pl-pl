---
title: Wyświetlanie zdalnie renderowanego modelu
description: "\"Hello world\" renderowania zdalnego na platformie Azure — samouczek pokazuje, jak wyświetlić model renderowany zdalnie przez platformę Azure"
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: bfcd1e600c722cf3a4951da60097c7c373f9b1a6
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592045"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Samouczek: wyświetlanie zdalnie renderowanego modelu

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Inicjowanie obsługi zdalnego renderowania (ARR) na platformie Azure
> * Tworzenie i zatrzymywanie sesji renderowania
> * Ponowne użycie istniejącej sesji renderowania
> * Łączenie i rozłączanie z sesjami
> * Ładowanie modeli do sesji renderowania

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku są potrzebne:

* Aktywną subskrypcję platformy Azure z opcją płatność zgodnie z rzeczywistym użyciem [Tworzenie konta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(pobieranie)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Najnowsza wersja programu Visual Studio 2019 [(pobieranie)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(pobieranie)](https://git-scm.com/downloads)
* Aparat Unity, Najnowsza wersja 2019,3, zalecamy użycie centrum Unity dla tego [(pobieranie)](https://unity3d.com/get-unity/download)
  * Zainstaluj te moduły w aparacie Unity:
    * **Platformy UWP** — obsługa kompilacji platforma uniwersalna systemu Windows
    * **IL2CPP** — obsługa kompilacji systemu Windows (IL2CPP)
* Pośrednia znajomość aparatu Unity i języka C# (na przykład: Tworzenie skryptów i obiektów przy użyciu prefabs, Konfigurowanie zdarzeń aparatu Unity itp.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Inicjowanie obsługi zdalnego renderowania (ARR) na platformie Azure

Aby uzyskać dostęp do usługi zdalnego renderowania na platformie Azure, musisz najpierw [utworzyć konto](../../../how-tos/create-an-account.md#create-an-account).

## <a name="create-a-new-unity-project"></a>Tworzenie nowego projektu aparatu Unity

> [!TIP]
> [Repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) zawiera projekt ze wszystkimi wykonanymi samouczkami, może służyć jako odwołanie. Poszukaj w *Unity\Tutorial-Complete* całego projektu środowiska Unity.

W centrum Unity Utwórz nowy projekt.
W tym przykładzie przyjęto założenie, że projekt zostanie utworzony w folderze o nazwie **RemoteRendering**.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Nowy projekt Unity":::

## <a name="include-the-azure-remote-rendering-package"></a>Uwzględnij pakiet renderowania zdalnego platformy Azure

Należy zmodyfikować plik `Packages/manifest.json` , który znajduje się w folderze projektu aparatu Unity. Otwórz plik w edytorze tekstów i Dodaj następujące wiersze na początku manifestu:

```json
{
    "scopedRegistries": [
    {
        "name": "Azure Mixed Reality Services",
        "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
        "scopes": ["com.microsoft.azure"]
    }
    ],
    "dependencies": {
        "com.unity.render-pipelines.universal": "7.3.1",
        "com.microsoft.azure.remote-rendering": "0.1.31",
        ...existing dependencies...
    }
}
```

Po zmodyfikowaniu i zapisaniu manifestu aparat Unity zostanie automatycznie odświeżony. Upewnij się, że pakiety zostały załadowane w oknie *projektu* :

:::image type="content" source="./media/confirm-packages.png" alt-text="Potwierdź Importy pakietów":::

Jeśli pakiety nie są ładowane, sprawdź, czy w konsoli aparatu Unity występują błędy. Jeśli nie masz żadnych błędów i nadal nie widzisz żadnych pakietów w folderze **Packages** , zaznacz przycisk przełączania widoczności pakietu. \
![Zrzut ekranu ze strzałką wskazującą na przycisk przełączania widoczności pakietu.](./media/unity-package-visibility.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Upewnij się, że masz najnowszą wersję pakietu

Poniższe kroki zapewniają, że Twój projekt używa najnowszej wersji pakietu do renderowania zdalnego.

1. W górnym menu edytora Unity Otwórz *okno Menedżer pakietów >*.
1. Wybierz pakiet, **Microsoft Azure zdalne renderowanie**.
1. Na stronie Menedżer pakietów dla Microsoft Azure pakietu **renderowania zdalnego** należy sprawdzić, czy jest dostępny przycisk **Aktualizuj** . Jeśli tak jest, kliknij go, aby zaktualizować pakiet do najnowszej dostępnej wersji: \
![Pakiet ARR w Menedżerze pakietów](./media/package-manager.png)
1. Aktualizacja pakietu może czasami prowadzić do błędów konsoli. W takim przypadku spróbuj zamknąć i ponownie otworzyć projekt.
1. Gdy pakiet jest aktualny, Menedżer pakietów będzie **wyświetlany jako aktualny zamiast przycisku** Aktualizuj.
![Aktualny pakiet](./media/package-up-to-date.png)
## <a name="configure-the-camera"></a>Konfigurowanie aparatu

1. Wybierz węzeł **główny aparat** .

1. Otwórz menu kontekstowe, klikając prawym przyciskiem myszy składnik *przekształcenia* i wybierając opcję **Resetuj** :

    ![Zresetuj transformację aparatu](./media/camera-reset-transform.png)

1. Ustaw **Wyczyść flagi** na *Kolor kryjący*

1. Ustaw **tło** na *czerń* (#000000), używając w pełni przezroczystego (0) alfa (A)

    ![Koło kolorów](./media/color-wheel-black.png)

1. Ustaw **płaszczyzny przycinające** na wartość *Near = 0,3* i *daleko = 20*. Oznacza to, że renderowanie będzie odcięta geometrii, która jest mniejsza niż 30 cm lub większa niż 20 metrów.

    ![Właściwości aparatu Unity](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Dostosuj ustawienia projektu

1. Otwórz *Edytowanie ustawień projektu >...*
1. Wybierz pozycję **jakość** z menu listy po lewej stronie
1. Zmień **domyślny poziom jakości** wszystkich platform na *niski*. To ustawienie umożliwi wydajniejsze renderowanie zawartości lokalnej i nie ma wpływu na jakość zdalnie renderowanej zawartości.

    ![Zmień ustawienia jakości projektu](./media/settings-quality.png)

1. Wybierz pozycję **grafika** z menu listy po lewej stronie
1. Zmień ustawienie **potoku renderowania skryptowego** na *HybridRenderingPipeline*. \
    ![Zrzut ekranu, który wskazuje, gdzie można zmienić ustawienie potoku renderowania skryptowego na HybridRenderingPipeline.](./media/settings-graphics-render-pipeline.png)\
    Czasami interfejs użytkownika nie wypełnia listy dostępnych typów potoków z pakietów. W takim przypadku należy ręcznie przeciągnąć element zawartości *HybridRenderingPipeline* na pole: \
    ![Zmienianie ustawień grafiki projektu](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Jeśli nie możesz przeciągnąć i upuścić elementu zawartości *HybridRenderingPipeline* do pola zasobu potoku renderowania (prawdopodobnie dlatego, że pole nie istnieje!), upewnij się, że konfiguracja pakietu zawiera `com.unity.render-pipelines.universal` pakiet.

1. Wybierz **odtwarzacz** z menu listy po lewej stronie
1. Wybierz kartę **ustawienia platforma uniwersalna systemu Windows** , reprezentowane jako ikona systemu Windows.
1. Zmień **Ustawienia XR** , aby obsługiwać rzeczywistość mieszana systemu Windows, jak pokazano poniżej:
    1. Włącz obsługę **wirtualnej rzeczywistości**
    1. Naciśnij przycisk "+" i Dodaj **rzeczywistość systemu Windows**
    1. Ustaw **Format głębokości** na *16-bitową głębię*
    1. Upewnij się, że włączono **udostępnianie buforu głębokości**
    1. Ustaw **tryb renderowania stereo** na *pojedynczy przebieg*

    ![ustawienia odtwarzacza](./media/xr-player-settings.png)

1. W tym samym oknie, powyżej **ustawień XR**, rozwiń pozycję **Ustawienia publikowania**
1. Przewiń w dół do opcji **możliwości** i wybierz:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** (*Opcjonalnie*). Wybierz tę opcję, jeśli chcesz połączyć zdalny debuger aparatu Unity z urządzeniem.

1. W obszarze **obsługiwane rodziny urządzeń** Włącz **Holographic** i **pulpit**
1. Zamknij lub Zadokuj panel **ustawień projektu**
1. Otwórz *plik — ustawienia kompilacji >*
1. Wybierz **platforma uniwersalna systemu Windows**
1. Skonfiguruj ustawienia tak, aby były zgodne z tymi, które znajdują się poniżej
1. Naciśnij przycisk **przełącznika platformy** . \
![Ustawienia kompilacji](./media/build-settings.png)
1. Po zmianie platform przez środowisko Unity Zamknij Panel kompilacji.

## <a name="validate-project-setup"></a>Weryfikowanie konfiguracji projektu

Wykonaj następujące kroki, aby sprawdzić, czy ustawienia projektu są poprawne.

1. Wybierz wpis **ValidateProject** z menu **RemoteRendering** na pasku narzędzi edytora aparatu Unity.
1. Przejrzyj okno **ValidateProject** pod kątem błędów i napraw ustawienia projektu, w razie potrzeby.

    ![Sprawdzanie poprawności projektu edytora Unity](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Utwórz skrypt do koordynowania połączenia i stanu renderowania zdalnego platformy Azure

Istnieją cztery podstawowe etapy pokazujące zdalnie renderowane modele, opisane w poniższym schemacie blokowym. Każdy etap należy wykonać w pożądanej kolejności. Następnym krokiem jest utworzenie skryptu, który będzie zarządzać stanem aplikacji i przebiegać przez poszczególne wymagane etapy.

![Stos ARR 0](./media/remote-render-stack-0.png)

1. W okienku *projekt* w obszarze **zasoby** Utwórz nowy folder o nazwie *RemoteRenderingCore*. Następnie wewnątrz *RemoteRenderingCore* Utwórz inny folder o nazwie *skrypty*.

1. Utwórz [Nowy skrypt języka C#](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) o nazwie **RemoteRenderingCoordinator**.
Projekt powinien wyglądać następująco:

    ![Hierarchia projektu](./media/project-structure.png)

    Ten skrypt koordynatora będzie śledził i zarządzać stanem renderowania zdalnego. Zwróć uwagę, że niektóre z tych kodów są używane do konserwacji stanu, ujawniania funkcjonalności innym składnikom, wyzwalania zdarzeń i przechowywania danych specyficznych dla aplikacji, które nie są *bezpośrednio* związane z renderowaniem zdalnym platformy Azure. Użyj poniższego kodu jako punktu wyjścia, a w dalszej części tego samouczka podasz i zaimplementujmy określony kod zdalnego renderowania platformy Azure.

1. Otwórz **RemoteRenderingCoordinator** w edytorze kodu i Zastąp całą zawartość następującym kodem:

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

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // The list of regions is available at https://docs.microsoft.com/azure/remote-rendering/reference/regions
    [SerializeField]
    private string accountDomain = "westus2.mixedreality.azure.com";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountAuthenticationDomain = "<enter your account authentication domain here>";
    public string AccountAuthenticationDomain
    {
        get => accountAuthenticationDomain.Trim();
        set => accountAuthenticationDomain = value;
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
                Debug.Log($"State changed to: {currentCoordinatorState}");
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
        return await Task.FromResult(new SessionConfiguration(AccountAuthenticationDomain, AccountDomain, AccountId, AccountKey));
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
        var allSessions = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
        return allSessions.SessionProperties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
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

## <a name="create-the-azure-remote-rendering-gameobject"></a>Tworzenie zdalnego renderowania platformy Azure — Graobject

Koordynator renderowania zdalnego i jego wymagany skrypt (*ARRServiceUnity*) są zarówno niektórymi zachowaniami, które muszą być dołączone do gry w scenie. Skrypt *ARRServiceUnity* jest dostarczany przez ARR w celu udostępnienia wielu funkcji ARR do łączenia się z sesjami zdalnymi i zarządzania nimi.

1. Utwórz nową Gręobject w scenie (Ctrl + Shift + N lub *gameobject — >utworzyć puste*) i nadaj jej nazwę **RemoteRenderingCoordinator**.
1. Dodaj skrypt *RemoteRenderingCoordinator* do **RemoteRenderingCoordinator** gameobject. \
![Dodaj składnik RemoteRenderingCoordinator](./media/add-coordinator-script.png)
1. Potwierdź, że skrypt *ARRServiceUnity* , wyświetlany jako *Usługa* w Inspektorze, jest automatycznie dodawany do gry. Jeśli zastanawiasz się, jest to wynik w `[RequireComponent(typeof(ARRServiceUnity))]` górnej części skryptu **RemoteRenderingCoordinator** .
1. Dodaj poświadczenia renderowania zdalnego platformy Azure, domenę uwierzytelniania konta i domenę konta do skryptu koordynatora: \
![Dodawanie poświadczeń](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Inicjowanie renderowania zdalnego platformy Azure

Teraz, gdy mamy już strukturę naszego koordynatora, zaimplementujmy każdy z czterech etapów, rozpoczynając od **inicjowania zdalnego renderowania**.

![Stos ARR 1](./media/remote-render-stack-1.png)

**Zainicjuj** oznacza, że usługa Azure Remote renderuje, który obiekt kamery ma być używany do renderowania i postępu maszyny stanu w **NotAuthorized**. Oznacza to, że jest zainicjowany, ale nie jest jeszcze autoryzowany do łączenia się z sesją. Od momentu rozpoczęcia sesji ARR jest koszt, dlatego musimy potwierdzić, że użytkownik chce przeprowadzić operację.

Podczas wprowadzania stanu **NotAuthorized** jest wywoływana **CheckAuthorization** , która wywołuje zdarzenie **RequestingAuthorization** i określa, które poświadczenia konta do użycia (**AccountInfo** jest zdefiniowany w górnej części klasy i używa poświadczeń zdefiniowanych za pośrednictwem inspektora Unity w powyższym kroku).

   > [!NOTE]
   > Ponowna kompilacja środowiska uruchomieniowego nie jest obsługiwana przez ARR. Modyfikacja skryptu i zapisanie go w czasie aktywności trybu odtwarzania może spowodować zamarzanie aparatu Unity i konieczność wymuszenia wyłączenia za pomocą Menedżera zadań. Zawsze upewnij się, że zatrzymano tryb odtwarzania przed edytowaniem skryptów.

1. Zastąp zawartość **InitializeARR** i **InitializeSessionService** następującym kodem:

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

Aby postępować od **NotAuthorized** do **nosession**, zwykle przedstawiamy użytkownikowi modalne okno dialogowe, aby można było wybrać (i zrobićmy tylko w innym rozdziale). Teraz automatycznie pomijamy sprawdzanie autoryzacji przez wywołanie **ByPassAuthentication** zaraz po wyzwoleniu zdarzenia **RequestingAuthorization** .

1. Wybierz **RemoteRenderingCoordinator** gameobject i Znajdź zdarzenie Unity **OnRequestingAuthorization** uwidocznione w Inspektorze składnika **RemoteRenderingCoordinator** .

1. Dodaj nowe zdarzenie, naciskając znak "+" w prawym dolnym rogu.
1. Przeciągnij składnik do własnego zdarzenia, aby odwołać się do samego siebie. \
![Obejdź uwierzytelnianie](./media/bypass-authorization-add-event.png)\
1. Z listy rozwijanej wybierz pozycję **RemoteRenderingCoordinator-> BypassAuthorization**. \
![Zrzut ekranu przedstawiający wybraną opcję RemoteRenderingCoordinator. BypassAuthorization.](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Tworzenie sesji zdalnej lub dołączanie do niej

Drugim etapem jest utworzenie lub dołączenie zdalnej sesji renderowania (zobacz [zdalne renderowanie sesji](../../../concepts/sessions.md) , aby uzyskać więcej informacji).

![Stos ARR 2](./media/remote-render-stack-2.png)

Sesja zdalna to miejsce, w którym będą renderowane modele. Metoda **JoinRemoteSession ()** podejmie próbę dołączenia do istniejącej sesji, która jest śledzona przy użyciu właściwości **LastUsedSessionID** lub ma przypisany identyfikator aktywnej sesji na **SessionIDOverride**. **SessionIDOverride** jest przeznaczona tylko do celów debugowania, dlatego powinna być używana tylko wtedy, gdy wiadomo, że sesja istnieje i chcesz w sposób jawny nawiązać z nią połączenie.

Jeśli żadna sesja nie jest dostępna, zostanie utworzona nowa sesja. Tworzenie nowej sesji jest jednak czasochłonną operacją. W związku z tym należy spróbować utworzyć sesje tylko wtedy, gdy jest to wymagane, i użyć ich ponownie, jeśli to możliwe (zobacz [komercyjne gotowe: pule sesji, planowanie i najlepsze rozwiązania,](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) Aby uzyskać więcej informacji na temat zarządzania sesjami).

> [!TIP]
> **StopRemoteSession ()** zakończy aktywną sesję. Aby uniknąć niepotrzebnych opłat, należy zawsze zatrzymywać sesje, gdy nie są już potrzebne.

Komputer stanu będzie teraz postępować z **ConnectingToNewRemoteSession** lub **ConnectingToExistingRemoteSession**, w zależności od dostępnych sesji. Zarówno otwarcie istniejącej sesji, jak i utworzenie nowej sesji spowoduje wyzwolenie zdarzenia **ARRSessionService. OnSessionStatusChanged** , wykonując naszą metodę **OnRemoteSessionStatusChanged** . W idealnym przypadku spowoduje to przechodzenie komputera stanu do **RemoteSessionReady**.

1. Aby dołączyć do nowej sesji, należy zmodyfikować kod w celu zastąpienia metod **JoinRemoteSession ()** i **StopRemoteSession ()** z przedstawionymi przykładami poniżej:

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
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationOptions(renderingSessionVmSize, maxLeaseHours, maxLeaseMinutes));
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

Aby zaoszczędzić czas przez ponowne użycie sesji, należy wyłączyć opcję **automatycznie Zatrzymaj sesję** w składniku *ARRServiceUnity* . Należy pamiętać, że spowoduje to pozostawienie uruchomionych sesji, nawet wtedy, gdy żaden z nich nie jest podłączony do nich. Sesja może działać tak długo, jak *MaxLeaseTime* przed zamknięciem serwera (wartość dla *MaxLeaseTime* można zmodyfikować w koordynatorze renderowania zdalnego, w obszarze *nowe wartości domyślne sesji*). Z drugiej strony, jeśli podczas odłączania automatycznie zostanie wyłączona każda sesja, należy poczekać na rozpoczęcie nowej sesji, co może być czasochłonne.

> [!NOTE]
> Zatrzymywanie sesji zajmie natychmiastowy skutek i nie można jej cofnąć. Po zatrzymaniu należy utworzyć nową sesję z tym samym obciążeniem podczas uruchamiania.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>Połącz lokalne środowisko uruchomieniowe z sesją zdalną

Następnie aplikacja musi połączyć swoje lokalne środowisko uruchomieniowe z sesją zdalną.

![Stos ARR 3](./media/remote-render-stack-3.png)

Aplikacja musi również nasłuchiwać zdarzeń dotyczących połączenia między środowiskiem uruchomieniowym a bieżącą sesją; te zmiany stanu są obsługiwane w **OnLocalRuntimeStatusChanged**. Ten kod będzie postępować według naszego stanu **ConnectingToRuntime**. Po nawiązaniu połączenia w **OnLocalRuntimeStatusChanged** stan zmieni się na **RuntimeConnected**. Nawiązywanie połączenia ze środowiskiem uruchomieniowym jest ostatnim stanem, co oznacza, że aplikacja jest wykonywana ze wspólną konfiguracją i jest gotowa do rozpoczęcia pracy specyficznej dla sesji w celu załadowania i renderowania modeli.

 1. Zastąp metody **ConnectRuntimeToRemoteSession ()** i **DisconnectRuntimeFromRemoteSession ()** z zakończonymi wersjami poniżej.
 1. Ważne jest, aby zanotować metodę Unity **Funkcja lateupdate** i że aktualizuje ona bieżącą aktywną sesję. Dzięki temu bieżąca sesja może wysyłać/odbierać komunikaty i aktualizować bufor ramki przy użyciu ramek odebranych z sesji zdalnej. Ma krytyczne znaczenie dla poprawnego działania.

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
> Połączenie lokalnego środowiska uruchomieniowego z sesją zdalną jest zależne od **aktualizacji** wywoływanej w aktualnie aktywnej sesji. Jeśli okaże się, że aplikacja nigdy nie postępuje w stanie **ConnectingToRuntime** , upewnij się, że jest **ona** regularnie wywoływana w aktywnej sesji.

## <a name="load-a-model"></a>Załaduj model

Z wymaganą podstawą jest gotowy do załadowania modelu do sesji zdalnej i rozpoczęcia otrzymywania ramek.

![Diagram pokazujący przepływ procesu przygotowywania do załadowania i wyświetlania modelu.](./media/remote-render-stack-4.png)

Metoda **LoadModel** została zaprojektowana w celu zaakceptowania ścieżki modelu, procedury obsługi postępu i transformacji nadrzędnej. Te argumenty zostaną użyte do załadowania modelu do sesji zdalnej, zaktualizowania użytkownika na postęp ładowania i zorientowania się zdalnie renderowanego modelu na podstawie transformacji nadrzędnej.

1. Zastąp metodę **LoadModel** całkowicie następującym kodem:

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
        var loadModelParams = new LoadModelFromSasParams(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

Powyższy kod wykonuje następujące czynności:

1. Utwórz [jednostkę zdalną](../../../concepts/entities.md).
1. Utwórz lokalną Gręobject, która będzie reprezentować jednostkę zdalną.
1. Skonfiguruj lokalną Gręobject, aby synchronizować jej stan (tj. Transform) z jednostką zdalną każdej ramki.
1. Ustaw nazwę i Dodaj [**WorldAnchor**](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) , aby pomóc w ustabilizowaniu.
1. Załaduj dane modelu z Blob Storage do jednostki zdalnej.
1. Zwróć jednostkę nadrzędną, aby uzyskać dalsze informacje.

## <a name="view-the-test-model"></a>Wyświetlanie modelu testowego

Mamy teraz cały kod wymagany do wyświetlenia zdalnie renderowanego modelu, wszystkie cztery z wymaganych etapów renderowania zdalnego są kompletne. Teraz musimy dodać mały kod, aby rozpocząć proces ładowania modelu.

![Stos ARR 4](./media/remote-render-stack-5.png)

1. Dodaj następujący kod do klasy **RemoteRenderingCoordinator** , tuż poniżej metody **LoadModel** :

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
    
    Ten kod tworzy obiekt gameobject do działania jako element nadrzędny w modelu testowym. Następnie wywołuje metodę **LoadModel** w celu załadowania modelu "BUILTIN://Engine", który jest zasobem wbudowanym do zdalnego renderowania na platformie Azure na potrzeby testowania renderowania.

1. Zapisz swój kod.
1. Naciśnij przycisk Odtwórz w edytorze aparatu Unity, aby rozpocząć proces nawiązywania połączenia ze zdalnym renderowaniem na platformie Azure i tworzenia nowej sesji.
1. Nie zobaczysz dużo w widoku gry, ale w konsoli zostanie wyświetlony stan zmiany aplikacji. Najprawdopodobniej będzie to potrwać `ConnectingToNewRemoteSession` , a nawet przez maksymalnie pięć minut.
1. Wybierz **RemoteRenderingCoordinator** gameobject, aby wyświetlić dołączone skrypty w Inspektorze. Obejrzyj aktualizację składnika **usługi** w miarę postępu przez kroki inicjowania i łączenia.
1. Monitoruj dane wyjściowe konsoli — oczekiwanie na zmianę stanu na **RuntimeConnected**.
1. Po powiązaniu środowiska uruchomieniowego kliknij prawym przyciskiem myszy **RemoteRenderingCoordinator** w Inspektorze, aby uwidocznić menu kontekstowe. Następnie kliknij opcję **model testu obciążenia** w menu kontekstowym, dodane przez `[ContextMenu("Load Test Model")]` część naszego kodu powyżej.

    ![Załaduj z menu kontekstowego](./media/load-test-model.png)

1. Obejrzyj konsolę dla danych wyjściowych **ProgressHandler** , które zostały przesłane do metody **LoadModel** .
1. Zobacz zdalnie renderowany model.

> [!NOTE]
> Model zdalny nigdy nie będzie widoczny w widoku sceny, tylko w widoku gry. Wynika to z faktu, że ARR renderuje ramki zdalnie, w odniesieniu do perspektywy aparatu wyświetlania gier i nie ma informacji o aparacie edytora (używanym do renderowania widoku sceny).

## <a name="next-steps"></a>Następne kroki

![Załadowano model](./media/test-model-rendered.png)

Gratulacje! Utworzono podstawową aplikację, która umożliwia wyświetlanie zdalnie renderowanych modeli przy użyciu funkcji renderowania zdalnego na platformie Azure. W następnym samouczku będziemy zintegrować MRTK i zaimportować własne modele.

> [!div class="nextstepaction"]
> [Dalej: interfejsy i modele niestandardowe](../custom-models/custom-models.md)
