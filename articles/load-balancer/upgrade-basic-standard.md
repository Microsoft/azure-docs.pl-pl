---
title: Uaktualnienie z podstawowego publicznego do standardowego publicznego — Azure Load Balancer
description: W tym artykule pokazano, jak uaktualnić moduł azure public load balancer z podstawowej jednostki SKU do standardowej jednostki SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: a2d6f41756d87e43ac7db9e6a8670c453920c834
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770365"
---
# <a name="upgrade-azure-public-load-balancer"></a>Uaktualnianie publicznego modułu równoważenia obciążenia usługi Azure
[Azure Standard Load Balancer](load-balancer-overview.md) oferuje bogaty zestaw funkcji i wysoką dostępność dzięki nadmiarowości strefowej. Aby dowiedzieć się więcej o jednostce SKU modułu równoważenia obciążenia, zobacz [tabelę porównawczą](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Istnieją trzy etapy uaktualnienia:

1. Migrowanie konfiguracji
2. Dodawanie maszyn wirtualnych do pul zaplecza standardowego modułu równoważenia obciążenia

W tym artykule opisano migrację konfiguracji. Dodawanie maszyn wirtualnych do pul wewnętrznej bazy danych może się różnić w zależności od określonego środowiska. Jednak niektóre wysokiego szczebla, ogólne zalecenia [są dostarczane](#add-vms-to-backend-pools-of-standard-load-balancer).

## <a name="upgrade-overview"></a>Omówienie uaktualnienia

Dostępny jest skrypt programu Azure PowerShell, który wykonuje następujące czynności:

* Tworzy standardowy moduł równoważenia obciążenia jednostki SKU w grupie zasobów i lokalizacji określonej przez użytkownika.
* Bezproblemowo kopiuje konfiguracje modułu równoważenia obciążenia podstawowej jednostki SKU do nowo utworzonego standardowego modułu równoważenia obciążenia.
* Tworzy domyślną regułę ruchu wychodzącego, która umożliwia łączność wychodzącą.

### <a name="caveatslimitations"></a>Zastrzeżenia\Ograniczenia

* Skrypt obsługuje tylko uaktualnienie modułu równoważenia obciążenia publicznego. Aby uzyskać instrukcje dotyczące aktualizacji wewnętrznego podstawowego modułu równoważenia obciążenia, zapoznaj się z [tą stroną.](https://docs.microsoft.com/azure/load-balancer/upgrade-basicinternal-standard)
* Standardowy moduł równoważenia obciążenia ma nowy adres publiczny. Nie można bezproblemowo przenieść adresów IP skojarzonych z istniejącym podstawowym modułem równoważenia obciążenia do standardowego modułu równoważenia obciążenia, ponieważ mają one różne jednostki SKU.
* Jeśli standardowy moduł równoważenia obciążenia jest tworzony w innym regionie, nie będzie można skojarzyć maszyn wirtualnych istniejących w starym regionie z nowo utworzonym modułem równoważenia obciążenia standardowego. Aby obejść to ograniczenie, upewnij się, że utworzysz nową maszynę wirtualną w nowym regionie.
* Jeśli moduł równoważenia obciążenia nie ma żadnej konfiguracji ip frontendu lub puli wewnętrznej bazy danych, prawdopodobnie zostanie wyświetlony błąd z uruchomieniem skryptu. Upewnij się, że nie są puste.

## <a name="download-the-script"></a>Pobierz skrypt

Pobierz skrypt migracji z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/2.0).
## <a name="use-the-script"></a>Korzystanie ze skryptu

Istnieją dwie opcje w zależności od lokalnej konfiguracji środowiska programu PowerShell i preferencji:

* Jeśli nie masz zainstalowanych modułów Az platformy Azure lub nie masz nic przeciwko odinstalowaniu modułów `Install-Script` usługi Azure Az, najlepszą opcją jest użycie opcji uruchomienia skryptu.
* Jeśli chcesz zachować moduły usługi Azure Az, najlepiej jest pobrać skrypt i uruchomić go bezpośrednio.

Aby ustalić, czy masz zainstalowane moduły `Get-InstalledModule -Name az`Az platformy Azure, uruchom program . Jeśli nie widzisz żadnych zainstalowanych modułów Az, `Install-Script` możesz użyć tej metody.

### <a name="install-using-the-install-script-method"></a>Instalowanie przy użyciu metody Install-Script

Aby użyć tej opcji, nie może mieć modułów Az platformy Azure zainstalowanych na komputerze. Jeśli są zainstalowane, następujące polecenie wyświetla błąd. Można odinstalować moduły usługi Azure Az lub użyć innej opcji, aby ręcznie pobrać skrypt i uruchomić go.
  
Uruchom skrypt za pomocą następującego polecenia:

`Install-Script -Name AzurePublicLBUpgrade`

To polecenie instaluje również wymagane moduły Az.  

### <a name="install-using-the-script-directly"></a>Instalowanie bezpośrednio przy użyciu skryptu

Jeśli masz zainstalowane moduły usługi Azure Az i nie możesz ich odinstalować (lub nie chcesz ich odinstalowywać), możesz ręcznie pobrać skrypt za pomocą karty **Ręczne pobieranie** w linku do pobierania skryptu. Skrypt jest pobierany jako nieprzetworzony plik nupkg. Aby zainstalować skrypt z tego pliku nupkg, zobacz [Ręczne pobieranie pakietu](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Aby uruchomić skrypt:

1. Służy `Connect-AzAccount` do łączenia się z platformą Azure.

1. Służy `Import-Module Az` do importowania modułów Az.

1. Sprawdź wymagane parametry:

   * **oldRgName: [Ciąg]: Wymagane** — jest to grupa zasobów dla istniejącego podstawowego modułu równoważenia obciążenia, który chcesz uaktualnić. Aby znaleźć tę wartość ciągu, przejdź do witryny Azure portal, wybierz źródło podstawowego modułu równoważenia obciążenia i kliknij **omówienie** modułu równoważenia obciążenia. Grupa zasobów znajduje się na tej stronie.
   * **oldLBName: [Ciąg]: Wymagane** — jest to nazwa istniejącego salda podstawowego, który chcesz uaktualnić. 
   * **newrgName: [Ciąg]: Wymagane** — jest to grupa zasobów, w której zostanie utworzony standardowy moduł równoważenia obciążenia. Może to być nowa grupa zasobów lub istniejąca. Jeśli wybierzesz istniejącą grupę zasobów, należy pamiętać, że nazwa modułu równoważenia obciążenia musi być unikatowa w grupie zasobów. 
   * **newlocation: [Ciąg]: Wymagane** — jest to lokalizacja, w której zostanie utworzony standardowy moduł równoważenia obciążenia. Zaleca się dziedziczenie tej samej lokalizacji wybranego podstawowego modułu równoważenia obciążenia do standardowego modułu równoważenia obciążenia w celu lepszego skojarzenia z innymi istniejącymi zasobami.
   * **newLBName: [Ciąg]: Wymagane** — jest to nazwa standardowego modułu równoważenia obciążenia, który ma zostać utworzony.
1. Uruchom skrypt przy użyciu odpowiednich parametrów. Może upłynąć od pięciu do siedmiu minut, aby zakończyć.

    **Przykład**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Dodawanie maszyn wirtualnych do pul zaplecza standardowego modułu równoważenia obciążenia

Najpierw należy dokładnie sprawdzić, czy skrypt pomyślnie utworzył nowy standardowy moduł równoważenia obciążenia publicznego z dokładną konfiguracją migrowaną z podstawowego modułu równoważenia obciążenia publicznego. Można to sprawdzić za pomocą witryny Azure portal.

Pamiętaj, aby wysłać niewielką ilość ruchu za pośrednictwem standardowego modułu równoważenia obciążenia jako test ręczny.
  
Oto kilka scenariuszy, jak dodać maszyny wirtualne do pul zaplecza nowo utworzonego standardowego modułu równoważenia obciążenia publicznego, a nasze zalecenia dla każdego z nich:

* **Przenoszenie istniejących maszyn wirtualnych z pul zaplecza starego podstawowego modułu równoważenia obciążenia publicznego do pul zaplecza nowo utworzonego standardowego modułu równoważenia obciążenia publicznego**.
    1. Aby wykonać zadania z tego przewodnika Szybki start, zaloguj się do witryny [Azure Portal](https://portal.azure.com).
 
    1. Wybierz **pozycję Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz z listy zasobów nowo utworzony **standardowy moduł równoważenia obciążenia.**
   
    1. W obszarze **Ustawienia**wybierz pozycję **Pule wewnętrznej bazy danych**.
   
    1. Wybierz pulę wewnętrznej bazy danych, która odpowiada puli wewnętrznej bazy danych modułu równoważenia obciążenia podstawowego, wybierz następującą wartość: 
      - **Maszyna wirtualna:** Rozwijana i wybierz maszyny wirtualne z pasującej puli wewnętrznej bazy danych modułu równoważenia obciążenia podstawowego.
    1. Wybierz pozycję **Zapisz**.
    >[!NOTE]
    >W przypadku maszyn wirtualnych, które mają publiczne adresy IP, należy najpierw utworzyć standardowe adresy IP, w przypadku gdy ten sam adres IP nie jest gwarantowany. Odłączyć maszyny wirtualne od podstawowych adresów IP i skojarzyć je z nowo utworzonych standardowych adresów IP. Następnie będzie można wykonać instrukcje, aby dodać maszyny wirtualne do puli wewnętrznej bazy danych modułu równoważenia obciążenia standardowego. 

* **Tworzenie nowych maszyn wirtualnych do dodania do pul wewnętrznej bazy danych nowo utworzonego standardowego modułu równoważenia obciążenia publicznego**.
    * Więcej instrukcji dotyczących tworzenia maszyn wirtualnych i kojarzenia jej ze standardowym modułem równoważenia obciążenia można znaleźć [tutaj](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Tworzenie reguły ruchu wychodzącego dla połączenia wychodzącego

Postępuj zgodnie z [instrukcjami,](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration) aby utworzyć regułę ruchu wychodzącego, aby
* Zdefiniuj wychodzący NAT od podstaw.
* Skaluj i dostrajaj zachowanie istniejącego wychodzącego translatora treści.

## <a name="common-questions"></a>Często zadawane pytania

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Czy istnieją jakieś ograniczenia ze skryptem programu Azure PowerShell w celu migracji konfiguracji z wersji 1 do wersji 2?

Tak. Zobacz [Zastrzeżenia/Ograniczenia](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Czy skrypt programu Azure PowerShell przełącza się również za ruch z mojego podstawowego modułu równoważenia obciążenia na nowo utworzony standardowy moduł równoważenia obciążenia?

Nie. Skrypt programu Azure PowerShell tylko migruje konfigurację. Rzeczywista migracja ruchu jest twoim obowiązkiem i kontrolą.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Wpadłem na pewne problemy z wykorzystaniem tego skryptu. Jak mogę uzyskać pomoc?
  
Możesz wysłać wiadomość slbupgradesupport@microsoft.come-mail do , otwórz sprawę pomocy technicznej za pomocą pomocy technicznej platformy Azure lub wykonaj obie te usługi.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o standardowym równoważącym obciążenia](load-balancer-overview.md)
