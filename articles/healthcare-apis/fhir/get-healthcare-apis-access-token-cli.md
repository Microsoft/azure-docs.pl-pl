---
title: Uzyskiwanie tokenu dostępu przy użyciu interfejsu wiersza polecenia platformy Azure — Azure API for FHIR
description: W tym artykule wyjaśniono, jak uzyskać token dostępu dla interfejsu API platformy Azure dla usługi FHIR przy użyciu interfejsu wiersza polecenia platformy Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: b7bdb7f4a22d080f382fea984e710980428067ff
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020303"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Uzyskaj token dostępu dla interfejsu API platformy Azure dla usługi FHIR przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule dowiesz się, jak uzyskać token dostępu dla interfejsu API platformy Azure dla usługi FHIR przy użyciu interfejsu wiersza polecenia platformy Azure. Po [udostępnieniu interfejsu API platformy Azure dla FHIR](fhir-paas-portal-quickstart.md)należy skonfigurować zbiór użytkowników lub jednostki usługi, które mają dostęp do usługi. Jeśli identyfikator obiektu użytkownika znajduje się na liście dozwolonych identyfikatorów obiektów, można uzyskać dostęp do usługi przy użyciu tokenu uzyskanego przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>Uzyskaj token

Interfejs API platformy Azure dla usługi FHIR `resource`  używa `Audience` identyfikatora URI lub z identyfikatorem URI serwera FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . Możesz uzyskać token i zapisać go w zmiennej (o nazwie `$token` ) przy użyciu następującego polecenia:

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Używanie z interfejsem API platformy Azure dla usługi FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak uzyskać token dostępu dla interfejsu API platformy Azure dla usługi FHIR przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak uzyskać dostęp do interfejsu API FHIR przy użyciu programu Poster, zapoznaj się z samouczkiem dotyczącym programu.

>[!div class="nextstepaction"]
>[Dostęp do interfejsu API FHIR za pomocą programu Poster](access-fhir-postman-tutorial.md)
