---
title: Wprowadzenie do funkcji przechwytywania pakietów na platformie Azure Network Watcher | Microsoft Docs
description: Ta strona zawiera omówienie możliwości przechwycenia pakietu Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1f17463125cead64bd58a2d07e53eee4d3cfcd70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76840812"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Wprowadzenie do funkcji przechwytywania pakietów zmiennych w usłudze Azure Network Watcher

Network Watcher przechwytywanie pakietów zmiennych umożliwia tworzenie sesji przechwytywania pakietów do śledzenia ruchu do i z maszyny wirtualnej. Przechwytywanie pakietów ułatwia diagnozowanie anomalii w sieci zarówno ponownie, jak i aktywnie. Inne zastosowania obejmują gromadzenie statystyk sieci, uzyskiwanie informacji o atakach sieci, debugowanie komunikacji klient-serwer i wiele więcej.

Przechwytywanie pakietów to rozszerzenie maszyny wirtualnej, które jest zdalnie uruchamiane za pomocą Network Watcher. Ta funkcja ułatwia obciążenie przechwycenia pakietu ręcznie na wybranej maszynie wirtualnej, co pozwala zaoszczędzić cenny czas. Przechwytywanie pakietów może być wyzwalane za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia lub API REST. Przykładem możliwości wyzwolenia funkcji przechwytywania pakietów są alerty maszyn wirtualnych. Filtry są udostępniane dla sesji przechwytywania, aby zapewnić przechwytywanie ruchu, który ma być monitorowany. Filtry są oparte na podanej kolekcji 5 (protokół, lokalny adres IP, zdalny adres IP, port lokalny i Port zdalny). Przechwycone dane są przechowywane na dysku lokalnym lub w magazynie obiektów BLOB. Istnieje limit 10 sesji przechwytywania pakietów na region na subskrypcję. Ten limit dotyczy tylko sesji i nie ma zastosowania do zapisanych plików przechwytywania pakietów lokalnie na maszynie wirtualnej lub na koncie magazynu.

> [!IMPORTANT]
> Przechwytywanie pakietów wymaga rozszerzenia maszyny wirtualnej `AzureNetworkWatcherExtension` . Aby zainstalować rozszerzenie na maszynie wirtualnej z systemem Windows, odwiedź [rozszerzenie maszyny wirtualnej usługi azure Network Watcher Agent dla systemu Windows](../virtual-machines/windows/extensions-nwa.md) i dla maszyny wirtualnej z systemem Linux odwiedź [rozszerzenie maszyny wirtualnej agenta usługi Azure Network Watcher](../virtual-machines/linux/extensions-nwa.md).

Aby zmniejszyć przechwytywane informacje tylko do żądanych informacji, dostępne są następujące opcje dla sesji przechwytywania pakietów:

**Konfiguracja przechwytywania**

|Właściwość|Opis|
|---|---|
|**Maksymalna liczba bajtów na pakiet (w bajtach)** | Liczba bajtów z każdego przechwytywanego pakietu, jeśli pole pozostanie puste, wszystkie bajty są przechwytywane. Liczba bajtów z każdego przechwytywanego pakietu, jeśli pole pozostanie puste, wszystkie bajty są przechwytywane. Jeśli potrzebujesz tylko nagłówka IPv4 — wskaż 34 tutaj |
|**Maksymalna liczba bajtów na sesję (w bajtach)** | Całkowita liczba przechwyconych bajtów, po osiągnięciu której wartość zostanie zakończona.|
|**Limit czasu (w sekundach)** | Ustawia ograniczenie czasu dla sesji przechwytywania pakietów. Wartość domyślna to 18000 sekund lub 5 godzin.|

**Filtrowanie (opcjonalne)**

|Właściwość|Opis|
|---|---|
|**Protokół** | Protokół do filtrowania przechwytywania pakietów. Dostępne wartości to TCP, UDP i ALL.|
|**Lokalny adres IP** | Ta wartość filtruje przechwytywanie pakietów do pakietów, w których lokalny adres IP jest zgodny z wartością tego filtru.|
|**Port lokalny** | Ta wartość filtruje przechwycenie pakietu do pakietów, w których port lokalny jest zgodny z wartością tego filtru.|
|**Zdalny adres IP** | Ta wartość filtruje przechwytywanie pakietów do pakietów, w których zdalny adres IP jest zgodny z wartością tego filtru.|
|**Port zdalny** | Ta wartość filtruje przechwytywanie pakietów do pakietów, w których Port zdalny jest zgodny z wartością tego filtru.|

### <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać przechwyceniami pakietów za pomocą portalu, odwiedzając [Zarządzanie przechwytywaniem pakietów w Azure Portal](network-watcher-packet-capture-manage-portal.md) lub przy użyciu programu PowerShell, odwiedzając [Zarządzanie pakietami za pomocą programu PowerShell](network-watcher-packet-capture-manage-powershell.md).

Dowiedz się, jak utworzyć proaktywne przechwycenia pakietów na podstawie alertów maszyny wirtualnej przez odwiedzenie [Create an wyzwolone przechwytywanie pakietów](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













