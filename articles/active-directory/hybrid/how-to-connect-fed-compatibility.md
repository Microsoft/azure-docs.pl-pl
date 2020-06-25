---
title: Lista zgodności usługi Azure AD z usługami federacyjnymi
description: Ta strona zawiera dostawców tożsamości innych firm, których można używać do implementowania logowania jednokrotnego.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3ca659535c225ca6736fe7bba8aa5b0120c684c
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85359827"
---
# <a name="azure-ad-federation-compatibility-list"></a>Lista zgodności usługi Azure AD z usługami federacyjnymi
Azure Active Directory zapewnia dostęp do funkcji logowania jednokrotnego i rozszerzonego dostępu do aplikacji dla pakietu Office 365 i innych usług online firmy Microsoft dla implementacji hybrydowych i opartych tylko na chmurze, bez konieczności stosowania jakichkolwiek rozwiązań innych firm. Pakiet Office 365, taki jak większość usług online firmy Microsoft, jest zintegrowany z usługą Azure Active Directory dla usług katalogowych, uwierzytelniania i autoryzacji. Azure Active Directory zapewnia także Logowanie jednokrotne do tysięcy aplikacji SaaS i lokalnych aplikacji sieci Web. Zapoznaj się z [galerią Azure Active Directory aplikacji](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) dla obsługiwanych aplikacji SaaS. 

## <a name="idp-validation"></a>DOSTAWCY tożsamości sprawdzanie poprawności
Jeśli Twoja organizacja korzysta z rozwiązania federacyjnego innej firmy, możesz skonfigurować Logowanie jednokrotne dla użytkowników lokalnych Active Directory przy użyciu usług online firmy Microsoft, takich jak pakiet Office 365, pod warunkiem, że rozwiązanie federacyjne innych firm jest zgodne z Azure Active Directory.  Aby uzyskać odpowiedzi na pytania dotyczące zgodności, skontaktuj się z dostawcą tożsamości.  Jeśli chcesz zobaczyć listę dostawców tożsamości, którzy zostali wcześniej przetestowani pod kątem zgodności z usługą Azure AD przez firmę Microsoft, kliknij [tutaj](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Firma Microsoft nie zapewnia już testów weryfikacyjnych niezależnym dostawcom tożsamości w celu zapewnienia zgodności z Azure Active Directory. Jeśli chcesz przetestować produkt pod kątem współdziałania, zapoznaj się z tymi [wskazówkami](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Następne kroki

- [Integrowanie katalogów lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect i Federacja](how-to-connect-fed-whatis.md)
