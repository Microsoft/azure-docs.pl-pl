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
ms.openlocfilehash: 39c35bc90986126d099e68023e15b97c58d0ac26
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108199"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Szybki Start: dołączanie aplikacji wywołującej do spotkania zespołów

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

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z naszym [przykładem wywołania Hero](../../samples/calling-hero-sample.md)
- Informacje o [wywoływaniu możliwości zestawu SDK](./calling-client-samples.md)
- Dowiedz się więcej o [sposobie wywoływania programu](../../concepts/voice-video-calling/about-call-types.md)
