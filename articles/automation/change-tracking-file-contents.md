---
title: Zarządzanie Change Tracking i spisem w Azure Automation
description: W tym artykule opisano sposób używania Change Tracking i spisu do śledzenia zmian oprogramowania i usług firmy Microsoft w danym środowisku.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 8e5ee8df1dfd250a6713d832bf176daecdaef7ea
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744405"
---
# <a name="manage-change-tracking-and-inventory"></a>Zarządzanie usługą Change Tracking and Inventory

Azure Automation włącza funkcję [Change Tracking i spis](change-tracking.md) dla maszyn w środowisku. Funkcja śledzi i wprowadza dostępne zmiany w kluczach rejestru, plikach, zawartości i tak samo. Ten artykuł zawiera procedury umożliwiające pracę z tą funkcją.

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>Włącz pełną Change Tracking i funkcję spisu

Jeśli włączono funkcję [monitorowania integralności plików Azure Security Center (FIM)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring), możesz użyć funkcji pełnego Change Tracking i spisu dla maszyn, jak opisano poniżej. Twoje ustawienia nie są usuwane przez ten proces.

> [!NOTE]
> Włączenie pełnego Change Tracking i funkcji spisu może spowodować naliczenie dodatkowych opłat. Zobacz [Cennik usługi Automation](https://azure.microsoft.com/pricing/details/automation/).

1. Usuń rozwiązanie monitorowania, przechodząc do obszaru roboczego i lokalizowanie go na [liście zainstalowanych rozwiązań monitorowania](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Kliknij nazwę rozwiązania, aby otworzyć jego stronę podsumowania, a następnie kliknij pozycję **Usuń**, zgodnie z opisem w temacie [usuwanie rozwiązania monitorowania](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Aby ponownie włączyć Change Tracking i spis, przejdź do konta usługi Automation i wybierz pozycję **śledzenie zmian** lub **spis** w obszarze **Zarządzanie konfiguracją**.
4. Wybierz obszar roboczy Log Analytics i konto usługi Automation, Potwierdź ustawienia obszaru roboczego, a następnie kliknij pozycję **Włącz**.

## <a name="enable-machines-for-change-tracking-and-inventory"></a><a name="onboard"></a>Włącz maszyny dla Change Tracking i spisu

Aby rozpocząć śledzenie zmian, należy włączyć Change Tracking i spis w Azure Automation. Poniżej przedstawiono zalecane i obsługiwane sposoby włączania tej funkcji dla maszyn: 

* [Włącz z poziomu maszyny wirtualnej](automation-onboard-solutions-from-vm.md)
* [Zezwalaj na przeglądanie wielu maszyn](automation-onboard-solutions-from-browse.md)
* [Włącz z konta usługi Automation](automation-onboard-solutions-from-automation-account.md)
* [Włącz w elemencie Runbook Azure Automation](automation-onboard-solutions.md)

## <a name="track-files"></a>Śledzenie plików

### <a name="configure-file-tracking-on-windows"></a>Konfigurowanie śledzenia plików w systemie Windows

Wykonaj następujące kroki, aby skonfigurować śledzenie plików na komputerach z systemem Windows:

1. Na koncie usługi Automation wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. 
2. Kliknij pozycję **Edytuj ustawienia** (symbol koła zębatego).
3. Na stronie Konfiguracja obszaru roboczego wybierz pozycję **pliki systemu Windows**, a następnie kliknij pozycję **+ Dodaj** , aby dodać nowy plik do śledzenia.
4. W okienku Dodaj plik systemu Windows dla Change Tracking wprowadź informacje dotyczące pliku do śledzenia i kliknij przycisk **Zapisz**. Poniższa tabela zawiera definicje właściwości, których można użyć w celu uzyskania informacji.

    |Właściwość  |Opis  |
    |---------|---------|
    |Enabled (Włączony)     | Ma wartość true, jeśli to ustawienie jest stosowane i w przeciwnym razie zwraca wartość false.        |
    |Nazwa elementu     | Przyjazna nazwa pliku do śledzenia.        |
    |Grupa     | Nazwa grupy do logicznego grupowania plików.        |
    |Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład **c:\Temp \\ \* . txt**. Można również używać zmiennych środowiskowych, takich jak `%winDir%\System32\\\*.*` .       |
    |Typ ścieżki     | Typ ścieżki. Możliwe wartości to plik i katalog.        |    
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
    |Grupa     | Nazwa grupy do logicznego grupowania plików.        |
    |Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład **/etc/*. conf**.       |
    |Typ ścieżki     | Typ ścieżki. Możliwe wartości to plik i katalog.        |
    |Rekursja     | Prawda, Jeśli rekursja jest używana podczas wyszukiwania elementu do śledzenia, a w przeciwnym razie ma wartość false.        |
    |Użyj polecenia Sudo     | Wartość true powoduje użycie sudo podczas sprawdzania elementu i FAŁSZ w przeciwnym razie.         |
    |Linki     | Ustawienie określające sposób postępowania z łączami symbolicznymi podczas przechodzenia między katalogami. Możliwe wartości:<br> Ignoruj — ignoruje linki symboliczne i nie uwzględnia plików/katalogów, do których się odwołuje.<br>Obserwuj poniższe linki symboliczne podczas rekursji, a także zawiera pliki/katalogi, do których się odwołuje.<br>Zarządzanie — następuje po linków symbolicznych i umożliwia zmianę zwracanej zawartości. **Uwaga** — ta opcja nie jest zalecana, ponieważ nie obsługuje pobierania zawartości pliku.    |
    |Przekaż zawartość pliku | Wartość true, aby przekazać zawartość pliku do śledzonych zmian, i w przeciwnym razie zwraca wartość false. |

5. Upewnij się, że określono wartość true dla **zawartości pliku do przekazania**. To ustawienie umożliwia śledzenie zawartości plików dla wskazanej ścieżki pliku.

   ![Dodaj plik systemu Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Śledź zawartość pliku

Śledzenie zawartości plików umożliwia wyświetlenie zawartości pliku przed prześledzoną zmianą i po niej. Funkcja zapisuje zawartość pliku na koncie magazynu po każdej zmianie. Oto kilka reguł, które należy wykonać, aby śledzić zawartość pliku:

* Konto magazynu w warstwie Standardowa przy użyciu modelu wdrażania Menedżer zasobów jest wymagane do przechowywania zawartości pliku. 

* Nie używaj kont magazynu modelu wdrażania Premium i klasycznych. Zobacz [Informacje o kontach usługi Azure Storage](../storage/common/storage-create-storage-account.md).

* Używane konto magazynu może być połączone tylko z jednym kontem usługi Automation.

* [Change Tracking i spis](change-tracking.md) jest włączony na Twoim koncie usługi Automation.

### <a name="enable-tracking-for-file-content-changes"></a>Włącz śledzenie zmian zawartości plików

1. W Azure Portal Otwórz konto usługi Automation, a następnie wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.
2. Kliknij pozycję **Edytuj ustawienia** (symbol koła zębatego).
3. Wybierz pozycję **zawartość pliku** , a następnie kliknij pozycję **Połącz**. Zaznaczenie spowoduje otwarcie okienka Dodawanie lokalizacji zawartości dla Change Tracking.

   ![Włącz lokalizację zawartości](./media/change-tracking-file-contents/enable.png)

4. Wybierz subskrypcję i konto magazynu, które ma być używane do przechowywania zawartości pliku. 

5. Jeśli chcesz włączyć śledzenie zawartości plików dla wszystkich istniejących śledzonych plików, wybierz pozycję **włączone** , aby **przekazać zawartość pliku dla wszystkich ustawień**. To ustawienie można zmienić dla każdej ścieżki pliku później.

   ![Ustawianie konta magazynu](./media/change-tracking-file-contents/storage-account.png)

6. Change Tracking i spis pokazuje identyfikatory URI konta magazynu i sygnatury dostępu współdzielonego (SAS) podczas włączania śledzenia zmian zawartości plików. Sygnatury wygasną po 365 dniach, a następnie można je utworzyć ponownie, klikając przycisk **Wygeneruj ponownie**.

   ![Wyświetl listę kluczy konta](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Wyświetlanie zawartości śledzonego pliku

Gdy Change Tracking i spis wykryje zmianę dla śledzonego pliku, można wyświetlić zawartość pliku w okienku Szczegóły zmiany.  

![Zmiany listy](./media/change-tracking-file-contents/change-list.png)

1. W Azure Portal Otwórz konto usługi Automation, a następnie wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

2. Wybierz plik z listy zmian i wybierz pozycję **Wyświetl zawartość pliku zmiany** , aby zobaczyć zawartość pliku. W okienku Szczegóły zmiany są wyświetlane informacje standardowe przed i po pliku.

   ![Szczegóły zmiany](./media/change-tracking-file-contents/change-details.png)

3. Zawartość pliku jest wyświetlana w widoku obok siebie. Możesz wybrać opcję **wbudowane** , aby wyświetlić widok w tekście zmian.

## <a name="track-registry-keys"></a>Śledzenie kluczy rejestru

Wykonaj następujące kroki, aby skonfigurować śledzenie kluczy rejestru na komputerach z systemem Windows:

1. Na koncie usługi Automation wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. 
2. Kliknij pozycję **Edytuj ustawienia** (symbol koła zębatego).
3. Na stronie Konfiguracja obszaru roboczego wybierz pozycję **Rejestr systemu Windows**.
4. Kliknij pozycję **+ Dodaj** , aby dodać nowy klucz rejestru do śledzenia.
5. W okienku Dodaj rejestr systemu Windows dla Change Tracking wprowadź informacje dotyczące klucza do śledzenia, a następnie kliknij przycisk **Zapisz**. Poniższa tabela zawiera definicje właściwości, których można użyć w celu uzyskania informacji.

    |Właściwość  |Opis  |
    |---------|---------|
    |Enabled (Włączony)     | Ma wartość true, jeśli jest stosowane ustawienie i w przeciwnym razie ma wartość false.        |
    |Nazwa elementu     | Przyjazna nazwa klucza rejestru do śledzenia.        |
    |Grupa     | Nazwa grupy do logicznego grupowania kluczy rejestru.        |
    |Klucz rejestru systemu Windows   | Nazwa klucza z ścieżką, na przykład **HKEY_LOCAL_MACHINE \Software\microsoft\windows\currentversion\explorer\user Shell Folders\Common Start**.      |

## <a name="search-logs-for-change-records"></a>Wyszukaj w dziennikach zmiany rekordów

Możesz wykonywać różne wyszukiwania względem dzienników Azure Monitor pod kątem rekordów zmian. Gdy zostanie otwarta strona śledzenie zmian, kliknij przycisk **log Analytics** , aby otworzyć stronę Dzienniki. Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów zmian.

|Zapytanie  |Opis  |
|---------|---------|
|ConfigurationData<br>&#124; gdzie ConfigDataType = = "Microsoft Services" i SvcStartupType = = "Auto"<br>&#124; gdzie SvcState = = "zatrzymana"<br>&#124; Podsumuj arg_max (TimeGenerated, *) według oprogramowania, komputera         | Pokazuje najnowsze rekordy spisu dla usług firmy Microsoft, które zostały ustawione na wartość automatycznie, ale zostały zgłoszone jako zatrzymane. Wyniki są ograniczone do najnowszego rekordu dla określonej nazwy oprogramowania i komputera.    |
|Zmianakonfiguracji<br>&#124;, gdzie ConfigChangeType = = "oprogramowanie" i ChangeCategory = = "usunięte"<br>&#124; Porządkuj według TimeGenerated|Pokazuje zmiany rekordów dla usuniętego oprogramowania.|

## <a name="create-alerts-on-changes"></a>Tworzenie alertów dotyczących zmian

Poniższy przykład pokazuje, że plik **C:\windows\System32\drivers\etc\HOSTS** został zmodyfikowany na komputerze. Ten plik jest ważny, ponieważ system Windows używa go do rozpoznawania nazw hostów na adresy IP. Ta operacja ma pierwszeństwo przed usługą DNS i może spowodować problemy z łącznością. Może również prowadzić do przekierowywania ruchu do złośliwych lub w inny sposób niebezpiecznych witryn sieci Web.

![Wykres przedstawiający zmianę pliku hosts](./media/change-tracking-file-contents/changes.png)

Skorzystajmy z tego przykładu, aby omówić kroki tworzenia alertów dotyczących zmian.

1. Na koncie usługi Automation wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**, a następnie wybierz pozycję **log Analytics**. 
2. W wyszukiwaniu dzienników Znajdź zmiany zawartości w pliku **hosts** z zapytaniem `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . To zapytanie szuka zmiany zawartości plików z w pełni kwalifikowaną ścieżką zawierającą wyraz "hosty". Możesz również poszukać określonego pliku, zmieniając część ścieżki na jej w pełni kwalifikowaną postać, na przykład używając `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Gdy zapytanie zwróci żądane wyniki, kliknij pozycję **Nowa reguła alertu** w przeszukiwaniu dzienników, aby otworzyć stronę tworzenie alertów. Możesz również przejść do tej strony za pomocą **Azure monitor** w Azure Portal. 

4. Sprawdź ponownie zapytanie i zmodyfikuj logikę alertów. W takim przypadku alert ma być wyzwalany, jeśli istnieje nawet jedna zmiana została wykryta między wszystkimi maszynami w środowisku.

    ![Zmień, aby wykonać zapytanie dotyczące śledzenia zmian w pliku hosts](./media/change-tracking-file-contents/change-query.png)

5. Po ustawieniu logiki alertu Przypisz grupy akcji, aby wykonać akcje w odpowiedzi na wyzwolony alert. W takim przypadku konfigurujemy wiadomości e-mail do wysłania i zostanie utworzony bilet zarządzania usługami IT (narzędzia ITSM). 

    ![Konfigurowanie grupy akcji do alertu dotyczącego zmiany](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Następne kroki

* [Przegląd Change Tracking i spisu](change-tracking.md)
* [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md)
* [Dzienniki wyszukiwania w dziennikach Azure Monitor](../log-analytics/log-analytics-log-searches.md)