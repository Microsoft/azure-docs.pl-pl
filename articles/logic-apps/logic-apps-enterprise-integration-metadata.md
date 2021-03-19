---
title: Zarządzaj metadanymi artefaktów konta integracji
description: Dodawanie lub Pobieranie metadanych artefaktów z kont integracji w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792471"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Zarządzanie metadanymi artefaktów na kontach integracji za pomocą usługi Azure Logic Apps i Pakietu integracyjnego dla przedsiębiorstw

Możesz zdefiniować niestandardowe metadane dla artefaktów na kontach integracji i pobrać te metadane w czasie wykonywania, aby aplikacja logiki mogła ich użyć. Na przykład można dostarczyć metadanych dla artefaktów, takich jak partnerzy, umowy, schematy i mapy — wszystkie metadane magazynu z użyciem par klucz-wartość. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Podstawowe [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) z artefaktami, w których chcesz dodać metadane, na przykład: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Umowa](logic-apps-enterprise-integration-agreements.md)
  * [Schemat](logic-apps-enterprise-integration-schemas.md)
  * [Mapa](logic-apps-enterprise-integration-maps.md)

* Aplikacja logiki, która jest połączona z kontem integracji i metadanymi artefaktów, których chcesz użyć. Jeśli Twoja aplikacja logiki nie jest już połączona, Dowiedz się, [jak połączyć Aplikacje logiki z kontami integracji](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Jeśli nie masz jeszcze aplikacji logiki, Dowiedz się, [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Dodaj wyzwalacz i akcje, które mają być używane do zarządzania metadanymi artefaktów. Lub, aby wypróbować je, Dodaj wyzwalacz, taki jak **żądanie** lub **http** do aplikacji logiki.

## <a name="add-metadata-to-artifacts"></a>Dodawanie metadanych do artefaktów

1. Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure Portal</a> przy użyciu poświadczeń konta Azure. Znajdź i Otwórz konto integracji.

1. Wybierz artefakt, do którego chcesz dodać metadane, a następnie wybierz pozycję **Edytuj**. Wprowadź szczegóły metadanych dla tego artefaktu, na przykład:

   ![Wprowadź metadane](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

1. Aby wyświetlić te metadane w definicji JavaScript Object Notation (JSON) dla konta integracji, wybierz opcję **Edytuj jako plik JSON** , aby otworzyć Edytor JSON: 

   ![Plik JSON dla metadanych partnera](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Pobierz metadane artefaktu

1. W Azure Portal Otwórz aplikację logiki, która jest połączona z żądanym kontem integracji. 

1. W Projektancie aplikacji logiki, jeśli dodajesz krok do pobierania metadanych w ramach wyzwalacza lub ostatniej akcji w przepływie pracy, wybierz pozycję **nowy krok**  >  **Dodaj akcję**. 

1. W polu wyszukiwania wprowadź ciąg "konto integracji". W polu wyszukiwania wybierz pozycję **Wszystko**. Z listy Akcje wybierz tę akcję: **Wyszukiwanie artefaktów konta integracji — konto integracji**

   ![Wybierz pozycję "wyszukiwanie artefaktów konta integracji"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Podaj te informacje dla artefaktu, który chcesz znaleźć:

   | Właściwość | Wymagany | Wartość | Opis | 
   |----------|---------|-------|-------------| 
   | **Typ artefaktu** | Tak | **Schemat**, **Mapa**, **partner**, **Umowa** lub typ niestandardowy | Typ żądanego artefaktu | 
   | **Nazwa artefaktu** | Tak | <*Nazwa artefaktu*> | Nazwa żądanego artefaktu | 
   ||| 

   Załóżmy na przykład, że chcesz pobrać metadane artefaktu partnera handlowego:

   ![Wybierz typ artefaktu i wprowadź nazwę artefaktu](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Dodaj akcję, która ma być obsługiwana dla tej metadanych, na przykład:

   1. W obszarze Akcja **odnośnika artefaktu konta integracji** wybierz pozycję **Następny krok**, a następnie wybierz pozycję **Dodaj akcję**. 

   1. W polu wyszukiwania wprowadź ciąg "http". W polu wyszukiwania wybierz pozycję **wbudowane** i wybierz tę akcję: **http-http**

      ![Dodaj akcję HTTP](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Podaj informacje dotyczące metadanych artefaktów, którymi chcesz zarządzać. 

      Załóżmy na przykład, że chcesz pobrać `routingUrl` metadane, które zostały dodane wcześniej w tym temacie. Poniżej przedstawiono wartości właściwości, które można określić: 

      | Właściwość | Wymagany | Wartość | Opis | 
      |----------|----------|-------|-------------| 
      | **Metoda** | Tak | <*Operacja do uruchomienia*> | Operacja HTTP do uruchomienia na artefaktie. Na przykład ta akcja HTTP używa metody **Get** . | 
      | **URI** | Tak | <*metadane — lokalizacja*> | Aby uzyskać dostęp do `routingUrl` wartości metadanych z pobranego artefaktu, można użyć wyrażenia, na przykład: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Nagłówki** | Nie | <*wartości nagłówka*> | Wszystkie dane wyjściowe nagłówka z wyzwalacza, które chcesz przekazać do akcji HTTP. Na przykład, aby przekazać wartość właściwości wyzwalacza `headers` : można użyć wyrażenia, na przykład: <p>`@triggeroutputs()['headers']` | 
      | **Treść** | Nie | <*treść — zawartość*> | Każda inna zawartość, która ma zostać przekazana przez właściwość akcji HTTP `body` . Ten przykład przekazuje wartości artefaktu `properties` do akcji http: <p>1. kliknij wewnątrz właściwości **Body** , aby wyświetlić listę zawartości dynamicznej. Jeśli nie zostaną wyświetlone żadne właściwości, wybierz pozycję **Zobacz więcej**. <br>2. z listy zawartości dynamicznej w obszarze **Wyszukiwanie artefaktów konta integracji** wybierz pozycję **Właściwości**. | 
      |||| 

      Na przykład:

      ![Określ wartości i wyrażenia dla akcji HTTP](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Aby sprawdzić informacje podane dla akcji HTTP, Wyświetl definicję JSON aplikacji logiki. Na pasku narzędzi projektanta aplikacji logiki wybierz pozycję **Widok kodu** , aby wyświetlić definicję JSON aplikacji, na przykład:

      ![Definicja JSON aplikacji logiki](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Po przełączeniu się z powrotem do projektanta aplikacji logiki zostaną wyświetlone wszystkie użyte wyrażenia, na przykład:

      ![Rozpoznane wyrażenia w Projektancie aplikacji logiki](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o umowach](logic-apps-enterprise-integration-agreements.md)
