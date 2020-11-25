---
title: Konfigurowanie usługi Google Firebase Cloud Messaging na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować centrum powiadomień Azure za pomocą ustawień obsługi komunikatów w usłudze Google Firebase Cloud.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 0da103c11e2412108535ca322917632f5d95559d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003569"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Skonfiguruj ustawienia usługi Google Firebase dla centrum powiadomień w Azure Portal

W tym artykule opisano sposób konfigurowania ustawień usługi Google Firebase Cloud Messaging (FCM) dla centrum powiadomień platformy Azure przy użyciu Azure Portal.  

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md).

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Konfigurowanie usługi Google Firebase Cloud Messaging (FCM)

Poniższa procedura opisuje kroki konfigurowania ustawień usługi Google Firebase Cloud Messaging (FCM) dla centrum powiadomień:

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Google (GCM/FCM)** w menu po lewej stronie.
2. Wklej **klucz interfejsu API** dla projektu FCM, który został wcześniej zapisany.
3. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu pokazujący sposób konfigurowania Notification Hubs dla usługi Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem zawierającym instrukcje krok po kroku dotyczące wysyłania powiadomień do urządzeń z systemem Android przy użyciu usług Azure Notification Hubs i Google Firebase Cloud Messaging, zobacz [wysyłanie powiadomień wypychanych do urządzeń z systemem Android za pomocą Notification Hubs i Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).
