---
title: 'Tożsamość hybrydowa: porównanie narzędzi do integracji katalogów | Microsoft Docs'
description: Na tej stronie udostępniona jest kompleksowa tabela zawierająca porównanie różnych narzędzi integracji katalogów, których można użyć do integracji katalogów.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9c8f5a75fea6ddd85026407fe4784c8a4589e33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89278313"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Tożsamość hybrydowa: porównanie narzędzi do integracji katalogów
Narzędzia do integracji katalogów były rozbudowywane i rozwijane przez wiele lat.  


- Usługi [FIM](/previous-versions/windows/desktop/forefront-2010/ff182370(v=vs.100)) i [MIM](/microsoft-identity-manager/microsoft-identity-manager-2016) są nadal obsługiwane i przede wszystkim umożliwiają synchronizację między systemami lokalnymi.   [Łącznik usługi Windows Azure AD programu FIM](/previous-versions/mim/dn511001(v=ws.10)) jest obsługiwany zarówno w programie FIM, jak i w programie MIM, ale nie jest to zalecane w przypadku nowych wdrożeń — klienci ze źródłami lokalnymi, takimi jak uwagi i SAP HCM, powinni użyć programu MIM do wypełniania Active Directory Domain Services (AD DS), a następnie do synchronizacji z AD DS do usługi Azure AD także użyć Azure AD Connect synchronizacji lub Azure AD Connect aprowizacji w chmurze.
- [Azure AD Connect Sync](how-to-connect-sync-whatis.md) obejmuje składniki i funkcje, które zostały wcześniej wydane w narzędziu DirSync i Azure AD Sync, do synchronizowania między AD DS lasami a usługą Azure AD.  
- [Azure AD Connect aprowizacji w chmurze](../cloud-provisioning/what-is-cloud-provisioning.md) to nowy Agent firmy Microsoft służący do synchronizowania danych z AD DS do usługi Azure AD, przydatny w scenariuszach takich jak fuzja i pozyskiwanie, w przypadku których odizolowane lasy usługi AD uzyskanych w firmie

Aby dowiedzieć się więcej o różnicach między Azure AD Connect synchronizacji i Azure AD Connect aprowizacji w chmurze, zobacz artykuł [co to jest Azure AD Connect aprowizacji w chmurze?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).