---
title: Publikowanie aplikacji zarządzanych za poorednictwem portalu
description: Pokazuje, w jaki sposób używać Azure Portal do tworzenia aplikacji zarządzanej przez platformę Azure, która jest przeznaczona dla członków organizacji.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75651724"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publikowanie aplikacji katalogu usług za pomocą Azure Portal

Za pomocą Azure Portal można publikować [aplikacje zarządzane](overview.md) , które są przeznaczone dla członków organizacji. Na przykład dział IT może publikować aplikacje zarządzane zapewniające zgodność z normami obowiązującymi w organizacji. Takie aplikacje zarządzane są dostępne w katalogu usług, a nie w witrynie Azure Marketplace.

## <a name="prerequisites"></a>Wymagania wstępne

Podczas publikowania aplikacji zarządzanej należy określić tożsamość do zarządzania zasobami. Zalecamy określenie grupy użytkowników Azure Active Directory. Aby utworzyć Azure Active Directory grupę użytkowników, zobacz [Tworzenie grupy i Dodawanie członków w Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

Plik. zip, który zawiera definicję aplikacji zarządzanej, musi być dostępny za pomocą identyfikatora URI. Zalecamy przekazanie pliku zip do magazynu obiektów BLOB. 

## <a name="create-managed-application-with-portal"></a>Tworzenie aplikacji zarządzanej przy użyciu portalu

1. W lewym górnym rogu wybierz pozycję **+ Nowy**.

   ![Nowa usługa](./media/publish-portal/new.png)

1. Wyszukaj **Katalog usług**.

1. W wynikach przewiń do momentu znalezienia **definicji aplikacji zarządzanej katalogu usług**. Wybierz ją.

   ![Wyszukaj definicje aplikacji zarządzanych](./media/publish-portal/select-managed-apps-definition.png)

1. Wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia definicji aplikacji zarządzanej.

   ![Utwórz definicję aplikacji zarządzanej](./media/publish-portal/create-definition.png)

1. Podaj wartości dla nazwy, nazwy wyświetlanej, opisu, lokalizacji, subskrypcji i grupy zasobów. W polu Identyfikator URI pliku pakietu podaj ścieżkę do utworzonego pliku zip.

   ![Podaj wartości](./media/publish-portal/fill-application-values.png)

1. Po wyświetleniu sekcji uwierzytelnianie i blokowanie poziomu wybierz pozycję **Dodaj autoryzację**.

   ![Dodaj autoryzację](./media/publish-portal/add-authorization.png)

1. Wybierz grupę Azure Active Directory, aby zarządzać zasobami, a następnie wybierz **przycisk OK**.

   ![Dodaj grupę autoryzacji](./media/publish-portal/add-auth-group.png)

1. Po podaniu wszystkich wartości wybierz pozycję **Utwórz**.

   ![Tworzenie aplikacji zarządzanej](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Następne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* Aby zapoznać się z przykładami aplikacji zarządzanych, zobacz [przykładowe projekty dla aplikacji zarządzanych przez platformę Azure](sample-projects.md).
* Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).