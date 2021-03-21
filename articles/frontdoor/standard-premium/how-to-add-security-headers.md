---
title: Konfigurowanie nagłówków zabezpieczeń przy użyciu zestawu reguł Standard/Premium systemu Azure (wersja zapoznawcza)
description: Ten artykuł zawiera wskazówki dotyczące sposobu korzystania z zestawu reguł w celu skonfigurowania nagłówków zabezpieczeń.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099901"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Konfigurowanie nagłówków zabezpieczeń przy użyciu zestawu reguł Standard/Premium systemu Azure (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

W tym artykule pokazano, jak wdrożyć nagłówki zabezpieczeń, aby zapobiec występowaniu luk w zabezpieczeniach przeglądarki, takich jak HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy lub X-Frame-Options. Atrybuty oparte na zabezpieczeniach można także definiować za pomocą plików cookie.

Poniższy przykład pokazuje, jak dodać nagłówek Content-Security-Policy do wszystkich żądań przychodzących, które pasują do ścieżki w marszrucie. W tym miejscu zezwolimy tylko na skrypty z naszej zaufanej witryny **https://apiphany.portal.azure-api.net** do uruchamiania w naszej aplikacji.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby można było skonfigurować nagłówki zabezpieczeń, należy najpierw utworzyć drzwi z przodu. Aby uzyskać więcej informacji, zobacz temat [Quickstart: Create a Front Door for a highly available global web application](create-front-door-portal.md) (Szybki start: tworzenie usługi Front Door na potrzeby łatwo dostępnej globalnej aplikacji internetowej).
* Zapoznaj się z tematem [Konfigurowanie zestawu reguł](how-to-configure-rule-set.md) , jeśli funkcja zestawu reguł nie została wcześniej użyta.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Dodawanie nagłówka Content-Security-Policy w Azure Portal

1. Przejdź do profilu usługi Azure Front-Standard/Premium i wybierz pozycję **zestaw reguł** w obszarze **Ustawienia.**

1. Wybierz pozycję **Dodaj** , aby dodać nowy zestaw reguł. Nadaj regule **nazwę** , a następnie podaj **nazwę** dla reguły. Wybierz pozycję **Dodaj akcję** , a następnie wybierz pozycję **Nagłówek odpowiedzi**.

1. Ustaw operator do **dołączenia** , aby dodać ten nagłówek jako odpowiedź do wszystkich żądań przychodzących dla tej trasy.

1. Dodaj nazwę nagłówka: **Content-Security-Policy** i Zdefiniuj wartości, które ten nagłówek powinien zaakceptować. W tym scenariuszu wybieramy *" https://apiphany.portal.azure-api.net " skrypt-src "*.

1. Po dodaniu wszystkich reguł, które chcesz skonfigurować, nie zapomnij skojarzyć zestawu reguł z trasą. Ten krok jest *wymagany* , aby zezwolić na wykonanie akcji przez zestaw reguł. 

> [!NOTE]
> W tym scenariuszu nie dodano [warunków dopasowania](concept-rule-set-match-conditions.md) do reguły. Ta reguła będzie miała zastosowanie wszystkie żądania przychodzące zgodne ze ścieżką zdefiniowaną w skojarzonej trasie. Jeśli chcesz, aby dotyczyły tylko podzbioru tych żądań, pamiętaj, aby dodać do tej reguły określone **warunki dopasowania** .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="deleting-a-rule"></a>Usuwanie reguły

W poprzednich krokach skonfigurowano nagłówek Content-Security-Policy z zestawem reguł. Jeśli nie potrzebujesz już reguły, możesz wybrać nazwę zestawu reguł, a następnie wybrać pozycję Usuń regułę. 

### <a name="deleting-a-rule-set"></a>Usuwanie zestawu reguł

Jeśli chcesz usunąć zestaw reguł, upewnij się, że został on odkojarzenia ze wszystkimi trasami przed usunięciem. Aby uzyskać szczegółowe wskazówki dotyczące usuwania zestawu reguł, zapoznaj się z tematem [Konfigurowanie zestawu reguł](how-to-configure-rule-set.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować zaporę aplikacji sieci Web dla drzwi przednich, zobacz [Zapora aplikacji sieci Web i drzwi z przodu](../../web-application-firewall/afds/afds-overview.md).
