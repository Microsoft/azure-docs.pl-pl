---
title: Włącz kompleksowe Transport Layer Security
titleSuffix: Azure Spring Cloud
description: Jak zapewnić kompleksowe Transport Layer Security aplikacji.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 647cf6f0b1af6a5858bbf1147cc03ecc4637ed25
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227810"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>Włączanie kompleksowego protokołu TLS dla aplikacji

W tym temacie przedstawiono sposób włączania kompleksowego protokołu SSL/TLS w celu zabezpieczenia ruchu z kontrolera transferu danych przychodzących do aplikacji, które obsługują protokół HTTPS. Po włączeniu kompleksowego protokołu TLS i załadowaniu certyfikatu z magazynu kluczy cała komunikacja w ramach chmury wiosennej platformy Azure jest zabezpieczona przy użyciu protokołu TLS.

   ![Wykres komunikacji zabezpieczony przy użyciu protokołu TLS.](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>Wymagania wstępne 

- Wdrożone wystąpienie chmury Azure wiosennej. Aby rozpocząć, Skorzystaj z naszego [przewodnika Szybki Start dotyczącego wdrażania za pośrednictwem interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) .
- Jeśli nie znasz kompleksowego protokołu TLS, zobacz [kompleksowy przykład protokołu TLS](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl).
- Aby bezpiecznie załadować wymagane certyfikaty do aplikacji do rozruchu sprężynowego, można użyć [rozruchu z magazynu kluczy Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates).


## <a name="enable-end-to-end-tls-on-an-existing-app"></a>Włączanie kompleksowego protokołu TLS dla istniejącej aplikacji

Użyj polecenia, `az spring-cloud app update --enable-end-to-end-tls` Aby włączyć lub wyłączyć kompleksową obsługę protokołu TLS dla aplikacji.

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>Włącz kompleksową transszyfrowanie TLS w przypadku powiązania domeny niestandardowej

Użyj polecenia `az spring-cloud app custom-domain update --enable-end-to-end-tls` lub, `az spring-cloud app custom-domain bind --enable-end-to-end-tls` Aby włączyć lub wyłączyć kompleksową obsługę protokołu TLS dla aplikacji.

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="verify-end-to-end-tls-status"></a>Weryfikowanie stanu kompleksowego protokołu TLS

Użyj polecenia, `az spring-cloud app show` Aby sprawdzić wartość `enableEndToEndTls` .
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Następne kroki
* [Serwer konfiguracji dostępu i rejestr usługi](how-to-access-data-plane-azure-ad-rbac.md)
