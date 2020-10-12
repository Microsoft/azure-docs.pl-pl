---
title: Konfigurowanie usługi powiadomień wypychanych systemu Windows na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia usługi powiadomień wypychanych systemu Windows dla centrum powiadomień platformy Azure.
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
ms.openlocfilehash: 62d73a23fff3bd15d873a579d451b2f91509b7fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87758734"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Skonfiguruj ustawienia usługi powiadomień wypychanych systemu Windows w Azure Portal

W tym artykule przedstawiono sposób konfigurowania ustawień usługi powiadomień systemu Windows (WNS) dla centrum powiadomień platformy Azure przy użyciu Azure Portal.  

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md).

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurowanie usługi powiadomień WNS (WNS)

Poniższa procedura zawiera opis czynności w celu skonfigurowania ustawień usługi wypychania powiadomień systemu Windows (WNS) dla centrum powiadomień:

1. Na Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Windows (WNS)** w menu po lewej stronie.
2. Wprowadź wartości w polach **Identyfikator SID pakietu** i **Klucz zabezpieczeń**.
3. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający pola identyfikatora SID pakietu i klucza zabezpieczeń](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Następne kroki

Samouczek zawierający instrukcje krok po kroku dotyczące wysyłania powiadomień wypychanych do aplikacji platforma uniwersalna systemu Windows przy użyciu usługi Azure Notification Hubs i Windows Push Notification Service (WNS) znajdują się w temacie [wysyłanie powiadomień do aplikacji platformy UWP przy użyciu platformy azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
