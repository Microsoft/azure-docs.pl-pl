---
title: Hotpatch for Windows Server Azure Edition (wersja zapoznawcza)
description: Dowiedz się, w jaki sposób program hotpatch for Windows Server Azure Edition działa i jak go włączyć
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 92b8bf240dfd73cc9191675db07f20816b7156a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953395"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Hotpatch dla nowych maszyn wirtualnych (wersja zapoznawcza)

Funkcja HotPatching to nowy sposób instalowania aktualizacji na nowych maszynach wirtualnych systemu Windows Server Azure Edition (VM), które nie wymagają ponownego uruchomienia po instalacji. Ten artykuł zawiera informacje na temat maszyn wirtualnych hotpatch for Windows Server Azure Edition, które mają następujące zalety:
* Dolny wpływ na obciążenie z mniejszą ilością ponownych uruchomień
* Szybsze wdrażanie aktualizacji w miarę mniejszych pakietów, instalowanie szybszych i łatwiejsze organizowanie poprawek za pomocą usługi Azure Update Manager
* Lepsza ochrona, ponieważ pakiety aktualizacji hotpatch są objęte zakresem aktualizacji zabezpieczeń systemu Windows, które instalują się szybciej bez ponownego uruchamiania

## <a name="how-hotpatch-works"></a>Jak działa hotpatch

Hotpatch działa, najpierw ustanawiając linię bazową z Windows Update najnowszą aktualizacją zbiorczą. Hotpatches są okresowo uwalniane (na przykład w drugi wtorek miesiąca), który kompiluje się w tej linii bazowej. Hotpatches będzie zawierać aktualizacje, które nie wymagają ponownego uruchomienia. Okresowo (począwszy od co trzy miesiące), linia bazowa jest odświeżana nową najnowszą aktualizacją zbiorczą.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Przykładowy harmonogram hotpatch.":::

Istnieją dwa typy linii bazowych: **planowane punkty odniesienia** i **nieplanowane linie bazowe**.
*  **Planowane punkty odniesienia** są udostępniane w regularnych erzeach, z wydaniami hotpatch między.  Planowane linie bazowe obejmują wszystkie aktualizacje w porównywalnej _najnowszej aktualizacji zbiorczej_ dla danego miesiąca i wymagają ponownego uruchomienia.
    * Powyższy przykładowy harmonogram ilustruje cztery planowane wersje bazowe w roku kalendarzowym (pięć łącznie na diagramie) i osiem hotpatch.
* **Nieplanowane linie bazowe** są uwalniane, gdy jest wydawana ważna aktualizacja (na przykład Naprawiono zero dni), a dana aktualizacja nie może zostać wydana jako hotpatch.  Po zwolnieniu nieplanowanych linii bazowych hotpatch wersja zostanie zamieniona na nieplanowaną linię bazową w danym miesiącu.  Nieplanowane linie bazowe obejmują również wszystkie aktualizacje w porównywalnej _najnowszej aktualizacji zbiorczej_ dla tego miesiąca, a także wymagają ponownego uruchomienia.
    * Powyższy przykładowy harmonogram ilustruje dwie nieplanowane linie bazowe, które zastąpią hotpatch wydania w tych miesiącach (rzeczywista liczba nieplanowanych linii bazowych w roku nie jest znana z góry).

## <a name="regional-availability"></a>Dostępność w regionach
Hotpatch jest dostępny we wszystkich globalnych regionach platformy Azure w wersji zapoznawczej. Regiony Azure Government nie są obsługiwane w wersji zapoznawczej.

## <a name="how-to-get-started"></a>Jak zacząć

> [!NOTE]
> W fazie zapoznawczej można zacząć korzystać tylko z Azure Portal za pomocą [tego linku](https://aka.ms/AzureAutomanageHotPatch).

Aby rozpocząć korzystanie z programu hotpatch na nowej maszynie wirtualnej, wykonaj następujące kroki:
1.  Włącz dostęp do wersji zapoznawczej
    * Wymagana jest możliwość jednorazowego włączenia dostępu w wersji zapoznawczej na subskrypcję.
    * Dostęp do wersji zapoznawczej można włączyć za pomocą interfejsu API, programu PowerShell lub interfejsu wiersza polecenia zgodnie z opisem w poniższej sekcji.
1.  Tworzenie maszyny wirtualnej na podstawie Azure Portal
    * W trakcie korzystania z wersji zapoznawczej należy zacząć korzystać z [tego linku](https://aka.ms/AzureAutomanageHotPatch).
1.  Podaj szczegóły maszyny wirtualnej
    * Upewnij się, że na liście rozwijanej obrazu wybrano _system Windows Server 2019 Datacenter: Azure Edition_
    * Na karcie Zarządzanie przewiń w dół do sekcji "aktualizacje systemu operacyjnego gościa". Zobaczysz, że funkcja HotPatching została ustawiona na wartość włączone, a Instalacja poprawek została przekazana do funkcji poprawek przystosowanych do platformy Azure.
    * Zasady autozarządzania MASZYNami wirtualnymi są domyślnie włączone.
1. Utwórz nową maszynę wirtualną

## <a name="enabling-preview-access"></a>Włączanie dostępu do wersji zapoznawczej

### <a name="rest-api"></a>Interfejs API REST

W poniższym przykładzie opisano sposób włączania wersji zapoznawczej subskrypcji:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Po zarejestrowaniu tej funkcji dla subskrypcji Zakończ proces wyboru, propagowanie zmiany do dostawcy zasobów obliczeniowych.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

```Register-AzProviderFeature```Aby włączyć podgląd subskrypcji, użyj polecenia cmdlet.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Po zarejestrowaniu tej funkcji dla subskrypcji Zakończ proces wyboru, propagowanie zmiany do dostawcy zasobów obliczeniowych.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Służy ```az feature register``` do włączania wersji zapoznawczej subskrypcji.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Po zarejestrowaniu tej funkcji dla subskrypcji Zakończ proces wyboru, propagowanie zmiany do dostawcy zasobów obliczeniowych.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Instalacja poprawki

W trakcie okresu zapoznawczego [Automatyczna poprawka gościa maszyny wirtualnej](../virtual-machines/automatic-vm-guest-patching.md) jest włączana automatycznie dla wszystkich maszyn wirtualnych utworzonych przy użyciu _systemu Windows Server 2019 Datacenter: Azure Edition_. Z włączoną automatyczną poprawką gościa maszyny wirtualnej:
* Poprawki sklasyfikowane jako krytyczne lub zabezpieczenia są automatycznie pobierane i stosowane na maszynie wirtualnej.
* Poprawki są stosowane w godzinach poza godzinami szczytu w strefie czasowej maszyny wirtualnej.
* Aranżacja poprawek jest zarządzana przez platformę Azure, a poprawki są stosowane po [pierwszej zasadzie dostępności](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching).
* Kondycja maszyny wirtualnej określona za pośrednictwem sygnałów kondycji platformy jest monitorowana w celu wykrywania niepowodzeń poprawek.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Jak działa automatyczna poprawka gościa maszyny wirtualnej?

Gdy na maszynie wirtualnej jest włączone [Automatyczne stosowanie poprawek gościa maszyny wirtualnej](../virtual-machines/automatic-vm-guest-patching.md) , dostępne są automatycznie pobierane i stosowane poprawki krytyczne i zabezpieczenia. Ten proces jest automatycznie rozpoczynany w każdym miesiącu, gdy zostaną wydane nowe poprawki. Ocena poprawek i instalacja jest automatyczna, a proces obejmuje ponowne uruchomienie maszyny wirtualnej zgodnie z wymaganiami.

Funkcja hotpatch włączona w _systemie Windows Server 2019 Datacenter: maszyny wirtualne platformy Azure_ , większość comiesięcznych aktualizacji zabezpieczeń jest dostarczanych jako hotpatches, które nie wymagają ponownego uruchomienia. Najnowsze aktualizacje zbiorcze wysyłane na planowanych lub nieplanowanych miesiącach bazowych są wymagane do ponownego uruchomienia maszyny wirtualnej. Dodatkowe poprawki krytyczne lub zabezpieczeń mogą być również okresowo dostępne, co może wymagać ponownego uruchomienia maszyny wirtualnej.

Maszyna wirtualna jest szacowana automatycznie co kilka dni i wiele razy w ciągu 30-dniowego okresu, aby określić odpowiednie poprawki dla tej maszyny wirtualnej. Ta automatyczna ocena gwarantuje, że wszystkie brakujące poprawki zostaną odnalezione w najwcześniejszym możliwym momencie.

Poprawki są instalowane w ciągu 30 dni od comiesięcznych wersji poprawek, zgodnie z [zasadami pierwszej dostępności](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching). Poprawki są instalowane tylko poza godzinami szczytu maszyny wirtualnej, w zależności od strefy czasowej maszyny wirtualnej. Maszyna wirtualna musi być uruchomiona w godzinach poza godzinami szczytu, aby poprawki były instalowane automatycznie. Jeśli maszyna wirtualna jest wyłączona podczas okresowej oceny, maszyna wirtualna zostanie oceniona, a odpowiednie poprawki zostaną zainstalowane automatycznie podczas kolejnej oceny okresowej, gdy maszyna wirtualna jest włączona. Kolejna okresowa Ocena odbywa się zwykle w ciągu kilku dni.

Aktualizacje definicji i inne poprawki niesklasyfikowane jako krytyczne lub zabezpieczenia nie zostaną zainstalowane za poorednictwem automatycznej poprawki gościa maszyny wirtualnej.

## <a name="understanding-the-patch-status-for-your-vm"></a>Informacje o stanie poprawek dla maszyny wirtualnej

Aby wyświetlić stan poprawki dla maszyny wirtualnej, przejdź do sekcji " **gość + aktualizacje hosta** " dla maszyny wirtualnej w Azure Portal. W sekcji **aktualizacje systemu operacyjnego gościa** kliknij pozycję "przejdź do hotpatch (wersja zapoznawcza)", aby wyświetlić najnowszy stan poprawki dla maszyny wirtualnej.

Na tym ekranie zobaczysz stan hotpatch dla maszyny wirtualnej. Możesz również sprawdzić, czy istnieją dostępne poprawki dla maszyny wirtualnej, które nie zostały zainstalowane. Zgodnie z opisem w powyższej sekcji "instalacja poprawki" wszystkie aktualizacje zabezpieczeń i krytyczne zostaną automatycznie zainstalowane na maszynie wirtualnej przy użyciu [automatycznej poprawki gościa maszyny wirtualnej](../virtual-machines/automatic-vm-guest-patching.md) i nie są wymagane żadne dodatkowe akcje. Poprawki z innymi klasyfikacjami aktualizacji nie są instalowane automatycznie. Zamiast tego są one widoczne na liście dostępnych poprawek na karcie "zgodność aktualizacji". Historię wdrożeń aktualizacji na maszynie wirtualnej można także wyświetlić za pomocą "Historia aktualizacji". Zostanie wyświetlona Historia aktualizacji z ostatnich 30 dni wraz ze szczegółami instalacji poprawki.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Hotpatch Management.":::

Dzięki automatycznej poprawkom gościa maszyny wirtualnej maszyna wirtualna jest okresowo i automatycznie oceniana pod kątem dostępnych aktualizacji. Te okresowe oceny zapewniają wykrycie dostępnych poprawek. Wyniki oceny można obejrzeć na ekranie aktualizacje powyżej, łącznie z czasem ostatniej oceny. Możesz również wybrać opcję wyzwalania oceny poprawek na żądanie dla maszyny wirtualnej w dowolnym momencie przy użyciu opcji "Oceń teraz" i przejrzeć wyniki po zakończeniu oceny.

Podobnie jak w przypadku oceny na żądanie, można również zainstalować poprawki na żądanie dla maszyny wirtualnej przy użyciu opcji "Zainstaluj aktualizacje teraz". W tym miejscu możesz zainstalować wszystkie aktualizacje w ramach określonych klasyfikacji poprawek. Możesz również określić aktualizacje do dołączenia lub wykluczenia, dostarczając listę poszczególnych artykułów bazy wiedzy. Poprawki zainstalowane na żądanie nie są instalowane przy użyciu zasad pierwszej dostępności i mogą wymagać większej ilości ponownych uruchomień oraz przestojów maszyn wirtualnych na potrzeby instalacji aktualizacji.

## <a name="supported-updates"></a>Obsługiwane aktualizacje

Hotpatch obejmuje aktualizacje zabezpieczeń systemu Windows i zapewnia ich zgodność z zawartością aktualizacji zabezpieczeń wystawionych w ramach regularnego (nie Hotpatchego) kanału Windows Update.

Istnieją pewne ważne zagadnienia dotyczące uruchamiania maszyny wirtualnej z systemem Windows Server Azure Edition przy włączonej hotpatch. Ponowny rozruch jest nadal wymagany do zainstalowania aktualizacji, które nie są uwzględnione w programie hotpatch. Ponowne uruchomienia są również wymagane okresowo po zainstalowaniu nowej linii bazowej. Te ponowne uruchomienia synchronizują maszynę wirtualną z poprawkami nienależącymi do zabezpieczeń zawartymi w najnowszej aktualizacji zbiorczej.
* Poprawki, które nie są obecnie zawarte w programie hotpatch, obejmują aktualizacje niezwiązane z zabezpieczeniami wydane dla systemu Windows, a także aktualizacje inne niż Windows (takie jak poprawki .NET).  Te typy poprawek muszą być instalowane w trakcie miesiąca bazowego i wymagają ponownego uruchomienia.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-is-hotpatching"></a>Co to jest Funkcja HotPatching?

* Funkcja HotPatching to nowy sposób instalowania aktualizacji na komputerze z systemem Windows Server 2019 Datacenter: Azure Edition VM na platformie Azure, który nie wymaga ponownego uruchomienia po instalacji. Działa przez poprawianie kodu w pamięci uruchomionych procesów bez konieczności ponownego uruchamiania procesu.

### <a name="how-does-hotpatching-work"></a>Jak działa funkcja HotPatching?

* Funkcja HotPatching działa przez ustanowienie linii bazowej z Windows Update najnowszej aktualizacji zbiorczej, a następnie kompiluje ją z aktualizacjami, które nie wymagają ponownego uruchomienia.  Linia bazowa jest okresowo aktualizowana przy użyciu nowej aktualizacji zbiorczej. Aktualizacja zbiorcza obejmuje wszystkie aktualizacje zabezpieczeń i jakości oraz wymaga ponownego uruchomienia komputera.

### <a name="why-should-i-use-hotpatch"></a>Dlaczego warto używać hotpatch?

* Jeśli używasz hotpatch w systemie Windows Server 2019 Datacenter: Azure Edition, maszyna wirtualna będzie miała wyższą dostępność (mniejszą liczbę ponownych uruchomień) i szybsze aktualizacje (mniejsze pakiety, które są instalowane szybciej bez konieczności ponownego uruchamiania procesów). Ten proces powoduje, że maszyna wirtualna jest zawsze aktualna i bezpieczna.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Jakie typy aktualizacji są objęte hotpatch?

* Hotpatch obecnie obejmuje aktualizacje zabezpieczeń systemu Windows.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Kiedy zostanie wyświetlona pierwsza aktualizacja hotpatch?

* Aktualizacje hotpatch są zwykle publikowane w drugi wtorek każdego miesiąca. Aby uzyskać więcej informacji, zobacz poniżej.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Jak będzie wyglądać harmonogram hotpatch?

* Funkcja HotPatching działa przez ustanowienie linii bazowej z Windows Update najnowszą aktualizacją zbiorczą, a następnie kompiluje ją z aktualizacją hotpatch publikowaną miesięcznie.  W trakcie okresu zapoznawczego punkty odniesienia zostaną opublikowane co trzy miesiące. Zapoznaj się z poniższym obrazem, aby zapoznać się z przykładem rocznego harmonogramu z trzema subskrypcjami (w tym przykładem nieplanowanych linii bazowych ze względu na poprawki zerowe).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Przykładowy harmonogram hotpatch.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Czy ponowne uruchomienia są nadal potrzebne dla maszyny wirtualnej zarejestrowanej w hotpatch?

* Ponowny rozruch jest nadal wymagany do zainstalowania aktualizacji nieuwzględnionych w programie hotpatch i są wymagane okresowo po zainstalowaniu linii bazowej (Windows Update najnowszej aktualizacji zbiorczej). To ponowne uruchomienie spowoduje synchronizację maszyny wirtualnej ze wszystkimi poprawkami zawartymi w aktualizacji zbiorczej. Linie bazowe (które wymagają ponownego uruchomienia) rozpocznie się w ciągu trzech miesięcy erze i rosną z upływem czasu.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Czy moje aplikacje mają oddziaływać po zainstalowaniu aktualizacji hotpatch?

* Ponieważ hotpatch poprawia kod w pamięci uruchomionych procesów bez konieczności ponownego uruchamiania procesu, proces stosowania poprawek nie będzie miał wpływu na aplikacje. Należy zauważyć, że jest to niezależne od potencjalnego wpływu na wydajność i funkcjonalność poprawki.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Czy mogę wyłączyć hotpatch na mojej maszynie wirtualnej?

* Można wyłączyć hotpatch na maszynie wirtualnej za pomocą Azure Portal.  Wyłączenie usługi hotpatch spowoduje Wyrejestrowanie maszyny wirtualnej z hotpatch, która przywraca maszynę wirtualną do typowego zachowania aktualizacji dla systemu Windows Server.  Po wyrejestrowaniu z usługi hotpatch na maszynie wirtualnej można ponownie zarejestrować tę maszynę wirtualną po wydaniu kolejnej linii bazowej hotpatch.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Czy mogę uaktualnić istniejący system operacyjny Windows Server?

* Uaktualnianie z istniejących wersji systemu Windows Server (czyli systemu Windows Server 2016 lub 2019 wersji niezwiązanych z platformą Azure) nie jest obecnie obsługiwane. Obsługiwane jest uaktualnianie do przyszłych wersji systemu Windows Server Azure Edition.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Czy mogę używać hotpatch do obciążeń produkcyjnych w ramach wersji zapoznawczej?

* Wersje zapoznawcze są przeznaczone tylko do celów testowych i nie mogą być używane w środowiskach produkcyjnych.

### <a name="will-i-be-charged-during-the-preview"></a>Czy zostanie naliczona opłata w ramach wersji zapoznawczej?

* Licencja na system Windows Server Azure Edition jest bezpłatna w ramach wersji zapoznawczej. Jednak koszt każdej źródłowej infrastruktury skonfigurowanej dla maszyny wirtualnej (magazynu, obliczeń, sieci itp.) nadal będzie obciążany subskrypcją.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Jak mogę uzyskać pomoc techniczną dotyczącą rozwiązywania problemów z funkcją HotPatching?

* Możesz zaplikować [bilet przypadku pomocy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)technicznej. Dla opcji usługa Wyszukaj i wybierz **maszynę wirtualną z systemem Windows** w obszarze Obliczanie. Wybierz **funkcje platformy Azure** dla typu problemu i **automatycznej poprawki gościa maszyny wirtualnej** dla podtypu problemu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o platformie Azure Update Management [tym miejscu](../automation/update-management/overview.md).
* Dowiedz się więcej na temat automatycznego stosowania [](../virtual-machines/automatic-vm-guest-patching.md) poprawek gościa maszyny wirtualnej