---
title: Wybierz odpowiednią warstwę cenową dla map Microsoft Azure
description: Dowiedz się więcej o warstwach cenowych Azure Maps. Zobacz, które funkcje są oferowane, w których warstwach i Wyświetl kluczowe zagadnienia dotyczące wyboru warstwy cenowej.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb57e424642141e73588a61d026d1ff2f1a8b096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905302"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Wybierz odpowiednią warstwę cenową w Azure Maps

Azure Maps oferuje dwie warstwy cenowe: S0 i S1. Celem tego artykułu jest ułatwienie wyboru odpowiedniej warstwy cenowej dla potrzeb użytkownika. Aby wybrać odpowiednią warstwę cenową, należy zadać sobie poniższe dwa pytania.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Ile współbieżnych użytkowników planuję obsługiwać?

Warstwy cenowe S0 i S1 obsługują różne ilości przepływności danych. Warstwa cenowa S0 obsługuje do **50 zapytań na sekundę**. Natomiast warstwa S1 obsługuje **więcej niż 50 zapytań na sekundę**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Jakie możliwości geoprzestrzenne są planowane do użycia?

Jeśli podstawowe interfejsy API geoprzestrzennej spełniają wymagania dotyczące usługi, wybierz warstwę cenową S0. Jeśli chcesz uzyskać bardziej zaawansowane możliwości aplikacji, rozważ wybranie warstwy cenowej S1. Zaawansowane możliwości obejmują: połączenie lotnicze oraz zdjęcia hybrydowe, uzyskiwanie zakresu tras i geokodowanie partii. Aby wybrać warstwę cenową, która jest najbardziej odpowiednia dla Twojej aplikacji, zapoznaj się z tabelą **możliwości warstwy cenowej** poniżej:

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
|  Twórca (wersja zapoznawcza)                         |                   |✓        |
|  Podniesienie uprawnień (wersja zapoznawcza)                        |                   |✓        |

Należy wziąć pod uwagę następujące dodatkowe punkty:

* Jakiego typu przedsiębiorstwa masz?
* Jak jest krytyczna aplikacja?

### <a name="pricing-tier-targeted-customers"></a>Zamierzone odbiorcy warstwy cenowej

Zapoznaj się z tabelą **docelową dla klientów warstwy cenowej** , aby lepiej poznać warstwy cenowe S0 i S1. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Warstwa cenowa  |     Dokierowany klienci                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    Warstwa cenowa S0 działa w przypadku aplikacji na wszystkich etapach produkcji: od projektowania rozwiązań do tworzenia koncepcji i wczesnego etapu testowania do produkcji i wdrażania aplikacji. Jednak ta warstwa została zaprojektowana na potrzeby rozwoju małych lub klientów, którzy mają mniej równoczesnych użytkowników lub obie te funkcje. 
| S1            |    Warstwa cenowa S1 jest przeznaczony dla klientów, którzy mają duże aplikacje dla przedsiębiorstw, aplikacje o znaczeniu krytycznym lub duże ilości równoczesnych użytkowników. Jest on również przeznaczony dla tych klientów, którzy wymagają zaawansowanych usług geoprzestrzennych.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sposobie wyświetlania i zmieniania warstw cenowych:

> [!div class="nextstepaction"]
> [Zarządzanie warstwą cenową](how-to-manage-pricing-tier.md)
