---
title: Włącz tryb podglądu dla IoT Hub platformy Azure
description: Dowiedz się, jak włączyć tryb podglądu dla IoT Hub, dlaczego chcesz, a niektóre ostrzeżenia
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621709"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>Włącz tryb podglądu dla IoT Hub, aby spróbować wybrać nowe funkcje

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

Nowe funkcje są dostępne w publicznej wersji zapoznawczej dla IoT Hub, w tym:

- MQTT 5
- Certyfikat serwera ECC
- Negocjowanie długości fragmentu TLS
- Obsługa łańcucha urzędu certyfikacji x509 dla protokołu HTTPS/WebSocket

Te funkcje są ulepszeniami protokołu IoT Hub i warstwy uwierzytelniania, dlatego są dostępne tylko dla **nowych** centrów IoT. *Nie* są jeszcze dostępne dla istniejących centrów IoT. Aby wyświetlić podgląd tych funkcji, należy włączyć Centrum IoT Hub z włączonym trybem podglądu.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>Włącz tryb podglądu dla nowego centrum IoT Hub

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Na stronie głównej platformy Azure wybierz przycisk **+ Utwórz zasób** , a następnie wprowadź *IoT Hub* w polu Wyszukaj w **portalu Marketplace** .

1. Wybierz **IoT Hub** z wyników wyszukiwania, a następnie wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** Wypełnij pola, które [zwykle](iot-hub-create-through-portal.md) z wyjątkiem **regionu**. Wybierz jeden z następujących regionów:
    
    - Środkowe stany USA
    - West Europe
    - Azja Południowo-Wschodnia

1. Wybierz kartę **Zarządzanie** , a następnie rozwiń sekcję **Ustawienia zaawansowane** .

1. W obszarze **tryb podglądu** wybierz pozycję **włączone**. Uważnie Przejrzyj tekst ostrzegawczy.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="Obraz przedstawiający miejsce wybrania opcji Tryb podglądu podczas tworzenia nowego centrum IoT Hub":::

1. Wybierz kolejno pozycje **Dalej: przegląd + Utwórz**, a następnie pozycję **Utwórz**.

Po utworzeniu IoT Hub w trybie podglądu zawsze będzie wyświetlany ten transparent, co pozwala na korzystanie z tego Centrum IoT Hub tylko w celach podglądu: 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="Obraz przedstawiający transparent w trybie podglądu Centrum IoT Hub":::

## <a name="using-an-iot-hub-in-preview-mode"></a>Korzystanie z Centrum IoT Hub w trybie podglądu

*Nie* używaj Centrum IoT w trybie podglądu dla środowiska produkcyjnego. Tryb podglądu jest przeznaczony *tylko* do wyświetlania podglądu funkcji wybranych w górnej części tej strony. Niektóre inne ograniczenia dotyczące trybu podglądu IoT Hub są

- Niektóre istniejące funkcje IoT Hub, takie jak filtr IP, link prywatny, tożsamość zarządzana, strumienie urządzeń i tryb failover, mogą zostać nieoczekiwanie wykonane lub nie.
- Centrum IoT w trybie podglądu nie można zmienić ani uaktualnić do normalnego Centrum IoT.
- Nie możemy zagwarantować normalnego [IoT Hub SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) — nie należy używać w środowisku produkcyjnym.

> [!TIP]
> Tryb podglądu nie jest wymagany w przypadku [strumieni urządzeń](iot-hub-device-streams-overview.md) i [rozproszonego śledzenia](iot-hub-distributed-tracing.md). Aby użyć tych starszych funkcji w wersji zapoznawczej, należy postępować zgodnie z dokumentacją. 

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat obsługi programu MQTT 5, zobacz temat [Omówienie usługi IoT Hub MQTT 5 (wersja zapoznawcza)](iot-hub-mqtt-5.md)
- Aby wyświetlić podgląd certyfikatu serwera ECC, zobacz [certyfikat TLS serwera kryptografii krzywej eliptycznej (ECC) (wersja zapoznawcza)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)
- Aby wyświetlić wersję zapoznawczą negocjowania [TLS maximum fragment length negotiation (preview)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview) rozmiaru FRAGMENTu TLS