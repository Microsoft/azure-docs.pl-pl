---
title: Jak skonfigurować komputer do tworzenia Media Services przy użyciu platformy .NET
description: Dowiedz się więcej o wymaganiach wstępnych dotyczących Media Services przy użyciu zestawu SDK Media Services dla platformy .NET. Dowiedz się także, jak utworzyć aplikację programu Visual Studio.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: dda7849b6a5e22eea4891eacb2678b4c500dc1e1
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693667"
---
# <a name="media-services-development-with-net"></a>Programowanie Media Services przy użyciu platformy .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

W tym artykule omówiono sposób rozpoczynania opracowywania aplikacji Media Services przy użyciu platformy .NET.

Biblioteka **Azure Media Services zestawu SDK platformy .NET** umożliwia programowanie Media Services przy użyciu platformy .NET. Aby jeszcze bardziej ułatwić programowanie przy użyciu platformy .NET, podano bibliotekę **rozszerzeń zestawu SDK platformy .net Azure Media Services** . Ta biblioteka zawiera zestaw metod rozszerzających i funkcji pomocniczych, które upraszczają kod platformy .NET. Obie biblioteki są dostępne za poorednictwem **NuGet** i **GitHub**.

## <a name="prerequisites"></a>Wymagania wstępne
* Konto usługi Media Services w nowej lub istniejącej subskrypcji platformy Azure. Zapoznaj się z artykułem [jak utworzyć konto Media Services](media-services-portal-create-account.md).
* Systemy operacyjne: Windows 10, Windows 7, Windows 2008 R2 lub Windows 8.
* .NET Framework 4,5 lub nowszy.
* Programu Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio
W tej sekcji pokazano, jak utworzyć projekt w programie Visual Studio i skonfigurować go do tworzenia Media Services.  W takim przypadku projekt jest aplikacją konsolową dla systemu Windows w języku C#, ale te same kroki konfiguracji mają zastosowanie do innych typów projektów, które można utworzyć dla Media Services aplikacji (na przykład aplikacja Windows Forms lub aplikacja sieci Web ASP.NET).

W tej sekcji pokazano, jak Media Services dodać rozszerzenia zestawu SDK platformy .NET i inne zależne biblioteki przy użyciu narzędzia **NuGet** .

Alternatywnie możesz uzyskać najnowsze Media Services .NET SDK z usługi GitHub ([GitHub.com/Azure/Azure-SDK-for-Media-Services](https://github.com/Azure/azure-sdk-for-media-services) lub [GitHub.com/Azure/Azure-SDK-for-Media-Services-Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), skompilować rozwiązanie i dodać odwołania do projektu klienta. Wszystkie niezbędne zależności są pobierane i wyodrębniane automatycznie.

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio. Wprowadź **nazwę**, **lokalizację** i **nazwę rozwiązania**, a następnie kliknij przycisk OK.
2. Skompiluj rozwiązanie.
3. Zainstaluj i Dodaj **rozszerzenia Azure Media Services .NET SDK** (**windowsazure. MediaServices. Extensions**) przy użyciu narzędzia **NuGet** . Podczas instalacji tego pakietu instalowany jest również zestaw **.NET SDK usługi Media Services** oraz dodawane są wszystkie inne wymagane zależności.
   
    Upewnij się, że masz zainstalowaną najnowszą wersję narzędzia NuGet. Aby uzyskać więcej informacji i instrukcje dotyczące instalacji, zobacz [NuGet](https://nuget.codeplex.com/).

    1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy nazwę projektu i wybierz polecenie **Zarządzaj pakietami NuGet**.

    2. Zostanie wyświetlone okno dialogowe Zarządzanie pakietami NuGet.

    3. W galerii online Wyszukaj rozszerzenia MediaServices platformy Azure, Azure Media Services wybierz pozycję **rozszerzenia zestawu .NET SDK** (**windowsazure. MediaServices. Extensions**), a następnie kliknij przycisk **Instaluj** .
   
    4. Projekt zostanie zmodyfikowany i odwołuje się do Media Services rozszerzeń zestawu .NET SDK, Media Services .NET SDK i innych zależnych zestawów.
4. Aby podwyższyć poziom czystego środowiska programistycznego, należy rozważyć włączenie przywracania pakietów NuGet. Aby uzyskać więcej informacji, zobacz [przywracanie pakietów NuGet "](https://docs.nuget.org/consume/package-restore).
5. Dodaj odwołanie do **System.Configzestawu wersja** . Ten zestaw zawiera System.Configwersja. Klasa **ConfigurationManager** , która jest używana do uzyskiwania dostępu do plików konfiguracji (na przykład App.config).
   
    1. Aby dodać odwołania za pomocą okna dialogowego Zarządzanie odwołaniami, kliknij prawym przyciskiem myszy nazwę projektu w Eksplorator rozwiązań. Następnie kliknij przycisk **Dodaj**, a następnie kliknij pozycję **odwołanie...**.
   
    2. Zostanie wyświetlone okno dialogowe Zarządzanie odwołaniami.
    3. W obszarze zestawy .NET Framework Znajdź i wybierz System.Configzestaw wersja i naciśnij przycisk **OK**.
6. Otwórz plik App.config i Dodaj do pliku sekcję **AppSettings** . Ustaw wartości, które są konieczne do nawiązania połączenia z interfejsem API Media Services. Aby uzyskać więcej informacji, zobacz [dostęp do interfejsu API Azure Media Services przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    Ustaw wartości, które są konieczne do nawiązania połączenia przy użyciu metody uwierzytelniania **nazwy głównej usługi** .

    ```xml
    <configuration>
    ...
        <appSettings>
            <add key="AMSAADTenantDomain" value="tenant"/>
            <add key="AMSRESTAPIEndpoint" value="endpoint"/>
            <add key="AMSClientId" value="id"/>
            <add key="AMSClientSecret" value="secret"/>
        </appSettings>
    </configuration>
    ```

7. Dodaj **System.Configodwołanie wersja** do projektu.
8. Zastąp istniejące instrukcje **using** na początku pliku program.cs następującym kodem:

    ```csharp      
    using System;
    using System.Configuration;
    using System.IO;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Collections.Generic;
    using System.Linq;
    ```

    Teraz możesz rozpocząć tworzenie aplikacji Media Services.    

## <a name="example"></a>Przykład

Oto mały przykład, który nawiązuje połączenie z interfejsem API usługi AMS i wyświetla listę wszystkich dostępnych procesorów multimediów.

```csharp
class Program
{
    // Read values from the App.config file.

    private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
    private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
    private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
    private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];
        
    private static CloudMediaContext _context = null;
    static void Main(string[] args)
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
        // List all available Media Processors
        foreach (var mp in _context.MediaProcessors)
        {
            Console.WriteLine(mp.Name);
        }
        
    }
 ```

## <a name="next-steps"></a>Następne kroki

Teraz [możesz nawiązać połączenie z interfejsem API usługi AMS](media-services-use-aad-auth-to-access-ams-api.md) i rozpocząć [Tworzenie](media-services-dotnet-get-started.md)aplikacji.


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
