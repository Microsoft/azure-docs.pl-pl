---
title: Opis aktualizacji urządzenia do IoT Hub importowania | Microsoft Docs
description: Kluczowe założenia dotyczące importowania nowej aktualizacji do aktualizacji urządzenia dla IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 4cd5e0c016b98a3dc9336237a5c1b14e6b0f5789
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040587"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>Importowanie aktualizacji do usługi IoT Hub
W celu wdrożenia aktualizacji na urządzeniach z aktualizacji urządzenia dla IoT Hub należy najpierw _zaimportować_ tę aktualizację do usługi aktualizacji urządzenia. Poniżej przedstawiono omówienie niektórych ważnych koncepcji, które należy zrozumieć, gdy chodzi o Importowanie aktualizacji.

## <a name="import-manifest"></a>Importuj manifest

Manifest importu to plik JSON, który definiuje ważne informacje o aktualizowanej aktualizacji. W ramach procesu importowania zostanie przesłany manifest importu oraz skojarzony plik lub pliki aktualizacji (takie jak pakiet aktualizacji oprogramowania układowego). Metadane, które są zdefiniowane w manifeście importu, są używane do pozyskiwania aktualizacji. Niektóre metadane są również używane w czasie wdrażania — na przykład w celu sprawdzenia, czy aktualizacja została zainstalowana prawidłowo.

Manifest importu zawiera kilka elementów reprezentujących ważną aktualizację urządzenia dla IoT Hub pojęć. Te pojęcia zostały przedstawione poniżej.

### <a name="update-identity-update-id"></a>Aktualizuj tożsamość (identyfikator aktualizacji)

Aktualizacja tożsamości reprezentuje unikatowy identyfikator aktualizacji. Definiuje ona ważne właściwości dotyczące importowanej aktualizacji. Tożsamość aktualizacji składa się z trzech części:
* Dostawca: jest to jednostka, która tworzy lub bezpośrednio odpowiada na aktualizację. Często będzie to nazwa firmy.
* Name: Identyfikator klasy aktualizacji. Klasa może być dowolna. Często będzie to nazwa urządzenia lub modelu.
* Wersja: jest to numer wersji odróżniający tę aktualizację od innych, którzy mają tego samego dostawcę i nazwę. Ta wersja jest używana przez aktualizację urządzenia dla usługi IoT Hub i może być niezgodna z wersją pojedynczego składnika oprogramowania na urządzeniu. 

### <a name="compatibility"></a>Zgodność

Aby uprościć wdrażanie aktualizacji, aktualizacja urządzenia dla IoT Hub porównuje właściwości zgodności dla aktualizacji, które są zdefiniowane w manifeście importu z odpowiednimi właściwościami urządzenia. Tylko aktualizacje, które mają pasujące właściwości, będą zwracane i dostępne do wdrożenia.

### <a name="installedcriteria"></a>InstalledCriteria

InstalledCriteria jest używany przez agenta aktualizacji na urządzeniu w celu ustalenia, czy aktualizacja została zainstalowana pomyślnie.


## <a name="next-steps"></a>Następne kroki

Jeśli wszystko jest gotowe, wypróbuj [Przewodnik importowania How-To](./import-update.md), który przeprowadzi Cię przez krok proces importowania.


