---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2b28cc742bb981e0db535d39cb0c7fbf027470ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83673768"
---
### <a name="identity-tier"></a>Warstwa tożsamości 

Partnerstwo Microsoft-Oracle umożliwia skonfigurowanie ujednoliconej tożsamości na platformie Azure, OCI i aplikacji Oracle. W przypadku pakietu aplikacji JD Edwards EnterpriseOne lub PeopleSoft do konfigurowania logowania jednokrotnego między usługą Azure AD i Oracle OHS jest wymagany wystąpienie serwera Oracle HTTP (IDCS).

OHS działa jako zwrotny serwer proxy do warstwy aplikacji, co oznacza, że wszystkie żądania do aplikacji końcowych przechodzą przez nią. Webbramą programu Oracle Access Manager jest wtyczka serwera sieci Web OHS, która przechwytuje każde żądanie przechodzenia do aplikacji końcowej. Jeśli dostęp do zasobu jest chroniony (wymaga uwierzytelnionej sesji), usługa webbram inicjuje przepływ uwierzytelniania OIDC przy użyciu usługi Identity w chmurze za pomocą przeglądarki użytkownika. Więcej informacji o przepływach obsługiwanych przez program webgate OpenID Connect Connect można znaleźć w [dokumentacji programu Oracle Access Manager](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327).

W przypadku tej konfiguracji użytkownik zalogowany do usługi Azure AD może przejść do aplikacji JD Edwards EnterpriseOne lub PeopleSoft bez ponownego zalogowania się za pomocą usługi tożsamości Oracle w chmurze. Klienci, którzy wdrażają to rozwiązanie, uzyskują zalety logowania jednokrotnego, w tym jeden zestaw poświadczeń, ulepszone środowisko logowania, ulepszone zabezpieczenia i krótszy koszt pomocy technicznej.

Aby dowiedzieć się więcej o konfigurowaniu rejestracji Jednokrotnej dla JD Edwards EnterpriseOne lub PeopleSoft za pomocą usługi Azure AD, zapoznaj się z [dokumentem](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)związanym z oprogramowaniem Oracle.