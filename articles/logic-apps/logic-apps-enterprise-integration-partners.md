---
title: Dodawanie partnerów handlowych dla integracji B2B
description: Utwórz partnerów handlowych na koncie integracji, które będą używane z usługą Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 8e3805fae5bf6cc5ad8cf759d3ba75220c6ddbd8
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565075"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Dodawanie partnerów handlowych do kont integracji dla usługi Azure Logic Apps

W [Azure Logic Apps](../logic-apps/logic-apps-overview.md)można tworzyć zautomatyzowane przepływy pracy integracji B2B (Business-to-Business) przy użyciu [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) z aplikacjami logiki. Aby przedstawić swoją organizację i inne osoby, możesz tworzyć i dodawać partnerów handlowych jako artefakty do konta integracji. Partnerzy to jednostki, które uczestniczą w transakcjach B2B i wymieniają wiadomości między sobą.

Przed utworzeniem tych partnerów należy omówić i udostępnić informacje partnerom dotyczącym sposobu identyfikowania i weryfikowania komunikatów wysyłanych przez inne. Po zaakceptowaniu tych informacji możesz utworzyć partnerów na koncie integracji.

## <a name="partner-roles-in-integration-accounts"></a>Role partnerów na kontach integracji

Aby zdefiniować szczegółowe informacje o komunikatach wymienianych z partnerami, należy utworzyć i dodać [umowy](../logic-apps/logic-apps-enterprise-integration-agreements.md) jako artefakty do konta integracji. Umowy wymagają co najmniej dwóch partnerów na koncie integracji. Twoja organizacja jest zawsze *partnerem hosta* w umowie. Organizacja, która wymienia wiadomości z organizacją, jest *partnerem gościa*. Partner gościa może być inną firmą, a nawet działem w organizacji. Po dodaniu tych partnerów możesz utworzyć umowę.

W umowie należy określić szczegóły dotyczące obsługi komunikatów przychodzących i wychodzących z perspektywy partnera hosta. W przypadku wiadomości przychodzących **Ustawienia Odbierz** określają, jak partner hosta odbiera komunikaty od partnera gościa w umowie. W przypadku wiadomości wychodzących **Ustawienia wysyłania** określają, jak partner hosta wysyła komunikaty do partnera gościa.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) do przechowywania partnerów, umów i innych artefaktów B2B. To konto integracji musi być skojarzone z subskrypcją platformy Azure.

## <a name="create-partner"></a>Utwórz partnera

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. W polu wyszukiwania wprowadź ciąg "Integracja" i wybierz pozycję **konta integracji**.

   ![Wybierz pozycję "konta integracji"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. W obszarze **konta integracji**wybierz konto integracji, do którego chcesz dodać partnerów.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Wybierz kafelek **partnerzy** .

   ![Zrzut ekranu przedstawiający kafelek partnerów.](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. W obszarze **partnerzy**wybierz pozycję **Dodaj**. W obszarze **Dodawanie partnera**Podaj szczegóły partnera zgodnie z opisem w poniższej tabeli.

   ![Wybierz pozycję "Dodaj" i podaj szczegóły partnera](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Tak | Nazwa partnera |
   | **Kwalifikator** | Tak | Uwierzytelniana treść, która udostępnia unikatowe tożsamości biznesowe organizacjom, na przykład **D-U-N-S (Dun & Bradstreet)**. <p>Partnerzy mogą wybrać wzajemnie zdefiniowaną tożsamość biznesową. W tych scenariuszach wybierz **wspólnie zdefiniowane** dla EDIFACT lub **wzajemnie zdefiniowane (X12)** dla X12. <p>Dla RosettaNet wybierz tylko **Duns**, który jest standardem. |
   | **Wartość** | Tak | Wartość, która identyfikuje dokumenty odbierane przez aplikacje logiki. <p>Dla RosettaNet ta wartość musi być cyfrą dziewięciu cyfr, która odpowiada numerowi DUNS. |
   ||||

   > [!NOTE]
   > W przypadku partnerów korzystających z usługi RosettaNet można określić dodatkowe informacje, tworząc najpierw tych partnerów, a następnie [edytując je później](#edit-partner).

1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   Nowy partner zostanie wyświetlony na liście **partnerów** . Ponadto kafelek **partnerzy** aktualizuje bieżącą liczbę partnerów.

   ![Nowy partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Edytuj partnera

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz swoje konto integracji.
Wybierz kafelek **partnerzy** .

   ![Wybierz kafelek "partnerzy"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. W obszarze **partnerzy**wybierz partnera, który chcesz edytować, a następnie wybierz pozycję **Edytuj**. W obszarze **Edytuj**wprowadź zmiany.

   ![Wprowadzanie i zapisywanie zmian](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   W przypadku RosettaNet, w obszarze **Właściwości partnera RosettaNet**, można określić następujące informacje dodatkowe:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Klasyfikacja partnera** | Nie | Typ organizacji partnera |
   | **Kod łańcucha dostaw** | Nie | Kod łańcucha dostaw partnera, na przykład "technologia informacyjna" lub "składniki elektroniczne" |
   | **Nazwa kontaktu** | Nie | Nazwa kontaktu partnera |
   | **Poczta e-mail** | Nie | Adres e-mail partnera |
   | **Faks** | Nie | Numer faksu partnera |
   | **Telephone** | Nie | Numer telefonu partnera |
   ||||

1. Gdy wszystko będzie gotowe, wybierz **przycisk OK** , aby zapisać zmiany.

## <a name="delete-partner"></a>Usuń partnera

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz swoje konto integracji. Wybierz kafelek **partnerzy** .

   ![Zrzut ekranu przedstawiający kafelek partnerzy wybierany podczas usuwania partnera.](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. W obszarze **partnerzy**wybierz partnera, który chcesz usunąć. Wybierz przycisk **Usuń**.

   ![Usuń partnera](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [umowach](../logic-apps/logic-apps-enterprise-integration-agreements.md)