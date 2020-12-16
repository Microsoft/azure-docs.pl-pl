---
title: Wprowadzenie do korzystania z zespołów międzyoperacyjnych w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku szybki start dowiesz się, jak sprzęgać spotkanie zespołów z biblioteką kliencką usługi Azure Communication Chat
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ea66e4295e8228aa382aa29a46fcca8147dcbc98
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578131"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Szybki Start: dołączanie aplikacji czatu do spotkania zespołów

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Rozpocznij pracę z usługami Azure Communications Services, łącząc rozwiązanie czatu z firmą Microsoft Teams przy użyciu biblioteki klienckiej języka JavaScript. 

## <a name="prerequisites"></a>Wymagania wstępne 

1.  [Wdrożenie zespołów](https://docs.microsoft.com/deployoffice/teams-install). 
2. Działająca [aplikacja czatu](./get-started.md). 

## <a name="enable-teams-interoperability"></a>Włącz współdziałanie zespołów 

Użytkownik usług komunikacyjnych, który łączy się ze spotkaniem zespołów, jako użytkownik-Gość może uzyskać dostęp do rozmowy na spotkaniu tylko wtedy, gdy dołączyli się do zespołu. Zapoznaj się z dokumentacją dotyczącą [zespołów międzyoperacyjnych](../voice-video-calling/get-started-teams-interop.md) , aby dowiedzieć się, jak dodać użytkownika usług komunikacyjnych do wywołania spotkania zespołowego.

Funkcja współdziałania zespołów jest obecnie w prywatnej wersji zapoznawczej. Aby włączyć tę funkcję dla zasobu usług komunikacyjnych, Wyślij wiadomość e-mail acsfeedback@microsoft.com na adres: 
1. Identyfikator subskrypcji subskrypcji platformy Azure, która zawiera zasób usług komunikacyjnych. 
2. Identyfikator dzierżawy zespołu. Najprostszym sposobem uzyskania takiego dostępu jest uzyskanie i udostępnienie linku do zespołu. 

Aby korzystać z tej funkcji, użytkownik musi być członkiem organizacji będącej właścicielem. 

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z naszym [przykładem programu Chat Hero](../../samples/chat-hero-sample.md)
- Dowiedz się więcej o tym, [jak działa rozmowa](../../concepts/chat/concepts.md)
