---
title: Tworzenie centrum powiadomień przy użyciu witryny Azure Portal | Microsoft Docs
description: W ramach tego samouczka nauczysz się tworzyć centrum powiadomień platformy Azure przy użyciu witryny Azure Portal.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 141812e83653b458a2562bf73b70117c39bf7be0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92426561"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Szybki Start: tworzenie centrum powiadomień platformy Azure w Azure Portal

Usługa Azure Notification Hubs oferuje łatwy w użyciu, skalowany w poziomie mechanizm, który umożliwia wysyłanie powiadomień do dowolnej platformy (iOS, Android, Windows, Kindle, Baidu, itp.) z poziomu dowolnego zaplecza (w chmurze lub w środowisku lokalnym). Aby uzyskać więcej informacji na temat usługi, zobacz [Co to jest usługa Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

W tym przewodniku Szybki start utworzysz centrum powiadomień w witrynie Azure Portal. Pierwsza sekcja zawiera kroki tworzenia przestrzeni nazw usługi Notification Hubs i centrum w tej przestrzeni nazw. Druga sekcja zawiera kroki tworzenia centrum powiadomień w istniejącej przestrzeni nazw usługi Notification Hubs.

## <a name="create-a-namespace-and-a-notification-hub"></a>Tworzenie przestrzeni nazw i centrum powiadomień

W tej sekcji utworzysz przestrzeń nazw i centrum w przestrzeni nazw.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Tworzenie centrum powiadomień w istniejącej przestrzeni nazw

W tej sekcji utworzysz centrum powiadomień w istniejącej przestrzeni nazw.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie, wyszukaj pozycję **Centrum powiadomień** i wybierz **gwiazdkę** (`*`) obok pozycji **Przestrzenie nazw centrum powiadomień**, aby dodać je do sekcji **ULUBIONE** w menu po lewej stronie. Wybierz pozycję **Przestrzenie nazw centrum powiadomień**.

      ![Azure Portal — wybieranie pozycji Przestrzenie nazw centrum powiadomień](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Na stronie **Przestrzenie nazw centrum powiadomień** wybierz przestrzeń nazw z listy.

      ![Wybieranie przestrzeni nazw z listy](./media/create-notification-hub-portal/select-namespace.png)
4. Na stronie **Przestrzeń nazw centrum powiadomień** wybierz pozycję **Dodaj centrum** na pasku narzędzi.

      ![Przestrzenie nazw centrum powiadomień — przycisk Dodaj centrum](./media/create-notification-hub-portal/add-hub-button.png)
5. Na stronie **Nowe centrum powiadomień** wprowadź nazwę centrum powiadomień, a następnie wybierz przycisk **OK**.

      ![Strona Nowe centrum powiadomień-> wprowadzanie nazwy centrum](./media/create-notification-hub-portal/new-notification-hub-page.png)
6. Wybierz pozycję **Powiadomienia** (ikona dzwonka) u góry, aby wyświetlić stan wdrożenia nowego centrum. Wybierz znak **X** w prawym rogu, aby zamknąć okno powiadomień.

      ![Powiadomienie dotyczące wdrożenia](./media/create-notification-hub-portal/deployment-notification.png)
7. Odśwież stronę internetową **Przestrzenie nazw centrum powiadomień**, aby zobaczyć nowe centrum na liście.

      ![Zrzut ekranu pokazujący stronę sieci Web Centrum powiadomień z nowym centrum na liście.](./media/create-notification-hub-portal/new-hub-in-list.png)
8. Wybierz swoje **centrum powiadomień**, aby wyświetlić jego stronę główną.

      ![Zrzut ekranu przedstawiający stronę główną centrum powiadomień.](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono centrum powiadomień. Aby dowiedzieć się, jak skonfigurować centrum przy użyciu ustawień systemu powiadomień platformy, zobacz [Konfigurowanie centrum powiadomień przy użyciu ustawień systemu powiadomień platformy](configure-notification-hub-portal-pns-settings.md).
