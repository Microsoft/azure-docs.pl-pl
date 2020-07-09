---
title: Konfigurowanie usługi Google Firebase Cloud Messaging na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować centrum powiadomień Azure za pomocą ustawień obsługi komunikatów w usłudze Google Firebase Cloud.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127467"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Skonfiguruj ustawienia usługi Google Firebase dla centrum powiadomień w Azure Portal

W tym artykule opisano sposób konfigurowania ustawień usługi Google Firebase Cloud Messaging (FCM) dla centrum powiadomień platformy Azure przy użyciu Azure Portal.  

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Konfigurowanie usługi Google Firebase Cloud Messaging (FCM)

Poniższa procedura zawiera instrukcje dotyczące konfigurowania ustawień usługi Google Firebase Cloud Messaging (FCM) dla centrum powiadomień: 

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Google (GCM/FCM)** w menu po lewej stronie. 
2. Wklej **klucz interfejsu API** dla projektu FCM, który został wcześniej zapisany. 
3. Wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu pokazujący sposób konfigurowania Notification Hubs dla usługi Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Następne kroki
Samouczek z instrukcjami krok po kroku dotyczącymi wypychania powiadomień do urządzeń z systemem Android przy użyciu usług Azure Notification Hubs i Google Firebase Cloud Messaging znajduje się w temacie [powiadomienia wypychane do urządzeń z systemem Android przy użyciu Notification Hubs i Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

