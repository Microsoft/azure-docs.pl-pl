---
title: Jak zarządzane tożsamości zasobów platformy Azure współpracują z usługą Azure Virtual Machines
description: Opis zarządzanych tożsamości zasobów platformy Azure współpracują z maszynami wirtualnymi platformy Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/11/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: b93f45b05e6d7773afc2f750fd1a9a034c01ca1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89178675"
---
# <a name="how-managed-identities-for-azure-resources-work-with-azure-virtual-machines"></a>Jak zarządzane tożsamości zasobów platformy Azure współpracują z maszynami wirtualnymi platformy Azure

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie.

W tym artykule dowiesz się, jak zarządzane tożsamości działają z maszynami wirtualnymi platformy Azure.


## <a name="how-it-works"></a>Jak to działa

Wewnętrznie tożsamości zarządzane to nazwy główne usług typu specjalnego, które mogą być używane tylko z zasobami platformy Azure. Po usunięciu tożsamości zarządzanej jest automatycznie usuwana odpowiednia jednostka usługi.
Ponadto po utworzeniu tożsamości User-Assigned lub System-Assigned dostawca zasobów tożsamości zarządzanej (MSRP) wystawia certyfikat wewnętrznie dla tej tożsamości. 

Przy użyciu tożsamości zarządzanej kod może zażądać tokenów dostępu dla usług obsługujących uwierzytelnianie usługi Azure AD. Platforma Azure zapewnia stopniową obsługę poświadczeń, które są używane przez wystąpienie usługi. 

Na poniższym diagramie pokazano, jak tożsamości usługi zarządzanej współpracują z maszynami wirtualnymi platformy Azure:

![Tożsamości usługi zarządzanej i maszyny wirtualne platformy Azure](media/how-managed-identities-work-vm/data-flow.png)

|  Właściwość    | Tożsamość zarządzana przypisana przez system | Tożsamość zarządzana przypisana przez użytkownika |
|------|----------------------------------|--------------------------------|
| Tworzenie |  Utworzone w ramach zasobu platformy Azure (na przykład maszyny wirtualnej lub Azure App Service platformy Azure). | Utworzono jako autonomiczny zasób platformy Azure. |
| Cykl życiowy | Współużytkowany cykl życia z zasobem platformy Azure, za pomocą którego utworzono tożsamość zarządzaną. <br/> Po usunięciu zasobu nadrzędnego tożsamość zarządzana również jest usuwana. | Niezależny cykl życia. <br/> Musi być jawnie usunięty. |
| Udostępnianie w zasobach platformy Azure | Nie można udostępnić. <br/> Może być skojarzony tylko z jednym zasobem platformy Azure. | Mogą być udostępniane. <br/> Ta sama tożsamość zarządzana przypisana przez użytkownika może być skojarzona z więcej niż jednym zasobem platformy Azure. |
| Typowe przypadki użycia | Obciążenia zawarte w ramach pojedynczego zasobu platformy Azure. <br/> Obciążenia, dla których są potrzebne niezależne tożsamości. <br/> Na przykład aplikacja działająca na jednej maszynie wirtualnej | Obciążenia działające na wielu zasobach, które mogą współużytkować jedną tożsamość. <br/> Obciążenia wymagające wstępnej autoryzacji do bezpiecznego zasobu w ramach przepływu aprowizacji. <br/> Obciążenia, w których zasoby są często odtwarzane, ale uprawnienia powinny pozostać spójne. <br/> Na przykład obciążenie, w przypadku którego wiele maszyn wirtualnych potrzebuje dostępu do tego samego zasobu |

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

1. Usługa Azure Resource Manager otrzymuje żądanie włączenia tożsamości zarządzanej przypisanej przez system na maszynie wirtualnej.

2. Usługa Azure Resource Manager tworzy w usłudze Azure AD jednostkę usługi potrzeby tożsamości maszyny wirtualnej. Jednostka usługi jest tworzona w dzierżawie usługi Azure AD, która jest zaufana w ramach subskrypcji.

3. Azure Resource Manager konfiguruje tożsamość na maszynie wirtualnej przez zaktualizowanie punktu końcowego tożsamości usługi Azure Instance Metadata Service przy użyciu identyfikatora klienta i certyfikatu jednostki usługi.

4. Teraz, gdy maszyna wirtualna zyskała tożsamość, używamy informacji o jednostce usługi w celu przyznania maszynie wirtualnej dostępu do zasobów platformy Azure. Aby wywoływać Azure Resource Manager, użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby przypisać odpowiednią rolę do jednostki usługi maszyny wirtualnej. Aby wywołać usługę Key Vault, należy przyznać kodowi dostęp do określonego wpisu tajnego lub klucza w usłudze Key Vault.

5. Kod uruchomiony na maszynie wirtualnej może zażądać tokenu z punktu końcowego usługi metadanych wystąpienia platformy Azure, dostępne tylko z poziomu maszyny wirtualnej: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Parametr zasobu określa usługę, do której jest wysyłany token. Aby przeprowadzić uwierzytelnianie w usłudze Azure Resource Manager, należy użyć elementu `resource=https://management.azure.com/`.
    - Parametr wersji interfejsu API określa wersję usługi IMDS, użyj wartości api-version=2018-02-01 lub nowszej.

6. W usłudze Azure AD jest wykonywane wywołanie żądające tokenu dostępu (jak określono w kroku 5) przy użyciu certyfikatu i identyfikatora klienta skonfigurowanego w kroku 3. Usługa Azure AD zwraca token dostępu powiązany z internetowym tokenem JSON (JWT, JSON Web Token).

7. Kod wysyła token dostępu w wywołaniu do usługi, która obsługuje uwierzytelnianie w usłudze Azure AD.

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

1. Usługa Azure Resource Manager otrzymuje żądanie włączenia tożsamości zarządzanej przypisanej przez użytkownika.

2. Usługa Azure Resource Manager tworzy w usłudze Azure AD jednostkę usługi na potrzeby tożsamości zarządzanej przypisanej przez użytkownika. Jednostka usługi jest tworzona w dzierżawie usługi Azure AD, która jest zaufana w ramach subskrypcji.

3. Azure Resource Manager odbiera żądanie skonfigurowania tożsamości zarządzanej przypisanej przez użytkownika na maszynie wirtualnej i aktualizuje punkt końcowy tożsamości usługi Azure Instance Metadata Service przy użyciu identyfikatora klienta i certyfikatu podmiotu zarządzania tożsamościami zarządzanymi przez użytkownika.

4. Po utworzeniu tożsamości zarządzanej przypisanej przez użytkownika należy użyć informacji o jednostce usługi w celu przyznania dostępu do zasobów platformy Azure. Aby wywoływać Azure Resource Manager, Użyj usługi Azure RBAC, aby przypisać odpowiednią rolę do nazwy głównej usługi tożsamości przypisanej przez użytkownika. Aby wywołać usługę Key Vault, należy przyznać kodowi dostęp do określonego wpisu tajnego lub klucza w usłudze Key Vault.

   > [!Note]
   > Ten krok można również wykonać przed krokiem 3.

5. Kod uruchomiony na maszynie wirtualnej może zażądać tokenu z punktu końcowego tożsamości usługi Azure Instance Metadata Service, dostępnego tylko z poziomu maszyny wirtualnej: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Parametr zasobu określa usługę, do której jest wysyłany token. Aby przeprowadzić uwierzytelnianie w usłudze Azure Resource Manager, należy użyć elementu `resource=https://management.azure.com/`.
    - Parametr Identyfikator klienta określa tożsamość, dla której jest żądany token. Ta wartość jest wymagana do przeprowadzenia uściślania, gdy na jednej maszynie wirtualnej znajduje się więcej niż jedna tożsamość przypisana przez użytkownika.
    - Parametr wersji interfejsu API określa wersję usługi Azure Instance Metadata Service. Należy użyć wersji `api-version=2018-02-01` lub nowszej.

6. W usłudze Azure AD jest wykonywane wywołanie żądające tokenu dostępu (jak określono w kroku 5) przy użyciu certyfikatu i identyfikatora klienta skonfigurowanego w kroku 3. Usługa Azure AD zwraca token dostępu powiązany z internetowym tokenem JSON (JWT, JSON Web Token).
7. Kod wysyła token dostępu w wywołaniu do usługi, która obsługuje uwierzytelnianie w usłudze Azure AD.


## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z funkcją tożsamości zarządzanych dla zasobów platformy Azure, korzystając z następujących przewodników Szybki start:

* [Używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Resource Manager](tutorial-windows-vm-access-arm.md)
* [Używanie przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Resource Manager](tutorial-linux-vm-access-arm.md)
