---
title: Zarządzanie Change Tracking i spisem w Azure Automation
description: W tym artykule opisano sposób używania Change Tracking i spisu do śledzenia zmian oprogramowania i usług firmy Microsoft w danym środowisku.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: a599bb6f07683540f5b12c6a69d6565161f89a4f
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210333"
---
# <a name="manage-change-tracking-and-inventory"></a>Zarządzanie usługą Change Tracking and Inventory

Po dodaniu nowego pliku lub klucza rejestru do śledzenia Program Azure Automation włącza go dla [Change Tracking i spisu](overview.md). W tym artykule opisano sposób konfigurowania śledzenia, przeglądania wyników śledzenia i obsługi alertów w przypadku wykrycia zmian.

Przed rozpoczęciem korzystania z procedur opisanych w tym artykule upewnij się, że na maszynach wirtualnych włączono Change Tracking i spis, korzystając z jednej z następujących metod:

* [Włączanie śledzenia zmian i spisu na koncie usługi Automation](enable-from-automation-account.md)
* [Włącz Change Tracking i spis, przeglądając Azure Portal](enable-from-portal.md)
* [Włączanie śledzenia zmian i spisu z poziomu elementu runbook](enable-from-runbook.md)
* [Włączanie śledzenia zmian i spisu na maszynie wirtualnej platformy Azure](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Ograniczanie zakresu wdrożenia

Change Tracking i spis używają konfiguracji zakresu w obszarze roboczym, aby określić, że komputery mają otrzymywać zmiany. Aby uzyskać więcej informacji, zobacz [ograniczanie Change Tracking i zakresu wdrożenia spisu](manage-scope-configurations.md).

## <a name="track-files"></a>Śledzenie plików

Change Tracking i spisu można użyć do śledzenia zmian plików i folderów/katalogów. Ta sekcja zawiera informacje o konfigurowaniu śledzenia plików w systemie Windows i w systemie Linux.

### <a name="configure-file-tracking-on-windows"></a>Konfigurowanie śledzenia plików w systemie Windows

Wykonaj następujące kroki, aby skonfigurować śledzenie plików na komputerach z systemem Windows:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz **Automation**. Po rozpoczęciu wpisywania lista filtruje sugestie w oparciu o dane wejściowe. Wybierz opcję **Konta automatyzacji**.

3. Na liście kont usługi Automation wybierz konto wybrane podczas włączania Change Tracking i spisu.

4. Na koncie usługi Automation wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

5. Wybierz pozycję **Edytuj ustawienia** (symbol koła zębatego).

6. Na stronie Konfiguracja obszaru roboczego wybierz pozycję **pliki systemu Windows**, a następnie kliknij pozycję **+ Dodaj** , aby dodać nowy plik do śledzenia.

7. W okienku Dodaj plik systemu Windows dla Change Tracking wprowadź informacje dotyczące pliku lub folderu do śledzenia, a następnie kliknij przycisk **Zapisz**. Poniższa tabela zawiera definicje właściwości, których można użyć w celu uzyskania informacji.

    |Właściwość  |Opis  |
    |---------|---------|
    |Enabled (Włączony)     | Ma wartość true, jeśli to ustawienie jest stosowane i w przeciwnym razie zwraca wartość false.        |
    |Nazwa elementu     | Przyjazna nazwa pliku do śledzenia.        |
    |Group (Grupa)     | Nazwa grupy do logicznego grupowania plików.        |
    |Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład **c:\Temp \\ \* . txt**. Można również używać zmiennych środowiskowych, takich jak `%winDir%\System32\\\*.*` .       |
    |Typ ścieżki     | Typ ścieżki. Możliwe wartości to plik i folder.        |    
    |Rekursja     | Prawda, Jeśli rekursja jest używana podczas wyszukiwania elementu do śledzenia, a w przeciwnym razie ma wartość false.        |    
    |Przekaż zawartość pliku | Wartość true, aby przekazać zawartość pliku do śledzonych zmian, i w przeciwnym razie zwraca wartość false.|

8. Upewnij się, że określono wartość true dla **zawartości pliku do przekazania**. To ustawienie umożliwia śledzenie zawartości plików dla wskazanej ścieżki pliku.

### <a name="configure-file-tracking-on-linux"></a>Konfigurowanie śledzenia plików w systemie Linux

Wykonaj następujące kroki, aby skonfigurować śledzenie plików na komputerach z systemem Linux:

1. Wybierz pozycję **Edytuj ustawienia** (symbol koła zębatego).

2. Na stronie Konfiguracja obszaru roboczego wybierz pozycję **pliki systemu Linux**, a następnie wybierz pozycję **+ Dodaj** , aby dodać nowy plik do śledzenia.

3. Na stronie **Dodawanie pliku systemu Linux dla Change Tracking** wprowadź informacje dotyczące pliku lub katalogu, który ma być śledzony, a następnie wybierz pozycję **Zapisz**. Poniższa tabela zawiera definicje właściwości, których można użyć w celu uzyskania informacji.

    |Właściwość  |Opis  |
    |---------|---------|
    |Enabled (Włączony)     | Ma wartość true, jeśli to ustawienie jest stosowane i w przeciwnym razie zwraca wartość false.        |
    |Nazwa elementu     | Przyjazna nazwa pliku do śledzenia.        |
    |Group (Grupa)     | Nazwa grupy do logicznego grupowania plików.        |
    |Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład **/etc/*. conf**.       |
    |Typ ścieżki     | Typ ścieżki. Możliwe wartości to plik i katalog.        |
    |Rekursja     | Prawda, Jeśli rekursja jest używana podczas wyszukiwania elementu do śledzenia, a w przeciwnym razie ma wartość false.        |
    |Użyj polecenia Sudo     | Wartość true powoduje użycie sudo podczas sprawdzania elementu i FAŁSZ w przeciwnym razie.         |
    |Linki     | Ustawienie określające sposób postępowania z łączami symbolicznymi podczas przechodzenia między katalogami. Możliwe wartości:<br> Ignoruj — ignoruje linki symboliczne i nie uwzględnia plików/katalogów, do których się odwołuje.<br>Obserwuj poniższe linki symboliczne podczas rekursji, a także zawiera pliki/katalogi, do których się odwołuje.<br>Zarządzaj — następuje po linków symbolicznych i umożliwia zmianę zwracanej zawartości.<br>**Uwaga:** Opcja zarządzania nie jest zalecana, ponieważ nie obsługuje pobierania zawartości pliku.    |
    |Przekaż zawartość pliku | Wartość true, aby przekazać zawartość pliku do śledzonych zmian, i w przeciwnym razie zwraca wartość false. |

4. Upewnij się, że określono **wartość true** dla **zawartości pliku do przekazania**. To ustawienie umożliwia śledzenie zawartości plików dla wskazanej ścieżki pliku.

   ![Dodaj plik systemu Linux](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>Śledź zawartość pliku

Śledzenie zawartości plików umożliwia wyświetlenie zawartości pliku przed prześledzoną zmianą i po niej. Funkcja zapisuje zawartość pliku na [koncie magazynu](../../storage/common/storage-account-overview.md) po każdej zmianie. Oto kilka reguł, które należy wykonać, aby śledzić zawartość pliku:

* Konto magazynu w warstwie Standardowa przy użyciu modelu wdrażania Menedżer zasobów jest wymagane do przechowywania zawartości pliku.
* Nie używaj kont magazynu modelu wdrażania Premium i klasycznych. Zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-account-create.md).
* Konto magazynu można połączyć tylko z jednym kontem usługi Automation.
* Na koncie usługi Automation należy włączyć Change Tracking i spis.

### <a name="enable-tracking-for-file-content-changes"></a>Włącz śledzenie zmian zawartości plików

Wykonaj następujące kroki, aby włączyć śledzenie zmian zawartości pliku:

1. Wybierz pozycję **Edytuj ustawienia** (symbol koła zębatego).

2. Wybierz pozycję **zawartość pliku** , a następnie wybierz pozycję **Połącz**. Wybranie tej opcji spowoduje otwarcie strony **Dodawanie lokalizacji zawartości dla Change Tracking** .

   ![Dodaj lokalizację zawartości](./media/manage-change-tracking/enable.png)

3. Wybierz subskrypcję i konto magazynu, które ma być używane do przechowywania zawartości pliku.

5. Jeśli chcesz włączyć śledzenie zawartości plików dla wszystkich istniejących śledzonych plików, wybierz pozycję **włączone** , aby **przekazać zawartość pliku dla wszystkich ustawień**. To ustawienie można zmienić dla każdej ścieżki pliku później.

   ![Ustawianie konta magazynu](./media/manage-change-tracking/storage-account.png)

6. Change Tracking i spis pokazuje identyfikatory URI konta magazynu i sygnatury dostępu współdzielonego (SAS) podczas włączania śledzenia zmian zawartości plików. Sygnatury wygasną po 365 dniach i można je utworzyć ponownie, wybierając pozycję **Regenerate**.

   ![Wyświetl listę kluczy konta](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Wyświetlanie zawartości śledzonego pliku

Gdy Change Tracking i spis wykryje zmianę dla śledzonego pliku, można wyświetlić zawartość pliku w okienku Szczegóły zmiany.  

![Zmiany listy](./media/manage-change-tracking/change-list.png)

1. Na stronie **śledzenie zmian** na koncie usługi Automation wybierz plik z listy zmian, a następnie wybierz pozycję **Wyświetl zawartość pliku zmiany** , aby zobaczyć zawartość pliku. W okienku Szczegóły zmiany zostanie wyświetlony Standard przed i po informacjach o pliku dla każdej właściwości.

   ![Szczegóły zmiany](./media/manage-change-tracking/change-details.png)

2. Zawartość pliku jest wyświetlana w widoku obok siebie. Możesz wybrać opcję **wbudowane** , aby wyświetlić widok w tekście zmian.

## <a name="track-registry-keys"></a>Śledzenie kluczy rejestru

Wykonaj następujące kroki, aby skonfigurować śledzenie kluczy rejestru na komputerach z systemem Windows:

1. Na stronie **śledzenie zmian** na koncie usługi Automation wybierz pozycję **Edytuj ustawienia** (symbol koła zębatego).

2. Na stronie Konfiguracja obszaru roboczego wybierz pozycję **Rejestr systemu Windows**.

3. Wybierz pozycję **+ Dodaj** , aby dodać nowy klucz rejestru do śledzenia.

4. Na stronie **Dodaj rejestr systemu Windows dla Change Tracking** wprowadź informacje dotyczące klucza do śledzenia, a następnie wybierz pozycję **Zapisz**. Poniższa tabela zawiera definicje właściwości, których można użyć w celu uzyskania informacji.

    |Właściwość  |Opis  |
    |---------|---------|
    |Enabled (Włączony)     | Ma wartość true, jeśli jest stosowane ustawienie i w przeciwnym razie ma wartość false.        |
    |Nazwa elementu     | Przyjazna nazwa klucza rejestru do śledzenia.        |
    |Group (Grupa)     | Nazwa grupy do logicznego grupowania kluczy rejestru.        |
    |Klucz rejestru systemu Windows   | Nazwa klucza z ścieżką, na przykład `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Wyszukaj w dziennikach zmiany rekordów

Możesz wykonywać różne wyszukiwania względem dzienników Azure Monitor pod kątem rekordów zmian. Gdy zostanie otwarta strona śledzenie zmian, kliknij przycisk **log Analytics** , aby otworzyć stronę Dzienniki. Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów zmian.

|Zapytanie  |Opis  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Pokazuje najnowsze rekordy spisu dla usług firmy Microsoft, które zostały ustawione na wartość automatycznie, ale zostały zgłoszone jako zatrzymane. Wyniki są ograniczone do najnowszego rekordu dla określonej nazwy oprogramowania i komputera.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Pokazuje zmiany rekordów dla usuniętego oprogramowania.|

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat konfiguracji zakresów, zobacz [ograniczanie Change Tracking i zakresu wdrożenia spisu](manage-scope-configurations.md).
* Jeśli musisz przeszukać dzienniki przechowywane w dziennikach Azure Monitor, zobacz [Wyszukiwanie w dzienniku w](../../azure-monitor/log-query/log-query-overview.md)dziennikach Azure monitor.
* Jeśli zakończono wdrożenia, zobacz [usuwanie Change Tracking i spisu](remove-feature.md).
* Aby usunąć maszyny wirtualne ze Change Tracking i spisu, zobacz [usuwanie maszyn wirtualnych z Change Tracking i spisu](remove-vms-from-change-tracking.md).
* Rozwiązywanie problemów z błędami funkcji można znaleźć w temacie [Rozwiązywanie problemów dotyczących Change Tracking i spisu](../troubleshoot/change-tracking.md).
