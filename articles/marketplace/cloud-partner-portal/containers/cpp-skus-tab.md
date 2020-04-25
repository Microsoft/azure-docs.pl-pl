---
title: Jednostki SKU dla obrazu kontenerów platformy Azure | Portal Azure Marketplace
description: Skonfiguruj jednostki SKU dla kontenera platformy Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: 5f37d157e11b8cd7cf093fa558e81d9a1ce345a9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146199"
---
# <a name="container-skus-tab"></a>Karta jednostki SKU kontenera

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami kontenerów platformy Azure w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty kontenera platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) , aby zarządzać migrowanymi ofertami.

Karta **jednostki SKU** nowej strony **oferty** umożliwia utworzenie co najmniej jednej jednostki SKU i skojarzenie ich z nową ofertą.  Możesz użyć różnych jednostek SKU do rozróżnienia rozwiązania przez zestawy funkcji, modele rozliczeń lub inne cechy.

## <a name="sku-settings"></a>Ustawienia jednostki SKU

Po rozpoczęciu tworzenia nowej oferty nie ma żadnych jednostek SKU skojarzonych z ofertą. Aby utworzyć nową jednostkę SKU, wykonaj następujące kroki:

1. Na karcie jednostki SKU wybierz pozycję **Nowa jednostka SKU**

   ![Nowy monit jednostki SKU](./media/containers-sku-settings.png)

2. Podaj wymaganą jednostkę SKU i informacje o kontenerze. Każda jednostka SKU odpowiada obrazowi kontenera. Istnieją dwie części jednostki SKU:

    -   Metadane jednostki SKU
    -   Metadane kontenera


### <a name="sku-metadata"></a>Metadane jednostki SKU

Metadane jednostki SKU zawierają informacje o wyświetlaniu w sklepie dla listy kontenerów.

![Metadane jednostki SKU](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metadane kontenera

Metadane kontenera zawierają informacje referencyjne dotyczące szczegółów repozytorium obrazów w Azure Container Registry (ACR). Witryna Azure Marketplace kopiuje ten obraz do specyficznego dla witryny Marketplace rejestru publicznego, a następnie udostępnia go klientom po certyfikacji. Wszystkie żądania od użytkownika platformy Azure korzystające z obrazu kontenera portalu Azure Marketplace są udostępniane z publicznego rejestru portalu Marketplace, a nie ACR.

![Metadane kontenera](./media/containers-image-repository.png)
    
**Szczegóły repozytorium obrazu** w poprzednim przechwyceniu ekranu zawierają następujące pola.  Wymagane pola są indicted przez gwiazdkę (*).

-   **Identyfikator\* subskrypcji** — Identyfikator subskrypcji platformy Azure, w której znajduje się ACR.
-   **Nazwa\* grupy zasobów** — nazwa grupy zasobów ACR.
-   **Nazwa\* rejestru** — nazwa ACR.
-   **Nazwa\* repozytorium** — Nazwa repozytorium. Po ustawieniu tej nazwy nie można zmienić tej wartości. Użyj unikatowej nazwy, aby uniknąć konfliktu z innymi ofertami na Twoim koncie.
-   **Nazwa\* użytkownika** — nazwa użytkownika (nazwa użytkownika administratora) skojarzona z obrazem ACR.
-   **Hasło\* ** — hasło skojarzone z obrazem ACR.

    >[!NOTE]
    >Wymagana jest nazwa użytkownika i hasło, aby zapewnić partnerom dostęp do ACR wspomnianego w procesie publikacji.


### <a name="image-version"></a>Wersja obrazu

Podczas publikowania obrazu kontenera można podać jeden lub więcej tagów obrazu i algorytmy SHA.

**Tag\* obrazu lub skrót**
 
- Ten tag lub skrót muszą zawierać `latest` tag i tag wersji (na przykład, rozpoczynając od `xx.xx.xx-` gdzie XX jest liczbą). Powinny one być [tagami manifestu](https://github.com/estesp/manifest-tool) dla wielu platform docelowych. Wszystkie Tagi, do których odwołuje się tag manifestu, również muszą zostać dodane, aby można było je przekazać. 
- Możesz dodać kilka wersji kontenera przy użyciu tagów. Wszystkie Tagi manifestu (oprócz `latest`) muszą zaczynać się `X.Y-` od `X.Y.Z-` lub gdzie X, Y, Z są liczbami całkowitymi. <br/> Na `latest` przykład, jeśli tag wskazuje na `1.0.1-linux-x64`, `1.0.1-linux-arm32`, i `1.0.1-windows-arm32`, należy dodać te Tagi w tym miejscu.

>[!NOTE]
>Pamiętaj, aby dodać **tag testowy** do obrazu, aby można było zidentyfikować obraz podczas testowania.


## <a name="next-steps"></a>Następne kroki

Skorzystaj z [karty Marketplace](./cpp-marketplace-tab.md) , aby utworzyć opis witryny Marketplace dla swojej oferty. 
