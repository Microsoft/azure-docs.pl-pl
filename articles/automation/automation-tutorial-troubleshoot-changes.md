---
title: Rozwiązywanie problemów dotyczących zmian na maszynie wirtualnej platformy Azure w Azure Automation | Microsoft Docs
description: W tym artykule opisano sposób rozwiązywania problemów ze zmianami w maszynie wirtualnej platformy Azure.
services: automation
ms.subservice: change-inventory-management
keywords: zmiany, śledzenie, śledzenie zmian, spis, Automatyzacja
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a223ac4296dd160bbdd904e1d3443552d4f49a23
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587941"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>Rozwiązywanie problemów dotyczących zmian na maszynie wirtualnej platformy Azure

W tym samouczku dowiesz się jak rozwiązywać problemy ze zmianami w maszynie wirtualnej platformy Azure. Dzięki włączeniu Change Tracking i spisu można śledzić zmiany w oprogramowaniu, plikach, demonach systemu Linux, usługach Windows i kluczach rejestru systemu Windows na komputerach.
Identyfikowanie tych zmian konfiguracji może pomóc w określeniu problemów z działaniem w swoim środowisku.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie Change Tracking i spisu dla maszyny wirtualnej
> * Wyszukiwanie dzienników zmian dla zatrzymanej usługi
> * Konfigurowanie śledzenia zmian
> * Włączanie połączenia dziennika aktywności
> * Wyzwalanie zdarzenia
> * Przeglądanie zmian
> * Konfigurowanie alertów

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](./index.yml) do przechowywania obserwatora i elementów Runbook akcji oraz zadania obserwatora.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md) , która ma zostać włączona dla tej funkcji.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

Najpierw należy włączyć Change Tracking i spis dla tego samouczka. Jeśli funkcja została wcześniej włączona, ten krok nie jest konieczny.

>[!NOTE]
>Jeśli pola są wyszarzone, dla maszyny wirtualnej jest włączona inna funkcja automatyzacji i musisz użyć tego samego obszaru roboczego i konta usługi Automation.

1. Wybierz pozycję **maszyny wirtualne** i wybierz maszynę wirtualną z listy.
2. Z menu po lewej stronie wybierz pozycję **spis** w obszarze **operacje**. Zostanie otwarta strona spis.

    ![Włącz zmianę](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. Wybierz obszar roboczy [log Analytics](../azure-monitor/logs/log-query-overview.md) . Ten obszar roboczy zbiera dane, które są generowane przez funkcje takie jak Change Tracking i spis. Obszar roboczy zawiera pojedynczą lokalizację do przeglądania i analizowania danych z wielu źródeł.

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. Wybierz konto usługi Automation, które ma być używane.

5. Skonfiguruj lokalizację wdrożenia.

5. Kliknij pozycję **Włącz** , aby wdrożyć funkcję dla maszyny wirtualnej. 

Podczas instalacji maszyna wirtualna jest obsługiwana za pomocą agenta Log Analytics dla systemu Windows i [hybrydowego procesu roboczego elementu Runbook](automation-hybrid-runbook-worker.md). Włączenie Change Tracking i spisu może potrwać do 15 minut. W tym czasie nie należy zamykać okna przeglądarki.

Po włączeniu tej funkcji informacje o zainstalowanym oprogramowaniu i zmianach na maszynach wirtualnych są przesyłane do dzienników Azure Monitor.
Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>Używanie Change Tracking i spisu w dziennikach Azure Monitor

Change Tracking i spis generuje dane dziennika, które są wysyłane do dzienników Azure Monitor. Aby wyszukać dzienniki przez uruchomienie zapytań, wybierz **log Analytics** w górnej części strony śledzenie zmian. Dane śledzenia zmian są przechowywane pod typem `ConfigurationChange` .

Poniższy przykład Log Analytics Query zwraca wszystkie usługi systemu Windows, które zostały zatrzymane.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Aby dowiedzieć się więcej na temat uruchamiania i wyszukiwania plików dziennika usługi Azure Monitor, zobacz [Dzienniki usługi Azure Monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="configure-change-tracking"></a>Konfigurowanie śledzenia zmian

Funkcja śledzenia zmian umożliwia wybranie plików i kluczy rejestru do zbierania i śledzenia przy użyciu opcji **Edytuj ustawienia** w górnej części strony śledzenie zmian na maszynie wirtualnej. Do śledzenia na stronie Konfiguracja obszaru roboczego można dodawać klucze rejestru systemu Windows, pliki systemu Windows lub pliki Linux.

> [!NOTE]
> Zarówno śledzenie zmian, jak i spis używają tych samych ustawień kolekcji, a ustawienia są konfigurowane na poziomie obszaru roboczego.

### <a name="add-a-windows-registry-key"></a>Dodaj klucz rejestru systemu Windows

1. Na karcie **Rejestr systemu Windows** wybierz opcję **Dodaj**. 

1. Na stronie Dodaj rejestr systemu Windows dla Change Tracking wprowadź informacje dotyczące klucza do śledzenia i kliknij przycisk **Zapisz** .

    |Właściwość  |Opis  |
    |---------|---------|
    |Enabled (Włączony)     | Określa, czy ustawienie jest stosowane        |
    |Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony        |
    |Group (Grupa)     | Nazwa grupy do logicznego grupowania plików        |
    |Klucz rejestru systemu Windows   | Ścieżka do sprawdzania pliku, na przykład: „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="add-a-windows-file"></a>Dodawanie pliku systemu Windows

1. Na karcie **Pliki systemu Windows** wybierz opcję **Dodaj**. 

1. Na stronie Dodawanie pliku systemu Windows dla Change Tracking wprowadź informacje dotyczące pliku lub katalogu, który ma być śledzony, i kliknij przycisk **Zapisz** .

    |Właściwość  |Opis  |
    |---------|---------|
    |Enabled (Włączony)     | Określa, czy ustawienie jest stosowane        |
    |Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony        |
    |Group (Grupa)     | Nazwa grupy do logicznego grupowania plików        |
    |Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład: „c:\temp\\\*.txt”<br>Możesz użyć również zmiennych środowiskowych, takich jak „%winDir%\System32\\\*.*”         |
    |Rekursja     | Określa, czy podczas wyszukiwania elementu, który ma być śledzony, ma być używana rekursja.        |
    |Przekaż zawartość pliku dla wszystkich ustawień| Włącza lub wyłącza przekazywanie zawartości pliku dla śledzonych zmian. Dostępne opcje: **True** lub **False**.|

### <a name="add-a-linux-file"></a>Dodawanie pliku systemu Linux

1. Na karcie **Pliki systemu Linux** wybierz opcję **Dodaj**. 

1. Na stronie Dodawanie pliku systemu Linux dla Change Tracking wprowadź informacje dotyczące pliku lub katalogu, który ma być śledzony, i kliknij przycisk **Zapisz**.

    |Właściwość  |Opis  |
    |---------|---------|
    |Enabled (Włączony)     | Określa, czy ustawienie jest stosowane        |
    |Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony        |
    |Group (Grupa)     | Nazwa grupy do logicznego grupowania plików        |
    |Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład: „/etc/*.conf”       |
    |Typ ścieżki     | Typ elementu, który ma być monitorowany; możliwe wartości to Plik i Katalog        |
    |Rekursja     | Określa, czy podczas wyszukiwania elementu, który ma być śledzony, ma być używana rekursja.        |
    |Użyj polecenia Sudo     | To ustawienie określa, czy podczas sprawdzania elementu jest używane polecenie sudo.         |
    |Linki     | To ustawienie określa, w jaki sposób są obsługiwane linki symboliczne podczas przechodzenia między katalogami.<br> **Ignoruj** — ignoruje linki symboliczne i nie uwzględnia plików/katalogów, do których się odwołują<br>**Śledź** — śledzi linki symboliczne podczas rekursji i uwzględnia również pliki/katalogi, do których się odwołują<br>**Zarządzaj** — śledzi linki symboliczne i umożliwia obsługę zwracanej zawartości      |
    |Przekaż zawartość pliku dla wszystkich ustawień| Włącza lub wyłącza przekazywanie zawartości pliku dla śledzonych zmian. Dostępne opcje: True lub False.|

   > [!NOTE]
   > Nie zaleca się **zarządzania** wartością właściwości **łącza** . Pobieranie zawartości plików nie jest obsługiwane.

## <a name="enable-activity-log-connection"></a>Włączanie połączenia dziennika aktywności

1. Ze strony Śledzenie zmian na swojej maszynie wirtualnej wybierz pozycję **Zarządzanie połączeniem dziennika aktywności**. 

2. Na stronie dziennik aktywności platformy Azure kliknij pozycję **Połącz** , aby połączyć Change Tracking i spis do dziennika aktywności platformy Azure dla maszyny wirtualnej.

3. Przejdź do strony przegląd dla swojej maszyny wirtualnej i wybierz pozycję **Zatrzymaj** , aby zatrzymać maszynę wirtualną. 

4. Po wyświetleniu monitu wybierz pozycję **Tak**, aby zatrzymać maszynę wirtualną. 

5. Po cofnięciu przydziału maszyny wirtualnej wybierz pozycję **Rozpocznij** , aby uruchomić ją ponownie. Zatrzymywanie i uruchamianie maszyny wirtualnej rejestruje zdarzenie w dzienniku aktywności. 

## <a name="view-changes"></a>Przeglądanie zmian

1. Przejdź z powrotem do strony śledzenie zmian i wybierz kartę **zdarzenia** w dolnej części strony. 

2. Po czasie zdarzenia śledzenia zmian są wyświetlane na wykresie i w tabeli. Wykres pokazuje zmiany, które wystąpiły w czasie. Wykres liniowy u góry Wyświetla zdarzenia dziennika aktywności platformy Azure. Każdy wiersz wykresów słupkowych reprezentuje inny typ zmiany do śledzenia. Te typy to demony, pliki, klucze rejestru systemu Windows, oprogramowanie i usługi systemu Windows. Karta zmiana zawiera szczegółowe informacje o wyświetlanych zmianach w pierwszej kolejności.

    ![Wyświetlanie zdarzeń w portalu](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. Należy zauważyć, że wprowadzono wiele zmian w systemie, w tym zmiany w usługach i oprogramowaniu. Możesz użyć filtrów u góry strony, aby przefiltrować wyniki według **Typu zmiany** lub zakresu czasu.

    ![Lista zmian w maszynie wirtualnej](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. Wybierz **WindowsServices** zmianę. Wybranie tej opcji spowoduje otwarcie strony Zmień szczegóły zawierającej szczegóły dotyczące zmiany oraz wartości przed zmianą i po niej. W tym wystąpieniu zatrzymano usługę ochrony oprogramowania.

    ![Wyświetlanie szczegółów zmiany w portalu](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Konfigurowanie alertów

Wyświetlanie zmian wprowadzonych w witrynie Azure Portal może być przydatne, ale bardziej korzystna jest możliwość otrzymywania alertów po wystąpieniu zmiany (np. po zatrzymaniu usługi). Dodajmy alert dotyczący zatrzymanej usługi. 

1. W Azure Portal przejdź do pozycji **monitorowanie**. 

2. Wybierz pozycję **alerty** w obszarze **usługi udostępnione**, a następnie kliknij pozycję **+ Nowa reguła alertu**.

3. Kliknij polecenie **Wybierz**, aby wybrać zasób. 

4. Na stronie wybierz zasób wybierz **log Analytics** z menu rozwijanego **Filtruj według typu zasobu** . 

5. Wybierz obszar roboczy Log Analytics, a następnie kliknij przycisk **gotowe**.

    ![Wybieranie zasobu](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. Kliknij pozycję **Dodaj warunek**.

7. W tabeli na stronie Konfiguruj logikę sygnałów wybierz opcję **Wyszukiwanie w dzienniku niestandardowym**. 

8. W polu tekstowym wyszukiwanie kwerendy wprowadź następujące zapytanie:

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    To zapytanie zwraca informacje o komputerach, na których w określonym przedziale czasu została zatrzymana usługa W3SVC.

9. W polu **próg** w obszarze **logika alertu** wprowadź **wartość 0**. Po zakończeniu kliknij przycisk **gotowe**.

    ![Konfigurowanie logiki sygnału](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. Wybierz pozycję **Utwórz nowy** w obszarze **grupy akcji**. Grupa akcji to grupa składająca się z akcji, których można używać w wielu alertach. Akcje mogą obejmować powiadomienia e-mail, elementy runbook i webhook oraz wiele innych. Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/alerts/action-groups.md).

11. W obszarze **Szczegóły alertu** wprowadź nazwę i opis alertu. 

12. Ustaw **Ważność** na **Informacyjny (ważność 2)**, **Ostrzegawczy (ważność 1)** lub **Krytyczny (ważność 0)**.

13. W polu **Nazwa grupy akcji** wprowadź nazwę alertu oraz krótką nazwę. Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

14. W polu **Akcje** wprowadź nazwę akcji, na przykład **administratorów poczty e-mail**. 

15. W obszarze **Typ akcji** wybierz pozycję **poczta e-mail/SMS/wypychanie/głos**. 

16. Aby uzyskać **szczegółowe informacje**, wybierz pozycję **Edytuj szczegóły**.

    ![Dodawanie grupy akcji](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

17. W okienku wiadomości E-mail/SMS/wypychanie/głos wprowadź nazwę, zaznacz pole wyboru **adres e-mail** , a następnie wprowadź prawidłowy adres e-mail. Po zakończeniu kliknij przycisk **OK** w okienku, a następnie kliknij przycisk **OK** na stronie Dodaj grupę akcji.

18. Aby dostosować temat wiadomości e-mail dotyczącej alertu, wybierz pozycję **Dostosuj akcje**. 

19. W obszarze **Utwórz regułę** wybierz pozycję **temat wiadomości e-mail**, a następnie wybierz pozycję **Utwórz regułę alertu**. Alert informuje użytkownika o pomyślnym wdrożeniu aktualizacji oraz o maszynach będących elementami danego uruchomienia wdrożenia aktualizacji. Poniższa ilustracja przedstawia przykład wiadomości e-mail otrzymanej po zatrzymaniu usługi W3SVC.

    ![Przechwytywanie ekranu pokazuje powiadomienie e-mail odebrane, gdy usługi W wersji 3 S V są zatrzymane.](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie Change Tracking i spisu dla maszyny wirtualnej
> * Wyszukiwanie dzienników zmian dla zatrzymanej usługi
> * Konfigurowanie śledzenia zmian
> * Włącz połączenie dziennika aktywności
> * Wyzwalanie zdarzenia
> * Przeglądanie zmian
> * Konfigurowanie alertów

Przejdź do omówienia funkcji Change Tracking i spisu, aby dowiedzieć się więcej na jej temat.

> [!div class="nextstepaction"]
> [Przegląd Change Tracking i spisu](change-tracking/overview.md)
