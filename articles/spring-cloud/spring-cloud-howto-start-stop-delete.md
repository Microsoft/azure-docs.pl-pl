---
title: Uruchamianie, zatrzymywanie i usuwanie aplikacji w chmurze Azure wiosny | Microsoft Docs
description: Jak uruchamiać, zatrzymywać i usuwać aplikację w chmurze platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 479daa9a94247ada30c54a9c5df3471035765087
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908282"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Uruchamianie, zatrzymywanie i usuwanie aplikacji w chmurze platformy Azure

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

W tym przewodniku wyjaśniono, jak zmienić stan aplikacji w chmurze Azure wiosennej za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Po wdrożeniu aplikacji można uruchomić, zatrzymać i usunąć ją za pomocą Azure Portal.

1. Przejdź do wystąpienia usługi w chmurze ze sprężyną platformy Azure w Azure Portal.
1. Wybierz kartę **pulpit nawigacyjny aplikacji** .
1. Wybierz aplikację, której stan chcesz zmienić.
1. Na stronie **Przegląd** dla tej aplikacji wybierz pozycję **Uruchom/Zatrzymaj**, **Uruchom ponownie**lub **Usuń**.

## <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

> [!NOTE]
> Możesz użyć parametrów opcjonalnych i skonfigurować wartości domyślne przy użyciu interfejsu wiersza polecenia platformy Azure. Dowiedz się więcej o interfejsie wiersza polecenia platformy Azure, odczytując [dokumentację referencyjną](/cli/azure/ext/spring-cloud/spring-cloud?view=azure-cli-latest&preserve-view=true).  

Najpierw zainstaluj rozszerzenie Cloud wiosny Azure dla interfejsu wiersza polecenia platformy Azure w następujący sposób:

```azurecli
az extension add --name spring-cloud
```

Następnie wybierz dowolną z następujących operacji interfejsu wiersza polecenia platformy Azure:

* Aby uruchomić aplikację:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby zatrzymać aplikację:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby ponownie uruchomić aplikację:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Aby usunąć aplikację:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
