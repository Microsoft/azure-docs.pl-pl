---
title: Zbieranie niestandardowych metryk dla maszyny wirtualnej z systemem Linux za pomocą agenta InfluxData telegraf
description: Instrukcje dotyczące sposobu wdrażania agenta InfluxData telegraf na maszynie wirtualnej z systemem Linux na platformie Azure i konfigurowania agenta do publikowania metryk w Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: fbed48dda3d6b022d30bb9fb0507c3f9f203771d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613252"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Zbieranie niestandardowych metryk dla maszyny wirtualnej z systemem Linux za pomocą agenta InfluxData telegraf

Korzystając z Azure Monitor, można zbierać niestandardowe metryki za pośrednictwem telemetrii aplikacji, agenta uruchomionego na zasobach platformy Azure, a nawet poza systemami monitorowania. Następnie można przesłać je bezpośrednio do Azure Monitor. Ten artykuł zawiera instrukcje dotyczące wdrażania agenta [InfluxData](https://www.influxdata.com/) telegraf na maszynie wirtualnej z systemem Linux na platformie Azure i konfigurowania agenta do publikowania metryk w Azure monitor. 

## <a name="influxdata-telegraf-agent"></a>Agent InfluxData telegraf 

[Telegraf](https://docs.influxdata.com/telegraf/) jest agentem opartym na wtyczkach, który umożliwia zbieranie metryk z ponad 150 różnych źródeł. W zależności od obciążenia uruchomionego na maszynie wirtualnej można skonfigurować agenta, aby korzystał z wyspecjalizowanych wtyczek wejściowych do zbierania metryk. Przykłady to MySQL, NGINX i Apache. Korzystając z wtyczek wyjściowych, Agent może następnie zapisywać do wybranych miejsc docelowych. Agent telegraf został zintegrowany bezpośrednio z interfejsem API REST niestandardowych metryk Azure Monitor. Obsługuje ona wtyczkę wyjściową Azure Monitor. Za pomocą tej wtyczki Agent może zbierać metryki specyficzne dla obciążeń na maszynie wirtualnej z systemem Linux i przesyłać je jako metryki niestandardowe do Azure Monitor. 

 ![Omówienie agenta telegraficznego](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

> [!NOTE]  
> Metryki niestandardowe nie są obsługiwane we wszystkich regionach. Obsługiwane regiony są wymienione [tutaj](../platform/metrics-custom-overview.md#supported-regions)

## <a name="send-custom-metrics"></a>Wysyłanie metryk niestandardowych 

W tym samouczku wdrażamy maszynę wirtualną z systemem Linux z systemem operacyjnym Ubuntu 16,04 LTS. Agent telegraf jest obsługiwany w przypadku większości systemów operacyjnych Linux. Pakiety Debian i RPM są dostępne wraz z nieopakowanymi plikami binarnymi systemu Linux w [portalu pobierania InfluxData](https://portal.influxdata.com/downloads). Więcej instrukcji i opcji instalacji można znaleźć w tym [przewodniku instalacji telegraf](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) . 

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

> [!NOTE]  
> Jeśli chcesz przeprowadzić migrację klasycznych reguł alertów i użyć istniejącej maszyny wirtualnej z systemem Linux, **upewnij się,** że maszyna wirtualnych ma ustawioną tożsamość przypisaną do systemu.

Utwórz nową maszynę wirtualną z systemem Linux: 

1. Wybierz opcję **Utwórz zasób** w okienku nawigacji po lewej stronie. 
1. Wyszukaj **maszynę wirtualną**.  
1. Wybierz pozycję **Ubuntu 16,04 LTS** i wybierz pozycję **Utwórz**. 
1. Podaj nazwę maszyny wirtualnej, taką jak **MyTelegrafVM**.  
1. Pozostaw typ dysku jako dysk **SSD**. Podaj **nazwę użytkownika**, taką jak **azureuser**. 
1. W obszarze **Typ uwierzytelniania** wybierz pozycję **hasło**. Następnie wprowadź hasło, które będzie używane później do protokołu SSH do tej maszyny wirtualnej. 
1. Wybierz opcję **utworzenia nowej grupy zasobów**. Podaj nazwę **, na przykład.** Wybierz **lokalizację**. Następnie wybierz przycisk **OK**. 

    ![Tworzenie maszyny wirtualnej z systemem Ubuntu](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Wybierz rozmiar maszyny wirtualnej. Możesz filtrować między innymi według wartości **Typ obliczeń** lub **Typ dysku**. 

    ![Omówienie agenta telegraficznego rozmiaru maszyny wirtualnej](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Na stronie **Ustawienia** **w**  >  **grupie zabezpieczeń sieci**  >  **Wybierz pozycję publiczne porty przychodzące**, wybierz pozycję **http** i **SSH (22)**. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**. 

1. Na stronie podsumowania wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej. 

1. Maszyna wirtualna jest przypięta do pulpitu nawigacyjnego witryny Azure Portal. Po zakończeniu wdrożenia zostanie automatycznie otwarte podsumowanie maszyny wirtualnej. 

1. W okienku maszyn wirtualnych przejdź do karty **tożsamość** . Upewnij się, że **na** maszynie wirtualnej jest ustawiona tożsamość przypisana do systemu. 
 
    ![Wersja zapoznawcza maszyny wirtualnej telegraf](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną 

Utwórz połączenie SSH z maszyną wirtualną. Na stronie przeglądu wybierz przycisk **Połącz** dla swojej maszyny wirtualnej. 

![Strona omówienia maszyny wirtualnej telegraf](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Na stronie **Nawiązywanie połączenia z maszyną wirtualną** zostaw opcje domyślne, aby połączyć się za pomocą nazwy DNS przez port 22. W obszarze **Logowanie przy użyciu lokalnego konta maszyny wirtualnej** jest wyświetlane polecenie połączenia. Wybierz przycisk, aby skopiować polecenie. W poniższym przykładzie pokazano, jak wygląda polecenie połączenia SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Wklej polecenie połączenia SSH do powłoki, na przykład Azure Cloud Shell lub bash na Ubuntu w systemie Windows, lub Użyj wybranego klienta SSH, aby utworzyć połączenie. 

## <a name="install-and-configure-telegraf"></a>Instalowanie i Konfigurowanie telegraf 

Aby zainstalować pakiet telegraf Debian na maszynie wirtualnej, uruchom następujące polecenia w sesji SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Plik konfiguracji telegraf definiuje operacje telegraf. Domyślnie przykładowy plik konfiguracji jest instalowany w ścieżce **/etc/telegraf/telegraf.conf**. Przykładowy plik konfiguracji zawiera listę wszystkich możliwych wtyczek wejściowych i wyjściowych. Jednak utworzymy niestandardowy plik konfiguracji i użyjesz go przez agenta, uruchamiając następujące polecenia: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Poprzedni kod włącza tylko dwie wtyczki wejściowe: **CPU** i **MEM**. Możesz dodać więcej wtyczek wejściowych, w zależności od obciążenia, które jest uruchamiane na maszynie. Przykłady to Docker, MySQL i NGINX. Aby zapoznać się z pełną listą wtyczek wejściowych, zobacz sekcję **dodatkowa konfiguracja** . 

Na koniec, aby uruchomić agenta przy użyciu nowej konfiguracji, Wymuś zatrzymanie i uruchomienie agenta przez uruchomienie następujących poleceń: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Teraz agent będzie zbierać metryki z każdej z określonych wtyczek wejściowych i emituje je do Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Wykreśl metryki telegraf w Azure Portal 

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). 

1. Przejdź do karty nowy **monitor** . Następnie wybierz pozycję **metryki**.  

     ![Monitor — metryki (wersja zapoznawcza)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Wybierz maszynę wirtualną w selektorze zasobów.

     ![Wykres metryk](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Wybierz przestrzeń nazw **telegraf/CPU** , a następnie wybierz metrykę **usage_system** . Możesz filtrować według wymiarów tej metryki lub podzielić je na nie.  

     ![Wybieranie przestrzeni nazw i metryki](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Dodatkowa konfiguracja 

Powyższy przewodnik zawiera informacje dotyczące sposobu konfigurowania agenta telegraf w celu zbierania metryk z kilku podstawowych wtyczek wejściowych. Agent telegraf obsługuje ponad 150 wtyczek wejściowych z niektórymi dodatkowymi opcjami konfiguracji. InfluxData opublikował [listę obsługiwanych wtyczek](https://docs.influxdata.com/telegraf/v1.15/plugins/inputs/) i instrukcje dotyczące [sposobu ich konfigurowania](https://docs.influxdata.com/telegraf/v1.15/administration/configuration/).  

Ponadto w tym instruktażu Agent telegraf był używany do emitowania metryk dotyczących maszyny wirtualnej, na której jest wdrożony Agent. Agenta telegraf można także użyć jako modułu zbierającego i usługi przesyłania dalej metryk dla innych zasobów. Aby dowiedzieć się, jak skonfigurować agenta do emisji metryk dla innych zasobów platformy Azure, zobacz [Azure monitor niestandardowe dane wyjściowe metryki dla telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Czyszczenie zasobów 

Gdy nie są już potrzebne, możesz usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby. W tym celu wybierz grupę zasobów dla maszyny wirtualnej, a następnie wybierz pozycję **Usuń**. Następnie Potwierdź nazwę grupy zasobów do usunięcia. 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metrykach niestandardowych](../platform/metrics-custom-overview.md).
