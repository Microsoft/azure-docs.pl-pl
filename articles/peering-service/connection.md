---
title: Połączenie usługi Komunikacja równorzędna Azure
description: Dowiedz się więcej o Microsoft Azure połączeniu z usługą komunikacji równorzędnej
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0540fa9c7a29456aaf343adce83509b2d0e390e4
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398924"
---
# <a name="peering-service-connection"></a>Połączenie usługi Komunikacja równorzędna

Połączenie zazwyczaj odnosi się do zestawu informacji logicznych, identyfikując usługę komunikacji równorzędnej. Jest on definiowany przez określenie następujących atrybutów:

- Nazwa logiczna
- Partner łączności
- Fizyczna lokalizacja klienta
- Prefiksy adresów IP

Klient może nawiązać jedno połączenie lub wiele połączeń zgodnie z wymaganiami. Połączenie jest również używane jako jednostka zbierania danych telemetrycznych. Na przykład w celu wyboru alertów telemetrii klient musi zdefiniować połączenie, które będzie monitorowane.

> [!Note]
> Po zarejestrowaniu się w usłudze Komunikacja równorzędna analizujemy dane telemetryczne dla systemu Windows i Microsoft 365, aby zapewnić pomiary opóźnienia dla wybranych prefiksów.
>Aby uzyskać więcej informacji o telemetrii połączenia, zapoznaj się z danymi [telemetrycznymi połączenia usługi komunikacji równorzędnej](connection-telemetry.md).
>

>## <a name="how-to-register-a-connection"></a>Jak zarejestrować połączenie?

**Scenariusz** — Załóżmy, że oddziały rozłożone są w różnych lokalizacjach geograficznych, jak pokazano na poniższej ilustracji. W tym miejscu klient musi podać nazwę logiczną, nazwę dostawcy usług (SP), fizyczną lokalizację klienta i prefiksy adresów IP (należące do klienta lub przydzielone przez dostawcę usługi) skojarzone z pojedynczym połączeniem. Ten proces musi zostać powtórzony w celu zarejestrowania usługi komunikacji równorzędnej w celu oddzielenia połączeń geograficznie nadmiarowych.

![Geograficznie nadmiarowe połączenia](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> Poziom stanu — filtrowanie jest brane pod uwagę w przypadku fizycznej lokalizacji klienta, gdy połączenie jest zlokalizowane geograficznie w Stany Zjednoczone.
>

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat sposobu rejestrowania połączenia z usługą komunikacji równorzędnej, zobacz temat [Rejestrowanie usługi komunikacji równorzędnej przy użyciu Azure Portal](azure-portal.md).

Aby dowiedzieć się więcej o telemetrii połączenia z usługą komunikacji równorzędnej, zobacz [Komunikacja równorzędna połączenia z usługą](connection-telemetry.md).

Aby mierzyć dane telemetryczne, zobacz [mierzenie danych telemetrycznych połączenia](measure-connection-telemetry.md).
