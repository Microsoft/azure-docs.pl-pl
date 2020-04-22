---
title: Uaktualnienie z podstawowego wewnętrznego do standardowego wewnętrznego — moduł równoważenia obciążenia platformy Azure
description: W tym artykule pokazano, jak uaktualnić wewnętrzny moduł równoważenia obciążenia platformy Azure z podstawowej jednostki SKU do standardowej jednostki SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770387"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Uaktualnianie wewnętrznego modułu równoważenia obciążenia platformy Azure — nie jest wymagane połączenie wychodzące
[Azure Standard Load Balancer](load-balancer-overview.md) oferuje bogaty zestaw funkcji i wysoką dostępność dzięki nadmiarowości strefowej. Aby dowiedzieć się więcej o jednostce SKU modułu równoważenia obciążenia, zobacz [tabelę porównawczą](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

W tym artykule przedstawiono skrypt programu PowerShell, który tworzy standardowy moduł równoważenia obciążenia z taką samą konfiguracją jak podstawowy moduł równoważenia obciążenia wraz z migracją ruchu z podstawowego modułu równoważenia obciążenia do standardowego modułu równoważenia obciążenia.

## <a name="upgrade-overview"></a>Omówienie uaktualnienia

Dostępny jest skrypt programu Azure PowerShell, który wykonuje następujące czynności:

* Tworzy standardowy wewnętrzny moduł równoważenia obciążenia jednostki SKU w określonej lokalizacji. Należy zauważyć, że żadne [połączenie wychodzące](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) nie zostanie dostarczone przez standardowy wewnętrzny moduł równoważenia obciążenia.
* Bezproblemowo kopiuje konfiguracje modułu równoważenia obciążenia podstawowej jednostki SKU do nowo utworzonego standardowego modułu równoważenia obciążenia.
* Bezproblemowo przenieś prywatne adresy IP z podstawowego modułu równoważenia obciążenia do nowo utworzonego standardowego modułu równoważenia obciążenia.
* Bezproblemowe przenoszenie maszyn wirtualnych z puli wewnętrznej bazy danych modułu równoważenia obciążenia podstawowego do puli wewnętrznej bazy danych standardowego modułu równoważenia obciążenia

### <a name="caveatslimitations"></a>Zastrzeżenia\Ograniczenia

* Skrypt obsługuje tylko uaktualnienie modułu wewnętrznego równoważenia obciążenia, w przypadku gdy nie jest wymagane połączenie wychodzące. Jeśli wymagane [jest połączenie wychodzące](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) dla niektórych maszyn wirtualnych, zapoznaj się z tą [stroną,](upgrade-InternalBasic-To-PublicStandard.md) aby uzyskać instrukcje. 
* Jeśli standardowy moduł równoważenia obciążenia jest tworzony w innym regionie, nie będzie można skojarzyć maszyn wirtualnych istniejących w starym regionie z nowo utworzonym modułem równoważenia obciążenia standardowego. Aby obejść to ograniczenie, upewnij się, że utworzysz nową maszynę wirtualną w nowym regionie.
* Jeśli moduł równoważenia obciążenia nie ma żadnej konfiguracji ip frontendu lub puli wewnętrznej bazy danych, prawdopodobnie zostanie wyświetlony błąd z uruchomieniem skryptu. Upewnij się, że nie są puste.

## <a name="download-the-script"></a>Pobierz skrypt

Pobierz skrypt migracji z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0).
## <a name="use-the-script"></a>Korzystanie ze skryptu

Istnieją dwie opcje w zależności od lokalnej konfiguracji środowiska programu PowerShell i preferencji:

* Jeśli nie masz zainstalowanych modułów Az platformy Azure lub nie masz nic przeciwko odinstalowaniu modułów `Install-Script` usługi Azure Az, najlepszą opcją jest użycie opcji uruchomienia skryptu.
* Jeśli chcesz zachować moduły usługi Azure Az, najlepiej jest pobrać skrypt i uruchomić go bezpośrednio.

Aby ustalić, czy masz zainstalowane moduły `Get-InstalledModule -Name az`Az platformy Azure, uruchom program . Jeśli nie widzisz żadnych zainstalowanych modułów Az, `Install-Script` możesz użyć tej metody.

### <a name="install-using-the-install-script-method"></a>Instalowanie przy użyciu metody Install-Script

Aby użyć tej opcji, nie może mieć modułów Az platformy Azure zainstalowanych na komputerze. Jeśli są zainstalowane, następujące polecenie wyświetla błąd. Można odinstalować moduły usługi Azure Az lub użyć innej opcji, aby ręcznie pobrać skrypt i uruchomić go.
  
Uruchom skrypt za pomocą następującego polecenia:

`Install-Script -Name AzureILBUpgrade`

To polecenie instaluje również wymagane moduły Az.  

### <a name="install-using-the-script-directly"></a>Instalowanie bezpośrednio przy użyciu skryptu

Jeśli masz zainstalowane moduły usługi Azure Az i nie możesz ich odinstalować (lub nie chcesz ich odinstalowywać), możesz ręcznie pobrać skrypt za pomocą karty **Ręczne pobieranie** w linku do pobierania skryptu. Skrypt jest pobierany jako nieprzetworzony plik nupkg. Aby zainstalować skrypt z tego pliku nupkg, zobacz [Ręczne pobieranie pakietu](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Aby uruchomić skrypt:

1. Służy `Connect-AzAccount` do łączenia się z platformą Azure.

1. Służy `Import-Module Az` do importowania modułów Az.

1. Sprawdź wymagane parametry:

   * **rgName: [Ciąg]: Wymagane** — jest to grupa zasobów dla istniejącego podstawowego modułu równoważenia obciążenia i nowego standardowego modułu równoważenia obciążenia. Aby znaleźć tę wartość ciągu, przejdź do witryny Azure portal, wybierz źródło podstawowego modułu równoważenia obciążenia i kliknij **omówienie** modułu równoważenia obciążenia. Grupa zasobów znajduje się na tej stronie.
   * **oldLBName: [Ciąg]: Wymagane** — jest to nazwa istniejącego salda podstawowego, który chcesz uaktualnić. 
   * **newlocation: [Ciąg]: Wymagane** — jest to lokalizacja, w której zostanie utworzony standardowy moduł równoważenia obciążenia. Zaleca się dziedziczenie tej samej lokalizacji wybranego podstawowego modułu równoważenia obciążenia do standardowego modułu równoważenia obciążenia w celu lepszego skojarzenia z innymi istniejącymi zasobami.
   * **newLBName: [Ciąg]: Wymagane** — jest to nazwa standardowego modułu równoważenia obciążenia, który ma zostać utworzony.
1. Uruchom skrypt przy użyciu odpowiednich parametrów. Może upłynąć od pięciu do siedmiu minut, aby zakończyć.

    **Przykład**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Często zadawane pytania

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Czy istnieją jakieś ograniczenia ze skryptem programu Azure PowerShell w celu migracji konfiguracji z wersji 1 do wersji 2?

Tak. Zobacz [Zastrzeżenia/Ograniczenia](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Czy skrypt programu Azure PowerShell przełącza się również za ruch z mojego podstawowego modułu równoważenia obciążenia na nowo utworzony standardowy moduł równoważenia obciążenia?

Tak, migruje ruch. Jeśli chcesz przeprowadzić migrację ruchu osobiście, użyj [tego skryptu,](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) który nie przenosi maszyn wirtualnych.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Wpadłem na pewne problemy z wykorzystaniem tego skryptu. Jak mogę uzyskać pomoc?
  
Możesz wysłać wiadomość slbupgradesupport@microsoft.come-mail do , otwórz sprawę pomocy technicznej za pomocą pomocy technicznej platformy Azure lub wykonaj obie te usługi.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o standardowym równoważącym obciążenia](load-balancer-overview.md)
