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
ms.openlocfilehash: 895e7347feea3190ba9bdc273200d2985dfaa0d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89661828"
---
# <a name="azure-ad-federation-compatibility-list"></a>Lista zgodności usługi Azure AD z usługami federacyjnymi
Azure Active Directory zapewnia ochronę dostępu jednokrotnego do aplikacji Microsoft 365 i innych usług online firmy Microsoft dla implementacji hybrydowej i opartej na chmurze bez konieczności stosowania rozwiązań innych firm. Microsoft 365, jak większość usług online firmy Microsoft, jest zintegrowany z Azure Active Directory dla usług katalogowych, uwierzytelniania i autoryzacji. Azure Active Directory zapewnia także Logowanie jednokrotne do tysięcy aplikacji SaaS i lokalnych aplikacji sieci Web. Zapoznaj się z [galerią Azure Active Directory aplikacji](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) dla obsługiwanych aplikacji SaaS. 

## <a name="idp-validation"></a>DOSTAWCY tożsamości sprawdzanie poprawności
Jeśli Twoja organizacja korzysta z rozwiązania federacyjnego innej firmy, możesz skonfigurować Logowanie jednokrotne dla użytkowników lokalnych Active Directory przy użyciu usług online firmy Microsoft, takich jak Microsoft 365, pod warunkiem, że rozwiązanie federacyjne innych firm jest zgodne z Azure Active Directory.  Aby uzyskać odpowiedzi na pytania dotyczące zgodności, skontaktuj się z dostawcą tożsamości.  Jeśli chcesz zobaczyć listę dostawców tożsamości, którzy zostali wcześniej przetestowani pod kątem zgodności z usługą Azure AD przez firmę Microsoft, zobacz [dokumenty zgodności dostawcy tożsamości usługi Azure AD](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Firma Microsoft nie zapewnia już testów weryfikacyjnych niezależnym dostawcom tożsamości w celu zapewnienia zgodności z Azure Active Directory. Jeśli chcesz przetestować produkt pod kątem współdziałania, zapoznaj się z tymi [wskazówkami](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Następne kroki

- [Integrowanie katalogów lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect i Federacja](how-to-connect-fed-whatis.md)
