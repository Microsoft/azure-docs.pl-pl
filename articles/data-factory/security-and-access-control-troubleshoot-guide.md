---
title: Rozwiązywanie problemów z zabezpieczeniami i kontrolami dostępu
description: Dowiedz się, jak rozwiązywać problemy z zabezpieczeniami i kontrolą dostępu w Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008733"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Rozwiązywanie problemów dotyczących zabezpieczeń Azure Data Factory i kontroli dostępu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów dotyczących zabezpieczeń i kontroli dostępu w programie Azure Data Factory.

## <a name="common-errors-and-messages"></a>Typowe błędy i komunikaty

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>Nieprawidłowy lub pusty klucz uwierzytelniania po wyłączeniu dostępu do sieci publicznej

#### <a name="symptoms"></a>Objawy

Własne środowisko Integration Runtime zgłasza błąd "klucz uwierzytelniania jest nieprawidłowy lub pusty" po wyłączeniu dostępu do sieci publicznej dla Data Factory.

#### <a name="cause"></a>Przyczyna

Problem jest najprawdopodobniej spowodowany przez problem z rozpoznawaniem nazw DNS, ponieważ wyłączenie łączności publicznej i ustanowienie prywatnego punktu końcowego nie ma pomocy przy ponownym nawiązywaniu połączenia.

#### <a name="resolution"></a>Rozwiązanie

1. PsPing z nazwą FQDN funkcji ADF i stwierdziła, że bufor przeszedł do publicznego punktu końcowego ADF, nawet po jego wyłączeniu.

1. Zmień plik hosta na maszynie wirtualnej, aby mapować prywatny adres IP na nazwę FQDN, a następnie ponownie uruchom PsPing. Bufor będzie mógł przejść do poprawnego prywatnego adresu IP w usłudze ADF.

1. Zarejestruj ponownie środowisko Integration Runtime (My-Hosted), aby je uruchomić.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Nie można zarejestrować klucza uwierzytelniania IR na własnych maszynach wirtualnych z powodu prywatnego linku

#### <a name="symptoms"></a>Objawy

Nie można zarejestrować klucza uwierzytelniania IR na samohostowanej maszynie wirtualnej z powodu włączenia prywatnego linku.

Informacje o błędzie są wyświetlane w następujący sposób:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Przyczyna

Problem może być spowodowany przez maszynę wirtualną, na której ma zostać zainstalowana SHIR. Dostęp do sieci publicznej powinien być włączony na potrzeby nawiązywania połączenia z chmurą.

#### <a name="resolution"></a>Rozwiązanie

 **Rozwiązanie 1:** Aby rozwiązać ten problem, można wykonać następujące czynności:

1. Przejdź do poniższego linku: 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Kliknij opcję **Wypróbuj ją** i Wypełnij wszystkie wymagane szczegóły. Wklej poniżej właściwość w **treści** :

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Kliknij przycisk **Uruchom** na końcu strony, aby uruchomić funkcję. Upewnij się, że pobrano kod odpowiedzi 200. Wklejona właściwość będzie również wyświetlana w definicji JSON.

1. Następnie możesz spróbować ponownie dodać klucz uwierzytelniania IR w środowisku Integration Runtime.


**Rozwiązanie 2:** W przypadku rozwiązania można zapoznać się z poniższym artykułem:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Spróbuj włączyć dostęp do sieci publicznej za pomocą interfejsu użytkownika.

![Włącz dostęp do sieci publicznej](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Prywatny link do Data Factory](data-factory-private-link.md)
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-data-factory.html)
*  [Forum przepełnienia stosu dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)