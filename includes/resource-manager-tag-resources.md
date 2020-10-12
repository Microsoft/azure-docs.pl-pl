---
title: plik dołączany
description: plik dołączany
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80132109"
---
Jeśli użytkownik nie ma wymaganego dostępu do stosowania tagów, można przypisać rolę **współautor tagu** do użytkownika. Aby uzyskać więcej informacji, zobacz [Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu RBAC i Azure Portal](../articles/role-based-access-control/quickstart-assign-role-user-portal.md).

1. Aby wyświetlić Tagi dla zasobu lub grupy zasobów, poszukaj istniejących tagów w przeglądzie. Jeśli wcześniej nie zastosowano tagów, lista będzie pusta.

   ![Wyświetl Tagi dla zasobu lub grupy zasobów](./media/resource-manager-tag-resources/view-tags.png)

1. Aby dodać tag, wybierz **pozycję kliknij tutaj, aby dodać tagi**.

1. Podaj nazwę i wartość.

   ![Dodawanie tagu](./media/resource-manager-tag-resources/add-tag.png)

1. Kontynuuj dodawanie tagów zgodnie z wymaganiami. Po zakończeniu wybierz pozycję **Zapisz**.

   ![Zapisz Tagi](./media/resource-manager-tag-resources/save-tags.png)

1. Tagi są teraz wyświetlane w przeglądzie.

   ![Pokaż Tagi](./media/resource-manager-tag-resources/view-new-tags.png)

1. Aby dodać lub usunąć tag, wybierz pozycję **Zmień**.

1. Aby usunąć tag, wybierz ikonę kosza. Następnie wybierz pozycję **Zapisz**.

   ![Usuń tag](./media/resource-manager-tag-resources/delete-tag.png)

Aby zbiorczo przypisać Tagi do wielu zasobów:

1. Z dowolnej listy zasobów zaznacz pole wyboru dla zasobów, dla których chcesz przypisać tag. Następnie wybierz pozycję **Przypisz Tagi**.

   ![Wybierz wiele zasobów](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Dodaj nazwy i wartości. Po zakończeniu wybierz pozycję **Zapisz**.

   ![Wybierz pozycję Przypisz](./media/resource-manager-tag-resources/select-assign.png)

Aby wyświetlić wszystkie zasoby ze znacznikiem:

1. W menu Azure Portal Wyszukaj **Tagi**. Wybierz ją z dostępnych opcji.

   ![Znajdź przez tag](./media/resource-manager-tag-resources/find-tags-general.png)

1. Wybierz tag do wyświetlania zasobów.

   ![Wybierz tag](./media/resource-manager-tag-resources/select-tag.png)

1. Wyświetlane są wszystkie zasoby z tym tagiem.

   ![Wyświetl zasoby według tagu](./media/resource-manager-tag-resources/view-resources-by-tag.png)
