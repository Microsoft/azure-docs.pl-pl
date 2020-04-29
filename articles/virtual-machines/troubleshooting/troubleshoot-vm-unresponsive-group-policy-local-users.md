---
title: Maszyna wirtualna nie odpowiada podczas stosowania zasad "zasady grupy lokalnych użytkowników & grup"
description: W tym artykule przedstawiono kroki rozwiązywania problemów, w których ekran ładowania ma zablokowany sposób stosowania zasad podczas rozruchu w maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80620859"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>Maszyna wirtualna nie odpowiada podczas stosowania zasad "zasady grupy lokalnych użytkowników & grup"

W tym artykule przedstawiono kroki rozwiązywania problemów, w których ekran ładowania ma zablokowany zastosowanie zasad podczas rozruchu w maszynie wirtualnej platformy Azure.

## <a name="symptom"></a>Objaw

Gdy używasz [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) do wyświetlania zrzutu ekranu maszyny wirtualnej, zobaczysz, że ekran zablokuje ładowanie z komunikatem: *stosowanie zasady grupy lokalnych zasad grupy i użytkowników*.

![Tekst alternatywny: ekran pokazujący zastosowanie zasady grupy lokalnych użytkowników i grup (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Tekst alternatywny: ekran pokazujący zastosowanie zasady grupy lokalnych użytkowników i grup ładowanie zasad (system Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Przyczyna

Objawy tego zamrożenia są spowodowane przez defekt kodu w bibliotece dołączanej dynamicznie usługi profilu systemu Windows (*Profsvc. dll*).

> [!NOTE]
> Ta usterka dotyczy tylko systemów Windows Server 2012 i Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>Dane zasady

Stosowane zasady, które nie ukończyją swoich procesów, to:

* *Szablony biostartu komputera \ System/użytkownik Profiles\Delete profile użytkowników starsze niż określona liczba dni po ponownym uruchomieniu systemu*

Te zasady będą zawieszać się tylko wtedy, gdy spełnione są następujące sześć warunków:

* Wszystkie *Profile użytkowników starsze niż określona liczba dni w zasadach ponownego uruchamiania systemu* są włączone.
* Istnieją profile spełniające wymagania dotyczące wieku, które wymagają oczyszczenia.
* Masz składniki, które zostały zarejestrowane w celu usunięcia powiadomienia o profilach.
* Składniki wykonują wywołania (bezpośrednio lub pośrednio), które muszą pobierać dane ze składników menedżera kontroli usług (SCM) systemu Windows, takich jak uruchamianie, zatrzymywanie lub zapytanie informacji o usłudze.
* Usługa została skonfigurowana tak, aby była uruchamiana *automatycznie*.
* Ta usługa jest uruchamiana w kontekście konta domeny (w przeciwieństwie do użycia wbudowanego konta, takiego jak system lokalny).

### <a name="the-code-defect"></a>Wady kodu

Usterka kodu jest spowodowana przez menedżera kontroli usług (SCM) i usługi profilu próbujące zastosować blokady jednocześnie. Istnieją blokady, aby uniemożliwić wielu usługom wprowadzanie zmian w tych samych danych w tym samym czasie, co może spowodować uszkodzenie. Zwykle wielokrotne żądania blokowania nie powodowały problemu. Jednak ponieważ dzieje się tak w trakcie rozruchu, żadna usługa nie może zakończyć procesów, ponieważ zablokują się od siebie nawzajem.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>Usterka systemu operacyjnego 5880648 — zakleszczenia menedżera kontroli usług z zasadami "Usuń profile użytkowników przy ponownym uruchomieniu"

Istnieją dwie akcje, które nakładają się na siebie w taki sposób, że:

* Akcja 1 uzyskuje blokadę profilu, ale nie uzyskała jeszcze blokady SCM.

  **LUB**

* Akcja 2 uzyskuje blokadę SCM, ale nie uzyskała jeszcze blokady profilu.

Po wystąpieniu tego zakleszczenia próba uzyskania drugiej wymaganej blokady zaprzestaje działania.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Akcja 1 — powiadomienie o usunięciu starego profilu (ma **blokadę profilu**, wymaga **blokady SCM**)

1. Najpierw zestaw zasad służący do usuwania starych profilów uzyskuje wewnętrzną blokadę usługi profilu.

   * Ta blokada uniemożliwia dwóm wątkom współdziałanie z profilami, gdy *operacja usuwania* jest w toku.

2. Zasada znajdzie profile, które są wystarczająco stare, aby można je było usunąć.
3. W ramach usuwania profilu składnik, który został zarejestrowany do powiadomień o usunięciu profilu, próbuje **uruchomić usługę**.
4. Przed uruchomieniem usługi Menedżer kontroli usług (SCM) musi uzyskać **wewnętrzną blokadę SCM** przechowywaną przez wątki w **akcji 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Akcja 2 — ładowanie/Tworzenie profilu dla danych specyficznych dla użytkownika (ma **blokadę usługi SCM**, wymaga **blokady profilu**)

1. Podczas rozruchu menedżer SCM musi zamówić wszystkie usługi *autostartu* według swojej grupy, a także wszystkich usług, od których zależą te usługi.

2. **Menedżer SCM uzyskuje wewnętrzną blokadę SCM** służącą do kontrolowania dostępu do uruchamiania, zatrzymywania lub konfigurowania usług w miarę zamawiania usług.

3. Gdy usługi są w tej kolejności, menedżer SCM przeprowadzi pętlę przez poszczególne usługi i uruchomi ją.

4. Jeśli usługa jest uruchomiona w kontekście konta domeny, należy załadować lub utworzyć profil dla konta domeny, aby można było przechowywać dane specyficzne dla użytkownika.

5. To żądanie jest wysyłane do **usługi profilu**.

6. Usługa profilu musi mieć dostęp do **blokady wewnętrznej** pobranej w **akcji 1**.

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu

1. Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu
2. Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci
3. Kompiluj ponownie maszynę wirtualną
4. Zbierz plik zrzutu pamięci

   > [!NOTE]
   > W przypadku wystąpienia tego błędu rozruchu system operacyjny gościa nie działa. Aby rozwiązać ten problem, nastąpi Rozwiązywanie problemów w trybie offline.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj [kroki 1-3 poleceń naprawy maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) , aby przygotować maszynę wirtualną naprawy.
2. Korzystanie z Podłączanie pulpitu zdalnego łączenia się z maszyną wirtualną naprawy.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci

Aby włączyć Zbieranie zrzutów pamięci i konsolę seryjną, uruchom poniższy skrypt:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujące polecenia:

   * Włącz konsolę seryjną:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Sprawdź, czy ilość wolnego miejsca na dysku systemu operacyjnego jest taka sama jak rozmiar pamięci (RAM) na maszynie wirtualnej.

   * Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, należy zmienić lokalizację, w której zostanie utworzony plik zrzutu pamięci, i odwołać się do dowolnego dysku danych dołączonego do maszyny wirtualnej z wystarczającą ilością wolnego miejsca. Aby zmienić lokalizację, Zamień `%SystemRoot%` na literę dysku (na przykład "F:") dysku danych w poniższych poleceniach.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Sugerowana konfiguracja do włączenia zrzutu systemu operacyjnego

**Załaduj przerwany dysk systemu operacyjnego:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Włącz w ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Włącz w ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>Kompiluj ponownie maszynę wirtualną

Aby ponownie połączyć maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .

### <a name="collect-the-memory-dump-file"></a>Zbierz plik zrzutu pamięci

Aby rozwiązać ten problem, należy najpierw zebrać plik zrzutu pamięci dla awarii i skontaktować się z pomocą techniczną pliku zrzutu pamięci. Aby zebrać plik zrzutu, wykonaj następujące kroki:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Dołącz dysk systemu operacyjnego do nowej maszyny wirtualnej naprawy

1. Wykonaj kroki [1-3 poleceń naprawy maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) , aby przygotować nową maszynę wirtualną naprawy.

2. Korzystanie z Podłączanie pulpitu zdalnego łączenia się z maszyną wirtualną naprawy.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej

1. Na stronie napraw maszynę wirtualną przejdź do folderu systemu Windows na dołączonym dysku systemu operacyjnego. Jeśli litera sterownika, która jest przypisana do dołączonego dysku systemu operacyjnego, to F, należy przejść do F:\Windows.

2. Zlokalizuj plik Memory. dmp, a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przy użyciu pliku zrzutu pamięci.

3. Jeśli masz problemy z lokalizowaniem pliku Memory. dmp, możesz zamiast tego użyć [wywołań przerwań, które nie są maskowane (NMI) w konsoli szeregowej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Można postępować zgodnie z przewodnikiem, aby [wygenerować jądro lub kompletny plik zrzutu awaryjnego](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) za pomocą wywołań NMI.
