---
title: Wyświetlanie zmian zawartości plików za pomocą usługi Azure Automation
description: Użyj funkcji zmiany zawartości pliku w funkcji Śledzenia zmian, aby wyświetlić zawartość pliku, który uległ zmianie.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 57c3c2c7a0c923921c727ccea7839940457bc1ee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682995"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Wyświetlanie zawartości pliku, który jest śledzony za pomocą śledzenia zmian

Śledzenie zawartości pliku umożliwia wyświetlanie zawartości pliku przed i po zmianie, która jest śledzona za pomocą śledzenia zmian. Aby to zrobić, zapisuje zawartość pliku na koncie magazynu po każdej zmianie.

## <a name="requirements"></a>Wymagania

* Do przechowywania zawartości pliku wymagane jest standardowe konto magazynu przy użyciu modelu wdrażania Menedżera zasobów. Konta magazynu modelu wdrożenia premium i klasycznego wdrożenia nie powinny być używane. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Informacje o kontach magazynu platformy Azure](../storage/common/storage-create-storage-account.md)

* Używane konto magazynu może mieć tylko 1 konto automatyzacji podłączone.

* [Śledzenie zmian](automation-change-tracking.md) jest włączone na koncie automatyzacji.

## <a name="enable-file-content-tracking"></a>Włączanie śledzenia zawartości pliku

1. W witrynie Azure portal otwórz konto automatyzacji, a następnie wybierz pozycję **Zmień śledzenie**.
2. W górnym menu wybierz pozycję **Edytuj ustawienia**.
3. Wybierz **pozycję Zawartość pliku** i kliknij pozycję **Łącze**. Spowoduje to otwarcie okienka **Dodaj lokalizację zawartości do śledzenia zmian.**

   ![Włącz](./media/change-tracking-file-contents/enable.png)

4. Wybierz konto subskrypcji i magazynu, w których chcesz przechowywać zawartość pliku. Jeśli chcesz włączyć śledzenie zawartości plików dla wszystkich istniejących śledzonych plików, wybierz pozycję **Włącz** dla **Przekaż zawartość pliku dla wszystkich ustawień**. Można to zmienić dla każdej ścieżki pliku później.

   ![ustawianie konta magazynu](./media/change-tracking-file-contents/storage-account.png)

5. Po włączeniu wyświetlane są konta magazynu i sas Uris. Sas Uris wygasa po 365 dniach i można go odtworzyć, klikając **przyciskGeneruj.**

   ![lista kluczy konta](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Dodawanie pliku

W następujących krokach można przejść przez włączenie śledzenia zmian dla pliku:

1. Na stronie Edytowanie ustawień **śledzenia zmian**wybierz kartę Pliki **systemu Windows** lub Pliki **systemu Linux,** a następnie kliknij przycisk **Dodaj**

1. Wypełnij informacje dotyczące ścieżki pliku i wybierz pozycję Wartość prawda w obszarze **Przekaż zawartość pliku dla wszystkich ustawień**. To ustawienie umożliwia śledzenie zawartości pliku tylko dla tej ścieżki pliku.

   ![dodawanie pliku linuksa](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Wyświetlanie zawartości śledzonego pliku

1. Po wykryciu zmiany dla pliku lub pliku w ścieżce, zostanie ona wyświetlona w portalu. Wybierz zmianę pliku z listy zmian. Zostanie wyświetlone okienko Zmień szczegóły.

   ![zmiany listy](./media/change-tracking-file-contents/change-list.png)

1. W okienku Zmienianie szczegółów są widoczne standardowe informacje o pliku przed i po. Wybierz **pozycję Wyświetl zmiany zawartości pliku,** aby wyświetlić zawartość pliku.

   ![zmienianie szczegółów](./media/change-tracking-file-contents/change-details.png)

1. Nowa strona pokazuje zawartość pliku w widoku obok siebie. Można również wybrać **opcję W linii,** aby wyświetlić wbudowany widok zmian.

   ![wyświetlanie zmian w pliku](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Następne kroki

Odwiedź samouczek na temat śledzenia zmian, aby dowiedzieć się więcej o korzystaniu z rozwiązania:

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów ze zmianami we własnym środowisku](automation-tutorial-troubleshoot-changes.md)

* Użyj [wyszukiwania dzienników w dziennikach usługi Azure Monitor,](../log-analytics/log-analytics-log-searches.md) aby wyświetlić szczegółowe dane śledzenia zmian.

