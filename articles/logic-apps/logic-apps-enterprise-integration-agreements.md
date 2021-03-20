---
title: Umowy partnerów handlowych
description: Tworzenie umów i zarządzanie nimi między partnerami handlowymi przy użyciu Azure Logic Apps i Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: c8cbfb619c9eed325161503f705bf5c4c0746265
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95992939"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Tworzenie umów z partnerami handlowymi w usłudze Azure Logic Apps i zarządzanie nimi

[](../logic-apps/logic-apps-enterprise-integration-partners.md)  
 *Umowa dotycząca* partnera handlowego pomaga organizacjom i firmom komunikować się bezproblemowo ze sobą przez zdefiniowanie określonego standardu branżowego, który ma być używany podczas wymiany komunikatów biznesowych (B2B). Umowy zapewniają typowe korzyści, na przykład:

* Umożliwiają organizacjom wymianę informacji przy użyciu dobrze znanego formatu.
* Lepsza wydajność podczas przeprowadzania transakcji B2B.
* Łatwe tworzenie, zarządzanie i używanie do tworzenia rozwiązań integracji przedsiębiorstwa.

W tym artykule pokazano, jak utworzyć umowę AS2, EDIFACT lub X12, której można użyć podczas budowania rozwiązań integracji dla przedsiębiorstw dla scenariuszy B2B przy użyciu [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md) i [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Po utworzeniu umowy można użyć łączników AS2, EDIFACT lub X12 do wymiany komunikatów B2B.

Aby utworzyć umowy dotyczące wymiany komunikatów RosettaNet, zobacz temat [Exchange RosettaNet messages](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) do przechowywania umowy i innych artefaktów B2B. To konto integracji musi być skojarzone z subskrypcją platformy Azure.

* Co najmniej dwóch [partnerów handlowych](../logic-apps/logic-apps-enterprise-integration-partners.md) , które zostały już utworzone na koncie integracji. Umowa wymaga zarówno partnera hosta, jak i partnera gościa. Obaj partnerzy muszą używać tego samego kwalifikatora "tożsamość biznesowa" jako umowy, którą chcesz utworzyć, takich jak AS2, X12 lub EDIFACT.

* Opcjonalnie: aplikacja logiki, w której chcesz korzystać z umowy i wyzwalaczem, który uruchamia przepływ pracy aplikacji logiki. Aby po prostu utworzyć konto integracji i artefakty B2B, nie potrzebujesz aplikacji logiki. Jednak zanim aplikacja logiki będzie mogła używać artefaktów B2B na koncie integracji, należy połączyć konto integracji z aplikacją logiki. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Utwórz umowy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
W głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. W polu wyszukiwania wprowadź "integrację" jako filtr. Z wyników wybierz pozycję ten zasób: **konta integracji**

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. W obszarze **konta integracji** wybierz konto integracji, w którym chcesz utworzyć umowę.

   ![Wybierz konto integracji, dla którego chcesz utworzyć umowę](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. W okienku po prawej stronie w obszarze **składniki** wybierz kafelek **umowy** .

   ![Wybieranie "umów"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. W obszarze **umowy** wybierz pozycję **Dodaj**. W okienku **Dodaj** podaj informacje o Twojej umowie, na przykład:

   ![Wybierz pozycję "Dodaj"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | <*Umowa — nazwa*> | Nazwa umowy |
   | **Typ umowy** | Tak | **AS2**, **X12** lub **EDIFACT** | Typ protokołu dla umowy. Podczas tworzenia pliku umowy zawartość tego pliku musi być zgodna z typem umowy. | |  
   | **Partner hosta** | Tak | <*Nazwa partnera hosta*> | Partner hosta reprezentuje organizację, która określa umowę |
   | **Tożsamość hosta** | Tak | <*Identyfikator partnera hosta*> | Identyfikator partnera hosta |
   | **Partner gościa** | Tak | <*Gość — nazwa partnera*> | Partner gościa reprezentuje organizację, która wykonuje działalność z partnerem hosta |
   | **Tożsamość gościa** | Tak | <*Gość-partner-identyfikator*> | Identyfikator partnera gościa |
   | **Ustawienia odbierania** | Różnie | Różnie | Te właściwości określają, jak partner hosta odbiera wszystkie komunikaty przychodzące od partnera gościa w umowie. Aby uzyskać więcej informacji, zobacz odpowiedni typ umowy: <p>- [Ustawienia komunikatu AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Ustawienia komunikatu EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Ustawienia komunikatu X12](logic-apps-enterprise-integration-x12.md) |
   | **Ustawienia wysyłania** | Różnie | Różnie | Te właściwości określają, jak partner hosta wysyła wszystkie komunikaty wychodzące do partnera gościa w umowie. Aby uzyskać więcej informacji, zobacz odpowiedni typ umowy: <p>- [Ustawienia komunikatu AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Ustawienia komunikatu EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Ustawienia komunikatu X12](logic-apps-enterprise-integration-x12.md) |
   |||||

   > [!IMPORTANT]
   > Rozwiązanie dla umowy zależy od pasujących do tych elementów, które są zdefiniowane w partnerze i w komunikacie przychodzącym:
   >
   > * Kwalifikator i identyfikator nadawcy
   > * Kwalifikator i identyfikator odbiorcy
   >
   > Jeśli te wartości są zmieniane dla partnera, upewnij się, że Zaktualizowano również umowę.

1. Po zakończeniu tworzenia umowy na stronie **Dodaj** wybierz pozycję **OK**, a następnie wróć do konta integracji.

   Lista **umów** zawiera teraz nową umowę.

## <a name="edit-agreements"></a>Edytuj umowy

1. W [Azure Portal](https://portal.azure.com)w głównym menu platformy Azure wybierz pozycję **wszystkie usługi**.

1. W polu wyszukiwania wprowadź "integrację" jako filtr. Z wyników wybierz pozycję ten zasób: **konta integracji**

1. W obszarze **konta integracji** wybierz konto integracji z umową, którą chcesz edytować.

1. W okienku po prawej stronie w obszarze **składniki** wybierz kafelek **umowy** .

1. W obszarze **umowy** wybierz swoją umowę, a następnie wybierz pozycję **Edytuj**.

1. Utwórz i Zapisz zmiany.

## <a name="delete-agreements"></a>Usuń umowy

1. W [Azure Portal](https://portal.azure.com)w głównym menu platformy Azure wybierz pozycję **wszystkie usługi**.

1. W polu wyszukiwania wprowadź "integrację" jako filtr. Z wyników wybierz pozycję ten zasób: **konta integracji**

1. W obszarze **konta integracji** wybierz konto integracji z umową, którą chcesz usunąć.

1. W okienku po prawej stronie w obszarze **składniki** wybierz kafelek **umowy** .

1. W obszarze **umowy** wybierz swoją umowę, a następnie wybierz pozycję **Usuń**.

1. Potwierdź, że chcesz usunąć wybraną umowę.

## <a name="next-steps"></a>Następne kroki

* [Wymienianie komunikatów AS2](logic-apps-enterprise-integration-as2.md)
* [Wymienianie komunikatów EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Wymienianie komunikatów X12](logic-apps-enterprise-integration-x12.md)
