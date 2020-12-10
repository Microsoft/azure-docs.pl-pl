---
title: Uaktualnij z warstwy Podstawowa publicznie do standardowej Azure Load Balancer publicznej
description: W tym artykule opisano sposób uaktualniania Load Balancer publicznej platformy Azure z podstawowej jednostki SKU do standardowej jednostki SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: d54994a7c64718835e70381f92abed83ef693018
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938515"
---
# <a name="upgrade-azure-public-load-balancer"></a>Uaktualnij Load Balancer publicznej platformy Azure
[Usługa Azure usługa Load Balancer w warstwie Standardowa](load-balancer-overview.md) oferuje bogaty zestaw funkcji i wysokiej dostępności za pomocą nadmiarowości stref. Aby dowiedzieć się więcej na temat Load Balancer SKU, zobacz [tabela porównania](./skus.md#skus).

Uaktualnienie obejmuje dwa etapy:

1. Zmień metodę alokacji adresu IP z dynamicznej na statyczną.
2. Uruchom skrypt programu PowerShell, aby zakończyć uaktualnianie i migrację ruchu.

## <a name="upgrade-overview"></a>Omówienie uaktualnienia

Dostępny jest skrypt Azure PowerShell, który wykonuje następujące czynności:

* Tworzy standardową Load Balancer jednostki SKU z lokalizacją określoną w tej samej grupie zasobów usługa Load Balancer w warstwie Standardowa podstawowej.
* Uaktualnia publiczny adres IP z podstawowej jednostki SKU do lokalizacji w miejscu w warstwie Standardowa.
* Bezproblemowo kopiuje konfiguracje podstawowej jednostki SKU Load Balancer do nowo utworzonej usługa Load Balancer w warstwie Standardowa.
* Tworzy domyślną regułę ruchu wychodzącego, która umożliwia łączność wychodzącą.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Skrypt obsługuje tylko publiczne uaktualnienie Load Balancer. Aby uzyskać instrukcje dotyczące wewnętrznego uaktualnienia Load Balancer, zapoznaj się z [tą stroną](./upgrade-basicinternal-standard.md) .
* Przed uruchomieniem skryptu należy zmienić metodę alokacji publicznego adresu IP na "static". 
* Jeśli Load Balancer nie ma żadnej konfiguracji adresu IP frontonu lub puli zaplecza, można napotkać błąd podczas uruchamiania skryptu. Upewnij się, że nie są puste.

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>Zmień metodę alokacji publicznego adresu IP na statyczną

* * * Oto zalecane kroki:

    1. Aby wykonać zadania z tego przewodnika Szybki start, zaloguj się do witryny [Azure Portal](https://portal.azure.com).
 
    1. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie, a następnie wybierz **podstawowy publiczny adres IP skojarzony z podstawowym Load Balancer** z listy zasobów.
   
    1. W obszarze **Ustawienia** wybierz pozycję **konfiguracje**.
   
    1. W obszarze **przypisanie** wybierz pozycję **statyczny**.
    1. Wybierz pozycję **Zapisz**.
    >[!NOTE]
    >W przypadku maszyn wirtualnych, które mają publiczne adresy IP, konieczne będzie utworzenie standardowych adresów w pierwszej kolejności, w której ten sam adres IP nie jest gwarantowany. Usuń skojarzenie maszyn wirtualnych z podstawowymi adresami IP i skojarz je z nowo utworzonymi standardowymi adresami. Następnie będzie można wykonać instrukcje dotyczące dodawania maszyn wirtualnych do puli zaplecza usługa Load Balancer w warstwie Standardowa. 

* **Tworzenie nowych maszyn wirtualnych do dodania do pul zaplecza dla nowo utworzonej Load Balancer publicznej**.
    * Więcej instrukcji dotyczących tworzenia maszyn wirtualnych i kojarzenia ich z usługa Load Balancer w warstwie Standardowa można znaleźć [tutaj](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines).


## <a name="download-the-script"></a>Pobierz skrypt

Pobierz skrypt migracji z  [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0).
## <a name="use-the-script"></a>Używanie skryptu

W zależności od konfiguracji i preferencji lokalnego środowiska programu PowerShell dostępne są dwie opcje:

* Jeśli nie masz zainstalowanych modułów platformy Azure AZ lub nie chcesz odinstalować modułów Azure AZ modules, najlepszą opcją jest użycie `Install-Script` opcji do uruchomienia skryptu.
* Jeśli zachodzi potrzeba zachowania usługi Azure AZ modules, najlepszym trafieniem jest pobranie skryptu i uruchomienie go bezpośrednio.

Aby określić, czy masz zainstalowane moduły Azure AZ, uruchom polecenie `Get-InstalledModule -Name az` . Jeśli nie widzisz żadnych zainstalowanych modułów AZ, możesz użyć `Install-Script` metody.

### <a name="install-using-the-install-script-method"></a>Instalowanie przy użyciu metody Install-Script

Aby można było użyć tej opcji, na komputerze nie trzeba mieć zainstalowanych modułów platformy Azure. Jeśli są zainstalowane, następujące polecenie wyświetla błąd. Możesz odinstalować usługę Azure AZ module lub użyć innej opcji, aby ręcznie pobrać skrypt i uruchomić go.
  
Uruchom skrypt za pomocą następującego polecenia:

`Install-Script -Name AzurePublicLBUpgrade`

To polecenie instaluje również wymagane AZ modules.  

### <a name="install-using-the-script-directly"></a>Instalowanie przy użyciu skryptu bezpośrednio

Jeśli zainstalowano kilka modułów na platformie Azure i nie można ich odinstalować (lub nie chcesz ich odinstalować), możesz ręcznie pobrać skrypt, korzystając z karty **pobranej ręcznie** w linku pobierania skryptu. Skrypt zostanie pobrany jako plik pierwotny NUPKG. Aby zainstalować skrypt z tego pliku NUPKG, zobacz [ręczne pobieranie pakietu](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Aby uruchomić skrypt:

1. Użyj `Connect-AzAccount` , aby nawiązać połączenie z platformą Azure.

1. Użyj `Import-Module Az` do zaimportowania AZ modules.

1. Badanie wymaganych parametrów:

   * **oldRgName: [ciąg]: wymagane** — jest to grupa zasobów dla istniejących Load Balancer podstawowych, które mają zostać uaktualnione. Aby znaleźć tę wartość ciągu, przejdź do Azure Portal, wybierz podstawowe źródło Load Balancer i kliknij **Przegląd** usługi równoważenia obciążenia. Grupa zasobów znajduje się na tej stronie.
   * **oldLBName: [ciąg]: wymagane** — jest to nazwa istniejącego modułu, który ma zostać uaktualniony. 
   * **newLBName: [ciąg]: wymagane** — jest to nazwa usługa Load Balancer w warstwie Standardowa, która ma zostać utworzona.
1. Uruchom skrypt przy użyciu odpowiednich parametrów. Ukończenie tego procesu może potrwać od 5 do siedmiu minut.

    **Przykład**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newLbName "LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Tworzenie reguły ruchu wychodzącego dla połączenia wychodzącego

Postępuj zgodnie z [instrukcjami](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) , aby utworzyć regułę wychodzącą, aby móc
* Zdefiniuj wychodzące NAT od podstaw.
* Skalowanie i dostrajanie zachowania istniejącego wychodzącego translatora adresów sieciowych.

## <a name="common-questions"></a>Często zadawane pytania

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Czy istnieją jakieś ograniczenia dotyczące skryptu Azure PowerShell w celu migrowania konfiguracji z wersji od 1 do v2?

Tak. Zobacz [zastrzeżenia/ograniczenia](#caveatslimitations).

### <a name="how-long-does-the-upgrade-take"></a>Jak długo trwa uaktualnianie?

Ukończenie skryptu zwykle trwa około 5-10 minut i może trwać dłużej w zależności od złożoności konfiguracji Load Balancer. W związku z tym Pamiętaj o przestoju i zaplanuj pracę w trybie failover w razie potrzeby.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Czy skrypt Azure PowerShell przełączany jest również ruch z mojego podstawowego Load Balancer do nowo utworzonego usługa Load Balancer w warstwie Standardowa?

Tak. Skrypt Azure PowerShell nie tylko uaktualnia publiczny adres IP, kopiuje konfigurację z warstwy Podstawowa do usługa Load Balancer w warstwie Standardowa, ale również migruje maszynę wirtualną wyłącznie do nowo utworzonego standardowego Load Balancer publicznego. 

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Wystąpił problem z używaniem tego skryptu. Jak uzyskać pomoc?
  
Możesz wysłać wiadomość e-mail na adres slbupgradesupport@microsoft.com , otworzyć sprawę pomocy technicznej w ramach pomocy technicznej platformy Azure lub obie te czynności.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o usługa Load Balancer w warstwie Standardowa](load-balancer-overview.md)
