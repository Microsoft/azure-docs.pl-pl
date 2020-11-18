---
title: Uaktualnianie z podstawowego wewnętrznego do standardowego wewnętrznego — Azure Load Balancer
description: W tym artykule opisano sposób uaktualniania wewnętrznego Load Balancer platformy Azure z podstawowej jednostki SKU do standardowej jednostki SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: 59bf5eb22289238633b1f07c29a878bd0a9ae620
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696170"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Uaktualnij usługę Azure Internal Load Balancer — nie jest wymagane połączenie wychodzące
[Usługa Azure usługa Load Balancer w warstwie Standardowa](load-balancer-overview.md) oferuje bogaty zestaw funkcji i wysokiej dostępności za pomocą nadmiarowości stref. Aby dowiedzieć się więcej na temat Load Balancer SKU, zobacz [tabela porównania](./skus.md#skus).

W tym artykule wprowadzono skrypt programu PowerShell, który tworzy usługa Load Balancer w warstwie Standardowa z taką samą konfiguracją jak podstawowa Load Balancer, a także migruje ruch z podstawowych Load Balancer do usługa Load Balancer w warstwie Standardowa.

## <a name="upgrade-overview"></a>Omówienie uaktualnienia

Dostępny jest skrypt Azure PowerShell, który wykonuje następujące czynności:

* Tworzy standardowy wewnętrzny Load Balancer jednostki SKU w określonej lokalizacji. Należy pamiętać, że żadne [połączenie wychodzące](./load-balancer-outbound-connections.md) nie zostanie dostarczone przez standardową Load Balancer wewnętrzny.
* Bezproblemowo kopiuje konfiguracje podstawowej jednostki SKU Load Balancer do nowo utworzonego usługa Load Balancer w warstwie Standardowa.
* Bezproblemowo przenoś prywatne adresy IP z podstawowego Load Balancer do nowo utworzonego usługa Load Balancer w warstwie Standardowa.
* Bezproblemowo Przenieś maszyny wirtualne z puli zaplecza podstawowego Load Balancer do puli zaplecza usługa Load Balancer w warstwie Standardowa

### <a name="caveatslimitations"></a>Caveats\Limitations

* Skrypt obsługuje tylko wewnętrzne uaktualnienie Load Balancer, w którym nie jest wymagane połączenie wychodzące. Jeśli wymagane jest [połączenie wychodzące](./load-balancer-outbound-connections.md) dla niektórych maszyn wirtualnych, Skorzystaj z tej [strony](upgrade-InternalBasic-To-PublicStandard.md) , aby uzyskać instrukcje. 
* Podstawowa Load Balancer musi znajdować się w tej samej grupie zasobów co maszyny wirtualne zaplecza i karty sieciowe.
* Jeśli w innym regionie zostanie utworzony standardowy moduł równoważenia obciążenia, nie będzie możliwe skojarzenie maszyn wirtualnych istniejących w starym regionie z nowo utworzonymi usługa Load Balancer w warstwie Standardowa. Aby obejść to ograniczenie, należy utworzyć nową maszynę wirtualną w nowym regionie.
* Jeśli Load Balancer nie ma żadnej konfiguracji adresu IP frontonu lub puli zaplecza, można napotkać błąd podczas uruchamiania skryptu. Upewnij się, że nie są puste.

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>Zmień metodę alokacji adresu IP na statyczną dla konfiguracji adresu IP frontonu (Zignoruj ten krok, jeśli jest już statyczny)

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz podstawową Load Balancer z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **Konfiguracja adresu IP frontonu**, a następnie wybierz pierwszą konfigurację adresu IP frontonu. 

3. W obszarze **przypisywanie** wybierz pozycję **statyczny** .

4. Powtórz krok 3 dla wszystkich konfiguracji adresu IP frontonu podstawowego Load Balancer.


## <a name="download-the-script"></a>Pobierz skrypt

Pobierz skrypt migracji z  [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzureILBUpgrade/5.0).
## <a name="use-the-script"></a>Używanie skryptu

W zależności od konfiguracji i preferencji lokalnego środowiska programu PowerShell dostępne są dwie opcje:

* Jeśli nie masz zainstalowanych modułów platformy Azure AZ lub nie chcesz odinstalować modułów Azure AZ modules, najlepszą opcją jest użycie `Install-Script` opcji do uruchomienia skryptu.
* Jeśli zachodzi potrzeba zachowania usługi Azure AZ modules, najlepszym trafieniem jest pobranie skryptu i uruchomienie go bezpośrednio.

Aby określić, czy masz zainstalowane moduły Azure AZ, uruchom polecenie `Get-InstalledModule -Name az` . Jeśli nie widzisz żadnych zainstalowanych modułów AZ, możesz użyć `Install-Script` metody.

### <a name="install-using-the-install-script-method"></a>Instalowanie przy użyciu metody Install-Script

Aby można było użyć tej opcji, na komputerze nie trzeba mieć zainstalowanych modułów platformy Azure. Jeśli są zainstalowane, następujące polecenie wyświetla błąd. Możesz odinstalować usługę Azure AZ module lub użyć innej opcji, aby ręcznie pobrać skrypt i uruchomić go.
  
Uruchom skrypt za pomocą następującego polecenia:

`Install-Script -Name AzureILBUpgrade`

To polecenie instaluje również wymagane AZ modules.  

### <a name="install-using-the-script-directly"></a>Instalowanie przy użyciu skryptu bezpośrednio

Jeśli zainstalowano kilka modułów na platformie Azure i nie można ich odinstalować (lub nie chcesz ich odinstalować), możesz ręcznie pobrać skrypt, korzystając z karty **pobranej ręcznie** w linku pobierania skryptu. Skrypt zostanie pobrany jako plik pierwotny NUPKG. Aby zainstalować skrypt z tego pliku NUPKG, zobacz [ręczne pobieranie pakietu](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Aby uruchomić skrypt:

1. Użyj `Connect-AzAccount` , aby nawiązać połączenie z platformą Azure.

1. Użyj `Import-Module Az` do zaimportowania AZ modules.

1. Badanie wymaganych parametrów:

   * **rgName: [ciąg]: wymagane** — jest to grupa zasobów dla istniejących Load Balancer podstawowych i nowych usługa Load Balancer w warstwie Standardowa. Aby znaleźć tę wartość ciągu, przejdź do Azure Portal, wybierz podstawowe źródło Load Balancer i kliknij **Przegląd** usługi równoważenia obciążenia. Grupa zasobów znajduje się na tej stronie.
   * **oldLBName: [ciąg]: wymagane** — jest to nazwa istniejącego modułu, który ma zostać uaktualniony. 
   * **newLocation: [ciąg]: wymagane** — lokalizacja, w której zostanie utworzony usługa Load Balancer w warstwie Standardowa. Zaleca się, aby dziedziczyć tę samą lokalizację wybranych Load Balancer podstawowych do usługa Load Balancer w warstwie Standardowa w celu lepszego skojarzenia z innymi istniejącymi zasobami.
   * **newLBName: [ciąg]: wymagane** — jest to nazwa usługa Load Balancer w warstwie Standardowa, która ma zostać utworzona.
1. Uruchom skrypt przy użyciu odpowiednich parametrów. Ukończenie tego procesu może potrwać od 5 do siedmiu minut.

    **Przykład**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Często zadawane pytania

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Czy istnieją jakieś ograniczenia dotyczące skryptu Azure PowerShell w celu migrowania konfiguracji z wersji od 1 do v2?

Tak. Zobacz [zastrzeżenia/ograniczenia](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Czy skrypt Azure PowerShell przełączany jest również ruch z mojego podstawowego Load Balancer do nowo utworzonego usługa Load Balancer w warstwie Standardowa?

Tak, aby migruje ruch. Jeśli chcesz migrować ruch danych osobowych, użyj [tego skryptu](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) , który nie przenosi maszyn wirtualnych.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Wystąpił problem z używaniem tego skryptu. Jak uzyskać pomoc?
  
Możesz wysłać wiadomość e-mail na adres slbupgradesupport@microsoft.com , otworzyć sprawę pomocy technicznej w ramach pomocy technicznej platformy Azure lub obie te czynności.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o usługa Load Balancer w warstwie Standardowa](load-balancer-overview.md)