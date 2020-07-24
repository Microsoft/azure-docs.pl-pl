---
title: Wybierz odpowiednią warstwę cenową | Mapy Microsoft Azure
description: W tym artykule znajdziesz informacje o warstwach cenowych oferowanych przez Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 4b32706822ff16780388f4acb5d59bf3d91e6d91
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133337"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Wybierz odpowiednią warstwę cenową w Azure Maps

Azure Maps oferuje dwie warstwy cenowe, S0 i S1. Celem tego artykułu jest ułatwienie wyboru odpowiedniej warstwy cenowej dla potrzeb użytkownika. Aby wybrać odpowiednią warstwę cenową, należy zadać sobie poniższe dwa pytania.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Ile współbieżnych użytkowników planuję obsługiwać? 
Warstwy cenowe S0 i S1 obsługują różne ilości przepływności danych. Warstwa cenowa S0 obsługuje do **50 zapytań na sekundę**. Natomiast warstwa S1 obsługuje **więcej niż 50 zapytań na sekundę**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jakie możliwości geoprzestrzenne są planowane do użycia?
Warstwa cenowa S0 jest dla Ciebie odpowiednia, jeśli podstawowe interfejsy API geoprzestrzennej spełniają wymagania dotyczące usługi. Jeśli chcesz uzyskać bardziej zaawansowane możliwości aplikacji, rozważ rezygnację z warstwy cenowej S1. Zaawansowane możliwości obejmują: połączenie lotnicze oraz zdjęcia hybrydowe, uzyskiwanie zakresu tras i geokodowanie partii. Zapoznaj się z tabelą **możliwości warstwy cenowej** , aby wybrać warstwę cenową, która jest najbardziej odpowiednia dla Twojej aplikacji.

### <a name="pricing-tier-capabilities"></a>Możliwości warstwy cenowej

| Możliwość                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Renderowanie mapy                              | ✓                   | ✓       |
| Obraz satelitarny                       |                     | ✓        |
| Wyszukaj                                  | ✓                    | ✓        |
| Wyszukiwanie wsadowe                            |                     | ✓        |
| Trasa                                   | ✓                    |✓        |
| Routing usługi Batch                            |                    | ✓        |
| Routing macierzy                          |                     | ✓        |
| Zakres tras (izochron)                |                     | ✓        |
| Ruch                                |✓                    |✓        |
| Strefa czasowa                               |✓                    |✓        |
| Geolokalizacja (wersja zapoznawcza)                    |✓                   |✓        |
| Operacje przestrzenne                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Dane Azure Maps (wersja zapoznawcza)                |                     | ✓        |
| Mobilność (wersja zapoznawcza)                       |                     | ✓        |
| Pogoda (wersja zapoznawcza)                        |✓                    |✓        |

Należy wziąć pod uwagę następujące dodatkowe punkty:
* Jakiego typu przedsiębiorstwa masz?
* Jak jest krytyczna aplikacja?

### <a name="pricing-tier-targeted-customers"></a>Zamierzone odbiorcy warstwy cenowej

Zapoznaj się z tabelą **docelową dla klientów warstwy cenowej** , aby lepiej poznać warstwy cenowe S0 i S1. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Warstwa cenowa  |     Dokierowany klienci                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Warstwa cenowa S0 działa w przypadku aplikacji na wszystkich etapach produkcji: od projektowania rozwiązań do tworzenia koncepcji i wczesnego etapu testowania do produkcji i wdrażania aplikacji. Jednak ta warstwa została zaprojektowana na potrzeby rozwoju małych lub klientów, którzy mają mniej równoczesnych użytkowników lub obie te funkcje. <p>|
| S1            |    <p>Warstwa cenowa S1 jest przeznaczony dla klientów, którzy mają duże aplikacje dla przedsiębiorstw, aplikacje o znaczeniu krytycznym lub duże ilości równoczesnych użytkowników. Jest on również przeznaczony dla tych klientów, którzy wymagają zaawansowanych usług geoprzestrzennych.</p>|

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sposobie wyświetlania i zmieniania warstw cenowych:

> [!div class="nextstepaction"] 
> [Zarządzanie warstwą cenową](how-to-manage-pricing-tier.md)
