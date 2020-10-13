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
ms.openlocfilehash: b543cf4655d0b961a144e9180385a532ae4216d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657095"
---
# <a name="more-details-about-features-in-preview"></a>Więcej szczegółów na temat funkcji w wersji zapoznawczej
W tym temacie opisano, jak używać funkcji obecnie dostępnych w wersji zapoznawczej.

## <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Interfejs API usługi Azure AD Connect Sync w wersji 2 (publiczna wersja zapoznawcza) 

Wdrożono nowy punkt końcowy (API) dla Azure AD Connect, który zwiększa wydajność operacji usługi synchronizacji do Azure Active Directory. Korzystając z nowego punktu końcowego w wersji 2, zobaczysz zauważalny wzrost wydajności dotyczący eksportowania i importowania do usługi Azure AD. Ten nowy punkt końcowy obsługuje również synchronizowanie grup z do członków 250 000. Korzystając z tego punktu końcowego, można również pisać Microsoft 365 ujednoliconych grup bez maksymalnego limitu członkostwa do Active Directory lokalnych, gdy włączono funkcję zapisywania zwrotnego grup. Aby uzyskać więcej informacji, zobacz [Azure AD Connect Sync v2 Endpoint API (publiczna wersja zapoznawcza)](how-to-connect-sync-endpoint-api-v2.md).

## <a name="user-writeback"></a>Zapisywanie zwrotne użytkownika
> [!IMPORTANT]
> Funkcja wersji zapoznawczej zapisywania zwrotnego użytkownika została usunięta z aktualizacji 2015 sierpnia do Azure AD Connect. Jeśli ją włączono, należy wyłączyć tę funkcję.
>
>

## <a name="next-steps"></a>Następne kroki
Kontynuuj [instalację Niestandardową Azure AD Connect](how-to-connect-install-custom.md).

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
