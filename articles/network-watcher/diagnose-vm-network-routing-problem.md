---
title: 'Samouczek: diagnozowanie problemu z routingiem sieci maszyn wirtualnych — Azure Portal'
titleSuffix: Azure Network Watcher
description: Korzystając z tego samouczka, dowiesz się, jak diagnozować problemy z routingiem sieciowym na maszynie wirtualnej przy użyciu funkcji określania następnego przeskoku w usłudze Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: bfcf9568f37a26f234a417a86eebf1b0bde67ab0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014505"
---
# <a name="tutorial-diagnose-a-virtual-machine-network-routing-problem-using-the-azure-portal"></a>Samouczek: diagnozowanie problemu z routingiem sieciowym na maszynie wirtualnej przy użyciu witryny Azure Portal

Podczas wdrażania maszyny wirtualnej na platformie Azure jest tworzonych kilka domyślnych tras. Możesz tworzyć trasy niestandardowe zastępujące domyślne trasy platformy Azure. Czasami użycie trasy niestandardowej może spowodować niepowodzenie komunikacji między maszyną wirtualną a innymi zasobami. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej
> * Testowanie komunikacji z adresem URL za pomocą funkcji określania następnego przeskoku w usłudze Network Watcher
> * Testowanie komunikacji z adresem IP
> * Diagnozowanie problemu z routingiem i poznanie sposobu jego rozwiązania

Jeśli wolisz, możesz zdiagnozować problem z routingiem sieciowym na maszynie wirtualnej za pomocą [interfejsu wiersza polecenia platformy Azure](diagnose-vm-network-routing-problem-cli.md) lub programu [Azure PowerShell](diagnose-vm-network-routing-problem-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Compute**, a następnie wybierz pozycję **Windows Server 2016 Datacenter** lub **Maszyna wirtualna z systemem Ubuntu Server 17.10**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa|myVm|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę **myResourceGroup**.|
    |Lokalizacja| Wybierz **Wschodnie stany USA**|

4. Wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **Wybierz**.
5. W obszarze **Ustawienia** zaakceptuj wszystkie wartości domyślne i wybierz przycisk **OK**.
6. W obszarze **Utwórz** na stronie **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej. Wdrożenie maszyny wirtualnej potrwa kilka minut. Zanim przejdziesz do pozostałych kroków, poczekaj na zakończenie wdrażania maszyny wirtualnej.

## <a name="test-network-communication"></a>Testowanie komunikacji sieciowej

Aby przetestować komunikację sieciową za pomocą usługi Network Watcher, należy najpierw włączyć usługę Network Watcher co najmniej w jednym regionie świadczenia usługi Azure, a następnie użyć funkcji określania następnego przeskoku w usłudze Network Watcher.

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już włączoną usługę Network Watcher co najmniej w jednym regionie, przejdź do sekcji [Korzystanie z funkcji Następny przeskok](#use-next-hop).

1. W portalu wybierz pozycję **Wszystkie usługi**. W **polu filtru** wprowadź ciąg *Network Watcher*. Gdy **Network Watcher** pojawi się w wynikach, wybierz ją.
2. Wybierz węzeł **Regiony**, aby go rozwinąć, a następnie wybierz symbol **...** z prawej strony pozycji **Wschodnie stany USA**, jak pokazano na poniższej ilustracji:

    ![Włączanie usługi Network Watcher](./media/diagnose-vm-network-traffic-filtering-problem/enable-network-watcher.png)

3. Wybierz pozycję **Włącz usługę Network Watcher**.

### <a name="use-next-hop"></a>Korzystanie z funkcji Następny przeskok

Na platformie Azure są automatycznie tworzone trasy do domyślnych miejsc docelowych. Możesz tworzyć trasy niestandardowe zastępujące domyślne trasy. Czasami użycie tras niestandardowych może spowodować niepowodzenie komunikacji. Funkcja Następny przeskok w usłudze Network Watcher umożliwia określenie trasy używanej przez platformę Azure do kierowania ruchu.

1. W witrynie Azure Portal wybierz pozycję **Następny przeskok** w sekcji **Network Watcher**.
2. Wybierz subskrypcję, wpisz lub wybierz następujące wartości i wybierz pozycję **Następny przeskok**, jak pokazano na poniższej ilustracji:

    |Ustawienie                  |Wartość                                                   |
    |---------                |---------                                               |
    | Grupa zasobów          | Wybierz pozycję myResourceGroup                                 |
    | Maszyna wirtualna         | Wybierz pozycję myVm                                            |
    | Interfejs sieciowy       | myvm — nazwa interfejsu sieciowego może się różnić.   |
    | Źródłowy adres IP       | 10.0.0.4                                               |
    | Docelowy adres IP  | 13.107.21.200 — jeden z adresów dla <www.bing.com>. |

    ![Narzędzie Następny przeskok](./media/diagnose-vm-network-routing-problem/next-hop.png)

    Po kilku sekundach zobaczysz wynik informujący, że typ następnego przeskoku to **Internet**, a **Identyfikator tabeli tras** to **Trasa systemowa**. Ten wynik oznacza, że istnieje prawidłowa trasa systemowa do miejsca docelowego.

3. Zmień **Docelowy adres IP** na *172.31.0.100* i ponownie wybierz pozycję **Następny przeskok**. Zwrócony wynik informuje, że **Typ następnego przeskoku** to **Brak**, a **Identyfikator tabeli tras** to również **Trasa systemowa**. Ten wynik oznacza, że istnieje prawidłowa trasa systemowa do miejsca docelowego, ale nie ma następnego przeskoku umożliwiającego kierowanie ruchu do miejsca docelowego.

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

1. Aby dokładniej przeanalizować routing, sprawdź obowiązujące trasy związane z interfejsem sieciowym. W polu wyszukiwania w górnej części portalu wprowadź ciąg *myvm* (lub inną nazwę interfejsu sieciowego, który sprawdzasz). Gdy pozycja **myvm** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Obowiązujące trasy** w sekcji **POMOC TECHNICZNA I ROZWIĄZYWANIE PROBLEMÓW**, jak pokazano na poniższej ilustracji:

    ![Obowiązujące trasy](./media/diagnose-vm-network-routing-problem/effective-routes.png)

    Podczas testu z użyciem adresu 13.107.21.200, przeprowadzonego w sekcji [Korzystanie z funkcji Następny przeskok](#use-next-hop), ruch był kierowany do tego adresu przy użyciu trasy z prefiksem adresu 0.0.0.0/0, ponieważ żadna inna trasa nie uwzględnia tego adresu. Domyślnie wszystkie adresy, które nie zostały określone w prefiksie adresu innej trasy, są kierowane do Internetu.

    Jednak wynik testu z użyciem adresu 172.31.0.100 informował, że nie ma typu następnego przeskoku. Jak widać na poprzedniej ilustracji, pomimo że istnieje trasa domyślna dla prefiksu 172.16.0.0/12, obejmująca adres 172.31.0.100, wartość w polu **TYP NASTĘPNEGO PRZESKOKU** to **Brak**. Platforma Azure tworzy domyślną trasę dla zakresu adresów 172.16.0.0/12, ale nie określa typu następnego przeskoku, jeśli nie jest to wymagane. Jeśli na przykład dodano zakres adresów 172.16.0.0/12 do przestrzeni adresów sieci wirtualnej, **TYP NASTĘPNEGO PRZESKOKU** dla tej trasy zostanie zmieniony na platformie Azure na typ **Sieć wirtualna**. W takim przypadku po przeprowadzeniu testu w polu **TYP NASTĘPNEGO PRZESKOKU** widoczna byłaby wartość **Sieć wirtualna**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie znajdujące się w niej zasoby nie będą już potrzebne, usuń je:

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź dla elementu *Webresourcename* **Typ Nazwa grupy zasobów:** a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem utworzono maszynę wirtualną i zdiagnozowano routing sieciowy z tej maszyny wirtualnej. Uzyskano informacje o tworzeniu tras domyślnych na platformie Azure i przetestowano routing do dwóch różnych miejsc docelowych. Uzyskaj więcej informacji na temat [routingu na platformie Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dowiedz się, jak [tworzyć trasy niestandardowe](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

W przypadku połączeń wychodzących maszyny wirtualnej możesz też określić opóźnienie, dozwolony i zablokowany ruch sieciowy między maszyną wirtualną a punktem końcowym, a także trasę używaną do kierowania ruchu do punktu końcowego, korzystając z funkcji [rozwiązywania problemów z połączeniami](network-watcher-connectivity-portal.md) w usłudze Network Watcher. Dowiedz się, jak monitorować komunikację między maszyną wirtualną a punktem końcowym, na przykład adresem IP lub URL, w czasie, korzystając z funkcji monitora połączeń w usłudze Network Watcher.

> [!div class="nextstepaction"]
> [Monitor a network connection (Monitorowanie połączenia sieciowego)](connection-monitor.md)