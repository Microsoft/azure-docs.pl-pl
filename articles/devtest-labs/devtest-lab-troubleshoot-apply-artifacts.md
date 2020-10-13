---
title: Rozwiązywanie problemów z artefaktami w Azure DevTest Labs | Microsoft Docs
description: Informacje o rozwiązywaniu problemów występujących podczas stosowania artefaktów w Azure DevTest Labs maszynie wirtualnej.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a89b675a1b3bf134b98e09c7278f0eccb594c325
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483197"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Rozwiązywanie problemów występujących podczas stosowania artefaktów w Azure DevTest Labs maszynie wirtualnej
Stosowanie artefaktów na maszynie wirtualnej może zakończyć się niepowodzeniem z różnych powodów. Ten artykuł przeprowadzi Cię przez niektóre metody, aby ułatwić identyfikację możliwych przyczyn.

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami Azure DevTest Labs (DTL) na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną.   

> [!NOTE]
> Ten artykuł dotyczy maszyn wirtualnych z systemem Windows i innym niż Windows. Chociaż istnieją pewne różnice, zostaną one wywołane jawnie w tym artykule.

## <a name="quick-troubleshooting-steps"></a>Szybkie rozwiązywanie problemów
Sprawdź, czy maszyna wirtualna jest uruchomiona. DevTest Labs wymaga, aby maszyna wirtualna była uruchomiona i że [Agent maszyny wirtualnej Microsoft Azure (Agent VM)](../virtual-machines/extensions/agent-windows.md) jest zainstalowany i gotowy.

> [!TIP]
> W **Azure Portal**przejdź do strony **Zarządzanie ARTEFAKTAMI** dla maszyny wirtualnej, aby sprawdzić, czy maszyna wirtualna jest gotowa do zastosowania artefaktów. Zobaczysz komunikat w bardzo górnej części tej strony. 
> 
> Korzystając z **Azure PowerShell**, należy sprawdzić flagę **canApplyArtifacts**, która jest zwracana tylko po rozwinięciu operacji get. Zobacz następujące przykładowe polecenie:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Sposoby rozwiązywania problemów 
Możesz rozwiązywać problemy z maszynami wirtualnymi utworzonymi za pomocą DevTest Labs i modelu wdrażania Menedżer zasobów przy użyciu jednej z następujących metod:

- **Azure Portal** — doskonały, jeśli chcesz szybko uzyskać wskazówkę wizualną, co może być przyczyną problemu.
- **Azure PowerShell** — Jeśli masz doświadczenie z wierszem programu PowerShell, szybko Zbadaj zasoby DevTest Labs przy użyciu Azure PowerShell poleceń cmdlet.

> [!TIP]
> Aby uzyskać więcej informacji na temat sposobu przeglądania wykonania artefaktu w ramach maszyny wirtualnej, zobacz [Diagnozowanie błędów artefaktów w laboratorium](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="symptoms-causes-and-potential-resolutions"></a>Objawy, przyczyny i potencjalne rozwiązania 

### <a name="artifact-appears-to-stop-responding"></a>Artefakt wydaje się przestać odpowiadać

Artefakt wydaje się przestać odpowiadać do momentu wygaśnięcia wstępnie zdefiniowanego okresu limitu czasu i artefakt zostanie oznaczony jako **Niepowodzenie**.

Gdy artefakt zostanie rozsunięty, należy najpierw określić, gdzie jest zablokowany. Artefakt można zablokować podczas wykonywania w dowolnym z następujących kroków:

- **Podczas pierwszego żądania**. DevTest Labs tworzy szablon Azure Resource Manager, aby zażądać użycia rozszerzenia skryptu niestandardowego (rozszerzenie). W związku z tym w tle zostanie wyzwolone wdrożenie grupy zasobów. Gdy wystąpi błąd na tym poziomie, uzyskasz szczegółowe informacje w **dziennikach aktywności** grupy zasobów dla danej maszyny wirtualnej.  
    - Możesz uzyskać dostęp do dziennika aktywności z poziomu paska nawigacyjnego na stronie maszyny wirtualnej laboratorium. Po wybraniu tej opcji zobaczysz wpis dotyczący **zastosowania artefaktów do maszyny wirtualnej** (jeśli operacja Zastosuj artefakty została wyzwolona bezpośrednio) lub **Dodaj lub zmodyfikuj maszyny wirtualne** (jeśli operacja zastosowania artefaktów była częścią procesu tworzenia maszyny wirtualnej).
    - Wyszukaj błędy w ramach tych wpisów. Czasami błąd nie zostanie odpowiednio oznakowany i konieczne będzie zbadanie każdego wpisu.
    - Badając szczegóły dotyczące poszczególnych wpisów, należy sprawdzić zawartość ładunku JSON. W dolnej części tego dokumentu może zostać wyświetlony komunikat o błędzie.
- **Podczas próby wykonania artefaktu**. Może to być spowodowane problemami z siecią lub magazynem. Aby uzyskać szczegółowe informacje, zobacz odpowiednią sekcję w dalszej części tego artykułu. Przyczyną może być również sposób tworzenia skryptu. Na przykład:
    - Skrypt programu PowerShell ma **obowiązkowe parametry**, ale jeden z nich nie przeszedł do niego wartości, ponieważ zezwalasz użytkownikowi na pozostawienie pustego elementu lub nie masz wartości domyślnej właściwości w artifactfile.jsw pliku definicji. Skrypt przestanie odpowiadać, ponieważ oczekuje na dane wejściowe użytkownika.
    - Skrypt programu PowerShell **wymaga wprowadzenia danych przez użytkownika** w ramach wykonywania. Skrypty muszą być zapisane w taki sposób, aby działały w sposób cichy bez konieczności interwencji użytkownika.
- **Agent maszyny wirtualnej trwa zbyt długo**. Gdy maszyna wirtualna jest uruchomiona po raz pierwszy lub gdy rozszerzenie niestandardowego skryptu zostanie zainstalowane w celu obsłużynia żądania zastosowania artefaktów, maszyna wirtualna może wymagać uaktualnienia agenta maszyny wirtualnej lub zaczekać na zainicjowanie agenta maszyny wirtualnej. Mogą istnieć usługi, od których zależy Agent maszyny wirtualnej, który zajmuje dużo czasu na zainicjowanie. W takich przypadkach zobacz [Omówienie agenta maszyny wirtualnej platformy Azure](../virtual-machines/extensions/agent-windows.md) , aby uzyskać dalsze Rozwiązywanie problemów.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>Aby sprawdzić, czy artefakt przestał odpowiadać z powodu skryptu

1. Zaloguj się do maszyny wirtualnej.
2. Skopiuj skrypt lokalnie na maszynie wirtualnej lub Znajdź go na maszynie wirtualnej w obszarze `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>` . Jest to lokalizacja, w której pobierane są skrypty artefaktów.
3. Za pomocą wiersza polecenia z podwyższonym poziomem uprawnień wykonaj skrypt lokalnie, dostarczając te same wartości parametrów, które są używane do tego problemu.
4. Ustal, czy skrypt cierpi z dowolnego niepożądanego zachowania. W takim przypadku należy zażądać aktualizacji artefaktu (jeśli pochodzi z repozytorium publicznego); lub wprowadź poprawki (jeśli pochodzą z repozytorium prywatnego).

> [!TIP]
> Możesz rozwiązać problemy dotyczące artefaktów hostowanych w naszym [repozytorium publicznym](https://github.com/Azure/azure-devtestlab) i przesłać zmiany dotyczące przeglądu i zatwierdzenia. Zapoznaj się z sekcją **materiały** do dokumentu [README.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) .
> 
> Aby uzyskać informacje na temat pisania własnych artefaktów, zobacz dokument [AUTHORING.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) .

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>Aby sprawdzić, czy artefakt przestał odpowiadać z powodu agenta maszyny wirtualnej:
1. Zaloguj się do maszyny wirtualnej.
2. Za pomocą Eksploratora plików przejdź do **C:\WindowsAzure\logs**.
3. Znajdź i Otwórz plik **WaAppAgent. log**.
4. Poszukaj wpisów, które są wyświetlane podczas uruchamiania agenta maszyny wirtualnej i po jego inicjacji (to oznacza, że pierwszy puls jest wysyłany). Preferuj nowsze wpisy lub w odniesieniu do czasu, w którym wystąpił problem.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    W tym przykładzie można zobaczyć, że czas rozpoczęcia agenta maszyny wirtualnej trwał 10 minut i 20 sekund, ponieważ został wysłany puls. Przyczyną tego problemu jest uruchomienie usługi OOBE przez długi czas.

> [!TIP]
> Aby uzyskać ogólne informacje na temat rozszerzeń platformy Azure, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](../virtual-machines/extensions/overview.md).

## <a name="storage-errors"></a>Błędy magazynu
DevTest Labs wymaga dostępu do konta magazynu laboratorium, które jest tworzone w celu buforowania artefaktów. Gdy DevTest Labs stosuje artefakt, odczyta konfigurację artefaktu i jego pliki ze skonfigurowanych repozytoriów. Domyślnie DevTest Labs konfiguruje dostęp do **publicznego repozytorium artefaktów**.

W zależności od konfiguracji maszyny wirtualnej może nie mieć bezpośredniego dostępu do tego repozytorium. W związku z tym, w ramach projektu, DevTest Labs buforuje artefakty na koncie magazynu, które jest tworzone podczas pierwszego inicjowania laboratorium.

Jeśli dostęp do tego konta magazynu jest blokowany w dowolny sposób, tak jak w przypadku blokowania ruchu z maszyny wirtualnej do usługi Azure Storage, może zostać wyświetlony komunikat o błędzie podobny do następującego:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

Powyższy błąd pojawi się w sekcji **komunikat wdrożenia** na stronie **wyniki artefaktu** w obszarze **Zarządzaj artefaktami**. Zostanie ona również wyświetlona w **dziennikach aktywności** w ramach grupy zasobów danej maszyny wirtualnej.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Aby zapewnić, że komunikacja z usługą Azure Storage nie jest blokowana:

- **Sprawdź, czy dodano grupy zabezpieczeń sieci (sieciowej grupy zabezpieczeń)**. Może się okazać, że dodano zasady subskrypcji, gdzie sieciowych grup zabezpieczeń są konfigurowane automatycznie we wszystkich sieciach wirtualnych. Ma także wpływ na domyślną sieć wirtualną laboratorium, jeśli jest używana, lub inną sieć wirtualną skonfigurowaną w laboratorium, służącą do tworzenia maszyn wirtualnych.
- **Sprawdź domyślne konto magazynu laboratorium** (czyli pierwsze konto magazynu utworzone podczas tworzenia laboratorium), którego nazwa zazwyczaj rozpoczyna się od litery "a" i kończąca się cyfrą wielocyfrową, a \<labname\> #).
    1. Przejdź do grupy zasobów laboratorium.
    2. Znajdź zasób typu **konto magazynu**, którego nazwa pasuje do Konwencji.
    3. Przejdź do strony konto magazynu o nazwie **zapory i sieci wirtualne**.
    4. Upewnij się, że jest ona ustawiona na **wszystkie sieci**. Jeśli wybrano opcję **wybrane sieci** , upewnij się, że sieci wirtualne laboratorium używane do tworzenia maszyn wirtualnych są dodawane do listy.

Aby uzyskać bardziej szczegółowe informacje dotyczące rozwiązywania problemów, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../storage/common/storage-network-security.md).

> [!TIP]
> **Sprawdź reguły sieciowej grupy zabezpieczeń**. Użyj [weryfikacji przepływu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) , aby potwierdzić, że reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Możesz również przejrzeć obowiązujące zasady grupy zabezpieczeń, aby upewnić się, że istnieje reguła **zezwalania na** ruch przychodzący sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz Używanie obowiązujących [reguł zabezpieczeń w celu rozwiązywania problemów z przepływem ruchu maszyn wirtualnych](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Inne źródła błędu
Istnieją inne mniej częste źródła błędów. Oceń każdy z nich, aby zobaczyć, czy ma on zastosowanie do Twojego przypadku. Oto jedna z nich: 

- **Wygasły osobisty token dostępu dla repozytorium prywatnego**. Po wygaśnięciu tego artefakt nie zostanie wyświetlony na liście, a wszystkie skrypty odwołujące się do artefaktów z repozytorium z wygasłym prywatnym tokenem dostępu zakończą się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki
Jeśli żaden z tych błędów nie wystąpi i nadal nie możesz zastosować artefaktów, możesz zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
