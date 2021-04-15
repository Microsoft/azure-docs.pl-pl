---
title: Understand OIDC-based single sign-on (SSO) for apps in Azure Active Directory
description: Informacje na temat logowania jednokrotnego (SSO) opartego na OIDC dla aplikacji w Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: iangithinji
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: 990e0c09f8a49b83bc68d7123f5f8146a3551575
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374543"
---
# <a name="understand-oidc-based-single-sign-on"></a>Understand OIDC-based single sign-on (Informacje na temat logowania pojedynczego opartego na OIDC)
W serii [przewodników Szybki](view-applications-portal.md) start dotyczącej zarządzania aplikacją opisano, jak używać usługi Azure AD jako dostawcy tożsamości dla aplikacji. W tym artykule bardziej szczegółowo opisano aplikacje, które używają OpenID Connect standardu w celu zaimplementowania logowania pojedynczego. 

## <a name="before-you-begin"></a>Zanim rozpoczniesz
Proces dodawania aplikacji do dzierżawy usługi Azure Active Directory zależy od typu logowania pojedynczego zaimplementowanej aplikacji. Aby dowiedzieć się więcej o opcjach logowania pojedynczego dostępnych dla aplikacji, które mogą używać usługi Azure AD do zarządzania tożsamościami, zobacz opcje [logowania pojedynczego.](sso-options.md) W tym artykule o mowa w aplikacjach opartych na OIDC.


## <a name="basic-oidc-configuration"></a>Podstawowa konfiguracja OIDC
W serii [przewodników Szybki](add-application-portal-setup-oidc-sso.md)start znajduje się artykuł na temat konfigurowania logowania pojedynczego. Z tego tematu dowiesz się, jak dodać aplikację opartą na OIDC do dzierżawy platformy Azure.

W przypadku dodawania aplikacji, która używa standardu OIDC do logowania pojedynczego, konfiguracja jest minimalna. Oto krótki film wideo pokazujący, jak dodać aplikację opartą na OIDC do dzierżawy.

Dodawanie aplikacji opartej na OIDC w Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Aby dowiedzieć się więcej na temat zgody użytkownika i administratora, zobacz Understand user and admin consent (Opis [zgody użytkownika i administratora).](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)

## <a name="next-steps"></a>Następne kroki

- [Seria przewodników Szybki start na temat zarządzania aplikacją](add-application-portal-setup-oidc-sso.md)
- [Opcje logowania jednokrotnego](sso-options.md)
- [Instrukcje: Logowanie się dowolnego użytkownika usługi Azure Active Directory za pomocą wzorca aplikacji wielodostępnych](../develop/howto-convert-app-to-be-multi-tenant.md)
