---
title: Konfigurowanie usługi powiadomień wypychanych firmy Microsoft na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia usługi powiadomień wypychanych firmy Microsoft dla centrum powiadomień platformy Azure.
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
ms.openlocfilehash: 6784331dccd779390ace7dc632d54fe54aedc458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87760995"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Skonfiguruj ustawienia usługi Microsoft Push Notification Service (usługi MPNS) w Azure Portal

W tym artykule opisano sposób konfigurowania ustawień usługi Microsoft Push Notification Service (usługi MPNS) dla centrum powiadomień platformy Azure przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md).

## <a name="configure-microsoft-push-notification-service-mpns"></a>Konfigurowanie usługi powiadomień wypychanych firmy Microsoft (usługi MPNS)

Poniższa procedura opisuje sposób konfigurowania ustawień usługi Microsoft Push Notification Service (usługi MPNS) dla centrum powiadomień:

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Windows Phone (usługi MPNS)** w menu po lewej stronie.
2. Włącz nieuwierzytelnione lub uwierzytelnione powiadomienia wypychane:

   a. Aby włączyć nieuwierzytelnione powiadomienia wypychane, zaznacz opcję **Włącz nieuwierzytelnione wypychanie**  >  .

      ![Zrzut ekranu pokazujący sposób włączania nieuwierzytelnionych powiadomień wypychanych](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Aby włączyć uwierzytelnione powiadomienia wypychane:
      * Na pasku narzędzi wybierz pozycję **Przekaż certyfikat**.
      * Wybierz ikonę pliku, a następnie wybierz plik certyfikatu.
      * Wprowadź hasło certyfikatu.
      * Wybierz przycisk **OK**.
      * Na stronie **Windows Phone (usługi MPNS)** wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem zawierającym instrukcje krok po kroku dotyczące wypychania powiadomień do urządzeń Windows Phone przy użyciu usług Azure Notification Hubs i Microsoft Push Notification Service (usługi MPNS), zobacz [wysyłanie powiadomień wypychanych do aplikacji Windows Phone za pomocą Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).
