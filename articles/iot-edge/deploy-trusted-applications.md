---
title: Zaufane aplikacje jako moduły Azure IoT Edge
description: Korzystanie z zestawu SDK i interfejsu API Open enklawy w celu zapisywania zaufanych aplikacji i wdrażania ich jako modułów IoT Edge na potrzeby przetwarzania poufnego
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: kgremban
ms.openlocfilehash: d81cc6c94c04c683362fd12cd6777c304a4b0a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361980"
---
# <a name="confidential-computing-at-the-edge"></a>Poufne dane obliczeniowe na brzegu

Azure IoT Edge obsługuje aplikacje zaufane, które działają w ramach bezpiecznego enclaves na urządzeniu. Szyfrowanie zapewnia zabezpieczenia danych podczas ich przesyłania lub w spoczynku, ale enclaves zapewnia bezpieczeństwo danych i obciążeń, które są używane. IoT Edge obsługuje funkcję Open enklawy jako standard do tworzenia zaufanych aplikacji.

Bezpieczeństwo ma zawsze ważny fokus Internet rzeczy (IoT), ponieważ często urządzenia IoT są często na świecie, a nie zabezpieczone w ramach prywatnego obiektu. To narażenie polega na wykorzystaniu urządzeń narażonych na manipulowanie i podrabianie, ponieważ są one fizycznie dostępne dla nieprawidłowych aktorów. IoT Edge urządzenia mają jeszcze większą potrzebę do zaufania i integralności, ponieważ umożliwiają uruchamianie poufnych obciążeń na krawędzi. W przeciwieństwie do częstych czujników i siłowników, te inteligentne urządzenia brzegowe mogą uwidaczniać poufne obciążenia, które wcześniej były uruchamiane tylko w chronionych środowiskach w chmurze lub lokalnych.

[IoT Edge Security Manager](iot-edge-security-manager.md) dotyczy jednego z wątpliwych wyzwań obliczeniowych. Program Security Manager używa sprzętowego modułu zabezpieczeń (HSM) do ochrony obciążeń związanych z tożsamościami i procesów wykonywanych przez IoT Edge urządzenia.

Innym aspektem odnoszącym się do poufności jest ochrona danych używanych na krawędzi. *Zaufane środowisko wykonawcze* (tee) to bezpieczne, izolowane środowisko na procesorze i czasami określane jako *enklawy*. *Zaufana aplikacja* jest aplikacją działającą w enklawy. Ze względu na charakter enclaves zaufane aplikacje są chronione przed innymi aplikacjami uruchomionymi w głównym procesorze lub w TEE.

## <a name="trusted-applications-on-iot-edge"></a>Zaufane aplikacje na IoT Edge

Zaufane aplikacje są szyfrowane podczas przesyłania i przechowywania, a jedynie odszyfrowywane w ramach zaufanego środowiska wykonawczego. Ten standard ma wartość true w przypadku aplikacji zaufanych wdrożonych jako moduły IoT Edge.

Deweloper tworzy zaufaną aplikację i pakuje ją jako moduł IoT Edge. Aplikacja jest szyfrowana przed wypychaniem do rejestru kontenerów. Aplikacja pozostaje zaszyfrowana w całym procesie wdrażania IoT Edge, dopóki moduł nie zostanie uruchomiony na urządzeniu IoT Edge. Gdy zaufana aplikacja znajduje się w TEEu urządzenia, zostanie odszyfrowana i może rozpocząć wykonywanie.

![Diagram — zaufane aplikacje są szyfrowane w modułach IoT Edge, dopóki nie zostaną wdrożone w bezpiecznej enklawy](./media/deploy-trusted-applications/trusted-applications-encrypted.png)

Zaufane aplikacje na IoT Edge są logicznymi rozszerzeniami do [przetwarzania poufnego platformy Azure](../confidential-computing/overview.md). Obciążenia, które działają w ramach bezpiecznego enclaves w chmurze, można również wdrożyć do uruchamiania w ramach bezpiecznego enclavesu na krawędzi.

## <a name="open-enclave"></a>Otwórz enklawy

[Open ENKLAWY SDK](https://openenclave.io/sdk/) to projekt open source, który pomaga deweloperom tworzyć zaufane aplikacje dla wielu platform i środowisk. Otwarty zestaw enklawy SDK działa w ramach zaufanego środowiska wykonawczego na urządzeniu, podczas gdy interfejs API Open enklawy działa jako interfejs między TEE i nieTEEnym środowiskiem przetwarzania.

Open enklawy obsługuje wiele platform sprzętowych. Obsługa IoT Edge dla enclaves wymaga obecnie otwartego systemu operacyjnego TEE (OP-TEE OS). Aby dowiedzieć się więcej, zobacz [Open ENKLAWY SDK for op-tee system operacyjny](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

Repozytorium open enklawy zawiera również przykłady ułatwiające rozpoczęcie pracy. Aby uzyskać więcej informacji, wybierz jeden z artykułów wprowadzających:

* [Kompilowanie przykładów Open enklawy SDK w systemie Linux](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Kompilowanie przykładów zestawu SDK open enklawy w systemie Windows](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>Sprzęt

Obecnie [TrustBox przez Scalys](https://scalys.com/trustbox-industrial/) jest jedynym urządzeniem obsługiwanym w ramach umów dotyczących usług dla producentów na potrzeby wdrażania zaufanych aplikacji jako modułów IoT Edge. TrustBox jest oparta na urządzeniach TrustBox Edge i TrustBox EdgeXL, które są wstępnie załadowane z zestawem SDK open enklawy i Azure IoT Edge.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do otwierania enklawy dla Scalys TrustBox](https://aka.ms/scalys-trustbox-edge-get-started).

## <a name="develop-and-deploy"></a>Opracowywanie i wdrażanie

Gdy wszystko będzie gotowe do opracowania i wdrożenia zaufanej aplikacji, rozszerzenie [Microsoft Open enklawy](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) dla Visual Studio Code może pomóc. Możesz użyć systemu Linux lub Windows jako komputera deweloperskiego do opracowania modułów dla TrustBox.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak rozpocząć tworzenie aplikacji zaufanych jako modułów IoT Edge przy użyciu [rozszerzenia Open enklawy dla Visual Studio Code](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)
