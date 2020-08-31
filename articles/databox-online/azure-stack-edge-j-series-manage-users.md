---
title: Azure Stack Edge procesora GPU zarządzanie użytkownikami | Microsoft Docs
description: Opisuje, jak używać Azure Portal do zarządzania użytkownikami na Azure Stack GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 883250bbf4bc745c1c2a0e8f442cab4d73c4479b
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147171"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge"></a>Użyj Azure Portal, aby zarządzać użytkownikami na Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym artykule opisano sposób zarządzania użytkownikami na Azure Stackej krawędzi. Możesz zarządzać Azure Stack krawędzią za pośrednictwem Azure Portal lub za pośrednictwem lokalnego interfejsu użytkownika sieci Web. Witryna Azure Portal umożliwia dodawanie, modyfikowanie i usuwanie użytkowników.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie użytkownika
> * Modyfikowanie harmonogramu
> * Usuwanie użytkownika

## <a name="about-users"></a>Informacje o użytkownikach

Użytkownicy mogą mieć prawo tylko do odczytu lub pełne uprawnienia. Jak sugerują nazwy, użytkownicy z prawem tylko do odczytu mogą wyłącznie wyświetlać dane udziału. Użytkownicy z pełnymi uprawnieniami mogą odczytywać dane udziału, dokonywać zapisu w tych udziałach oraz modyfikować lub usuwać dane udziału.

 - **Użytkownik z pełnymi uprawnieniami** — użytkownik lokalny z pełnym dostępem.
 - **Użytkownik z prawem tylko do odczytu** — użytkownik lokalny z dostępem tylko do odczytu. Ci użytkownicy są powiązani z udziałami, które umożliwiają wykonywanie operacji tylko do odczytu.

Uprawnienia użytkownika są definiowane podczas jego tworzenia w czasie tworzenia udziału. Po zdefiniowaniu uprawnień związanych z użytkownikiem można je zmodyfikować za pomocą Eksploratora plików.


## <a name="add-a-user"></a>Dodawanie użytkownika

Wykonaj następujące czynności w witrynie Azure Portal, aby dodać użytkownika.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do **omówienia > użytkowników**. Wybierz pozycję **+ Dodaj użytkownika** na pasku poleceń.

    ![Wybierz pozycję Dodaj użytkownika](media/azure-stack-edge-j-series-manage-users/add-user-1.png)

2. Określ nazwę i hasło użytkownika, którego chcesz dodać. Potwierdź hasło i wybierz pozycję **Dodaj**.

    ![Określ nazwę użytkownika i hasło](media/azure-stack-edge-j-series-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Oto użytkownicy zarezerwowani przez system, których nie należy używać: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Powiadomienie jest wyświetlane po rozpoczęciu i zakończeniu tworzenia przez użytkownika. Po utworzeniu użytkownika na pasku poleceń wybierz pozycję **Odśwież** , aby wyświetlić zaktualizowaną listę użytkowników.


## <a name="modify-user"></a>Modyfikowanie harmonogramu

Po utworzeniu użytkownika możesz zmienić przypisane do niego hasło. Wybierz z listy użytkowników. Wprowadź i Potwierdź nowe hasło. Zapisz zmiany.
 
![Modyfikowanie harmonogramu](media/azure-stack-edge-j-series-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Usuwanie użytkownika

Wykonaj następujące czynności w witrynie Azure Portal, aby usunąć użytkownika.


1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do **omówienia > użytkowników**.

    ![Wybierz użytkownika do usunięcia](media/azure-stack-edge-j-series-manage-users/delete-user-1.png)

2. Wybierz użytkownika z listy użytkowników, a następnie wybierz pozycję **Usuń**. Po wyświetleniu monitu potwierdź usunięcie.

    ![Wybierz użytkownika do usunięcia](media/azure-stack-edge-j-series-manage-users/delete-user-2.png)

Lista użytkowników zostanie zaktualizowana w celu uwzględnienia usuniętego użytkownika.

![Zaktualizowana lista użytkowników](media/azure-stack-edge-j-series-manage-users/delete-user-4.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać przepustowością](azure-stack-edge-j-series-manage-bandwidth-schedules.md).
