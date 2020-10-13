---
title: Uruchamianie usługi Service Fabric platformy Azure jako użytkownika lub grupy usługi AD
description: Dowiedz się, jak uruchomić usługę jako użytkownika lub grupę Active Directory w Service Fabric autonomicznym klastrze systemu Windows.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: d4a7afc2ddb0f39014a7cf0fd006d7fe23673a95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840731"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Uruchamianie usługi jako użytkownika lub grupy Active Directory
W klastrze autonomicznym systemu Windows Server można uruchomić usługę jako użytkownika lub grupę Active Directory przy użyciu zasad RunAs.  Domyślnie Service Fabric aplikacje są uruchamiane w ramach konta, w ramach którego działa proces Fabric.exe. Uruchamianie aplikacji na różnych kontach, nawet w udostępnianym środowisku hostowanym, sprawia, że są one bezpieczniejsze od siebie nawzajem. Należy zauważyć, że ta funkcja używa Active Directory lokalnie w domenie, a nie Azure Active Directory (Azure AD).  Możesz również uruchomić usługę jako [konto usługi zarządzanej przez grupę (gMSA)](service-fabric-run-service-as-gmsa.md).

Przy użyciu użytkownika domeny lub grupy można następnie uzyskać dostęp do innych zasobów w domenie (na przykład udziałów plików), którym udzielono uprawnień.

W poniższym przykładzie przedstawiono Active Directory użytkownika o nazwie *Użytkownik testowy* z hasłem domeny szyfrowanym przy użyciu certyfikatu o nazwie mój *certyfikat*. Możesz użyć `Invoke-ServiceFabricEncryptText` polecenia programu PowerShell, aby utworzyć tajny tekst szyfrowania. Aby uzyskać szczegółowe informacje, zobacz Zarządzanie wpisami [tajnymi w aplikacjach Service Fabric](service-fabric-application-secret-management.md) .

Należy wdrożyć klucz prywatny certyfikatu w celu odszyfrowania hasła do komputera lokalnego przy użyciu metody poza pasmem (na platformie Azure — za pośrednictwem Azure Resource Manager). Następnie, gdy Service Fabric wdraża pakiet usługi na komputerze, może odszyfrować klucz tajny i (wraz z nazwą użytkownika) uwierzytelniać się za pomocą Active Directory do uruchamiania w ramach tych poświadczeń.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Jeśli zastosujesz zasady RunAs do usługi, a manifest usługi deklaruje zasoby punktów końcowych przy użyciu protokołu HTTP, należy również określić **SecurityAccessPolicy**.  Aby uzyskać więcej informacji, zobacz [przypisywanie zasad dostępu zabezpieczeń dla punktów końcowych http i https](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Następnym krokiem jest zapoznanie się z następującymi artykułami:
* [Omówienie modelu aplikacji](service-fabric-application-model.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
