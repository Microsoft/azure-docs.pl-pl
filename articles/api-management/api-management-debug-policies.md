---
title: Debugowanie zasad usługi Azure API Management w programie Visual Studio Code | Microsoft Docs
description: Informacje na temat debugowania zasad usługi Azure API Management przy użyciu rozszerzenia usługi Azure API Management Visual Studio Code
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: 4eb32243df219d721d7baae80984c45d0fc4cf25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91345126"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Debugowanie zasad usługi Azure API Management w programie Visual Studio Code

[Zasady](api-management-policies.md) na platformie Azure API Management zapewniają zaawansowane funkcje, które pomagają wydawcom interfejsu API rozwiązywać problemy z zmniejszeniem, takie jak uwierzytelnianie, autoryzacja, ograniczanie, buforowanie i przekształcanie. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. 

W tym artykule opisano sposób debugowania zasad API Management przy użyciu [rozszerzenia Azure API Management do Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz API Management wystąpienia warstwy dewelopera, wykonując najpierw ten [samouczek](get-started-create-service-instance.md) .

Zainstaluj [Visual Studio Code](https://code.visualstudio.com/) i najnowszą wersję [rozszerzenia API Management platformy Azure dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

## <a name="restrictions-and-limitations"></a>Ograniczenia i ograniczenia

Ta funkcja jest dostępna tylko w warstwie deweloperów API Management. Każde wystąpienie API Management obsługuje tylko jedną współbieżną sesję debugowania.

## <a name="initiate-a-debugging-session"></a>Inicjowanie sesji debugowania

1. Uruchom program Visual Studio Code.
2. Przejdź do rozszerzenia API Management w obszarze rozszerzenia platformy Azure
3. Znajdź wystąpienie API Management do debugowania
4. Znajdź interfejs API i operację do debugowania
5. Kliknij prawym przyciskiem myszy operację i wybierz pozycję **Rozpocznij debugowanie zasad**

W tym momencie rozszerzenie podejmie próbę zainicjowania i ustanowienia sesji debugowania z bramą API Management.

![Inicjowanie debugowania](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>Wyślij żądanie testowe
Po ustanowieniu sesji debugowania rozszerzenie otworzy nowy edytor, który pozwala nam utworzyć i wysłać testowe żądanie HTTP do tej operacji, wykorzystując [rozszerzenie klienta REST](https://marketplace.visualstudio.com/items?itemName=humao.rest-client).

Zobaczysz, że nagłówek **OCP-APIM-Debug** został już dodany do żądania. Ten nagłówek jest wymagany, a wartość musi być ustawiona na klucz subskrypcji All-Access poziomu usługi, aby wyzwolić funkcje debugowania w bramie API Management.

Zmodyfikuj żądanie HTTP w edytorze zgodnie z scenariuszem testowym. Następnie kliknij pozycję **Wyślij żądanie** , aby wysłać żądanie testowe do bramy API Management.

![Wyślij żądanie testowe](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>Debugowanie zasad
Po wysłaniu testowego żądania HTTP rozszerzenie otworzy okno debugowanie z informacjami o obowiązujących zasadach tej operacji i zostanie zatrzymane przy pierwszej skutecznej zasadzie. 

![zasady debugowania](media/api-management-debug-policies/main-window.png)

Aby postępować zgodnie z potoku zasad, możesz wykonać jedną czynność przez poszczególne zasady lub ustawić punkt przerwania w zasadach i przejść bezpośrednio do tych zasad. 

W panelu **zmienne** można sprawdzić wartości zmiennych utworzonych przez system i utworzonych przez użytkownika. W panelu **punkty przerwania** można zobaczyć listę wszystkich ustawionych punktów przerwania. W panelu **stosu wywołań** można zobaczyć bieżący obowiązujący zakres zasad. 

Jeśli wystąpi błąd podczas wykonywania zasad, zobaczysz szczegóły błędu w zasadach, w których wystąpił. 

![wyłączenia](media/api-management-debug-policies/exception.png)

> [!TIP]
> Pamiętaj, aby wyjść z sesji debugowania przez kliknięcie przycisku **Zatrzymaj** po zakończeniu.


## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej na temat [rozszerzenia API Management Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 
+ Zgłoś problemy w [repozytorium GitHub](https://github.com/Microsoft/vscode-apimanagement)

