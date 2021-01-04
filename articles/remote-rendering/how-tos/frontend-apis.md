---
title: Interfejsy API frontonu platformy Azure do uwierzytelniania
description: Wyjaśnia, jak używać interfejsu API frontonu języka C# do uwierzytelniania
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 8042e1d3f93b870cdc669628a28fcbad54b69150
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724849"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Uwierzytelnianie przy użyciu interfejsów API frontonu platformy Azure

W tej sekcji opisano sposób korzystania z interfejsu API do zarządzania uwierzytelnianiem i sesją.

> [!CAUTION]
> Funkcje opisane w tym rozdziale generują wewnętrznie wywołania REST na serwerze. Podobnie jak w przypadku wszystkich wywołań REST, wysłanie tych poleceń zbyt często spowoduje ograniczenie i zwrócenie błędu. Wartością `SessionGeneralContext.HttpResponseCode` elementu członkowskiego w tym przypadku jest 429 ("zbyt wiele żądań"). Zgodnie z zasadą dla elementu kciuka należy mieć opóźnienie **5-10 sekund między kolejnymi wywołaniami**.


## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo służy do konfigurowania informacji uwierzytelniania dla ```AzureFrontend``` wystąpienia w zestawie SDK.

Ważne są następujące pola:

```cs

public class AzureFrontendAccountInfo
{
    // Domain that will be used for account authentication for the Azure Remote Rendering service, in the form [region].mixedreality.azure.com.
    // [region] should be set to the domain of the Azure Remote Rendering account.
    public string AccountAuthenticationDomain;
    // Domain that will be used to generate sessions for the Azure Remote Rendering service, in the form [region].mixedreality.azure.com.
    // [region] should be selected based on the region closest to the user. For example, westus2.mixedreality.azure.com or westeurope.mixedreality.azure.com.
    public string AccountDomain;

    // Can use one of:
    // 1) ID and Key.
    // 2) ID and AuthenticationToken.
    // 3) ID and AccessToken.
    public string AccountId = Guid.Empty.ToString();
    public string AccountKey = string.Empty;
    public string AuthenticationToken = string.Empty;
    public string AccessToken = string.Empty;
}
```

Odpowiednik w języku C++ wygląda następująco:

```cpp
struct AzureFrontendAccountInfo
{
    std::string AccountAuthenticationDomain{};
    std::string AccountDomain{};
    std::string AccountId{};
    std::string AccountKey{};
    std::string AuthenticationToken{};
    std::string AccessToken{};
};
```

W przypadku części _regionu_ w domenie Użyj [regionu w sąsiedztwie](../reference/regions.md).

Informacje o koncie można uzyskać z portalu zgodnie z opisem w akapicie [pobieranie informacji o koncie](create-an-account.md#retrieve-the-account-information) .

## <a name="azure-frontend"></a>Fronton platformy Azure

Odpowiednie klasy to ```AzureFrontend``` i ```AzureSession``` . ```AzureFrontend``` służy do zarządzania kontami i funkcji na poziomie konta, w tym: konwersja zasobów i Tworzenie sesji renderowania. ```AzureSession``` służy do obsługi funkcji na poziomie sesji i obejmuje: aktualizowanie sesji, zapytania, odnawianie i likwidowanie.

Każda otwarta/utworzona ```AzureSession``` będzie przechowywać odwołanie do frontonu, który go utworzył. Aby wyczyścić ten stan, wszystkie sesje muszą zostać cofnięte, zanim zostanie cofnięta alokacja frontonu.

Cofnięcie przydziału sesji nie spowoduje zatrzymania serwera na platformie Azure, `AzureSession.StopAsync` musi być jawnie wywołana.

Po utworzeniu sesji i jej stanie została oznaczona jako gotowa, można nawiązać połączenie ze środowiskiem uruchomieniowym renderowania zdalnego przy użyciu `AzureSession.ConnectToRuntime` .

### <a name="threading"></a>Wątkowość

Wszystkie wywołania asynchroniczne AzureSession i AzureFrontend są wykonywane w wątku w tle, a nie w wątku aplikacji głównej.

### <a name="conversion-apis"></a>Interfejsy API konwersji

Aby uzyskać więcej informacji na temat usługi konwersji, zobacz [interfejs API REST konwersji modeli](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Rozpocznij konwersję zasobów

```cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string storageContainer, string blobinputpath, string bloboutpath, string modelName, string outputName)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionInputParams(storageContainer, blobinputpath, "", modelName),
        new AssetConversionOutputParams(storageContainer, bloboutpath, "", outputName)
        );
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

```cpp
void StartAssetConversion(ApiHandle<AzureFrontend> frontend, std::string storageContainer, std::string blobinputpath, std::string bloboutpath, std::string modelName, std::string outputName)
{
    AssetConversionInputParams input;
    input.BlobContainerInformation.BlobContainerName = blobinputpath;
    input.BlobContainerInformation.StorageAccountName = storageContainer;
    input.BlobContainerInformation.FolderPath = "";
    input.InputAssetPath = modelName;

    AssetConversionOutputParams output;
    output.BlobContainerInformation.BlobContainerName = blobinputpath;
    output.BlobContainerInformation.StorageAccountName = storageContainer;
    output.BlobContainerInformation.FolderPath = "";
    output.OutputAssetPath = outputName;

    ApiHandle<StartAssetConversionAsync> conversionAsync = *frontend->StartAssetConversionAsync(input, output);
    conversionAsync->Completed([](ApiHandle<StartAssetConversionAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res.Result
        }
        else
        {
            printf("Failed to start asset conversion!");
        }
    }
    );
}
```


#### <a name="get-conversion-status"></a>Pobierz stan konwersji

```cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetAssetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

```cpp
void GetConversionStatus(ApiHandle<AzureFrontend> frontend, std::string assetId)
{
    ApiHandle<ConversionStatusAsync> pendingAsync = *frontend->GetAssetConversionStatusAsync(assetId);
    pendingAsync->Completed([](ApiHandle<ConversionStatusAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            // use res->Result
        }
        else
        {
            printf("Failed to get status of asset conversion!");
        }

    });
}
```


### <a name="rendering-apis"></a>Renderowanie interfejsów API

Aby uzyskać szczegółowe informacje na temat zarządzania sesją [, zobacz Interfejs API REST zarządzania sesją](session-rest-api.md) .

Sesję renderowania można utworzyć dynamicznie w usłudze lub już istniejący identyfikator sesji może być "otwarty" w obiekcie AzureSession.

#### <a name="create-rendering-session"></a>Utwórz sesję renderowania

```cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend, RenderingSessionVmSize vmSize, const ARRTimeSpan& maxLease)
{
    RenderingSessionCreationParams params;
    params.MaxLease = maxLease;
    params.Size = vmSize;
    ApiHandle<CreateSessionAsync> pendingAsync = *frontend->CreateNewRenderingSessionAsync(params);

    pendingAsync->Completed([] (ApiHandle<CreateSessionAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result
        }
        else
        {
            printf("Failed to create session!");
        }
    });
}
```

#### <a name="open-an-existing-rendering-session"></a>Otwórz istniejącą sesję renderowania

Otwarcie istniejącej sesji to wywołanie synchroniczne.

```cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend, std::string sessionId)
{
    ApiHandle<AzureSession> session = *frontend->OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```


#### <a name="get-current-rendering-sessions"></a>Pobierz bieżące sesje renderowania

```cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void GetCurrentRenderingSessions(ApiHandle<AzureFrontend> frontend)
{
    ApiHandle<SessionPropertiesArrayAsync> pendingAsync = *frontend->GetCurrentRenderingSessionsAsync();
    pendingAsync->Completed([](ApiHandle<SessionPropertiesArrayAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            // use res.Result
        }
        else
        {
            printf("Failed to get current rendering sessions!");
        }
    });
}
```

### <a name="session-apis"></a>Interfejsy API sesji

#### <a name="get-rendering-session-properties"></a>Pobierz właściwości sesji renderowania

```cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void GetRenderingSessionProperties(ApiHandle<AzureSession> session)
{
    ApiHandle<SessionPropertiesAsync> pendingAsync = *session->GetPropertiesAsync();
    pendingAsync->Completed([](ApiHandle<SessionPropertiesAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res.Result
        }
        else
        {
            printf("Failed to get properties of session!");
        }
    });
}
```

#### <a name="update-rendering-session"></a>Aktualizuj sesję renderowania

```cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void UpdateRenderingSession(ApiHandle<AzureSession> session, const ARRTimeSpan& updatedLease)
{
    RenderingSessionUpdateParams params;
    params.MaxLease = updatedLease;
    ApiHandle<SessionAsync> pendingAsync = *session->RenewAsync(params);
    pendingAsync->Completed([](ApiHandle<SessionAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            printf("Rendering session renewed succeeded!");
        }
        else
        {
            printf("Failed to renew rendering session!");
        }
    });
}
```

#### <a name="stop-rendering-session"></a>Zatrzymaj sesję renderowania

```cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void StopRenderingSession(ApiHandle<AzureSession> session)
{
    ApiHandle<SessionAsync> pendingAsync = *session->StopAsync();
    pendingAsync->Completed([](ApiHandle<SessionAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            printf("Rendering session stopped successfully!");
        }
        else
        {
            printf("Failed to stop rendering session!");
        }
    });
}
```

#### <a name="connect-to-arr-inspector"></a>Połącz z inspektorem ARR

```cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session)
{
    _pendingAsync = session.ConnectToArrInspectorAsync();
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

```cpp
void ConnectToArrInspector(ApiHandle<AzureSession> session)
{
    ApiHandle<ArrInspectorAsync> pendingAsync = *session->ConnectToArrInspectorAsync();
    pendingAsync->Completed([](ApiHandle<ArrInspectorAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            // Launch the html file with default browser
            std::string htmlPath = "file:///" + *res->Result();
            ShellExecuteA(NULL, "open", htmlPath.c_str(), NULL, NULL, SW_SHOWDEFAULT);
        }
        else
        {
            printf("Failed to connect to ARR inspector!");
        }
    });
}
```

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta](create-an-account.md)
* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
