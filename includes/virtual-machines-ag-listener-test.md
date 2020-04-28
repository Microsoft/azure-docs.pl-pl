---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183176"
---
W tym kroku przetestujesz odbiornik grupy dostępności przy użyciu aplikacji klienckiej uruchomionej w tej samej sieci.

Łączność z klientem ma następujące wymagania:

* Połączenia klienta z odbiornikiem muszą pochodzić z maszyn, które znajdują się w innej usłudze w chmurze niż ta, która obsługuje zawsze włączone repliki dostępności.
* Jeśli zawsze włączone repliki znajdują się w różnych podsieciach, klienci muszą określić *MultiSubnetFailover = true* w parametrach połączenia. Ten warunek powoduje próby połączenia równoległego z replikami w różnych podsieciach. W tym scenariuszu uwzględniono wdrożenie grupy dostępności obejmujące wiele regionów.

Jednym z przykładu jest połączenie się z odbiornikiem z jednej z maszyn wirtualnych w tej samej sieci wirtualnej platformy Azure (ale nie na jednej z nich, która obsługuje replikę). Prostym sposobem przeprowadzenia tego testu jest próba nawiązania połączenia SQL Server Management Studio z odbiornikiem grupy dostępności. Inną prostą metodą jest uruchomienie pliku [sqlcmd. exe](https://technet.microsoft.com/library/ms162773.aspx)w następujący sposób:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Jeśli EndpointPort wartość to *1433*, nie trzeba określać jej w wywołaniu. W poprzednim wywołaniu założono również, że komputer kliencki jest przyłączony do tej samej domeny, a obiekt wywołujący przyznał uprawnienia do bazy danych przy użyciu uwierzytelniania systemu Windows.
> 
> 

Podczas testowania odbiornika upewnij się, że grupa dostępności jest przełączana w tryb failover, aby upewnić się, że klienci mogą łączyć się z odbiornikiem w trybie awaryjnym.

