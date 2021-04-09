---
title: Samouczek — Importowanie interfejsów API i zarządzanie nimi — API Management platformy Azure i Visual Studio Code | Microsoft Docs
description: W tym samouczku dowiesz się, jak używać rozszerzenia API Management platformy Azure do Visual Studio Code do importowania i testowania interfejsów API oraz zarządzania nimi.
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0dea8e43d5f09b84c5795bc257cf3331ad919fcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649550"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Samouczek: Używanie rozszerzenia API Management Visual Studio Code do importowania interfejsów API i zarządzania nimi

W ramach tego samouczka nauczysz się używać rozszerzenia API Management Visual Studio Code do wykonywania typowych operacji w API Management. Używaj znanego środowiska Visual Studio Code do importowania, aktualizowania i testowania interfejsów API oraz zarządzania nimi.

Omawiane kwestie:

> [!div class="checklist"]
> * Importowanie interfejsu API do API Management
> * Edytowanie interfejsu API
> * Zastosuj zasady API Management
> * Testowanie interfejsu API


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="Interfejs API w rozszerzeniu API Management":::

Aby zapoznać się z wprowadzeniem do dodatkowych funkcji API Management, zobacz samouczki API Management przy użyciu [Azure Portal](import-and-publish.md).

## <a name="prerequisites"></a>Wymagania wstępne
- Opis [terminologii dotyczącej usługi Azure API Management](api-management-terminology.md)
- Upewnij się, że zainstalowano [Visual Studio Code](https://code.visualstudio.com/) i najnowsze [rozszerzenie platformy Azure API Management dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)
- [Tworzenie wystąpienia API Management](vscode-create-service-instance.md)

## <a name="import-an-api"></a>Importowanie interfejsu API

Poniższy przykład importuje specyfikację OpenAPI w formacie JSON do API Management. Firma Microsoft udostępnia interfejs API zaplecza, który jest używany w tym przykładzie, i hostuje go na platformie Azure pod adresem `https://conferenceapi.azurewebsites.net?format=json` .

1. W Visual Studio Code wybierz ikonę platformy Azure na pasku działania.
1. W okienku Eksploratora rozwiń utworzone wystąpienie API Management.
1. Kliknij prawym przyciskiem myszy pozycję **interfejsy API**, a następnie wybierz pozycję **Importuj z openapi**. 
1. Po wyświetleniu monitu wprowadź następujące wartości:
    1. **Link openapi** do zawartości w formacie JSON. Na potrzeby tego przykładu: *https://conferenceapi.azurewebsites.net?format=json* .
    Ten adres URL jest usługą implementującą przykładowy interfejs API. API Management przekazuje żądania do tego adresu.
    1. **Nazwa interfejsu API**, taka jak *demo-Konferencja-API*, która jest unikatowa w wystąpieniu API Management. Ta nazwa może zawierać tylko litery, cyfry i łączniki. Pierwszy i ostatni znak musi być alfanumeryczny. Ta nazwa jest używana w ścieżce do wywoływania interfejsu API.

Po pomyślnym zaimportowaniu interfejsu API pojawia się on w okienku Eksploratora, a dostępne operacje interfejsu API są wyświetlane w węźle **operacje** .

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="Zaimportowany interfejs API w okienku Eksploratora":::

## <a name="edit-the-api"></a>Edytowanie interfejsu API

Interfejs API można edytować w Visual Studio Code. Na przykład Edytuj opis Menedżer zasobów JSON interfejsu API w oknie edytora, aby usunąć protokół **http** używany do uzyskiwania dostępu do interfejsu API. Następnie wybierz pozycję **plik**  >  **Zapisz**.

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="Edytuj opis JSON":::

Aby edytować format OpenAPI, kliknij prawym przyciskiem myszy nazwę interfejsu API w okienku Eksploratora i wybierz polecenie **Edytuj openapi**. Wprowadź zmiany, a następnie wybierz pozycję **plik**  >  **Zapisz**.

## <a name="apply-policies-to-the-api"></a>Stosowanie zasad do interfejsu API 

API Management zawiera [zasady](api-management-policies.md) , które można skonfigurować dla interfejsów API. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. Zasady mogą być globalne, które mają zastosowanie do wszystkich interfejsów API w wystąpieniu API Management lub mogą być ograniczone do określonego interfejsu API lub operacji interfejsu API.

W tej sekcji pokazano, jak zastosować niektóre typowe zasady wychodzące do interfejsu API, który przekształci odpowiedź interfejsu API. Zasady w tym przykładzie zmieniają nagłówki odpowiedzi i ukrywają oryginalne adresy URL zaplecza, które są wyświetlane w treści odpowiedzi.

1. W okienku Eksplorator wybierz pozycję **zasady** w obszarze *Demonstracja-Konferencja-interfejs API* , który został zaimportowany. Plik zasad zostanie otwarty w oknie edytora. Ten plik konfiguruje zasady dla wszystkich operacji w interfejsie API. 

1. Zaktualizuj plik o następującą zawartość w `<outbound>` elemencie:
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

    * Pierwsze `set-header` zasady dodaje niestandardowy nagłówek odpowiedzi dla celów demonstracyjnych.
    * Druga `set-header` zasada usuwa nagłówek **X-d-by** , jeśli istnieje. Ten nagłówek może ujawnić strukturę aplikacji używaną w zapleczu interfejsu API, a wydawcy często ją usuwają.
    * `redirect-content-urls`Zasady ponownie zapisuje linki w treści odpowiedzi, aby wskazywały równoważne linki za pośrednictwem bramy API Management.
    
1. Zapisz plik. Jeśli zostanie wyświetlony monit, wybierz pozycję **Przekaż** , aby przekazać plik do chmury.

## <a name="test-the-api"></a>Testowanie interfejsu API

### <a name="get-the-subscription-key"></a>Pobierz klucz subskrypcji

Aby przetestować zaimportowany zaimportowany interfejs API i zastosowane zasady, musisz mieć klucz subskrypcji dla wystąpienia API Management.

1. W okienku Eksplorator kliknij prawym przyciskiem myszy nazwę wystąpienia API Management.
1. Wybierz pozycję **Kopiuj klucz subskrypcji**.

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Kopiuj klucz subskrypcji":::

### <a name="test-an-api-operation"></a>Testowanie operacji interfejsu API

1. W okienku Eksplorator rozwiń węzeł **operacje** w obszarze zaimportowano polecenie *demo-Konferencja-interfejs API* .
1. Wybierz operację, taką jak *getgłośników*.
1. W oknie edytora obok pozycji **OCP-APIM-Subscription-Key** Zastąp ciąg `{{SubscriptionKey}}` kluczem subskrypcji, który został skopiowany.
1. Wybierz pozycję **Wyślij wniosek**. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Wyślij żądanie interfejsu API z Visual Studio Code":::

Gdy żądanie zakończy się powodzeniem, zaplecze reaguje z **200 OK** i danymi.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="Operacja testowania interfejsu API":::

Zwróć uwagę na następujące szczegóły dotyczące odpowiedzi:
* Nagłówek **niestandardowy** zostanie dodany do odpowiedzi.
* Nagłówek **X-by** nie pojawia się w odpowiedzi.
* Adresy URL zaplecza interfejsu API są przekierowywane do bramy API Management w tym przypadku `https://apim-hello-world.azure-api.net/demo-conference-api` .

### <a name="trace-the-api-operation"></a>Śledzenie operacji interfejsu API

Aby uzyskać szczegółowe informacje dotyczące śledzenia, które ułatwiają debugowanie operacji interfejsu API, wybierz link wyświetlany obok pozycji **OCP-APIM-Trace-Location**. 

Plik JSON w tej lokalizacji zawiera dane przychodzące, zaplecze i informacje o śledzeniu wychodzącym, dzięki czemu można określić, gdzie występują problemy po wprowadzeniu żądania.

> [!TIP]
> Podczas testowania operacji interfejsu API rozszerzenie API Management zezwala na opcjonalne [debugowanie zasad](api-management-debug-policies.md) (dostępne w warstwie usług dla deweloperów).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń wystąpienie API Management, klikając prawym przyciskiem myszy i wybierając pozycję **Otwórz w portalu** , aby [usunąć usługę API Management](get-started-create-service-instance.md#clean-up-resources) i jej grupę zasobów.

Alternatywnie można wybrać polecenie **usuń API Management** , aby usunąć tylko wystąpienie API Management (Ta operacja nie powoduje usunięcia grupy zasobów).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="Usuń wystąpienie API Management z VS Code":::

## <a name="next-steps"></a>Następne kroki

W tym samouczku wprowadzono kilka funkcji rozszerzenia API Management Visual Studio Code, których można użyć do importowania interfejsów API i zarządzania nimi. W tym samouczku omówiono:

> [!div class="checklist"]
> * Importowanie interfejsu API do API Management
> * Edytowanie interfejsu API
> * Zastosuj zasady API Management
> * Testowanie interfejsu API

Rozszerzenie API Management udostępnia dodatkowe funkcje do pracy z interfejsami API. Na przykład [zasady debugowania](api-management-debug-policies.md) (dostępne w warstwie usług dla deweloperów) lub tworzenia i zarządzania [nazwanymi wartościami](api-management-howto-properties.md).