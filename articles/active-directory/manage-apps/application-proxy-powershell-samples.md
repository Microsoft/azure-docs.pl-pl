---
title: Przykłady programu PowerShell dla usługi Azure serwer proxy aplikacji usługi Azure AD
description: Za pomocą tych przykładów programu PowerShell dla usługi Azure serwer proxy aplikacji usługi Azure AD można uzyskać informacje o aplikacjach i łącznikach serwera proxy aplikacji, przypisać użytkowników i grupy do aplikacji oraz uzyskać informacje o certyfikatach.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117b4fec13c22ffb2f3163d50be44734d24ef3fa
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259104"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Przykłady programu Azure AD PowerShell dla usługi Azure serwer proxy aplikacji usługi Azure AD

Poniższa tabela zawiera linki do przykładów skryptów programu PowerShell dla usługi Azure serwer proxy aplikacji usługi Azure AD. Te przykłady wymagają [modułu AzureAD v2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) lub w [wersji zapoznawczej modułu AzureAD v2 PowerShell dla programu Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), chyba że zaznaczono inaczej.


Aby uzyskać więcej informacji o poleceniach cmdlet używanych w tych przykładach, zobacz [Zarządzanie aplikacjami serwera proxy aplikacji](/powershell/module/azuread/#application_proxy_application_management) i [Zarządzanie łącznikami serwera proxy aplikacji](/powershell/module/azuread/#application_proxy_connector_management).

| Link | Opis |
|---|---|
|**Aplikacje serwera proxy aplikacji**||
| [Wyświetl podstawowe informacje dotyczące wszystkich aplikacji serwera proxy aplikacji](scripts/powershell-get-all-app-proxy-apps-basic.md) | Wyświetla listę podstawowych informacji (AppId, DisplayName, ObjId) dotyczących wszystkich aplikacji serwera proxy aplikacji w katalogu. |
| [Wyświetl rozszerzone informacje dla wszystkich aplikacji serwera proxy aplikacji](scripts/powershell-get-all-app-proxy-apps-extended.md) | Wyświetla informacje rozszerzone (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) dotyczące wszystkich aplikacji serwera proxy aplikacji w katalogu.  |
| [Wyświetl listę wszystkich aplikacji serwera proxy aplikacji według grupy łączników](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Wyświetla listę informacji o wszystkich aplikacjach serwera proxy aplikacji w katalogu i grup łączników, do których są przypisane aplikacje. |
| [Pobierz wszystkie aplikacje serwera proxy aplikacji z zasadami istnienia tokenu](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Wyświetla listę wszystkich aplikacji serwera proxy aplikacji w katalogu za pomocą zasad okresu istnienia tokenu i ich szczegółów. Ten przykład wymaga programu [PowerShell w wersji zapoznawczej modułu AzureAD v2](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview). |
|**Grupy łączników**||
| [Pobierz wszystkie grupy łączników i łączniki w katalogu](scripts/powershell-get-all-connectors.md) | Wyświetla listę wszystkich grup łączników i łączników w katalogu. |
| [Przenoszenie wszystkich aplikacji przypisanych do grupy łączników do innej grupy łączników](scripts/powershell-move-all-apps-to-connector-group.md) | Przenosi wszystkie aplikacje aktualnie przypisane do grupy łączników do innej grupy łączników. |
|**Przypisani Użytkownicy i grupy**||
| [Wyświetlanie użytkowników i grup przypisanych do aplikacji serwera proxy aplikacji](scripts/powershell-display-users-group-of-app.md) | Wyświetla listę użytkowników i grup przypisanych do określonej aplikacji serwera proxy aplikacji. |
| [Przypisywanie użytkownika do aplikacji](scripts/powershell-assign-user-to-app.md) | Przypisuje określonego użytkownika do aplikacji. |
| [Przypisywanie grupy do aplikacji](scripts/powershell-assign-group-to-app.md) | Przypisuje konkretną grupę do aplikacji. |
|**Konfiguracja zewnętrznego adresu URL**||
| [Pobierz wszystkie aplikacje serwera proxy aplikacji przy użyciu domen domyślnych (. msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Wyświetla listę wszystkich aplikacji serwera proxy aplikacji przy użyciu domen domyślnych (. msappproxy.net). |
| [Pobierz wszystkie aplikacje serwera proxy aplikacji przy użyciu publikowania symboli wieloznacznych](scripts/powershell-get-all-wildcard-apps.md) | Wyświetla listę wszystkich aplikacji serwera proxy aplikacji przy użyciu publikowania symboli wieloznacznych. |
|**Konfiguracja domeny niestandardowej**||
| [Pobierz wszystkie aplikacje serwera proxy aplikacji przy użyciu domen niestandardowych i informacji o certyfikacie](scripts/powershell-get-all-custom-domains-and-certs.md) | Wyświetla listę wszystkich aplikacji serwera proxy aplikacji, które używają domen niestandardowych i informacje o certyfikatach skojarzonych z domenami niestandardowymi. |
| [Pobierz wszystkie aplikacje aplikacji serwera proxy usługi Azure AD opublikowane bez przekazania certyfikatu](scripts/powershell-get-all-custom-domain-no-cert.md) | Wyświetla listę wszystkich aplikacji serwera proxy aplikacji, które korzystają z domen niestandardowych, ale nie mają prawidłowego przekazanego certyfikatu TLS/SSL. |
| [Pobierz wszystkie aplikacje aplikacji serwera proxy usługi Azure AD opublikowane przy użyciu identycznego certyfikatu](scripts/powershell-get-custom-domain-identical-cert.md) | Wyświetla listę wszystkich aplikacji serwera proxy usługi Azure AD opublikowanych przy użyciu identycznego certyfikatu. |
| [Pobierz wszystkie aplikacje aplikacji serwera proxy usługi Azure AD opublikowane przy użyciu identycznego certyfikatu i zastąp je](scripts/powershell-get-custom-domain-replace-cert.md) | W przypadku aplikacji aplikacji Azure AD proxy, które są publikowane przy użyciu identycznego certyfikatu, umożliwia zbiorcze zastąpienie certyfikatu. |
