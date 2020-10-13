---
title: Zarządzanie wykresem przepływu danych mapowania
description: Efektywne zarządzanie i edytowanie grafu przepływu danych mapowania
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: 0cdad47123d69ca7cee468c5bb0cea3268d73bfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89420115"
---
# <a name="managing-the-mapping-data-flow-graph"></a>Zarządzanie wykresem przepływu danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mapowanie przepływów danych jest tworzone przy użyciu powierzchni projektowej znanej jako wykres przepływu danych. Na wykresie logika transformacji jest tworzona od lewej do prawej, a dodatkowe strumienie danych są dodawane z góry. Aby dodać nową transformację, wybierz znak plus w prawym dolnym rogu istniejącej transformacji.

![Kanwa](media/data-flow/canvas2.png "Kanwa")

Ponieważ przepływy danych są bardziej skomplikowane, użyj następujących mechanizmów, aby efektywnie nawigować do wykresu przepływu danych i zarządzać nim. 

## <a name="moving-transformations"></a>Przeniesienie transformacji

W mapowaniu przepływów danych zestaw połączonej logiki transformacji jest znany jako **strumień**. Pole **strumień przychodzący** określa, który strumień danych ponosi bieżące przekształcenie. Każda transformacja ma jeden lub dwa strumienie przychodzące w zależności od funkcji i reprezentuje strumień wyjściowy. Schemat danych wyjściowych strumieni przychodzących określa, które metadane kolumny mogą być przywoływane przez bieżące przekształcenie.

![Przenieś węzeł](media/data-flow/move-nodes.png "Przenieś węzeł")

W przeciwieństwie do kanwy potoku przekształcenia przepływu danych nie są edytowane przy użyciu modelu przeciągania i upuszczania. Aby zmienić strumień przychodzący lub "Przenieś" transformację, wybierz inną wartość z listy rozwijanej **strumienia przychodzącego** . Po wykonaniu tej czynności wszystkie przekształcenia podrzędne zostaną przeniesione obok edytowanej transformacji. Wykres zostanie automatycznie zaktualizowany, aby pokazać Nowy przepływ logiczny. W przypadku zmiany strumienia przychodzącego na transformację, która ma już transformację przechodzącą, zostanie utworzony nowy gałąź lub równoległy strumień danych. Dowiedz się więcej [na temat nowych gałęzi w mapowaniu przepływu danych](data-flow-new-branch.md).

## <a name="hide-graph-and-show-graph"></a>Ukryj wykres i Pokaż wykres

Podczas edytowania przekształcenia można rozwinąć panel konfiguracji, aby przejąć całą kanwę, ukrywając wykres. Kliknij cudzysłów ostrokątny w górę znajdujący się po prawej stronie kanwy.

![Ukryj wykres](media/data-flow/hide-graph.png "Ukryj wykres")

Gdy wykres jest ukryty, można przechodzić między transformacjemi w strumieniu, klikając przycisk **dalej** lub **poprzedni**. Kliknij cudzysłów ostrokątny w dół, aby wyświetlić wykres.

![Pokaż wykres](media/data-flow/show-graph.png "Pokaż wykres")

## <a name="searching-for-transformations"></a>Wyszukiwanie transformacji

Aby szybko znaleźć transformację w grafie, kliknij ikonę **wyszukiwania** powyżej ustawienia powiększenia.

![Wyszukiwanie](media/data-flow/search-1.png "Wyszukaj wykres")

Aby zlokalizować transformację, można wyszukać według nazwy lub opisu transformacji.

![Wyszukiwanie](media/data-flow/search-2.png "Wyszukaj wykres")

## <a name="hide-reference-nodes"></a>Ukryj węzły odniesienia

Jeśli przepływ danych ma jakiekolwiek sprzężenia, odnośnik, istnieje lub przekształcenia Union, przepływ danych pokazuje węzły odwołań do wszystkich strumieni przychodzących. Jeśli chcesz zminimalizować ilość zajętego miejsca w pionie, możesz zminimalizować węzły odwołań. Aby to zrobić, kliknij prawym przyciskiem myszy kanwę i wybierz pozycję **Ukryj węzły odniesienia**.

![Ukryj węzły odniesienia](media/data-flow/hide-reference-nodes.png "Ukryj węzły odniesienia")

## <a name="next-steps"></a>Następne kroki

Po ukończeniu logiki przepływu danych Włącz [tryb debugowania](concepts-data-flow-debug-mode.md) i przetestuj go w podglądzie danych.
