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
ms.openlocfilehash: 99c03ae4430d1a4caf575bdb9900200af0217bf1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109774"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Rozwiązywanie problemów dotyczących zabezpieczeń Azure Data Factory i kontroli dostępu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów dotyczących zabezpieczeń i kontroli dostępu w programie Azure Data Factory.

## <a name="common-errors-and-messages"></a>Typowe błędy i komunikaty


### <a name="connectivity-issue-of-copy-activity-in-cloud-data-store"></a>Problem z łącznością działania kopiowania w magazynie danych w chmurze

#### <a name="symptoms"></a>Objawy

W przypadku wystąpienia problemu z połączeniem dla magazynu danych Source/ujścia można zwrócić różne rodzaje komunikatów o błędach.

#### <a name="cause"></a>Przyczyna 

Problem jest głównie spowodowany następującymi czynnikami:

1. Ustawienie serwera proxy w węźle samoobsługowego środowiska IR (Jeśli używasz własnego środowiska IR)

1. Ustawienie zapory w węźle samodzielnego środowiska IR (Jeśli używasz własnego środowiska IR)

1. Ustawienie zapory w magazynie danych w chmurze

#### <a name="resolution"></a>Rozwiązanie

1. Najpierw sprawdź następujące punkty, aby upewnić się, że przyczyną problemu jest problem z łącznością:

   - Ten błąd jest zgłaszany z łączników źródła/ujścia.

   - Działanie kończy się niepowodzeniem na początku kopiowania

   - Jest to spójny błąd w przypadku Azure IR lub samodzielnego środowiska IR z jednym węzłem, ponieważ może to być Losowa awaria w przypadku samodzielnego środowiska IR z wieloma węzłami, jeśli tylko część węzłów ma problem.

1. Sprawdź ustawienia serwera proxy, zapory i sieci, jeśli używasz własnego środowiska **IR** , ponieważ uruchomienie go w tym samym magazynie danych może powieść się w Azure IR. Aby rozwiązać problem, Skorzystaj z następujących linków:

   [Samoobsługowe porty i zapory IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls) 
    [Łącznik ADLS](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
1. Jeśli używasz **Azure IR**, spróbuj wyłączyć ustawienie zapory dla magazynu danych. W ten sposób można rozwiązać problem dotyczący następujących dwóch sytuacji:
  
   * [Adresy IP Azure IR](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) nie znajdują się na liście dozwolonych.

   * *Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tej funkcji konta magazynu* jest wyłączone dla usług [Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) i [ADLS Gen 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities).

   * *Zezwalanie na dostęp do usług platformy Azure* nie jest włączone dla ADLS Gen1.

1. Jeśli powyższe metody nie działają, skontaktuj się z firmą Microsoft w celu uzyskania pomocy.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Nieprawidłowy lub pusty klucz uwierzytelniania po wyłączeniu dostępu do sieci publicznej

#### <a name="symptoms"></a>Objawy

Po wyłączeniu dostępu do sieci publicznej dla Data Factory, ponieważ środowisko Integration Runtime (własne) zgłasza następujący błąd: "klucz uwierzytelniania jest nieprawidłowy lub pusty".

#### <a name="cause"></a>Przyczyna

Problem jest najprawdopodobniej spowodowany przez problem z rozpoznawaniem nazw DNS, ponieważ wyłączenie łączności publicznej i ustanowienie prywatnego punktu końcowego nie ma pomocy przy ponownym nawiązywaniu połączenia.

Aby sprawdzić, czy Data Factory nazwa FQDN jest rozpoznawana jako publiczny adres IP, można wykonać poniższe czynności:

1. Upewnij się, że utworzono maszynę wirtualną platformy Azure w tej samej sieci wirtualnej co Data Factory prywatny punkt końcowy.

2. Uruchom PsPing i polecenie ping z maszyny wirtualnej platformy Azure, aby Data Factory FQDN:

   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`

   > [!Note]
   > Port musi być określony dla polecenia PsPing, podczas gdy port 443 nie jest wymagany.

3. Sprawdź, czy oba polecenia zostały rozpoznane jako publiczny adres IP usługi ADF, który jest oparty na określonym regionie (format xxx. xxx. xxx. 0).

#### <a name="resolution"></a>Rozwiązanie

- Aby uzyskać Azure Data Factory, możesz zapoznać się z artykułem w [prywatnym linku platformy Azure](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints). Instrukcja służy do konfigurowania prywatnej strefy/serwera DNS w celu rozpoznania Data Factory FQDN dla prywatnego adresu IP.

- Jeśli nie chcesz obecnie konfigurować prywatnej strefy/serwera DNS, wykonaj poniższe kroki jako rozwiązanie tymczasowe. Jednak niestandardowa usługa DNS jest nadal zalecana jako długoterminowe rozwiązanie.

  1. Zmień plik hosta w systemie Windows i Mapuj prywatny adres IP (osobisty punkt końcowy ADF) na nazwę FQDN ADF.
  
     Przejdź do ścieżki "C:\Windows\System32\drivers\etc" na maszynie wirtualnej platformy Azure i Otwórz plik **hosta** za pomocą Notatnika. Dodaj wiersz mapowanie prywatnego adresu IP do nazwy FQDN na końcu pliku, a następnie Zapisz zmiany.
     
     ![Dodaj mapowanie do hosta](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Uruchom ponownie te same polecenia w powyższych krokach weryfikacji, aby sprawdzić odpowiedź, która powinna zawierać prywatny adres IP.

  1. Zarejestruj ponownie własne środowisko Integration Runtime i problem powinien zostać rozwiązany.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Nie można zarejestrować klucza uwierzytelniania IR na własnych maszynach wirtualnych z powodu prywatnego linku

#### <a name="symptoms"></a>Objawy

Nie można zarejestrować klucza uwierzytelniania IR na samohostowanej maszynie wirtualnej z powodu włączenia prywatnego linku.

Informacje o błędzie są wyświetlane w następujący sposób:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Przyczyna

Problem może być spowodowany przez maszynę wirtualną, w której próbujesz zainstalować własne środowisko IR. Aby nawiązać połączenie z chmurą, upewnij się, że jest włączony dostęp do sieci publicznej.

#### <a name="resolution"></a>Rozwiązanie

 **Rozwiązanie 1:** Aby rozwiązać ten problem, można wykonać następujące czynności:

1. Przejdź do strony [fabryki — aktualizacja](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) .

1. W prawym górnym rogu wybierz przycisk **Wypróbuj** .

1. W obszarze **Parametry** wykonaj wymagane informacje. 

1. W obszarze **treść** wklej następującą właściwość:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Wybierz pozycję **Uruchom** , aby uruchomić funkcję. 

1. Upewnij się, że jest wyświetlany **kod odpowiedzi: 200** . Wklejona właściwość powinna również być wyświetlana w definicji JSON.

1. Ponownie Dodaj klucz uwierzytelniania IR w środowisku Integration Runtime.


**Rozwiązanie 2:** W przypadku rozwiązania można zapoznać się z poniższym artykułem:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Spróbuj włączyć dostęp do sieci publicznej w interfejsie użytkownika, jak pokazano na poniższym zrzucie ekranu:

![Włącz dostęp do sieci publicznej](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Prywatny link do Data Factory](data-factory-private-link.md)
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&stronie](/answers/topics/azure-data-factory.html)
*  [Forum przepełnienia stosu dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
