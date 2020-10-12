---
title: Korzystanie z interfejsu wiersza polecenia usługi Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Zobacz, jak rozpocząć pracę z interfejsem wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji i korzystać z niego.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2c642b2441d1f30c31e707a237732e028f548ac5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89298185"
---
# <a name="use-the-azure-digital-twins-cli"></a>Korzystanie z interfejsu wiersza polecenia usługi Azure Digital Twins

Poza zarządzaniem wystąpieniem usługi Azure Digital bliźniaczych reprezentacji w Azure Portal, usługa Azure Digital bliźniaczych reprezentacji ma **interfejs wiersza polecenia (CLI)** , którego można użyć do wykonywania większości najważniejszych działań związanych z usługą, w tym:
* Zarządzanie wystąpieniem usługi Azure Digital bliźniaczych reprezentacji
* Zarządzanie modelami
* Zarządzanie cyfrowym bliźniaczych reprezentacji
* Zarządzanie relacjami bliźniaczymi
* Konfigurowanie punktów końcowych
* Zarządzanie [trasami](concepts-route-events.md)
* Konfigurowanie [zabezpieczeń](concepts-security.md) za pośrednictwem kontroli dostępu opartej na ROLACH (RBAC)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>Używa (Wdróż i Weryfikuj)

Oprócz ogólnego zarządzania wystąpieniem, interfejs wiersza polecenia jest również użytecznym narzędziem do wdrażania i walidacji.
* Za pomocą poleceń płaszczyzny kontroli można wykonać wdrożenie nowego wystąpienia powtarzalnego lub zautomatyzowanego.
* Za pomocą poleceń płaszczyzny danych można szybko sprawdzać wartości w wystąpieniu i sprawdzać, czy operacje zostały wykonane zgodnie z oczekiwaniami.

## <a name="get-the-extension"></a>Pobierz rozszerzenie

Polecenia usługi Azure Digital bliźniaczych reprezentacji są częścią [rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension). Dokumentację referencyjną tych poleceń można wyświetlić w ramach `az iot` zestawu poleceń: [AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

Aby upewnić się, że masz najnowszą wersję rozszerzenia, wykonaj te kroki. Te polecenia można uruchomić w [Azure Cloud Shell](../cloud-shell/overview.md) lub [lokalnego interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z alternatywą dla poleceń interfejsu wiersza polecenia, zobacz jak zarządzać wystąpieniem usługi Azure Digital bliźniaczych reprezentacji przy użyciu interfejsów API i zestawów SDK:
* [*Instrukcje: korzystanie z interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji na platformie Azure*](how-to-use-apis-sdks.md)
