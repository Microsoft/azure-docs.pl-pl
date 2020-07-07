---
title: Konfigurowanie usługi powiadomień wypychanych systemu Windows na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia usługi powiadomień wypychanych systemu Windows dla centrum powiadomień platformy Azure.
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
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127311"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Skonfiguruj ustawienia usługi powiadomień wypychanych systemu Windows w Azure Portal

W tym artykule opisano sposób konfigurowania ustawień usługi powiadomień systemu Windows (WNS) dla centrum powiadomień platformy Azure przy użyciu Azure Portal.  

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurowanie usługi powiadomień WNS (WNS)

Poniższa procedura zawiera instrukcje dotyczące konfigurowania ustawień usługi Windows Push Notification Service (WNS) dla centrum powiadomień: 

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Windows (WNS)** w menu po lewej stronie.
2. Wprowadź wartości w polach **Identyfikator SID pakietu** i **Klucz zabezpieczeń**.
3. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający pola identyfikatora SID pakietu i klucza zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z samouczkiem zawierającym instrukcje krok po kroku dotyczące wypychania powiadomień do aplikacji platforma uniwersalna systemu Windows przy użyciu usług Azure Notification Hubs i Windows Push Notification Service (WNS), zobacz [wysyłanie powiadomień do aplikacji platformy UWP przy użyciu usługi azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


