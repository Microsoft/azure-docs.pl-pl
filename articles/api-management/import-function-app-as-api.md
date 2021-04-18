---
title: Importowanie aplikacji funkcji platformy Azure jako interfejsu API w usłudze API Management
titleSuffix: Azure API Management
description: W tym artykule pokazano, jak zaimportować aplikację funkcji platformy Azure do usługi Azure API Management jako interfejs API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: apimpm
ms.openlocfilehash: 8e8047fc6865bab8f4aac2315b269bf7526b1e42
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599382"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importowanie aplikacji funkcji platformy Azure jako interfejsu API w usłudze Azure API Management

Usługa Azure API Management obsługuje importowanie aplikacji funkcji platformy Azure jako nowych interfejsów API lub dodawanie ich do istniejących interfejsów API. Proces automatycznie generuje w aplikacji funkcji platformy Azure klucz hosta, który jest następnie przypisywany do nazwanej wartości w usłudze Azure API Management.

W tym artykule oujemy importowanie i testowanie aplikacji funkcji platformy Azure jako interfejsu API w usłudze Azure API Management. 

Poznasz następujące czynności:

> [!div class="checklist"]
> * Importowanie aplikacji funkcji platformy Azure jako interfejsu API
> * Dołączanie aplikacji funkcji platformy Azure do interfejsu API
> * Wyświetlanie nowego klucza hosta aplikacji funkcji platformy Azure i nazwanej wartości usługi Azure API Management
> * Testowanie interfejsu API w witrynie Azure Portal

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ przewodnik [Szybki start Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md) Azure.
* Upewnij się, że Twoja subskrypcja zawiera aplikację usługi Azure Functions. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji funkcji platformy Azure](../azure-functions/functions-get-started.md). Funkcje muszą mieć wyzwalacz HTTP i poziom autoryzacji ustawiony na *Anonimowe* lub *Funkcja*.

> [!NOTE]
> Możesz użyć rozszerzenia API Management dla Visual Studio Code do importowania interfejsów API i zarządzania nimi. Postępuj zgodnie [z API Management samouczkiem rozszerzenia,](visual-studio-code-tutorial.md) aby zainstalować rozszerzenie i rozpocząć pracę.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Importowanie aplikacji funkcji platformy Azure jako nowego interfejsu API

Aby utworzyć nowy interfejs API z poziomu aplikacji funkcji platformy Azure, wykonaj poniższe kroki.

1. Przejdź do usługi API Management w chmurze Azure Portal a następnie wybierz pozycję **Interfejsy API** z menu.

2. Z listy **Dodaj nowy interfejs API** wybierz pozycję **Aplikacja funkcji**.

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="Zrzut ekranu przedstawiający kafelek Aplikacja funkcji.":::

3. Kliknij przycisk **Przeglądaj**, aby wybrać funkcje do zaimportowania.

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="Zrzut ekranu przedstawiający przycisk Przeglądaj.":::

4. Kliknij w sekcji **Aplikacja funkcji**, aby wybrać pozycję z listy dostępnych aplikacji funkcji.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Zrzut ekranu przedstawiający sekcję Aplikacja funkcji.":::

5. Znajdź aplikację funkcji, z której chcesz zaimportować funkcje, kliknij ją i naciśnij przycisk **Wybierz**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Zrzut ekranu przedstawiający aplikację funkcji, z której chcesz zaimportować funkcje, i przycisk Wybierz.":::

6. Wybierz funkcje, które chcesz zaimportować, a następnie kliknij przycisk **Wybierz**.
    * Funkcje oparte na wyzwalaczu HTTP można importować tylko z *poziomami autoryzacji Anonimowe* *lub* Funkcje.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Zrzut ekranu przedstawiający funkcje do zaimportowania i przycisk Wybierz.":::

7. Przełącz się do widoku **Pełny** i przypisz **Produkt** do nowego interfejsu API. 
8. W razie potrzeby określ inne pola podczas tworzenia lub skonfiguruj je później za pomocą **karty** Ustawienia. 
    * Ustawienia zostały wyjaśnione w [samouczku Importowanie i publikowanie pierwszego interfejsu API.](import-and-publish.md#import-and-publish-a-backend-api)

    >[!NOTE]
    > Produkty to skojarzenia co najmniej jednego interfejsu API oferowanego deweloperom za pośrednictwem portalu dla deweloperów. Najpierw deweloperzy muszą subskrybować produkt, aby uzyskać dostęp do interfejsu API. Po zasubskrybowania uzyskają klucz subskrypcji dla dowolnego interfejsu API w tym produkcie. Jako twórca API Management jesteś administratorem i domyślnie subskrybujesz każdy produkt.
    >
    > Każde API Management jest dostępne z dwoma domyślnymi przykładami produktów:
    > - **Starter (początkowy)**
    > - **Nieograniczona liczba**

9. Kliknij pozycję **Utwórz**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Dołączanie aplikacji funkcji platformy Azure do istniejącego interfejsu API

Aby dołączyć aplikację funkcji platformy Azure do istniejącego interfejsu API, wykonaj poniższe kroki.

1. W Twoim wystąpieniu usługi **Azure API Management** wybierz pozycję **Interfejsy API** z menu po lewej stronie.

2. Wybierz interfejs API, który chcesz zaimportować do aplikacji funkcji platformy Azure. Kliknij przycisk **...** i wybierz pozycję **Importuj** z menu kontekstowego.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="Zrzut ekranu przedstawiający opcję menu Importuj.":::

3. Kliknij kafelek **Aplikacja funkcji**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="Zrzut ekranu przedstawiający kafelek Aplikacja funkcji.":::

4. W oknie podręcznym kliknij przycisk **Przeglądaj**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="Zrzut ekranu przedstawiający przycisk Przeglądaj.":::

5. Kliknij w sekcji **Aplikacja funkcji**, aby wybrać pozycję z listy dostępnych aplikacji funkcji.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Zrzut ekranu przedstawiający listę aplikacji funkcji.":::

6. Znajdź aplikację funkcji, z której chcesz zaimportować funkcje, kliknij ją i naciśnij przycisk **Wybierz**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Zrzut ekranu przedstawiający aplikację funkcji, z której chcesz zaimportować funkcje.":::

7. Wybierz funkcje, które chcesz zaimportować, a następnie kliknij przycisk **Wybierz**.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Zrzut ekranu przedstawiający funkcje, które chcesz zaimportować.":::

8. Kliknij **pozycję Importuj**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="Dołączanie z poziomu aplikacji funkcji":::

## <a name="authorization"></a><a name="authorization"></a> Autoryzacji

Zaimportowanie aplikacji funkcji platformy Azure powoduje automatyczne wygenerowanie:

* Klucz hosta wewnątrz aplikacji funkcji o nazwie apim-{*nazwa wystąpienia usługi Azure API Management*},
* Nazwana wartość wewnątrz wystąpienia usługi Azure API Management o nazwie { nazwa wystąpienia aplikacji funkcji platformy *Azure*}-key, która zawiera utworzony klucz hosta.

W przypadku interfejsów API utworzonych po 4 kwietnia 2019 r. klucz hosta jest przekazywany w żądaniach HTTP z usługi API Management do aplikacji funkcji w nagłówku. Starsze interfejsy API przekażą klucz hosta jako [parametr zapytania](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). To zachowanie można zmienić za pomocą wywołania `PATCH Backend` [interfejsu API REST](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) w *jednostce zaplecza skojarzonej* z aplikacją funkcji.

> [!WARNING]
> Usunięcie lub zmiana wartości klucza hosta aplikacji funkcji platformy Azure lub nazwanej wartości usługi Azure API Management spowoduje przerwę w komunikacji między usługami. Wartości nie są synchronizowane automatycznie.
>
> Jeśli musisz wymienić klucz hosta, upewnij się, że została zmodyfikowana także nazwana wartość w usłudze Azure API Management.

### <a name="access-azure-function-app-host-key"></a>Uzyskiwanie dostępu do klucza hosta aplikacji funkcji platformy Azure

1. Przejdź do Twojego wystąpienia aplikacji funkcji platformy Azure.

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="Zrzut ekranu przedstawiający wybieranie wystąpienia aplikacji funkcji.":::

2. W sekcji **Funkcje** w bocznym menu nawigacji wybierz pozycję **Klucze aplikacji.**

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="Zrzut ekranu przedstawiający opcję ustawienia Aplikacje funkcji.":::

3. Klucze znajdują się w **sekcji Klucze** hosta.

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="Zrzut ekranu przedstawiający sekcję Klucze hosta.":::

### <a name="access-the-named-value-in-azure-api-management"></a>Uzyskiwanie dostępu do nazwanej wartości w usłudze Azure API Management

Przejdź do Twojego wystąpienia usługi Azure API Management i wybierz pozycję **Nazwane wartości** z menu po lewej stronie. Tam jest przechowywany klucz aplikacji funkcji platformy Azure.

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="Dodawanie z poziomu aplikacji funkcji":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Testowanie nowego interfejsu API w Azure Portal

Operacje można wywoływać bezpośrednio w witrynie Azure Portal. Witryna Azure Portal to wygodny sposób wyświetlania i testowania operacji interfejsu API.  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="Zrzut ekranu przedstawiający procedurę testowania.":::

1. Wybierz interfejs API utworzony w poprzedniej sekcji.

2. Wybierz kartę **Test**.

3. Wybierz operację, którą chcesz przetestować.

    * Na stronie są wyświetlane pola parametrów zapytania i nagłówków. 
    * Jeden z nagłówków to "Ocp-Apim-Subscription-Key" dla klucza subskrypcji produktu skojarzonego z tym interfejsem API. 
    * Jako twórca API Management jesteś już administratorem, więc klucz jest wypełniany automatycznie. 

4. Wybierz pozycję **Wyślij**.

    * Gdy test zakończy się pomyślnie, na zadumień odpowiada **200 OK** i niektóre dane.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)
