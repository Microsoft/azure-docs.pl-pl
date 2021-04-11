---
title: Szybki Start — zespoły międzyoperacyjności w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku szybki start dowiesz się, jak połączyć zespoły ze spotkaniem z zestawem SDK wywołań komunikacyjnych platformy Azure.
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 0e75d2b480a9cbfd2977d9d449c1ea12bdfe4920
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095615"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Szybki Start: dołączanie aplikacji wywołującej do spotkania zespołów

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> Aby włączyć/wyłączyć [współdziałanie dzierżawcy dla zespołów](../../concepts/teams-interop.md), Wypełnij [ten formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Rozpocznij pracę z usługami Azure Communications Services, łącząc rozwiązanie wywołujące z firmą Microsoft Teams przy użyciu zestawu JavaScript SDK.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

Funkcje opisane w tym dokumencie korzystają z ogólnej wersji dostępności zestawów SDK usług komunikacyjnych. Współdziałanie zespołów wymaga wersji beta zestawów SDK usług komunikacyjnych. Zestawy SDK dla wersji beta można zbadać na [stronie informacje o wersji](https://github.com/Azure/Communication/tree/master/releasenotes).

Podczas wykonywania kroku "Zainstaluj pakiet" z zestawami SDK Beta Zmień wersję pakietu do najnowszej wersji beta, określając wersję `@1.0.0-beta.10` (wersja w momencie pisania tego artykułu) w `communication-calling` nazwie pakietu. Nie trzeba modyfikować `communication-common` polecenia Package. Na przykład:

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z naszym [przykładem wywołania Hero](../../samples/calling-hero-sample.md)
- Informacje o [wywoływaniu możliwości zestawu SDK](./calling-client-samples.md)
- Dowiedz się więcej o [sposobie wywoływania programu](../../concepts/voice-video-calling/about-call-types.md)
