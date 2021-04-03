---
title: Importowanie interfejsu API protokołu SOAP i konwertowanie na interfejs API REST | Microsoft Docs
description: Dowiedz się, jak zaimportować interfejs API protokołu SOAP, przekonwertować go na API Management, a następnie przetestować interfejs API w portalach platformy Azure i deweloperów.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: a87f4d0748d14ab995f75279b6a192f350165d6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87843831"
---
# <a name="import-a-soap-api-and-convert-to-rest"></a>Importowanie interfejsu API SOAP i konwertowanie go na interfejs API REST

W tym artykule przedstawiono sposób importowania interfejsu API protokołu SOAP i przekształcania go w interfejs API REST. Artykuł przedstawia również sposób testowania interfejsu API usługi APIM.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Importowanie interfejsu API SOAP i konwertowanie go na interfejs API REST
> * Testowanie interfejsu API w witrynie Azure Portal
> * Testowanie interfejsu API w portalu dla deweloperów

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Wybierz pozycję **WSDL** z listy **Dodaj nowy interfejs API**.

    ![Interfejs API protokołu SOAP](./media/restify-soap-api/wsdl-api.png)
3. W obszarze **Specyfikacja WSDL** wprowadź adres URL, pod którym znajduje się Twój interfejs API protokołu SOAP.
4. Kliknij przycisk radiowy **Konwersja z interfejsu SOAP na interfejs REST**. Po kliknięciu tej opcji usługa APIM spróbuje automatycznie przeprowadzić przekształcanie między kodem XML i JSON. W tym przypadku użytkownicy powinni wywoływać interfejs API jako interfejs API RESTful, który zwraca kod JSON. Usługa APIM konwertuje każde żądanie na wywołanie protokołu SOAP.

    ![Konwersja z interfejsu SOAP na interfejs REST](./media/restify-soap-api/soap-to-rest.png)

5. Naciśnij klawisz Tab.

    Następujące pola są wypełniane przy użyciu informacji z interfejsu API protokołu SOAP: Nazwa wyświetlana, Nazwa, Opis.
6. Dodaj sufiks adresu URL interfejsu API. Sufiks to nazwa, która identyfikuje dany interfejs API w tym wystąpieniu usługi APIM. Musi ona być unikatowa w tym wystąpieniu usługi APIM.
9. Opublikuj interfejs API przez skojarzenie go z produktem. W takim przypadku jest używany produkt typu „*Nieograniczony*”.  Jeśli chcesz, aby interfejs API został opublikowany i był dostępny dla deweloperów, dodaj go do produktu. Możesz to zrobić podczas tworzenia interfejsu API lub ustawić tę opcję później.

    Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc domyślnie posiada subskrypcję każdego produktu.

    Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe:

    * **Starter (początkowy)**
    * **Nieograniczona liczba**   
10. Wybierz przycisk **Utwórz**.

## <a name="test-the-new-api-in-the-azure-portal"></a>Przetestuj nowy interfejs API w Azure Portal

Operacje mogą być wywoływane bezpośrednio z witryny Azure Portal, która zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API.  

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Naciśnij kartę **Test**.
3. Wybierz operację.

    Na stronie zostaną wyświetlone pola parametrów zapytania i pola nagłówków. Jeden z nagłówków to „Ocp-Apim-Subscription-Key” dla klucza subskrypcji produktu, który został skojarzony z tym interfejsem API. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc klucz zostanie uzupełniony automatycznie. 
1. Kliknij pozycję **Wyślij**.

    Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)