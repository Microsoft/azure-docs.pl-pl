---
title: Jak dodać kolejnych właścicieli do laboratorium w Azure Lab Services
description: W tym artykule opisano, jak administrator może dodać użytkownika jako właściciela do laboratorium w Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 9302cadf0b900c9faac382bad86b51cea7d828fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77443520"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Jak dodać dodatkowych właścicieli do istniejącego laboratorium w Azure Lab Services
W tym artykule opisano, jak administrator może dodać dodatkowych właścicieli do istniejącego laboratorium.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Dodawanie użytkownika do roli czytelnik dla konta laboratorium
Aby dodać użytkownika jako dodatkowego właściciela do istniejącego laboratorium, musisz najpierw przyznać uprawnienia do **odczytu** użytkownikowi na koncie laboratorium.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wyszukaj pozycję **usługi laboratoryjne**, a następnie wybierz ją.
3. Wybierz z listy **konto laboratorium** . 
2. Na **stronie konto laboratorium**wybierz pozycję **Access Control (IAM)** w menu po lewej stronie. 
2. Na stronie **Kontrola dostępu (IAM)** wybierz pozycję **Dodaj** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przypisanie roli**.

    ![Przypisanie roli dla konta laboratorium ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności: 
    1. Wybierz opcję **czytelnik** dla **roli**. 
    2. Wybierz użytkownika. 
    3. Wybierz pozycję **Zapisz**. 

        ![Dodawanie użytkownika do roli czytelnik dla konta laboratorium ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Dodawanie użytkownika do roli właściciela laboratorium

1. Na stronie **konto laboratorium** wybierz pozycję **Wszystkie laboratoria** w menu po lewej stronie.
2. Wybierz **laboratorium** , do którego chcesz dodać użytkownika jako właściciela. 
    
    ![Wybieranie laboratorium ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Na stronie **laboratorium** wybierz pozycję **Kontrola dostępu (IAM)** w menu po lewej stronie.
4. Na stronie **Kontrola dostępu (IAM)** wybierz pozycję **Dodaj** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przypisanie roli**.
5. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności: 
    1. Wybierz **właściciela** dla **roli**. 
    2. Wybierz użytkownika. 
    3. Wybierz pozycję **Zapisz**. 

## <a name="next-steps"></a>Następne kroki
Upewnij się, że użytkownik widzi laboratorium po zalogowaniu się do [portalu usługi Lab Services](https://labs.azure.com).