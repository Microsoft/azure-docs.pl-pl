---
title: Rejestrowanie aplikacji zasobów w usłudze Azure AD — Azure API for FHIR
description: Zarejestruj aplikację zasobów (lub interfejs API) w Azure Active Directory, aby aplikacje klienckie mogły żądać dostępu do zasobu podczas uwierzytelniania.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020093"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Rejestrowanie aplikacji zasobów w Azure Active Directory

W tym artykule dowiesz się, jak zarejestrować aplikację zasobów (lub interfejsu API) w Azure Active Directory. Aplikacja zasobów to Azure Active Directory reprezentacja samego interfejsu API serwera FHIR, a aplikacje klienckie mogą zażądać dostępu do zasobu podczas uwierzytelniania. Aplikacja do zasobów jest również znana jako *odbiorcy* w sprzężeniem uwierzytelniania OAuth.

## <a name="azure-api-for-fhir"></a>Interfejs API platformy Azure dla standardu FHIR

Jeśli używasz interfejsu API platformy Azure dla usługi FHIR, aplikacja zasobów jest tworzona automatycznie podczas wdrażania usługi. O ile korzystasz z interfejsu API platformy Azure dla FHIR w tej samej dzierżawie Azure Active Directory podczas wdrażania aplikacji, możesz pominąć ten przewodnik, a zamiast tego wdrożyć interfejs API platformy Azure dla FHIR, aby rozpocząć pracę.

Jeśli używasz innej dzierżawy Azure Active Directory (nieskojarzonej z Twoją subskrypcją), możesz zaimportować aplikację Azure API for FHIR Resource do swojej dzierżawy za pomocą programu PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

Możesz też użyć interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Serwer FHIR dla platformy Azure

Jeśli używasz serwera FHIR typu open source dla platformy Azure, postępuj zgodnie z instrukcjami w [repozytorium GitHub](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) , aby zarejestrować aplikację zasobów. 

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób rejestrowania aplikacji zasobów w Azure Active Directory. Następnie zarejestruj poufną aplikację kliencką.
 
>[!div class="nextstepaction"]
>[Zarejestruj poufną aplikację kliencką](register-confidential-azure-ad-client-app.md)