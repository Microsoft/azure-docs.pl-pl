---
title: Rozwiązywanie problemów z zabezpieczeniami i kontrolami dostępu
description: Dowiedz się, jak rozwiązywać problemy z zabezpieczeniami i kontrolą dostępu w Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 70e4d0c1a9f26f83924c1343e4e8e51c5595b324
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944513"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Rozwiązywanie problemów dotyczących zabezpieczeń Azure Data Factory i kontroli dostępu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów dotyczących zabezpieczeń i kontroli dostępu w programie Azure Data Factory.

## <a name="common-errors-and-messages"></a>Typowe błędy i komunikaty

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>Problem z łącznością w działaniu kopiowania magazynu danych w chmurze

#### <a name="symptoms"></a>Objawy

W przypadku wystąpienia problemów z łącznością w źródłowym lub ujścia danych magazynu mogą zostać zwrócone różne komunikaty o błędach.

#### <a name="cause"></a>Przyczyna 

Problem jest zwykle spowodowany przez jeden z następujących czynników:

* Ustawienie serwera proxy w węźle środowisko Integration Runtime (IR), jeśli używasz własnego środowiska IR.

* Ustawienie zapory w węźle samodzielnego środowiska IR, jeśli używasz własnego środowiska IR.

* Ustawienie zapory w magazynie danych w chmurze.

#### <a name="resolution"></a>Rozwiązanie

* Aby upewnić się, że jest to problem z połączeniem, sprawdź następujące kwestie:

   - Błąd jest zgłaszany z łączników źródła lub ujścia.
   - Awaria znajduje się na początku działania kopiowania.
   - Awaria jest spójna w przypadku Azure IR lub samodzielnego środowiska IR z jednym węzłem, ponieważ może to być błędna awaria w przypadku samodzielnego hosta z wieloma węzłami, jeśli problem występuje tylko w niektórych węzłach.

* Jeśli używasz własnego środowiska **IR**, sprawdź ustawienia serwera proxy, zapory i sieci, ponieważ połączenie z tym samym magazynem danych może się powieść, jeśli używasz Azure IR. Aby rozwiązać problem z tym scenariuszem, zobacz:

   * [Samoobsługowe porty i zapory IR](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Łącznik Azure Data Lake Storage](./connector-azure-data-lake-store.md)
  
* Jeśli używasz **Azure IR**, spróbuj wyłączyć ustawienie zapory dla magazynu danych. Takie podejście może rozwiązać problemy w następujących dwóch sytuacjach:
  
   * [Adresy IP Azure IR](./azure-integration-runtime-ip-addresses.md) nie znajdują się na liście dozwolonych.
   * Funkcja *Zezwalanie na dostęp do tego konta usługi Microsoft zaufane* są wyłączone dla [platformy Azure Blob Storage](./connector-azure-blob-storage.md#supported-capabilities) i [Azure Data Lake Storage generacji 2](./connector-azure-data-lake-storage.md#supported-capabilities).
   * Ustawienie *Zezwalaj na dostęp do usług platformy Azure* nie jest włączone dla Azure Data Lake Storage Gen1.

Jeśli żadna z powyższych metod nie działa, skontaktuj się z firmą Microsoft w celu uzyskania pomocy.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Nieprawidłowy lub pusty klucz uwierzytelniania po wyłączeniu dostępu do sieci publicznej

#### <a name="symptoms"></a>Objawy

Po wyłączeniu dostępu do sieci publicznej dla Data Factory środowisko Integration Runtime (własne) zgłasza następujący błąd: "klucz uwierzytelniania jest nieprawidłowy lub pusty".

#### <a name="cause"></a>Przyczyna

Problem jest najprawdopodobniej spowodowany przez problem z rozpoznawaniem systemu nazw domen (DNS), ponieważ wyłączenie łączności publicznej i ustanowienie prywatnego punktu końcowego uniemożliwia ponowne nawiązanie połączenia.

Aby sprawdzić, czy Data Factory w pełni kwalifikowana nazwa domeny (FQDN) jest rozpoznawana jako publiczny adres IP, wykonaj następujące czynności:

1. Upewnij się, że utworzono maszynę wirtualną platformy Azure w tej samej sieci wirtualnej co Data Factory prywatny punkt końcowy.

2. Uruchom PsPing i polecenie ping z maszyny wirtualnej platformy Azure do Data Factory FQDN:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > Należy określić port dla polecenia PsPing. W tym miejscu jest wyświetlany port 443, ale nie jest to wymagane.

3. Sprawdź, czy oba polecenia rozwiązują Azure Data Factory publicznego adresu IP, który jest oparty na określonym regionie. Adres IP powinien mieć następujący format: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj następujące czynności:
- Zapoznaj się z artykułem [prywatnym platformy Azure dla Azure Data Factory](./data-factory-private-link.md#dns-changes-for-private-endpoints) artykułu. Instrukcja służy do konfigurowania prywatnej strefy lub serwera DNS w celu rozpoznania Data Factory FQDN na prywatnym adresie IP.

- Zalecamy używanie niestandardowego systemu DNS jako rozwiązania długoterminowego. Jeśli jednak nie chcesz konfigurować prywatnej strefy lub serwera DNS, wypróbuj następujące tymczasowe rozwiązanie:

  1. Zmień plik hosta w systemie Windows i Mapuj prywatny adres IP (Azure Data Factory prywatny punkt końcowy) na Azure Data Factory FQDN.
  
     Na maszynie wirtualnej platformy Azure przejdź do `C:\Windows\System32\drivers\etc` , a następnie otwórz plik *hosta* w Notatniku. Dodaj wiersz, który mapuje prywatny adres IP na nazwę FQDN na końcu pliku, a następnie Zapisz zmiany.
     
     ![Zrzut ekranu przedstawiający mapowanie prywatnego adresu IP na host.](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Uruchom ponownie te same polecenia jak w poprzednich krokach weryfikacji, aby sprawdzić odpowiedź, która powinna zawierać prywatny adres IP.

  1. Zarejestruj ponownie środowisko Integration Runtime, a problem powinien zostać rozwiązany.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Nie można zarejestrować klucza uwierzytelniania IR na własnych maszynach wirtualnych z powodu prywatnego linku

#### <a name="symptoms"></a>Objawy

Nie można zarejestrować klucza uwierzytelniania IR na samohostowanej maszynie wirtualnej, ponieważ link prywatny jest włączony. Zostanie wyświetlony następujący komunikat o błędzie:

"Nie można pobrać tokenu usługi z usługi ADF z kluczem * * * * * * * * * * * * * * * i kosztem czasu: 0,1250079 sekund kod błędu to: InvalidGatewayKey, activityId: XXXXXXX i szczegółowy komunikat o błędzie to adres IP klienta nie jest prawidłowym prywatnym adresem IP, ponieważ Fabryka danych nie może uzyskać dostępu do sieci publicznej, dlatego nie można skontaktować się z chmurą w celu pomyślnego nawiązania połączenia".

#### <a name="cause"></a>Przyczyna

Problem może być spowodowany przez maszynę wirtualną, w której próbujesz zainstalować własne środowisko IR. Aby nawiązać połączenie z chmurą, upewnij się, że jest włączony dostęp do sieci publicznej.

#### <a name="resolution"></a>Rozwiązanie

**Rozwiązanie 1**
 
Aby rozwiązać ten problem, wykonaj następujące czynności:

1. Przejdź do strony [fabryki — aktualizacja](/rest/api/datafactory/Factories/Update) .

1. W prawym górnym rogu wybierz przycisk **Wypróbuj** .
1. W obszarze **Parametry** wykonaj wymagane informacje. 
1. W obszarze **treść** wklej następującą właściwość:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. Wybierz pozycję **Uruchom** , aby uruchomić funkcję. 

1. W obszarze **Parametry** wykonaj wymagane informacje. 

1. W obszarze **treść** wklej następującą właściwość:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Wybierz pozycję **Uruchom** , aby uruchomić funkcję. 
1. Upewnij się, że jest wyświetlany **kod odpowiedzi: 200** . Wklejona właściwość powinna również być wyświetlana w definicji JSON.

1. Ponownie Dodaj klucz uwierzytelniania IR w środowisku Integration Runtime.


**Rozwiązanie 2**

Aby rozwiązać ten problem, przejdź do [prywatnego linku platformy Azure dla Azure Data Factory](./data-factory-private-link.md).

Spróbuj włączyć dostęp do sieci publicznej w interfejsie użytkownika, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający kontrolkę "Enabled" dla "Zezwalaj na dostęp do sieci publicznej" w okienku sieć.](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Prywatny link do Data Factory](data-factory-private-link.md)
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&stronie](/answers/topics/azure-data-factory.html)
*  [Forum przepełnienia stosu dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)