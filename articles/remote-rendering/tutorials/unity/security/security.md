---
title: Zabezpieczanie usługi Azure Remote Rendering i magazynu modeli
description: Ograniczanie funkcjonalności zdalnego renderowania aplikacji do zabezpieczania zawartości
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 200d23f390c9c22af90099e1e136c832287aa10d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207533"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Samouczek: Zabezpieczanie zdalnego renderowania i magazynu modeli na platformie Azure

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Zabezpiecz Blob Storage platformy Azure zawierające modele renderowania zdalnego platformy Azure
> * Uwierzytelnianie za pomocą usługi Azure AD w celu uzyskania dostępu do wystąpienia zdalnego renderowania na platformie Azure
> * Użyj poświadczeń platformy Azure na potrzeby uwierzytelniania zdalnego renderowania na platformie Azure

## <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek kompiluje się w [samouczku: rafinacja materiałów, oświetlenia i efektów](..\materials-lighting-effects\materials-lighting-effects.md).

## <a name="why-additional-security-is-needed"></a>Dlaczego jest wymagana dodatkowa ochrona

Bieżący stan aplikacji i jej dostęp do zasobów platformy Azure wygląda następująco:

![Zabezpieczenia wstępne](./media/security-one.png)

Zarówno identyfikator "AccountID + AccountKey", jak i "adres URL i token SAS" są zasadniczo przechowujące nazwę użytkownika i hasło. Na przykład, jeśli "AccountID + AccountKey" były widoczne, osoba atakująca będzie mogła korzystać z zasobów ARR bez zgody użytkownika na wydatki.

## <a name="securing-your-content-in-azure-blob-storage"></a>Zabezpieczanie zawartości w usłudze Azure Blob Storage

Zdalne renderowanie na platformie Azure pozwala bezpiecznie uzyskać dostęp do zawartości Blob Storage platformy Azure przy użyciu poprawnej konfiguracji. Zobacz [instrukcje: Łączenie kont magazynu](../../../how-tos/create-an-account.md#link-storage-accounts) w celu skonfigurowania wystąpienia renderowania zdalnego platformy Azure przy użyciu kont usługi BLOB Storage.

W przypadku korzystania z połączonego magazynu obiektów BLOB używane są nieco inne metody ładowania modeli:

```csharp
var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
```

W powyższych wierszach jest używana `FromSAS` wersja parametrów i akcja sesji. Muszą zostać przekonwertowane na wersje inne niż SAS:

```csharp
var loadModelParams = new LoadModelParams(storageAccountPath, blobContainerName, modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
```

Zmodyfikujmy **RemoteRenderingCoordinator** w celu załadowania modelu niestandardowego z połączonego konta usługi BLOB Storage.

1. Jeśli jeszcze tego nie zrobiono, wykonaj [następujące czynności: Łączenie kont magazynu](../../../how-tos/create-an-account.md#link-storage-accounts) w celu udzielenia uprawnienia do wystąpienia ARR w celu uzyskania dostępu do wystąpienia BLOB Storage.
1. Dodaj następującą zmodyfikowaną metodę **LoadModel** do **RemoteRenderingCoordinator** tuż poniżej bieżącej metody **LoadModel** :

    ```csharp
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

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
        var loadModelParams = new LoadModelParams($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
        if (progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

    W większości przypadków ten kod jest identyczny z oryginalną `LoadModel` metodą, jednak zastępujemy wersję SAS metody wywołaniami z wersjami innymi niż SAS.

    Dodatkowe dane wejściowe `storageAccountName` i `blobContainerName` zostały również dodane do argumentów. Wywołamy tę nową metodę **LoadModel** z innej metody podobnej do pierwszej **LoadTestModel** metody utworzonej w pierwszym samouczku.

1. Dodaj następującą metodę do **RemoteRenderingCoordinator** po **LoadTestModel**

    ```csharp
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

    Ten kod dodaje trzy dodatkowe zmienne ciągu do składnika **RemoteRenderingCoordinator** .
    ![Zrzut ekranu, który wyróżnia nazwę konta magazynu, nazwę kontenera obiektów blob i ścieżkę modelu składnika RemoteRenderingCoordinator.](./media/storage-account-linked-model.png)

1. Dodaj wartości do składnika **RemoteRenderingCoordinator** . Po przeprowadzeniu [szybkiego startu do konwersji modelu](../../../quickstarts/convert-model.md)należy wykonać następujące wartości:

    * **Nazwa konta magazynu**: nazwa konta magazynu, globalnie unikatowa nazwa wybrana dla konta magazynu. W tym przewodniku Szybki Start *arrtutorialstorage*wartość będzie różna.
    * **Nazwa kontenera obiektów BLOB**: Arroutput, kontener BLOB Storage
    * **Ścieżka modelu**: kombinacja "outputFolderPath" i "outputAssetFileName" zdefiniowanego w *arrconfig.jsw* pliku. W tym przewodniku szybki start był to "outputFolderPath": "skonwertowane/robotne", "outputAssetFileName": "Robot. arrAsset". W efekcie wartość ścieżki modelu "skonwertowane/Robot/Robot. arrAsset" będzie różna.

    >[!TIP]
    > W przypadku [uruchomienia skryptu **Conversion.ps1** ](../../../quickstarts/convert-model.md#run-the-conversion) bez argumentu "-UseContainerSas" skrypt będzie wyprowadzał wszystkie powyższe wartości zamiast tokenu SAS. ![Połączony model](./media/converted-output.png)
1. Na czas należy usunąć lub wyłączyć **TestModel**gryobject, aby zwolnić miejsce na załadowanie modelu niestandardowego.
1. Odtwórz scenę i Połącz się z sesją zdalną.
1. Kliknij prawym przyciskiem myszy **RemoteRenderingCoordinator** i wybierz pozycję **Załaduj połączony model niestandardowy**.
    ![Załaduj połączony model](./media/load-linked-model.png)

Te kroki zwiększyły bezpieczeństwo aplikacji przez usunięcie tokenu sygnatury dostępu współdzielonego z lokalnej aplikacji.

Teraz bieżący stan aplikacji i jej dostęp do zasobów platformy Azure wygląda następująco:

![Lepsze zabezpieczenia](./media/security-two.png)

Mamy jeszcze jeden "hasło", czyli AccountKey do usunięcia z lokalnej aplikacji. Można to zrobić przy użyciu uwierzytelniania Azure Active Directory (AAD).

## <a name="azure-active-directory-azure-ad-authentication"></a>Uwierzytelnianie Azure Active Directory (Azure AD)

Uwierzytelnianie w usłudze AAD umożliwi określenie, którzy użytkownicy lub grupy korzystają z funkcji ARR w bardziej kontrolowany sposób. ARR ma wbudowaną obsługę akceptowania [tokenów dostępu](../../../../active-directory/develop/access-tokens.md) zamiast korzystania z klucza konta. Tokeny dostępu można traktować jako ograniczoną czasowo klucz specyficzny dla użytkownika, który odblokowuje tylko niektóre części konkretnego zasobu, którego żądała.

Skrypt **RemoteRenderingCoordinator** ma delegata o nazwie **ARRCredentialGetter**, który zawiera metodę zwracającą obiekt **AzureFrontendAccountInfo** , który jest używany do konfigurowania zarządzania sesją zdalną. Możemy przypisać do **ARRCredentialGetter**inną metodę, umożliwiając nam korzystanie z usługi Azure Signing w usłudze Flow, generując obiekt **AzureFrontendAccountInfo** , który zawiera token dostępu platformy Azure. Ten token dostępu będzie specyficzny dla użytkownika, który loguje się.

1. Postępuj zgodnie z instrukcjami w temacie [jak skonfigurować uwierzytelnianie-uwierzytelnianie dla wdrożonych aplikacji](../../../how-tos/authentication.md#authentication-for-deployed-applications). w tym celu wykonaj instrukcje wymienione w dokumentacji kotwice przestrzenne platformy Azure [uwierzytelnianie użytkowników w usłudze Azure AD](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication). Obejmuje to rejestrowanie nowej aplikacji Azure Active Directory i Konfigurowanie dostępu do wystąpienia ARR.
1. Po skonfigurowaniu nowej aplikacji usługi AAD Sprawdź, czy aplikacja AAD wygląda podobnie do następujących obrazów:

    **Aplikacja usługi AAD — uwierzytelnianie >** ![ Uwierzytelnianie aplikacji](./media/app-authentication-public.png)

    Uprawnienia interfejsu API **> aplikacji usługi AAD** ![ Interfejsy API aplikacji](./media/request-api-permissions-step-five.png)

1. Po skonfigurowaniu konta renderowania zdalnego Sprawdź, czy konfiguracja wygląda następująco:

    **AAR — > AccessControl (IAM)** ![ Rola ARR](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > Rola *właściciela* nie jest wystarczająca do zarządzania sesjami za pośrednictwem aplikacji klienckiej. Dla każdego użytkownika, którym chcesz udzielić możliwości zarządzania sesjami, musisz zapewnić **klientowi zdalne renderowanie**roli. Dla każdego użytkownika, który ma zarządzać sesjami i konwertować modele, musisz zapewnić **administratorowi zdalnego renderowania**roli.

Po zalogowaniu się po stronie platformy Azure należy zmodyfikować sposób, w jaki kod nawiązuje połączenie z usługą AAR. Możemy to zrobić, implementując wystąpienie elementu **BaseARRAuthentication**, które zwróci nowy obiekt **AzureFrontendAccountInfo** . W takim przypadku informacje o koncie zostaną skonfigurowane przy użyciu tokenu dostępu platformy Azure.

1. Utwórz nowy skrypt o nazwie **AADAuthentication** i zastąp go następującym kodem:

    ```csharp
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
        private string accountDomain;
        public string AccountDomain
        {
            get => accountDomain.Trim();
            set => accountDomain = value;
        }

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
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }

        public override event Action<string> AuthenticationInstructions;

        string authority => "https://login.microsoftonline.com/" + AzureTenantID;

        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";

        string[] scopes => new string[] { "https://sts.mixedreality.azure.com/mixedreality.signin" };

        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }

        public async override Task<AzureFrontendAccountInfo> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);

                var AD_Token = result.AccessToken;

                return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
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
> Ten kod jest przez nie oznacza kompletny i nie jest gotowy do aplikacji komercyjnej. Na przykład najprawdopodobniej zechcesz dodać możliwość wylogowania. Można to zrobić za pomocą `Task RemoveAsync(IAccount account)` metody dostarczonej przez aplikację kliencką. Ten kod jest przeznaczony tylko do użycia w samouczkach, implementacja będzie konkretna dla Twojej aplikacji.

Kod najpierw próbuje uzyskać token dyskretnie przy użyciu **AquireTokenSilent**. Ten stan zakończy się pomyślnie, jeśli użytkownik wcześniej uwierzytelnił tę aplikację. Jeśli to się nie powiedzie, przechodź do większej strategii związanej z użytkownikami.

W przypadku tego kodu używamy [przepływu kodu urządzenia](../../../../active-directory/develop/v2-oauth2-device-code.md) w celu uzyskania tokenu dostępu. Ten przepływ umożliwia użytkownikowi zalogowanie się do swojego konta platformy Azure na komputerze lub urządzeniu przenośnym, a następnie wysłanie otrzymanego tokenu z powrotem do aplikacji HoloLens.

Najważniejszym elementem tej klasy z perspektywy ARR jest ten wiersz:

```csharp
return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

W tym miejscu tworzymy nowy obiekt **AzureFrontendAccountInfo** przy użyciu domeny konta, identyfikatora konta i tokenu dostępu. Token ten jest następnie używany przez usługę ARR do wykonywania zapytań, tworzenia i dołączania zdalnych sesji renderowania, o ile użytkownik jest autoryzowany na podstawie uprawnień opartych na rolach skonfigurowanych wcześniej.

W przypadku tej zmiany bieżący stan aplikacji i jej dostęp do zasobów platformy Azure wygląda następująco:

![Jeszcze lepsze zabezpieczenia](./media/security-three.png)

Ponieważ poświadczenia użytkownika nie są przechowywane na urządzeniu (lub w tym przypadku jest to nawet wprowadzane na urządzeniu), ryzyko narażenia jest bardzo niskie. Teraz urządzenie korzysta z tokenu dostępu ograniczonego przez użytkownika, aby uzyskać dostęp do funkcji ARR, która używa kontroli dostępu (IAM) w celu uzyskania dostępu do Blob Storage. Te dwa kroki zostały całkowicie usunięte "hasła" z kodu źródłowego i znacząco zwiększone zabezpieczenia. Jednak nie jest to największa dostępna dostępność, przeniesienie modelu i zarządzania sesją do usługi sieci Web spowoduje dalsze zwiększenie bezpieczeństwa. Dodatkowe zagadnienia dotyczące zabezpieczeń zostały omówione w rozdziale dotyczącej [gotowości komercyjnej](../commercial-ready/commercial-ready.md) .

### <a name="testing-aad-auth"></a>Testowanie uwierzytelniania usługi AAD

W edytorze aparatu Unity, gdy uwierzytelnianie w usłudze AAD jest aktywne, należy uwierzytelnić się za każdym razem, gdy uruchamiasz aplikację. Na urządzeniu krok uwierzytelniania zostanie wykonany po raz pierwszy i będzie wymagany ponownie tylko wtedy, gdy token wygaśnie lub zostanie unieważniony.

1. Dodaj składnik **AADAuthentication** do **RemoteRenderingCoordinator** gameobject.

    ![Składnik uwierzytelniania usługi AAD](./media/azure-active-directory-auth-component.png)

1. Wprowadź wartości w polu Identyfikator klienta i identyfikator dzierżawy. Te wartości można znaleźć na stronie Przegląd rejestracji aplikacji:

    * **Domena konta** jest tą samą domeną, która była używana w domenie konta **RemoteRenderingCoordinator**.
    * **Identyfikator klienta aplikacji Active Directory** to *Identyfikator aplikacji (klienta)* znaleziony w rejestracji aplikacji usługi AAD (zobacz poniższy obraz).
    * **Identyfikator dzierżawy platformy Azure** to *Identyfikator katalogu (dzierżawy)* znaleziony w rejestracji aplikacji usługi AAD (zobacz poniższy obraz).
    * **Identyfikator konta renderowania zdalnego platformy Azure** to ten sam **Identyfikator konta** , którego użyto do **RemoteRenderingCoordinator**.

    ![Zrzut ekranu, który podświetla identyfikator aplikacji (klienta) i identyfikator katalogu (dzierżawy).](./media/app-overview-data.png)

1. Naciśnij przycisk Odtwórz w edytorze aparatu Unity i wyrażasz zgodę na uruchomienie sesji.
    Ze względu na to, że składnik **AADAuthentication** ma kontroler widoku, jego automatycznie podłączany do wyświetlenia monitu po modalnym panelu autoryzacji sesji.
1. Postępuj zgodnie z instrukcjami znajdującymi się w panelu z prawej strony **AppMenu**.
    Powinieneś zobaczyć coś podobnego do tego: ![ ilustracja przedstawiająca panel instrukcji, który pojawia się po prawej stronie AppMenu.](./media/device-flow-instructions.png)
    Po wprowadzeniu zakodowanego na urządzeniu pomocniczym (lub w przeglądarce na tym samym urządzeniu) i zalogowaniu się przy użyciu poświadczeń token dostępu zostanie zwrócony do aplikacji żądającej, w tym przypadku edytora aparatu Unity.
1. Po tym momencie wszystko w aplikacji powinno działać normalnie. Sprawdź, czy w konsoli aparatu Unity zostały wykryte błędy, jeśli nie postępują zgodnie z oczekiwaniami.

## <a name="build-to-device"></a>Kompiluj na urządzenie

Jeśli tworzysz aplikację przy użyciu MSAL do urządzenia, musisz dołączyć plik do folderu **zasobów** projektu. Pomoże to kompilatorowi poprawnie skompilować aplikację przy użyciu *Microsoft.Identity.Client.dll* zawartej w **zasobach samouczka**.

1. Dodaj nowy plik do **zasobów** o nazwie **link.xml**
1. Dodaj następujący plik do pliku:

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

Wykonaj kroki opisane w [przewodniku szybki start: wdrażanie przykładu aparatu Unity w celu przetworzenia](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project)przykładowego projektu w celu skompilowania do programu HoloLens.

## <a name="next-steps"></a>Następne kroki

Pozostała część tego zestawu samouczka zawiera tematy dotyczące pojęć związanych z tworzeniem aplikacji gotowej do produkcji, która korzysta z zdalnego renderowania platformy Azure.

> [!div class="nextstepaction"]
> [Dalej: gotowość komercyjna](../commercial-ready/commercial-ready.md)