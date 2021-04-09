---
title: 'Samouczek: Migrowanie z usługi mapy Bing do Azure Maps | Mapy Microsoft Azure'
description: Samouczek dotyczący sposobu migrowania z mapy Bing do map Microsoft Azure. Wskazówki przeprowadzą Cię przez proces przełączania do Azure Maps interfejsów API i zestawów SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9bd0516889733a666bf15668cffd124dcc468f3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388961"
---
# <a name="tutorial-migrate-from-bing-maps-to-azure-maps"></a>Samouczek: Migrowanie z usługi mapy Bing do Azure Maps

Ten przewodnik zawiera szczegółowe informacje na temat migrowania aplikacji sieci Web, mobilnych i opartych na serwerze z usługi mapy Bing do Azure Maps platformy. Ten przewodnik zawiera przykłady kodu porównawczego, sugestie dotyczące migracji oraz najlepsze rozwiązania dotyczące migracji do Azure Maps. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Porównanie wysokiego poziomu dla równoważnych funkcji mapy Bing dostępnych w Azure Maps.
> * Jakie różnice licencyjne należy wziąć pod uwagę.
> * Planowanie migracji.
> * Gdzie można znaleźć zasoby techniczne i pomoc techniczną.

## <a name="prerequisites"></a>Wymagania wstępne

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
2. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Omówienie platformy Azure Maps

Azure Maps oferuje deweloperom możliwość korzystania z zaawansowanych możliwości geograficznych, spakowanych za pomocą najświeższych danych mapowania dostępnych w celu zapewnienia geograficznego kontekstu dla aplikacji sieci Web i mobilnych. Azure Maps to zestaw interfejsów API REST zgodny z platformą Azure, które umożliwiają mapowanie, wyszukiwanie, routing, ruch, strefy czasowe, geoogrodzenie, dane dotyczące danych pogodowych i wiele innych usług, w tym zestawy SDK sieci Web i Android, które ułatwiają tworzenie, elastyczne i przenośne projektowanie na wielu platformach. [Azure Maps jest również dostępna w Power BI](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>Porównanie platform wysokiego poziomu

Poniższa tabela zawiera listę funkcji mapy Bing na wysokim poziomie oraz pomoc techniczną dla tych funkcji w Azure Maps. Ta lista nie zawiera dodatkowych funkcji Azure Maps, takich jak ułatwienia dostępu, interfejsy API geoogrodzenia, usługi ruchu, operacje przestrzenne, dostęp kafelków bezpośrednich i usługi Batch.

| Funkcja mapy Bing                     | Obsługa Azure Maps |
|---------------------------------------|:------------------:|
| Zestaw SDK sieci Web                               | ✓                  |
| Android SDK                           | ✓                  |
| Zestaw SDK systemu iOS                               | Planowany            |
| PLATFORMY UWP SDK                               | Nie dotyczy                 |
| ZESTAW WPF SDK                               | Nie dotyczy                 |
| Interfejsy API usługi REST                     | ✓                  |
| Automatyczne sugerowanie                           | ✓                  |
| Kierunki (w tym ciężarówka)          | ✓                  |
| Macierz odległości                       | ✓                  |
| Podniesienia                            | ✓ (Wersja zapoznawcza)        |
| Obraz — Mapa statyczna                  | ✓                  |
| Metadane obrazów                      | ✓                  |
| Izochron                            | ✓                  |
| Lokalne szczegółowe informacje                        | ✓                  |
| Wyszukiwanie lokalne                          | ✓                  |
| Rozpoznawanie lokalizacji                  | ✓                  |
| Lokalizacje (do przodu/do tyłu geokodowania) | ✓                  |
| Zoptymalizowane trasy trasy podróży            | Planowany            |
| Przyciągaj do dróg                         | ✓                  |
| Data Services przestrzenne           | Częściowe            |
| Strefa czasowa                             | ✓                  |
| Zdarzenia dotyczące ruchu                     | ✓                  |
| Mapy oparte na konfiguracji             | Nie dotyczy                |

Usługi mapy Bing zapewniają podstawowe uwierzytelnianie oparte na kluczach. Azure Maps zapewnia podstawowe uwierzytelnianie oparte na kluczach i wysoce bezpieczne, Azure Active Directory uwierzytelnianie.

## <a name="licensing-considerations"></a>Zagadnienia dotyczące licencjonowania

W przypadku migrowania do Azure Maps z usługi mapy Bing następujące informacje powinny być brane pod uwagę w odniesieniu do licencjonowania.

* Azure Maps opłaty za użycie map interakcyjnych na podstawie liczby załadowanych kafelków mapy, podczas gdy usługi Bing mapują opłaty za ładowanie kontrolki mapy (sesje). Aby obniżyć koszty dla deweloperów, Azure Maps Automatyczne buforowanie kafelków mapy. Dla każdego 15 ładowanych kafelków mapy jest generowana jedna transakcja Azure Maps. Zestawy SDK Interactive Azure Maps używają kafelków 512-pikselowych, a średnio generują co najmniej jedną transakcję na widok strony.

* Azure Maps umożliwia przechowywanie danych z platformy na platformie Azure. Może ona być również buforowana w innym miejscu przez maksymalnie sześć miesięcy zgodnie z [warunkami użytkowania](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Poniżej przedstawiono niektóre zasoby związane z licencjonowaniem dla Azure Maps:

-   [Strona cennika Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Maps okres użytkowania](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (zawarty w postanowieniach dotyczących usług online firmy Microsoft)
-   [Wybierz odpowiednią warstwę cenową w Azure Maps](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Sugerowany plan migracji

Oto przykład planu migracji wysokiego poziomu.

1.  Utwórz spis zestawów SDK i usług mapy Bing używanych przez aplikację i sprawdź, czy Azure Maps udostępnia alternatywne zestawy SDK i usługi do migracji do programu.
2.  Utwórz subskrypcję platformy Azure (jeśli jeszcze jej nie masz) <https://azure.com> .
3.  Utwórz konto Azure Maps ([dokumentację](./how-to-manage-account-keys.md)) i klucz uwierzytelniania lub Azure Active Directory ([Dokumentacja](./how-to-manage-authentication.md)).
4.  Migruj kod aplikacji.
5.  Przetestuj zmigrowane aplikacje.
6.  Wdróż zmigrowane aplikacje w środowisku produkcyjnym.

## <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

Aby utworzyć konto Azure Maps i uzyskać dostęp do platformy Azure Maps, wykonaj następujące kroki:

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
2. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
3. Utwórz [konto Azure Maps](./how-to-manage-account-keys.md).
4. [Pobierz klucz subskrypcji Azure Maps](./how-to-manage-authentication.md#view-authentication-details) lub skonfiguruj Azure Active Directory uwierzytelnianie, aby zwiększyć bezpieczeństwo.

## <a name="azure-maps-technical-resources"></a>Azure Maps zasoby techniczne

Poniżej znajduje się lista przydatnych zasobów technicznych dla Azure Maps.

* Omówienie: <https://azure.com/maps>
* Łączoną <https://aka.ms/AzureMapsDocs>
* Przykłady kodu zestawu SDK sieci Web: <https://aka.ms/AzureMapsSamples>
* Fora dla deweloperów: <https://aka.ms/AzureMapsForums>
* Wideo <https://aka.ms/AzureMapsVideos>
* Wpisów <https://aka.ms/AzureMapsBlog>
* Azure Maps opinię (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Obsługa migracji

Deweloperzy mogą szukać pomocy technicznej dotyczącej migracji za pomocą [forów](/answers/topics/azure-maps.html) lub jednej z wielu opcji pomocy technicznej platformy Azure: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Nowa terminologia

Poniższa lista zawiera typowe terminy dotyczące usługi mapy Bing i odpowiadające im warunki Azure Maps.

| Terminy usługi Bing Maps                    | Termin Azure Maps                                                |
|-----------------------------------|----------------------------------------------------------------|
| Z lotu ptaka                            | Satelita lub Satelita                                            |
| Wskazówki                        | Może być również określana jako Routing                             |
| Jednostki                          | Geometrie lub funkcje                                         |
| `EntityCollection`                | Źródło danych lub warstwa                                           |
| `Geopoint`                        | Położenie                                                       |
| `GeoXML`                          | Pliki XML w module we/wy przestrzennym                             |
| Nakładka uziemienia                    | Warstwa obrazu                                                    |
| Hybrydowe (w odniesieniu do typu mapy) | Satelity ze drogami                                           |
| Infobox                           | Okno podręczne                                                          |
| Lokalizacja                          | Położenie                                                       |
| `LocationRect`                    | Pole ograniczenia                                                   |
| Typ mapowania                          | Styl mapy                                                      |
| Pasek nawigacyjny                    | Selektor stylu mapy, kontrolka powiększenia, Kontrola wysokości, kontrolka kompasu |
| Pinezk                           | Warstwa bąbelkowa, warstwa symboli lub znacznik HTML                      |

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Brak zasobów, które wymagają oczyszczenia.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się ze szczegółami dotyczącymi sposobu migrowania aplikacji mapy Bing z następującymi artykułami:

> [!div class="nextstepaction"]
> [Migracja aplikacji internetowej](migrate-from-bing-maps-web-app.md)
