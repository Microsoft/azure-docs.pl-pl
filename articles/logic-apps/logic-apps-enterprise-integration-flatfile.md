---
title: Kodowanie lub dekodowanie plików prostych
description: Koduj lub Dekoduj pliki płaskie integracji przedsiębiorstwa w Azure Logic Apps przy użyciu Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87001489"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>Kodowanie i dekodowanie plików prostych w usłudze Azure Logic Apps za pomocą pakietu integracyjnego dla przedsiębiorstw

Przed wysłaniem zawartości XML do partnera biznesowego w scenariuszu biznesowym (B2B) możesz chcieć najpierw zakodować tę zawartość. Tworząc aplikację logiki, można kodować i dekodować pliki płaskie przy użyciu łącznika **prostego pliku** . Aplikacja logiki może pobrać tę zawartość XML z różnych źródeł, takich jak wyzwalacz żądania, inna aplikacja lub inne [Łączniki obsługiwane przez Azure Logic Apps](../connectors/apis-list.md). Aby uzyskać więcej informacji, zobacz [co to jest Azure Logic Apps](logic-apps-overview.md)?

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, w której ma być używany **prosty łącznik plików** , oraz wyzwalacz, który uruchamia przepływ pracy aplikacji logiki. Łącznik **prostego pliku** zawiera tylko akcje, a nie wyzwalacze. Można użyć wyzwalacza lub innej akcji, aby dostarczyć zawartość XML do aplikacji logiki w celu kodowania lub dekodowania. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj się z [przewodnikiem Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) skojarzone z subskrypcją platformy Azure i połączone z [aplikacją logiki](./logic-apps-enterprise-integration-create-integration-account.md#link-account) , w której zamierzasz używać łącznika **prostego pliku** . Zarówno aplikacja logiki, jak i konto integracji muszą istnieć w tej samej lokalizacji lub regionie platformy Azure.

* [Schemat](logic-apps-enterprise-integration-schemas.md) pliku prostego, który został przekazany do konta integracji w celu kodowania lub dekodowania zawartości XML

* Co najmniej dwóch [partnerów handlowych](logic-apps-enterprise-integration-partners.md) , które zostały już zdefiniowane na koncie integracji

## <a name="add-flat-file-encode-action"></a>Dodawanie akcji kodowania pliku prostego

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W obszarze wyzwalacza lub akcji w aplikacji logiki wybierz pozycję **nowy krok**  >  **Dodaj akcję**. W tym przykładzie jest używany wyzwalacz żądania o nazwie, **gdy odebrane żądanie HTTP**i obsługuje żądania przychodzące spoza aplikacji logiki.

   > [!TIP]
   > Udostępnianie schematu JSON jest opcjonalne. Jeśli masz przykładowy ładunek z żądania przychodzącego, wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**, wprowadź przykładowy ładunek i wybierz pozycję **gotowe**. Schemat pojawia się w polu **schemat JSON treści żądania** .

1. W obszarze **Wybierz akcję**wprowadź `flat file` . Z listy Akcje wybierz pozycję Ta akcja: **kodowanie pliku prostego**

   ![Wybieranie akcji "Kodowanie plików prostych"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. Kliknij wewnątrz pola **zawartość** , aby wyświetlić listę zawartość dynamiczna. Z listy w sekcji **po odebraniu żądania HTTP** wybierz właściwość **Body** , która zawiera dane wyjściowe treści żądania z wyzwalacza i zawartość do zakodowania.

   ![Wybierz zawartość do kodowania z listy zawartości dynamicznej](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > Jeśli właściwość **Body** nie jest widoczna na liście zawartości dynamicznej, wybierz pozycję **Zobacz więcej** obok pozycji **po odebraniu żądania HTTP** .
   > Możesz również bezpośrednio wprowadzić zawartość do dekodowania w polu **zawartość** .

1. Z listy **Nazwa schematu** wybierz schemat, który znajduje się na połączonym koncie integracji do użycia na potrzeby kodowania, na przykład:

   ![Wybierz schemat, który ma być używany do kodowania](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > Jeśli na liście nie ma schematu, konto integracji nie zawiera żadnych plików schematu do użycia w celu kodowania. Przekaż schemat, który ma być używany na potrzeby konta integracji.

1. Zapisz aplikację logiki. Aby przetestować łącznik, należy wysłać żądanie do punktu końcowego HTTPS, który jest wyświetlany w właściwości **http post adresu URL** wyzwalacza żądania i dołączyć zawartość XML, która ma być zakodowana w treści żądania.

Teraz wszystko jest gotowe do skonfigurowania akcji kodowania pliku prostego. W aplikacji Real World można przechowywać dane zakodowane w aplikacji biznesowych, na przykład w usłudze Salesforce. Można też wysyłać zakodowane dane do partnera handlowego. Aby wysłać dane wyjściowe z akcji kodowania do usługi Salesforce lub do partnera handlowego, użyj innych [łączników dostępnych w Azure Logic Apps](../connectors/apis-list.md).

## <a name="add-flat-file-decode-action"></a>Dodawanie akcji dekodowania pliku prostego

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W obszarze wyzwalacza lub akcji w aplikacji logiki wybierz pozycję **nowy krok**  >  **Dodaj akcję**. W tym przykładzie jest używany wyzwalacz żądania o nazwie, **gdy odebrane żądanie HTTP**i obsługuje żądania przychodzące spoza aplikacji logiki.

   > [!TIP]
   > Udostępnianie schematu JSON jest opcjonalne. Jeśli masz przykładowy ładunek z żądania przychodzącego, wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**, wprowadź przykładowy ładunek i wybierz pozycję **gotowe**. Schemat pojawia się w polu **schemat JSON treści żądania** .

1. W obszarze **Wybierz akcję**wprowadź `flat file` . Z listy Akcje wybierz akcję: **dekodowanie pliku prostego**

   ![Wybierz akcję "rozkodowanie prostego pliku"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. Kliknij wewnątrz pola **zawartość** , aby wyświetlić listę zawartość dynamiczna. Z listy w sekcji **po odebraniu żądania HTTP** wybierz właściwość **Body** , która zawiera dane wyjściowe treści żądania z wyzwalacza i zawartość do zdekodowania.

   ![Wybierz zawartość do zdekodowania z listy zawartości dynamicznej](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > Jeśli właściwość **Body** nie jest widoczna na liście zawartości dynamicznej, wybierz pozycję **Zobacz więcej** obok pozycji **po odebraniu żądania HTTP** . Możesz również bezpośrednio wprowadzić zawartość do dekodowania w polu **zawartość** .

1. Z listy **Nazwa schematu** wybierz schemat, który znajduje się na połączonym koncie integracji, który ma być używany do dekodowania, na przykład:

   ![Wybierz schemat, który ma być używany do dekodowania](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > Jeśli na liście nie ma schematu, konto integracji nie zawiera żadnych plików schematu do użycia podczas dekodowania. Przekaż schemat, który ma być używany na potrzeby konta integracji.

1. Zapisz aplikację logiki. Aby przetestować łącznik, należy wysłać żądanie do punktu końcowego HTTPS, który jest wyświetlany w właściwości **http post adresu URL** wyzwalacza żądania i dołączyć zawartość XML, która ma zostać zdekodowana w treści żądania.

Teraz można skonfigurować akcję dekodowania prostego pliku. W świecie rzeczywistym można przechowywać zdekodowane dane w aplikacji biznesowej (LOB), na przykład w usłudze Salesforce. Można też wysyłać zdekodowane dane do partnera handlowego. Aby wysłać dane wyjściowe z akcji dekodowania do usługi Salesforce lub do partnera handlowego, użyj innych [łączników dostępnych w Azure Logic Apps](../connectors/apis-list.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)
