---
title: Jak platforma tożsamości firmy Microsoft używa protokołu SAML
description: Ten artykuł zawiera omówienie jednego Sign-On i jednego Sign-Out profilów SAML w Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 06f80f94be25e42c9e8f0270e6cb15aca086ae18
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994404"
---
# <a name="how-microsoft-identity-platform-uses-the-saml-protocol"></a>Jak platforma tożsamości firmy Microsoft używa protokołu SAML

Platforma tożsamości firmy Microsoft korzysta z protokołu SAML 2,0, aby umożliwić aplikacjom udostępnianie użytkownikom funkcji logowania jednokrotnego. Profil SAML [logowania](single-sign-on-saml-protocol.md) jednokrotnego [i logowania jednokrotnego](single-sign-out-saml-protocol.md) w usłudze Azure AD objaśnia, w jaki sposób potwierdzenia SAML, protokoły i powiązania są używane w usłudze dostawca tożsamości.

Protokół SAML wymaga od dostawcy tożsamości (Microsoft Identity platform) i dostawcy usług (aplikacji) do wymiany informacji o sobie.

Gdy aplikacja jest zarejestrowana w usłudze Azure AD, Deweloper aplikacji rejestruje informacje dotyczące Federacji w usłudze Azure AD. Te informacje obejmują **Identyfikator URI przekierowania** i **Identyfikator URI metadanych** aplikacji.

Platforma tożsamości firmy Microsoft używa **identyfikatora URI metadanych** usługi w chmurze do pobrania klucza podpisywania i identyfikatora URI wylogowania. Klient może otworzyć aplikację w **usłudze Azure AD — > rejestracji aplikacji** , a następnie we **właściwościach ustawienia->**, które mogą zaktualizować adres URL wylogowywania. Dzięki temu platforma tożsamości firmy Microsoft może wysłać odpowiedź do poprawnego adresu URL. 

Azure Active Directory udostępnia specyficzne dla dzierżawców i wspólne (niezależne od dzierżawy) punkty końcowe logowania jednokrotnego i wylogowania jednokrotnego. Te adresy URL przedstawiają lokalizacje adresowane — nie są tylko identyfikatorami, więc możesz przejść do punktu końcowego, aby odczytać metadane.

* Punkt końcowy specyficzny dla dzierżawy znajduje się w lokalizacji `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` . *\<TenantDomainName>* Symbol zastępczy reprezentuje zarejestrowaną nazwę domeny lub identyfikator GUID TenantID dzierżawy usługi Azure AD. Na przykład metadane federacji dzierżawy contoso.com są następujące: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Punkt końcowy niezależny od dzierżawy znajduje się w lokalizacji `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` . W tym adresie punktu końcowego pojawia się **typowy** zamiast nazwy domeny dzierżawy lub identyfikatora.

Aby uzyskać informacje o dokumentach metadanych Federacji publikowanych przez usługę Azure AD, zobacz [metadane federacji](../azuread-dev/azure-ad-federation-metadata.md).
