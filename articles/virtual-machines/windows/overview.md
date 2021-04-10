---
title: Omówienie maszyn wirtualnych z systemem Windows na platformie Azure
description: Omówienie maszyn wirtualnych z systemem Windows na platformie Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 799a7ea6d76df06cea9d3960f43fc78de9bdf5b6
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067796"
---
# <a name="windows-virtual-machines-in-azure"></a>Maszyny wirtualne z systemem Windows na platformie Azure

Usługa Azure Virtual Machines (VM) to jeden z wielu [skalowalnych zasobów obliczeniowych dostępnych na żądanie](/azure/architecture/guide/technology-choices/compute-decision-tree), które są oferowane na platformie Azure. W większości przypadków maszynę wirtualną należy wybrać wtedy, gdy potrzebna jest większa kontrola nad środowiskiem obliczeniowym niż ta, jaką oferują inne opcje. Ten artykuł zawiera informacje o kwestiach, jakie należy rozważyć przed przystąpieniem do tworzenia maszyny wirtualnej. Opisano w nim także tworzenie maszyny wirtualnej i zarządzanie nią.

Maszyna wirtualna Azure umożliwia swobodne korzystanie z wirtualizacji bez konieczności kupowania i utrzymywania fizycznego sprzętu potrzebnego do jej działania. Nadal niezbędna jest jednak konserwacja maszyny wirtualnej — konfigurowanie i instalowanie jej oprogramowania, a także instalowanie poprawek tego oprogramowania.

Z maszyn wirtualnych na platformie Azure można korzystać na różne sposoby. Przykłady to:

* **Tworzenie i testowanie oprogramowania** — maszyny wirtualne umożliwiają szybkie i łatwe utworzenie komputera o określonej konfiguracji wymaganej do tworzenia kodu oraz testowania aplikacji.
* **Aplikacje w chmurze** — jako że zapotrzebowanie na aplikację może zmieniać się w czasie, uzasadnione ekonomicznie może okazać się uruchomienie jej na maszynie wirtualnej na platformie Azure. Jeśli zajdzie potrzeba użycia dodatkowych maszyn wirtualnych, wystarczy je opłacić. Gdy dodatkowe maszyny staną się zbędne, można je wyłączyć.
* **Rozszerzone centrum danych** — maszyny wirtualne w sieci wirtualnej na platformie Azure można zostać z łatwością połączyć z siecią organizacji.

Liczbę maszyn wirtualnych używanych przez aplikację można dowolnie i bez ograniczeń zwiększać odpowiednio do potrzeb.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Co należy wziąć pod uwagę przed utworzeniem maszyny wirtualnej?
Podczas tworzenia infrastruktury aplikacji na platformie Azure należy zawsze wziąć pod uwagę wiele różnych [zagadnień projektowych](/azure/architecture/reference-architectures/n-tier/windows-vm). Kwestie dotyczące maszyn wirtualnych, które należy rozważyć, to między innymi:

* nazwy zasobów aplikacji;
* lokalizacja, w której są przechowywane zasoby;
* rozmiar maszyny wirtualnej;
* maksymalna liczba maszyn wirtualnych, które można utworzyć;
* system operacyjny uruchomiony na maszynie wirtualnej;
* konfiguracja maszyny wirtualnej po jej uruchomieniu;
* powiązane zasoby niezbędne do działania maszyny wirtualnej.

### <a name="locations"></a>Lokalizacje
Wszystkie zasoby tworzone na platformie Azure są dystrybuowane w obrębie wielu [regionów geograficznych](https://azure.microsoft.com/regions/) na całym świecie. Zazwyczaj podczas tworzenia maszyny wirtualnej region określa się mianem **lokalizacji**. W przypadku maszyny wirtualnej lokalizacja określa miejsce, w którym są przechowywane wirtualne dyski twarde.

W poniższej tabeli przedstawiono wybrane metody uzyskania dostępu do listy dostępnych lokalizacji.

| Metoda | Opis |
| --- | --- |
| Azure Portal |Wybór lokalizacji z listy podczas tworzenia maszyny wirtualnej. |
| Azure PowerShell |Użyj polecenia [Get-AzLocation](/powershell/module/az.resources/get-azlocation). |
| Interfejs API REST |Użycie operacji [wyświetlania listy lokalizacji](/rest/api/resources/subscriptions/subscriptions/listlocations). |
| Interfejs wiersza polecenia platformy Azure |Użyj operacji [az account list-locations](/cli/azure/account). |

## <a name="availability"></a>Dostępność
Platforma Azure oferuje najlepszą w branży umowę dotyczącą poziomu usług gwarantującą dostępność pojedynczego wystąpienia maszyny wirtualnej na poziomie 99,9% pod warunkiem wdrożenia tej maszyny wirtualnej z magazynem Premium dla wszystkich dysków.  Aby wdrożenie kwalifikowało się do standardowej umowy dotyczącej poziomu usług na poziomie 99,95%, należy wdrożyć co najmniej dwie maszyny wirtualne obsługujące obciążenie w zestawie dostępności. Zestaw dostępności gwarantuje, że maszyny wirtualne są rozproszone w wielu domenach błędów w centrach danych platformy Azure oraz wdrożone na hostach z różnymi okresami konserwacji. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.


## <a name="vm-size"></a>Rozmiar maszyny wirtualnej
[Rozmiar](../sizes.md) maszyny wirtualnej zależy od obciążenia, które ma zostać uruchomione. Wybrany rozmiar ma więc wpływ na takie czynniki jak moc procesora, pamięć i przestrzeń dyskowa. W ramach platformy Azure dostępna jest szeroka gama rozmiarów umożliwiających wykorzystanie produktu do wielu różnych zastosowań.

Na platformie Azure jest naliczana [Cena godzinowa](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) w oparciu o rozmiar i system operacyjny maszyny wirtualnej. W przypadku rozpoczętych godzin opłaty są pobierane tylko za faktycznie wykorzystane minuty. Magazyn jest wyceniany oddzielnie; związane z nim opłaty są także pobierane osobno.

## <a name="vm-limits"></a>Limity maszyn wirtualnych
Subskrypcje mają domyślne [limity przydziałów](../../azure-resource-manager/management/azure-subscription-service-limits.md), które mogą mieć wpływ na wdrożenie wielu maszyn wirtualnych w projekcie. Aktualny limit dla każdej subskrypcji wynosi 20 maszyn wirtualnych na region. Limity można zwiększyć, [wypełniając odpowiednio bilet pomocy technicznej](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

### <a name="operating-system-disks-and-images"></a>Dyski i obrazy z systemem operacyjnym
Maszyny wirtualne używają [wirtualnych dysków twardych (VHD)](../managed-disks-overview.md) do przechowywania systemu operacyjnego i danych. Wirtualne dyski twarde są również używane do obsługi obrazów, spośród których można wybierać, chcąc zainstalować system operacyjny. 

Na platformie Azure jest dostępnych wiele [obrazów z portalu Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images%3Bwindows&page=1), których można używać z różnymi wersjami i typami systemów operacyjnych Windows Server. Obrazy z Marketplace są oznaczone nazwą wydawcy i oferty, jednostką SKU i wersją (zwykle najnowszą). Obsługiwane są tylko 64-bitowe systemy operacyjne. Aby uzyskać więcej informacji dotyczących obsługiwanych systemów operacyjnych gościa, ról oraz funkcji, zobacz temat [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Obsługa oprogramowania serwera Microsoft maszyn wirtualnych Microsoft Azure).

W poniższej tabeli pokazano, jak można znaleźć informacje o obrazie.

| Metoda | Opis |
| --- | --- |
| Azure Portal |Wartości są podawane automatycznie po wybraniu obrazu, który ma zostać użyty. |
| Azure PowerShell |[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) -Location *lokalizacja*<BR>[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) -Location *lokalizacja* -Publisher *nazwaWydawcy*<BR>[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) -Location *lokalizacja* -Publisher *nazwaWydawcy* -Offer *nazwaOferty* |
| Interfejsy API REST |[Wyświetl listę wydawców obrazów](/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Wyświetl listę ofert obrazów](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Wyświetl listę jednostek SKU obrazów](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Interfejs wiersza polecenia platformy Azure |[az vm image list-publishers](/cli/azure/vm/image) --location *lokalizacja*<BR>[AZ VM Image list-offers](/cli/azure/vm/image) *-Lokalizacja lokalizacji--* Publisher *PublisherName*<BR>[az vm image list-skus](/cli/azure/vm) --location *lokalizacja* --publisher *nazwa_wydawcy* --offer *nazwa_oferty*|

Istnieje możliwość [przesłania i użycia własnego obrazu](upload-generalized-managed.md) — nazwa wydawcy, oferta i jednostka SKU nie są wtedy używane.

### <a name="extensions"></a>Rozszerzenia
[Rozszerzenia](../extensions/features-windows.md?toc=/azure/virtual-machines/windows/toc.json) maszyn wirtualnych udostępniają dodatkowe funkcje, z których można korzystać po wdrożeniu i do automatyzacji zadań.

Typowe zadania można realizować przy użyciu różnych rozszerzeń:

* **Uruchamianie skryptów niestandardowych** — [rozszerzenie niestandardowego skryptu](../extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json) ułatwia konfigurowanie obciążeń na maszynie wirtualnej poprzez uruchomienie skryptu po aprowizacji maszyny wirtualnej.
* **Wdrażanie konfiguracji i zarządzanie nimi** — [rozszerzenie Konfiguracja żądanego stanu (Desired State Configuration, DSC) programu PowerShell](../extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) ułatwia skonfigurowanie żądanego stanu na maszynie wirtualnej w celu zarządzania konfiguracjami i środowiskami.
* **Zbieranie danych diagnostycznych** — [rozszerzenie Diagnostyka Azure](../extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json) ułatwia skonfigurowanie maszyny wirtualnej w celu zbierania danych diagnostycznych używanych do monitorowania kondycji aplikacji.

### <a name="related-resources"></a>Powiązane zasoby
Zasoby wymienione w tej tabeli są używane przez maszynę wirtualną i muszą istnieć lub zostać utworzone podczas jej tworzenia.

| Zasób | Wymagane | Opis |
| --- | --- | --- |
| [Grupa zasobów](../../azure-resource-manager/management/overview.md) |Tak |Maszyna wirtualna musi być zawarta w grupie zasobów. |
| [Konto magazynu](../../storage/common/storage-account-create.md) |Tak |Maszyna wirtualna wymaga konta magazynu do przechowywania wirtualnych dysków twardych. |
| [Sieć wirtualna](../../virtual-network/virtual-networks-overview.md) |Tak |Maszyna wirtualna musi należeć do sieci wirtualnej. |
| [Publiczny adres IP](../../virtual-network/public-ip-addresses.md) |Nie |Maszyna wirtualna może mieć przypisany publiczny adres IP umożliwiający uzyskiwanie do niej dostępu zdalnego. |
| [Interfejs sieciowy](../../virtual-network/virtual-network-network-interface.md) |Tak |Maszyna wirtualna wymaga interfejsu sieciowego do komunikacji w sieci. |
| [Dyski danych](attach-managed-disk-portal.md) |Nie |Maszyna wirtualna może zawierać dyski danych zwiększające jej pojemność. |


## <a name="data-residency"></a>Rezydencja danych

Na platformie Azure funkcja umożliwiająca przechowywanie danych klienta w jednym regionie jest obecnie dostępna tylko w regionie Azja Południowo-Wschodnia (Singapur) Azja i Pacyfik regionie geograficznym i Brazylia Południowa (stan Świętego Paulo) regionu Brazylia. W przypadku wszystkich innych regionów dane klienta są przechowywane w lokalizacji geograficznej. Aby uzyskać więcej informacji, zobacz [Centrum zaufania](https://azure.microsoft.com/global-infrastructure/data-residency/).


## <a name="next-steps"></a>Następne kroki

Utwórz pierwszą maszynę wirtualną.

- [Portal](quick-create-portal.md)
- [Program PowerShell](quick-create-powershell.md)
- [Interfejs wiersza polecenia platformy Azure](quick-create-cli.md)
