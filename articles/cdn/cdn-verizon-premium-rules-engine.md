---
title: Przesłoń zachowanie protokołu HTTP przy użyciu aparatu reguł Azure CDN Verizon Premium
description: Aparat reguł umożliwia dostosowywanie sposobu obsługi żądań HTTP przez Azure CDN z Verizon Premium, takich jak blokowanie dostarczania określonych typów zawartości, definiowanie zasad buforowania i modyfikowanie nagłówków HTTP.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: a49912bc2275e478d657f06587c4ddc830210d3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040220"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Przesłoń zachowanie protokołu HTTP przy użyciu Azure CDN z aparatu reguł Verizon Premium

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie

Aparat reguł Azure CDN umożliwia dostosowywanie sposobu obsługi żądań HTTP. Na przykład blokowanie dostarczania określonych typów zawartości, definiowanie zasad buforowania lub modyfikowanie nagłówka HTTP. W tym samouczku pokazano, jak utworzyć regułę, która zmienia zachowanie buforowania zasobów sieci CDN. Aby uzyskać więcej informacji na temat składni aparatu reguł, zobacz temat [Informacje o aparacie Azure CDN reguł](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Access

Aby uzyskać dostęp do aparatu reguł, musisz najpierw wybrać pozycję **Zarządzaj** w górnej części strony **profilu usługi CDN** , aby uzyskać dostęp do strony zarządzania Azure CDN. W zależności od tego, czy punkt końcowy jest zoptymalizowany pod kątem przyspieszania witryn dynamicznych (DSA), uzyskujesz dostęp do aparatu reguł przy użyciu zestawu reguł odpowiednich dla danego typu punktu końcowego:

- Punkty końcowe zoptymalizowane pod kątem ogólnej dostarczania sieci Web lub optymalizacji innej niż DSA:
    
    Wybierz **dużą kartę http** , a następnie wybierz pozycję **aparat reguł**.

    ![Aparat reguł dla protokołu HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Punkty końcowe zoptymalizowane pod kątem DSA:
    
    Wybierz kartę **wdrożenie** , a następnie wybierz opcję **aparat reguł**.
    
    WDROŻENIE to termin używany przez Verizon do określania zawartości DSA. Wszystkie utworzone tutaj reguły są ignorowane przez wszystkie punkty końcowe w profilu, które nie są zoptymalizowane pod kątem DSA.

    ![Aparat reguł dla agenta DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Samouczek

1. Na stronie **profil usługi CDN** wybierz pozycję **Zarządzaj**.
   
    ![Przycisk zarządzania profilem CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania sieci CDN.

2. Wybierz **dużą kartę http** , a następnie wybierz pozycję **aparat reguł**.
   
    Zostaną wyświetlone opcje nowej reguły.
   
    ![Opcje nowej reguły sieci CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Kolejność, w której są wymienione wiele reguł, ma wpływ na sposób ich obsługi. Kolejna reguła może zastąpić akcje określone przez poprzednią regułę. Na przykład jeśli masz regułę, która zezwala na dostęp do zasobu na podstawie właściwości żądania i reguły, która odmówi dostępu do wszystkich żądań, druga reguła zastępuje pierwszy z nich. Reguły przesłonią wcześniejsze reguły tylko wtedy, gdy współpracują z tymi samymi właściwościami.
   >

3. Wprowadź nazwę w polu tekstowym **Nazwa/opis** .

4. Określ typ żądań, których dotyczy reguła. Użyj domyślnego warunku dopasowania, **zawsze**.
   
   ![Warunek dopasowania reguły sieci CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Na liście rozwijanej są dostępne wiele warunków dopasowywania. Aby uzyskać informacje dotyczące aktualnie wybranego warunku dopasowywania, wybierz niebieską ikonę informacyjną po lewej stronie.
   >
   >  Aby uzyskać szczegółową listę wyrażeń warunkowych, zobacz temat [wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > Aby uzyskać szczegółową listę warunków zgodności, zobacz [warunki dopasowania aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Aby dodać nową funkcję, wybierz **+** przycisk obok pozycji **funkcje**.  Na liście rozwijanej po lewej stronie wybierz pozycję **Wymuszaj wewnętrzny maksymalny wiek**.  W wyświetlonym polu tekstowym wprowadź **300**. Nie należy zmieniać pozostałych wartości domyślnych.
   
   ![Funkcja reguły sieci CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Na liście rozwijanej są dostępne wiele funkcji. Aby uzyskać informacje na temat aktualnie wybranej funkcji, wybierz niebieską ikonę informacyjną po lewej stronie.
   >
   > W przypadku **wymuszonego maksymalnego wieku, element** zawartości `Cache-Control` i `Expires` nagłówki są zastępowane w celu kontrolowania, kiedy węzeł brzegowy usługi CDN odświeża element zawartości ze źródła. W tym przykładzie węzeł brzegowy usługi CDN buforuje zasób przez 300 sekund lub 5 minut, zanim spowoduje odświeżenie elementu zawartości z jego źródła.
   >
   > Aby uzyskać szczegółową listę funkcji, zobacz [reguły aparatu funkcji](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Wybierz pozycję **Dodaj** , aby zapisać nową regułę.  Nowa reguła czeka teraz na zatwierdzenie. Po jego zatwierdzeniu stan zmieni się z **oczekującego XML** na **aktywny plik XML**.
   
   > [!IMPORTANT]
   > Azure CDN propagowanie zmian w regułach może potrwać do 10 minut.
   >
   >

## <a name="see-also"></a>Zobacz też

- [Przegląd Azure CDN](cdn-overview.md)
- [Dokumentacja aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Warunki dopasowań aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkcje aparatu reguł](cdn-verizon-premium-rules-engine-reference-features.md)
- [Piątek platformy Azure: zaawansowane nowe funkcje Premium](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (wideo) Azure CDN