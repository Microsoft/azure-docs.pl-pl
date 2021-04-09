---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 2ec602f056b339a1b1dcb78d6b8d7583aeaf0434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009127"
---
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **wszystkie usługi** , a następnie wybierz pozycję **Notification Hubs** w sekcji **mobilnej** . Wybierz ikonę gwiazdki obok nazwy usługi, aby dodać usługę do sekcji **Ulubione** w menu po lewej stronie. Po dodaniu **Notification Hubs** do **ulubionych** wybierz je w menu po lewej stronie.

      ![Azure Portal — wybieranie usługi Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Na stronie **Notification Hubs** wybierz pozycję **Dodaj** na pasku narzędzi.

      ![Notification Hubs — przycisk Dodaj na pasku narzędzi](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Na stronie **Centrum powiadomień** wykonaj następujące kroki:

    1. Wprowadź nazwę w **centrum powiadomień**.  

    1. Wprowadź nazwę w polu **Utwórz nową przestrzeń nazw**. Przestrzeń nazw zawiera co najmniej jedno centrum.

    1. Wybierz wartość z listy rozwijanej **Lokalizacja** . Ta wartość określa lokalizację, w której ma zostać utworzone centrum.

    1. Wybierz istniejącą grupę zasobów w **grupie zasobów** lub Utwórz nazwę nowej grupy zasobów.

    1. Wybierz przycisk **Utwórz**.

        ![Azure Portal — ustawianie właściwości centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Wybierz pozycję **powiadomienia** (ikona dzwonka), a następnie wybierz pozycję **Przejdź do zasobu**. Możesz również odświeżyć listę na stronie **Notification Hubs** i wybrać centrum.

      ![Azure Portal — przejdź do zasobu](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Wybierz z listy pozycję **Zasady dostępu**. Należy pamiętać, że dwa parametry połączenia są dostępne dla Ciebie. Będą one potrzebne później do obsługi powiadomień wypychanych.

      >[!IMPORTANT]
      >*Nie* należy używać zasad **DefaultFullSharedAccessSignatureymi** w aplikacji. Jest to przeznaczone tylko do użycia w zapleczu.
      >

      ![Azure Portal — parametry połączenia centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
