---
title: Samouczek — monitorowanie komunikacji sieciowej przy użyciu Azure Portal
description: W tym samouczku dowiesz się, jak monitorować komunikację sieciową między dwiema maszynami wirtualnymi za pomocą funkcji monitorowania połączeń Network Watcher platformy Azure.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 24b1549b2e460bc0e72fb76f5437b15838604949
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896366"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Samouczek: monitorowanie komunikacji sieciowej między dwiema maszynami wirtualnymi przy użyciu witryny Azure Portal

> [!NOTE]
> Ten samouczek obejmuje monitor połączeń (klasyczny). Wypróbuj nowy i udoskonalony [monitor połączeń](connection-monitor-overview.md) , aby uzyskać ulepszone Monitorowanie łączności

Pomyślna komunikacja między maszyną wirtualną i punktem końcowym, takim jak inna maszyna wirtualna, może być krytyczna dla działania organizacji. Czasami są wprowadzane zmiany konfiguracji, co może powodować przerwy w komunikacji. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dwóch maszyn wirtualnych
> * Monitorowanie komunikacji między maszynami wirtualnymi za pomocą możliwości monitorowania połączenia usługi Network Watcher
> * Generowanie alertów dotyczących metryk monitora połączeń
> * Diagnozowanie problemu z komunikacją między dwiema maszynami wirtualnymi i poznanie sposobu jego rozwiązywania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).



## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-vms"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Compute**, a następnie wybierz system operacyjny. W tym samouczku jest używany system **Windows Server 2016 Datacenter**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa|myVm1|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę **myResourceGroup**.|
    |Lokalizacja| Wybierz **Wschodnie stany USA**|

4. Wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **Wybierz**.
5. W obszarze **Ustawienia** wybierz pozycję **Rozszerzenia**. Wybierz polecenie **Dodaj rozszerzenie** i wybierz pozycję **Network Watcher Agent for Windows**, jak pokazano na poniższej ilustracji:

    ![Rozszerzenie agenta usługi Network Watcher](./media/connection-monitor/nw-agent-extension.png)

6. W obszarze **Network Watcher Agent for Windows** wybierz polecenie **Utwórz**, w obszarze **Instalowanie rozszerzenia** wybierz przycisk **OK**, a następnie w obszarze **Rozszerzenia** wybierz pozycję **OK**.
7. Zaakceptuj wartości domyślne pozostałych **ustawień**, a następnie wybierz przycisk **OK**.
8. W obszarze **Utwórz** na stronie **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Wykonaj ponownie kroki z sekcji [Tworzenie pierwszej maszyny wirtualnej](#create-the-first-vm) z następującymi zmianami:

|Krok|Ustawienie|Wartość|
|---|---|---|
| 1 | Wybierz wersję **Ubuntu Server** |                                                                         |
| 3 | Nazwa                                  | myVm2                                                                   |
| 3 | Typ uwierzytelniania                   | Wklej klucz publiczny SSH lub wybierz opcję **Hasło** i wprowadź hasło. |
| 3 | Grupa zasobów                        | Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**.                 |
| 6 | Rozszerzenia                            | **Agent Network Watcher dla systemu Linux**                                             |

Wdrożenie maszyny wirtualnej potrwa kilka minut. Zanim przejdziesz do pozostałych kroków, poczekaj na zakończenie wdrażania maszyny wirtualnej.

## <a name="create-a-connection-monitor"></a>Tworzenie monitora połączeń

Utwórz monitor połączeń na potrzeby monitorowania komunikacji na porcie TCP 22 z maszyny *myVm1* do *myVm2*.

1. Po lewej stronie portalu wybierz pozycję **Wszystkie usługi**.
2. Zacznij pisać *network watcher* w polu **Filtr**. Gdy w wynikach wyszukiwania pojawi się nazwa **Network Watcher**, wybierz ją.
3. W obszarze **MONITOROWANIE** wybierz pozycję **Monitor połączeń**.
4. Wybierz pozycję **+ Dodaj**.
5. Wprowadź lub wybierz informacje dotyczące połączenia, które chcesz monitorować, a następnie wybierz pozycję **Dodaj**. W przykładzie przedstawiono na poniższej ilustracji jest monitorowane połączenie z maszyny wirtualnej *myVm1* do maszyny *myVm2* na porcie 22:

    | Ustawienie                  | Wartość               |
    | ---------                | ---------           |
    | Nazwa                     | myVm1-myVm2(22)     |
    | Element źródłowy                   |                     |
    | Maszyna wirtualna          | myVm1               |
    | Element docelowy              |                     |
    | Wybierz maszynę wirtualną |                     |
    | Maszyna wirtualna          | myVm2               |
    | Port                     | 22                  |

    ![Dodawanie monitora połączeń](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Wyświetlanie monitora połączeń

1. Wykonaj kroki od 1 do 3 w sekcji [Tworzenie monitora połączeń](#create-a-connection-monitor), aby wyświetlić monitorowanie połączenia. Zostanie wyświetlona lista istniejących monitorów połączeń, jak pokazano na poniższej ilustracji:

    ![Monitory połączeń](./media/connection-monitor/connection-monitors.png)

2. Wybierz monitor o nazwie **myVm1-myVm2(22)** w sposób pokazany na ilustracji powyżej, aby wyświetlić szczegóły monitora. Widać to na poniższej ilustracji:

    ![Szczegóły monitora](./media/connection-monitor/vm-monitor.png)

    Zanotuj następujące informacje:

    | Element                     | Wartość                      | Szczegóły                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Stan                   | Osiągalne                  | Informuje o tym, czy punkt końcowy jest osiągalny.|
    | ŚREDNI CZAS RUNDY          | Umożliwia poznanie czasu rundy podczas nawiązania połączenia (w milisekundach). Monitor połączeń sonduje połączenie co 60 sekund, dzięki czemu można monitorować czas opóźnienia.                                         |
    | Przeskoki                     | Monitor połączeń informuje o przeskokach między dwoma punktami końcowymi. W tym przykładzie połączenie istnieje między dwiema maszynami wirtualnymi w tej samej sieci wirtualnej, dlatego jest tylko jeden przeskok do adresu IP 10.0.0.5. Dodatkowe przeskoki pojawią się, jeśli jakikolwiek istniejący system lub trasy niestandardowe kierują ruch między maszynami wirtualnymi za pośrednictwem na przykład bramy sieci VPN lub sieciowego urządzenia wirtualnego.                                                                                                                         |
    | STAN                   | Zielone znaczniki wyboru przy poszczególnych punktach końcowych informują o dobrej kondycji każdego punktu końcowego.    ||

## <a name="generate-alerts"></a>Generowanie alertów

Alerty są tworzone na podstawie reguł alertów na platformie Azure Monitor i mogą automatycznie uruchamiać zapisane zapytania lub niestandardowe wyszukiwania dziennika w regularnych odstępach czasu. Wygenerowany alert może automatycznie uruchomić jedną lub więcej akcji, takich jak powiadomienie kogoś lub uruchomienie innego procesu. Podczas ustawiania reguły alertu zasób docelowy określa listę dostępnych metryk, które umożliwiają generowanie alertów.

1. W witrynie Azure Portal wybierz usługę **Monitor**, a następnie wybierz pozycję **Alerty** > **Nowa reguła alertu**.
2. Kliknij pozycję **Wybierz element docelowy**, a następnie wybierz zasoby docelowe. Wybierz pozycję **Subskrypcja** i ustaw **Typ zasobu**, aby odfiltrować monitor połączeń, którego chcesz używać.

    ![ekran alertu z zaznaczonym elementem docelowym](./media/connection-monitor/set-alert-rule.png)
1. Po wybraniu zasobu docelowego wybierz pozycję **Dodaj kryteria**. Usługa Network Watcher zawiera [metryki, na podstawie których można tworzyć alerty](../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported). W obszarze **Dostępne sygnały** ustaw metryki ProbesFailedPercent i AverageRoundtripMs:

    ![strona alertów z zaznaczonymi sygnałami](./media/connection-monitor/set-alert-signals.png)
1. Wypełnij szczegóły alertu, takie jak nazwa, opis i ważność reguły alertu. Do alertu możesz także dodać grupę akcji, aby zautomatyzować i dostosować reakcję na alert.

## <a name="view-a-problem"></a>Wyświetlanie problemu

Domyślnie platforma Azure zezwala na komunikację na wszystkich portach między maszynami wirtualnymi w tej samej sieci wirtualnej. Może się zdarzyć, że Ty lub inna osoba w Twojej organizacji przesłoni domyślne reguły platformy Azure, co może doprowadzić do niepowodzenia komunikacji. Wykonaj poniższe kroki, aby utworzyć problem z komunikacją i ponownie wyświetlić monitor połączeń:

1. W polu wyszukiwania w górnej części portalu prowadź ciąg *myResourceGroup*. Gdy grupa zasobów **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz sieciową grupę zabezpieczeń o nazwie **myVm2-nsg**.
3. Wybierz **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie wybierz przycisk **Dodaj**, jak pokazano na poniższej ilustracji:

    ![Reguły zabezpieczeń dla ruchu przychodzącego](./media/connection-monitor/inbound-security-rules.png)

4. Domyślna reguła, która umożliwia komunikację między wszystkimi maszynami wirtualnymi w sieci wirtualnej, nosi nazwę **AllowVnetInBound**. Utwórz regułę o wyższym priorytecie (mniejsza liczba) niż reguła **AllowVnetInBound**, która odmówi komunikacji przychodzącej na porcie 22. Wybierz lub wprowadź poniższe informacje, zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Dodaj**:

    | Ustawienie                 | Wartość          |
    | ---                     | ---            |
    | Zakresy portów docelowych | 22             |
    | Akcja                  | Zablokuj           |
    | Priorytet                | 100            |
    | Nazwa                    | DenySshInbound |

5. Ponieważ monitor połączeń sonduje co 60 sekund, zaczekaj kilka minut, po lewej stronie portalu wybierz kolejno pozycje **Network Watcher** i **Monitor połączeń**, a następnie ponownie wybierz monitor **myVm1-myVm2(22)**. Teraz wyniki są inne, jak pokazano na poniższej ilustracji:

    ![Błąd szczegółów monitora](./media/connection-monitor/vm-monitor-fault.png)

    Widać, że w kolumnie Stan znajduje się ikona czerwonego wykrzyknika dla interfejsu sieciowego **myvm2529**.

6. Aby dowiedzieć się, jaka jest przyczyna zmiany stanu, wybierz adres 10.0.0.5 widoczny na poprzedniej ilustracji. Monitor połączeń informuje o następującej przyczynie komunikacji: *Zablokowano ruch zgodnie z następującą regułą grupy zabezpieczeń sieci: UserRule_DenySshInbound*.

    Dzięki monitorowi połączeń możesz dowiedzieć się, że ktoś zaimplementował regułę zabezpieczeń podobną do tej, którą utworzono w kroku 4, i że ona powoduje problem z komunikacją. Następnie możesz zmienić, przesłonić lub usunąć tę regułę, aby przywrócić komunikację między maszynami wirtualnymi.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie znajdujące się w niej zasoby nie będą już potrzebne, usuń je:

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź dla elementu *Webresourcename* **Typ Nazwa grupy zasobów:** a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób monitorowania połączenia między dwiema maszynami wirtualnymi. Reguła sieciowej grupy zabezpieczeń uniemożliwiała komunikację z maszyną wirtualną. Aby uzyskać informacje o wszystkich różnych odpowiedziach, które może zwrócić monitor połączeń, zobacz [typy odpowiedzi](network-watcher-connectivity-overview.md#response). Można również monitorować połączenie między maszyną wirtualną, w pełni kwalifikowaną nazwą domeny, identyfikatorem URI lub adresem IP.

W pewnym momencie może się okazać, że zasoby w sieci wirtualnej nie mogą się komunikować z zasobami w innych sieciach połączonych za pośrednictwem bramy sieci wirtualnej platformy Azure. Przejdź do następnego samouczka, aby dowiedzieć się, jak zdiagnozować problem z bramą sieci wirtualnej.

> [!div class="nextstepaction"]
> [Diagnozowanie problemów z komunikacją między sieciami](diagnose-communication-problem-between-networks.md)