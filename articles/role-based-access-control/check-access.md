---
title: Przewodnik Szybki Start — wyświetlanie dostępu użytkownika do zasobów platformy Azure — kontrola RBAC Azure
description: W tym przewodniku szybki start dowiesz się, jak wyświetlić dostęp użytkownika lub innego podmiotu zabezpieczeń do zasobów platformy Azure przy użyciu Azure Portal i kontroli dostępu opartej na rolach (RBAC) platformy Azure.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9be53aa964e75bab0b90495640537fe927a5af0e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "82734165"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Szybki Start: wyświetlanie dostępu użytkownika do zasobów platformy Azure

Możesz użyć bloku **kontroli dostępu (IAM)** w [kontroli dostępu opartej na ROLACH (Azure RBAC)](overview.md) na platformie Azure, aby wyświetlić dostęp użytkownika lub innego podmiotu zabezpieczeń do zasobów platformy Azure. Czasami jednak wystarczy szybki przegląd informacji o dostępie pojedynczego użytkownika lub innego podmiotu zabezpieczeń. Najłatwiej można to zrobić przy użyciu funkcji **Sprawdź dostęp** w witrynie Azure Portal.

## <a name="view-role-assignments"></a>Wyświetlanie przypisań ról

 Aby sprawdzić informacje o dostępie użytkownika, należy wyświetlić jego przypisania ról. Wykonaj następujące kroki, aby wyświetlić przypisania ról dla jednego użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej w zakresie subskrypcji.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Kliknij swoją subskrypcję.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij kartę **Sprawdź dostęp**.

    ![Kontrola dostępu — karta Sprawdź dostęp](./media/check-access/access-control-check-access.png)

1. Na liście **Znajdź** wybierz typ podmiotu zabezpieczeń, dla którego chcesz sprawdzić dostęp.

1. W polu wyszukiwania wprowadź ciąg, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail lub identyfikatory obiektów.

    ![Sprawdzanie dostępu — lista wyboru](./media/check-access/check-access-select.png)

1. Kliknij podmiot zabezpieczeń, aby otworzyć okienko **przypisania**.

    ![Okienko przypisania](./media/check-access/check-access-assignments.png)

    W tym okienku są wyświetlane role przypisane do wybranego podmiotu zabezpieczeń i zakresu. Jeśli istnieją przypisania odmowy występujące w tym zakresie lub dziedziczone przez ten zakres, zostaną wyświetlone.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure Portal](quickstart-assign-role-user-portal.md)
