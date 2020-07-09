---
title: Korzystanie z interfejsu wiersza polecenia usługi Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Zobacz, jak rozpocząć pracę z interfejsem wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji i korzystać z niego.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 53b20ded8e4b4a003beff1ef8489ecd9ff3451ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725305"
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

Polecenia usługi Azure Digital bliźniaczych reprezentacji są częścią [rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension). Dokumentację referencyjną tych poleceń można wyświetlić w ramach `az iot` zestawu poleceń: [AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

## <a name="deploy-and-validate"></a>Wdróż i sprawdź poprawność

Oprócz ogólnego zarządzania wystąpieniem, interfejs wiersza polecenia jest również użytecznym narzędziem do wdrażania i walidacji.
* Za pomocą poleceń płaszczyzny kontroli można wykonać wdrożenie nowego wystąpienia powtarzalnego lub zautomatyzowanego.
* Za pomocą poleceń płaszczyzny danych można szybko sprawdzać wartości w wystąpieniu i sprawdzać, czy operacje zostały wykonane zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z alternatywą dla poleceń interfejsu wiersza polecenia, zobacz jak zarządzać wystąpieniem usługi Azure Digital bliźniaczych reprezentacji przy użyciu interfejsów API i zestawów SDK:
* [Instrukcje: korzystanie z interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji na platformie Azure](how-to-use-apis-sdks.md)
