---
title: Importowanie aplikacja funkcji platformy Azure jako interfejsu API w programie API Management
titleSuffix: Azure API Management
description: Ten samouczek przedstawia sposób importowania aplikacji funkcji platformy Azure do usługi Azure API Management jako interfejsu API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: be8a8f35ece946d1be907b2ca13a640bebbb4ee5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627027"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importowanie aplikacji funkcji platformy Azure jako interfejsu API w usłudze Azure API Management

Usługa Azure API Management obsługuje importowanie aplikacji funkcji platformy Azure jako nowych interfejsów API lub dodawanie ich do istniejących interfejsów API. Proces automatycznie generuje w aplikacji funkcji platformy Azure klucz hosta, który jest następnie przypisywany do nazwanej wartości w usłudze Azure API Management.

Ten artykuł przeprowadzi Cię przez proces importowania aplikacji funkcji platformy Azure jako interfejsu API w usłudze Azure API Management. Omówiono w nim także proces testowania.

Poznasz następujące czynności:

> [!div class="checklist"]
> * Importowanie aplikacji funkcji platformy Azure jako interfejsu API
> * Dołączanie aplikacji funkcji platformy Azure do interfejsu API
> * Wyświetlanie nowego klucza hosta aplikacji funkcji platformy Azure i nazwanej wartości usługi Azure API Management
> * Testowanie interfejsu API w witrynie Azure Portal
> * Testowanie interfejsu API w portalu dla deweloperów

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj procedury przedstawione w przewodniku Szybki start [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
* Upewnij się, że Twoja subskrypcja zawiera aplikację usługi Azure Functions. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji funkcji platformy Azure](../azure-functions/functions-create-first-azure-function.md#create-a-function-app). Musi ona zawierać funkcje z wyzwalaczem HTTP oraz ustawienie poziomu autoryzacji *Anonimowe* lub *Funkcja*.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Importowanie aplikacji funkcji platformy Azure jako nowego interfejsu API

Aby utworzyć nowy interfejs API z poziomu aplikacji funkcji platformy Azure, wykonaj poniższe kroki.

1. Przejdź do usługi API Management w Azure Portal i wybierz opcję **interfejsy API** z menu.

2. Z listy **Dodaj nowy interfejs API** wybierz pozycję **Aplikacja funkcji**.

    ![Dodawanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/add-01.png)

3. Kliknij przycisk **Przeglądaj**, aby wybrać funkcje do zaimportowania.

    ![Dodawanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/add-02.png)

4. Kliknij w sekcji **Aplikacja funkcji**, aby wybrać pozycję z listy dostępnych aplikacji funkcji.

    ![Dodawanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/add-03.png)

5. Znajdź aplikację funkcji, z której chcesz zaimportować funkcje, kliknij ją i naciśnij przycisk **Wybierz**.

    ![Dodawanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/add-04.png)

6. Wybierz funkcje, które chcesz zaimportować, a następnie kliknij przycisk **Wybierz**.

    ![Dodawanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Możesz zaimportować tylko te funkcje, które są oparte na wyzwalaczu HTTP i mają ustawienie poziomu autoryzacji *Anonimowe* lub *Funkcja*.

7. Przełącz się do widoku **Pełny** i przypisz **Produkt** do nowego interfejsu API. Jeśli to konieczne, określ inne pola podczas tworzenia lub skonfiguruj je później, przechodząc do karty **Ustawienia** . Ustawienia są wyjaśnione w samouczku [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md#import-and-publish-a-backend-api) .
8. Kliknij przycisk **Utwórz**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Dołączanie aplikacji funkcji platformy Azure do istniejącego interfejsu API

Aby dołączyć aplikację funkcji platformy Azure do istniejącego interfejsu API, wykonaj poniższe kroki.

1. W Twoim wystąpieniu usługi **Azure API Management** wybierz pozycję **Interfejsy API** z menu po lewej stronie.

2. Wybierz interfejs API, który chcesz zaimportować do aplikacji funkcji platformy Azure. Kliknij przycisk **...** i wybierz pozycję **Importuj** z menu kontekstowego.

    ![Dołączanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/append-01.png)

3. Kliknij kafelek **Aplikacja funkcji**.

    ![Dołączanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/append-02.png)

4. W oknie podręcznym kliknij przycisk **Przeglądaj**.

    ![Dołączanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/append-03.png)

5. Kliknij w sekcji **Aplikacja funkcji**, aby wybrać pozycję z listy dostępnych aplikacji funkcji.

    ![Dodawanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/add-03.png)

6. Znajdź aplikację funkcji, z której chcesz zaimportować funkcje, kliknij ją i naciśnij przycisk **Wybierz**.

    ![Dodawanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/add-04.png)

7. Wybierz funkcje, które chcesz zaimportować, a następnie kliknij przycisk **Wybierz**.

    ![Dodawanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/add-05.png)

8. Kliknij pozycję **Importuj**.

    ![Dołączanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a><a name="authorization"></a> Zgody

Zaimportowanie aplikacji funkcji platformy Azure powoduje automatyczne wygenerowanie:

* Klucz hosta w aplikacja funkcji o nazwie APIM-{*Nazwa wystąpienia usługi API Management platformy Azure*},
* Nazwana wartość w wystąpieniu API Management platformy Azure o nazwie {*Nazwa wystąpienia aplikacja funkcji platformy Azure*} — klucz, który zawiera utworzony klucz hosta.

W przypadku interfejsów API utworzonych po 4 kwietnia 2019 klucz hosta jest przesyłany w żądaniach HTTP od API Management do aplikacja funkcji w nagłówku. Starsze interfejsy API przekazują klucz hosta jako [parametr zapytania](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). To zachowanie można zmienić za pomocą `PATCH Backend` [wywołania interfejsu API REST](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) w jednostce *zaplecza* skojarzonej z aplikacja funkcji.

> [!WARNING]
> Usunięcie lub zmiana wartości klucza hosta aplikacji funkcji platformy Azure lub nazwanej wartości usługi Azure API Management spowoduje przerwanie komunikacji między usługami. Wartości nie są synchronizowane automatycznie.
>
> Jeśli musisz wymienić klucz hosta, upewnij się, że została zmodyfikowana także nazwana wartość w usłudze Azure API Management.

### <a name="access-azure-function-app-host-key"></a>Uzyskiwanie dostępu do klucza hosta aplikacji funkcji platformy Azure

1. Przejdź do Twojego wystąpienia aplikacji funkcji platformy Azure.

2. Wybierz pozycję **Ustawienia aplikacji funkcji** z omówienia.

    ![Dodawanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/keys-02-a.png)

3. Klucz znajduje się w sekcji **Klucze hosta**.

    ![Dodawanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Uzyskiwanie dostępu do nazwanej wartości w usłudze Azure API Management

Przejdź do Twojego wystąpienia usługi Azure API Management i wybierz pozycję **Nazwane wartości** z menu po lewej stronie. Tam jest przechowywany klucz aplikacji funkcji platformy Azure.

![Dodawanie z poziomu aplikacji funkcji](./media/import-function-app-as-api/keys-01.png)

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Przetestuj nowy interfejs API w Azure Portal

Operacje można wywoływać bezpośrednio w witrynie Azure Portal. Witryna Azure Portal to wygodny sposób wyświetlania i testowania operacji interfejsu API.  

1. Wybierz interfejs API utworzony w poprzedniej sekcji.

2. Wybierz kartę **Test**.

3. Wybierz operację.

    Na stronie zostaną wyświetlone pola parametrów zapytania i pola nagłówków. Jeden z nagłówków to **OCP-APIM-Subscription-Key**dla klucza subskrypcji produktu skojarzonego z tym interfejsem API. Jeśli utworzono wystąpienie API Management, oznacza to, że już jesteś administratorem, więc klucz zostanie uzupełniony automatycznie. 

4. Wybierz pozycję **Wyślij**.

    Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)
