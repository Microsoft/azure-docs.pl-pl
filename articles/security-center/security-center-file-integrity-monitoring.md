---
title: Monitorowanie integralności plików w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak skonfigurować monitorowanie integralności plików (FIM) w Azure Security Center przy użyciu tego przewodnika.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b64ff51836f8d291acf57b1cd9ca100c4f87ebed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541173"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitorowanie integralności plików w Azure Security Center
Dowiedz się, jak skonfigurować monitorowanie integralności plików (FIM) w Azure Security Center przy użyciu tego przewodnika.


## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|Wymaga [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md)|
|Wymagane role i uprawnienia:|**Właściciel obszaru roboczego** może włączyć/wyłączyć program FIM (Aby uzyskać więcej informacji, zobacz [role platformy Azure dla log Analytics](https://docs.microsoft.com/services-hub/health/azure-roles#azure-roles)).<br>**Czytelnik** może wyświetlać wyniki.|
|Połączeń|![Tak ](./media/icons/yes-icon.png) chmury komercyjne<br>![Tak ](./media/icons/yes-icon.png) US gov<br>![Brak ](./media/icons/no-icon.png) Chin gov, inne gov<br>Obsługiwane tylko w regionach, w których jest dostępne Azure Automation rozwiązanie do śledzenia zmian.<br>Zobacz sekcję [Obsługiwane regiony dla połączonego obszaru roboczego log Analytics](../automation/how-to/region-mappings.md).<br>[Dowiedz się więcej o śledzeniu zmian](../automation/change-tracking.md) |
|||





## <a name="what-is-fim-in-security-center"></a>Co to jest FIM w Security Center?
Monitorowanie integralności plików (FIM), znane także jako monitorowanie zmian, bada pliki i rejestry systemu operacyjnego, oprogramowania aplikacji i innych osób, które mogą wskazywać na atak. Metoda porównania służy do określenia, czy bieżący stan pliku różni się od ostatniego skanowania pliku. Możesz użyć tego porównania, aby określić, czy w plikach wprowadzono prawidłowe lub podejrzane modyfikacje.

Monitorowanie integralności plików Security Center sprawdza integralność plików systemu Windows, rejestru systemu Windows i plików Linux. Należy wybrać pliki, które mają być monitorowane, włączając program FIM. Security Center monitoruje pliki z włączoną obsługą programu FIM dla działania takiego jak:

- Tworzenie i usuwanie plików i rejestrów
- Modyfikacje plików (zmiany rozmiaru pliku, list kontroli dostępu i wartości skrótu zawartości)
- Modyfikacje rejestru (zmiany rozmiaru, list kontroli dostępu, typu i zawartości)

Security Center zalecamy monitorowanie jednostek, które można łatwo włączyć w programie FIM. Można także definiować własne zasady lub jednostki programu FIM do monitorowania. W tym instruktażu pokazano, jak to zrobić.

> [!NOTE]
> Funkcja monitorowania integralności plików działa w przypadku komputerów z systemem Windows i Linux oraz maszyn wirtualnych i jest dostępna tylko wtedy, gdy jest włączona **usługa Azure Defender dla serwerów** . Aby dowiedzieć się więcej, zobacz [Cennik](security-center-pricing.md) . KOD FIM przekazuje dane do obszaru roboczego Log Analytics. Opłaty za dane są stosowane w oparciu o ilość przekazywanych danych. Aby dowiedzieć się więcej, zobacz [cennik log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) .

Usługa FIM używa rozwiązania Change Tracking platformy Azure do śledzenia i identyfikowania zmian w środowisku. Po włączeniu monitorowania integralności plików istnieje zasób **Change Tracking** typu **rozwiązanie**. Aby uzyskać szczegółowe informacje o częstotliwości zbierania danych, zobacz [Szczegółowe informacje dotyczące zbierania danych przez usługę Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) dla usługi Azure Change Tracking.

> [!NOTE]
> W przypadku usunięcia zasobu **Change Tracking** należy również wyłączyć funkcję monitorowania integralności plików w programie Security Center.

## <a name="which-files-should-i-monitor"></a>Które pliki należy monitorować?
W przypadku wybrania plików do monitorowania należy wziąć pod uwagę informacje o plikach, które są krytyczne dla systemu i aplikacji. Rozważ wybranie plików, których nie można zmienić bez planowania. Wybór plików, które są często zmieniane przez aplikacje lub system operacyjny (na przykład pliki dziennika i pliki tekstowe), tworzy wiele szumów, co utrudnia zidentyfikowanie ataku.

Security Center zawiera poniższą listę zalecanych elementów do monitorowania na podstawie znanych wzorców ataków. Obejmują one pliki i klucze rejestru systemu Windows. Wszystkie klucze są w obszarze HKEY_LOCAL_MACHINE ("HKLM" w tabeli).

|**Pliki systemu Linux**|**Pliki systemu Windows**|**Klucze rejestru systemu Windows**|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*. conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini \Boot.|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin.|C:\Windows\regedit.exe|Foldery HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell|
|/sbin|C:\Windows\System32\userinit.exe|Foldery powłoki HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User|
|/ Boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program Files\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini \Boot.|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||Foldery HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell|
|||Foldery powłoki HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>Włącz monitorowanie integralności plików 

1. W obszarze **zaawansowanej ochrony** pulpitu nawigacyjnego **usługi Azure Defender** wybierz pozycję **monitorowanie integralności plików**.

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="Uruchamianie programu FIM" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    Zostanie otwarta strona Konfiguracja **monitorowania integralności plików** .

    Dla każdego obszaru roboczego są dostępne następujące informacje:

    - Łączna liczba zmian, które wystąpiły w ostatnim tygodniu (w obszarze roboczym nie jest włączona funkcja FIM)
    - Łączna liczba komputerów i maszyn wirtualnych raportowanych do obszaru roboczego
    - Lokalizacja geograficzna obszaru roboczego
    - Subskrypcja platformy Azure, w której znajduje się obszar roboczy

1. Ta strona służy do:

    - Dostęp i wyświetlanie stanu i ustawień każdego obszaru roboczego

    - ![Ikona planu uaktualniania ][4] umożliwia uaktualnienie obszaru roboczego do korzystania z usługi Azure Defender. Ta ikona wskazuje, że obszar roboczy lub subskrypcja nie są chronione przez usługę Azure Defender. Aby można było korzystać z funkcji programu FIM, Twoja subskrypcja musi być chroniona przez usługę Azure Defender. [Dowiedz się więcej](security-center-pricing.md).

    - ![Ikona włączania][3] Włącz program FIM na wszystkich komputerach w obszarze roboczym i skonfiguruj opcje programu FIM. Ta ikona wskazuje, że w obszarze roboczym nie jest włączona funkcja FIM.

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="Uruchamianie programu FIM":::


    > [!TIP]
    > Jeśli nie ma przycisku włączania lub uaktualniania, a spacja jest pusta, oznacza to, że program FIM jest już włączony w obszarze roboczym.


1. Wybierz pozycję **Włącz**. Pokazane są szczegóły obszaru roboczego, w tym liczba maszyn z systemami Windows i Linux w obszarze roboczym.

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="Uruchamianie programu FIM":::

   Wyświetlane są również zalecane ustawienia dla systemów Windows i Linux.  Rozwiń pozycje **pliki systemu Windows**, **Rejestr**i **Linux** , aby wyświetlić pełną listę zalecanych elementów.

1. Wyczyść pola wyboru dla wszystkich zalecanych jednostek, które nie mają być monitorowane przez program FIM.

1. Wybierz pozycję **Zastosuj monitorowanie integralności plików** , aby włączyć program FIM.

> [!NOTE]
> Ustawienia można zmienić w dowolnym momencie. Aby dowiedzieć się więcej, zobacz [Edytowanie monitorowanych jednostek](#edit-monitored-entities) poniżej.



## <a name="audit-monitored-workspaces"></a>Inspekcja monitorowanych obszarów roboczych 

Zostanie wyświetlony pulpit nawigacyjny **monitorowanie integralności plików** dla obszarów roboczych, w których jest włączony kod FIM. Pulpit nawigacyjny usługi FIM zostanie otwarty po włączeniu programu FIM w obszarze roboczym lub po wybraniu obszaru roboczego w oknie **monitorowanie integralności plików** , w którym już włączono funkcję FIM.

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="Uruchamianie programu FIM":::

Pulpit nawigacyjny programu FIM dla obszaru roboczego zawiera następujące szczegóły:

- Łączna liczba maszyn podłączonych do obszaru roboczego
- Łączna liczba zmian, które wystąpiły w wybranym okresie
- Podział typu zmiany (pliki, rejestr)
- Podział kategorii zmiany (zmodyfikowano, dodano, usunięto)

Wybierz opcję **Filtr** w górnej części pulpitu nawigacyjnego, aby zmienić okres, w którym będą wyświetlane zmiany.

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="Uruchamianie programu FIM":::

Na karcie **serwery** znajduje się lista komputerów raportowanych do tego obszaru roboczego. Na każdej maszynie pulpit nawigacyjny zawiera listę:

- Łączna liczba zmian, które wystąpiły w wybranym okresie
- Podział łącznej liczby zmian w plikach lub zmianach rejestru

**Wyszukiwanie w dzienniku** jest otwierane po wprowadzeniu nazwy komputera w polu wyszukiwania lub wybraniu maszyny na liście na karcie komputery. Wyszukiwanie w dzienniku zawiera wszystkie zmiany wprowadzone w wybranym okresie dla maszyny. Aby uzyskać więcej informacji, możesz rozwinąć zmianę.

![Przeszukiwanie dzienników][8]

Na karcie **zmiany** (pokazane poniżej) są wyświetlane wszystkie zmiany w obszarze roboczym w wybranym okresie. Dla każdej jednostki, która została zmieniona, pulpit nawigacyjny wyświetla listę:

- Komputer, na którym nastąpiła zmiana
- Typ zmiany (rejestr lub plik)
- Kategoria zmiany (zmodyfikowano, dodano, usunięto)
- Data i godzina zmiany

![Zmiany w obszarze roboczym][9]

**Szczegóły zmiany** są otwierane po wprowadzeniu zmiany w polu wyszukiwania lub wybraniu jednostki na karcie **zmiany** .

![Szczegóły zmiany][10]

## <a name="edit-monitored-entities"></a>Edytuj monitorowane jednostki

1. Wróć do **pulpitu nawigacyjnego monitorowania integralności plików** i wybierz pozycję **Ustawienia**.

   ![Ustawienia][11]

   Zostanie otwarta **Konfiguracja obszaru roboczego** wyświetlająca trzy karty: **Rejestr systemu Windows**, **pliki systemu Windows**i **pliki Linux**. Każda karta zawiera listę jednostek, które można edytować w tej kategorii. Dla każdej jednostki wymienionej Security Center określa, czy program FIM jest włączony (true), czy nie jest włączony (false).  Edytowanie jednostki umożliwia włączenie lub wyłączenie programu FIM.

   ![Konfiguracja obszaru roboczego][12]

2. Wybierz opcję Ochrona tożsamości. W tym przykładzie wybrano element w rejestrze systemu Windows. Zostanie otwarte okno **Edycja dla Change Tracking** .

   ![Edytuj lub Zmień śledzenie][13]

W obszarze **Edytuj dla Change Tracking** można:

- Włącz (true) lub Wyłącz (false) monitorowanie integralności plików
- Podaj lub Zmień nazwę jednostki
- Podaj lub zmień wartość lub ścieżkę
- Usuń jednostkę, Odrzuć zmianę lub Zapisz zmianę

## <a name="add-a-new-entity-to-monitor"></a>Dodaj nową jednostkę do monitorowania
1. Wróć do **pulpitu nawigacyjnego monitorowania integralności plików** i wybierz pozycję **Ustawienia** w górnej części ekranu. Zostanie otwarta **Konfiguracja obszaru roboczego** .
2. W obszarze **Konfiguracja obszaru roboczego**wybierz kartę dla typu jednostki, którą chcesz dodać: Rejestr systemu Windows, pliki systemu Windows lub pliki z systemem Linux. W tym przykładzie wybrano **pliki systemu Linux**.

   ![Dodaj nowy element do monitorowania][14]

3. Wybierz pozycję **Dodaj**. Zostanie otwarte okno **Dodawanie do Change Tracking** .

   ![Wprowadź żądane informacje][15]

4. Na stronie **Dodaj** wpisz żądane informacje i wybierz pozycję **Zapisz**.

## <a name="disable-monitored-entities"></a>Wyłącz monitorowane jednostki
1. Wróć do pulpitu nawigacyjnego **monitorowania integralności plików** .
2. Wybierz obszar roboczy, w którym jest obecnie włączony kod FIM. Obszar roboczy jest włączony dla programu FIM, jeśli brakuje przycisku Włącz lub planu uaktualniania.

   ![Wybierz obszar roboczy, w którym jest włączony kod FIM][16]

3. W obszarze monitorowanie integralności plików wybierz pozycję **Ustawienia**.

   ![Wybieranie pozycji Ustawienia][17]

4. W obszarze **Konfiguracja obszaru roboczego**wybierz grupę, w której opcja **Enabled** jest ustawiona na wartość true.

   ![Konfiguracja obszaru roboczego][18]

5. W obszarze **Edycja dla Change Tracking** okna, dla którego **włączono** wartość false.

   ![Ustawiony na wartość false][19]

6. Wybierz pozycję **Zapisz**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Monitorowanie folderów i ścieżek przy użyciu symboli wieloznacznych

Użyj symboli wieloznacznych, aby uprościć śledzenie między katalogami. Podczas konfigurowania monitorowania folderów przy użyciu symboli wieloznacznych stosowane są następujące reguły:
-   Do śledzenia wielu plików wymagane są symbole wieloznaczne.
-   Symboli wieloznacznych można używać tylko w ostatnim segmencie ścieżki, na przykład C:\folder\file lub/etc/*. conf
-   Jeśli zmienna środowiskowa zawiera nieprawidłową ścieżkę, walidacja powiedzie się, ale ścieżka zakończy się niepowodzeniem po uruchomieniu spisu.
-   Podczas ustawiania ścieżki należy unikać ogólnych ścieżek, takich jak c: \* . *, co spowoduje, że przechodzą zbyt wiele folderów.

## <a name="disable-fim"></a>Wyłącz FIM
Możesz wyłączyć funkcję FIM. Usługa FIM używa rozwiązania Change Tracking platformy Azure do śledzenia i identyfikowania zmian w środowisku. Wyłączenie programu FIM powoduje usunięcie rozwiązania Change Tracking z wybranego obszaru roboczego.

1. Aby wyłączyć program FIM, Wróć do pulpitu nawigacyjnego **monitorowania integralności plików** .
2. Wybierz obszar roboczy.
3. W obszarze **monitorowanie integralności plików**wybierz pozycję **Wyłącz**.

   ![Wyłącz FIM][20]

4. Wybierz pozycję **Usuń** , aby wyłączyć.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób korzystania z monitorowania integralności plików (FIM) w programie Security Center. Aby dowiedzieć się więcej na temat Security Center, zobacz następujące strony:

* [Ustawianie zasad zabezpieczeń](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
* [Zarządzanie zaleceniami](security-center-recommendations.md) dotyczącymi zabezpieczeń — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
* [Blog Azure Security](https://docs.microsoft.com/archive/blogs/azuresecurity/) — najnowsze informacje na temat zabezpieczeń platformy Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png