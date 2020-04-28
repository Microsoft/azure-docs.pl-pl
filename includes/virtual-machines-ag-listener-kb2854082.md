---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183179"
---
Następnie, jeśli na dowolnym serwerze w klastrze działa system Windows Server 2008 R2 lub Windows Server 2012, należy sprawdzić, czy poprawka [KB2854082](https://support.microsoft.com/kb/2854082) jest zainstalowana na każdym serwerze lokalnym lub na maszynach wirtualnych platformy Azure, które są częścią klastra. Ta poprawka powinna również być zainstalowana na dowolnym serwerze lub maszynie wirtualnej znajdującej się w klastrze, ale nie w grupie dostępności.

W sesji pulpitu zdalnego dla każdego z węzłów klastra Pobierz [KB2854082](https://support.microsoft.com/kb/2854082) do katalogu lokalnego. Następnie zainstaluj poprawkę w każdym węźle klastra sekwencyjnie. Jeśli usługa klastra jest aktualnie uruchomiona w węźle klastra, serwer zostanie ponownie uruchomiony na końcu instalacji poprawki.

> [!WARNING]
> Zatrzymanie usługi klastrowania lub ponowne uruchomienie serwera wpływa na kondycję kworum klastra i grupy dostępności, co może spowodować, że klaster przejdzie do trybu offline. Aby zachować wysoką dostępność klastra podczas instalacji, należy się upewnić, że:
> 
> * Klaster jest w optymalny kondycji kworum. 
> * Przed zainstalowaniem poprawki w dowolnym węźle wszystkie węzły klastra są w trybie online.
> * Przed zainstalowaniem poprawki w dowolnym innym węźle w klastrze, należy zezwolić na ukończenie instalacji poprawek w jednym węźle, w tym w celu pełnego ponownego uruchomienia serwera.
> 
> 

