---
title: Problem z logowaniem do aplikacji lokalnej przy użyciu serwera proxy aplikacji usługi Azure AD | Microsoft Docs
description: Rozwiązywanie typowych problemów związanych z zalogowaniem się do aplikacji lokalnej zintegrowanej z usługą Azure AD przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a92ce03b4a8ad241a21b29bbff4e7367d656fab
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259998"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Problemy z logowaniem do aplikacji lokalnej przy użyciu serwera proxy aplikacji usługi Azure AD

Jeśli masz problemy z zalogowaniem się do aplikacji lokalnej, możesz spróbować wykonać poniższe kroki, aby rozwiązać problem.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>Mogę załadować moją aplikację, lecz niektóre elementy strony wyglądają na uszkodzone

Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.

  * [Mogę uzyskać dostęp do swojej aplikacji, ale strona aplikacji nie jest wyświetlana poprawnie](application-proxy-page-appearance-broken-problem.md)
  * [Mogę uzyskać dostęp do swojej aplikacji, ale jej załadowanie trwa zbyt długo](application-proxy-page-load-speed-problem.md)
  * [Mogę uzyskać dostęp do swojej aplikacji, ale linki na stronie aplikacji nie działają](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>Mam problem z połączeniem w mojej aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Nie wiem, jakie porty otworzyć dla swojej aplikacji](application-proxy-add-on-premises-application.md)
  * [Wystąpił problem spowodowany brakiem działającego łącznika w grupie łączników dla mojej aplikacji](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Mam problem ze skonfigurowaniem serwera proxy aplikacji usługi Azure AD w portalu administracyjnym
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Mam problem z konfiguracją aplikacji serwera proxy aplikacji](application-proxy-config-how-to.md)
  * [Nie wiem, jak skonfigurować logowanie jednokrotne do aplikacji serwera proxy aplikacji](application-proxy-config-sso-how-to.md)
  * [Wystąpił problem podczas tworzenia aplikacji w portalu administracyjnym](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Mam problem ze skonfigurowaniem uwierzytelniania zaplecza dla mojej aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Nie wiem, jak skonfigurować ograniczone delegowanie protokołu Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [Nie wiem, jak skonfigurować współdziałanie swojej aplikacji z usługą PingAccess](./application-proxy-ping-access-publishing-guide.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Mam problem podczas logowania do mojej aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Występuje błąd „Can't Access this Corporate Application” (Nie można uzyskać dostępu do tej aplikacji firmowej)](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Mam problem z łącznikiem agenta serwera proxy aplikacji
  Następujące dokumenty mogą ułatwić rozwiązanie niektórych często spotykanych problemów tego typu.
  * [Mam problemy z instalowaniem łącznika agenta serwera proxy aplikacji](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>Następne kroki
[Jak zapewnić bezpieczny, zdalny dostęp do aplikacji lokalnych](application-proxy.md)