---
title: Ważne zmiany dotyczące Azure Security Center
description: Nadchodzące zmiany w Azure Security Center, które mogą być potrzebne, i dla których może być konieczne zaplanowanie
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: bf89135d3b5d33936d0b5d4142e966fa3f8f52b9
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92559044"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Ważne zmiany w Azure Security Center

> [!IMPORTANT]
> Informacje na tej stronie odnoszą się do produktów lub funkcji w wersji wstępnej, które mogą być znacząco modyfikowane przed ich udostępnieniem komercyjnemu. Firma Microsoft nie udziela żadnych zobowiązań ani gwarancji, wyraźnych ani dorozumianych, w odniesieniu do informacji podanych w tym miejscu.

Na tej stronie znajdziesz informacje o zmianach, które są planowane dla Security Center. Opisano w nim planowane modyfikacje produktu, które mogą mieć wpływ na takie rzeczy jak bezpieczny wynik lub przepływy pracy.

Jeśli szukasz najnowszych informacji o wersji, znajdziesz je w temacie [co nowego w Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planowane zmiany

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>Zalecenia dotyczące programu testowego zabezpieczeń platformy Azure do dodania (wersja zapoznawcza)

| Aspekt | Szczegóły |
|---------|---------|
|Data anonsu | 26 października 2020  |
|Data zmiany  |  Listopad 2020 |
|Wpływ     | Potencjalnie więcej zaleceń do przejrzenia.<br>Brak bezpośredniego wpływu na zalecenia dotyczące bezpiecznego przeglądu oceny nie ma wpływu na swój Bezpieczny wynik.<br>Brak bezpośredniego wpływu na kondycję zasobów — zalecenia dotyczące wersji zapoznawczej nie renderują zasobu w złej kondycji.|
|  |  |

Usługa Azure Security test to zestaw wytycznych dotyczących zabezpieczeń i zgodności opartych na platformie Azure, które są stosowane do najlepszych rozwiązań w zakresie bezpieczeństwa i zapewniających zgodność. [Dowiedz się więcej o teście porównawczym zabezpieczeń platformy Azure](../security/benchmarks/introduction.md).

Do Security Center zostaną dodane następujące 29 nowych zaleceń w celu zwiększenia zakresu testów porównawczych.

Zalecenia dotyczące wersji zapoznawczej nie powodują złej kondycji zasobu i nie są uwzględniane w obliczeniach bezpiecznego wyniku. Skoryguj je wszędzie tam, gdzie to możliwe, aby po zakończeniu okresu korzystania z wersji zapoznawczej doczyniły się do oceny. Dowiedz się więcej o tym, jak odpowiedzieć na te zalecenia w temacie [Koryguj zalecenia w Azure Security Center](security-center-remediate-recommendations.md).

- Azure Backup powinna być włączona dla maszyn wirtualnych
- Wartość przechowywania inspekcji dla serwerów SQL powinna wynosić co najmniej 90 dni
- Dzienniki diagnostyczne powinny być włączone w App Service 
- Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL
- Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL
- FTPS powinna być wymagana w aplikacji interfejsu API
- FTPS powinna być wymagana w aplikacji funkcji
- FTPS powinna być wymagana w aplikacji sieci Web
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MySQL
- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for PostgreSQL
- Środowisko Java należy zaktualizować do najnowszej wersji dla aplikacji interfejsu API
- Środowisko Java należy zaktualizować do najnowszej wersji aplikacji funkcji
- Środowisko Java należy zaktualizować do najnowszej wersji aplikacji sieci Web
- Tożsamość zarządzana powinna być używana w aplikacji interfejsu API
- Tożsamość zarządzana powinna być używana w aplikacji funkcji
- Tożsamość zarządzana powinna być używana w aplikacji sieci Web
- Aplikacja PHP powinna zostać zaktualizowana do najnowszej wersji dla aplikacji interfejsu API
- Aplikacja PHP powinna zostać zaktualizowana do najnowszej wersji aplikacji sieci Web
- Prywatny punkt końcowy powinien być włączony dla serwerów MariaDB
- Prywatny punkt końcowy powinien być włączony dla serwerów MySQL
- Prywatny punkt końcowy powinien być włączony dla serwerów PostgreSQL
- Środowisko Python należy zaktualizować do najnowszej wersji dla aplikacji interfejsu API
- Środowisko Python należy zaktualizować do najnowszej wersji aplikacji funkcji
- Środowisko Python należy zaktualizować do najnowszej wersji aplikacji sieci Web
- Protokół TLS należy zaktualizować do najnowszej wersji dla aplikacji interfejsu API
- Protokół TLS należy zaktualizować do najnowszej wersji aplikacji funkcji
- Protokół TLS należy zaktualizować do najnowszej wersji aplikacji sieci Web
- Aplikacje sieci Web powinny zażądać certyfikatu SSL dla wszystkich żądań przychodzących

Powiązane linki:

- [Dowiedz się więcej o teście porównawczym zabezpieczeń platformy Azure](../security/benchmarks/introduction.md).
- [Dowiedz się więcej o usłudze Azure API Apps](../app-service/app-service-web-tutorial-rest-api.md)
- [Dowiedz się więcej na temat aplikacji funkcji platformy Azure](../azure-functions/functions-overview.md)
- [Dowiedz się więcej o usłudze Azure Web Apps](../app-service/overview.md)
- [Dowiedz się więcej o Azure Database for MariaDB](../mariadb/overview.md)
- [Dowiedz się więcej o Azure Database for MySQL](../mysql/overview.md)
- [Dowiedz się więcej o Azure Database for PostgreSQL](../postgresql/overview.md)

## <a name="next-steps"></a>Następne kroki

Wszystkie ostatnie zmiany w produkcie można znaleźć w artykule [co nowego w programie Azure Security Center](release-notes.md).