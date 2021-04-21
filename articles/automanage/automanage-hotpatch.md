---
title: Hotpatch for Windows Server Azure Edition (wersja zapoznawcza)
description: Dowiedz się, jak działa hotpatch dla systemu Windows Server Azure Edition i jak ją włączyć
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b3fc9f12dfa6ad4edcc120ac7c9592c9435a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830182"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Hotpatch dla nowych maszyn wirtualnych (wersja zapoznawcza)

Hotpatching to nowy sposób instalowania aktualizacji na nowych maszynach wirtualnych z systemem Windows Server Azure Edition, które nie wymagają ponownego uruchomienia po instalacji. Ten artykuł zawiera informacje o poprawkach na gorąco dla maszyn wirtualnych z systemem Windows Server Azure Edition, które mają następujące zalety:
* Mniejszy wpływ na obciążenie przy mniejszej ilości ponownych uruchomień
* Szybsze wdrażanie aktualizacji, ponieważ pakiety są mniejsze, instalują się szybciej i mają łatwiejszą aranżację poprawek za pomocą usługi Azure Update Manager
* Lepsza ochrona, ponieważ pakiety aktualizacji hotpatch są ograniczony do aktualizacji zabezpieczeń systemu Windows, które instalują się szybciej bez ponownego uruchamiania

## <a name="how-hotpatch-works"></a>Jak działa hotpatch

Hotpatch działa przez utworzenie planu bazowego z Windows Update najnowszą aktualizacją zbiorczą. Poprawki są okresowo zwalniane (na przykład w drugi wtorek miesiąca), które bazują na tym punkcie odniesienia. Poprawki będą zawierać aktualizacje, które nie wymagają ponownego uruchomienia. Okresowo (począwszy od co trzy miesiące) punkt odniesienia jest odświeżany przy użyciu nowej aktualizacji zbiorczej.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Harmonogram przykładu hotpatch.":::

Istnieją dwa typy punktów odniesienia: **planowane i** **nieplanowane.**
*  **Planowane linie bazowe** są zwalniane w regularnych odstępach czasu, a między nimi są dostępne wersje hotpatch.  Planowane linie bazowe obejmują wszystkie aktualizacje w porównywalnej najnowszej aktualizacji _zbiorczej_ dla tego miesiąca i wymagają ponownego uruchomienia.
    * Powyższy przykładowy harmonogram ilustruje cztery planowane wydania planu bazowego w roku kalendarzowym (łącznie pięć na diagramie) i osiem wydań poprawki na gorąco.
* **Nieplanowane linie** bazowe są publikowane po wywłaszczeniu ważnej aktualizacji (takiej jak poprawka zerowa) i tej konkretnej aktualizacji nie można wydano jako poprawki.  Po wydaniu nieplanowanych punktów odniesienia wydanie poprawki na gorąco zostanie zastąpione nieplanowanym punktem odniesienia w tym miesiącu.  Nieplanowane linie bazowe obejmują również  wszystkie aktualizacje w porównywalnej najnowszej aktualizacji zbiorczej dla tego miesiąca, a także wymagają ponownego uruchomienia.
    * Powyższy przykładowy harmonogram ilustruje dwa nieplanowane plany bazowe, które zastąpią wydania hotpatch dla tych miesięcy (rzeczywista liczba nieplanowanych linii bazowych w roku nie jest znana z wyprzedzeniem).

## <a name="regional-availability"></a>Dostępność w regionach
Hotpatch jest dostępna we wszystkich globalnych regionach platformy Azure w wersji zapoznawczej. Azure Government nie są obsługiwane w wersji zapoznawczej.

## <a name="how-to-get-started"></a>Jak zacząć

> [!NOTE]
> W fazie wersji zapoznawczej możesz rozpocząć pracę tylko w oknie Azure Portal użyciu [tego linku.](https://aka.ms/AzureAutomanageHotPatch)

Aby rozpocząć korzystanie z poprawki na nowej maszynie wirtualnej, wykonaj następujące kroki:
1.  Włączanie dostępu w wersji zapoznawczej
    * Wymagana jest jedna wersja zapoznawcza włączania dostępu na subskrypcję.
    * Dostęp w wersji zapoznawczej można włączyć za pośrednictwem interfejsu API, programu PowerShell lub interfejsu wiersza polecenia zgodnie z opisem w poniższej sekcji.
1.  Tworzenie maszyny wirtualnej na Azure Portal
    * W okresie korzystania z wersji zapoznawczej musisz rozpocząć korzystanie z [tego linku.](https://aka.ms/AzureAutomanageHotPatch)
1.  Dostarczanie szczegółów maszyny wirtualnej
    * Upewnij się, że na liście rozwijanej Obraz wybrano _opcję Windows Server 2019 Datacenter: Azure Edition)_
    * Na kroku karty Zarządzanie przewiń w dół do sekcji "Aktualizacje systemu operacyjnego gościa". Zobaczysz, że opcja Hotpatching ustawiona na Wł. i Instalacja poprawek jest domyślnie ustawiona na stosowanie poprawek orkiestrowanych przez platformę Azure.
    * Najlepsze rozwiązania dotyczące automatycznego tworzenia maszyn wirtualnych będą domyślnie włączone
1. Tworzenie nowej maszyny wirtualnej

## <a name="enabling-preview-access"></a>Włączanie dostępu w wersji zapoznawczej

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

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia ```Register-AzProviderFeature``` cmdlet , aby włączyć podgląd subskrypcji.

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

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj ```az feature register``` , aby włączyć podgląd subskrypcji.

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

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Instalacja poprawna

W wersji zapoznawczej [automatyczne](../virtual-machines/automatic-vm-guest-patching.md) stosowanie poprawek gościa maszyny wirtualnej jest automatycznie włączone dla wszystkich maszyn wirtualnych utworzonych za pomocą centrum danych systemu _Windows Server 2019: Wersja platformy Azure._ Z włączonym automatycznym stosowaniem poprawek gościa maszyny wirtualnej:
* Poprawki sklasyfikowane jako krytyczne lub zabezpieczenia są automatycznie pobierane i stosowane na maszynie wirtualnej.
* Poprawki są stosowane poza godzinami szczytu w strefie czasowej maszyny wirtualnej.
* Orkiestracja poprawek jest zarządzana przez platformę Azure, a poprawki są stosowane zgodnie z [zasadami availability-first.](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching)
* Kondycja maszyny wirtualnej określona za pomocą sygnałów kondycji platformy jest monitorowana w celu wykrywania błędów poprawek.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Jak działa automatyczne stosowanie poprawek gościa maszyny wirtualnej?

Gdy [automatyczne stosowanie poprawek gościa](../virtual-machines/automatic-vm-guest-patching.md) maszyny wirtualnej jest włączone na maszynie wirtualnej, dostępne poprawki krytyczne i zabezpieczeń są pobierane i stosowane automatycznie. Ten proces jest uruchamiany automatycznie co miesiąc, gdy są wydane nowe poprawki. Ocena poprawek i instalacja są automatyczne, a proces obejmuje ponowne uruchomienie maszyny wirtualnej zgodnie z potrzebami.

Po włączeniu funkcji Hotpatch w systemie _Windows Server 2019 Datacenter:_ maszyny wirtualne w wersji Azure Edition większość comiesięcznych aktualizacji zabezpieczeń jest dostarczana jako poprawki, które nie wymagają ponownego uruchomienia. Najnowsze aktualizacje zbiorcze wysyłane w planowanych lub nieplanowanych miesiącach linii bazowej będą wymagać ponownego uruchomienia maszyny wirtualnej. Okresowo mogą być również dostępne dodatkowe poprawki krytyczne lub poprawki zabezpieczeń, które mogą wymagać ponownego uruchomienia maszyny wirtualnej.

Maszyna wirtualna jest oceniana automatycznie co kilka dni i wielokrotnie w dowolnym okresie 30 dni w celu określenia odpowiednich poprawek dla tej maszyny wirtualnej. Ta automatyczna ocena gwarantuje, że wszelkie brakujące poprawki zostaną odnalezione jak najszybciej.

Poprawki są instalowane w ciągu 30 dni od comiesięcznych wydań poprawek zgodnie z zasadami [pierwszej dostępności.](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching) Poprawki są instalowane tylko poza godzinami szczytu maszyny wirtualnej, w zależności od strefy czasowej maszyny wirtualnej. Maszyna wirtualna musi działać poza godzinami szczytu, aby poprawki zostały automatycznie zainstalowane. Jeśli maszyna wirtualna zostanie wyłączona podczas okresowej oceny, maszyna wirtualna zostanie oceniona, a odpowiednie poprawki zostaną zainstalowane automatycznie podczas następnej okresowej oceny, gdy maszyna wirtualna jest wł. Następna okresowa ocena zwykle następuje w ciągu kilku dni.

Aktualizacje definicji i inne poprawki, które nie zostały sklasyfikowane jako krytyczne lub nie zostaną zainstalowane za pomocą automatycznego poprawiania gościa maszyny wirtualnej.

## <a name="understanding-the-patch-status-for-your-vm"></a>Opis stanu poprawki dla maszyny wirtualnej

Aby wyświetlić stan poprawki dla maszyny wirtualnej, przejdź do sekcji **Aktualizacje** gościa i hosta dla maszyny wirtualnej w Azure Portal. W sekcji **Aktualizacje systemu operacyjnego gościa** kliknij pozycję "Przejdź do poprawki (wersja zapoznawcza)", aby wyświetlić stan najnowszej poprawki dla maszyny wirtualnej.

Na tym ekranie zobaczysz stan hotpatch dla maszyny wirtualnej. Możesz również sprawdzić, czy są dostępne poprawki dla maszyny wirtualnej, które nie zostały zainstalowane. Zgodnie z opisem w powyższej sekcji "Instalacja poprawek" wszystkie aktualizacje [](../virtual-machines/automatic-vm-guest-patching.md) zabezpieczeń i krytyczne zostaną automatycznie zainstalowane na maszynie wirtualnej przy użyciu automatycznego stosowania poprawek gościa maszyny wirtualnej i nie są wymagane żadne dodatkowe akcje. Poprawki z innymi klasyfikacjami aktualizacji nie są instalowane automatycznie. Zamiast tego są one dostępne na liście dostępnych poprawek na karcie "Aktualizowanie zgodności". Historię wdrożeń aktualizacji na maszynie wirtualnej można również wyświetlić za pomocą "Historii aktualizacji". Zostanie wyświetlona historia aktualizacji z ostatnich 30 dni wraz ze szczegółami instalacji poprawek.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Zarządzanie hotpatch.":::

Dzięki automatycznemu stosowaniem poprawek gościa maszyny wirtualnej maszyna wirtualna jest okresowo i automatycznie oceniana pod celu oceny dostępnych aktualizacji. Te okresowe oceny zapewniają wykrycie dostępnych poprawek. Wyniki oceny można wyświetlić na ekranie Aktualizacje powyżej, w tym czas ostatniej oceny. Możesz również w dowolnym momencie wyzwolić ocenę poprawek na żądanie dla maszyny wirtualnej przy użyciu opcji "Oceń teraz" i przejrzeć wyniki po zakończeniu oceny.

Podobnie jak w przypadku oceny na żądanie, poprawki można również instalować na żądanie dla maszyny wirtualnej przy użyciu opcji "Zainstaluj aktualizacje teraz". W tym miejscu możesz zainstalować wszystkie aktualizacje w ramach określonych klasyfikacji poprawek. Możesz również określić aktualizacje do dołączyć lub wykluczyć, podając listę poszczególnych artykułów bazy wiedzy. Poprawki zainstalowane na żądanie nie są instalowane przy użyciu zasad availability-first i mogą wymagać większej liczby ponownych uruchomień i przestojów maszyny wirtualnej w przypadku instalacji aktualizacji.

## <a name="supported-updates"></a>Obsługiwane aktualizacje

Hotpatch obejmuje Zabezpieczenia Windows aktualizacji systemu Windows i utrzymuje parzystość z zawartością aktualizacji zabezpieczeń wystawionych dla w zwykłym (nie hotpatch) kanału aktualizacji systemu Windows.

Istnieją pewne ważne zagadnienia dotyczące uruchamiania maszyny wirtualnej z systemem Windows Server w wersji Azure z włączoną hotpatch. Ponowne uruchomienia są nadal wymagane do zainstalowania aktualizacji, które nie są uwzględnione w programie Hotpatch. Ponowne uruchomienia są również wymagane okresowo po zainstalowaniu nowego planu bazowego. Te ponowne uruchomienia aktualizują maszynę wirtualną w synchronizacji z poprawkami innymi niż poprawki zabezpieczeń zawarte w najnowszej aktualizacji zbiorczej.
* Poprawki, które nie są obecnie zawarte w programie Hotpatch, obejmują aktualizacje niebędące zabezpieczeniami wydane dla systemu Windows oraz aktualizacje inne niż systemu Windows (takie jak poprawki programu .NET).  Tego typu poprawki muszą być instalowane w miesiącu odniesienia i będą wymagać ponownego uruchomienia.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-is-hotpatching"></a>Co to jest hotpatching?

* Hotpatching to nowy sposób instalowania aktualizacji na centrum danych systemu Windows Server 2019: maszyna wirtualna w wersji Azure Edition na platformie Azure, która nie wymaga ponownego uruchomienia po instalacji. Działa on przez stosowanie poprawek do kodu w pamięci uruchomionych procesów bez konieczności ponownego uruchamiania procesu.

### <a name="how-does-hotpatching-work"></a>Jak działa hotpatching?

* Hotpatching działa przez ustanowienie linii bazowej z najnowszą aktualizacją zbiorczą Windows Update, a następnie opiera się na tym punkcie odniesienia z aktualizacjami, które nie wymagają ponownego uruchomienia, aby weszły w życie.  Punkt odniesienia jest okresowo aktualizowany przy użyciu nowej aktualizacji zbiorczej. Aktualizacja zbiorcza obejmuje wszystkie aktualizacje zabezpieczeń i jakości oraz wymaga ponownego uruchomienia.

### <a name="why-should-i-use-hotpatch"></a>Dlaczego należy używać funkcji Hotpatch?

* W przypadku korzystania z narzędzia Hotpatch w systemie Windows Server 2019 Datacenter: Azure Edition maszyna wirtualna będzie mieć wyższą dostępność (mniej ponownych uruchomień) i szybsze aktualizacje (mniejsze pakiety instalowane szybciej bez konieczności ponownego uruchamiania procesów). Ten proces powoduje, że maszyna wirtualna jest zawsze aktualny i bezpieczny.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Jakie typy aktualizacji są objęte aktualizacją na gorąco?

* Hotpatch obecnie obejmuje aktualizacje zabezpieczeń systemu Windows.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Kiedy otrzymam pierwszą aktualizację hotpatch?

* Aktualizacje poprawki są zwykle zwalniane w drugi wtorek każdego miesiąca. Aby uzyskać więcej informacji, zobacz poniżej.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Jak będzie wyglądać harmonogram hotpatch?

* Tworzenie aktualizacji na gorąco działa przez ustanowienie planu bazowego z najnowszą Windows Update aktualizacją zbiorczą, a następnie bazuje na tym punkcie odniesienia z aktualizacjami hotpatch wydanymi co miesiąc.  W okresie wersji zapoznawczej punkty odniesienia będą wydane co trzy miesiące. Na poniższej ilustracji przedstawiono przykład rocznego harmonogramu na trzy miesiące (w tym przykład nieplanowanych linii bazowych z powodu poprawek z dnia zerowego).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Harmonogram przykładu hotpatch.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Czy ponowne uruchomienie maszyny wirtualnej zarejestrowanej w usłudze Hotpatch jest nadal potrzebne?

* Ponowne uruchomienia są nadal wymagane do zainstalowania aktualizacji, które nie są uwzględnione w programie Hotpatch, i są wymagane okresowo po zainstalowaniu planu bazowego (Windows Update najnowszej aktualizacji zbiorczej). Ten ponowny rozruch pozwoli zachować synchronizację maszyny wirtualnej ze wszystkimi poprawkami dołączonymi do aktualizacji zbiorczej. Punkty odniesienia (które wymagają ponownego uruchomienia) będą rozpoczynać się w ciągu trzech miesięcy i zwiększać się z czasem.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Czy po zainstalowaniu aktualizacji poprawki ma to wpływ na moje aplikacje?

* Ponieważ program Hotpatch poprawia kod w pamięci uruchomionych procesów bez konieczności ponownego uruchamiania procesu, proces poprawiania nie ma wpływu na aplikacje. Należy pamiętać, że jest to oddzielone od wszelkich potencjalnych implikacji wydajności i funkcjonalności samej poprawki.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Czy mogę wyłączyć hotpatch na mojej maszynie wirtualnej?

* Możesz wyłączyć hotpatch na maszynie wirtualnej za pomocą Azure Portal.  Wyłączenie poprawki spowoduje wyzwień maszynę wirtualną z programu Hotpatch, co spowoduje przywrócenie typowego zachowania aktualizacji maszyny wirtualnej dla systemu Windows Server.  Po wyrejestrować się z poprawki na maszynie wirtualnej można ponownie zarejestrować maszynę wirtualną po zwolnieniu następnego planu bazowego poprawki.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Czy można uaktualnić istniejący system operacyjny Windows Server?

* Uaktualnianie z istniejących wersji systemu Windows Server (czyli wersji systemu Windows Server 2016 lub 2019 spoza platformy Azure) nie jest obecnie obsługiwane. Będzie obsługiwane uaktualnianie do przyszłych wersji systemu Windows Server Azure Edition.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Czy mogę używać poprawki w przypadku obciążeń produkcyjnych w wersji zapoznawczej?

* Wersje zapoznawcze są przeznaczone tylko do celów testowych, a nie do użytku w środowiskach produkcyjnych.

### <a name="will-i-be-charged-during-the-preview"></a>Czy w okresie zapoznawczym zostaną naliczone opłaty?

* Licencja na system Windows Server Azure Edition jest bezpłatna w okresie zapoznawczym. Jednak koszt dowolnej podstawowej infrastruktury ustawionej dla maszyny wirtualnej (magazynu, mocy obliczeniowej, sieci itp.) nadal będzie naliczany w ramach subskrypcji.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Jak uzyskać pomoc techniczną rozwiązywania problemów z hotpatching?

* Możesz utworzyć bilet zgłoszenia [do pomocy technicznej.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) W polu Usługa wyszukaj i wybierz pozycję Maszyna wirtualna z **systemem Windows w** obszarze Obliczenia. Wybierz **pozycję Funkcje platformy Azure** dla typu problemu i automatyczne stosowanie **poprawek** gościa maszyny wirtualnej dla podtypu problemu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o usłudze Azure Update Management [tutaj.](../automation/update-management/overview.md)
* Dowiedz się więcej o automatycznym stosowaniem poprawek gościa maszyny wirtualnej [tutaj](../virtual-machines/automatic-vm-guest-patching.md)
