---
title: Azure Front Door
description: W tym artykule przedstawiono sposób konfigurowania nagłówka zabezpieczeń za pośrednictwem aparatu reguł na platformie Azure front-drzwi
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 6/22/2020
ms.author: mebeatty
ms.openlocfilehash: 4d698375488d4dac551f0028883fc4e18a10d8ef
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323528"
---
# <a name="add-security-headers-with-rules-engine"></a>Dodawanie nagłówków zabezpieczeń z aparatem reguł

Zaimplementuj nagłówki zabezpieczeń, aby zapobiec występowaniu luk w zabezpieczeniach opartych na przeglądarce, takich jak HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy lub X-Frame-Options. Atrybuty oparte na zabezpieczeniach można także definiować za pomocą plików cookie.

Poniższy przykład pokazuje, jak dodać nagłówek Content-Security-Policy do wszystkich żądań przychodzących, które pasują do ścieżki zdefiniowanej w marszrucie konfiguracji aparatu reguł. W tym miejscu zezwolimy tylko na skrypty z naszej zaufanej witryny **https://apis.contoso.com** do uruchamiania w naszej aplikacji.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Dodawanie nagłówka Content-Security-Policy w Azure Portal

1. Przed utworzeniem tej konkretnej reguły zapoznaj się z tematem jak [utworzyć drzwi tylne](quickstart-create-front-door.md) lub jak [utworzyć aparat reguł](front-door-tutorial-rules-engine.md) , jeśli jest to pierwsze użycie AFD lub funkcji aparatu reguł.

2. Kliknij pozycję **Dodaj**, aby dodać nową regułę. Podaj nazwę reguły, a następnie kliknij pozycję **Dodaj**  >  **Nagłówek odpowiedzi**akcji.

3. Ustaw operator do **dołączenia** , aby dodać ten nagłówek jako odpowiedź do wszystkich żądań przychodzących do tej trasy.

4. Dodaj nazwę nagłówka: **Content-Security-Policy** i Zdefiniuj wartości, które ten nagłówek powinien zaakceptować. W tym scenariuszu wybieramy *"sam" skrypt-src " https://apis.contoso.com .*

5. Po dodaniu wszystkich reguł, które chcesz skonfigurować, nie zapomnij przejść do preferowanej trasy i skojarz konfigurację aparatu reguł z regułą trasy. Ten krok jest wymagany, aby umożliwić działanie reguły. 

![przykład portalu](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> W tym scenariuszu nie dodano [warunków dopasowania](front-door-rules-engine-match-conditions.md) do reguły. Ta reguła będzie miała zastosowanie wszystkie żądania przychodzące zgodne ze ścieżką zdefiniowaną w regule trasy. Jeśli chcesz, aby dotyczyły tylko podzbioru tych żądań, pamiętaj, aby dodać do tej reguły określone warunki dopasowania.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [aparacie reguł AFD](front-door-rules-engine.md). 
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
- Dowiedz się więcej na temat [warunków dopasowania](front-door-rules-engine-match-conditions.md) aparatu
- Więcej informacji znajduje się w [dokumentacji interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)aparatu AFD. 
- Zapoznaj się z informacjami w temacie AFD Rules Engine [PowerShell](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0). 
