---
title: Korzystanie z interfejsu wiersza polecenia usługi Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Zobacz, jak rozpocząć pracę z interfejsem wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji i korzystać z niego.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5037450d401153811899b8d769ca92af7ce4068e
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103780"
---
# <a name="use-the-azure-digital-twins-cli"></a>Korzystanie z interfejsu wiersza polecenia usługi Azure Digital Twins

Poza zarządzaniem wystąpieniem usługi Azure Digital bliźniaczych reprezentacji w Azure Portal, usługa Azure Digital bliźniaczych reprezentacji ma **ustawione polecenie dla [interfejsu wiersza polecenia platformy Azure](/cli/azure/what-is-azure-cli)** , za pomocą którego można wykonywać większość najważniejszych działań związanych z usługą, w tym:
* Zarządzanie wystąpieniem usługi Azure Digital bliźniaczych reprezentacji
* Zarządzanie modelami
* Zarządzanie cyfrowym bliźniaczych reprezentacji
* Zarządzanie relacjami bliźniaczymi
* Konfigurowanie punktów końcowych
* Zarządzanie [trasami](concepts-route-events.md)
* Konfigurowanie [zabezpieczeń](concepts-security.md) za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC)

Zestaw poleceń jest wywoływany **AZ DT** i jest częścią [rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension). Pełną listę poleceń i ich użycia można wyświetlić jako część dokumentacji referencyjnej dotyczącej `az iot` zestawu poleceń: [ *AZ DT* Command Reference](/cli/azure/dt).

## <a name="uses-deploy-and-validate"></a>Używa (Wdróż i Weryfikuj)

Oprócz ogólnego zarządzania wystąpieniem, interfejs wiersza polecenia jest również użytecznym narzędziem do wdrażania i walidacji.
* Za pomocą poleceń płaszczyzny kontroli można wykonać wdrożenie nowego wystąpienia powtarzalnego lub zautomatyzowanego.
* Za pomocą poleceń płaszczyzny danych można szybko sprawdzać wartości w wystąpieniu i sprawdzać, czy operacje zostały wykonane zgodnie z oczekiwaniami.

## <a name="get-the-command-set"></a>Pobierz zestaw poleceń

Polecenia usługi Azure Digital bliźniaczych reprezentacji są częścią [rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure (Azure-IoT)](https://github.com/Azure/azure-iot-cli-extension), dlatego wykonaj następujące kroki, aby upewnić się, że masz najnowsze `azure-iot` rozszerzenie z poleceniami **AZ DT** .

### <a name="cli-version-requirements"></a>Wymagania dotyczące wersji interfejsu wiersza polecenia

Jeśli używasz interfejsu wiersza polecenia platformy Azure z programem PowerShell, pakiet rozszerzenia wymaga, aby wersja interfejsu wiersza polecenia platformy **Azure była** nowsza lub wyższa.

Aby sprawdzić wersję interfejsu wiersza polecenia platformy Azure, możesz wykonać następujące polecenie:
```azurecli
az --version
```

Aby uzyskać instrukcje dotyczące instalowania lub aktualizowania interfejsu wiersza polecenia platformy Azure do nowszej wersji, zobacz [*Instalowanie interfejsu wiersza polecenia platformy Azure*](/cli/azure/install-azure-cli).

### <a name="get-the-extension"></a>Pobierz rozszerzenie

Interfejs wiersza polecenia platformy Azure automatycznie wyświetli monit o zainstalowanie rozszerzenia przy pierwszym użyciu poleceń, które go wymagają.

Alternatywnie możesz użyć poniższego polecenia, aby samodzielnie zainstalować rozszerzenie w dowolnym momencie (lub zaktualizować je, jeśli jest już używana Starsza wersja). Polecenie można uruchomić w [Azure Cloud Shell](../cloud-shell/overview.md) lub [lokalnego interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsem wiersza polecenia i jego pełnym zestawem poleceń, korzystając z dokumentacji referencyjnej:
* [polecenie *AZ DT* Command Reference](/cli/azure/dt)