---
title: Jak ustawić lub edytować zasady usługi Azure API Management | Microsoft Docs
description: Dowiedz się, jak ustawiać lub edytować zasady usługi Azure API Management. Te zasady są dokumentami XML, które opisują sekwencję przychodzących i wychodzących instrukcji.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 3a8d3309cdc5ddb0ae75ed6fcf0376118275b835
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87848013"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Jak ustawić i edytować zasady usługi Azure API Management

Definicja zasad jest dokumentem XML, który opisuje sekwencję przychodzących i wychodzących instrukcji. KOD XML można edytować bezpośrednio w oknie definicji. Możesz również wybrać wstępnie zdefiniowane zasady z listy znajdującej się po prawej stronie okna zasady. Instrukcje mające zastosowanie do bieżącego zakresu są włączone i wyróżnione. Kliknięcie instrukcji Enabled powoduje dodanie odpowiedniego kodu XML w lokalizacji kursora w widoku definicji. 

Aby uzyskać szczegółowe informacje na temat zasad, zobacz [zasady w usłudze Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Ustawianie lub edytowanie zasad

Aby ustawić lub edytować zasady, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Przejdź do swojego wystąpienia usługi APIM.
3. Kliknij kartę **Interfejsy API**.

    ![Edycja zasad](./media/set-edit-policies/code-editor.png)

4. Wybierz jeden z wcześniej zaimportowanych interfejsów API.
5. Wybierz kartę **Projekt**.
6. Wybierz operację, do której chcesz zastosować zasady. Jeśli chcesz zastosować zasady do wszystkich operacji, wybierz pozycję **wszystkie operacje**.
7. Wybierz **</>** ikonę (Edytor kodu) w sekcji przetwarzanie **przychodzące** lub **Przetwarzanie wychodzące** .
8. Wklej żądany kod zasad do jednego z odpowiednich bloków.

    ```xml
    <policies>
        <inbound>
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```
 
## <a name="configure-scope"></a>Konfigurowanie zakresu

Zasady można konfigurować globalnie lub w zakresie produktu, interfejsu API lub operacji. Aby rozpocząć konfigurowanie zasad, należy najpierw wybrać zakres, w którym zasady powinny być stosowane.

Zakresy zasad są oceniane w następującej kolejności:

1. Zakres globalny
2. Zakres produktu
3. Zakres interfejsu API
4. Zakres operacji

Instrukcje w ramach zasad są oceniane zgodnie z położeniem `base` elementu, jeśli jest obecny. Zasady globalne nie mają zasad nadrzędnych i używanie `<base>` elementu nie ma żadnego efektu.

Aby wyświetlić zasady w bieżącym zakresie w edytorze zasad, kliknij pozycję **Oblicz ponownie obowiązujące zasady dla wybranego zakresu**.

### <a name="global-scope"></a>Zakres globalny

Globalny zakres jest skonfigurowany dla **wszystkich interfejsów API** w wystąpieniu usługi APIM.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do wystąpienia APIM.
2. Kliknij pozycję **wszystkie interfejsy API**.

    ![Zakres globalny](./media/api-management-howto-policies/global-scope.png)

3. Kliknij ikonę trójkąta.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij pozycję **Zapisz**. 

    Zmiany są natychmiast propagowane do bramy API Management.

### <a name="product-scope"></a>Zakres produktu

Zakres produktu został skonfigurowany dla wybranego produktu.

1. Kliknij pozycję **produkty**.

    ![Zakres produktu](./media/api-management-howto-policies/product-scope.png)

2. Wybierz produkt, do którego chcesz zastosować zasady.
3. Kliknij pozycję **zasady**.
4. Dodawanie lub edytowanie zasad.
5. Naciśnij pozycję **Zapisz**. 

### <a name="api-scope"></a>Zakres interfejsu API

Zakres interfejsu API jest skonfigurowany dla **wszystkich operacji** wybranego interfejsu API.

1. Wybierz **interfejs API** , do którego chcesz zastosować zasady.

    ![Zakres interfejsu API](./media/api-management-howto-policies/api-scope.png)

2. Wybierz **wszystkie operacje**
3. Kliknij ikonę trójkąta.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij pozycję **Zapisz**. 

### <a name="operation-scope"></a>Zakres operacji 

Dla wybranej operacji skonfigurowano zakres operacji.

1. Wybierz **interfejs API**.
2. Wybierz operację, do której chcesz zastosować zasady.

    ![Zakres operacji](./media/api-management-howto-policies/operation-scope.png)

3. Kliknij ikonę trójkąta.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij pozycję **Zapisz**. 

## <a name="next-steps"></a>Następne kroki

Zobacz następujące tematy pokrewne:

+ [Przekształć interfejsy API](transform-api.md)
+ [Dokumentacja zasad](./api-management-policies.md) pełna lista instrukcji zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
