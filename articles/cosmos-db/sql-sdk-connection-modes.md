---
title: Azure Cosmos DB tryby łączności z zestawem SDK SQL
description: Dowiedz się więcej na temat różnych trybów łączności dostępnych w Azure Cosmos DB zestawach SQL SDK.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: d88d52f67274d14836520494580e9208ce4eecbe
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283993"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB tryby łączności z zestawem SDK SQL

Sposób, w jaki klient nawiązuje połączenie z Azure Cosmos DB, ma ważne konsekwencje dotyczące wydajności, szczególnie w przypadku zaobserwowanego opóźnienia po stronie klienta. Azure Cosmos DB oferuje prosty, otwarty model programowania RESTful za pośrednictwem protokołu HTTPS o nazwie Gateway Mode. Ponadto oferuje wydajny protokół TCP, który jest również RESTful w swoim modelu komunikacji i używa protokołu TLS do uwierzytelniania początkowego i szyfrowania ruchu, nazywanego trybem bezpośrednim.

## <a name="available-connectivity-modes"></a>Dostępne tryby łączności

Istnieją dwa dostępne tryby łączności:

  * Tryb bramy
      
    Tryb bramy jest obsługiwany na wszystkich platformach SDK. Jeśli aplikacja działa w sieci firmowej z rygorystycznymi ograniczeniami zapory, najlepszym wyborem jest tryb bramy, ponieważ używa on standardowego portu HTTPS i pojedynczego punktu końcowego DNS. Jednak jest to, że tryb bramy obejmuje dodatkowy przeskok sieciowy za każdym razem, gdy dane są odczytywane lub zapisywane w Azure Cosmos DB. Zalecamy również tryb połączenia bramy w przypadku uruchamiania aplikacji w środowiskach, które mają ograniczoną liczbę połączeń gniazd.

    Korzystając z zestawu SDK w Azure Functions, szczególnie w [planie zużycia](../azure-functions/functions-scale.md#consumption-plan), należy pamiętać o bieżących [limitach połączeń](../azure-functions/manage-connections.md).

  * Tryb bezpośredni

    Tryb bezpośredni obsługuje łączność za pośrednictwem protokołu TCP i zapewnia lepszą wydajność z powodu mniejszej liczby przeskoków sieci. Aplikacja łączy się bezpośrednio z replikami zaplecza. Tryb bezpośredni jest obecnie obsługiwany tylko na platformach .NET i Java SDK.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Tryby łączności Azure Cosmos DB" border="false":::

Te tryby łączności zasadniczo polegają na tym, że trasy, które są żądaniami płaszczyzny danych, odczytuje dokumenty i zapisuje je z komputera klienckiego do partycji w Azure Cosmos DB zaplecza. Tryb bezpośredni jest preferowaną opcją dla najlepszej wydajności — pozwala klientowi na otwieranie połączeń TCP bezpośrednio do partycji w Azure Cosmos DB zaplecza i wysyłanie żądań *bezpośrednich*ly bez pośrednika. Natomiast w trybie bramy żądania wysyłane przez klienta są kierowane do serwera "brama" w ramach frontonu Azure Cosmos DB, co z kolei powoduje, że żądania do odpowiednich partycji w Azure Cosmos DB zaplecza.

## <a name="service-port-ranges"></a>Zakresy portów usługi

W przypadku korzystania z trybu bezpośredniego oprócz portów bramy należy upewnić się, że zakres portów od 10000 do 20000 jest otwarty, ponieważ Azure Cosmos DB używa dynamicznych portów TCP. W przypadku korzystania z trybu bezpośredniego dla [prywatnych punktów końcowych](./how-to-configure-private-endpoints.md)powinien być otwarty cały zakres portów TCP — od 0 do 65535. Jeśli te porty nie są otwarte i podjęto próbę użycia protokołu TCP, może zostać wyświetlony błąd niedostępności usługi 503.

W poniższej tabeli przedstawiono podsumowanie trybów łączności dostępnych dla różnych interfejsów API i portów usług używanych dla każdego interfejsu API:

|Tryb połączenia  |Obsługiwany protokół  |Obsługiwane zestawy SDK  |Port API/usługi  |
|---------|---------|---------|---------|
|Brama  |   HTTPS    |  Wszystkie zestawy SDK    |   SQL (443), MongoDB (10250, 10255, 10256), tabela (443), Cassandra (10350), Graph (443) <br> Port 10250 mapuje do domyślnego interfejsu API Azure Cosmos DB dla wystąpienia MongoDB bez replikacji geograficznej. Porty 10255 i 10256 są mapowane na wystąpienie mające replikację geograficzną.   |
|Direct    |     TCP    |  Zestaw SDK .NET    | Gdy są używane publiczne/punkty końcowe usługi: porty z zakresu od 10000 do 20000<br>W przypadku korzystania z prywatnych punktów końcowych: porty w zakresie od 0 do 65535 |

## <a name="next-steps"></a>Następne kroki

Dla określonych optymalizacji wydajności platformy SDK:

* [Wskazówki dotyczące wydajności zestawu SDK platformy .NET V2](performance-tips.md)

* [Wskazówki dotyczące wydajności zestawu .NET v3 SDK](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Porady dotyczące wydajności zestawu SDK Java v4](performance-tips-java-sdk-v4-sql.md)