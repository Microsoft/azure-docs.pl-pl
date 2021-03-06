---
title: Przechowywanie i używanie certyfikatów w usługach Azure Cloud Services (wsparcie dodatkowe)
description: Procesy dotyczące przechowywania i używania certyfikatów na platformie Azure Cloud Services (obsługa rozszerzona)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4d771e77fcca05b090e5d47d70ae93ece8f79e3e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865707"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Używanie certyfikatów z usługą Azure Cloud Services (obsługa rozszerzona)

Key Vault jest używany do przechowywania certyfikatów skojarzonych z Cloud Services (obsługa rozszerzona). Magazyny kluczy można tworzyć za poorednictwem [Azure Portal](../key-vault/general/quick-create-portal.md) i [programu PowerShell](../key-vault/general/quick-create-powershell.md). Dodaj certyfikaty do Key Vault, a następnie odwołuje się do odcisków palców certyfikatu w pliku konfiguracji usługi. Należy również włączyć Key Vault dla odpowiednich uprawnień, aby zasób Cloud Services (obsługa rozszerzona) mógł pobrać certyfikat zapisany jako wpisy tajne z Key Vault.  

## <a name="upload-a-certificate-to-key-vault"></a>Przekaż certyfikat do Key Vault 

1.  Zaloguj się do Azure Portal i przejdź do Key Vault. Jeśli nie masz skonfigurowanego Key Vault, możesz wybrać opcję Utwórz ją w tym samym oknie.

2. Wybieranie zasad **dostępu**

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="Obraz pokazuje Wybieranie zasad dostępu z bloku magazynu kluczy.":::

3. Upewnij się, że zasady dostępu obejmują następującą właściwość:
    - **Włącz dostęp do usługi Azure Virtual Machines na potrzeby wdrożenia**

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Obraz przedstawia okno zasady dostępu w Azure Portal.":::
 
4.  Wybierz **Certyfikaty** 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="Obraz przedstawia Wybieranie opcji certyfikaty z okna zasady blokowania magazynu kluczy w Azure Portal.":::

5. Wybierz pozycję **Generuj/Importuj**

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="Obraz przedstawia Wybieranie opcji Generuj/Importuj":::

4.  Ukończ wymagane informacje, aby zakończyć przekazywanie certyfikatu. Certyfikat musi znajdować się w **. Format PFX** .

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="Obraz pokazuje okno importowania w Azure Portal.":::

5.  Dodaj szczegóły certyfikatu do roli w pliku konfiguracji usługi (. cscfg). Upewnij się, że odcisk palca certyfikatu w Azure Portal jest zgodny z odciskiem palca w pliku konfiguracji usługi (. cscfg). 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```
6.  W przypadku wdrożenia za pomocą szablonu ARM certificateUrl można znaleźć, przechodząc do certyfikatu w magazynie kluczy oznaczonym jako identyfikator tajny

    :::image type="content" source="media/certs-and-key-vault-6.png" alt-text="Obraz przedstawia pole identyfikatora tajnego w magazynie kluczy.":::

## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).
