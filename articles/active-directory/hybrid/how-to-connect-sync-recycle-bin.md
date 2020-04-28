---
title: 'Azure AD Connect Sync: Włącz Kosz usługi AD | Microsoft Docs'
description: W tym temacie zaleca się używanie funkcji Kosz usługi AD z Azure AD Connect.
services: active-directory
keywords: Kosz usługi AD, przypadkowe usunięcie, zakotwiczenie źródła
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe7d3ea7d4f6d648438efc1a484d5909ade2f23
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "60382899"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect Sync: Włącz Kosz usługi AD
Zaleca się włączenie funkcji Kosz usługi AD dla lokalnych katalogów aktywnych, które są synchronizowane z usługą Azure AD. 

Jeśli przypadkowo usunięto lokalny obiekt użytkownika usługi AD i przywrócono go przy użyciu funkcji, usługa Azure AD przywraca odpowiedni obiekt użytkownika usługi Azure AD.  Informacje o funkcji Kosz usługi AD znajdują się w artykule scenariusz artykułu [Omówienie przywracania usuniętych obiektów Active Directory](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Zalety włączania Kosza usługi AD
Ta funkcja pomaga w przywracaniu obiektów użytkowników usługi Azure AD, wykonując następujące czynności:

* Jeśli przypadkowo usunięto lokalny obiekt użytkownika usługi AD, odpowiedni obiekt użytkownika usługi Azure AD zostanie usunięty w następnym cyklu synchronizacji. Domyślnie usługa Azure AD utrzymuje usunięty obiekt użytkownika usługi Azure AD w stanie nieusuniętym przez 30 dni.

* Jeśli włączona jest funkcja lokalnego kosza usługi AD, można przywrócić usunięty lokalny obiekt użytkownika usługi AD bez zmiany jego źródłowej wartości zakotwiczenia. Po zsynchronizowaniu obiektu lokalnego użytkownika usługi AD z usługą Azure AD usługa Azure AD przywróci odpowiednie nietrwałe obiekty użytkownika usługi Azure AD. Aby uzyskać informacje o atrybucie zakotwiczenia źródła, zapoznaj się z artykułem [Azure AD Connect: koncepcje projektowania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Jeśli nie masz włączonej funkcji lokalnego kosza usługi AD, może być konieczne utworzenie obiektu użytkownika usługi AD w celu zamienienia usuniętego obiektu. Jeśli usługa synchronizacji Azure AD Connect jest skonfigurowana do używania wygenerowanego przez system atrybutu usługi AD (takiego jak ObjectGuid) dla atrybutu zakotwiczenia źródła, nowo utworzony obiekt użytkownika usługi AD nie będzie miał tej samej wartości zakotwiczenia źródła, co usunięty obiekt użytkownika usługi AD. Po zsynchronizowaniu nowo utworzonego obiektu użytkownika usługi AD z usługą Azure AD usługa Azure AD utworzy nowy obiekt użytkownika usługi Azure AD zamiast przywracania nietrwałego obiektu użytkownika usługi Azure AD.

> [!NOTE]
> Domyślnie usługa Azure AD zachowuje usunięte obiekty użytkownika usługi Azure AD w stanie nieusuniętym przez 30 dni przed ich trwałe usunięciem. Jednak administratorzy mogą przyspieszyć usuwanie takich obiektów. Po usunięciu obiektów obiekty nie mogą już być odzyskiwane, nawet jeśli włączona jest funkcja lokalnego kosza usługi AD.

## <a name="next-steps"></a>Następne kroki
**Tematy dotyczące omówienia**

* [Azure AD Connect Sync: omówienie i dostosowanie synchronizacji](how-to-connect-sync-whatis.md)

* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
