---
title: Jak korzystać z magazynu trwałego w chmurze Azure wiosennej | Microsoft Docs
description: Jak korzystać z magazynu trwałego w chmurze Azure wiosennej
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9d769cb6e2cc33df259da46a82ad27a8bfd9e74b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888521"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Używanie magazynu trwałego w usłudze Azure Spring Cloud

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Chmura sprężynowa platformy Azure oferuje dwa typy magazynów dla aplikacji: trwałe i tymczasowe.

Domyślnie chmura sprężynowa platformy Azure zapewnia tymczasowy magazyn dla każdego wystąpienia aplikacji. Magazyn tymczasowy jest ograniczony do 5 GB na wystąpienie z domyślną ścieżką instalacji/tmp.

> [!WARNING]
> Po ponownym uruchomieniu wystąpienia aplikacji skojarzony magazyn tymczasowy zostanie trwale usunięty.

Magazyn trwały jest kontenerem udziałów plików zarządzanym przez platformę Azure i przydzielonym na aplikację. Dane przechowywane w magazynie trwałym są współużytkowane przez wszystkie wystąpienia aplikacji. Wystąpienie chmury sieci platformy Azure może mieć maksymalnie 10 aplikacji z włączonym magazynem trwałym. Każda aplikacja ma przydzieloną 50 GB trwałego magazynu. Domyślną ścieżką instalacji dla trwałego magazynu jest/persistent.

> [!WARNING]
> Jeśli wyłączysz trwały magazyn aplikacji, cały magazyn zostanie cofnięty, a wszystkie przechowywane dane zostaną utracone.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Użyj Azure Portal, aby włączyć magazyn trwały

1. Na stronie **głównej** Azure Portal wybierz pozycję **wszystkie zasoby**.

    >![Znajdź ikonę wszystkie zasoby](media/portal-all-resources.jpg)

1. Wybierz zasób chmury Azure wiosny, który potrzebuje trwałego magazynu. W tym przykładzie wybrana aplikacja **jest nazywana**.

    > ![Wybierz aplikację](media/select-service.jpg)

1. W polu Nagłówek **ustawień** wybierz pozycję **aplikacje**.

1. Usługi w chmurze Azure wiosny są wyświetlane w tabeli.  Wybierz usługę, do której chcesz dodać magazyn trwały. W tym przykładzie wybrano usługę **bramy** .

    > ![Wybór usługi](media/select-gateway.jpg)

1. Na stronie Konfiguracja usługi wybierz pozycję **Konfiguracja** .

1. Wybierz kartę **Magazyn trwały** i wybierz pozycję **Włącz**.

    > ![Włącz magazyn trwały](media/enable-persistent-storage.jpg)

Po włączeniu magazynu trwałego jego rozmiar i ścieżka są wyświetlane na stronie Konfiguracja.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Modyfikowanie trwałego magazynu za pomocą interfejsu wiersza polecenia platformy Azure

W razie potrzeby zainstaluj rozszerzenie chmury wiosennej dla interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name spring-cloud
```
Inne operacje:

* Aby utworzyć aplikację z włączonym magazynem trwałym:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Aby włączyć magazyn trwały dla istniejącej aplikacji:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Aby wyłączyć magazyn trwały w istniejącej aplikacji:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Jeśli wyłączysz trwały magazyn aplikacji, cały magazyn zostanie cofnięty, a wszystkie przechowywane dane zostaną trwale utracone.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [przydziałów aplikacji i usług](spring-cloud-quotas.md).
* Dowiedz się, jak [ręcznie skalować aplikację](spring-cloud-tutorial-scale-manual.md).
