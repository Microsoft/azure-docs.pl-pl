---
title: Azure Monitor widoku projektanta na skoroszyty — podsumowanie konwersji i dostęp
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7614dd1d60bad5c124269ae2af02d30a5aacfe3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564036"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Wyświetl podsumowanie konwersji i dostęp do programu Designer
[Projektant widoków](view-designer.md) jest funkcją Azure monitor, która umożliwia tworzenie niestandardowych widoków, które ułatwiają wizualizację danych w obszarze roboczym log Analytics, z wykresami, listami i osiami czasu. Są one wycofywane i zastępowane skoroszytami, które zapewniają dodatkową funkcjonalność. W tym artykule szczegółowo opisano sposób tworzenia podsumowania przeglądu i uprawnień wymaganych do uzyskiwania dostępu do skoroszytów.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Tworzenie podsumowania obszaru roboczego z poziomu pulpitu nawigacyjnego platformy Azure
Użytkownicy projektanta widoków mogą znać, że masz kafelek przegląd do reprezentowania zestawu widoków. Aby zachować przegląd wizualizacji, taki jak podsumowanie obszaru roboczego projektanta widoków, skoroszyty oferują przypięte kroki, które można przypinać do [pulpitu nawigacyjnego Azure Portal](../../azure-portal/azure-portal-dashboards.md). Podobnie jak w przypadku kafelków przegląd w obszarze roboczym, przypięte elementy skoroszytu zostaną połączone bezpośrednio z widokiem skoroszytu.

Można korzystać z funkcji dostosowywania dostępnych w pulpitach nawigacyjnych platformy Azure, które umożliwiają automatyczne odświeżanie, przesuwanie, Określanie rozmiarów i dodatkowe filtrowanie dla przypiętych elementów i wizualizacji. 

![Zrzut ekranu przedstawia niestandardowe podsumowanie obszaru pulpit nawigacyjny platformy Azure.](media/view-designer-conversion-access/dashboard.png)

Utwórz nowy pulpit nawigacyjny platformy Azure lub wybierz istniejący pulpit nawigacyjny, aby rozpocząć Przypinanie elementów z skoroszytów.

Aby przypiąć poszczególne elementy, musisz włączyć ikonę pinezki dla określonego kroku. Aby to zrobić, wybierz odpowiedni przycisk **edycji** dla danego kroku, a następnie wybierz ikonę koła zębatego, aby otworzyć okno **Ustawienia zaawansowane**. Zaznacz opcję **Zawsze wyświetlaj ikonę pinezki w tym kroku**, a ikona pinezki zostanie wyświetlona w prawym górnym rogu kroku. Ten kod PIN umożliwia Przypinanie określonych wizualizacji do pulpitu nawigacyjnego, takich jak kafelki z omówieniem.

![Przypnij krok](media/view-designer-conversion-access/pin-step.png)


Możesz też chcieć przypiąć wiele wizualizacji ze skoroszytu lub całej zawartości skoroszytu do pulpitu nawigacyjnego. Aby przypiąć cały skoroszyt, wybierz pozycję **Edytuj** na górnym pasku narzędzi, aby przełączyć **tryb edycji**. Zostanie wyświetlona ikona pinezki, która umożliwia Przypinanie całego elementu skoroszytu lub wszystkich poszczególnych kroków i wizualizacji w skoroszycie.

![Przypnij wszystko](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Udostępnianie i przeglądanie uprawnień 
W przypadku skoroszytów dostępne są dokumenty prywatne lub udostępnione. Domyślnie zapisane skoroszyty zostaną zapisane w obszarze **Moje raporty**, co oznacza, że tylko twórca może wyświetlić ten skoroszyt.

Możesz udostępnić skoroszyty, wybierając ikonę **udostępniania** na górnym pasku narzędzi w **trybie edycji**. Zostanie wyświetlony monit o przeniesienie skoroszytu do **raportów udostępnionych**, co spowoduje wygenerowanie linku zapewniającego bezpośredni dostęp do skoroszytu.

Aby użytkownik mógł wyświetlić udostępniony skoroszyt, musi mieć dostęp do subskrypcji i grupy zasobów, w których zapisano skoroszyt.

![Dostęp oparty na subskrypcji](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Następne kroki

- [Typowe zadania](view-designer-conversion-tasks.md)