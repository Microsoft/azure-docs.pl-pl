---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77123221"
---
Agent Azure File Sync jest regularnie aktualizowany, aby można było dodać nowe funkcje i rozwiązać problemy. Zalecamy skonfigurowanie Microsoft Update do pobierania aktualizacji dla agenta Azure File Sync, gdy są one dostępne.

#### <a name="major-vs-minor-agent-versions"></a>Wersje agenta głównego a pomocniczego
* Wersje głównych agentów często zawierają nowe funkcje i mają rosnącą liczbę jako pierwszą część numeru wersji. Na przykład: \* 2. \* .\*\*
* Wersje agentów pomocniczych są również nazywane "poprawkami" i są wydawane częściej niż wersje główne. Często zawierają poprawki błędów i mniejsze ulepszenia, ale nie nowe funkcje. Na przykład: \* \* . 3.\*\*

#### <a name="upgrade-paths"></a>Ścieżki uaktualniania
Istnieją cztery zatwierdzone i przetestowane sposoby instalowania aktualizacji agenta Azure File Sync. 
1. **Wybiera Skonfiguruj Microsoft Update, aby automatycznie pobierać i instalować aktualizacje agentów.**  
    Zawsze zalecamy pobranie każdej Azure File Sync aktualizacji, aby upewnić się, że masz dostęp do najnowszych poprawek dla agenta serwera. Microsoft Update ten proces bezproblemowo, pobierając i instalując aktualizacje.
2. **Użyj AfsUpdater.exe, aby pobrać i zainstalować aktualizacje agentów.**  
    AfsUpdater.exe znajduje się w katalogu instalacji agenta. Kliknij dwukrotnie plik wykonywalny, aby pobrać i zainstalować aktualizacje agenta. 
3. **Poprawka istniejącego agenta Azure File Sync przy użyciu pliku poprawki Microsoft Update lub plik wykonywalny msp. Najnowszy pakiet aktualizacji Azure File Sync można pobrać z [katalogu Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Uruchomienie pliku wykonywalnego. msp spowoduje uaktualnienie instalacji Azure File Sync przy użyciu tej samej metody, która została użyta automatycznie przez Microsoft Update w poprzedniej ścieżce uaktualnienia. Zastosowanie poprawki Microsoft Update spowoduje przeprowadzenie uaktualnienia w miejscu Azure File Sync instalacji.
4. **Pobierz najnowszą wersję Instalatora agenta Azure File Sync z [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Aby uaktualnić istniejącą instalację agenta Azure File Sync, Odinstaluj starszą wersję, a następnie zainstaluj najnowszą wersję z pobranego instalatora. Rejestracja serwera, grupy synchronizacji i inne ustawienia są obsługiwane przez Instalatora Azure File Sync.

#### <a name="automatic-agent-lifecycle-management"></a>Automatyczne zarządzanie cyklem życia agenta
W przypadku agenta w wersji 6 zespół synchronizacji plików wprowadził funkcję autouaktualnienia agenta. Można wybrać jeden z dwóch trybów i określić okno obsługi, w którym ma zostać podjęta próba uaktualnienia na serwerze programu. Ta funkcja została zaprojektowana tak, aby pomóc w zarządzaniu cyklem życia agenta przez udostępnienie Guardrail zapobiegające wygaśnięciu lub umożliwieniu bieżącego ustawienia.
1. **Ustawienie domyślne** podejmie próbę zapobiegania wygaśnięciu agenta. W ciągu 21 dni od opublikowania daty wygaśnięcia agenta Agent podejmie próbę samouaktualnienia. Rozpocznie się próba uaktualnienia raz w tygodniu w ciągu 21 dni przed wygaśnięciem i w wybranym oknie obsługi. **Ta opcja nie eliminuje potrzeby podejmowania zwykłych Microsoft Update poprawek.**
1. Opcjonalnie możesz wybrać, że agent będzie automatycznie uaktualniany, gdy tylko zostanie udostępniona nowa wersja agenta (obecnie nie dotyczy to serwerów klastrowanych). Ta aktualizacja będzie miała miejsce w wybranym oknie obsługi i pozwala serwerowi korzystać z nowych funkcji i ulepszeń, gdy tylko staną się ogólnie dostępne. Jest to zalecane, nieodpłatne ustawienie, które zapewni główne wersje agenta, a także regularne aktualizacje poprawek do serwera. Każdy wydawany Agent jest w dobrej jakości. Jeśli wybierzesz tę opcję, firma Microsoft będzie przeprowadził inspekcję najnowszej wersji agenta. Serwery klastrowane są wykluczone. Po zakończeniu lotu Agent również stanie się dostępny w [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?linkid=858257) aka.MS/AFS/Agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Zmiana ustawienia autouaktualniania

W poniższych instrukcjach opisano, jak zmienić ustawienia po zakończeniu instalacji, jeśli trzeba wprowadzić zmiany.

Otwórz konsolę programu PowerShell i przejdź do katalogu, w którym zainstalowano agenta synchronizacji, a następnie zaimportuj polecenia cmdlet serwera. Domyślnie będzie to wyglądać następująco:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

Można uruchomić, `Get-StorageSyncAgentAutoUpdatePolicy` Aby sprawdzić bieżące ustawienie zasad i określić, czy chcesz je zmienić.

Aby zmienić bieżące ustawienie zasad na ścieżkę opóźnionej aktualizacji, można użyć:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Aby zmienić bieżące ustawienie zasad na ścieżkę natychmiastowej aktualizacji, można użyć:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Gwarancje dotyczące cyklu życia agenta i zarządzania zmianami
Azure File Sync to usługa w chmurze, która ciągle wprowadza nowe funkcje i ulepszenia. Oznacza to, że określona wersja agenta Azure File Sync może być obsługiwana tylko przez ograniczony czas. Aby ułatwić wdrożenie, następujące reguły gwarantują, że masz wystarczającą ilość czasu i powiadomienia, aby uwzględnić aktualizacje/uaktualnienia agenta w procesie zarządzania zmianami:

- Wersje głównych agentów są obsługiwane przez co najmniej sześć miesięcy od dnia wydania wersji początkowej.
- Firma Microsoft gwarantuje, że istnieje nakładanie się co najmniej przez trzy miesiące między wsparciem dla głównych wersji agentów. 
- Ostrzeżenia są wydawane dla zarejestrowanych serwerów za pomocą wkrótce wygasłego agenta co najmniej trzy miesiące przed wygaśnięciem. Możesz sprawdzić, czy serwer zarejestrowany korzysta ze starszej wersji agenta w sekcji zarejestrowane serwery usługi synchronizacji magazynu.
- Okres istnienia pomocniczej wersji agenta jest powiązany z skojarzoną wersją główną. Na przykład po udostępnieniu agenta w wersji 3,0 Agent wersji 2. \* wszystkie zostaną skonfigurowane do wygasania razem.

> [!Note]
> Zainstalowanie wersji agenta z ostrzeżeniem o wygaśnięciu spowoduje wyświetlenie ostrzeżenia, ale powiedzie się. Próba zainstalowania lub połączenia z wygasłą wersją agenta nie jest obsługiwana i zostanie zablokowana.
