---
title: Konteneryzowanie istniejącej aplikacji .NET dla usługi Service Fabric Mesh
description: Dodaj obsługę aranżacji kontenera Service Fabric siatki do projektów ASP.NET i konsolowych, które korzystają z pełnego programu .NET Framework.
author: georgewallace
ms.author: gwallace
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: 0f236292fff0d0e806e6eec32e1e058cbf67545c
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93144481"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Konteneryzowanie istniejącej aplikacji .NET dla usługi Service Fabric Mesh

W tym artykule przedstawiono sposób dodawania obsługi orkiestracji kontenerów usługi Service Fabric Mesh do istniejącej aplikacji .NET.

W programie Visual Studio 2017 obsługę konteneryzacji można dodać do projektów platformy ASP.NET i konsoli korzystających z pełnej platformy .NET.

> [!NOTE]
> Projekty platformy .NET **Core** nie są obecnie obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Upewnij się, że masz [skonfigurowane środowisko projektowe](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Obejmuje to zainstalowanie środowiska uruchomieniowego usługi Service Fabric, zestawu SDK, platformy Docker i programu Visual Studio 2017 oraz utworzenie lokalnego klastra.

## <a name="open-an-existing-net-app"></a>Otwieranie istniejącej aplikacji platformy .NET

Otwórz aplikację, do której chcesz dodać obsługę orkiestracji kontenerów.

Jeśli chcesz wypróbować przykład, użyj przykładowego kodu [eShop](https://github.com/MikkelHegn/ContainersSFLab). W pozostałej części tego artykułu przyjęto, że jest używany ten projekt, lecz kroki można także zastosować do własnego projektu.

Pobierz kopię projektu **eShop** :

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Po jej pobraniu otwórz w programie Visual Studio 2017 plik **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln** .

## <a name="add-container-support"></a>Dodawanie obsługi kontenerów
 
Dodaj obsługę orkiestracji kontenerów do istniejącego projektu platformy ASP.NET lub konsoli przy użyciu narzędzi usługi Service Fabric Mesh w następujący sposób:

W eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy nazwę projektu (w tym przykładzie **eShopLegacyWebForms** ), a następnie wybierz pozycję **Dodaj** > **Obsługa orkiestratora kontenerów** .
Zostanie wyświetlone okno dialogowe **Dodawanie obsługi orkiestratora kontenerów** .

![Okno dialogowe dodawania orkiestratora kontenerów w programie Visual Studio](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Wybierz pozycję **Service Fabric Mesh** z listy rozwijanej, a następnie kliknij pozycję **OK** .


>[!NOTE]
> Od 2 listopada 2020 [limity szybkości pobierania mają zastosowanie](https://docs.docker.com/docker-hub/download-rate-limit/) do żądań anonimowych i uwierzytelnionych do usługi Docker Hub z kont planów bezpłatnych platformy Docker i są wymuszane na podstawie adresu IP. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie przy użyciu usługi Docker Hub](https://docs.microsoft.com/azure/container-registry/buffer-gate-public-content#authenticate-with-docker-hub).
>
> Aby uniknąć ograniczania szybkości, upewnij się, że wartość domyślna `FROM microsoft/aspnet:4.7.2-windowsservercore-1803 AS base` w pliku dockerfile jest zastępowana `FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-1803 AS base`

Wtedy narzędzie sprawdzi, czy platforma Docker jest zainstalowana, doda plik Dockerfile do projektu i ściągnie obraz platformy Docker dla projektu.  
Projekt aplikacji usługi Service Fabric Mesh został dodany do rozwiązania. Zawiera on profile publikowania usługi Mesh i pliki konfiguracji. Nazwa projektu jest taka sama jak nazwa Twojego projektu z dołączonym słowem „Application” na końcu, na przykład **eShopLegacyWebFormsApplication** . 

W nowym projekcie usługi Mesh zobaczysz dwa foldery, o których warto wiedzieć:
- **App Resources** zawierający pliki YAML, które opisują dodatkowe zasoby usługi Mesh, takie jak sieć.
- **Service Resources** zawierający plik service.yaml, który opisuje, jak należy uruchomić aplikację po wdrożeniu.

Po dodaniu obsługi orkiestracji kontenerów do aplikacji możesz nacisnąć klawisz **F5** , aby debugować aplikację .NET w lokalnym klastrze usługi Service Fabric Mesh. Tutaj przedstawiono aplikację platformy ASP.NET eShop uruchomioną w klastrze usługi Service Fabric Mesh: 

![Aplikacja eShop](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Teraz możesz opublikować aplikację w usłudze Azure Service Fabric Mesh.

## <a name="next-steps"></a>Następne kroki

Zobacz, jak opublikować aplikację w usłudze Service Fabric Mesh: [Samouczek — wdrażanie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)