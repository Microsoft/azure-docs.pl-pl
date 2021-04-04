---
title: Samouczek — diagnozowanie problemów z komunikacją między sieciami przy użyciu Azure Portal
titleSuffix: Azure Network Watcher
description: W tym samouczku dowiesz się, jak zdiagnozować problem z komunikacją między siecią wirtualną platformy Azure połączoną z lokalną lub inną siecią wirtualną za pośrednictwem bramy sieci wirtualnej platformy Azure, korzystając z funkcji diagnostyki Network Watcher sieci VPN.
services: network-watcher
documentationcenter: na
author: damendo
Customer intent: I need to determine why resources in a virtual network can't communicate with resources in a different network.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 1a556ea66bb54f994349ce9ba4d12fb486827edf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013300"
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>Samouczek: diagnozowanie problemu z komunikacją między sieciami przy użyciu witryny Azure Portal

Brama sieci wirtualnej łączy sieć wirtualną platformy Azure z lokalną lub inną siecią wirtualną. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Diagnozowanie problemu z bramą sieci wirtualnej za pomocą diagnostyki sieci VPN usługi Network Watcher
> * Diagnozowanie problemu z połączeniem bramy
> * Rozwiązywanie problemu z bramą

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby móc używać diagnostyki sieci VPN, musisz mieć istniejącą i działającą bramę sieci VPN. Jeśli nie masz istniejącej bramy sieci VPN do diagnozowania, możesz ją wdrożyć przy użyciu [skryptu programu PowerShell](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Skrypt programu PowerShell możesz uruchomić z następujących lokalizacji:
- **Lokalna instalacja programu PowerShell**: skrypt wymaga `Az` modułu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- **Usługa Azure Cloud Shell**: usługa [Azure Cloud Shell](https://shell.azure.com/powershell) ma zainstalowaną i skonfigurowaną najnowszą wersję programu PowerShell i powoduje zalogowanie na platformie Azure.

Utworzenie bramy sieci VPN za pomocą tego skryptu trwa około godziny. W pozostałych krokach przyjęto założenie, że diagnozowana brama została wdrożona przy użyciu tego skryptu. Jeśli zamiast tego diagnozujesz własną istniejącą bramę, wyniki będą się różnić.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już włączoną usługę Network Watcher w regionie Wschodnie stany USA, przejdź do sekcji [Diagnozowanie bramy](#diagnose-a-gateway).

1. W portalu wybierz pozycję **Wszystkie usługi**. W **polu filtru** wprowadź ciąg *Network Watcher*. Gdy **Network Watcher** pojawi się w wynikach, wybierz ją.
2. Wybierz węzeł **Regiony**, aby go rozwinąć, a następnie wybierz symbol **...** z prawej strony pozycji **Wschodnie stany USA**, jak pokazano na poniższej ilustracji:

    ![Włączanie usługi Network Watcher](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. Wybierz pozycję **Włącz usługę Network Watcher**.

## <a name="diagnose-a-gateway"></a>Diagnozowanie bramy

1. Po lewej stronie portalu wybierz pozycję **Wszystkie usługi**.
2. Zacznij pisać *network watcher* w polu **Filtr**. Gdy w wynikach wyszukiwania pojawi się nazwa **Network Watcher**, wybierz ją.
3. W obszarze **NARZĘDZIA DIAGNOSTYCZNE SIECI** wybierz pozycję **Diagnostyka sieci VPN**.
4. Wybierz pozycję **Konto magazynu**, a następnie wybierz konto magazynu, w którym chcesz zapisać informacje diagnostyczne.
5. Z listy **Konta magazynu** wybierz konto magazynu, którego chcesz użyć. Jeśli nie masz istniejącego konta magazynu, utwórz je. W tym celu wybierz pozycję **+ Konto magazynu**, wprowadź lub wybierz wymagane informacje, a następnie wybierz polecenie **Utwórz**. Jeśli bramę sieci VPN utworzono przy użyciu skryptu z sekcji [Wymagania wstępne](#prerequisites), warto utworzyć konto magazynu w tej samej grupie zasobów (*TestRG1*), w której jest brama.
6. Na liście **Kontenery** zaznacz kontener, którego chcesz użyć, a następnie wybierz pozycję **Wybierz**. Jeśli nie masz żadnych kontenerów, wybierz pozycję **+ Kontener**, wprowadź nazwę kontenera i wybierz przycisk **OK**.
7. Zaznacz bramę, a następnie wybierz polecenie **Rozpocznij rozwiązywanie problemów**. Jak pokazano na poniższej ilustracji, test jest uruchamiany względem bramy o nazwie **Vnet1GW**:

    ![Diagnostyka sieci VPN](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. Podczas działania testu w kolumnie **STAN ROZWIĄZYWANIA PROBLEMÓW** jest wyświetlana informacja **Uruchomiono** w miejscu, w którym na poprzedniej ilustracji była wyświetlana informacja **Nie uruchomiono**. Wykonywanie testu może trwać kilka minut.
9. Wyświetl stan ukończonego testu. Na poniższej ilustracji przedstawiono informacje o stanie ukończonego testu diagnostycznego:

    ![Zrzut ekranu przedstawia wyniki testu diagnostycznego w złej kondycji, w tym podsumowanie i szczegóły.](./media/diagnose-communication-problem-between-networks/status.png)

    W kolumnie **STAN ROZWIĄZYWANIA PROBLEMÓW** jest wyświetlana wartość **W złej kondycji**. Widać też **Podsumowanie** i **Szczegóły** problemu na karcie **Stan**.
10. Po wybraniu karty **Akcja** diagnostyka sieci VPN udostępnia dodatkowe informacje. W przykładzie pokazanym na poniższej ilustracji diagnostyka sieci VPN informuje o tym, że należy sprawdzić kondycję każdego połączenia:

    ![Zrzut ekranu przedstawia kartę Akcja, która umożliwia uzyskanie dodatkowych informacji.](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>Diagnozowanie połączenia bramy

Brama jest połączona z innymi sieciami za pośrednictwem połączenia bramy. Zarówno brama, jak i połączenia bramy muszą być w dobrej kondycji, aby mogły zapewnić pomyślną komunikację między siecią wirtualną i połączoną siecią.

1. Wykonaj ponownie krok 7 z sekcji [Diagnozowanie bramy](#diagnose-a-gateway), tym razem wybierając połączenie. W poniższym przykładzie jest testowane połączenie o nazwie **VNet1toSite1**:

    ![Zrzut ekranu pokazuje, jak rozpocząć rozwiązywanie problemów z wybranym połączeniem.](./media/diagnose-communication-problem-between-networks/connection.png)

    Test jest wykonywany przez kilka minut.
2. Po zakończeniu testu połączenia zostaną wyświetlone wyniki podobne do wyników pokazanych w poniższych ilustracjach na kartach **Stan** i **Akcja**:

    ![Stan połączenia](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![Akcja połączenia](./media/diagnose-communication-problem-between-networks/connection-action.png)

    Na karcie **Stan** diagnostyka sieci VPN informuje o nieprawidłowościach, a na karcie **Akcja** są udostępniane sugestie dotyczących przyczyn problemu.

    Jeśli testowana brama została wdrożona za pomocą [skryptu](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) z sekcji [Wymagania wstępne](#prerequisites), problem wyświetlany na karcie **Stan** i dwa pierwsze elementy na karcie **Akcje** dokładnie odpowiadają występującemu problemowi. Skrypt konfiguruje zastępczy adres IP 23.99.221.164 dla lokalnego urządzenia bramy sieci VPN.

    Aby rozwiązać ten problem, należy się upewnić, że lokalna brama sieci VPN jest [poprawnie skonfigurowana](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), i zmienić adres IP skonfigurowany za pomocą skryptu dla bramy sieci lokalnej na rzeczywisty publiczny adres lokalnej bramy sieci VPN.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli bramę sieci VPN utworzono przy użyciu skryptu z sekcji [Wymagania wstępne](#prerequisites) wyłącznie w celu wykonania kroków tego samouczka i nie jest ona już potrzebna, usuń grupę zasobów i wszystkie zawarte w niej zasoby:

1. Wprowadź *TestRG1* w polu **Wyszukiwanie** w górnej części portalu. Po wyświetleniu nazwy **TestRG1** w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *TestRG1*, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób diagnozowania problemu z bramą sieci wirtualnej. Warto rejestrować komunikację sieciową do i z maszyny wirtualnej. Dzięki temu można przejrzeć dziennik pod kątem ewentualnych nieprawidłowości. Aby dowiedzieć się, jak to zrobić, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Rejestrowanie ruchu sieciowego do i z maszyny wirtualnej](network-watcher-nsg-flow-logging-portal.md)
