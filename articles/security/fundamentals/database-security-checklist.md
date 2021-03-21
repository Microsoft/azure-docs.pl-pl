---
title: Lista kontrolna zabezpieczeń usługi Azure Database | Microsoft Docs
description: Lista kontrolna zabezpieczeń usługi Azure Database pozwala upewnić się, że są używane ważne problemy z zabezpieczeniami w chmurze.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 80455b442bbfb9c8a7d40799b2ddd5fc25460578
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595577"
---
# <a name="azure-database-security-checklist"></a>Lista kontrolna zabezpieczeń usługi Azure Database

Aby pomóc w ulepszaniu zabezpieczeń, usługa Azure Database zawiera szereg wbudowanych mechanizmów kontroli zabezpieczeń, których można użyć do ograniczania i kontrolowania dostępu.

Są one następujące:

-    Zapora umożliwiająca tworzenie [reguł zapory](../../azure-sql/database/firewall-configure.md) ograniczających łączność przy użyciu adresu IP
-    Zapora na poziomie serwera dostępna z Azure Portal
-    Reguły zapory na poziomie bazy danych dostępne w programie SSMS
-    Zabezpieczanie łączności z bazą danych przy użyciu bezpiecznych parametrów połączenia
-    Korzystanie z zarządzania dostępem
-    Szyfrowanie danych
-    Inspekcja usługi SQL Database
-    SQL Database wykrywanie zagrożeń

## <a name="introduction"></a>Wprowadzenie
Chmura obliczeniowa wymaga nowych odmian zabezpieczeń, które nie są znane dla wielu użytkowników aplikacji, administratorów bazy danych i programistów. W związku z tym niektóre organizacje są wątpliwości do wdrożenia infrastruktury chmurowej na potrzeby zarządzania danymi ze względu na postrzegane zagrożenia bezpieczeństwa. Wiele z tych problemów można jednak poprawić, aby lepiej zrozumieć funkcje zabezpieczeń wbudowane w Microsoft Azure i Microsoft Azure SQL Database.

## <a name="checklist"></a>Lista kontrolna
Zalecamy zapoznanie się z artykułem [najlepsze rozwiązania w zakresie zabezpieczeń usługi Azure Database](../../azure-sql/database/security-best-practice.md)  przed przejrzeniem tej listy kontrolnej. Po zrozumieniu najlepszych rozwiązań będzie można maksymalnie wykorzystać tę listę kontrolną. Następnie można użyć tej listy kontrolnej, aby upewnić się, że zostały rozwiązane ważne problemy w zabezpieczeniach usługi Azure Database.


|Kategoria listy kontrolnej| Opis|
| ------------ | -------- |
|**Ochrona danych**||
| <br> Szyfrowanie w ruchu/tranzycie| <ul><li>[Transport Layer Security](/windows-server/security/tls/transport-layer-security-protocol)do szyfrowania danych, gdy dane są przenoszone do sieci.</li><li>Baza danych wymaga bezpiecznej komunikacji od klientów opartych na protokole [TDS (strumienia danych tabelarycznych)](/openspecs/windows_protocols/ms-tds/893fcc7e-8a39-4b3c-815a-773b7b982c50) za pośrednictwem protokołu TLS (Transport Layer Security).</li></ul> |
|<br>Szyfrowanie danych magazynowanych| <ul><li>[Transparent Data Encryption](../../azure-sql/database/transparent-data-encryption-tde-overview.md), gdy nieaktywne dane są przechowywane fizycznie w dowolnym formularzu cyfrowym.</li></ul>|
|**Kontrola dostępu**||  
|<br> Dostęp do bazy danych | <ul><li>[Uwierzytelnianie usługi Active Directory (Azure Active Directory](../../azure-sql/database/logins-create-manage.md) Authentication) używa tożsamości zarządzanych przez Azure Active Directory.</li><li>[Autoryzacja](../../azure-sql/database/logins-create-manage.md) użytkowników przyznająca im najmniejsze uprawnienia.</li></ul> |
|<br>Dostęp do aplikacji| <ul><li>[Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) (przy użyciu zasad zabezpieczeń, jednocześnie ograniczając dostęp na poziomie wierszy na podstawie tożsamości użytkownika, roli lub kontekstu wykonania).</li><li>[Dynamiczne maskowanie danych](../../azure-sql/database/dynamic-data-masking-overview.md) (przy użyciu uprawnień & zasad, ograniczanie ekspozycji danych poufnych przez zamaskowanie ich dla użytkowników bez uprawnień)</li></ul>|
|**Proaktywne monitorowanie**||  
| <br>Śledzenie wykrywania &| <ul><li>[Inspekcja](../../azure-sql/database/auditing-overview.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji/dzienniku aktywności na [koncie usługi Azure Storage](../../storage/common/storage-account-create.md).</li><li>Śledź usługę Azure Database Health przy użyciu [dzienników aktywności Azure monitor](../../azure-monitor/essentials/platform-logs-overview.md).</li><li>[Wykrywanie zagrożeń](../../azure-sql/database/threat-detection-configure.md) wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych. </li></ul> |
|<br>Azure Security Center| <ul><li>[Monitorowanie danych](../../security-center/security-center-remediate-recommendations.md) Użyj Azure Security Center jako scentralizowanego rozwiązania do monitorowania zabezpieczeń dla SQL i innych usług platformy Azure.</li></ul>|        

## <a name="conclusion"></a>Podsumowanie
Azure Database to niezawodna platforma baz danych z pełnymi funkcjami zabezpieczeń, które spełniają wiele wymagań w zakresie organizacji i zgodności. Możesz łatwo chronić dane, kontrolując fizyczny dostęp do danych i korzystając z różnych opcji zabezpieczeń danych na poziomie plików, kolumn lub wierszy z Transparent Data Encryption, szyfrowaniem Cell-Level lub zabezpieczeniami Row-Level. Always Encrypted włącza również operacje związane z zaszyfrowanymi danymi, upraszczając proces aktualizacji aplikacji. Z kolei dostęp do dzienników inspekcji SQL Database działania zawiera informacje, które są potrzebne, co pozwala dowiedzieć się, jak i kiedy dane są dostępne.

## <a name="next-steps"></a>Następne kroki
W kilku prostych krokach możesz poprawić zabezpieczenia bazy danych, aby chronić ją przed złośliwymi użytkownikami i nieautoryzowanym dostępem. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

- Skonfiguruj [reguły zapory](../../azure-sql/database/firewall-configure.md) dla serwera i bazy danych.
- Ochrona danych za pomocą [szyfrowania](/sql/relational-databases/security/encryption/sql-server-encryption).
- Włącz [inspekcję SQL Database](../../azure-sql/database/auditing-overview.md).