---
title: Informacje o zestawie Azure Kinect DK
description: Omówienie narzędzi platformy Azure urządzenia Kinect Developer Kit (DK) i zintegrowanych usług.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: Azure, urządzenia Kinect, omówienie, dev Kit, DK, urządzenie, Głębokość, śledzenie treści, mowę, usługi poznawcze, zestawy SDK
ms.openlocfilehash: d188d37c0247aebb16f51b4404da81d469136468
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359657"
---
# <a name="about-azure-kinect-dk"></a>Informacje o zestawie Azure Kinect DK

 ![Azure Kinect DK](./media/index/device-image.jpg)

Azure Kinect DK to zestaw dla deweloperów z zaawansowanymi czujnikami AI udostępniający złożone modele przetwarzania obrazów i mowy.  Kinect to niewielkie urządzenie zawierające czujnik głębi, przestrzenną macierz mikrofonów z kamerą i czujnik orientacji, które oferuje wiele trybów, opcji i zestawów SDK. Jest ona dostępna do zakupu w [Sklepie Microsoft Online](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq).

Środowisko deweloperskie usługi Azure urządzenia Kinect DK składa się z następujących wielu zestawów SDK:

- Zestaw SDK czujnika dla czujnika niskiego poziomu i dostępu do urządzenia.
- Zestaw SDK śledzenia treści dla treści śledzenia w 3D.
- Zestaw Speech Cognitive Services SDK do włączania dostępu do mikrofonu i usług mowy opartych na chmurze platformy Azure.

Ponadto usługi poznawcze mogą być używane z kamerą RGB urządzenia.

   ![Diagram zestawów SDK usługi Azure urządzenia Kinect](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>Azure Kinect Sensor SDK

Zestaw SDK czujnika usługi Azure urządzenia Kinect zapewnia dostęp czujnika niskiego poziomu dla czujników sprzętowych i konfiguracji urządzeń platformy Azure urządzenia Kinect.

Aby dowiedzieć się więcej o zestawie SDK usługi Azure urządzenia Kinect, zobacz [using SDK czujnika](about-sensor-sdk.md).

### <a name="azure-kinect-sensor-sdk-features"></a>Funkcje zestawu SDK czujnika usługi Azure urządzenia Kinect

Zestaw SDK czujnika ma następujące funkcje, które działają po zainstalowaniu i uruchomieniu na platformie Azure urządzenia Kinect DK:

- Głębokość dostępu do aparatu i Sterowanie trybem (tryb pasywnego IR oraz szeroki i wąski tryb głębokości widoku) 
- Dostęp i kontrola w aparacie RGB (na przykład ekspozycja i białe saldo) 
- Dostęp do czujnika ruchu (żyroskop i przyspieszeniomierz) 
- Zsynchronizowana głębokość — przesyłanie strumieniowe aparatu RGB z konfigurowalnymi opóźnieniami między kamerami 
- Kontrola synchronizacji urządzeń zewnętrznych z konfigurowalnym przesunięciem opóźnienia między urządzeniami 
- Ramka aparatu — dostęp do metadanych dla rozdzielczości obrazu, sygnatury czasowej itp. 
- Dostęp do danych kalibracji urządzenia 

### <a name="azure-kinect-sensor-sdk-tools"></a>Narzędzia usługi Azure urządzenia Kinect sensor SDK

Następujące narzędzia są dostępne w zestawie SDK czujnika:

- Narzędzie do przeglądania do monitorowania strumieni danych urządzenia i konfigurowania różnych trybów.
- Narzędzie do rejestrowania czujnika i interfejs API czytnika odtwarzania, który używa formatu kontenera Matroska.
- Narzędzie do aktualizacji oprogramowania układowego w usłudze Azure urządzenia Kinect DK.

## <a name="azure-kinect-body-tracking-sdk"></a>Azure Kinect Body Tracking SDK

Zestaw SDK śledzenia treści zawiera bibliotekę systemu Windows i środowisko uruchomieniowe do śledzenia treści w 3D, gdy jest używany z sprzętem usługi Azure urządzenia Kinect DK.

### <a name="azure-kinect-body-tracking-features"></a>Funkcje śledzenia treści usługi Azure urządzenia Kinect

Następujące funkcje śledzenia treści są dostępne w dołączonym zestawie SDK:

- Zapewnia segmentację treści.
- Zawiera poprawnie "szkieletowo" dla każdej częściowej lub pełnej treści w FOV.
- Oferuje unikatową tożsamość dla każdej treści.
- Może śledzić ciała w czasie.

### <a name="azure-kinect-body-tracking-tools"></a>Narzędzia do śledzenia treści w usłudze Azure urządzenia Kinect

- Śledzenie treści zawiera narzędzie do przeglądania, które umożliwia śledzenie treści w 3D.

## <a name="speech-cognitive-services-sdk"></a>Zestaw SDK usługi rozpoznawania mowy

Zestaw Speech SDK umożliwia korzystanie z usługi mowy połączonej z platformą Azure.

### <a name="speech-services"></a>Usługi mowy

- Zamiana mowy na tekst
- Tłumaczenie mowy
- Zamiana tekstu na mowę

>[!NOTE]
>Na platformie Azure urządzenia Kinect DK nie ma głośników.

Aby uzyskać dodatkowe informacje i informacje, zapoznaj się z [dokumentacją usługi mowy](../cognitive-services/speech-service/index.yml).

## <a name="vision-services"></a>Usługi wizji

Poniższe [usługi Azure poznawczych usług](https://azure.microsoft.com/services/cognitive-services/directory/vision/) zapewniają usługi platformy Azure, które mogą identyfikować i analizować zawartość w obrazach i wideo.

- [Przetwarzanie obrazów](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [Rozpoznawanie twarzy](https://azure.microsoft.com/services/cognitive-services/face/)
- [Indeksator wideo](https://azure.microsoft.com/services/media-services/video-indexer/)
- [Moderator zawartości](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [Niestandardowa wizja](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

Usługi są stale rozwijane i ulepszane, dlatego należy regularnie sprawdzać dostępność nowych lub dodatkowych [usług poznawczych](https://azure.microsoft.com/services/cognitive-services/) w celu ulepszania aplikacji. Wczesne spojrzenie na nowe usługi można znaleźć w [laboratoriach usługi poznawczej](https://labs.cognitive.microsoft.com/).

## <a name="azure-kinect-hardware-requirements"></a>Wymagania sprzętowe platformy Azure urządzenia Kinect

Usługa Azure urządzenia Kinect DK integruje najnowszą technologię czujnika firmy Microsoft z urządzeniami podłączonymi do jednego złącza USB. Aby uzyskać więcej informacji, zobacz artykuł [Specyfikacja sprzętu platformy Azure urządzenia Kinect DK](hardware-specification.md).

## <a name="next-steps"></a>Następne kroki

Masz teraz przegląd usługi Azure urządzenia Kinect DK. Następnym krokiem jest szczegółowe i skonfigurowanie go!

> [!div class="nextstepaction"]
>[Szybki Start: Konfigurowanie platformy Azure urządzenia Kinect DK](set-up-azure-kinect-dk.md)