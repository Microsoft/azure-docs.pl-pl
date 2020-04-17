---
title: Wysyłanie i odbieranie wiadomości AS2 dla B2B
description: Komunikaty AS2 programu Exchange dla scenariuszy integracji przedsiębiorstwa B2B przy użyciu aplikacji Azure Logic Apps z pakietem integracji przedsiębiorstwa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0e7c34e42d0ab68a5dab9718075f02a85322ce6c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458832"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Wymiana komunikatów AS2 na integrację przedsiębiorstwa B2B w usłudze Azure Logic Apps z pakietem integracji dla przedsiębiorstw

> [!IMPORTANT]
> Oryginalny łącznik AS2 jest przestarzały, więc upewnij się, że zamiast tego używasz łącznika **AS2 (v2).** Ta wersja zapewnia takie same możliwości jak oryginalna wersja, jest natywny dla środowiska wykonawczego aplikacji logiki i zapewnia znaczną poprawę wydajności pod względem przepływności i rozmiaru wiadomości. Ponadto natywny łącznik w wersji 2 nie wymaga utworzenia połączenia z kontem integracji. Zamiast tego, zgodnie z opisem w wymaganiach wstępnych, upewnij się, że należy połączyć konto integracji z aplikacją logiki, w której planujesz używać łącznika.

Aby pracować z komunikatami AS2 w usłudze Azure Logic Apps, można użyć łącznika AS2, który udostępnia wyzwalacze i akcje do zarządzania komunikacją AS2. Na przykład w celu ustanowienia zabezpieczeń i niezawodności podczas przesyłania wiadomości można użyć następujących akcji:

* [ **As2 Encode** akcji](#encode) w celu zapewnienia szyfrowania, podpisywania cyfrowego i potwierdzania za pośrednictwem powiadomień dyspozycji wiadomości (MDN), które pomagają wspierać nie odrzucenia. Na przykład ta akcja stosuje nagłówki AS2/HTTP i wykonuje następujące zadania po skonfigurowaniu:

  * Podpisuje wiadomości wychodzące.
  * Szyfruje wiadomości wychodzące.
  * Kompresuje wiadomość.
  * Przesyła nazwę pliku w nagłówku MIME.

* [ **As2 Dekodowanie** akcji](#decode) w celu zapewnienia odszyfrowywania, podpisywania cyfrowego i potwierdzeń za pośrednictwem powiadomień dyspozycji wiadomości (MDN). Na przykład ta akcja wykonuje następujące zadania:

  * Przetwarza nagłówki AS2/HTTP.
  * Uzgadnia odebrane mdny z oryginalnymi wiadomościami wychodzącymi.
  * Aktualizuje i koreluje rekordy w bazie danych bez odrzucenia.
  * Zapisuje rekordy dla raportowania stanu AS2.
  * Wyprowadza zawartość ładunku jako kodowane base64.
  * Określa, czy mdn są wymagane. Na podstawie umowy AS2 określa, czy mdn powinny być synchroniczne lub asynchroniczne.
  * Generuje synchroniczne lub asynchroniczne mdny na podstawie umowy AS2.
  * Ustawia tokeny korelacji i właściwości na MDN.

  Ta akcja wykonuje również następujące zadania po skonfigurowaniu:

  * Weryfikuje podpis.
  * Odszyfrowuje wiadomości.
  * Dekompresuje wiadomość.
  * Sprawdź i nie zezwalaj na duplikaty identyfikatorów wiadomości.

W tym artykule pokazano, jak dodać akcje kodowania i dekodowania AS2 do istniejącej aplikacji logiki.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby uzyskać bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

* Aplikacja logiki, z której chcesz użyć łącznika AS2 i wyzwalacza, który uruchamia przepływ pracy aplikacji logiki. Łącznik AS2 udostępnia tylko akcje, a nie wyzwalacze. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) i szybki [start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) skojarzone z subskrypcją platformy Azure i połączone z aplikacją logiki, w której zamierzasz używać łącznika AS2. Zarówno aplikacja logiki i konta integracji musi istnieć w tej samej lokalizacji lub regionu platformy Azure.

* Co najmniej dwóch [partnerów handlowych,](../logic-apps/logic-apps-enterprise-integration-partners.md) które zostały już zdefiniowane na koncie integracji przy użyciu kwalifikatora tożsamości AS2.

* Przed użyciem łącznika AS2 należy utworzyć [umowę](../logic-apps/logic-apps-enterprise-integration-agreements.md) AS2 między partnerami handlowymi i przechowywać tę umowę na koncie integracyjnym.

* Jeśli używasz [usługi Azure Key Vault](../key-vault/general/overview.md) do zarządzania certyfikatami, sprawdź, czy klucze magazynu zezwalają na operacje **szyfrowania** i **odszyfrowywania.** W przeciwnym razie akcje kodowania i dekodowania nie powiodą się.

  W witrynie Azure portal przejdź do klucza w magazynie kluczy, przejrzyj **dozwolone operacje**klucza i potwierdź, że są wybrane operacje **szyfrowania** i **odszyfrowywania,** na przykład:

  ![Sprawdzanie operacji klucza przechowalni](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Kodowanie wiadomości AS2

1. Jeśli jeszcze tego nie zrobiłeś, w [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

1. W projektancie dodaj nową akcję do aplikacji logiki.

1. W obszarze **Wybierz akcję** i pole wyszukiwania wybierz pozycję **Wszystkie**. W polu wyszukiwania wpisz "as2 encode" i upewnij się, że wybierzesz akcję AS2 (v2): **AS2 Encode**

   ![Wybierz "AS2 Encode"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Teraz podaj informacje dotyczące tych właściwości:

   | Właściwość | Opis |
   |----------|-------------|
   | **Wiadomość do zakodowania** | Ładunek wiadomości |
   | **AS2 od** | Identyfikator nadawcy wiadomości określony w umowie AS2 |
   | **AS2 do** | Identyfikator odbiornika wiadomości określony w umowie AS2 |
   |||

   Przykład:

   ![Właściwości kodowania wiadomości](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Jeśli występują problemy podczas wysyłania podpisanych lub zaszyfrowanych wiadomości, należy rozważyć wypróbowanie różnych formatów algorytmów SHA256. Specyfikacja AS2 nie dostarcza żadnych informacji o formatach SHA256, więc każdy dostawca używa własnej implementacji lub formatu.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Dekodowanie wiadomości AS2

1. Jeśli jeszcze tego nie zrobiłeś, w [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

1. W projektancie dodaj nową akcję do aplikacji logiki.

1. W obszarze **Wybierz akcję** i pole wyszukiwania wybierz pozycję **Wszystkie**. W polu wyszukiwania wpisz "dekodowanie as2" i upewnij się, że wybierzesz akcję AS2 (v2): **AS2 Decode**

   ![Wybierz "DEKODowanie AS2"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Aby **okno Message zakodować** i **message nagłówki** właściwości, wybierz te wartości z poprzednich wyzwalaczy lub wyjść akcji.

   Załóżmy na przykład, że aplikacja logiki odbiera wiadomości za pośrednictwem wyzwalacza żądania. Można wybrać wyjścia z tego wyzwalacza.

   ![Wybierz treść i nagłówki z wyjść żądania](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Przykład

Aby spróbować wdrożyć w pełni operacyjną aplikację logiki i przykładowy scenariusz AS2, zobacz [szablon i scenariusz aplikacji logiki AS2.](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/as2/). 

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)oryginalna wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
