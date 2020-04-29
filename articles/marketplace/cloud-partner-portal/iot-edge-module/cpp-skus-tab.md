---
title: Jednostki SKU modułu Azure IoT Edge | Portal Azure Marketplace
description: Utwórz jednostki SKU dla modułu IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 0a394c14842fe0c4eb4baa18cbb13acb8ca3170a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142460"
---
# <a name="iot-edge-module-skus-tab"></a>Karta jednostki SKU modułu IoT Edge

>[!Important]
>Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą modułu IoT Edge w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Create an IoT Edge module](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) , aby zarządzać migrowanymi ofertami.

Karta **jednostki SKU** nowej strony **oferty** umożliwia utworzenie co najmniej jednej jednostki SKU i skojarzenie ich z nową ofertą.  Możesz użyć różnych jednostek SKU do rozróżnienia rozwiązania przez zestawy funkcji, modele rozliczeń lub inne cechy.


## <a name="sku-settings"></a>Ustawienia jednostki SKU

Po rozpoczęciu tworzenia nowej oferty nie ma żadnych jednostek SKU skojarzonych z ofertą. Aby utworzyć nową jednostkę SKU, wykonaj następujące kroki:

- Na stronie **IoT Edge modułów > nowej oferty** wybierz kartę **jednostki SKU** .
- W obszarze jednostki SKU wybierz pozycję **+ Nowa jednostka SKU** , aby otworzyć okno dialogowe.

  ![Przycisk nowej jednostki SKU na karcie Nowa oferta dla modułów IoT Edge](./media/iot-edge-module-skus-tab-new-sku.png)

- W oknie dialogowym **Nowa jednostka SKU** wprowadź identyfikator jednostki SKU, a następnie wybierz przycisk **OK**.
(Poniższa tabela zawiera konwencje nazewnictwa identyfikatorów).

Karta **jednostki SKU** jest odświeżana i wyświetla pola, które można edytować w celu skonfigurowania jednostki SKU. Gwiazdka (*) dołączona do nazwy pola wskazuje, że jest to wymagane.

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **IDENTYFIKATOR JEDNOSTKI SKU\***       | Identyfikator dla tej jednostki SKU. Ta nazwa ma maksymalnie 50 znaków składających się z małych znaków alfanumerycznych lub kresek (-), ale nie może kończyć się kreską. **Uwaga:** Tej nazwy nie można zmienić po opublikowaniu oferty. Nazwa jest publicznie widoczna w adresach URL produktów. |
|  |  |


## <a name="sku-details"></a>Szczegóły jednostki SKU

Skonfiguruj **szczegóły jednostki SKU** , aby określić sposób wyświetlania jednostki SKU w witrynie Azure Marketplace i witrynach sieci Web Azure Portal.

![Metadane jednostki SKU modułu IoT Edge](media/iot-edge-module-skus-tab-metadata.png)

W poniższej tabeli opisano przeznaczenie, zawartość i formatowanie pól w obszarze **szczegóły jednostki SKU**. Wymagane pola są indicted przez gwiazdkę (*).

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Tytuły\***        | Tytuł dla tej jednostki SKU. Maksymalna długość 50 znaków. <br/> Zostanie on wyświetlony w Azure Portal i będzie używany jako domyślna nazwa modułu (bez spacji i znaków specjalnych) podczas wdrażania. Zobacz poniższe obrazy, aby zobaczyć dokładnie miejsce wyświetlania tego pola.|
| **Podsumowanie\***      | Krótkie podsumowanie tej jednostki SKU. Maksymalna długość 100 znaków. **Nie** Sumuj oferty, tylko dla jednostki SKU.  To podsumowanie zostanie wyświetlone w portalu Azure Marketplace. Zobacz poniższe obrazy, aby zobaczyć dokładnie miejsce wyświetlania tego pola.|
| **Opis\***  | Krótki opis tej jednostki SKU. Maksymalna długość 3000 znaków. NIE należy opisywać oferty, ale tylko tej jednostki SKU. Zostanie ona wyświetlona w portalu Azure Marketplace i w Azure Portal. W Azure Portal zostanie on dołączony do opisu witryny Marketplace opisującej ofertę zdefiniowaną na karcie Marketplace.  Może być taka sama jak w podsumowaniu jednostki SKU. Zobacz poniższe obrazy, aby zobaczyć dokładnie miejsce wyświetlania tego pola.|
| **Ukryj tę jednostkę SKU\*** | Zachowaj ustawienie domyślne, które **nie**jest. |
|  |  |


### <a name="sku-example"></a>Przykład jednostki SKU

 W poniższych przykładach pokazano, jak pola **tytuł**jednostki SKU, **Podsumowanie**i **Opis** pojawiają się w różnych widokach.
 

#### <a name="on-the-azure-marketplace-website"></a>W witrynie sieci Web portalu Azure Marketplace:

- Podczas przeglądania szczegółów jednostki SKU:

    ![Jak jednostki SKU są wyświetlane w witrynie sieci Web portalu Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>W witrynie Azure Portal:

- Podczas przeglądania jednostek SKU:

    ![Jak moduł IoT Edge jest wyświetlany podczas przeglądania Azure Portal #1](media/iot-edge-module-portal-browse.png)

    ![Jak moduł IoT Edge jest wyświetlany podczas przeglądania Azure Portal #2](media/iot-edge-module-portal-product-picker.png)

- Podczas wyszukiwania jednostek SKU:

    ![Jak moduł IoT Edge jest wyświetlany podczas wyszukiwania Azure Portal](media/iot-edge-module-portal-search.png)

- Podczas przeglądania szczegółów jednostki SKU:

    ![Jak moduł IoT Edge jest wyświetlany podczas wyszukiwania szczegółów produktu w portalu](./media/iot-edge-module-portal-pdp.png)

- Podczas wdrażania modułu:
    
    ![Jak moduł IoT Edge jest wyświetlany podczas wdrażania](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>Zawartość jednostki SKU

W obszarze **obrazy modułu Edge**podaj informacje potrzebne do przekazania modułu IoT Edge.

Nadaj nam dostęp do [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR), który zawiera obraz modułu IoT Edge, dzięki czemu będziemy mogli go przekazać i zatwierdzić. Po opublikowaniu moduł IoT Edge zostanie skopiowany i rozdystrybuowany za pomocą publicznego rejestru kontenerów hostowanego w portalu Azure Marketplace.

Można docelowo wiele platform i udostępnić kilka wersji za poorednictwem tagów. Dowiedz się więcej o [tagach i wersji w temacie "przygotowanie IoT Edge zasobów technicznych modułu"](./cpp-create-technical-assets.md).

![IoT Edge obrazów modułów](./media/iot-edge-module-skus-tab-acr.png)

W poniższej tabeli opisano przeznaczenie, zawartość i formatowanie pól dla sekcji **szczegóły repozytorium obrazów** i **wersja obrazu**.  Wymagane pola są indicted przez gwiazdkę (*).


|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Szczegóły repozytorium obrazów***    |
| **Identyfikator subskrypcji\***        | Identyfikator subskrypcji platformy Azure ACR.|
| **Nazwa grupy zasobów\***      | Nazwa grupy zasobów ACR.|
| **Nazwa rejestru\***  | Nazwa rejestru ACR. Skopiuj tylko nazwę rejestru, a nie nazwę serwera logowania (na przykład bez `azurecr.io`.) |
| **Nazwa repozytorium\***  | Nazwa repozytorium ACR zawierającego moduł IoT Edge. **Uwaga:** Po ustawieniu nazwy nie można jej później zmienić. Użyj unikatowej nazwy, aby upewnić się, że żadna inna oferta w Twoim koncie nie ma takiej samej nazwy. |
| **Uż\*** | Nazwa użytkownika skojarzona z ACR (nazwa użytkownika administratora). |
| **Hasło\*** | Hasło skojarzone z Twoim ACR. |
|    |  ***Wersja obrazu***   |
| **Tag obrazu lub skrót\*** | Musi zawierać co najmniej `latest` tag i tag wersji (na przykład, rozpoczynając od `xx.xx.xx-` gdzie XX jest liczbą). Powinny one być [tagami manifestu](https://github.com/estesp/manifest-tool) dla wielu platform docelowych. Wszystkie Tagi, do których odwołuje się tag manifestu, również muszą zostać dodane, aby można było je przekazać. Możesz dodać kilka wersji modułu IoT Edge przy użyciu tagów. Wszystkie Tagi manifestu (oprócz `latest`) muszą zaczynać się `X.Y-` od `X.Y.Z-` lub gdzie X, Y, Z są liczbami całkowitymi. Dowiedz się więcej o [tagach i wersji w temacie "przygotowanie IoT Edge zasobów technicznych modułu"](./cpp-create-technical-assets.md). <br/> Na przykład, `latest` Jeśli tag wskazuje na to `1.0.1-linux-x64`, `1.0.1-linux-arm32`,,, i `1.0.1-windows-arm32`, te 6 tagów należy dodać w tym miejscu. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Pomóż klientom uruchamiać przy użyciu ustawień domyślnych

Zdefiniuj najczęściej używane ustawienia w celu wdrożenia modułu IoT Edge. Zoptymalizuj wdrożenia klientów, umożliwiając im uruchamianie modułu IoT Edge z użyciem tych wartości domyślnych.

![Domyślne ustawienia modułu IoT Edge podczas wdrażania](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

W poniższej tabeli opisano przeznaczenie, zawartość i formatowanie pól dla **tras domyślnych**, **domyślne właściwości przędzy**, **domyślne zmienne środowiskowe**i **domyślne opcje**.

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
| **Trasy domyślne**        | Każda domyślna nazwa trasy i wartość musi być krótsza niż 512 znaków. Można zdefiniować maksymalnie 5 domyślnych tras. Upewnij się, że w wartości trasy użyto prawidłowej [składni trasy](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) . Aby odwołać się do modułu, użyj jego domyślnej nazwy modułu, który będzie **tytułem jednostki SKU** bez spacji i znaków specjalnych. Aby odwołać się do innych modułów, które nie są `<FROM_MODULE_NAME>` jeszcze znane, użyj Konwencji, aby poinformować klientów, że potrzebują oni zaktualizować te informacje. Dowiedz się więcej na temat [tras IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Na przykład jeśli moduł `ContosoModule` nasłuchuje danych wejściowych `ContosoInput` i wyjściowych w `ContosoOutput`programie, warto zdefiniować następujące 2 trasy domyślne:<br/>-Nazwa #1:`ToContosoModule`<br/>-Wartość #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>-Nazwa #2:`FromContosoModuleToCloud`<br/>-Wartość #2:`FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Domyślne właściwości przędzy**      | Każda nazwa i wartość każdej domyślnej właściwości przędzy muszą być krótsze niż 512 znaków. Można zdefiniować maksymalnie 5 pożądanych właściwości nazwa/wartość. Wartości właściwości przędzy muszą być prawidłowymi wartościami JSON, niezmienionymi, bez tablic i z maksymalną zagnieżdżoną hierarchią 4. Dowiedz się więcej o [żądanych właściwościach bliźniaczych](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Na przykład, jeśli moduł obsługuje dynamicznie konfigurowalne częstotliwość odświeżania za pośrednictwem pożądanych właściwości, warto zdefiniować następującą domyślną właściwość splotu:<br/> -Nazwa #1:`RefreshRate`<br/>-Wartość #1:`60`|
| **Domyślne zmienne środowiskowe**  | Każda domyślna nazwa zmiennej środowiskowej i wartość musi być krótsza niż 512 znaków. Można zdefiniować maksymalnie 5 zmiennych środowiskowych nazwa/wartość. <br/>Jeśli na przykład moduł wymaga zaakceptowania warunków użytkowania przed rozpoczęciem, można zdefiniować następującą zmienną środowiskową:<br/> -Nazwa #1:`ACCEPT_EULA`<br/>-Wartość #1:`Y`|
| **Domyślne opcje**  | Wartość a musi być krótsza niż 512 znaków. Musi to być prawidłowy kod JSON, bez znaku ucieczki. Dowiedz się więcej o [opcjach](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Na przykład, jeśli moduł wymaga powiązania portu, można zdefiniować następujące opcje:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Wybierz pozycję **Zapisz** , aby zapisać ustawienia jednostki SKU. 


## <a name="next-steps"></a>Następne kroki

Skorzystaj z [karty Marketplace](./cpp-marketplace-tab.md) , aby utworzyć opis witryny Marketplace dla swojej oferty.
