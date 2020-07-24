---
title: Testowanie wydajności i obciążenia przy użyciu usługi Azure Application Insights | Microsoft Docs
description: Konfigurowanie testów wydajności i obciążenia przy użyciu usługi Azure Application Insights
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 8885a2e511c11317f593706255437e52819adcae
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024354"
---
# <a name="performance-testing"></a>Testowanie wydajności

> [!NOTE]
> Usługa testowania obciążenia opartego na chmurze została wycofana. Więcej informacji na temat wycofania, dostępności usługi i usług alternatywnych można znaleźć [tutaj](/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights umożliwia generowanie testów obciążenia dla witryn sieci Web. Podobnie jak w przypadku [testów dostępności](monitor-web-app-availability.md), możesz wysyłać podstawowe żądania lub [żądania wieloetapowe](availability-multistep.md) z agentów testów platformy Azure na całym świecie. Testy wydajności umożliwiają symulowanie nawet 20 000 jednoczesnych użytkowników przez maksymalnie 60 minut.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Aby można było utworzyć test wydajności, należy najpierw utworzyć zasób Application Insights. Jeśli zasób został już utworzony, przejdź do następnej sekcji.

W Azure Portal wybierz pozycję **Utwórz zasób**  >  **Narzędzia deweloperskie**  >  **Application Insights** i utwórz zasób Application Insights.

## <a name="configure-performance-testing"></a>Konfigurowanie testowania wydajności

Jeśli tworzysz test wydajności po raz pierwszy, wybierz pozycję **Ustaw organizację** i wybierz organizację Azure DevOps, która będzie źródłem testów wydajności.

W obszarze **Konfiguracja**przejdź do opcji **testowanie wydajności** , a następnie kliknij pozycję **Nowy** , aby utworzyć test.

![Podaj przynajmniej adres URL swojej witryny sieci Web](./media/performance-testing/new-performance-test.png)

Aby utworzyć podstawowy test wydajności, wybierz typ testowy **testu ręcznego** i Wypełnij odpowiednie ustawienia dla testu.

|Ustawienie| Wartość maksymalna
|----------|------------|
| Obciążenie użytkownikami | 20 000 |
| Czas trwania (w minutach)  | 60 |  

Po utworzeniu testu kliknij pozycję **Uruchom test**.

Po zakończeniu testu zobaczysz wyniki podobne do poniższych:

![Wyniki tekstu](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Konfiguruj test sieci Web programu Visual Studio

Zaawansowane możliwości testowania wydajności Application Insights są tworzone w oparciu o projekty testów wydajności i obciążenia programu Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Następne kroki

* [Wieloetapowe testy sieci Web](availability-multistep.md)
* [Testy ping adresu URL](monitor-web-app-availability.md)
