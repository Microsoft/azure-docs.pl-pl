---
title: Powiadomienia w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Wysyłaj powiadomienia do użytkowników aplikacji opartych na usłudze Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a52188dc5058dbc74d3b03fba860b98540cd4a41
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608507"
---
# <a name="communication-services-notifications"></a>Powiadomienia dotyczące usług komunikacyjnych

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Usługa Azure Communication Services chat i wywołująca biblioteki klienckie umożliwiają utworzenie kanału obsługi komunikatów w czasie rzeczywistym, który umożliwia sygnalizowanie wiadomości do klientów podłączonych w skuteczny i niezawodny sposób. Dzięki temu można tworzyć rozbudowane funkcje komunikacji w czasie rzeczywistym w aplikacjach bez konieczności implementowania skomplikowanej logiki sondowania protokołu HTTP. Jednak w przypadku aplikacji mobilnych ten kanał sygnalizujący pozostanie połączony tylko wtedy, gdy aplikacja jest aktywna na pierwszym planie. Jeśli chcesz, aby użytkownicy odbierali wywołania przychodzące lub wiadomości czatu, gdy aplikacja jest w tle, należy używać powiadomień wypychanych.

Powiadomienia wypychane umożliwiają wysyłanie informacji z aplikacji do urządzeń przenośnych użytkowników. Możesz użyć powiadomień wypychanych, aby wyświetlić okno dialogowe, odtworzyć dźwięk lub wyświetlić interfejs użytkownika wywołania przychodzącego. Usługi Azure Communication Services oferują integrację z usługami [Azure Event Grid](../../event-grid/overview.md) i [Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md) , które umożliwiają dodawanie powiadomień wypychanych do aplikacji.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Wyzwalaj powiadomienia wypychane za pośrednictwem Azure Event Grid

Usługi komunikacyjne platformy Azure integrują się z [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) , aby zapewnić niezawodne i skalowalne powiadomienia o zdarzeniach w czasie rzeczywistym. Za pomocą tej integracji można utworzyć usługę powiadamiania, która dostarcza użytkownikom mobilne powiadomienia wypychane przez utworzenie subskrypcji usługi Event Grid, która wyzwala [funkcję platformy Azure](../../azure-functions/functions-overview.md) lub element webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagram przedstawiający sposób integracji usług komunikacyjnych z Event Grid.":::

Dowiedz się więcej o [obsłudze zdarzeń w usłudze Azure Communications Services](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Dostarczanie powiadomień wypychanych za pomocą usługi Azure Notification Hubs

Możesz połączyć centrum powiadomień platformy Azure z zasobem usług komunikacyjnych, aby automatycznie wysyłać powiadomienia wypychane do urządzenia przenośnego użytkownika po odebraniu połączenia przychodzącego. Te powiadomienia wypychane powinny być używane do wznawiania działania aplikacji z poziomu tła i wyświetlania interfejsu użytkownika, który umożliwia użytkownikowi zaakceptowanie lub odrzucanie wywołania. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagram przedstawiający sposób integracji usług komunikacyjnych z centrum powiadomień platformy Azure.":::

Usługi komunikacyjne korzystają z centrum powiadomień platformy Azure jako usługi przekazującej w celu komunikowania się z różnymi usługami powiadomień wypychanych specyficznymi dla platformy przy użyciu interfejsu API [bezpośredniego wysyłania](/rest/api/notificationhubs/direct-send) . Dzięki temu możesz ponownie wykorzystać istniejące zasoby i konfiguracje usługi Azure Notification Hub, aby zapewnić małym opóźnieniu, niezawodne powiadomienia o wywołaniach do aplikacji.

> [!NOTE]
> Obecnie są obsługiwane tylko wywołania powiadomień wypychanych.

### <a name="notification-hub-provisioning"></a>Inicjowanie obsługi administracyjnej centrum powiadomień 

Aby dostarczać powiadomienia wypychane do urządzeń klienckich przy użyciu Notification Hubs, [Utwórz centrum powiadomień](../../notification-hubs/create-notification-hub-portal.md) w ramach tej samej subskrypcji, co zasób usług komunikacyjnych. Usługa Azure Notification Hubs musi być skonfigurowana dla usługi powiadomień platformy, której chcesz użyć. Aby dowiedzieć się, jak uzyskać powiadomienia wypychane w aplikacji klienckiej z Notification Hubs, zobacz [wprowadzenie do Notification Hubs](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) i wybierz docelową platformę klienta z listy rozwijanej w górnej części strony.

> [!NOTE]
> Obecnie obsługiwane są platformy APNs i FCM.  
Platforma APNs musi być skonfigurowana z trybem uwierzytelniania tokenu. Tryb uwierzytelniania certyfikatu nie jest obecnie obsługiwany. 

Po skonfigurowaniu centrum powiadomień można je skojarzyć z zasobem usług komunikacyjnych, dostarczając parametry połączenia dla centrum przy użyciu klienta Azure Resource Manager lub za pośrednictwem Azure Portal. Parametry połączenia powinny zawierać uprawnienia "Send". Zalecamy utworzenie innych zasad dostępu z uprawnieniami tylko do wysyłania przeznaczonymi dla centrum. Dowiedz się więcej na temat [Notification Hubs zasad zabezpieczeń i dostępu](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>Konfigurowanie centrum powiadomień przy użyciu klienta Azure Resource Manager

Aby zalogować się do Azure Resource Manager, wykonaj następujące czynności i zaloguj się przy użyciu swoich poświadczeń.

```console
armclient login
```

 Po pomyślnym zalogowaniu wykonaj następujące czynności, aby zainicjować obsługę administracyjną centrum powiadomień:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>Konfigurowanie centrum powiadomień przy użyciu Azure Portal

W portalu przejdź do zasobu usługi Azure Communications Services. W obszarze zasób usług komunikacyjnych wybierz pozycję powiadomienia wypychane w menu po lewej stronie usługi komunikacyjne i Połącz się z centrum powiadomień, które zostało wcześniej zainicjowane. Należy podać parametry połączenia i identyfikator zasobu tutaj:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Zrzut ekranu przedstawiający ustawienia powiadomień wypychanych w witrynie Azure Portal.":::

> [!NOTE]
> Jeśli parametry połączenia centrum powiadomień platformy Azure zostały zaktualizowane, należy również zaktualizować zasób usług komunikacyjnych.  
Wszelkie zmiany dotyczące sposobu łączenia centrum zostaną odzwierciedlone w płaszczyźnie danych (tj. podczas wysyłania powiadomienia) w ciągu maksymalnego okresu ``10`` minut. Dotyczy to również sytuacji, gdy centrum jest połączone po raz pierwszy, **Jeśli** wcześniej zostały wysłane powiadomienia.

#### <a name="device-registration"></a>Rejestracja urządzenia 

Zapoznaj się z [przewodnikiem Szybki Start dla połączeń głosowych](../quickstarts/voice-video-calling/getting-started-with-calling.md) , aby dowiedzieć się, jak zarejestrować uchwyt urządzenia za pomocą usług komunikacyjnych.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](../../event-grid/overview.md)
* Aby dowiedzieć się więcej na temat koncepcji centrum powiadomień platformy Azure, zobacz [dokumentację usługi azure Notification Hubs](../../notification-hubs/index.yml)