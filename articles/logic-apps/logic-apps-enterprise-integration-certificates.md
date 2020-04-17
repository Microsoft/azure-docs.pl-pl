---
title: Zabezpieczanie komunikatów B2B z certyfikatami
description: Dodawanie certyfikatów ułatwiające zabezpieczanie wiadomości B2B w usłudze Azure Logic Apps za pomocą pakietu Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 19a1883685193e80da5f1365ec2a30db0b8754f6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450144"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>Zwiększenie bezpieczeństwa komunikatów B2B przy użyciu certyfikatów

Gdy musisz zachować poufność komunikacji B2B, możesz zwiększyć bezpieczeństwo komunikacji B2B w aplikacjach integracji przedsiębiorstwa, w szczególności w aplikacjach logiki, dodając certyfikaty do konta integracji. Certyfikaty są dokumentami cyfrowymi, które sprawdzają tożsamość uczestników komunikacji elektronicznej i pomagają w zabezpieczeniu komunikacji w ten sposób:

* Szyfruj zawartość wiadomości.
* Podpisz cyfrowo wiadomości.

Tych certyfikatów można używać w aplikacjach integracyjnych dla przedsiębiorstw:

* [Certyfikaty publiczne](https://en.wikipedia.org/wiki/Public_key_certificate), które należy zakupić od publicznego [urzędu certyfikacji internetu,](https://en.wikipedia.org/wiki/Certificate_authority) ale nie wymagają żadnych kluczy. 

* Certyfikaty prywatne lub [*certyfikaty z podpisem własnym*](https://en.wikipedia.org/wiki/Self-signed_certificate), które można utworzyć i wydać samodzielnie, ale również wymagają kluczy prywatnych. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Przekazywanie certyfikatu publicznego

Aby użyć *certyfikatu publicznego* w aplikacjach logiki, które mają możliwości B2B, należy najpierw przekazać certyfikat na konto integracji. Po zdefiniowaniu właściwości w [umowach,](logic-apps-enterprise-integration-agreements.md) które tworzysz, certyfikat jest dostępny, aby ułatwić zabezpieczenie wiadomości B2B.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). W głównym menu platformy Azure wybierz pozycję **Wszystkie zasoby**. W polu wyszukiwania wprowadź nazwę konta integracji, a następnie wybierz żądane konto integracji.

   ![Znajdowanie i wybieranie konta integracyjnego](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. W obszarze **Komponenty**wybierz kafelek **Certyfikaty.**

   ![Wybierz "Certyfikaty"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. W obszarze **Certyfikaty**wybierz pozycję **Dodaj**. W obszarze **Dodaj certyfikat**podaj te szczegóły certyfikatu. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------|
   | **Nazwa** | <*nazwa certyfikatu*> | Nazwa certyfikatu, która jest "publicCert" w tym przykładzie | 
   | **Typ certyfikatu** | Public | Typ certyfikatu |
   | **Certyfikat** | <*nazwa pliku certyfikatu*> | Aby znaleźć i wybrać plik certyfikatu, który chcesz przekazać, wybierz ikonę folderu obok pola **Certyfikat.** |
   ||||

   ![Wybierz "Dodaj", podaj szczegóły certyfikatu](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Po platformie Azure sprawdza poprawność wyboru platforma Azure przekazuje certyfikat.

   ![Platforma Azure wyświetla nowy certyfikat](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Przekazywanie certyfikatu prywatnego

Aby użyć *certyfikatu prywatnego* w aplikacjach logiki, które mają możliwości B2B, należy najpierw przekazać certyfikat na konto integracji. Musisz również mieć klucz prywatny, który najpierw dodasz do [usługi Azure Key Vault](../key-vault/key-vault-get-started.md). 

Po zdefiniowaniu właściwości w [umowach,](logic-apps-enterprise-integration-agreements.md) które tworzysz, certyfikat jest dostępny, aby ułatwić zabezpieczenie wiadomości B2B.

> [!NOTE]
> W przypadku certyfikatów prywatnych należy dodać odpowiedni certyfikat publiczny, który pojawia się w ustawieniach **wyślij i odbierz** [umowy AS2](logic-apps-enterprise-integration-as2.md) do podpisywania i szyfrowania wiadomości.

1. [Dodaj klucz prywatny do usługi Azure Key Vault](../key-vault/certificates/certificate-scenarios.md#import-a-certificate) i podaj nazwę **klucza**.
   
2. Autoryzuj aplikacje azure logic do wykonywania operacji w usłudze Azure Key Vault. Aby udzielić dostępu do jednostki usługi Aplikacje logiki, należy użyć polecenia Programu PowerShell, [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), na przykład:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). W głównym menu platformy Azure wybierz pozycję **Wszystkie zasoby**. W polu wyszukiwania wprowadź nazwę konta integracji, a następnie wybierz żądane konto integracji.

   ![Znajdź swoje konto integracyjne](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. W obszarze **Komponenty**wybierz kafelek **Certyfikaty.**  

   ![Wybieranie kafelka Certyfikaty](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. W obszarze **Certyfikaty**wybierz pozycję **Dodaj**. W obszarze **Dodaj certyfikat**podaj te szczegóły certyfikatu. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------|
   | **Nazwa** | <*nazwa certyfikatu*> | Nazwa certyfikatu, która jest "privateCert" w tym przykładzie | 
   | **Typ certyfikatu** | Private | Typ certyfikatu |
   | **Certyfikat** | <*nazwa pliku certyfikatu*> | Aby znaleźć i wybrać plik certyfikatu, który chcesz przekazać, wybierz ikonę folderu obok pola **Certyfikat.** Podczas korzystania z magazynu kluczy dla klucza prywatnego przekazany plik będzie certyfikatem publicznym. | 
   | **Grupa zasobów** | <*integracja-konto-grupa zasobów*> | Grupa zasobów konta integracji, która w tym przykładzie jest "MyResourceGroup". | 
   | **Usługa Key Vault** | <*nazwa magazynu kluczy*> | Nazwa magazynu kluczy platformy Azure |
   | **Nazwa klucza** | <*nazwa klucza*> | Nazwa klucza |
   ||||

   ![Wybierz "Dodaj", podaj szczegóły certyfikatu](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Po platformie Azure sprawdza poprawność wyboru platforma Azure przekazuje certyfikat.

   ![Platforma Azure wyświetla nowy certyfikat](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Następne kroki

* [Tworzenie umowy B2B](logic-apps-enterprise-integration-agreements.md)
