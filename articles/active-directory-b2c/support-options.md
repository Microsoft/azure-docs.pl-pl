---
title: Obsługa Azure Active Directory B2C | Microsoft Docs
description: Jak plików obsługiwać żądania Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5195241003b1ce4ea505002e2cc3c10410e6cde1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78183724"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: żądania obsługi plików
Żądania obsługi plików dla Azure Active Directory B2C (Azure AD B2C) można uzyskać w Azure Portal, wykonując następujące czynności:

1. Przełącz się z dzierżawy B2C do innej dzierżawy, z którą skojarzona jest subskrypcja platformy Azure. Zwykle jest to Twoja dzierżawa pracownika lub domyślna dzierżawa utworzona dla Ciebie podczas rejestrowania się w celu uzyskania subskrypcji platformy Azure. Aby dowiedzieć się więcej, zobacz [jak subskrypcja platformy Azure jest powiązana z usługą Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![Azure Portal z wyróżnionym wyborem dzierżawy](./media/support-options/support-switch-dir.png)

1. Po przełączeniu dzierżawców kliknij pozycję **Pomoc i obsługa techniczna**.

    ![Kafelek pomoc i obsługa techniczna wyróżniona w Azure Portal](./media/support-options/support-support.png)

1. Kliknij pozycję **nowe żądanie obsługi**.

    ![Nowy kafelek żądania obsługi wyróżniony w Azure Portal](./media/support-options/support-new.png)

1. W bloku **podstawy** Użyj tych szczegółów i kliknij przycisk **dalej**.

    * **Typ problemu** to **techniczne**.
    * Wybierz odpowiednią **subskrypcję**.
    * **Usługa** jest **Active Directory**.
    * Wybierz odpowiedni **Plan pomocy technicznej**. Jeśli go nie masz, możesz utworzyć konto w [tym miejscu](https://azure.microsoft.com/support/plans/).

     ![Strona podstawy z wyróżnionym przyciskiem dalej w Azure Portal](./media/support-options/support-basics.png)

1. W bloku **problemu** Użyj tych szczegółów i kliknij przycisk **dalej**.

    * Wybierz odpowiedni poziom **ważności** .
    * **Typ problemu** to **B2C**.
    * Wybierz odpowiednią **kategorię**.
    * Opisz swój problem w polu **szczegóły** . Podaj szczegóły, takie jak nazwa dzierżawy B2C, opis problemu, komunikaty o błędach, identyfikatory korelacji (jeśli są dostępne) itd.
    * W polu **przedział czasu** Podaj datę i godzinę (w tym strefę czasową) wystąpienia problemu.
    * W obszarze **przekazywanie plików** Przekaż wszystkie zrzuty ekranu i pliki, które mogą być pomocne w rozwiązaniu problemu.

     ![Strona problemu z wyróżnionym przyciskiem dalej w Azure Portal](./media/support-options/support-problem.png)

1. W bloku **informacje kontaktowe** Dodaj swoje informacje kontaktowe. Kliknij pozycję **Utwórz**.

    ![Strona informacje kontaktowe z wyróżnionym przyciskiem Utwórz w portalu](./media/support-options/support-contact.png)

1. Po przesłaniu żądania pomocy technicznej można je monitorować, klikając pozycję **Pomoc i obsługa techniczna** w tablicy startowej, a następnie **zarządzać żądaniami pomocy technicznej**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Znany problem: zgłoszenie żądania pomocy technicznej w kontekście dzierżawy B2C

Jeśli pominięto krok 2 opisany powyżej i spróbujesz utworzyć żądanie pomocy technicznej w kontekście dzierżawy B2C, zostanie wyświetlony następujący błąd.

> [!IMPORTANT]
> Nie próbuj rejestrować się w celu uzyskania nowej subskrypcji platformy Azure w dzierżawie usługi B2C.

![Nie masz błędu subskrypcji w Azure Portal](./media/support-options/support-no-sub.png)
