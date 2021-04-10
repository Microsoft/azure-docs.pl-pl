---
title: Powiadomienia w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Wysyłaj powiadomienia do użytkowników aplikacji opartych na usłudze Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 9a878dc5cdbbe336e7279d0cd919bd17cd42d0e8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728219"
---
# <a name="communication-services-notifications"></a>Powiadomienia dotyczące usług komunikacyjnych

Czat i wywoływanie zestawów SDK usługi Azure Communication Services umożliwiają utworzenie kanału obsługi komunikatów w czasie rzeczywistym, który umożliwia wypychanie wiadomości do połączonych klientów w skuteczny i niezawodny sposób. Dzięki temu można tworzyć rozbudowane funkcje komunikacji w czasie rzeczywistym w aplikacjach bez konieczności implementowania skomplikowanej logiki sondowania protokołu HTTP. Jednak w przypadku aplikacji mobilnych ten kanał sygnalizujący pozostanie połączony tylko wtedy, gdy aplikacja jest aktywna na pierwszym planie. Jeśli chcesz, aby użytkownicy odbierali wywołania przychodzące lub wiadomości czatu, gdy aplikacja jest w tle, należy używać powiadomień wypychanych.

Powiadomienia wypychane umożliwiają wysyłanie informacji z aplikacji do urządzeń przenośnych użytkowników. Możesz użyć powiadomień wypychanych, aby wyświetlić okno dialogowe, odtworzyć dźwięk lub wyświetlić interfejs użytkownika wywołania przychodzącego. Usługi Azure Communication Services oferują integrację z usługami [Azure Event Grid](../../event-grid/overview.md) i [Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md) , które umożliwiają dodawanie powiadomień wypychanych do aplikacji.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Wyzwalaj powiadomienia wypychane za pośrednictwem Azure Event Grid

Usługi komunikacyjne platformy Azure integrują się z [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) , aby zapewnić niezawodne i skalowalne powiadomienia o zdarzeniach w czasie rzeczywistym. Za pomocą tej integracji można utworzyć usługę powiadamiania, która dostarcza użytkownikom mobilne powiadomienia wypychane przez utworzenie subskrypcji usługi Event Grid, która wyzwala [funkcję platformy Azure](../../azure-functions/functions-overview.md) lub element webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagram przedstawiający sposób integracji usług komunikacyjnych z Event Grid.":::

Dowiedz się więcej o [obsłudze zdarzeń w usłudze Azure Communications Services](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Dostarczanie powiadomień wypychanych za pomocą usługi Azure Notification Hubs

Możesz połączyć centrum powiadomień platformy Azure z zasobem usług komunikacyjnych, aby automatycznie wysyłać powiadomienia wypychane do urządzenia przenośnego użytkownika po odebraniu połączenia przychodzącego. Użyj tych powiadomień wypychanych, aby wznowić działanie aplikacji z poziomu tła i wyświetlić interfejs użytkownika, który umożliwia użytkownikowi zaakceptowanie lub odrzucanie wywołania.

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagram przedstawiający sposób integracji usług komunikacyjnych z usługą Azure Notification Hubs.":::

Usługi komunikacyjne korzystają z centrum powiadomień platformy Azure jako usługi przekazującej w celu komunikowania się z różnymi usługami powiadomień wypychanych specyficznymi dla platformy przy użyciu interfejsu API [bezpośredniego wysyłania](/rest/api/notificationhubs/direct-send) . Dzięki temu możesz ponownie wykorzystać istniejące zasoby i konfiguracje usługi Azure Notification Hub, aby zapewnić małym opóźnieniu, niezawodne powiadomienia o wywołaniach do aplikacji.

> [!NOTE]
> Obecnie są obsługiwane tylko wywołania powiadomień wypychanych.

### <a name="notification-hub-provisioning"></a>Inicjowanie obsługi administracyjnej centrum powiadomień

Aby dostarczać powiadomienia wypychane do urządzeń klienckich przy użyciu Notification Hubs, [Utwórz centrum powiadomień](../../notification-hubs/create-notification-hub-portal.md) w ramach tej samej subskrypcji, co zasób usług komunikacyjnych. Należy skonfigurować centrum powiadomień platformy Azure dla system powiadomień platformy, którego chcesz użyć. Aby dowiedzieć się, jak uzyskać powiadomienia wypychane w aplikacji klienckiej z Notification Hubs, zobacz [wprowadzenie do Notification Hubs](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) i wybierz docelową platformę klienta z listy rozwijanej w górnej części strony.

> [!NOTE]
> Obecnie obsługiwane są platformy APNs i FCM.
Platforma APNs musi być skonfigurowana z trybem uwierzytelniania tokenu. Tryb uwierzytelniania certyfikatu nie jest obecnie obsługiwany.

Po skonfigurowaniu centrum powiadomień można je skojarzyć z zasobem usług komunikacyjnych, dostarczając parametry połączenia dla centrum przy użyciu klienta Azure Resource Manager lub za pośrednictwem Azure Portal. Parametry połączenia powinny zawierać `Send` uprawnienia. Zalecamy utworzenie innych zasad dostępu tylko z `Send` uprawnieniami przeznaczonymi dla centrum. Dowiedz się więcej na temat [Notification Hubs zasad zabezpieczeń i dostępu](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>Łączenie centrum powiadomień za pomocą klienta Azure Resource Manager

Aby zalogować się do Azure Resource Manager, wykonaj następujące czynności i zaloguj się przy użyciu swoich poświadczeń.

```console
armclient login
```

 Po pomyślnym zalogowaniu wykonaj następujące czynności, aby zainicjować obsługę administracyjną centrum powiadomień:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Używanie Azure Portal do łączenia centrum powiadomień

W portalu przejdź do zasobu usługi Azure Communications Services. W obszarze zasób usług komunikacyjnych wybierz pozycję powiadomienia wypychane w menu po lewej stronie usługi komunikacyjne i Połącz się z centrum powiadomień, które zostało wcześniej zainicjowane. Należy podać parametry połączenia i resourceId tutaj:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Zrzut ekranu przedstawiający ustawienia powiadomień wypychanych w Azure Portal.":::

> [!NOTE]
> Jeśli parametry połączenia centrum powiadomień platformy Azure zostały zaktualizowane, należy również zaktualizować zasób usług komunikacyjnych.
Wszelkie zmiany dotyczące sposobu łączenia centrum zostaną odzwierciedlone w płaszczyźnie danych (tj. podczas wysyłania powiadomienia) w ciągu maksymalnego okresu ``10`` minut. Dotyczy to również sytuacji, gdy centrum jest połączone po raz pierwszy, **Jeśli** wcześniej zostały wysłane powiadomienia.

### <a name="device-registration"></a>Rejestracja urządzenia

Zapoznaj się z [przewodnikiem Szybki Start dla połączeń głosowych](../quickstarts/voice-video-calling/getting-started-with-calling.md) , aby dowiedzieć się, jak zarejestrować uchwyt urządzenia za pomocą usług komunikacyjnych.

### <a name="troubleshooting-guide-for-push-notifications"></a>Przewodnik rozwiązywania problemów dotyczących powiadomień wypychanych

Jeśli na urządzeniu nie widzisz powiadomień wypychanych, istnieją trzy miejsca, w których można było porzucić powiadomienia:

- Usługa Azure Notification Hubs nie zaakceptował powiadomienia z usług Azure Communications Services
- System powiadomień platformy (na przykład APNs i FCM) nie zaakceptował powiadomienia z platformy Azure Notification Hubs
- System powiadomień platformy nie dostarczył powiadomienia do urządzenia.

Poniżej znajduje się pierwsze miejsce, w którym można porzucić powiadomienie (usługa Azure Notification Hubs nie zaakceptował powiadomień z usług Azure Communications Services). W przypadku innych dwóch miejsc należy zapoznać się z tematem [diagnozowanie porzuconych powiadomień na platformie Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-fixer.md).

Jednym ze sposobów, aby sprawdzić, czy zasób usług komunikacyjnych wysyła powiadomienia do usługi Azure Notification Hubs polega na wyszukiwaniu `incoming messages` metryki z połączonych [metryk centrum powiadomień platformy Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs).

Poniżej przedstawiono kilka typowych błędów konfiguracji, które mogą być przyczyną, dlaczego usługa Azure Notification Hub nie akceptuje powiadomień z zasobu usług komunikacyjnych.

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Centrum powiadomień platformy Azure nie jest połączone z zasobem usług komunikacyjnych

Może wystąpić sytuacja, w której centrum powiadomień platformy Azure nie zostało połączone z zasobem usług komunikacyjnych. Zapoznaj się z [sekcją aprowizacji centrum powiadomień](#notification-hub-provisioning) , aby dowiedzieć się, jak je połączyć.

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>Połączone centrum powiadomień platformy Azure nie jest skonfigurowane

Należy skonfigurować połączone centrum powiadomień z poświadczeniami system powiadomień platformy dla platformy (na przykład iOS lub Android), których chcesz użyć. Aby uzyskać więcej informacji na temat tego, jak można to zrobić, możesz zapoznać się z tematem [Konfigurowanie powiadomień wypychanych w centrum powiadomień](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>Połączone centrum powiadomień platformy Azure nie istnieje

Centrum powiadomień platformy Azure połączone z zasobem usługi komunikacyjnej już nie istnieje. Sprawdź, czy połączone centrum powiadomień nadal istnieje.

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>Platforma APNs centrum powiadomień platformy Azure jest skonfigurowana z trybem uwierzytelniania certyfikatu

Jeśli chcesz używać platformy APNs z trybem uwierzytelniania certyfikatu, nie jest to obecnie obsługiwane. Skonfiguruj platformę APNs z trybem uwierzytelniania tokenu określonym w temacie [Konfigurowanie powiadomień wypychanych w centrum powiadomień](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>Połączone parametry połączenia nie mają `Send` uprawnienia

Parametry połączenia, które zostały użyte do połączenia centrum powiadomień z zasobem usług komunikacyjnych, muszą mieć odpowiednie `Send` uprawnienia. Aby uzyskać więcej informacji na temat sposobu tworzenia nowych parametrów połączenia lub wyświetlania bieżących parametrów połączenia z centrum powiadomień platformy Azure, możesz przyjrzeć się [Notification Hubs zasadom zabezpieczeń i dostępu](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>Połączone parametry połączenia lub identyfikator zasobu centrum powiadomień platformy Azure są nieprawidłowe

Upewnij się, że skonfigurowano zasób usług komunikacyjnych z prawidłowymi parametrami połączenia i identyfikatorem zasobu centrum powiadomień platformy Azure

#### <a name="the-linked-connection-string-is-regenerated"></a>Parametry połączenia połączonego są generowane ponownie

W przypadku ponownego wygenerowania parametrów połączenia połączonego centrum powiadomień platformy Azure należy zaktualizować parametry połączenia z nową w zasobie usług komunikacyjnych, ponownie [łącząc centrum powiadomień](#notification-hub-provisioning).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](../../event-grid/overview.md)
* Aby dowiedzieć się więcej na temat koncepcji centrum powiadomień platformy Azure, zobacz [dokumentację usługi azure Notification Hubs](../../notification-hubs/index.yml)
