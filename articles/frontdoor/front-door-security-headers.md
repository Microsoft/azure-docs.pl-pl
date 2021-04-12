---
title: 'Samouczek: Dodawanie nagłówków zabezpieczeń z aparatem reguł — drzwi platformy Azure'
description: W tym samouczku przedstawiono sposób konfigurowania nagłówka zabezpieczeń za pośrednictwem aparatu reguł na platformie Azure front-drzwi
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: e8462b98aee1d34b6c7a2ed671667223eca45a25
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551921"
---
# <a name="tutorial-add-security-headers-with-rules-engine"></a>Samouczek: Dodawanie nagłówków zabezpieczeń z aparatem reguł

W tym samouczku pokazano, jak wdrożyć nagłówki zabezpieczeń, aby zapobiec występowaniu luk w zabezpieczeniach przeglądarki, takich jak HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy lub X-Frame-Options. Atrybuty oparte na zabezpieczeniach można także definiować za pomocą plików cookie.

Poniższy przykład pokazuje, jak dodać nagłówek Content-Security-Policy do wszystkich żądań przychodzących, które pasują do ścieżki zdefiniowanej w marszrucie konfiguracji aparatu reguł. W tym miejscu zezwolimy tylko na skrypty z naszej zaufanej witryny **https://apiphany.portal.azure-api.net** do uruchamiania w naszej aplikacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Skonfiguruj zasady Content-Security-Policy w aparacie reguł.

## <a name="prerequisites"></a>Wymagania wstępne

* Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć usługę Front Door. Aby uzyskać więcej informacji, zobacz temat [Quickstart: Create a Front Door for a highly available global web application](quickstart-create-front-door.md) (Szybki start: tworzenie usługi Front Door na potrzeby łatwo dostępnej globalnej aplikacji internetowej).
* Jeśli korzystasz z funkcji aparat reguł, zobacz jak [skonfigurować aparat reguł](front-door-tutorial-rules-engine.md).

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Dodawanie nagłówka Content-Security-Policy w Azure Portal

1. Kliknij pozycję **Dodaj**, aby dodać nową regułę. Podaj nazwę reguły, a następnie kliknij pozycję **Dodaj**  >  **Nagłówek odpowiedzi** akcji.

1. Ustaw operator do **dołączenia** , aby dodać ten nagłówek jako odpowiedź do wszystkich żądań przychodzących do tej trasy.

1. Dodaj nazwę nagłówka: **Content-Security-Policy** i Zdefiniuj wartości, które ten nagłówek powinien zaakceptować. W tym scenariuszu wybieramy *"sam" skrypt-src " https://apiphany.portal.azure-api.net .*

   > [!NOTE]
   > Wartości nagłówka są ograniczone do 128 znaków.

1. Po dodaniu wszystkich reguł, które chcesz skonfigurować, nie zapomnij przejść do preferowanej trasy i skojarz konfigurację aparatu reguł z regułą trasy. Ten krok jest wymagany, aby umożliwić działanie reguły. 

![przykład portalu](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> W tym scenariuszu nie dodano [warunków dopasowania](front-door-rules-engine-match-conditions.md) do reguły. Ta reguła będzie miała zastosowanie wszystkie żądania przychodzące zgodne ze ścieżką zdefiniowaną w regule trasy. Jeśli chcesz, aby dotyczyły tylko podzbioru tych żądań, pamiętaj, aby dodać do tej reguły określone **warunki dopasowania** .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach zostały skonfigurowane nagłówki zabezpieczeń z aparatem reguł. Jeśli chcesz, aby reguła nie była już potrzebna, możesz ją usunąć, klikając pozycję Usuń regułę.

:::image type="content" source="./media/front-door-rules-engine/rules-engine-delete-rule.png" alt-text="Usuwanie reguły":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować zaporę aplikacji sieci Web dla drzwi z przodu, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Zapora aplikacji internetowej i usługa Front Door](front-door-waf.md)
