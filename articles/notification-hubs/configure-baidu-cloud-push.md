---
title: Konfigurowanie wypychania w chmurze Baidu na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia Baidu dla centrum powiadomień platformy Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 759e35ba353f470ea3abc5f5d4182fa2b2ea0e73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "71212519"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Przestarzałe: Skonfiguruj ustawienia wypychania w chmurze Baidu dla centrum powiadomień w Azure Portal

W tym artykule opisano sposób konfigurowania ustawień wypychania w chmurze Baidu dla centrum powiadomień platformy Azure przy użyciu Azure Portal.

> [!IMPORTANT]
> Ten samouczek jest przestarzały. 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Konfigurowanie usługi Baidu Cloud Push
Poniższa procedura zawiera instrukcje dotyczące konfigurowania ustawień wypychania w chmurze Baidu dla centrum powiadomień:

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Baidu (Android China)** w menu po lewej stronie. 
2. Wprowadź **klucz interfejsu API** uzyskany z konsoli Baidu w projekcie powiadomienia wypychane w chmurze Baidu. 
3. Wprowadź **klucz tajny** uzyskany z konsoli Baidu w projekcie powiadomienia wypychane w chmurze Baidu. 
4. Wybierz pozycję **Zapisz**. 

    ![Zrzut ekranu przedstawiający Notification Hubs, w którym jest wyświetlana konfiguracja Baidu (Android China) dla powiadomień wypychanych](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z samouczkiem zawierającym instrukcje krok po kroku dotyczące wypychania powiadomień do usługi Baidu przy użyciu usług Azure Notification Hubs i Baidu Cloud push, zobacz Wprowadzenie [do Notification Hubs przy użyciu Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
