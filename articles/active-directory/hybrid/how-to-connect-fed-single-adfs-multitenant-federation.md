---
title: Federowanie wiele usług Azure AD za pomocą jednego AD FS — Azure
description: Z tego dokumentu dowiesz się, jak federować wiele wystąpień usługi Azure AD przy użyciu jednego wystąpienia usługi AD FS.
keywords: federate, ADFS, AD FS, multiple tenants, single AD FS, one ADFS, multi-tenant federation, multi-forest adfs, aad connect, federation, cross-tenant federation
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956428b6f197912e2ab7c3a94133ed9d59f37749
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279928"
---
# <a name="federate-multiple-instances-of-azure-ad-with-single-instance-of-ad-fs"></a>Federowanie wielu wystąpień usługi Azure AD przy użyciu jednego wystąpienia usługi AD FS

Pojedyncza farma usługi AD FS o wysokiej dostępności może federować wiele lasów, jeśli istnieje między nimi dwukierunkowa relacja zaufania. Te lasy mogą, ale nie muszą, odpowiadać tej samej usłudze Azure Active Directory. Ten artykuł zawiera instrukcje dotyczące konfigurowania federacji między pojedynczym wdrożeniem usługi AD FS i co najmniej dwoma lasami synchronizującymi dane z różnymi usługami Azure AD.

![Federacja wielu dzierżaw z jedną usługą AD FS](./media/how-to-connect-fed-single-adfs-multitenant-federation/concept.png)
 
> [!NOTE]
> W tym scenariuszu nie jest obsługiwane zapisywanie zwrotne urządzeń ani automatyczne dołączanie urządzeń.

> [!NOTE]
> W celu skonfigurowania federacji w tym scenariuszu nie można użyć programu Azure AD Connect, ponieważ ten program umożliwia konfigurowanie federacji dla domen w pojedynczej usłudze Azure AD.

## <a name="steps-for-federating-ad-fs-with-multiple-azure-ad"></a>Kroki umożliwiające federowanie usługi AD FS z wieloma usługami Azure AD

Na potrzeby tej procedury przyjmij, że domena contoso.com w usłudze Azure Active Directory contoso.onmicrosoft.com jest już sfederowana z lokalną usługą AD FS zainstalowaną w lokalnym środowisku Active Directory contoso.com. Fabrikam.com to domena w usłudze Azure Active Directory fabrikam.onmicrosoft.com.

## <a name="step-1-establish-a-two-way-trust"></a>Krok 1. Ustanów dwukierunkową relację zaufania
 
Aby usługa AD FS w domenie contoso.com mogła uwierzytelniać użytkowników w domenie fabrikam.com, między domenami contoso.com i fabrikam.com musi istnieć dwukierunkowa relacja zaufania. Postępuj zgodnie z wytycznymi w tym [artykule](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816590(v=ws.10)), aby ustanowić dwukierunkową relację zaufania.
 
## <a name="step-2-modify-contosocom-federation-settings"></a>Krok 2. Zmodyfikuj ustawienia federacji domeny contoso.com 
 
Domyślny wystawca ustawiany dla jednej domeny federacyjnej z usługą AD FS to „http\://nazwa_FQDN_ADFS/adfs/services/trust”, na przykład `http://fs.contoso.com/adfs/services/trust`. Usługa Azure Active Directory wymaga unikatowego wystawcy dla każdej domeny federacyjnej. Ponieważ ta sama usługa AD FS będzie federować dwie domeny, wartość wystawcy należy zmodyfikować tak, aby była unikatowa dla każdej domeny federowanej przez usługę AD FS z usługą Azure Active Directory. 
 
Na serwerze usług AD FS otwórz program PowerShell usługi Azure AD (upewnij się, że moduł MSOnline jest zainstalowany) i wykonaj następujące czynności:
 
Nawiąż połączenie z usługą Azure Active Directory zawierającą domenę contoso.com (Connect-MsolService). Zaktualizuj ustawienia federacji domeny contoso.com (Update-MsolFederatedDomain -DomainName contoso.com –SupportMultipleDomain)
 
Wystawca w ustawieniu federacji domeny zostanie zmieniony na „http\://contoso.com/adfs/services/trust”, a dla relacji zaufania jednostki zależnej usługi Azure AD zostanie dodana reguła dotycząca oświadczeń wydawania, aby wystawić prawidłową wartość issuerId na podstawie sufiksu UPN.
 
## <a name="step-3-federate-fabrikamcom-with-ad-fs"></a>Krok 3. Sfederuj domenę fabrikam.com z usługą AD FS
 
W sesji programu Azure AD PowerShell wykonaj następujące czynności: nawiąż połączenie z usługą Azure Active Directory, która zawiera domenę fabrikam.com

```powershell
Connect-MsolService
```
Konwertuj domenę zarządzaną fabrikam.com na domenę federacyjną:

```powershell
Convert-MsolDomainToFederated -DomainName fabrikam.com -Verbose -SupportMultipleDomain
```
 
Powyższa operacja spowoduje sfederowanie domeny fabrikam.com z tą samą usługą AD FS. Ustawienia domeny możesz sprawdzić za pomocą polecenia Get-MsolDomainFederationSettings dla obu domen.

## <a name="next-steps"></a>Następne kroki
[Łączenie usługi Active Directory z usługą Azure Active Directory](whatis-hybrid-identity.md)