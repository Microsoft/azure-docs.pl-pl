---
title: 'Azure AD Connect: funkcje w wersji zapoznawczej | Microsoft Docs'
description: W tym temacie opisano bardziej szczegółowe funkcje, które są w wersji zapoznawczej w Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bb7305fe8ee96697c5c8b2172e32a2a97e5bd6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85358433"
---
# <a name="more-details-about-features-in-preview"></a>Więcej szczegółów na temat funkcji w wersji zapoznawczej
W tym temacie opisano, jak używać funkcji obecnie dostępnych w wersji zapoznawczej.

## <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Interfejs API usługi Azure AD Connect Sync w wersji 2 (publiczna wersja zapoznawcza) 

Wdrożono nowy punkt końcowy (API) dla Azure AD Connect, który zwiększa wydajność operacji usługi synchronizacji do Azure Active Directory. Korzystając z nowego punktu końcowego w wersji 2, zobaczysz zauważalny wzrost wydajności dotyczący eksportowania i importowania do usługi Azure AD. Ten nowy punkt końcowy obsługuje również synchronizowanie grup z do członków 250 000. Za pomocą tego punktu końcowego można także pisać ujednolicone grupy usługi O365, bez maksymalnego limitu członkostwa, do Active Directory lokalnego, gdy włączono funkcję zapisywania zwrotnego grup.   Aby uzyskać więcej informacji, zobacz [Azure AD Connect Sync v2 Endpoint API (publiczna wersja zapoznawcza)](how-to-connect-sync-endpoint-api-v2.md).

## <a name="user-writeback"></a>Zapisywanie zwrotne użytkownika
> [!IMPORTANT]
> Funkcja wersji zapoznawczej zapisywania zwrotnego użytkownika została usunięta z aktualizacji 2015 sierpnia do Azure AD Connect. Jeśli ją włączono, należy wyłączyć tę funkcję.
>
>

## <a name="next-steps"></a>Następne kroki
Kontynuuj [instalację Niestandardową Azure AD Connect](how-to-connect-install-custom.md).

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
