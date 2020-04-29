---
title: Zarządzanie modelami IoT Plug and Play w wersji zapoznawczej w repozytorium | Microsoft Docs "
description: Jak zarządzać modelami możliwości urządzeń w repozytorium za pomocą portalu Azure Certified for IoT, interfejsu wiersza polecenia platformy Azure i programu Visual Studio Code.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159153"
---
# <a name="manage-models-in-the-repository"></a>Zarządzanie modelami w repozytorium

Repozytorium modeli Plug and Play IoT w wersji zapoznawczej przechowuje modele możliwości urządzeń i interfejsy. Repozytorium sprawia, że modele i interfejsy są wykrywalne i zużywane przez deweloperów rozwiązań.

Istnieją trzy narzędzia, których można użyć do zarządzania repozytorium:

- Portal certyfikatu platformy Azure dla IoT
- Interfejs wiersza polecenia platformy Azure
- Visual Studio Code

## <a name="model-repositories"></a>Repozytoria modelu

Istnieją dwa typy repozytorium modelu do przechowywania modeli możliwości urządzeń i interfejsów:

- Istnieje jedno _publiczne repozytorium_ , w którym są przechowywane modele możliwości urządzeń i interfejsy dla urządzeń w [katalogu certyfikatu platformy Azure dla IoT](https://aka.ms/iotdevcat). To repozytorium przechowuje również [typowe interfejsy](./concepts-common-interfaces.md) i [DCMs i interfejsy opublikowane przez partnerów firmy Microsoft](./howto-onboard-portal.md). Aby dowiedzieć się, jak zatwierdzić urządzenie i dodać jego model możliwości urządzenia do repozytorium publicznego, zobacz samouczek [certyfikowania urządzenia IoT Plug and Play](./tutorial-certification-test.md).
- Istnieje wiele _repozytoriów firmy_. Repozytorium firmowe jest tworzone automatycznie dla Twojej organizacji, gdy dołączysz [się do portalu Azure Certified for IoT](./howto-onboard-portal.md). Za pomocą repozytorium firmy można przechowywać modele i interfejsy możliwości urządzenia podczas tworzenia i testowania.

## <a name="azure-certified-for-iot-portal"></a>Azure Certified for IoT — Portal

W [portalu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com)można wykonać następujące zadania:

- [Ukończ proces certyfikacji urządzenia IoT](./tutorial-certification-test.md).
- Znajdź modele możliwości urządzeń Plug and Play IoT. Za pomocą tych modeli można [szybko tworzyć urządzenia gotowe do użycia w usłudze IoT i integrować je z rozwiązaniami](./quickstart-connect-pnp-device-solution-node.md).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia zarządzanie modelami możliwości urządzeń i interfejsami w usłudze IoT Plug and Play repozytoria modelu publicznego i firmy. Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący [instalacji i używania rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure](./howto-install-pnp-cli.md) .

## <a name="visual-studio-code"></a>Visual Studio Code

Aby otworzyć widok **repozytorium modeli** w Visual Studio Code.

1. Otwórz Visual Studio Code, użyj **kombinacji klawiszy Ctrl + Shift + P**, wpisz i wybierz pozycję **IoT Plug and Play: Otwórz repozytorium modeli**.

1. Możesz **otworzyć repozytorium modelu publicznego** lub **otworzyć repozytorium modelu organizacyjnego**. W przypadku repozytorium modelu firmy należy wprowadzić parametry połączenia repozytorium modeli. Te parametry połączenia można znaleźć w [portalu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) na karcie **Parametry połączenia** dla **repozytorium firmy**.

1. Zostanie otwarta nowa karta widok **repozytorium modeli** .

    Ten widok umożliwia dodawanie, pobieranie i usuwanie modeli i interfejsów możliwości urządzeń. Możesz użyć filtru, aby znaleźć określone elementy na liście.

1. Aby przełączać się między repozytorium modelu firmy a publicznym repozytorium modeli, użyj **kombinacji klawiszy Ctrl + Shift + P**, wpisz i wybierz pozycję **IoT Plug and Play: Wyloguj repozytorium modeli**. Następnie użyj ponownie polecenia **Plug and Play IoT: Otwórz repozytorium modeli** .

> [!NOTE]
> W VS Code repozytorium modelu publicznego jest tylko do odczytu. Partnerzy firmy Microsoft mogą zaktualizować repozytorium publiczne w [portalu certyfikatu platformy Azure dla IoT](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Następne kroki

Sugerowany następny krok to Dowiedz się, jak [przesłać urządzenie usługi IoT Plug and Play do certyfikacji](tutorial-certification-test.md).
