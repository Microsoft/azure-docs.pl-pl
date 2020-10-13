---
title: 'ML Studio (klasyczny): korzystanie z usługi sieci Web w programie Excel — Azure'
description: Azure Machine Learning Studio (klasyczny) ułatwia wywoływanie usług sieci Web bezpośrednio z programu Excel bez konieczności pisania kodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: bceaa8fd2f47476d6b6ea6dafb88576c8bcc9712
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91367890"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Zużywanie Azure Machine Learning Studio (klasycznej) usługi sieci Web z programu Excel

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  

Azure Machine Learning Studio (klasyczny) ułatwia wywoływanie usług sieci Web bezpośrednio z programu Excel bez konieczności pisania kodu.

Jeśli używasz programu Excel 2013 (lub nowszego) lub usługi Excel Online, zalecamy użycie dodatku programu Excel [Excel](excel-add-in-for-web-services.md).



## <a name="steps"></a>Kroki
Opublikuj usługę sieci Web. [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md) wyjaśnia, jak to zrobić. Obecnie funkcja skoroszytu programu Excel jest obsługiwana tylko w przypadku usług żądania/odpowiedzi z pojedynczym wyjściem (czyli pojedynczą etykietą oceny). 

Gdy masz usługę sieci Web, kliknij sekcję **usługi sieci Web** po lewej stronie programu Studio, a następnie wybierz usługę sieci Web, która ma być wykorzystana z programu Excel.

**Klasyczna usługa sieci Web**

1. Na karcie **pulpit nawigacyjny** usługi sieci Web jest wierszem usługi **żądania/odpowiedzi** . Jeśli ta usługa ma pojedyncze dane wyjściowe, w tym wierszu powinien zostać wyświetlony link **Pobierz skoroszyt programu Excel** .

    ![Pobieranie skoroszytu programu Excel przy użyciu portalu usługi sieci Web programu Studio (klasycznego)](./media/consuming-from-excel/excellink.png)
2. Kliknij pozycję **Pobierz skoroszyt programu Excel**.

**Nowa usługa sieci Web**

1. W portalu usługi sieci Web **Azure Machine Learning wybierz pozycję**Użyj.
2. Na stronie korzystanie w sekcji **Opcje zużycia usług sieci Web** kliknij ikonę programu Excel.

**Korzystanie z skoroszytu**

1. Otwórz skoroszyt.
2. Zostanie wyświetlone ostrzeżenie o zabezpieczeniach. Kliknij przycisk **Włącz edycję** .

    ![Włącz edytowanie, aby usunąć ostrzeżenie o zabezpieczeniach widoku chronionego](./media/consuming-from-excel/enableeditting.png)
3. Zostanie wyświetlone ostrzeżenie dotyczące zabezpieczeń. Kliknij przycisk **Włącz zawartość** , aby uruchomić makra w arkuszu kalkulacyjnym.

    ![Włącz zawartość, aby odrzucić makra ostrzeżenia o wyłączaniu zabezpieczeń](./media/consuming-from-excel/enablecontent.png)
4. Po włączeniu makr jest generowana tabela. Kolumny w kolorze niebieskim są wymagane jako dane wejściowe do usługi sieci Web RR lub **parametrów**. Zwróć uwagę na dane wyjściowe usługi RR, **wartości przewidywane** w kolorze zielonym. Gdy wypełniono wszystkie kolumny dla danego wiersza, skoroszyt automatycznie wywoła interfejs API oceniania i wyświetla wyniki wynikowe.

    ![Tabela dla danych wejściowych parametrów i wyniki przewidywane](./media/consuming-from-excel/sampletable.png)
5. Aby wyrównać więcej niż jeden wiersz, Wypełnij drugi wiersz danymi, a przewidywane wartości są generowane. Można nawet wkleić kilka wierszy jednocześnie.

Możesz użyć dowolnej z funkcji programu Excel (wykresy, Mapa napięcia, formatowanie warunkowe itp.) z wartościami przewidywanymi, aby ułatwić wizualizowanie danych.

## <a name="sharing-your-workbook"></a>Udostępnianie skoroszytu
Aby makra działały, klucz interfejsu API musi być częścią arkusza kalkulacyjnego. Oznacza to, że należy udostępnić skoroszyt tylko z zaufanymi jednostkami i osobami.

## <a name="automatic-updates"></a>Automatyczne aktualizacje
W następujących dwóch sytuacjach jest wykonywane wywołanie RR:

1. Pierwszy raz wiersz zawiera zawartość we wszystkich jego **parametrach**
2. Wszystkie parametry każdego z **parametrów** zmieniają się w wierszu, który miał wszystkie wprowadzone **Parametry** .
