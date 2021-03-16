---
title: Opóźnienie połączenia użytkownika pulpitu wirtualnego systemu Windows — Azure
description: Opóźnienie połączenia dla użytkowników pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb0ee52d37fe901a610723d5864240b8778d8b17
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574598"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Określanie opóźnienia połączenia użytkownika w programie Virtual Desktop systemu Windows

Pulpit wirtualny systemu Windows jest dostępny globalnie. Administratorzy mogą tworzyć maszyny wirtualne w dowolnym regionie świadczenia usługi Azure. Opóźnienie połączenia będzie się różnić w zależności od lokalizacji użytkowników i maszyn wirtualnych. Usługi pulpitu wirtualnego systemu Windows będą stale przełączane do nowych lokalizacje geograficzne w celu poprawy opóźnienia.

[Narzędzie szacowania środowiska pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) może pomóc w ustaleniu najlepszej lokalizacji w celu zoptymalizowania opóźnień maszyn wirtualnych. Zalecamy używanie narzędzia co dwa do trzech miesięcy, aby upewnić się, że optymalna lokalizacja nie została zmieniona jako Windows Virtual Desktop w nowe obszary.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Interpretowanie wyników z narzędzia szacowania pulpitu wirtualnego systemu Windows

W przypadku pulpitu wirtualnego systemu Windows opóźnienie do 150 MS nie ma wpływu na środowisko użytkownika, które nie obejmuje renderowania ani wideo. Czasy oczekiwania między 150 MS i 200 ms powinny być ograniczone do przetwarzania tekstu. Opóźnienie powyżej 200 MS może mieć wpływ na środowisko użytkownika. 

Ponadto połączenie pulpitu wirtualnego systemu Windows jest zależne od połączenia internetowego komputera, z którego korzysta użytkownik. Użytkownicy mogą utracić połączenie lub opóźniać opóźnienia wejścia w jednej z następujących sytuacji:

 - Użytkownik nie ma stabilnego lokalnego połączenia internetowego, a opóźnienie jest ponad 200 ms.
 - Sieć jest nasycenia lub jest ograniczona.

Zalecamy wybranie lokalizacji maszyn wirtualnych, które są blisko użytkowników, jak to możliwe. Na przykład jeśli użytkownik znajduje się w Indiach, ale maszyna wirtualna znajduje się w Stany Zjednoczone, będzie to miało miejsce opóźnienia, które wpłynie na ogólne środowisko użytkownika. 

## <a name="azure-front-door"></a>Azure Front Door

Pulpit wirtualny systemu Windows korzysta z [platformy Azure](https://azure.microsoft.com/services/frontdoor/) , aby przekierować połączenie użytkownika do najbliższej bramy pulpitu wirtualnego systemu Windows na podstawie źródłowego adresu IP. Pulpit wirtualny systemu Windows zawsze będzie używać bramy pulpitu wirtualnego systemu Windows, którą wybiera klient.

## <a name="next-steps"></a>Następne kroki

- Aby sprawdzić najlepszą lokalizację optymalnego opóźnienia, zobacz [narzędzie szacowania pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Aby zapoznać się z planami cenowymi, zobacz [Cennik usługi pulpit wirtualny systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Aby rozpocząć pracę z wdrożeniem pulpitu wirtualnego systemu Windows, zapoznaj się z [naszym samouczkiem](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
