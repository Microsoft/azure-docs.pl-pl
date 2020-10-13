---
title: Korzystanie z interfejsu wiersza polecenia usługi Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Zobacz, jak rozpocząć pracę z interfejsem wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji i korzystać z niego.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 65462937db4711066bdb8b31cc22de508321d701
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950911"
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

Polecenia usługi Azure Digital bliźniaczych reprezentacji są częścią [rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension). Pełną listę poleceń i ich użycia można wyświetlić jako część dokumentacji referencyjnej dotyczącej `az iot` zestawu poleceń: [ *AZ DT* Command Reference](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true).

Aby upewnić się, że masz najnowszą wersję rozszerzenia, wykonaj te kroki. Te polecenia można uruchomić w [Azure Cloud Shell](../cloud-shell/overview.md) lub [lokalnego interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsem wiersza polecenia i jego pełnym zestawem poleceń, korzystając z dokumentacji referencyjnej:
* [polecenie *AZ DT* Command Reference](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
