---
title: Rozwiązywanie problemów z połączeniami — Azure Portal
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak korzystać z funkcji rozwiązywania problemów z usługą Azure Network Watcher przy użyciu Azure Portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f33c5f0fdf69737df0d8bd83499ded1e0e0f4f88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898117"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Rozwiązywanie problemów z usługą Azure Network Watcher przy użyciu Azure Portal

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [Program PowerShell](network-watcher-connectivity-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-connectivity-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-connectivity-rest.md)

Dowiedz się, jak używać rozwiązywania problemów z połączeniami, aby sprawdzić, czy można nawiązać bezpośrednie połączenie TCP z maszyny wirtualnej do danego punktu końcowego.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule założono, że masz następujące zasoby:

* Wystąpienie Network Watcher w regionie, w którym chcesz rozwiązać problem z połączeniem.
* Maszyny wirtualne do rozwiązywania problemów z usługą.

> [!IMPORTANT]
> Rozwiązywanie problemów z połączeniem wymaga, aby maszyna wirtualna z maszyną wirtualną była `AzureNetworkWatcherExtension` zainstalowana jako rozszerzenie maszyny wirtualnej. Aby zainstalować rozszerzenie na maszynie wirtualnej z systemem Windows, odwiedź [rozszerzenie maszyny wirtualnej usługi azure Network Watcher Agent dla systemu Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dla maszyny wirtualnej z systemem Linux odwiedź [rozszerzenie maszyny wirtualnej agenta usługi Azure Network Watcher](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozszerzenie nie jest wymagane w docelowym punkcie końcowym.

## <a name="check-connectivity-to-a-virtual-machine"></a>Sprawdź łączność z maszyną wirtualną

Ten przykład umożliwia sprawdzenie łączności z docelową maszyną wirtualną przez port 80.

Przejdź do Network Watcher i kliknij pozycję **Rozwiązywanie problemów z połączeniami**. Wybierz maszynę wirtualną, z której ma zostać sprawdzona łączność. W sekcji **miejsce docelowe** wybierz **pozycję Wybierz maszynę wirtualną** , a następnie wybierz odpowiednią maszynę wirtualną i port do przetestowania.

Po kliknięciu przycisku **Sprawdź**, zostanie sprawdzona łączność między maszynami wirtualnymi w określonym porcie. W tym przykładzie docelowa maszyna wirtualna jest nieosiągalna. zostanie wyświetlona lista przeskoków.

![Sprawdź wyniki łączności dla maszyny wirtualnej][1]

## <a name="check-remote-endpoint-connectivity"></a>Sprawdzanie łączności zdalnego punktu końcowego

Aby sprawdzić łączność i opóźnienie do zdalnego punktu końcowego, wybierz przycisk **Określ ręcznie** radio w sekcji **miejsce docelowe** , wprowadź adres URL i port, a następnie kliknij przycisk **Sprawdź**.  Jest on używany do zdalnych punktów końcowych, takich jak witryny sieci Web i punkty końcowe magazynu.

![Sprawdź wyniki łączności dla witryny sieci Web][2]

## <a name="next-steps"></a>Następne kroki

Informacje o automatyzowaniu przechwytywania pakietów przy użyciu alertów dotyczących maszyn wirtualnych poprzez wyświetlanie [funkcji tworzenia alertu wyzwolenie pakietu](network-watcher-alert-triggered-packet-capture.md)

Sprawdź, czy określony ruch jest dozwolony w lub z maszyny wirtualnej, odwiedzając [sprawdzenie przepływu IP](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png