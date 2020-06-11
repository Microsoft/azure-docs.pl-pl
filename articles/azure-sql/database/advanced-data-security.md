---
title: Zaawansowane zabezpieczenia danych
description: Dowiedz się więcej na temat funkcji odnajdywania i klasyfikowania poufnych danych, zarządzania lukami w bazie danych oraz wykrywania nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych w Azure SQL Database, wystąpieniu zarządzanym Azure SQL lub Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: 7efcc4a9ec7da2bdd4005c124d5d0e8dd03c4363
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669702"
---
# <a name="advanced-data-security"></a>Zaawansowane zabezpieczenia danych
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Zaawansowane zabezpieczenia danych (ADS) to ujednolicony pakiet zaawansowanych funkcji zabezpieczeń SQL. Usługa ADS jest dostępna dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse Analytics. Obejmuje to funkcję wykrywania i klasyfikowania danych poufnych, uwidacznianie i korygowanie potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywanie nietypowych działań, które mogą wskazywać, że baza danych jest zagrożona. Zapewnia ona pojedynczą lokalizację, w której można włączać te możliwości i zarządzać nimi.

## <a name="overview"></a>Omówienie

Usługa ADS oferuje zestaw zaawansowanych funkcji zabezpieczeń SQL, takich jak funkcja odnajdywania danych & klasyfikacja, Ocena luk w zabezpieczeniach i zaawansowana ochrona przed zagrożeniami.
- [Funkcja odnajdywania danych &](data-discovery-and-classification-overview.md) obejmuje funkcje wbudowane w Azure SQL Database, wystąpienie zarządzane Azure SQL i usługa Azure Synapse na potrzeby odnajdywania, klasyfikowania, etykietowania i raportowania poufnych danych w bazach danych. Za jej pomocą można zapewniać wgląd w stan klasyfikacji bazy danych oraz śledzić dostęp do danych poufnych w bazie danych i poza jej granicami.
- [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to prosta w konfiguracji usługa, która umożliwia odnajdywanie, śledzenie i rozwiązywanie problemów z potencjalnymi lukami w zabezpieczeniach bazy danych. Zapewnia wgląd w stan zabezpieczeń i obejmuje czynności do wykonania w celu rozwiązywania problemów z zabezpieczeniami i ulepszania bazy danych FORTIFICATIONS.
- Usługa [Advanced Threat Protection](threat-detection-overview.md) wykrywa nietypowe działania wskazujące na nieprawidłowe i potencjalnie szkodliwe próby uzyskania dostępu do bazy danych lub wykorzystania jej. Ciągle monitoruje bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataki iniekcji SQL Azure oraz nietypowe wzorce dostępu do bazy danych. Alerty usługi Advanced Threat Protection zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.

Włącz opcję Zaawansowane zabezpieczenia danych, aby włączyć wszystkie te funkcje. Jednym kliknięciem jest możliwość włączenia reklam dla wszystkich baz danych na [serwerze](logical-servers.md) na platformie Azure lub w wystąpieniu zarządzanym SQL. Włączanie i zarządzanie ustawieniami ADS wymaga przynależności do roli [programu SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) lub jednej z ról administratora bazy danych lub serwera.

Cennik programu ADS jest wyrównany do warstwy Standardowa Azure Security Center, gdzie każdy chroniony serwer lub wystąpienie zarządzane jest liczony jako jeden węzeł. Nowo chronione zasoby kwalifikują się do bezpłatnej wersji próbnej Security Center warstwy Standardowa. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Wprowadzenie do reklam

Poniższe kroki ułatwiają rozpoczęcie pracy z usługą ADS.

## <a name="1-enable-ads"></a>1. Włącz reklamy

Włącz reklamy, przechodząc do opcji **Zaawansowane zabezpieczenia danych** w obszarze nagłówka **zabezpieczenia** dla serwera lub wystąpienia zarządzanego.

> [!NOTE]
> Konto magazynu jest automatycznie tworzone i konfigurowane do przechowywania wyników skanowania **oceny luk w zabezpieczeniach** . Jeśli już włączono usługi AD dla innego serwera w tej samej grupie zasobów i regionie, używane jest istniejące konto magazynu.

![Włącz reklamy](./media/advanced-data-security/enable_ads.png)

> [!NOTE]
> Koszt reklam jest wyrównany przy użyciu cen warstwy Standardowa Azure Security Center na węzeł, gdzie węzeł jest całym serwerem lub wystąpieniem zarządzanym. W ten sposób płacisz tylko raz na ochronę wszystkich baz danych na serwerze lub w wystąpieniu zarządzanym z REKLAMami. Możesz bezpłatnie wypróbować reklamy, korzystając z bezpłatnej wersji próbnej.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Rozpocznij klasyfikowanie danych, śledzenie luk w zabezpieczeniach i badanie alertów dotyczących zagrożeń

Kliknij kartę **& odnajdywania danych** , aby zobaczyć zalecane poufne kolumny do klasyfikowania i klasyfikowania danych za pomocą etykiet trwałych. Kliknij kartę **Ocena luk w zabezpieczeniach** , aby wyświetlić i zarządzać skanowaniem i raportami luk w zabezpieczeniach oraz śledzić schemacie zabezpieczeń. Jeśli alerty zabezpieczeń zostały odebrane, kliknij kartę **Zaawansowana ochrona przed zagrożeniami** , aby wyświetlić szczegóły alertów i wyświetlić skonsolidowany raport dotyczący wszystkich alertów w ramach subskrypcji platformy Azure za pośrednictwem strony alerty zabezpieczeń Azure Security Center.

## <a name="3-manage-ads-settings"></a>3. Zarządzaj ustawieniami reklam

Aby wyświetlić ustawienia reklam i zarządzać nimi, przejdź do pozycji **Zaawansowane zabezpieczenia danych** w obszarze nagłówek **zabezpieczenia** dla serwera lub wystąpienia zarządzanego. Na tej stronie można włączać lub wyłączać reklamy oraz modyfikować ustawienia oceny luk w zabezpieczeniach i zaawansowanej ochrony przed zagrożeniami dla całego serwera lub wystąpienia zarządzanego.

![Ustawienia serwera](./media/advanced-data-security/server_settings.png)

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Zarządzanie ustawieniami usługi AD dla bazy danych SQL

Aby zastąpić ustawienia reklamy dla konkretnej bazy danych, zaznacz pole wyboru **Włącz zaawansowane zabezpieczenia danych na poziomie bazy danych** . Tej opcji należy używać tylko w przypadku, gdy istnieje szczególny wymóg otrzymywania osobnych alertów dotyczących zaawansowanej ochrony przed zagrożeniami lub oceny luk w zabezpieczeniach dla pojedynczej bazy danych zamiast alertów i odebranych wyników dla wszystkich baz danych na serwerze lub w zarządzanym wystąpieniu.

Po zaznaczeniu pola wyboru można skonfigurować odpowiednie ustawienia dla tej bazy danych.

![Ustawienia bazy danych i zaawansowanej ochrony przed zagrożeniami](./media/advanced-data-security/database_threat_detection_settings.png)

Zaawansowane ustawienia zabezpieczeń danych dla serwera lub wystąpienia zarządzanego można również uzyskać z okienka baza danych usługi AD. W okienku główne reklamy kliknij pozycję **Ustawienia** , a następnie kliknij pozycję **Wyświetl zaawansowane ustawienia serwera zabezpieczeń danych**.

![Ustawienia bazy danych](./media/advanced-data-security/database_settings.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [klasyfikacji & odnajdywania danych](data-discovery-and-classification-overview.md)
- Dowiedz się więcej na temat [oceny luk w zabezpieczeniach](sql-vulnerability-assessment.md)
- Dowiedz się więcej na temat [zaawansowanej ochrony przed zagrożeniami](threat-detection-configure.md)
- Dowiedz się więcej o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
