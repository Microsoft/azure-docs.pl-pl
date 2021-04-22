---
title: Uruchamianie, zatrzymywanie i usuwanie Azure Spring Cloud aplikacji | Microsoft Docs
description: Jak uruchomić, zatrzymać i usunąć aplikację Azure Spring Cloud aplikacji
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 914325aba3d123fb1b700f0eff8ddb17119c5d12
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863213"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Uruchamianie, zatrzymywanie i usuwanie Azure Spring Cloud aplikacji

**Ten artykuł dotyczy: ✔️** Java ✔️ C #

W tym przewodniku wyjaśniono, jak zmienić stan aplikacji w usłudze Azure Spring Cloud użyciu interfejsu wiersza Azure Portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Po wdrożeniu aplikacji można ją uruchomić, zatrzymać i usunąć przy użyciu Azure Portal.

1. Przejdź do swojego Azure Spring Cloud usługi w Azure Portal.
1. Wybierz **kartę Pulpit nawigacyjny** aplikacji.
1. Wybierz aplikację, której stan chcesz zmienić.
1. Na stronie **Przegląd** dla tej aplikacji wybierz pozycję **Uruchom/Zatrzymaj,** **Uruchom ponownie** lub **Usuń.**

## <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

> [!NOTE]
> Za pomocą interfejsu wiersza polecenia platformy Azure można używać parametrów opcjonalnych i konfigurować wartości domyślne. Dowiedz się więcej o interfejsie wiersza polecenia platformy Azure, [czytając naszą dokumentację referencyjną](/cli/azure/spring-cloud).  

Najpierw zainstaluj rozszerzenie Azure Spring Cloud interfejsu wiersza polecenia platformy Azure w następujący sposób:

```azurecli
az extension add --name spring-cloud
```

Następnie wybierz dowolną z tych operacji interfejsu wiersza polecenia platformy Azure:

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
