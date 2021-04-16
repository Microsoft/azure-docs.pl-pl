---
title: Samouczek — importowanie interfejsów API i zarządzanie nimi — azure API Management i Visual Studio Code | Microsoft Docs
description: Z tego samouczka dowiesz się, jak używać rozszerzenia Azure API Management dla usługi Visual Studio Code do importowania i testowania interfejsów API oraz zarządzania nimi.
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0090d981e93cee12f2feaaf7d2c12f341564f6ec
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482334"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Samouczek: używanie rozszerzenia API Management dla Visual Studio Code do importowania interfejsów API i zarządzania nimi

Z tego samouczka dowiesz się, jak używać rozszerzenia API Management dla Visual Studio Code typowych operacji w programie API Management. Użyj znanego Visual Studio Code do importowania, aktualizowania i testowania interfejsów API oraz zarządzania nimi.

Omawiane kwestie:

> [!div class="checklist"]
> * Importowanie interfejsu API do API Management
> * Edytowanie interfejsu API
> * Stosowanie API Management zabezpieczeń
> * Testowanie interfejsu API


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="Interfejs API w rozszerzeniu API Management":::

Aby uzyskać wprowadzenie do dodatkowych funkcji API Management, zobacz samouczki API Management samouczków przy [użyciu](import-and-publish.md)Azure Portal .

## <a name="prerequisites"></a>Wymagania wstępne
- Understand [Azure API Management terminology (Opis terminologii usługi Azure API Management)](api-management-terminology.md)
- Upewnij się, że [zainstalowano Visual Studio Code](https://code.visualstudio.com/) i najnowsze [rozszerzenie azure API Management dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)
- [Tworzenie API Management danych](vscode-create-service-instance.md)

## <a name="import-an-api"></a>Importowanie interfejsu API

Poniższy przykład importuje specyfikację OpenAPI w formacie JSON do API Management. Firma Microsoft udostępnia interfejs API zaplecza używany w tym przykładzie i hostuje go na platformie Azure pod witrynie `https://conferenceapi.azurewebsites.net?format=json` .

1. W Visual Studio Code wybierz ikonę platformy Azure na pasku działań.
1. W okienku Eksplorator rozwiń API Management utworzone wystąpienie.
1. Kliknij prawym przyciskiem myszy **pozycję Interfejsy API** i wybierz **polecenie Importuj z linku OpenAPI.** 
1. Po wyświetleniu monitu wprowadź następujące wartości:
    1. Link **do pliku OpenAPI** dla zawartości w formacie JSON. W tym przykładzie: *https://conferenceapi.azurewebsites.net?format=json* .
    Ten adres URL to usługa, która implementuje przykładowy interfejs API. API Management przekazuje żądania na ten adres.
    1. Nazwa **interfejsu API,** taka jak *demo-conference-api,* która jest unikatowa w API Management wystąpienia. Ta nazwa może zawierać tylko litery, cyfry i łączniki. Pierwszy i ostatni znak muszą być alfanumeryczne. Ta nazwa jest używana w ścieżce do wywołania interfejsu API.

Po pomyślnym zaimportowaniu interfejsu API zostanie on wyświetlony w okienku Eksploratora, a dostępne operacje interfejsu API zostaną wyświetlone w **węźle Operacje.**

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="Zaimportowany interfejs API w okienku Eksplorator":::

## <a name="edit-the-api"></a>Edytowanie interfejsu API

Interfejs API można edytować w Visual Studio Code. Na przykład edytuj opis Resource Manager JSON interfejsu API w oknie edytora, aby usunąć **protokół HTTP** używany do uzyskiwania dostępu do interfejsu API. Następnie wybierz **pozycję Zapisz**  >  **plik.**

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="Edytowanie opisu JSON":::

Aby edytować format OpenAPI, kliknij prawym przyciskiem myszy nazwę interfejsu API w okienku Eksploratora i wybierz **polecenie Edytuj interfejs OpenAPI.** Dokonaj zmian, a następnie wybierz **pozycję Zapisz**  >  **plik.**

## <a name="apply-policies-to-the-api"></a>Stosowanie zasad do interfejsu API 

API Management udostępnia [zasady,](api-management-policies.md) które można skonfigurować dla interfejsów API. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. Zasady mogą być globalne, które mają zastosowanie do wszystkich interfejsów API API Management wystąpieniu lub mogą być objęte zakresem określonej operacji interfejsu API lub interfejsu API.

W tej sekcji pokazano, jak zastosować niektóre typowe zasady ruchu wychodzącego do interfejsu API, które przekształcają odpowiedź interfejsu API. Zasady w tym przykładzie zmieniają nagłówki odpowiedzi i ukrywają oryginalne adresy URL zaplecza, które są wyświetlane w treści odpowiedzi.

1. W okienku Eksplorator wybierz pozycję **Zasady** w obszarze *demo-conference-api,* który został zaimportowany. Plik zasad zostanie otwarty w oknie edytora. Ten plik konfiguruje zasady dla wszystkich operacji w interfejsie API. 

1. Zaktualizuj plik przy użyciu następującej zawartości w `<outbound>` elemencie :
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * Pierwsze zasady `set-header` dodają niestandardowy nagłówek odpowiedzi w celach demonstracyjnych.
    * Drugie zasady `set-header` usuwa nagłówek **X-Powered-By,** jeśli istnieje. Ten nagłówek może ujawnić platformę aplikacji używaną w za zaplecza interfejsu API, a wydawcy często ją usuwają.
    * Zasady ponownie zapisuje linki (maski) w treści odpowiedzi, tak aby wskazać równoważne linki za `redirect-content-urls` pośrednictwem API Management bramy.
    
1. Zapisz plik. Jeśli zostanie wyświetlony monit, wybierz pozycję **Przekaż,** aby przekazać plik do chmury.

## <a name="test-the-api"></a>Testowanie interfejsu API

### <a name="get-the-subscription-key"></a>Uzyskiwanie klucza subskrypcji

Aby przetestować zaimportowany interfejs API i zastosowane zasady, potrzebujesz klucza subskrypcji dla API Management wystąpienia.

1. W okienku Eksplorator kliknij prawym przyciskiem myszy nazwę wystąpienia API Management.
1. Wybierz **pozycję Copy Subscription Key (Skopiuj klucz subskrypcji).**

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Kopiowanie klucza subskrypcji":::

### <a name="test-an-api-operation"></a>Testowanie operacji interfejsu API

1. W okienku Eksplorator rozwiń węzeł **Operacje** w obszarze zaimportowanego interfejsu *demo-conference-api.*
1. Wybierz operację, taką jak *GetSpeakers,* a następnie kliknij prawym przyciskiem myszy operację i wybierz polecenie **Test Operation (Testuj operację).**
1. W oknie edytora obok klucza **Ocp-Apim-Subscription-Key** zastąp skopiowanym `{{SubscriptionKey}}` kluczem subskrypcji.
1. Wybierz pozycję **Wyślij wniosek**. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Wysyłanie żądania interfejsu API z Visual Studio Code":::

Gdy żądanie zakończy się powodzeniem, zaplecza odpowiada **200 OK** i niektóre dane.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="Operacja testowania interfejsu API":::

Zwróć uwagę na następujące szczegóły w odpowiedzi:
* Nagłówek **Niestandardowy** jest dodawany do odpowiedzi.
* Nagłówek **X-Powered-By** nie jest wyświetlany w odpowiedzi.
* Adresy URL zaplecza interfejsu API są przekierowywani do bramy API Management, w tym przypadku `https://apim-hello-world.azure-api.net/demo-conference-api` .

### <a name="trace-the-api-operation"></a>Śledzenie operacji interfejsu API

Aby uzyskać szczegółowe informacje o śledzeniu, które ułatwiają debugowanie operacji interfejsu API, wybierz link wyświetlany obok pozycji **Ocp-APIM-Trace-Location.** 

Plik JSON w tej lokalizacji zawiera informacje o śledzeniu ruchu przychodzącego, zaplecza i ruchu wychodzącego, dzięki czemu można ustalić, gdzie po zsyłaniu żądania występują jakiekolwiek problemy.

> [!TIP]
> Podczas testowania operacji interfejsu API rozszerzenie API Management umożliwia opcjonalne debugowanie zasad [(dostępne](api-management-debug-policies.md) w warstwie usługi dewelopera).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy wystąpienie usługi nie będzie już API Management, kliknij  prawym przyciskiem myszy i wybierz polecenie Otwórz w portalu, aby usunąć API Management [i](get-started-create-service-instance.md#clean-up-resources) jej grupę zasobów.

Alternatywnie możesz wybrać **pozycję** Usuń API Management, aby usunąć tylko wystąpienie API Management (ta operacja nie powoduje usunięcia jego grupy zasobów).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="Usuwanie API Management z VS Code":::

## <a name="next-steps"></a>Następne kroki

W tym samouczku wprowadzono kilka funkcji rozszerzenia API Management dla Visual Studio Code, których można używać do importowania interfejsów API i zarządzania nimi. W tym samouczku omówiono:

> [!div class="checklist"]
> * Importowanie interfejsu API do API Management
> * Edytowanie interfejsu API
> * Stosowanie API Management zasad
> * Testowanie interfejsu API

Rozszerzenie API Management udostępnia dodatkowe funkcje do pracy z interfejsami API. Na przykład można [debugować policys](api-management-debug-policies.md) (dostępne w warstwie usługi dewelopera) lub utworzyć nazwane wartości i [zarządzać nimi.](api-management-howto-properties.md)
