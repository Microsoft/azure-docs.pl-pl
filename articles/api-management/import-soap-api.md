---
title: Importowanie interfejsu API protokołu SOAP przy użyciu witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak zaimportować standardową reprezentację XML interfejsu API protokołu SOAP, a następnie przetestować interfejs API w portalach platformy Azure i deweloperów.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 81ded79ee72fb7c2d89898595602cb3e6d7ae5e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011068"
---
# <a name="import-soap-api"></a>Importowanie interfejsu API protokołu SOAP

W tym artykule przedstawiono sposób importowania standardowej reprezentacji kodu XML dla interfejsu API protokołu SOAP. W tym artykule przedstawiono również sposób testowania API Management API.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Importowanie interfejsu API protokołu SOAP
> * Testowanie interfejsu API w witrynie Azure Portal
> * Testowanie interfejsu API w portalu dla deweloperów

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

1. Przejdź do usługi API Management w Azure Portal i wybierz opcję **interfejsy API** z menu.
2. Wybierz pozycję **WSDL** z listy **Dodaj nowy interfejs API**.

    ![interfejs api protokołu soap](./media/import-soap-api/wsdl-api.png)
3. W obszarze **Specyfikacja WSDL** wprowadź adres URL, pod którym znajduje się Twój interfejs API protokołu SOAP.
4. Przycisk radiowy **Przekazywanie protokołu SOAP** jest wybierany domyślnie. Ten wybór spowoduje, że interfejs API będzie uwidaczniany jako protokół SOAP. Użytkownik musi stosować reguły protokołu SOAP. Jeśli chcesz przekształcić interfejs API przy użyciu stylu REST, postępuj zgodnie z instrukcjami podanymi w temacie [Import a SOAP API and convert it to REST](restify-soap-api.md) (Importowanie interfejsu API protokołu SOAP i konwertowanie go do stylu REST).

    ![Zrzut ekranu przedstawia okno dialogowe Tworzenie z w S D L, w którym można wprowadzić specyfikację W S D L.](./media/import-soap-api/pass-through.png)
5. Naciśnij klawisz Tab.

    Następujące pola są wypełniane przy użyciu informacji z interfejsu API protokołu SOAP: Nazwa wyświetlana, Nazwa, Opis.
6. Dodaj sufiks adresu URL interfejsu API. Sufiks to nazwa, która identyfikuje dany interfejs API w tym wystąpieniu usługi API Management. Musi być unikatowa w tym wystąpieniu API Management.
7. Opublikuj interfejs API przez skojarzenie go z produktem. W takim przypadku jest używany produkt typu „*Nieograniczony*”.  Jeśli chcesz, aby interfejs API został opublikowany i był dostępny dla deweloperów, dodaj go do produktu. Możesz to zrobić podczas tworzenia interfejsu API lub ustawić tę opcję później.

    Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie API Management, jesteś administratorem, więc domyślnie subskrybujesz każdy produkt.

    Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe:

    * **Starter (początkowy)**
    * **Nieograniczona liczba**   
8. Wprowadź inne ustawienia interfejsu API. Możesz ustawić wartości podczas tworzenia lub skonfigurować je później, przechodząc do karty **Ustawienia** . Ustawienia są wyjaśnione w samouczku [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md#import-and-publish-a-backend-api) .
9. Wybierz przycisk **Utwórz**.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Testowanie nowego interfejsu API w portalu administracyjnym

Operacje mogą być wywoływane bezpośrednio z portalu administracyjnego, który zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API.  

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Naciśnij kartę **Test**.
3. Wybierz operację.

    Na stronie zostaną wyświetlone pola parametrów zapytania i pola nagłówków. Jeden z nagłówków to „Ocp-Apim-Subscription-Key” dla klucza subskrypcji produktu, który został skojarzony z tym interfejsem API. Jeśli utworzono wystąpienie API Management, oznacza to, że już jesteś administratorem, więc klucz zostanie uzupełniony automatycznie. 
1. Kliknij pozycję **Wyślij**.

    Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)