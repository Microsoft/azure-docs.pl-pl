---
title: Samouczek — Migrowanie z usługi Google Maps do Azure Maps | Mapy Microsoft Azure
description: Samouczek dotyczący migracji z usługi Google Maps do Microsoft Azure Maps. Wskazówki przeprowadzą Cię przez proces przełączania do Azure Maps interfejsów API i zestawów SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 3ece62e671caa0838bbf4713c0ad4a74f19fd6cb
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518878"
---
# <a name="tutorial---migrate-from-google-maps-to-azure-maps"></a>Samouczek — Migrowanie z usługi Google Maps do Azure Maps

Ten artykuł zawiera szczegółowe informacje na temat migracji aplikacji sieci Web, mobilnych i opartych na serwerze z usługi Google Maps do platformy Maps Microsoft Azure. Ten samouczek zawiera porównawcze przykłady kodu, sugestie dotyczące migracji oraz najlepsze rozwiązania dotyczące migracji do Azure Maps. Z tego samouczka dowiesz się:

> [!div class="checklist"]
> * Porównanie wysokiego poziomu dla równoważnych funkcji usługi Google Maps dostępnych w Azure Maps
> * Jakie różnice licencyjne należy wziąć pod uwagę
> * Planowanie migracji
> * Gdzie można znaleźć zasoby techniczne i pomoc techniczną.

## <a name="prerequisites"></a>Wymagania wstępne 

1. Zaloguj się do [portalu Azure](https://portal.azure.com). Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
2. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Omówienie platformy Azure Maps

Azure Maps oferuje deweloperom możliwość korzystania z zaawansowanych możliwości geograficznych. Możliwości są zapakowane z regularnymi aktualizacjami danych mapy w celu zapewnienia geograficznego kontekstu dla aplikacji internetowych i mobilnych. Azure Maps ma zestaw interfejsów API REST zgodny z platformą Azure. Interfejsy API REST oferują funkcje renderowania, wyszukiwania, routingu, ruchu, strefy czasowej, geolokalizacji, geoprzestrzennia, danych mapy, warunków pogodowych, mobilności i operacji przestrzennych. Do operacji dołączone są zestawy SDK sieci Web i Android, które ułatwiają tworzenie, elastyczne i przenośne Programowanie na wielu platformach.

## <a name="high-level-platform-comparison"></a>Porównanie platform wysokiego poziomu

Tabela zawiera listę funkcji Azure Maps, które odpowiadają funkcjom usługi Google Maps. Na tej liście nie są wyświetlane wszystkie funkcje Azure Maps. Dodatkowe funkcje Azure Maps obejmują: ułatwienia dostępu, geoogrodzenie, izochron, operacje przestrzenne, bezpośredni dostęp do kafelków mapy, usługi Batch i porównania pokrycia danych (czyli pokrycie obrazów).

| Funkcja usługi Google Maps         | Obsługa Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Zestaw SDK sieci Web                     | ✓                                      |
| Android SDK                 | ✓                                      |
| Zestaw SDK systemu iOS                     | Planowany                                |
| Interfejsy API usługi REST           | ✓                                      |
| Kierunki (routing)        | ✓                                      |
| Macierz odległości             | ✓                                      |
| Noszeniu                   | Planowany                                |
| Geokodowanie (do przodu/wstecz) | ✓                                      |
| Geolokalizacja                 | Nie dotyczy                                    |
| Najbliższe drogi               | ✓                                      |
| Wyszukiwanie miejsc               | ✓                                      |
| Szczegóły miejsc              | Nie dotyczy — witryna sieci Web & jest dostępny numer telefonu |
| Umieszcza Zdjęcia               | Nie dotyczy                                    |
| Umieść Autouzupełnianie          | ✓                                      |
| Przyciągaj do drogi                | ✓                                      |
| Limity szybkości                | ✓                                      |
| Mapy statyczne                 | ✓                                      |
| Widok statyczny          | Nie dotyczy                                    |
| Strefa czasowa                   | ✓                                      |
| Maps — osadzony interfejs API           | Nie dotyczy                                    |
| Adresy URL map                    | Nie dotyczy                                    |

Usługi Mapy Google zapewniają podstawowe uwierzytelnianie oparte na kluczach. Azure Maps zapewnia podstawowe uwierzytelnianie oparte na kluczach i uwierzytelnianie Azure Active Directory. Uwierzytelnianie Azure Active Directory zapewnia więcej funkcji zabezpieczeń w porównaniu do podstawowego uwierzytelniania opartego na kluczach.

## <a name="licensing-considerations"></a>Zagadnienia dotyczące licencjonowania

Podczas migrowania do Azure Maps z usługi Google Maps należy wziąć pod uwagę następujące kwestie dotyczące licencjonowania.

- Azure Maps opłaty za użycie map interaktywnych, które są oparte na liczbie załadowanych kafelków mapy. Z drugiej strony usługi Google Maps naliczą opłaty za ładowanie formantu mapy. W zestawach SDK interakcyjne Azure Maps kafelki mapy są automatycznie buforowane, aby zmniejszyć koszty tworzenia oprogramowania. Dla każdego 15 ładowanych kafelków mapy jest generowana jedna transakcja Azure Maps. Interaktywna Azure Maps zestawy SDK wykorzystują 512-pikselowe kafelki, a przeciętnie generuje co najmniej jedną transakcję na widok strony.
- Często jest to bardziej ekonomiczne, aby zamienić statyczne obrazy map z usług sieci Web usługi Google Maps za pomocą zestawu SDK Azure Maps Web. Zestaw SDK sieci Web Azure Maps używa kafelków mapy. Jeśli użytkownik nie Pans i nie powiększy mapy, usługa często generuje tylko ułamek transakcji na obciążenie mapy. Zestaw SDK sieci Web Azure Maps zawiera opcje wyłączania panoramowania i powiększania w razie potrzeby. Ponadto zestaw SDK sieci Web Azure Maps udostępnia wiele opcji wizualizacji niż usługa sieci Web mapy statycznej.
- Azure Maps umożliwia przechowywanie danych z platformy na platformie Azure. Ponadto dane mogą być buforowane w innym miejscu przez maksymalnie sześć miesięcy zgodnie z [warunkami użytkowania](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Poniżej przedstawiono kilka powiązanych zasobów Azure Maps:

- [Strona cennika Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps okres użytkowania](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (zawarty w postanowieniach dotyczących usług online firmy Microsoft)
- [Wybierz odpowiednią warstwę cenową w Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Sugerowany plan migracji

Poniżej znajduje się plan migracji wysokiego poziomu.

1. Utwórz spis zestawów SDK i usług usługi Google Maps używanych przez aplikację. Sprawdź, czy Azure Maps udostępnia alternatywne zestawy SDK i usługi.
2. Jeśli jeszcze tego nie masz, Utwórz subskrypcję platformy Azure pod adresem [https://azure.com](https://azure.com) .
3. Utwórz konto Azure Maps ([dokumentację](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) i klucz uwierzytelniania lub Azure Active Directory ([Dokumentacja](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migruj kod aplikacji.
5. Przetestuj zmigrowane aplikacje.
6. Wdróż zmigrowane aplikacje w środowisku produkcyjnym.

## <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

Aby utworzyć konto Azure Maps i uzyskać dostęp do platformy Azure Maps, wykonaj następujące kroki:

1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
2. Zaloguj się do [portalu Azure](https://portal.azure.com/).
3. Utwórz [konto Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys). 
4. [Pobierz klucz subskrypcji Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication#view-authentication-details) lub skonfiguruj Azure Active Directory uwierzytelnianie, aby zwiększyć bezpieczeństwo.

## <a name="azure-maps-technical-resources"></a>Azure Maps zasoby techniczne

Poniżej znajduje się lista przydatnych zasobów technicznych dla Azure Maps.

- Podsumowanie [https://azure.com/maps](https://azure.com/maps)
- Łączoną [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Przykłady kodu zestawu SDK sieci Web: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fora dla deweloperów: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Wideo [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Wpisów [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Blog techniczny: [https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Azure Maps opinię (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

## <a name="migration-support"></a>Obsługa migracji

Deweloperzy mogą szukać pomocy technicznej dotyczącej migracji za pomocą [forów](https://aka.ms/AzureMapsForums) lub jednej z wielu opcji pomocy technicznej platformy Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się ze szczegółami dotyczącymi migracji aplikacji usługi Google Maps z następującymi artykułami:

> [!div class="nextstepaction"]
> [Migracja aplikacji internetowej](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migracja usługi internetowej](migrate-from-google-maps-web-services.md) 

> [!div class="nextstepaction"]
> [Migracja aplikacji dla systemu Android](migrate-from-google-maps-android-app.md) 