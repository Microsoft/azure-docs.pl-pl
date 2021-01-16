---
title: Wprowadzenie do korzystania z zespołów międzyoperacyjnych w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku szybki start dowiesz się, jak sprzęgać spotkanie zespołów z biblioteką kliencką usługi Azure Communication Chat
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 1ad6b7241c7167c6da8952e7db2797fa275b7246
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251929"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Szybki Start: dołączanie aplikacji czatu do spotkania zespołów

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Rozpocznij pracę z usługami Azure Communications Services, łącząc rozwiązanie czatu z firmą Microsoft Teams przy użyciu biblioteki klienckiej języka JavaScript. 

## <a name="prerequisites"></a>Wymagania wstępne 

1.  [Wdrożenie zespołów](https://docs.microsoft.com/deployoffice/teams-install). 
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
