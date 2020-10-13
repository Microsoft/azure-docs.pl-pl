---
title: 'Azure AD Connect: Opcje urządzenia | Microsoft Docs'
description: Ten dokument zawiera szczegóły opcji urządzenia dostępnych w Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90d8cc39174f6392b0c532948305fa713535f0c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279231"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: Opcje urządzenia

Poniższa dokumentacja zawiera informacje dotyczące różnych opcji urządzeń dostępnych w Azure AD Connect. Za pomocą Azure AD Connect można skonfigurować następujące dwie operacje: 
* **Sprzężenie hybrydowe usługi Azure AD**: Jeśli środowisko ma lokalną usługę AD i chcesz skorzystać z zalet usługi Azure AD, możesz zaimplementować hybrydowe urządzenia dołączone do usługi Azure AD. Te urządzenia są przyłączone do lokalnego Active Directory i Azure Active Directory.
* **Zapisywanie zwrotne urządzeń**: zapisywanie zwrotne urządzeń służy do włączania dostępu warunkowego opartego na urządzeniach do AD FS (2012 R2 lub nowsze) chronionych urządzeń

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfigurowanie opcji urządzeń w Azure AD Connect

1.  Uruchom Azure AD Connect. Na stronie **dodatkowe zadania** wybierz pozycję **Konfiguruj opcje urządzenia**.  Kliknij przycisk **Dalej**.
    ![Konfiguruj opcje urządzenia](./media/how-to-connect-device-options/deviceoptions.png) 

    Na stronie **Przegląd** są wyświetlane szczegóły.
    ![Omówienie](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > Nowe opcje konfigurowania urządzeń są dostępne tylko w wersji 1.1.819.0 i nowszych.

2.  Po podaniu poświadczeń dla usługi Azure AD możesz wybrać operację do wykonania na stronie Opcje urządzenia.
    ![Operacje na urządzeniach](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie sprzężenia hybrydowego usługi Azure AD](../devices/hybrid-azuread-join-plan.md)
* [Skonfiguruj/Wyłącz zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md)