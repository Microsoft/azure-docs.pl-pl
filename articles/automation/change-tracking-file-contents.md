---
title: Zarządzanie Change Tracking i spisem w Azure Automation
description: W tym artykule opisano sposób używania Change Tracking i spisu do śledzenia zmian oprogramowania i usług firmy Microsoft w danym środowisku.
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: eab509e389c074232526aa93fcebb72f3bc986c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185606"
---
# <a name="manage-change-tracking-and-inventory"></a>Zarządzanie usługą Change Tracking and Inventory

Po dodaniu nowego pliku lub klucza rejestru do śledzenia Program Azure Automation włącza go dla [Change Tracking i spisu](change-tracking.md). W tym artykule opisano sposób konfigurowania śledzenia, przeglądania wyników śledzenia i obsługi alertów w przypadku wykrycia zmian.

Przed rozpoczęciem korzystania z procedur opisanych w tym artykule upewnij się, że na maszynach wirtualnych włączono Change Tracking i spis, korzystając z jednej z następujących metod:

* [Włączanie śledzenia zmian i spisu na koncie usługi Automation](automation-enable-changes-from-auto-acct.md)
* [Włącz Change Tracking i spis, przeglądając Azure Portal](automation-enable-changes-from-browse.md)
* [Włączanie śledzenia zmian i spisu z poziomu elementu runbook](automation-enable-changes-from-runbook.md)
* [Włączanie śledzenia zmian i spisu na maszynie wirtualnej platformy Azure](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Ograniczanie zakresu wdrożenia

Change Tracking i spis używają konfiguracji zakresu w obszarze roboczym, aby określić, że komputery mają otrzymywać zmiany. Aby uzyskać więcej informacji, zobacz [ograniczanie Change Tracking i zakresu wdrożenia spisu](automation-scope-configurations-change-tracking.md).

## <a name="track-files"></a>Śledzenie plików

Change Tracking i spisu można użyć do śledzenia zmian plików i folderów/katalogów. Ta sekcja zawiera informacje o konfigurowaniu śledzenia plików w systemie Windows i w systemie Linux.

### <a name="configure-file-tracking-on-windows"></a>Konfigurowanie śledzenia plików w systemie Windows

Wykonaj następujące kroki, aby skonfigurować śledzenie plików na komputerach z systemem Windows:

1. Na koncie usługi Automation wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. 
2. Kliknij pozycję **Edytuj ustawienia** (symbol koła zębatego).
3. Na stronie Konfiguracja obszaru roboczego wybierz pozycję **pliki systemu Windows**, a następnie kliknij pozycję **+ Dodaj** , aby dodać nowy plik do śledzenia.
4. W okienku Dodaj plik systemu Windows dla Change Tracking wprowadź informacje dotyczące pliku lub folderu do śledzenia, a następnie kliknij przycisk **Zapisz**. Poniższa tabela zawiera definicje właściwości, których można użyć w celu uzyskania informacji.

    |Właściwość  |Opis  |
    |---------|---------|
    |Enabled (Włączony)     | Ma wartość true, jeśli to ustawienie jest stosowane i w przeciwnym razie zwraca wartość false.        |
    |Nazwa elementu     | Przyjazna nazwa pliku do śledzenia.        |
    |Group (Grupa)     | Nazwa grupy do logicznego grupowania plików.        |
    |Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład **c:\Temp \\ \* . txt**. Można również używać zmiennych środowiskowych, takich jak `%winDir%\System32\\\*.*` .       |
    |Typ ścieżki     | Typ ścieżki. Możliwe wartości to plik i folder.        |    
    |Rekursja     | Prawda, Jeśli rekursja jest używana podczas wyszukiwania elementu do śledzenia, a w przeciwnym razie ma wartość false.        |    
    |Przekaż zawartość pliku | Wartość true, aby przekazać zawartość pliku do śledzonych zmian, i w przeciwnym razie zwraca wartość false.|

5. Upewnij się, że określono wartość true dla **zawartości pliku do przekazania**. To ustawienie umożliwia śledzenie zawartości plików dla wskazanej ścieżki pliku.

### <a name="configure-file-tracking-on-linux"></a>Konfigurowanie śledzenia plików w systemie Linux

Wykonaj następujące kroki, aby skonfigurować śledzenie plików na komputerach z systemem Linux:

1. Na koncie usługi Automation wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. 
2. Kliknij pozycję **Edytuj ustawienia** (symbol koła zębatego).
3. Na stronie Konfiguracja obszaru roboczego wybierz pozycję **pliki systemu Linux**, a następnie kliknij pozycję **+ Dodaj** , aby dodać nowy plik do śledzenia.
4. W okienku Dodawanie pliku systemu Linux dla Change Tracking wprowadź informacje dotyczące pliku lub katalogu do śledzenia, a następnie kliknij przycisk **Zapisz**. Poniższa tabela zawiera definicje właściwości, których można użyć w celu uzyskania informacji.

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

5. Upewnij się, że określono wartość true dla **zawartości pliku do przekazania**. To ustawienie umożliwia śledzenie zawartości plików dla wskazanej ścieżki pliku.

   ![Dodaj plik systemu Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Śledź zawartość pliku

Śledzenie zawartości plików umożliwia wyświetlenie zawartości pliku przed prześledzoną zmianą i po niej. Funkcja zapisuje zawartość pliku na [koncie magazynu](../storage/common/storage-account-overview.md) po każdej zmianie. Oto kilka reguł, które należy wykonać, aby śledzić zawartość pliku:

* Konto magazynu w warstwie Standardowa przy użyciu modelu wdrażania Menedżer zasobów jest wymagane do przechowywania zawartości pliku. 
* Nie używaj kont magazynu modelu wdrażania Premium i klasycznych. Zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-account-create.md).
* Konto magazynu można połączyć tylko z jednym kontem usługi Automation.
* Na koncie usługi Automation należy włączyć [Change Tracking i spis](change-tracking.md) .

### <a name="enable-tracking-for-file-content-changes"></a>Włącz śledzenie zmian zawartości plików

Wykonaj następujące kroki, aby włączyć śledzenie zmian zawartości pliku:

1. W Azure Portal Otwórz konto usługi Automation, a następnie wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.
2. Kliknij pozycję **Edytuj ustawienia** (symbol koła zębatego).
3. Wybierz pozycję **zawartość pliku** , a następnie kliknij pozycję **Połącz**. Zaznaczenie spowoduje otwarcie okienka Dodawanie lokalizacji zawartości dla Change Tracking.

   ![Dodaj lokalizację zawartości](./media/change-tracking-file-contents/enable.png)

4. Wybierz subskrypcję i konto magazynu, które ma być używane do przechowywania zawartości pliku. 

5. Jeśli chcesz włączyć śledzenie zawartości plików dla wszystkich istniejących śledzonych plików, wybierz pozycję **włączone** , aby **przekazać zawartość pliku dla wszystkich ustawień**. To ustawienie można zmienić dla każdej ścieżki pliku później.

   ![Ustawianie konta magazynu](./media/change-tracking-file-contents/storage-account.png)

6. Change Tracking i spis pokazuje identyfikatory URI konta magazynu i sygnatury dostępu współdzielonego (SAS) podczas włączania śledzenia zmian zawartości plików. Sygnatury wygasną po 365 dniach, a następnie można je utworzyć ponownie, klikając przycisk **Wygeneruj ponownie**.

   ![Wyświetl listę kluczy konta](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Wyświetlanie zawartości śledzonego pliku

Gdy Change Tracking i spis wykryje zmianę dla śledzonego pliku, można wyświetlić zawartość pliku w okienku Szczegóły zmiany.  

![Zmiany listy](./media/change-tracking-file-contents/change-list.png)

1. W Azure Portal Otwórz konto usługi Automation, a następnie wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

2. Wybierz plik z listy zmian i wybierz pozycję **Wyświetl zawartość pliku zmiany** , aby zobaczyć zawartość pliku. W okienku Szczegóły zmiany zostanie wyświetlony Standard przed i po informacjach o pliku dla każdej właściwości.

   ![Szczegóły zmiany](./media/change-tracking-file-contents/change-details.png)

3. Zawartość pliku jest wyświetlana w widoku obok siebie. Możesz wybrać opcję **wbudowane** , aby wyświetlić widok w tekście zmian.

## <a name="track-registry-keys"></a>Śledzenie kluczy rejestru

Wykonaj następujące kroki, aby skonfigurować śledzenie kluczy rejestru na komputerach z systemem Windows:

1. W Azure Portal Otwórz konto usługi Automation, a następnie wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. 
2. Kliknij pozycję **Edytuj ustawienia** (symbol koła zębatego).
3. Na stronie Konfiguracja obszaru roboczego wybierz pozycję **Rejestr systemu Windows**.
4. Kliknij pozycję **+ Dodaj** , aby dodać nowy klucz rejestru do śledzenia.
5. W okienku Dodaj rejestr systemu Windows dla Change Tracking wprowadź informacje dotyczące klucza do śledzenia, a następnie kliknij przycisk **Zapisz**. Poniższa tabela zawiera definicje właściwości, których można użyć w celu uzyskania informacji.

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

## <a name="create-alerts-on-changes"></a>Tworzenie alertów dotyczących zmian

Poniższy przykład pokazuje, że plik **c:\windows\System32\drivers\etc\HOSTS** został zmodyfikowany na komputerze. Ten plik jest ważny, ponieważ system Windows używa go do rozpoznawania nazw hostów na adresy IP. Ta operacja ma pierwszeństwo przed usługą DNS i może spowodować problemy z łącznością. Może również prowadzić do przekierowywania ruchu do złośliwych lub w inny sposób niebezpiecznych witryn sieci Web.

![Wykres przedstawiający zmianę pliku hosts](./media/change-tracking-file-contents/changes.png)

Skorzystajmy z tego przykładu, aby omówić kroki tworzenia alertów dotyczących zmian.

1. Na koncie usługi Automation wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**, a następnie wybierz pozycję **log Analytics**. 
2. W wyszukiwaniu dzienników Znajdź zmiany zawartości w pliku **hosts** z zapytaniem `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . To zapytanie szuka zmian zawartości plików z w pełni kwalifikowanymi nazwami ścieżek zawierającymi wyraz `hosts` . Możesz również poszukać określonego pliku, zmieniając część ścieżki na jej w pełni kwalifikowaną postać, na przykład używając `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Gdy zapytanie zwróci wyniki, kliknij pozycję **Nowa reguła alertu** w przeszukiwaniu dzienników, aby otworzyć stronę tworzenie alertów. Możesz również przejść do tej strony za pomocą **Azure monitor** w Azure Portal. 

4. Sprawdź ponownie zapytanie i zmodyfikuj logikę alertów. W takim przypadku alert ma być wyzwalany, jeśli istnieje nawet jedna zmiana została wykryta między wszystkimi maszynami w środowisku.

    ![Zmień, aby wykonać zapytanie dotyczące śledzenia zmian w pliku hosts](./media/change-tracking-file-contents/change-query.png)

5. Po ustawieniu logiki alertu Przypisz grupy akcji, aby wykonać działania w odpowiedzi na wyzwolenie alertu. W takim przypadku konfigurujemy wiadomości e-mail do wysłania i zostanie utworzony bilet zarządzania usługami IT (narzędzia ITSM). 

    ![Konfigurowanie grupy akcji do alertu dotyczącego zmiany](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat konfiguracji zakresów, zobacz [ograniczanie Change Tracking i zakresu wdrożenia spisu](automation-scope-configurations-change-tracking.md).
* Jeśli musisz przeszukać dzienniki przechowywane w obszarze roboczym Log Analytics, zobacz [Wyszukiwanie w dzienniku w](../azure-monitor/log-query/log-query-overview.md)dziennikach Azure monitor.
* Jeśli zakończono wdrożenia, zobacz [Odłącz obszar roboczy z konta usługi Automation dla Change Tracking i spisu](automation-unlink-workspace-change-tracking.md).
* Aby usunąć maszyny wirtualne ze Change Tracking i spisu, zobacz [usuwanie maszyn wirtualnych z Change Tracking i spisu](automation-remove-vms-from-change-tracking.md).
* Rozwiązywanie problemów z błędami funkcji można znaleźć w temacie [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md).
