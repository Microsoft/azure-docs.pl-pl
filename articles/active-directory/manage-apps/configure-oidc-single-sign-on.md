---
title: Informacje na temat OIDC logowania jednokrotnego (SSO) dla aplikacji w Azure Active Directory
description: Informacje na temat OIDC logowania jednokrotnego (SSO) dla aplikacji w Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: ffaa55d4aa482e8f0eda93b1b32db6310d17e2a3
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255253"
---
# <a name="understand-oidc-based-single-sign-on"></a>Informacje na temat logowania jednokrotnego opartego na OIDC
W [serii szybkiego startu](view-applications-portal.md) w zarządzaniu aplikacjami wiesz, jak używać usługi Azure AD jako dostawcy tożsamości (dostawcy tożsamości) dla aplikacji. W tym artykule opisano aplikacje, które używają standardu OpenID Connect Connect do implementowania logowania jednokrotnego. 

## <a name="before-you-begin"></a>Zanim rozpoczniesz
Proces dodawania aplikacji do dzierżawy Azure Active Directory zależy od typu logowania jednokrotnego wdrożonej aplikacji. Aby dowiedzieć się więcej na temat opcji logowania jednokrotnego dostępnych dla aplikacji, które mogą używać usługi Azure AD do zarządzania tożsamościami, zobacz [Opcje logowania](sso-options.md)jednokrotnego. W tym artykule opisano aplikacje oparte na OIDC.


## <a name="basic-oidc-configuration"></a>Podstawowa konfiguracja OIDC
W [serii szybkiego startu](add-application-portal-setup-oidc-sso.md)znajduje się artykuł dotyczący konfigurowania logowania jednokrotnego. W tym temacie dowiesz się, jak dodać aplikację opartą na OIDC do dzierżawy platformy Azure.

W celu dodania aplikacji, która używa standardu OIDC do logowania jednokrotnego, konfiguracja jest minimalna. Oto krótkie wideo pokazujące, jak dodać aplikację opartą na OIDC do dzierżawy.

Dodawanie aplikacji opartej na OIDC w Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Aby dowiedzieć się więcej o zgodzie użytkowników i administratorów, zobacz [Opis zgody użytkownika i administratora](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Następne kroki

- [Seria szybkiego startu w zarządzaniu aplikacjami](add-application-portal-setup-oidc-sso.md)
- [Opcje logowania jednokrotnego](sso-options.md)
- [Instrukcje: Logowanie się dowolnego użytkownika usługi Azure Active Directory za pomocą wzorca aplikacji wielodostępnych](../develop/howto-convert-app-to-be-multi-tenant.md)
