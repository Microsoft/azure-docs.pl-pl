---
title: Jak dodać kolejnych właścicieli do laboratorium w Azure Lab Services
description: W tym artykule opisano, jak administrator może dodać użytkownika jako właściciela do laboratorium w Azure Lab Services.
ms.topic: article
ms.date: 09/04/2020
ms.openlocfilehash: 4b46dd08a3b9527cdfc7bdf766ee4976e67656d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89482673"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Jak dodać dodatkowych właścicieli do istniejącego laboratorium w Azure Lab Services
W tym artykule opisano, jak administrator może dodać dodatkowych właścicieli do istniejącego laboratorium.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Dodawanie użytkownika do roli czytelnik dla konta laboratorium
Aby dodać użytkownika jako dodatkowego właściciela do istniejącego laboratorium, musisz najpierw przyznać uprawnienia do **odczytu** użytkownikowi na koncie laboratorium.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wyszukaj pozycję **usługi laboratoryjne**, a następnie wybierz ją.
3. Wybierz z listy **konto laboratorium** . 
2. Na **stronie konto laboratorium** wybierz pozycję **Access Control (IAM)** w menu po lewej stronie. 
2. Na stronie **Kontrola dostępu (IAM)** wybierz pozycję **Dodaj** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przypisanie roli**.

    ![Przypisanie roli dla konta laboratorium ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności: 
    1. Wybierz opcję **czytelnik** dla **roli**. 
    2. Wybierz użytkownika. 
    3. Wybierz pozycję **Zapisz**. 

        ![Dodawanie użytkownika do roli czytelnik dla konta laboratorium ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Dodawanie użytkownika do roli właściciela laboratorium

> [!NOTE]
> Jeśli użytkownik ma tylko dostęp z czytnika do laboratorium, laboratorium nie jest wyświetlane w labs.azure.com.

1. Na stronie **konto laboratorium** wybierz pozycję **Wszystkie laboratoria** w menu po lewej stronie.
2. Wybierz **laboratorium** , do którego chcesz dodać użytkownika jako właściciela. 
    
    ![Wybieranie laboratorium ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. Na stronie **laboratorium** wybierz pozycję **Kontrola dostępu (IAM)** w menu po lewej stronie.
4. Na stronie **Kontrola dostępu (IAM)** wybierz pozycję **Dodaj** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przypisanie roli**.
5. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności: 
    1. Wybierz **właściciela** dla **roli**. 
    2. Wybierz użytkownika. 
    3. Wybierz pozycję **Zapisz**. 

## <a name="next-steps"></a>Następne kroki
Upewnij się, że użytkownik widzi laboratorium po zalogowaniu się do [portalu usługi Lab Services](https://labs.azure.com).
