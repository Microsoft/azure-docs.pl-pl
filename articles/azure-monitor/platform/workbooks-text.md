---
title: Parametry tekstu skoroszytów monitora Platformy Azure
description: Uprość składanie złożonych raportów dzięki wstępnie utworzonym i niestandardowych skoroszytom sparametryzowanym. Dowiedz się więcej o parametrach tekstu skoroszytu.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c804cc8942a40e2f30c980636194daa82e0fb0e8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687331"
---
# <a name="workbook-text-parameters"></a>Parametry tekstu skoroszytu

Parametry pola tekstowego zapewniają prosty sposób zbierania tekstu od użytkowników skoroszytu. Są one używane, gdy nie jest praktyczne użycie listy rozwijanej do zbierania danych wejściowych (na przykład dowolnego progu lub filtrów ogólnych). Skoroszyty umożliwiają autorom uzyskanie domyślnej wartości pola tekstowego z kwerendy. Dzięki temu ciekawe scenariusze, takie jak ustawienie domyślnego progu na podstawie p95 metryki.

Typowe użycie pól tekstowych jest jako zmienne wewnętrzne używane przez inne formanty skoroszytu. Odbywa się to za pomocą kwerendy dla wartości domyślnych i co formant danych wejściowych niewidoczne w trybie odczytu. Na przykład użytkownik może chcieć, aby próg pochodził z formuły (nie użytkownika), a następnie używał tego progu w kolejnych kwerendach.

## <a name="creating-a-text-parameter"></a>Tworzenie parametru tekstowego
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz _pozycję Dodaj parametry_ z łączy w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr._
4. W nowym okienku parametrów, które wyskakuje wpisać:
    1. Nazwa parametru:`SlowRequestThreshold`
    2. Typ parametru:`Text`
    3. Wymagane:`checked`
    4. Pobierz wartość domyślną z kwerendy:`unchecked`
5. Wybierz "Zapisz" na pasku narzędzi, aby utworzyć parametr.

    ![Obraz przedstawiający tworzenie parametru tekstowego](./media/workbooks-text/text-create.png)

Tak będzie wyglądał skoroszyt w trybie odczytu.

![Obraz przedstawiający parametr tekstowy w trybie odczytu](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Odwoływanie się do parametru tekstowego
1. Dodaj formant kwerendy do skoroszytu, zaznaczając niebieskie `Add query` łącze i wybierając zasób usługi Application Insights.
2. W polu KQL dodaj ten fragment kodu:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Za pomocą parametru tekstowego o wartości 500 w połączeniu z kontrolą kwerendy skutecznie uruchamiasz zapytanie poniżej:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Uruchom kwerendę, aby zobaczyć wyniki

    ![Obraz przedstawiający parametr tekstowy, do którego odwołuje się KQL](./media/workbooks-text/text-reference.png)

> [!NOTE]
> W powyższym `{SlowRequestThreshold}` przykładzie reprezentuje wartość całkowitą. Jeśli były kwerendy dla `{ComputerName}` ciągu, jak trzeba by zmodyfikować kwerendy Kusto, aby dodać cudzysłowy `"{ComputerName}"` w kolejności dla pola parametru do akceptowania danych wejściowych bez cudzysłowów.

## <a name="setting-default-values"></a>Ustawianie wartości domyślnych
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz _pozycję Dodaj parametry_ z łączy w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr._
4. W nowym okienku parametrów, które wyskakuje wpisać:
    1. Nazwa parametru:`SlowRequestThreshold`
    2. Typ parametru:`Text`
    3. Wymagane:`checked`
    4. Pobierz wartość domyślną z kwerendy:`checked`
5. W polu KQL dodaj ten fragment kodu:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Ta kwerenda ustawia domyślną wartość pola tekstowego na 95-ty percentyl czas trwania dla wszystkich żądań w aplikacji.
6. Uruchom kwerendę, aby zobaczyć wynik
7. Wybierz "Zapisz" na pasku narzędzi, aby utworzyć parametr.

    ![Obraz przedstawiający parametr tekstowy o wartości domyślnej z KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Podczas gdy w tym przykładzie kwerendy application insights danych, podejście może służyć do dowolnego źródła danych opartego na dzienniku — usługi Log Analytics, Azure Resource Graph, itp.

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](workbooks-visualizations.md) naukę o skoroszytach wiele rozbudowanych opcji wizualizacji.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
