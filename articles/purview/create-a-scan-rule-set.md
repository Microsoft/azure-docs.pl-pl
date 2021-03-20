---
title: Tworzenie zestawu reguł skanowania
description: Utwórz zestaw reguł skanowania w usłudze Azure kontrolą, aby szybko skanować źródła danych w organizacji.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96554745"
---
# <a name="create-a-scan-rule-set"></a>Tworzenie zestawu reguł skanowania

W katalogu usługi Azure kontrolą można utworzyć zestawy reguł skanowania, aby umożliwić szybkie skanowanie źródeł danych w organizacji.

Zestaw reguł skanowania to kontener służący do grupowania zestawu reguł skanowania w taki sposób, aby można było łatwo skojarzyć je z skanem. Można na przykład utworzyć domyślny zestaw reguł skanowania dla każdego typu źródła danych, a następnie użyć tych zestawów reguł skanowania domyślnie dla wszystkich skanów w firmie. Możesz również chcieć, aby użytkownicy z uprawnieniami do tworzenia innych zestawów reguł skanowania z różnymi konfiguracjami w zależności od potrzeb firmy.

## <a name="steps-to-create-a-scan-rule-set"></a>Procedura tworzenia zestawu reguł skanowania

Aby utworzyć zestaw reguł skanowania:

1. W katalogu usługi Azure kontrolą wybierz pozycję **centrum zarządzania**.

1. Wybierz pozycję **Skanuj zestawy reguł** w lewym okienku, a następnie wybierz pozycję **Nowy**.

1. Na stronie **Nowy zestaw reguł skanowania** wybierz źródła danych obsługiwane przez skaner wykazu z listy rozwijanej **Typ źródła** . Dla każdego typu źródła danych, które ma zostać przeskanowane, można utworzyć zestaw reguł skanowania.

1. Nadaj regule skanowania **nazwę**. Maksymalna długość to 63 znaków, bez spacji. Opcjonalnie wprowadź **Opis**. Maksymalna długość to 256 znaków.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="Zrzut ekranu przedstawiający stronę Ustawienia skanowania zestawu reguł." border="true":::

1. Wybierz opcję **Kontynuuj**.

   Zostanie wyświetlona strona **Wybieranie typów plików** . Zauważ, że opcje typu pliku na tej stronie różnią się w zależności od typu źródła danych wybranego na poprzedniej stronie. Wszystkie typy plików są domyślnie włączone.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="Zrzut ekranu przedstawiający stronę Wybieranie typów plików.":::

   Wybór **typów plików dokumentu** na tej stronie umożliwia dołączenie lub wykluczenie następujących typów plików pakietu Office:. doc, docm, docx,. dot,. ODP,. ods,. ODT,. PDF,. pot,. PPS,. ppsx,. ppt,. pptm, PPTX,. xlc,. xls,. xlsb,. xlsm,. xlsx i. xlt.

1. Włącz lub Wyłącz kafelek typ pliku, zaznaczając lub usuwając zaznaczenie pola wyboru. Jeśli wybierzesz źródło danych typu Data Lake (na przykład Azure Data Lake Storage Gen2 lub obiekt blob platformy Azure), Włącz typy plików, dla których schemat ma zostać wyodrębniony i sklasyfikowany.

1. W przypadku niektórych typów źródeł danych można również [utworzyć niestandardowy typ pliku](#create-a-custom-file-type).

1. Wybierz opcję **Kontynuuj**.

   Zostanie wyświetlona strona **Wybierz reguły klasyfikacji** . Ta strona zawiera wybrane **reguły systemu** i **reguły niestandardowe** oraz łączną liczbę wybranych reguł klasyfikacji. Domyślnie zaznaczone są wszystkie pola wyboru **reguły systemu**

1. Dla reguł, które mają zostać dołączone lub wykluczone, możesz zaznaczyć lub wyczyścić pola wyboru reguły klasyfikacji **reguły systemu** globalnie według kategorii.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="Zrzut ekranu przedstawiający stronę Wybieranie reguł klasyfikacji.":::

1. Można rozwinąć węzeł kategorii i zaznaczyć lub wyczyścić pojedyncze pola wyboru. Na przykład jeśli reguła dla **Argentyny. liczba dni** ma wysokie fałszywe dodatnie, możesz wyczyścić to pole wyboru.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania reguł systemowych.":::

1. Wybierz pozycję **Utwórz** , aby zakończyć tworzenie zestawu reguł skanowania.

### <a name="create-a-custom-file-type"></a>Tworzenie niestandardowego typu pliku

Usługa Azure kontrolą obsługuje Dodawanie niestandardowych rozszerzeń i Definiowanie ogranicznika kolumny niestandardowej w zestawie reguł skanowania.

Aby utworzyć niestandardowy typ pliku:

1. Wykonaj kroki 1 – 5 w [kroku, aby utworzyć zestaw reguł skanowania](#steps-to-create-a-scan-rule-set) lub edytować istniejący zestaw reguł skanowania.

1. Na stronie **Wybieranie typów plików** wybierz pozycję **nowy typ** pliku, aby utworzyć nowy typ pliku niestandardowego.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania nowego typu pliku na stronie Wybieranie typów plików.":::

1. Wprowadź **rozszerzenie pliku** i opcjonalny **Opis**.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="Zrzut ekranu przedstawiający nową stronę typu pliku niestandardowego." border="true":::

1. Wybierz jedną z następujących opcji **zawartości pliku w programie** , aby określić typ zawartości pliku w pliku:

   - Wybierz **niestandardowy ogranicznik** i wprowadź własny **ogranicznik niestandardowy** (tylko pojedynczy znak).

   - Wybierz **Typ pliku systemowego** i wybierz typ pliku systemowego (na przykład XML) z listy rozwijanej **Typ pliku systemowego** .

1. Wybierz pozycję **Utwórz** , aby zapisać plik niestandardowy.

   System wraca do strony **Wybieranie typów plików** i wstawia nowy typ pliku niestandardowego jako nowy kafelek.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="Zrzut ekranu przedstawiający nowy kafelek typ pliku niestandardowego na stronie Wybieranie typów plików.":::

1. Wybierz pozycję **Edytuj** w obszarze nowy typ pliku, jeśli chcesz ją zmienić lub usunąć.

1. Wybierz pozycję **Kontynuuj** , aby zakończyć konfigurowanie zestawu reguł skanowania.

## <a name="system-scan-rule-sets"></a>Zestawy reguł skanowania systemu

Zestawy reguł skanowania systemu są zestawami reguł skanowania zdefiniowanych przez firmę Microsoft, które są tworzone automatycznie dla każdego wykazu usługi Azure kontrolą. Każdy zestaw reguł skanowania systemu jest skojarzony z określonym typem źródła danych. Podczas tworzenia skanowania można je skojarzyć z zestawem reguł skanowania systemu. Za każdym razem, gdy firma Microsoft dokonuje aktualizacji tych zestawów reguł systemowych, można je zaktualizować w wykazie i zastosować aktualizację do wszystkich skojarzonych z nimi skanów.

1. Aby wyświetlić listę zestawów reguł skanowania systemu, wybierz pozycję **Skanuj zestawy reguł** w **centrum zarządzania** i wybierz kartę **system** .

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="Zrzut ekranu przedstawiający listę zestawów reguł skanowania systemu." lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. Każdy zestaw reguł skanowania systemu ma **nazwę**, **Typ źródła** i **wersję**. W przypadku wybrania numeru wersji zestawu reguł skanowania w kolumnie **wersja** są wyświetlane reguły skojarzone z bieżącą wersją i poprzednimi wersjami (jeśli istnieją).

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="Zrzut ekranu przedstawiający stronę zestawu reguł skanowania systemu." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. Jeśli dostępna jest aktualizacja zestawu reguł skanowania systemu, w kolumnie **wersja** można wybrać opcję **Aktualizacja** . Na stronie reguła skanowania systemu wybierz wersję z listy rozwijanej **Wybierz nową wersję do zaktualizowania** . Na stronie znajduje się lista reguł klasyfikacji systemu skojarzonych z nową wersją i bieżącą wersją.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="Zrzut ekranu przedstawiający sposób zmiany wersji zestawu reguł skanowania systemu." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>Kojarzenie skanowania z zestawem reguł skanowania systemu

Podczas [tworzenia skanowania](tutorial-scan-data.md#scan-data-into-the-catalog)można wybrać opcję skojarzenia z zestawem reguł skanowania systemu w następujący sposób:

1. Na stronie **Wybierz zestaw reguł skanowania** wybierz zestaw reguł skanowania systemu.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="Zrzut ekranu przedstawiający sposób wybierania zestawu reguł skanowania systemu do skanowania." lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. Wybierz pozycję **Kontynuuj**, a następnie wybierz pozycję **Zapisz i uruchom**.
