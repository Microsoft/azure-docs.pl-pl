---
title: Mapuj pola danych na jednostki wskaźnikowe platformy Azure | Microsoft Docs
description: Mapuj pola danych w tabelach na jednostki wskaźnikowe platformy Azure w regułach analizy, aby uzyskać lepsze informacje o zdarzeniach
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: cb91d269f6b166510db54637d17d776e71137408
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456293"
---
# <a name="map-data-fields-to-entities-in-azure-sentinel"></a>Mapowanie pól danych na jednostki na platformie Azure — wskaźnik 

> [!IMPORTANT]
>
> - Nowa wersja funkcji mapowania jednostek jest w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

> [!IMPORTANT]
>
> - Zapoznaj się z [uwagami dotyczącymi nowej wersji](#notes-on-the-new-version) na końcu tego dokumentu, aby uzyskać ważne informacje dotyczące zgodności z poprzednimi wersjami i różnic między nowymi i starymi wersjami mapowania jednostek.

## <a name="introduction"></a>Wprowadzenie

Mapowanie jednostki jest integralną częścią konfiguracji [reguł zaplanowanych analiz zapytań](tutorial-detect-threats-custom.md). Wzbogacają one reguły "dane wyjściowe (alerty i incydenty) o podstawowe informacje służące jako bloki konstrukcyjne wszelkich procesów dochodzeniowych i czynności zaradczych, które są następujące.

Opisana poniżej procedura jest częścią Kreatora tworzenia reguł analizy. Jest on traktowany w tym miejscu niezależnie w celu rozwiązania scenariusza dodawania lub zmieniania mapowań jednostek w istniejącej regule analizy.

## <a name="how-to-map-entities"></a>Jak mapować jednostki

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Analiza**.

1. Wybierz zaplanowaną regułę zapytania, a następnie kliknij przycisk **Edytuj**. Lub Utwórz nową regułę, klikając pozycję **utwórz &#10132; zaplanowaną regułę zapytania** w górnej części ekranu.

1. Kliknij kartę **Ustawianie logiki reguły** .

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="Mapuj pola do jednostek":::

1. W sekcji **ulepszenie alertu** w obszarze **Mapowanie jednostki** wybierz typ jednostki z listy rozwijanej **Typ jednostki** .

1. Wybierz **Identyfikator** dla jednostki. Identyfikatory są atrybutami jednostki, które mogą odpowiednio identyfikować ją. Wybierz jeden z listy rozwijanej **Identyfikator** , a następnie wybierz pole danych z listy rozwijanej **wartość** , która będzie odpowiadać identyfikatorowi. Z pewnymi wyjątkami lista **wartości** jest wypełniana przez pola danych w tabeli zdefiniowanej jako podmiot kwerendy reguły.

    Można zdefiniować **maksymalnie trzy identyfikatory** dla danej jednostki. Niektóre identyfikatory są wymagane, inne są opcjonalne. Musisz wybrać co najmniej jeden wymagany identyfikator. Jeśli tego nie zrobisz, zostanie wyświetlony komunikat ostrzegawczy z instrukcjami, które identyfikatory są wymagane. Aby uzyskać najlepsze wyniki — w celu uzyskania maksymalnej unikatowej identyfikacji — należy używać **silnych identyfikatorów** wszędzie tam, gdzie jest to możliwe, a korzystanie z wielu silnych identyfikatorów umożliwi większej korelacji między źródłami danych. Zapoznaj się z pełną listą dostępnych [obiektów i identyfikatorów](entities-reference.md).

1. Kliknij pozycję **Dodaj nową jednostkę** , aby zamapować więcej jednostek. Można mapować **maksymalnie pięć jednostek** w jednej regule analizy. Możesz również zmapować więcej niż jeden z tego samego typu. Na przykład można mapować dwie jednostki **IP** , jeden ze *źródłowego pola adresu IP* i jeden z *docelowego adresu IP* . W ten sposób można śledzić oba te elementy.

    Jeśli zmienisz zdanie lub wystąpi błąd, możesz usunąć mapowanie jednostki, klikając ikonę kosza obok listy rozwijanej jednostka.

1. Po zakończeniu mapowania jednostek kliknij kartę **Przegląd i tworzenie** . Po pomyślnym sprawdzeniu reguły kliknij przycisk **Zapisz**.

## <a name="notes-on-the-new-version"></a>Uwagi dotyczące nowej wersji

- Jeśli wcześniej zdefiniowano mapowania jednostek dla tej reguły analizy przy użyciu starej wersji, te mapowania pojawiają się w kodzie zapytania. Mapowania jednostek zdefiniowane w nowej wersji **nie są wyświetlane w kodzie zapytania**. Reguły analizy mogą obsługiwać tylko jedną wersję mapowań jednostek jednocześnie, a nowa wersja ma pierwszeństwo. W związku z tym każde Mapowanie zdefiniowane w tym miejscu spowoduje odrzucenie **wszystkich** mapowań zdefiniowanych w kodzie zapytania **, gdy kwerenda zostanie uruchomiona** . 

- Jeśli nadal potrzebujesz użyć **starej wersji** mapowania jednostki (o ile nowa wersja jest nadal w wersji zapoznawczej), nadal możesz uzyskać do niej dostęp przy użyciu flagi funkcji w adresie URL. Umieść kursor między elementami `https://portal.azure.com/` i i `#blade` Wstaw tekst `?feature.EntityMapping=false` .

  - Nadal będą stosowane limity starej wersji. Można mapować tylko jednostki użytkownika, hosta, adres IP, adres URL i skrót pliku, a tylko jeden z nich.

  - **Przed** przywróceniem starej wersji należy **usunąć** wszystkie mapowania jednostek utworzone przy użyciu nowej wersji. w przeciwnym razie żadne mapowania jednostek, które używają starej wersji, **nie będą działały**.

- Gdy nowa wersja mapowania jednostki jest ogólnie dostępna, nie będzie już można używać starej wersji. Zdecydowanie zaleca się przeprowadzenie migracji starych mapowań jednostek do nowej wersji.


## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób mapowania pól danych do jednostek w regułach analizy wskaźnikowej platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Pobierz kompletny obraz dla [zaplanowanych reguł analizy zapytań](tutorial-detect-threats-custom.md).
- Dowiedz się więcej o [jednostkach na platformie Azure](entities-in-azure-sentinel.md).
