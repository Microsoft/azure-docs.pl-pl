---
title: Konfigurowanie projektu Unity od podstaw
description: W tym artykule wyjaśniono, jak skonfigurować pusty projekt środowiska Unity do użycia z renderowaniem zdalnym platformy Azure.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 7901f12763cb97fa76c0908e76755247ae934a20
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300593"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Samouczek: Konfigurowanie projektu Unity od podstaw

Ten samouczek zawiera następujące informacje:

> [!div class="checklist"]
>
> * Konfigurowanie projektu geosieci Unity dla ARR.
> * Tworzenie i zatrzymywanie sesji renderowania.
> * Używanie istniejących sesji.
> * Łączenie i rozłączanie sesji.
> * Ładowanie modeli do sesji renderowania.
> * Wyświetlanie statystyk połączeń.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku są potrzebne:

* Informacje o koncie (Identyfikator konta, klucz konta, Identyfikator subskrypcji). Jeśli nie masz konta, [Utwórz konto](../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(pobieranie)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Najnowsza wersja programu Visual Studio 2019 [(Pobierz)](https://visualstudio.microsoft.com/vs/older-downloads/). 
* [Visual Studio Tools dla rzeczywistości mieszanej](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). W szczególnych przypadkach następujące instalacje *obciążenia* są obowiązkowe:
  * **Programowanie aplikacji klasycznych w języku C++**
  * **Programowanie platforma uniwersalna systemu Windows (platformy UWP)**
* GIT [(pobieranie)](https://git-scm.com/downloads)
* 2019.3.1 Unity [(pobieranie)](https://unity3d.com/get-unity/download)
  * Zainstaluj te moduły w aparacie Unity:
    * **Platformy UWP** — obsługa kompilacji platforma uniwersalna systemu Windows
    * **IL2CPP** — obsługa kompilacji systemu Windows (IL2CPP)

> [!TIP]
> [Repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) zawiera przygotowane projekty Unity dla wszystkich samouczków. Możesz użyć tych projektów jako odwołania.

## <a name="create-a-new-unity-project"></a>Utwórz nowy projekt aparatu Unity

W centrum Unity Utwórz nowy projekt.
W tym przykładzie przyjęto założenie, że projekt zostanie utworzony w folderze o nazwie `RemoteRendering` .

![okno nowego projektu](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Konfigurowanie manifestu projektu

Należy zmodyfikować plik `Packages/manifest.json` , który znajduje się w folderze projektu aparatu Unity. Otwórz plik w edytorze tekstów i Dołącz wiersze wymienione poniżej:

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
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

Pakiet uniwersalnego potoku renderowania jest opcjonalny, ale zalecany ze względu na wydajność.
Po zmodyfikowaniu i zapisaniu manifestu aparat Unity zostanie automatycznie odświeżony. Upewnij się, że pakiety zostały załadowane w oknie *projektu* :

![Potwierdź Importy pakietów](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Upewnij się, że masz najnowszą wersję pakietu

Poniższe kroki zapewniają, że Twój projekt używa najnowszej wersji pakietu do renderowania zdalnego.
1. Wybierz pakiet w oknie projektu i kliknij :::no-loc text="package"::: ikonę: Wybierz ![ ikonę pakietu](media/package-icons.png)
1. W Inspektorze kliknij pozycję "Wyświetl w Menedżerze pakietów": ![ Inspektor pakietów](media/package-properties.png)
1. Na stronie Menedżer pakietów dla pakietu zdalnego renderowania Sprawdź, czy przycisk Aktualizuj jest dostępny. Jeśli tak, to kliknięcie spowoduje zaktualizowanie pakietu do najnowszej dostępnej wersji: ![ pakietu ARR w Menedżerze pakietów](media/package-manager.png)
1. Czasami aktualizacja pakietu może prowadzić do błędów w konsoli programu. W takim przypadku spróbuj zamknąć i ponownie otworzyć projekt.

## <a name="configure-the-camera"></a>Konfigurowanie aparatu

Wybierz węzeł **główny aparat** .

1. Zresetuj jego *transformację*:

    ![Zresetuj transformację aparatu](media/camera-reset-transform.png)

1. Ustaw **:::no-loc text="Clear flags":::** na*:::no-loc text="Solid Color":::*

1. Ustaw **:::no-loc text="Background":::** na*:::no-loc text="Black":::*

1. Ustaw wartość **:::no-loc text="Clipping Planes":::** na *blisko = 0,3* i *daleko = 20*. Oznacza to, że renderowanie będzie odcięta geometrii, która jest mniejsza niż 30 cm lub większa niż 20 metrów.

    ![Właściwości aparatu Unity](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Dostosuj ustawienia projektu

1. Otwórz *Edytowanie ustawień projektu >...*
1. Na liście po lewej stronie wybierz pozycję jakość.
1. Zmień **domyślny poziom jakości** na *niski*

    ![Zmień ustawienia jakości projektu](media/settings-quality.png)

1. Wybierz pozycję **grafika** po lewej stronie.
1. Zmień ustawienie **potoku renderowania skryptowego** na *HybridRenderingPipeline*. Pomiń ten krok, jeśli nie jest używany potok uniwersalny renderowania.

    ![Zmiana ustawień grafiki projektu ](media/settings-graphics-lwrp.png) czasami interfejs użytkownika nie wypełnia listy dostępnych typów potoków z pakietów. w takim przypadku element zawartości *HybridRenderingPipeline* musi być przeciągany do pola ręcznie: ![ Zmiana ustawień grafiki projektu](media/hybrid-rendering-pipeline.png)
1. Wybierz **odtwarzacz** po lewej stronie.
1. Wybierz kartę **ustawienia platforma uniwersalna systemu Windows**
1. Zmień **Ustawienia XR** , aby obsługiwały rzeczywistość systemu Windows: ![ Ustawienia odtwarzacza](media/xr-player-settings.png)
1. Wybierz ustawienia tak jak na poniższym zrzucie ekranu:
    1. Włącz obsługę **wirtualnej rzeczywistości**
    1. Ustaw **Format głębokości** na *16-bitową głębię*
    1. Włącz **udostępnianie buforu głębokości**
    1. Ustaw **tryb renderowania stereo** na *pojedynczy przebieg*

1. W tym samym oknie, powyżej *ustawień XR*, rozwiń pozycję **Ustawienia publikowania**
1. Przewiń w dół do opcji **możliwości** i wybierz:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * Opcjonalne do tworzenia: **PrivateNetworkClientServer**

      Ta opcja jest wymagana, jeśli chcesz połączyć zdalny debuger aparatu Unity z urządzeniem.

1. W **obsługiwanych rodzinach urządzeń**Włącz **Holographic** i **pulpit**

1. Jeśli chcesz użyć zestawu narzędzi rzeczywistości mieszanej, zobacz [dokumentację MRTK](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview), aby uzyskać więcej informacji na temat zalecanych ustawień i możliwości.

## <a name="validate-project-setup"></a>Weryfikowanie konfiguracji projektu

Wykonaj następujące kroki, aby sprawdzić, czy ustawienia projektu są poprawne.

1. Wybierz wpis ValidateProject z menu RemoteRendering na pasku narzędzi edytora aparatu Unity.
1. Użyj okna ValidateProject, aby sprawdzić i naprawić ustawienia projektu, w razie potrzeby.

    ![Sprawdzanie poprawności projektu edytora Unity](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Tworzenie skryptu w celu zainicjowania renderowania zdalnego na platformie Azure

Utwórz [Nowy skrypt](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) i nadaj mu nazwę **RemoteRendering**. Otwórz plik skryptu i Zastąp całą zawartość następującym kodem:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

Ten skrypt inicjuje zdalne renderowanie na platformie Azure, poinformuj o tym, który obiekt aparatu ma być używany do renderowania, i umieszcza przycisk **Utwórz sesję** w okienku ekranu, gdy *tryb odtwarzania* jest aktywowany.

> [!CAUTION]
> Modyfikacja skryptu i zapisanie go w czasie, gdy tryb odtwarzania jest aktywny w aparacie Unity, może spowodować zamarzanie aparatu Unity i wymuszone zamknięcie go za pomocą Menedżera zadań. W związku z tym zawsze należy zatrzymać tryb odtwarzania przed edycją skryptu *RemoteRendering* .

## <a name="test-azure-remote-rendering-session-creation"></a>Testowanie tworzenia sesji renderowania zdalnego platformy Azure

Utwórz nowy obiekt gameobject w scenie i Dodaj do niego składnik *RemoteRendering* . Wypełnij odpowiednie *domeny konta*, *Identyfikator konta*i *klucz konta* dla konta renderowania zdalnego:

![Właściwości składnika renderowania zdalnego](media/remote-rendering-component.png)

Uruchom aplikację w edytorze (**naciśnij klawisze Play** lub Ctrl + P). W okienku ekranu powinna zostać wyświetlona przycisk **Utwórz sesję** . Kliknij go, aby rozpocząć pierwszą sesję ARR:

![Tworzenie pierwszej sesji](media/test-create.png)

Jeśli to się nie powiedzie, upewnij się, że poprawnie wprowadzono szczegóły konta we właściwościach składnika RemoteRendering. W przeciwnym razie w oknie konsoli zostanie wyświetlony komunikat z informacją o IDENTYFIKATORze sesji przypisanym do użytkownika i zostanie wyświetlona, że sesja jest obecnie w stanie *uruchomienia* :

![Wyjście rozpoczynające sesję](media/create-session-output.png)

W tym momencie platforma Azure zastrzega sobie serwerowi i uruchamia zdalną maszynę wirtualną. Zwykle trwa to od **3 do 5 minut**. Gdy maszyna wirtualna jest gotowa, zostanie `OnSessionStatusChanged` wykonane wywołanie zwrotne skryptu aparatu Unity i zostanie wydrukowany nowy stan sesji:

![Wyjście gotowe do sesji](media/create-session-output-2.png)

To wszystko! Nie będzie to miało więcej czasu. Aby uniknąć naliczania opłat, należy zawsze zatrzymywać sesje, gdy nie są już potrzebne. W tym przykładzie możesz to zrobić, klikając przycisk **Zatrzymaj sesję** lub zatrzymując symulację aparatu Unity. Ze względu na Właściwość **sesji automatycznego zatrzymania** w składniku *ARRServiceUnity* , która jest domyślnie włączona, sesja zostanie zatrzymana automatycznie. Jeśli działanie zakończy się niepowodzeniem, z powodu awarii lub problemów z połączeniem, sesja może działać tak długo, jak *MaxLeaseTime* przed zamknięciem serwera.

> [!NOTE]
> Zatrzymywanie sesji zajmie natychmiastowy skutek i nie można jej cofnąć. Po zatrzymaniu należy utworzyć nową sesję z tym samym obciążeniem podczas uruchamiania.

## <a name="reusing-sessions"></a>Korzystanie z sesji

Tworzenie nowej sesji to, niestety, czasochłonna operacja. W związku z tym jedna powinna próbować tworzyć sesje sporadycznie i ponownie używać ich, jeśli jest to możliwe.

Wstaw następujący kod do skryptu *RemoteRendering* i usuń stare wersje funkcji zduplikowanych:

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> Przed uruchomieniem tego kodu, upewnij się, że w składniku RemoteRendering można dezaktywować opcję **zatrzymywania sesji** . W przeciwnym razie każda utworzona sesja zostanie automatycznie zatrzymana po zatrzymaniu symulacji i próba ponownego użycia nie powiedzie się.

Po naciśnięciu przycisku *Odtwórz*uzyskasz trzy przyciski w okienku ekranu: **Utwórz sesję**, **Badaj aktywne sesje**i **Użyj istniejącej sesji**. Pierwszy przycisk zawsze tworzy nową sesję. Drugi przycisk wysyła zapytania, które istnieją *aktywne* sesje. Jeśli nie określisz ręcznie identyfikatora sesji, który ma zostać użyty, ta akcja spowoduje automatyczne wybranie tego identyfikatora sesji do użytku w przyszłości. Trzeci przycisk próbuje nawiązać połączenie z istniejącą sesją. Można go określić ręcznie za pomocą właściwości składnika *identyfikatora sesji* lub jednej znalezionej przez *zapytanie aktywnych sesji*.

Funkcja **AutoStartSessionAsync** służy do symulowania naciśnięcia przycisku poza edytorem.

> [!TIP]
> Istnieje możliwość otwarcia sesji, które zostały zatrzymane, wygasły lub są w stanie błędu. Chociaż nie można ich użyć do renderowania, można zbadać ich szczegóły, po otwarciu nieaktywnej sesji. Powyższy kod sprawdza stan sesji w programie `ARRService_OnSessionStarted` , aby automatycznie zatrzymywać, gdy sesja stanie się niezdatna do użytku.

Korzystając z tej funkcji, można teraz tworzyć i ponownie używać sesji, co znacznie poprawia przepływ pracy programistycznej.

Zwykle Tworzenie sesji zostanie wyzwolone poza aplikacją kliencką z powodu czasu wymaganego do uruchomienia serwera.

## <a name="connect-to-an-active-session"></a>Nawiązywanie połączenia z aktywną sesją

Do tej pory zostały utworzone lub otwarte sesje. Następnym krokiem jest *nawiązanie połączenia* z sesją. Po nawiązaniu połączenia serwer renderowania utworzy obrazy i wyśle strumień wideo do naszej aplikacji.

Wstaw następujący kod do skryptu *RemoteRendering* i usuń stare wersje funkcji zduplikowanych:

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

Aby przetestować tę funkcję:

1. Naciśnij pozycję **Odtwórz** w aparacie Unity.
1. Otwórz sesję:
    1. Jeśli masz już sesję, naciśnij przycisk **Query Active Sessions** , a następnie **Użyj istniejącej sesji**.
    1. W przeciwnym razie naciśnij pozycję **Utwórz sesję**.
1. Naciśnij pozycję **Połącz**.
1. Po kilku sekundach dane wyjściowe konsoli powinny być drukowane.
1. Na razie nic innego nie powinno się zdarzyć.
1. Naciśnij przycisk **Rozłącz** lub Zatrzymaj tryb odtwarzania aparatu Unity.

>[!NOTE]
> Wielu użytkowników może *otworzyć* sesję, aby zbadać jej informacje, ale tylko jeden użytkownik może być *połączony* z sesją w danym momencie. Jeśli inny użytkownik jest już połączony, połączenie zakończy się niepowodzeniem z **powodu błędu uzgadniania**.

## <a name="load-a-model"></a>Załaduj model

Wstaw następujący kod do skryptu *RemoteRendering* i usuń stare wersje funkcji zduplikowanych:

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

Gdy teraz naciśniesz przycisk Odtwórz, Otwórz sesję i nawiąż połączenie z nią, zostanie wyświetlony przycisk **Załaduj model** . Po kliknięciu tej opcji w danych wyjściowych konsoli zostanie wyświetlony postęp ładowania i kiedy osiągnie on 100% powinien zostać wyświetlony model aparatu:

![Model załadowany w edytorze](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) jest ważnym składnikiem używanym do [stateczności hologramu](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). Jednak będzie obowiązywać tylko po wdrożeniu na urządzeniu rzeczywistości mieszanej.

> [!TIP]
> W przypadku korzystania z [przewodnika Szybki Start: konwertowanie modelu do renderowania](../../quickstarts/convert-model.md), już wiesz, jak przekonwertować własne modele. Wszystko, co musisz zrobić teraz, aby je renderować, polega na umieszczeniu identyfikatora URI w przekonwertowanym modelu we właściwości *nazwy modelu* .

## <a name="display-frame-statistics"></a>Wyświetlanie statystyk ramek

Zdalne renderowanie na platformie Azure śledzi różne informacje o jakości połączenia. Aby szybko wyświetlić te informacje, wykonaj następujące czynności:

Utwórz [Nowy skrypt](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) i nadaj mu nazwę **RemoteFrameStats**. Otwórz plik skryptu i Zastąp całą zawartość następującym kodem:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

Utwórz Gręobject i nadaj jej nazwę *FrameStats*. Dołącz go jako węzeł podrzędny do głównego obiektu *aparatu* i ustaw jego pozycję na **x = 0, y = 0, z = 0,325**. Dodaj składnik **RemoteFrameStats** do obiektu.

Dodaj **interfejs użytkownika > obiekt podrzędny kanwy** do obiektu *FrameStats* i ustaw jego właściwości w następujący sposób:

![Właściwości kanwy](media/framestats-canvas.png)

Dodaj **interfejs użytkownika > obiekt tekstowy** jako element podrzędny kanwy i ustaw jego właściwości w następujący sposób:

![Właściwości tekstu](media/framestats-text.png)

Wybierz obiekt *FrameStats* i wypełnij **pole FrameStats** , klikając ikonę okręgu i zaznaczając obiekt **tekstowy** :

![Ustawianie właściwości text](media/framestats-set-text.png)

Teraz po nawiązaniu połączenia z sesją zdalną tekst powinien zawierać statystyki przesyłania strumieniowego:

![dane wyjściowe statystyk ramki](media/framestats-output.png)

Kod wyłącza aktualizację statystyk poza edytorem, ponieważ pole tekstowe zablokowane przez nagłówek jest rozpraszane. Bardziej zaawansowaną implementację można znaleźć w projekcie [szybkiego startu](../../quickstarts/render-model.md) .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono wszystkie kroki niezbędne do przejęcia pustego projektu środowiska Unity i pobrania go przy użyciu zdalnego renderowania platformy Azure. W następnym samouczku zajmiemy się tym, jak współpracować z obiektami zdalnymi.

> [!div class="nextstepaction"]
> [Samouczek: Praca z jednostkami zdalnymi w środowisku Unity](working-with-remote-entities.md)
