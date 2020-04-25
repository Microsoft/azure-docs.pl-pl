---
title: Przegląd udostępniania oferty modułu Azure IoT Edge Portal Azure Marketplace
description: Przegląd procesu publikowania oferty modułu IoT Edge w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: e9116e5cdb3bd9ed61205ceabd4d51c96c6aadc2
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144666"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Przegląd udostępniania oferty modułu IoT Edge

>[!Important]
>Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą modułu IoT Edge w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Create an IoT Edge module](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) , aby zarządzać migrowanymi ofertami.

<table> <tr> <td>W tej sekcji opisano sposób publikowania nowej oferty modułu Azure IoT Edge w <a href="https://azuremarketplace.microsoft.com">portalu Microsoft Azure Marketplace</a>. Moduł IoT Edge jest kontenerem zgodnym z platformą Docker, który jest uruchamiany na urządzeniu IoT Edge. Moduły Azure IoT Edge są najmniejszą jednostką obliczeniową zarządzaną przez IoT Edge i mogą zawierać usługi platformy Azure lub niestandardowy kod rozwiązania. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Proces publikowania

Poniżej przedstawiono ogólne kroki publikowania oferty modułu IoT Edge:

1. Tworzenie oferty<br> Podaj szczegółowe informacje o ofercie. Te informacje obejmują: opis oferty, materiały marketingowe, informacje o pomocy technicznej i specyfikacje zasobów.

2. Tworzenie zasobów firmowych i technicznych<br> Utwórz zasoby biznesowe (dokumenty prawne i materiały marketingowe) i zasoby techniczne dla skojarzonego rozwiązania (IoT Edge obrazy modułu hostowane w Azure Container Registry.

3. Tworzenie jednostki SKU<br> Utwórz jednostki SKU skojarzone z ofertą. Dla każdego obrazu, który ma zostać opublikowany, wymagana jest unikatowa jednostka SKU.

4. Certyfikowanie i publikowanie oferty <br>Po zakończeniu oferty i zasobów technicznych można przesłać ofertę. To przesyłanie rozpocznie proces publikowania. W trakcie tego procesu rozwiązanie jest testowane, weryfikowane, certyfikowane, a następnie "trafia na żywo" w portalu Azure Marketplace.

## <a name="parts-of-an-offer"></a>Części oferty

Poniższe artykuły obejmują podstawowe elementy oferty modułu IoT Edge.

- [Wymagania wstępne](./cpp-prerequisites.md) <br>W tym artykule wymieniono wymagania techniczne i biznesowe przed utworzeniem lub opublikowaniem oferty modułu IoT Edge.
- [Przygotowywanie zasobów technicznych dla modułu usługi IoT Edge](./cpp-create-technical-assets.md) <br>W tym artykule opisano sposób przygotowania zasobów technicznych dla modułu IoT Edge. Te zasoby muszą spełniać wszystkie wymagane kryteria techniczne przed opublikowaniem modułu IoT Edge w portalu Azure Marketplace.
- [Tworzenie oferty modułu usługi IoT Edge](./cpp-create-offer.md) <br>W tym artykule wymieniono kroki wymagane do utworzenia nowego wpisu oferty modułu IoT Edge przy użyciu [Portal Cloud partner](https://cloudpartner.azure.com).
- [Publikowanie oferty modułu usługi IoT Edge](./cpp-publish-offer.md)<br> W tym artykule opisano sposób przesyłania oferty do opublikowania w portalu Azure Marketplace.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [wymaganiami technicznymi i biznesowymi](./cpp-prerequisites.md) dotyczącymi publikowania modułu IoT Edge w Microsoft Azure Marketplace.
