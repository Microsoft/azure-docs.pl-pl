---
title: Zabezpieczanie usługi Azure Remote Rendering i magazynu modeli
description: Wzmacnianie ochrony aplikacji Remote Rendering do zabezpieczania zawartości
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: d30ab051e58573daefd16f178feb4fc94f2ec83f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835474"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Samouczek: zabezpieczanie Azure Remote Rendering magazynu modeli

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Bezpieczne Azure Blob Storage zawierające Azure Remote Rendering modeli
> * Uwierzytelnianie za pomocą usługi Azure AD w celu uzyskania dostępu Azure Remote Rendering wystąpienia
> * Używanie poświadczeń platformy Azure do Azure Remote Rendering uwierzytelniania

## <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek jest kompilowany w [oparciu o samouczek: uściślianie materiałów, oświetlenia i efektów](..\materials-lighting-effects\materials-lighting-effects.md).

## <a name="why-additional-security-is-needed"></a>Dlaczego potrzebne są dodatkowe zabezpieczenia

Bieżący stan aplikacji i jej dostępu do zasobów platformy Azure wygląda następująco:

![Zabezpieczenia początkowe](./media/security-one.png)

Zarówno "AccountID + AccountKey", jak i "ADRES URL i token SAS" zasadniczo przechowują razem nazwę użytkownika i hasło. Jeśli na przykład ujawniono element "AccountID + AccountKey", osoba atakująca może korzystać z zasobów ARR bez Twojej zgody kosztem.

## <a name="securing-your-content-in-azure-blob-storage"></a>Zabezpieczanie zawartości w Azure Blob Storage

Azure Remote Rendering można bezpiecznie uzyskać dostęp do zawartości Azure Blob Storage z poprawną konfiguracją. Zobacz [How-to: Link storage accounts](../../../how-tos/create-an-account.md#link-storage-accounts) to configure your Azure Remote Rendering with your blob storage accounts (Jak połączyć konta magazynu, aby skonfigurować Azure Remote Rendering z kontami magazynu obiektów blob.

W przypadku korzystania z połączonego magazynu obiektów blob użyjesz nieco innych metod ładowania modeli:

```cs
var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams);
```

Powyższe wiersze używają `FromSas` wersji params i akcji sesji. Należy je przekonwertować na wersje inne niż sas:

```cs
var loadModelParams = new LoadModelOptions(storageAccountPath, blobContainerName, modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelAsync(loadModelParams);
```

Zmodyfikujmy **remoteRenderingCoordinator,** aby załadować model niestandardowy z połączonego konta magazynu obiektów blob.

1. Jeśli jeszcze tego nie zdążę, wykonaj 2019 3000: Łączenie kont magazynu, aby udzielić wystąpieniu usługi ARR uprawnień dostępu do Blob Storage magazynu. [](../../../how-tos/create-an-account.md#link-storage-accounts)
1. Dodaj następującą **zmodyfikowaną metodę LoadModel** do **funkcji RemoteRenderingCoordinator** tuż poniżej bieżącej **metody LoadModel:**

    ```cs
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, Action<float> progress = null)
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
        var loadModelParams = new LoadModelOptions($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

    W większości przypadków ten kod jest identyczny z oryginalną metodą, jednak wersja sygnatury dostępu współdzielonego wywołań metody została zastąpiona wersjami innymi niż `LoadModel` SAS.

    Dodatkowe dane wejściowe `storageAccountName` i zostały również dodane do `blobContainerName` argumentów. Wywołamy tę nową metodę **LoadModel** z innej metody podobnej do pierwszej metody **LoadTestModel** utworzonej w pierwszym samouczku.

1. Dodaj następującą metodę do **funkcji RemoteRenderingCoordinator** tuż po **modelu LoadTestModel**

    ```cs
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    Ten kod dodaje trzy dodatkowe zmienne ciągu do **składnika RemoteRenderingCoordinator.**
    ![Zrzut ekranu przedstawiający nazwę konta magazynu, nazwę kontenera obiektów blob i ścieżkę modelu składnika RemoteRenderingCoordinator.](./media/storage-account-linked-model.png)

1. Dodaj wartości do **składnika RemoteRenderingCoordinator.** Po wylieniu [przewodnika Szybki start na temat konwersji modelu](../../../quickstarts/convert-model.md)wartości powinny być:

    * **Nazwa konta magazynu:** nazwa konta magazynu, globalnie unikatowa nazwa wybierana dla konta magazynu. W tym przewodniku Szybki start *był to adres arrtutorialstorage.* Wartość będzie inna.
    * **Nazwa kontenera obiektów blob:** arroutput, Blob Storage kontenera
    * **Ścieżka modelu:** kombinacja elementów "outputFolderPath" i "outputAssetFileName" zdefiniowanych w *arrconfig.jspliku.* W tym przewodniku Szybki start były to wartości "outputFolderPath":"converted/robot", "outputAssetFileName": "robot.arrAsset". W wyniku czego wartość ścieżki modelu "converted/robot/robot.arrAsset" będzie inna.

    >[!TIP]
    > Jeśli [uruchamiasz  ](../../../quickstarts/convert-model.md#run-the-conversion) skryptConversion.ps1bez argumentu "-UseContainerSas", skrypt wyprowadzi wszystkie powyższe wartości dla Twojego tokenu zamiast tokenu SAS. ![Połączony model](./media/converted-output.png)
1. Na razie usuń lub wyłącz model **TestModel** GameObject, aby zrobić miejsce na załadowanie modelu niestandardowego.
1. Odtwarzanie sceny i nawiązywanie połączenia z sesją zdalną.
1. Kliknij prawym przyciskiem myszy pozycję **RemoteRenderingCoordinator** i wybierz **polecenie Załaduj połączony model niestandardowy.**
    ![Ładowanie połączonego modelu](./media/load-linked-model.png)

Te kroki zwiększyły bezpieczeństwo aplikacji przez usunięcie tokenu SAS z aplikacji lokalnej.

Teraz bieżący stan aplikacji i jej dostęp do zasobów platformy Azure wygląda następująco:

![Lepsze zabezpieczenia](./media/security-two.png)

Mamy jeszcze jedno "hasło", AccountKey, do usunięcia z aplikacji lokalnej. Można to zrobić przy użyciu Azure Active Directory (AAD).

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure Active Directory (Azure AD)

Uwierzytelnianie usługi AAD pozwala określić, które osoby lub grupy będą używać usługi ARR w bardziej kontrolowany sposób. ARR ma wbudowaną obsługę akceptowania [tokenów dostępu](../../../../active-directory/develop/access-tokens.md) zamiast używania klucza konta. Tokeny dostępu można nazwać ograniczonym czasowo, specyficznym dla użytkownika kluczem, który odblokowuje tylko niektóre części określonego zasobu, o który zażądano.

Skrypt **RemoteRenderingCoordinator** ma delegata o nazwie **ARRCredentialGetter,** który przechowuje metodę, która zwraca obiekt **SessionConfiguration,** który służy do konfigurowania zarządzania sesją zdalną. Możemy przypisać inną metodę do obiektu **ARRCredentialGetter,** dzięki czemu możemy użyć przepływu logowania platformy Azure, generując obiekt **SessionConfiguration** zawierający token dostępu platformy Azure. Ten token dostępu będzie specyficzny dla użytkownika, który się loguje.

1. Postępuj zgodnie [z instrukcjami: konfigurowanie](../../../how-tos/authentication.md#authentication-for-deployed-applications)uwierzytelniania — uwierzytelnianie dla wdrożonych aplikacji. W szczególności postępuj zgodnie z instrukcjami podanymi w dokumentacji usługi Azure Spatial Anchors uwierzytelnianie użytkowników w [usłudze Azure AD.](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) Obejmuje to zarejestrowanie nowej aplikacji Azure Active Directory i skonfigurowanie dostępu do wystąpienia ARR.
1. Po skonfigurowaniu nowej aplikacji AAD sprawdź, czy aplikacja AAD wygląda jak na poniższych obrazach:

    **Aplikacja usługi AAD — uwierzytelnianie > usługi AAD** ![ Uwierzytelnianie aplikacji](./media/app-authentication-public.png)

    **Aplikacja usługi AAD — > interfejsu API** ![ Interfejsy API aplikacji](./media/request-api-permissions-step-five.png)

1. Po skonfigurowaniu konta Remote Rendering sprawdź, czy konfiguracja wygląda jak na poniższej ilustracji:

    **AAR -> AccessControl (IAM)** ![ Rola ARR](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > Rola *właściciela* nie wystarcza do zarządzania sesjami za pośrednictwem aplikacji klienckiej. Dla każdego użytkownika, który chcesz przyznać możliwość zarządzania sesjami, musisz podać rolę Remote Rendering **Client.** Dla każdego użytkownika, który ma zarządzać sesjami i konwertować modele, musisz podać rolę Remote Rendering **Administrator.**

Po stronie platformy Azure musimy zmodyfikować sposób, w jaki kod łączy się z usługą AAR. W tym celu implementuje się wystąpienie klasy **BaseARRAuthentication,** które zwraca nowy **obiekt SessionConfiguration.** W takim przypadku informacje o koncie zostaną skonfigurowane przy użyciu tokenu dostępu platformy Azure.

1. Utwórz nowy skrypt o nazwie **AADAuthentication** i zastąp jego kod następującym:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;
    
    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }
    
        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingDomain;
        public string AzureRemoteRenderingDomain
        {
            get => azureRemoteRenderingDomain.Trim();
            set => azureRemoteRenderingDomain = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountDomain;
        public string AzureRemoteRenderingAccountDomain
        {
            get => azureRemoteRenderingAccountDomain.Trim();
            set => azureRemoteRenderingAccountDomain = value;
        }    
    
        public override event Action<string> AuthenticationInstructions;
    
        string authority => "https://login.microsoftonline.com/" + AzureTenantID;
    
        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";
    
        string[] scopes => new string[] { "https://sts." + AzureRemoteRenderingAccountDomain + "/mixedreality.signin" };
    
        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }
    
        public async override Task<SessionConfiguration> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);
    
                var AD_Token = result.AccessToken;
    
                return await Task.FromResult(new SessionConfiguration(AzureRemoteRenderingAccountDomain, AzureRemoteRenderingDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }
    
        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });
    
            return Task.FromResult(0);
        }
    
        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();
    
                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();
    
                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }
    
            return null;
        }
    }
    ```

>[!NOTE]
> Ten kod nie jest w żaden sposób kompletny i nie jest gotowy do użycia w komercyjnej aplikacji. Na przykład w minimalnym przypadku prawdopodobnie chcesz również dodać możliwość wylogowania się. Można to zrobić przy użyciu `Task RemoveAsync(IAccount account)` metody dostarczonej przez aplikację kliencyjną. Ten kod jest przeznaczony tylko do użycia w samouczku. Implementacja będzie specyficzna dla Aplikacji.

Kod najpierw próbuje uzyskać token w trybie dyskretnym przy użyciu **AquireTokenSilent**. Ta instrukcja powiedzie się, jeśli użytkownik wcześniej uwierzytelnił tę aplikację. Jeśli to się nie powiedzie, przejdź do strategii bardziej zaangażowanej przez użytkowników.

W przypadku tego kodu używamy przepływu kodu [urządzenia w](../../../../active-directory/develop/v2-oauth2-device-code.md) celu uzyskania tokenu dostępu. Ten przepływ umożliwia użytkownikowi zalogowanie się do konta platformy Azure na komputerze lub urządzeniu przenośnym i wysłanie wynikowego tokenu z powrotem do aplikacji HoloLens.

Najważniejszą częścią tej klasy z perspektywy ARR jest ten wiersz:

```cs
return await Task.FromResult(new SessionConfiguration(AzureRemoteRenderingAccountDomain, AzureRemoteRenderingDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

W tym miejscu utworzymy nowy obiekt **SessionConfiguration** przy użyciu domeny renderowania zdalnego, identyfikatora konta, domeny konta i tokenu dostępu. Ten token jest następnie używany przez usługę ARR do wykonywania zapytań, tworzenia i dołączania sesji renderowania zdalnego, o ile użytkownik jest autoryzowany na podstawie skonfigurowanych wcześniej uprawnień opartych na rolach.

Po tej zmianie bieżący stan aplikacji i jej dostęp do zasobów platformy Azure wygląda następująco:

![Jeszcze lepsze zabezpieczenia](./media/security-three.png)

Ponieważ poświadczenia użytkownika nie są przechowywane na urządzeniu (lub w tym przypadku nawet wprowadzone na urządzeniu), ryzyko ich narażenia jest bardzo niskie. Teraz urządzenie używa specyficznego dla użytkownika, ograniczonego czasowo tokenu dostępu do uzyskiwania dostępu do usługi ARR, która używa kontroli dostępu (IAM) do uzyskiwania dostępu do Blob Storage. Te dwa kroki całkowicie usunęliśmy "hasła" z kodu źródłowego i znacznie zwiększyliśmy bezpieczeństwo. Nie jest to jednak najbardziej dostępne bezpieczeństwo. Przeniesienie modelu i zarządzania sesjami do usługi internetowej dodatkowo poprawi bezpieczeństwo. Dodatkowe zagadnienia dotyczące zabezpieczeń zostały omówione w rozdziale [Gotowość komercyjna.](../commercial-ready/commercial-ready.md)

### <a name="testing-aad-auth"></a>Testowanie uwierzytelniania WAD

W edytorze aparatu Unity, gdy uwierzytelnianie usługi AAD jest aktywne, konieczne będzie uwierzytelnienie przy każdym uruchomieniu aplikacji. Na urządzeniu krok uwierzytelniania będzie się odbywał po raz pierwszy i będzie wymagany ponownie tylko wtedy, gdy token wygaśnie lub zostanie unieważniony.

1. Dodaj składnik **AADAuthentication** do elementu GameObject **RemoteRenderingCoordinator.**

    ![Składnik uwierzytelniania usługi AAD](./media/azure-active-directory-auth-component.png)

1. Wypełnij wartości w polach Client ID (Identyfikator klienta) i Tenant ID (Identyfikator dzierżawy). Te wartości można znaleźć na stronie przeglądu rejestracji aplikacji:

    * **Identyfikator klienta aplikacji usługi Active Directory** to identyfikator aplikacji *(klienta)* znaleziony w rejestracji aplikacji usługi AAD (zobacz obraz poniżej).
    * **Identyfikator dzierżawy platformy Azure** to *identyfikator katalogu (dzierżawy)* znaleziony w rejestracji aplikacji usługi AAD (zobacz obraz poniżej).
    * **Azure Remote Rendering domeny to** ta sama domena, która była już używana w domenie Remote Rendering **RemoteRenderingCoordinator.**
    * **Azure Remote Rendering konto jest** tym **samym** identyfikatorem konta, który był już w przypadku **remoteRenderingCoordinator.**
    * **Azure Remote Rendering konto jest** tej **samej** domeny konta, która była używana w **RemoteRenderingCoordinator.**

    ![Zrzut ekranu przedstawiający identyfikator aplikacji (klienta) i identyfikator katalogu (dzierżawy).](./media/app-overview-data.png)

1. Naciśnij klawisz Play w edytorze aparatu Unity i wyrażaj zgodę na uruchamianie sesji.
    Ponieważ składnik **AADAuthentication** ma kontroler widoku, jest automatycznie podłączony w celu wyświetlenia monitu po panelu modalnego autoryzacji sesji.
1. Postępuj zgodnie z instrukcjami w panelu po prawej stronie **appMenu.**
    Powinien zostać wyświetlony ekran podobny do poniższego: Ilustracja przedstawiająca panel instrukcji wyświetlany po prawej stronie ![ menu AppMenu.](./media/device-flow-instructions.png)
    
    Po wprowadzeniu podanego kodu na urządzeniu pomocniczym (lub przeglądarce na tym samym urządzeniu) i zalogowaniu się przy użyciu poświadczeń do aplikacji żądającego zostanie zwrócony token dostępu , w tym przypadku do edytora aparatu Unity.

Po tym punkcie wszystko w aplikacji powinno przebiegać normalnie. Sprawdź, czy w konsoli aparatu Unity występują błędy, jeśli nie przechodzisz przez etapy zgodnie z oczekiwaniami.

## <a name="build-to-device"></a>Kompilowanie na urządzeniu

W przypadku tworzenia aplikacji na urządzeniu przy użyciu usługi MSAL należy dołączyć plik do folderu **Assets projektu.** Pomoże to kompilatorowi poprawnie skompilować aplikację przy użyciu *Microsoft.Identity.Client.dll* zawartych w zasoby **samouczka**.

1. Dodaj nowy plik w pliku **Assets** o **nazwielink.xml**
1. Dodaj następujący kod do pliku :

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. Zapisz zmiany

Wykonaj kroki opisane w przewodniku Szybki start: wdrażanie przykładu aparatu Unity na [urządzeniach HoloLens](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project)— kompilowanie przykładowego projektu , aby skompilować na urządzeniach HoloLens.

## <a name="next-steps"></a>Następne kroki

Pozostała część tego zestawu samouczków zawiera tematy koncepcyjne dotyczące tworzenia aplikacji gotowej do użycia w środowisku produkcyjnym, która używa Azure Remote Rendering.

> [!div class="nextstepaction"]
> [Dalej: Gotowość komercyjna](../commercial-ready/commercial-ready.md)