---
title: Wyświetl zmiany zawartości plików przy użyciu Azure Automation
description: Użyj funkcji Zmień zawartość plików Change Tracking, aby wyświetlić zawartość pliku, który został zmieniony.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 57c3c2c7a0c923921c727ccea7839940457bc1ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682995"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Wyświetl zawartość pliku, który jest śledzony za pomocą Change Tracking

Śledzenie zawartości plików umożliwia wyświetlenie zawartości pliku przed zmianą, która jest śledzona przy użyciu Change Tracking. W tym celu zapisuje zawartość pliku na koncie magazynu po każdej zmianie.

## <a name="requirements"></a>Wymagania

* Konto magazynu w warstwie Standardowa przy użyciu modelu wdrażania Menedżer zasobów jest wymagane do przechowywania zawartości pliku. Kont magazynu modelu wdrażania w warstwie Premium i klasycznym nie należy używać. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-create-storage-account.md)

* Używane konto magazynu może mieć tylko jedno połączenie z kontem usługi Automation.

* [Change Tracking](automation-change-tracking.md) jest włączona na Twoim koncie usługi Automation.

## <a name="enable-file-content-tracking"></a>Włącz śledzenie zawartości plików

1. W Azure Portal Otwórz konto usługi Automation, a następnie wybierz pozycję **śledzenie zmian**.
2. W górnym menu wybierz pozycję **Edytuj ustawienia**.
3. Wybierz pozycję **zawartość pliku** , a następnie kliknij pozycję **Połącz**. Spowoduje to otwarcie okienka **Dodawanie lokalizacji zawartości dla Change Tracking** .

   ![Włącz](./media/change-tracking-file-contents/enable.png)

4. Wybierz subskrypcję i konto magazynu, w którym ma być przechowywana zawartość pliku. Jeśli chcesz włączyć śledzenie zawartości plików dla wszystkich istniejących śledzonych plików, wybierz pozycję **włączone** , aby **przekazać zawartość pliku dla wszystkich ustawień**. Można zmienić tę funkcję dla każdej ścieżki pliku później.

   ![Ustawianie konta magazynu](./media/change-tracking-file-contents/storage-account.png)

5. Po włączeniu konta magazynu i identyfikatory URI sygnatury dostępu współdzielonego są wyświetlane. Identyfikatory URI sygnatury dostępu współdzielonego wygasną po 365 dniach i można je odtworzyć, klikając przycisk **Regenerate** .

   ![Wyświetl listę kluczy konta](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Dodawanie pliku

Poniższe kroki przeprowadzą Cię przez włączenie śledzenia zmian dla pliku:

1. Na stronie Edytuj ustawienia w obszarze **Change Tracking**wybierz kartę **pliki systemu Windows** lub **Linux plików** , a następnie kliknij przycisk **Dodaj** .

1. Wprowadź informacje dotyczące ścieżki pliku i wybierz wartość true w obszarze **Przekaż zawartość pliku dla wszystkich ustawień**. To ustawienie umożliwia śledzenie zawartości plików tylko dla tej ścieżki pliku.

   ![Dodawanie pliku systemu Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Wyświetlanie zawartości śledzonego pliku

1. Po wykryciu zmiany pliku lub pliku w ścieżce jest on wyświetlany w portalu. Wybierz zmianę pliku z listy zmian. Zostanie wyświetlone okienko Szczegóły zmiany.

   ![zmiany listy](./media/change-tracking-file-contents/change-list.png)

1. W okienku Szczegóły zmiany zobaczysz Standard przed i po informacjach o pliku. Wybierz pozycję **Wyświetl zawartość pliku zmiany** , aby zobaczyć zawartość pliku.

   ![szczegóły zmiany](./media/change-tracking-file-contents/change-details.png)

1. Na nowej stronie zostanie wyświetlona zawartość pliku w widoku obok siebie. Możesz również wybrać opcję **wbudowane** , aby wyświetlić widok w tekście zmian.

   ![Wyświetl zmiany plików](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkiem dotyczącym Change Tracking, aby dowiedzieć się więcej o korzystaniu z rozwiązania:

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów ze zmianami we własnym środowisku](automation-tutorial-troubleshoot-changes.md)

* Użyj [wyszukiwania w dzienniku Azure monitor](../log-analytics/log-analytics-log-searches.md) , aby wyświetlić szczegółowe dane śledzenia zmian.

