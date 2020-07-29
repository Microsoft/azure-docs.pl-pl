---
title: Konwertowanie danych JSON przy użyciu transformacji płynnych
description: Tworzenie transformacji lub map dla zaawansowanych przekształceń JSON przy użyciu szablonu Logic Apps i płynu
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: d2598dfe9d7972dcb764abf4a1239613a1e8417a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80879177"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Wykonywanie zaawansowanych przekształceń JSON przy użyciu szablonów Liquid w usłudze Azure Logic Apps

W aplikacjach logiki można wykonywać podstawowe przekształcenia JSON z akcjami operacji danych natywnych, takimi jak **Redaguj** lub **Analizuj dane JSON**. Aby przeprowadzić zaawansowane przekształcenia JSON, możesz tworzyć szablony lub mapy z [płynnością](https://shopify.github.io/liquid/), czyli językiem szablonu Open-Source dla elastycznych aplikacji sieci Web. Szablon Liquid definiuje sposób przekształcania danych wyjściowych JSON i obsługuje bardziej złożone przekształcenia JSON, takie jak iteracje, przepływy sterowania, zmienne itp.

Aby można było wykonać transformację płynną w aplikacji logiki, należy najpierw zdefiniować mapowanie JSON na format JSON z szablonem płynnym i sklepem, który jest mapowany na koncie integracji. W tym artykule przedstawiono sposób tworzenia i używania tego szablonu lub mapy ciekłej.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Podstawowe [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Podstawowa wiedza na temat [języka szablonu ciekłego](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Utwórz szablon płynny lub mapę dla konta integracji

1. Na potrzeby tego przykładu Utwórz przykładowy szablon płynny opisany w tym kroku. W szablonie płynnym można używać [filtrów ciekłych](https://shopify.github.io/liquid/basics/introduction/#filters), które używają konwencji nazewnictwa [DotLiquid](https://github.com/dotliquid/dotliquid) i C#.

   > [!NOTE]
   > Upewnij się, że nazwy filtrów używają *wielkości liter* w szablonie. W przeciwnym razie filtry nie będą działały. Ponadto mapy mają [limity rozmiaru plików](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits).

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

1. W [Azure Portal](https://portal.azure.com)w polu wyszukiwania Azure wpisz `integration accounts` , a następnie wybierz pozycję **konta integracji**.

   ![Znajdź "konta integracji"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Znajdź i wybierz swoje konto integracji.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. W okienku **Przegląd** w obszarze **składniki**wybierz pozycję **mapy**.

    ![Wybierz kafelek "Maps"](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. W okienku **mapy** wybierz pozycję **Dodaj** i podaj następujące szczegóły dotyczące mapy:

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------|
   | **Nazwa** | `JsonToJsonTemplate` | Nazwa mapy, która jest w tym przykładzie "JsonToJsonTemplate" | 
   | **Typ mapy** | **obrotow** | Typ mapy. W przypadku transformacji JSON do formatu JSON należy wybrać pozycję **Liquid**. | 
   | **Mapa** | `SimpleJsonToJsonTemplate.liquid` | Istniejący szablon płynu lub plik mapy do użycia na potrzeby transformacji, czyli "SimpleJsonToJsonTemplate. Liquid" w tym przykładzie. Aby znaleźć ten plik, można użyć selektora plików. Limity rozmiaru mapy można znaleźć w temacie [limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Dodaj szablon płynny](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Dodawanie akcji ciekłej dla transformacji JSON

1. W Azure Portal wykonaj następujące kroki, aby [utworzyć pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. W Projektancie aplikacji logiki Dodaj [wyzwalacz żądania](../connectors/connectors-native-reqres.md#add-request) do aplikacji logiki.

1. W obszarze wyzwalacza wybierz pozycję **nowy krok**. W polu wyszukiwania wprowadź `liquid` jako filtr, a następnie wybierz tę akcję: **Przekształć JSON do JSON-Liquid**

   ![Znajdź i wybierz akcję ciekłej](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Otwórz listę **Mapa** i wybierz szablon płynny, czyli "JsonToJsonTemplate" w tym przykładzie.

   ![Wybieranie mapy](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Jeśli lista Maps jest pusta, najprawdopodobniej Twoja aplikacja logiki nie jest połączona z kontem integracji. 
   Aby połączyć aplikację logiki z kontem integracji, które ma szablon lub mapę płynną, wykonaj następujące kroki:

   1. W menu aplikacji logiki wybierz pozycję **Ustawienia przepływu pracy**.

   1. Z listy **Wybierz konto integracji** wybierz swoje konto integracji i wybierz pozycję **Zapisz**.

      ![Połącz aplikację logiki z kontem integracji](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Teraz Dodaj właściwość **Content** do tej akcji. Otwórz listę **Dodaj nowy parametr** i wybierz pozycję **zawartość**.

   ![Dodaj właściwość "Content" do akcji](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Aby ustawić wartość właściwości **Content** , kliknij wewnątrz pola **Content** (zawartość), aby wyświetlić listę zawartości dynamicznej. Wybierz token **treści** reprezentujący treść danych wyjściowych z wyzwalacza.

   ![Wybierz token "Body" dla wartości właściwości "Content"](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Gdy wszystko będzie gotowe, akcja będzie wyglądać jak następujący przykład:

   ![Zakończono akcję "Przekształć JSON w notację JSON"](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Wyślij dane wejściowe JSON do aplikacji logiki z programu [Poster](https://www.getpostman.com/postman) lub podobnego narzędzia. Przekształcone dane wyjściowe JSON z aplikacji logiki wyglądają podobnie jak w tym przykładzie:
  
![Przykładowe dane wyjściowe](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Więcej przykładowych akcji w postaci ciekłej
Płyn nie jest ograniczony tylko do transformacji JSON. Oto inne dostępne akcje transformacji, które używają cieczy.

* Przekształć JSON do tekstu
  
  Oto szablon płynu używany do tego przykładu:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:
  
   ![Przykład danych wyjściowych JSON do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Przekształć XML w notację JSON
  
  Oto szablon płynu używany do tego przykładu:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:

   ![Przykład danych wyjściowych XML do formatu JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Przekształć XML do tekstu
  
  Oto szablon płynu używany do tego przykładu:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:

   ![Przykład danych wyjściowych XML do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw")  
* [Dowiedz się więcej o mapach](../logic-apps/logic-apps-enterprise-integration-maps.md "Dowiedz się więcej na temat map integracji przedsiębiorstwa")  

