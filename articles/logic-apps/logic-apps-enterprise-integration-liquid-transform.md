---
title: Konwertowanie JSON i XML przy użyciu szablonów płynnych
description: Przekształć dane JSON i XML przy użyciu szablonów płynnych jako mapy w Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0362c9ed4f736474dbd49e1bfaf1373e0f48acd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94992713"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>Przekształć dane JSON i XML przy użyciu szablonów płynnych jako mapy w Azure Logic Apps

Jeśli chcesz wykonać podstawowe przekształcenia JSON w aplikacjach logiki, możesz użyć natywnych [operacji na danych](../logic-apps/logic-apps-perform-data-operations.md) , takich jak **Tworzenie** lub **Analizowanie JSON**. W przypadku zaawansowanych i złożonych przekształceń JSON do transformacji JSON, które mają elementy takie jak iteracje, przepływy kontroli i zmienne, Utwórz i Użyj szablonów, które opisują te przekształcenia przy użyciu języka szablonu typu ["Open Source](https://shopify.github.io/liquid/) ". Można również [wykonywać inne przekształcenia](#other-transformations), na przykład JSON do tekstu, XML, JSON i XML do tekstu.

Aby można było przeprowadzić transformację płynną w aplikacji logiki, należy najpierw utworzyć szablon płynny, który definiuje odpowiednie mapowanie. Następnie możesz [przekazać szablon jako mapę](../logic-apps/logic-apps-enterprise-integration-maps.md) do [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Po dodaniu akcji **Przekształć dane JSON do** aplikacji logiki, można wybrać szablon płynny jako mapę dla akcji, która ma zostać użyta.

W tym artykule przedstawiono sposób wykonywania następujących zadań:

* Utwórz szablon płynny.
* Dodaj szablon do konta integracji.
* Dodaj akcję transformacji ciekłej do aplikacji logiki.
* Wybierz szablon jako mapę, której chcesz użyć.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Podstawowa wiedza na temat [języka szablonu ciekłego](https://shopify.github.io/liquid/)

  > [!NOTE]
  > Akcja **Przekształć JSON do formatu JSON-Liquid** jest zgodna z [implementacją DotLiquid dla cieczy](https://github.com/dotliquid/dotliquid), która różni się w określonych przypadkach od [implementacji Shopify dla cieczy](https://shopify.github.io/liquid). Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące szablonu płynności](#liquid-template-considerations).

## <a name="create-the-template"></a>Tworzenie szablon

1. Utwórz szablon płynny, który będzie używany jako Mapa dla transformacji JSON. Możesz użyć dowolnego narzędzia do edycji.

   Na potrzeby tego przykładu Utwórz przykładowy szablon płynny zgodnie z opisem w tej sekcji:

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

1. Zapisz szablon przy użyciu `.liquid` rozszerzenia. W tym przykładzie użyto wartości `SimpleJsonToJsonTemplate.liquid`.

## <a name="upload-the-template"></a>Przekaż szablon

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W polu wyszukiwania Azure Portal wprowadź `integration accounts` i wybierz pozycję **konta integracji**.

   ![Znajdź "konta integracji"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Znajdź i wybierz swoje konto integracji.

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. W okienku **Przegląd** w obszarze **składniki** wybierz pozycję **mapy**.

    ![Wybierz kafelek "Maps"](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. W okienku **mapy** wybierz pozycję **Dodaj** i podaj następujące szczegóły dotyczące mapy:

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | `JsonToJsonTemplate` | Nazwa mapy, która jest w tym przykładzie "JsonToJsonTemplate" |
   | **Typ mapy** | **obrotow** | Typ mapy. W przypadku transformacji JSON do formatu JSON należy wybrać pozycję **Liquid**. |
   | **Mapa** | `SimpleJsonToJsonTemplate.liquid` | Istniejący szablon płynu lub plik mapy do użycia na potrzeby transformacji, czyli "SimpleJsonToJsonTemplate. Liquid" w tym przykładzie. Aby znaleźć ten plik, można użyć selektora plików. Limity rozmiaru mapy można znaleźć w temacie [limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   |||

   ![Dodaj szablon płynny](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>Dodaj akcję transformacji ciekłej

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

Za pomocą programu [Poster](https://www.getpostman.com/postman) lub podobnego narzędzia, dane wejściowe JSON są ogłaszane w aplikacji logiki. Przekształcone dane wyjściowe JSON z aplikacji logiki wyglądają podobnie jak w tym przykładzie:

![Przykładowe dane wyjściowe](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>Zagadnienia dotyczące szablonu ciekłego

* Szablony ciekłe są zgodne z [limitami rozmiaru plików dla map](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) w Azure Logic Apps.

* Akcja **Przekształć JSON do formatu JSON-Liquid** jest zgodna z [implementacją DotLiquid dla cieczy](https://github.com/dotliquid/dotliquid). Ta implementacja jest portem do .NET Framework z [implementacji Shopify dla cieczy](https://shopify.github.io/liquid/) i różni się w [określonych przypadkach](https://github.com/dotliquid/dotliquid/issues).

  Poniżej przedstawiono znane różnice:

  * Akcja **Przekształć JSON do formatu JSON-Liquid** natywnie wyprowadza ciąg, który może zawierać dane JSON, XML, HTML i tak dalej. Akcja ciekłe wskazuje, że oczekiwane dane wyjściowe tekstu z szablonu ciekłego są ciągiem JSON. Akcja instruuje aplikację logiki, aby przeanalizować dane wejściowe jako obiekt JSON i stosuje otokę, tak aby ciecz mogła interpretować strukturę JSON. Po przekształceniu akcja nakazuje aplikacji logiki przeanalizowanie danych wyjściowych z powrotem do formatu JSON.

    DotLiquid nie zna natywnie kodu JSON, dlatego należy się upewnić, że wykorzystasz znak ukośnika odwrotnego ( `\` ) i wszystkie inne zastrzeżone znaki JSON.

  * Jeśli szablon używa [filtrów płynnych](https://shopify.github.io/liquid/basics/introduction/#filters), upewnij się, że korzystasz z [konwencji nazewnictwa DotLiquid i C#](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing), które używają *wielkich liter*. W przypadku wszystkich transformacji płynnych upewnij się, że nazwy filtrów w szablonie używają również wielkości liter w zdaniu. W przeciwnym razie filtry nie będą działały.

    Na przykład, gdy używasz `replace` filtru, użyj `Replace` , nie `replace` . Ta sama reguła ma zastosowanie w przypadku wypróbowania przykładów w witrynie [DotLiquid online](http://dotliquidmarkup.org/try-online). Aby uzyskać więcej informacji, zobacz [Shopify Liquid filters](https://shopify.dev/docs/themes/liquid/reference/filters) and [DotLiquid Liquid filters](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters). Specyfikacja Shopify zawiera przykłady dla każdego filtru, dlatego w celu porównania można wypróbować te przykłady w [DotLiquid — Wypróbuj w trybie online](http://dotliquidmarkup.org/try-online).

  * `json`Filtr z filtrów rozszerzeń Shopify nie jest obecnie [zaimplementowany w DotLiquid](https://github.com/dotliquid/dotliquid/issues/384). Zazwyczaj ten filtr służy do przygotowywania danych wyjściowych do analizy ciągów JSON, ale zamiast tego należy użyć `Replace` filtru zamiast tego.

  * Filtr standardowy `Replace` w [implementacji DotLiquid](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425) używa [dopasowania wyrażenia regularnego (](/dotnet/standard/base-types/regular-expression-language-quick-reference)regularnego), podczas gdy [implementacja Shopify](https://shopify.github.io/liquid/filters/replace/) używa [prostego dopasowania ciągu](https://github.com/Shopify/liquid/issues/202). Obie implementacje wyglądają tak samo, dopóki nie użyjesz znaku wyrażenia regularnego lub znaku ucieczki w parametrze Match.

    Na przykład, aby wyjść z wyrażenia regularnego, zarezerwowanych ukośnika odwrotnego ( `\` ), użyj `| Replace: '\\', '\\'` , i nie `| Replace: '\', '\\'` . W poniższych przykładach pokazano, jak `Replace` Filtr działa inaczej przy próbie ucieczki znaku ukośnika odwrotnego. Ta wersja działa prawidłowo:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    Z tym wynikiem:

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    Ta wersja kończy się niepowodzeniem:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    Z powodu tego błędu:

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    Aby uzyskać więcej informacji, zobacz temat [Zastąp standardowy filtr używa dopasowania wzorca wyrażenia regularnego...](https://github.com/dotliquid/dotliquid/issues/385)

  * `Sort`Filtr w [implementacji DotLiquid](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326) sortuje elementy w tablicy lub kolekcji według właściwości, ale z następującymi różnicami:<p>

    * Postępuj [zgodnie z zachowaniem Sort_natural Shopify](https://shopify.github.io/liquid/filters/sort_natural/), a nie [Shopify](https://shopify.github.io/liquid/filters/sort/).

    * Sortuje tylko w kolejności ciągów znaków alfanumerycznych. Aby uzyskać więcej informacji, zobacz [sortowanie numeryczne](https://github.com/Shopify/liquid/issues/980).

    * Używa kolejności bez uwzględniania *wielkości* liter, a nie kolejności z uwzględnieniem wielkości liter. Aby uzyskać więcej informacji, zobacz [sort Filter nie obserwuje zachowania wielkości liter w specyfikacji Shopify]( https://github.com/dotliquid/dotliquid/issues/393).

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>Inne przekształcenia wykorzystujące ciecz

Płyn nie jest ograniczony tylko do transformacji JSON. Możesz również użyć cieczy, aby wykonać inne przekształcenia, na przykład:

* [JSON do tekstu](#json-text)
* [XML do formatu JSON](#xml-json)
* [XML do tekstu](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>Przekształć JSON do tekstu

Oto szablon płynny, który jest używany w tym przykładzie:

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:

![Przykład danych wyjściowych JSON do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>Przekształć XML w notację JSON

Oto szablon płynny, który jest używany w tym przykładzie:

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

`JSONArrayFor`Pętla jest mechanizmem niestandardowej pętli dla danych wejściowych XML, dzięki czemu można tworzyć ładunki JSON, które unikają końcowej przecinka. Ponadto `where` warunek dla tego niestandardowego mechanizmu zapętlenia używa nazwy elementu XML do porównania, a nie wartości elementu, podobnie jak inne filtry płynne. Aby uzyskać więcej informacji, zobacz [głębokie szczegółowe na zasadach dotyczących zestawu i kolekcji rzeczy](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy).

Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:

![Przykład danych wyjściowych XML do formatu JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>Przekształć XML do tekstu

Oto szablon płynny, który jest używany w tym przykładzie:

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Poniżej przedstawiono przykładowe dane wejściowe i wyjściowe:

![Przykład danych wyjściowych XML do tekstu](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Następne kroki

* [Shopify i przykłady w języku ciekłym](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid — Wypróbuj w trybie online](http://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [DotLiquid — problemy z usługą GitHub](https://github.com/dotliquid/dotliquid/issues/)
* Dowiedz się więcej o [mapach](../logic-apps/logic-apps-enterprise-integration-maps.md)
