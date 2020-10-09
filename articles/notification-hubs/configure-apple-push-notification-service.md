---
title: Konfigurowanie Apple Push Notification Service na platformie Azure Notification Hubs | Microsoft Docs
description: Dowiedz się, jak skonfigurować centrum powiadomień platformy Azure przy użyciu ustawień Apple Push Notification Service (APNS).
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 63c7e0c9569428b55420911f253deee52ce440cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85255402"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Skonfiguruj Apple Push Notification Service ustawienia centrum powiadomień w Azure Portal

W tym artykule opisano sposób konfigurowania ustawień usług Apple Push Notification Service (APNS) dla centrum powiadomień platformy Azure przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie utworzono jeszcze centrum powiadomień, utwórz je teraz. Aby uzyskać więcej informacji, zobacz [Tworzenie centrum powiadomień platformy Azure w Azure Portal](create-notification-hub-portal.md).

## <a name="configure-apple-push-notification-service"></a>Konfigurowanie Apple Push Notification Service

Poniższa procedura zawiera instrukcje dotyczące konfigurowania ustawień usługi Apple Push Notification Service (APNS) dla centrum powiadomień:

1. W Azure Portal na stronie **centrum powiadomień** wybierz pozycję **Apple (APNs)** w menu po lewej stronie.

1. W obszarze **tryb uwierzytelniania**wybierz pozycję **certyfikat** lub **token**.

   - W przypadku wybrania opcji **certyfikat**:
      - Wybierz ikonę pliku, a następnie wybierz plik *. p12* , który chcesz przekazać.
      - Wprowadź hasło.
      - Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili aplikację ze sklepu, wybierz pozycję Tryb **produkcyjny** .

     ![Zrzut ekranu przedstawiający konfigurację certyfikatu APNS w Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   - W przypadku wybrania opcji **token**:
      - Wprowadź wartości dla **identyfikatora klucza**, **identyfikatora pakietu**, **identyfikatora zespołu**i **tokenu**.
      - Wybierz tryb **Piaskownica**. Aby wysyłać powiadomienia wypychane do użytkowników, którzy kupili aplikację ze sklepu, wybierz pozycję Tryb **produkcyjny** .

     ![Zrzut ekranu przedstawiający konfigurację tokenu usługi APNS w Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Następne kroki

Samouczek z instrukcjami krok po kroku dotyczącymi wysyłania powiadomień do urządzeń z systemem iOS znajduje się w następującym artykule: [wysyłanie powiadomień wypychanych do aplikacji systemu iOS przy użyciu usługi Azure Notification Hubs](ios-sdk-get-started.md).
