---
title: Rotacja certyfikatów dla Azure SQL Database & wystąpienia zarządzanego SQL
description: Zapoznaj się z nadchodzącymi zmianami zmian certyfikatów głównych, które wpłyną na Azure SQL Database i wystąpienie zarządzane Azure SQL
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: a273b9aaae083bb4566d289e9680b50c686d4e9b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346065"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Informacje o zmianach w katalogu głównym urzędu certyfikacji dla Azure SQL Database & wystąpienia zarządzanego SQL

Azure SQL Database & wystąpienie zarządzane SQL zmieni certyfikat główny dla aplikacji klienckiej/sterownika włączonej przy użyciu protokołu SSL, który służy do nawiązywania bezpiecznego połączenia TDS. [Bieżący certyfikat główny](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) jest ustawiany na wygaśnięcie 26 października 2020 w ramach standardowej konserwacji i najlepszych rozwiązań w zakresie zabezpieczeń. Ten artykuł zawiera bardziej szczegółowe informacje o nadchodzących zmianach, zasobach, których to dotyczy, oraz o czynnościach, które należy wykonać, aby zapewnić łączność z serwerem bazy danych przez aplikację.

## <a name="what-update-is-going-to-happen"></a>Jaką aktualizację ma mieć miejsce?

[Forum przeglądarki certyfikatów](https://cabforum.org/) urzędu certyfikacji Niedawno opublikowane raporty wielu certyfikatów wystawionych przez dostawców urzędu certyfikacji jako niezgodne.

Zgodnie z wymaganiami dotyczącymi zgodności w branży dostawcy urzędu certyfikacji rozpoczęły Odwoływanie certyfikatów urzędu certyfikacji dla niezgodnych urzędów certyfikacji, co wymaga od serwerów używania certyfikatów wystawionych przez zgodne urzędy certyfikacji i podpisanych przez certyfikaty urzędu certyfikacji z tych zgodnych urzędów certyfikacji. Ponieważ Azure SQL Database & wystąpienie zarządzane SQL aktualnie używa jednego z tych niezgodnych certyfikatów, których aplikacje klienckie używają do sprawdzania poprawności połączeń SSL, musimy upewnić się, że podejmowane są odpowiednie działania (opisane poniżej) w celu zminimalizowania potencjalnego wpływu na serwery SQL platformy Azure.

Nowy certyfikat zostanie użyty od 26 października 2020. Jeśli podczas nawiązywania połączenia z klientem SQL (TrustServerCertificate = true) używasz pełnej weryfikacji certyfikatu serwera, musisz się upewnić, że klient SQL będzie mógł zweryfikować nowy certyfikat główny przed 26 października 2020.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>Jak mogę dowiedzieć się, czy aplikacja może być objęta problemem?

Wszystkie aplikacje, które używają protokołu SSL/TLS i sprawdzają, czy certyfikat główny musi zaktualizować certyfikat główny, aby można było połączyć się z Azure SQL Database & wystąpienie zarządzane SQL. 

Jeśli nie korzystasz obecnie z protokołu SSL/TLS, nie ma to wpływu na dostępność aplikacji. Możesz sprawdzić, czy aplikacja kliencka próbuje zweryfikować certyfikat główny, przeglądając parametry połączenia. Jeśli TrustServerCertificate jest jawnie ustawiona na wartość true, nie ma to żadnego oddziaływania.

Jeśli sterownik klienta używa magazynu certyfikatów systemu operacyjnego, jako większości sterowników, a system operacyjny jest regularnie konserwowany, ta zmiana prawdopodobnie nie wpłynie na Ciebie, ponieważ certyfikat główny, do którego przejdziemy, powinien być już dostępny w magazynie zaufanych certyfikatów głównych. Sprawdź, czy Baltimore CyberDigiCert GlobalRoot G2 i sprawdź, czy istnieje.

Jeśli sterownik klienta korzysta z lokalnego magazynu certyfikatów plików, aby uniknąć przerwania dostępności aplikacji z powodu nieoczekiwanego odwołania certyfikatów lub zaktualizowania certyfikatu, który został odwołany, zapoznaj się z sekcją [**co należy zrobić, aby zachować łączność**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Co należy zrobić, aby zachować łączność

Aby uniknąć przerwania dostępności aplikacji z powodu nieoczekiwanego odwołania certyfikatów lub zaktualizowania certyfikatu, który został odwołany, wykonaj następujące czynności:

*   Pobierz Baltimore CyberTrust root & DigiCert GlobalRoot G2 głównego urzędu certyfikacji z poniższych linków:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Wygeneruj połączony magazyn certyfikatów urzędu certyfikacji z certyfikatami **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** .

## <a name="what-can-be-the-impact"></a>Jaki może być wpływ?
Jeśli sprawdzasz poprawność certyfikatów serwera zgodnie z opisem w tym miejscu, dostępność aplikacji może się przerwać, ponieważ baza danych nie będzie osiągalna. W zależności od aplikacji może pojawić się wiele komunikatów o błędach, w tym między innymi:
*   Nieprawidłowy certyfikat/odwołany certyfikat
*   Przekroczono limit czasu połączenia
*   Błąd, jeśli dotyczy

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Czy jeśli nie korzystam z protokołu SSL/TLS, czy muszę zaktualizować główny urząd certyfikacji?
Jeśli nie korzystasz z protokołu SSL/TLS, nie są wymagane żadne akcje dotyczące tej zmiany. Nadal należy ustawić plan rozpoczęcia korzystania z najnowszej wersji protokołu TLS zgodnie z planem wymuszania TLS w najbliższej przyszłości.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>Co się stanie, jeśli nie zaktualizuję certyfikatu głównego przed 26 października 2020?
Jeśli certyfikat główny nie zostanie zaktualizowany do 30 listopada 2020, aplikacje, które łączą się za pośrednictwem protokołu SSL/TLS i sprawdzają, czy certyfikat główny nie będą mogły komunikować się z Azure SQL Database & wystąpienie zarządzane SQL i aplikacja wystąpią problemy z łącznością z Azure SQL Database & wystąpienia zarządzanego SQL.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>Czy muszę zaplanować przestoje związane z konserwacją tej zmiany?<BR>
Nie. Ze względu na to, że zmiana ta jest dostępna tylko po stronie klienta, aby nawiązać połączenie z serwerem, w przypadku tej zmiany nie jest wymagane przestoje.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>Co zrobić, jeśli nie mogę uzyskać zaplanowanego przestoju dla tej zmiany przed 26 października 2020?
Ponieważ klienci używani do łączenia się z serwerem muszą zaktualizować informacje o certyfikacie zgodnie z opisem w sekcji poprawka [tutaj](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity), w tym przypadku nie ma potrzeby przestoju serwera.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>Czy w przypadku utworzenia nowego serwera po 30 listopada 2020 będzie to miało wpływ?
W przypadku serwerów utworzonych po 26 października 2020 można użyć nowo wystawionego certyfikatu dla aplikacji, aby połączyć się przy użyciu protokołu SSL.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Jak często firma Microsoft aktualizuje swoje certyfikaty lub jakie są zasady wygasania?
Te certyfikaty używane przez Azure SQL Database & wystąpienia zarządzane SQL są udostępniane przez zaufane urzędy certyfikacji. Aby obsłużyć te certyfikaty w Azure SQL Database & wystąpienie zarządzane SQL jest powiązane z obsługą tych certyfikatów przez urząd certyfikacji. Jednak podobnie jak w tym przypadku, w tych wstępnie zdefiniowanych certyfikatach mogą znajdować się nieprzewidziane usterki, które muszą zostać ustalone najwcześniej.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>Czy w przypadku korzystania z replik odczytu muszę wykonać tę aktualizację tylko na serwerze głównym lub w odniesieniu do wszystkich replik odczytu?
Ponieważ ta aktualizacja jest zmianą po stronie klienta, jeśli klient używany do odczytywania danych z serwera repliki, będzie musiał również zastosować zmiany dla tych klientów. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>Czy mamy zapytanie po stronie serwera, aby sprawdzić, czy jest używany protokół SSL?
Ponieważ ta konfiguracja jest po stronie klienta, informacje nie są dostępne po stronie serwera.

### <a name="what-if-i-have-further-questions"></a>Co zrobić, jeśli mam więcej pytań?
Jeśli masz plan pomocy technicznej i potrzebujesz pomocy technicznej, Utwórz żądanie pomocy technicznej platformy Azure, patrz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
