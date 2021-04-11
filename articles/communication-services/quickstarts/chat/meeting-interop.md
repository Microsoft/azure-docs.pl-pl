---
title: Wprowadzenie do korzystania z zespołów międzyoperacyjnych w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku szybki start dowiesz się, jak sprzęgać spotkanie zespołów z zestawem SDK usługi Azure Communication Chat
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d7ea3b67c3ce85ce104d16785e4e5f4d45b138f6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106800"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Szybki Start: dołączanie aplikacji czatu do spotkania zespołów

> [!IMPORTANT]
> Aby włączyć/wyłączyć [współdziałanie dzierżawcy dla zespołów](../../concepts/teams-interop.md), Wypełnij [ten formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Rozpocznij pracę z usługami Azure Communications Services, łącząc rozwiązanie czatu z firmą Microsoft Teams przy użyciu zestawu JavaScript SDK. 

## <a name="prerequisites"></a>Wymagania wstępne 

1.  [Wdrożenie zespołów](/deployoffice/teams-install). 
2. Działająca [aplikacja czatu](./get-started.md). 

## <a name="enable-teams-interoperability"></a>Włącz współdziałanie zespołów 

Użytkownik usług komunikacyjnych, który łączy się ze spotkaniem zespołów, jako użytkownik-Gość może uzyskać dostęp do rozmowy na spotkaniu tylko wtedy, gdy dołączyli się do zespołu. Zapoznaj się z dokumentacją dotyczącą [zespołów międzyoperacyjnych](../voice-video-calling/get-started-teams-interop.md) , aby dowiedzieć się, jak dodać użytkownika usług komunikacyjnych do wywołania spotkania zespołowego.

Aby korzystać z tej funkcji, użytkownik musi być członkiem organizacji będącej właścicielem.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z naszym [przykładem programu Chat Hero](../../samples/chat-hero-sample.md)
- Dowiedz się więcej o tym, [jak działa rozmowa](../../concepts/chat/concepts.md)