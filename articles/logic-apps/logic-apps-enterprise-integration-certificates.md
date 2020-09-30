---
title: Zabezpieczanie komunikatów B2B przy użyciu certyfikatów
description: Dodaj certyfikaty, aby pomóc w zabezpieczaniu komunikatów B2B w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 03fc17c0d071cef4c8de92c6b50d60d961d18aef
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565263"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>Zwiększanie bezpieczeństwa wiadomości B2B przy użyciu certyfikatów

W przypadku konieczności zachowania poufności komunikacji B2B można zwiększyć bezpieczeństwo komunikacji B2B w aplikacjach integracji przedsiębiorstwa, a w odniesieniu do aplikacji logiki, dodając certyfikaty do konta integracji. Certyfikaty to dokumenty cyfrowe, które sprawdzają tożsamość uczestników w komunikacji elektronicznej i pomagają w zabezpieczaniu komunikacji w następujący sposób:

* Szyfruj zawartość wiadomości.
* Cyfrowe podpisywanie wiadomości.

Możesz użyć tych certyfikatów w aplikacjach integracji przedsiębiorstwa:

* [Certyfikaty publiczne](https://en.wikipedia.org/wiki/Public_key_certificate), które należy zakupić od publicznego [urzędu certyfikacji (CA)](https://en.wikipedia.org/wiki/Certificate_authority) , ale nie wymagają żadnych kluczy. 

* Certyfikaty prywatne lub [*Certyfikaty*](https://en.wikipedia.org/wiki/Self-signed_certificate)z podpisem własnym, które tworzysz i wystawiają samodzielnie, ale również wymagają kluczy prywatnych. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Przekaż certyfikat publiczny

Aby używać *publicznego certyfikatu* w usłudze Logic Apps z możliwościami B2B, należy najpierw przekazać certyfikat do konta integracji. Po zdefiniowaniu właściwości w tworzonych [umowach](logic-apps-enterprise-integration-agreements.md) certyfikat jest dostępny, aby pomóc w zabezpieczeniu wiadomości B2B.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). W głównym menu platformy Azure wybierz pozycję **wszystkie zasoby**. W polu wyszukiwania wprowadź nazwę konta integracji, a następnie wybierz odpowiednie konto integracji.

   ![Znajdź i wybierz swoje konto integracji](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. W obszarze **składniki**wybierz kafelek **Certyfikaty** .

   ![Wybieranie "certyfikatów"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. W obszarze **Certyfikaty**wybierz pozycję **Dodaj**. W obszarze **Dodawanie certyfikatu**podaj te szczegóły certyfikatu. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------|
   | **Nazwa** | <*Nazwa certyfikatu*> | Nazwa certyfikatu, czyli "publicCert" w tym przykładzie | 
   | **Typ certyfikatu** | Public | Typ certyfikatu |
   | **Certyfikat** | <*Nazwa pliku certyfikatu*> | Aby znaleźć i wybrać plik certyfikatu, który ma zostać przekazany, wybierz ikonę folderu obok pola **certyfikat** . |
   ||||

   ![Zrzut ekranu pokazuje, gdzie wybrać opcję Dodaj, aby podać szczegóły certyfikatu.](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Po sprawdzeniu wyboru przez platformę Azure usługa Azure przekazuje certyfikat.

   ![Zrzut ekranu pokazujący, gdzie platforma Azure wyświetla nowy certyfikat.](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Przekaż certyfikat prywatny

Aby użyć *prywatnego certyfikatu* w usłudze Logic Apps z możliwościami B2B, należy najpierw przekazać certyfikat do konta integracji. Musisz również mieć klucz prywatny, który należy najpierw dodać do [Azure Key Vault](../key-vault/general/overview.md). 

Po zdefiniowaniu właściwości w tworzonych [umowach](logic-apps-enterprise-integration-agreements.md) certyfikat jest dostępny, aby pomóc w zabezpieczeniu wiadomości B2B.

> [!NOTE]
> W przypadku certyfikatów prywatnych upewnij się, że dodano odpowiedni certyfikat publiczny, który jest wyświetlany w ustawieniach **wysyłania i odbierania** w [umowie AS2](logic-apps-enterprise-integration-as2.md) w celu podpisywania i szyfrowania wiadomości.

1. [Dodaj swój klucz prywatny do Azure Key Vault](../key-vault/certificates/certificate-scenarios.md#import-a-certificate) i podaj **nazwę klucza**.
   
2. Autoryzuj Azure Logic Apps, aby wykonywać operacje na Azure Key Vault. Aby udzielić dostępu do jednostki usługi Logic Apps, użyj polecenia programu PowerShell, [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), na przykład:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). W głównym menu platformy Azure wybierz pozycję **wszystkie zasoby**. W polu wyszukiwania wprowadź nazwę konta integracji, a następnie wybierz odpowiednie konto integracji.

   ![Znajdowanie konta integracji](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. W obszarze **składniki**wybierz kafelek **Certyfikaty** .  

   ![Wybierz kafelek certyfikaty](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. W obszarze **Certyfikaty**wybierz pozycję **Dodaj**. W obszarze **Dodawanie certyfikatu**podaj te szczegóły certyfikatu. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------|
   | **Nazwa** | <*Nazwa certyfikatu*> | Nazwa certyfikatu, czyli "privateCert" w tym przykładzie | 
   | **Typ certyfikatu** | Private | Typ certyfikatu |
   | **Certyfikat** | <*Nazwa pliku certyfikatu*> | Aby znaleźć i wybrać plik certyfikatu, który ma zostać przekazany, wybierz ikonę folderu obok pola **certyfikat** . W przypadku korzystania z magazynu kluczy dla klucza prywatnego przekazany plik będzie certyfikatem publicznym. | 
   | **Grupa zasobów** | <*Integracja — konto-Grupa zasobów*> | Grupa zasobów konta integracji, która jest w tym przykładzie "grupą zasobów" | 
   | **Usługa Key Vault** | <*Nazwa magazynu kluczy*> | Nazwa magazynu kluczy platformy Azure |
   | **Nazwa klucza** | <*Nazwa klucza*> | Nazwa klucza |
   ||||

   ![Wybierz pozycję "Dodaj", podaj szczegóły certyfikatu](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Po sprawdzeniu wyboru przez platformę Azure usługa Azure przekazuje certyfikat.

   ![Na platformie Azure jest wyświetlany nowy certyfikat](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Następne kroki

* [Tworzenie umowy B2B](logic-apps-enterprise-integration-agreements.md)
