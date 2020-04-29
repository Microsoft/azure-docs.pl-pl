---
title: Zaawansowane zabezpieczenia danych
description: Informacje na temat funkcji odnajdywania i klasyfikowania poufnych danych, zarządzania lukami w bazie danych oraz wykrywania nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: aed0bcb79dedf057c5943cea9f4b4399b2f630cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80677465"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Zaawansowane zabezpieczenia danych dla Azure SQL Database

Zaawansowane zabezpieczenia danych to ujednolicony pakiet zaawansowanych funkcji zabezpieczeń SQL. Obejmuje to funkcję wykrywania i klasyfikowania danych poufnych, uwidacznianie i korygowanie potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywanie nietypowych działań, które mogą wskazywać, że baza danych jest zagrożona. Zapewnia ona pojedynczą lokalizację, w której można włączać te możliwości i zarządzać nimi.

## <a name="overview"></a>Omówienie

Usługa Advanced Data Security (ADS) oferuje zestaw zaawansowanych funkcji zabezpieczeń SQL, takich jak funkcja odnajdywania danych & klasyfikacji, oceny luk w zabezpieczeniach i zaawansowanej ochrony przed zagrożeniami.

- [Klasyfikacja & odnajdywania danych](sql-database-data-discovery-and-classification.md) udostępnia funkcje wbudowane w Azure SQL Database do odnajdywania, klasyfikowania i etykietowania & raportowania poufnych danych w bazach danych. Za jej pomocą można zapewniać wgląd w stan klasyfikacji bazy danych oraz śledzić dostęp do danych poufnych w bazie danych i poza jej granicami.
- [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to łatwa do skonfigurowania usługa umożliwiająca odnajdywanie i śledzenie potencjalnych luk w zabezpieczeniach bazy danych oraz pomagająca w ich usuwaniu. Zapewnia wgląd w stan zabezpieczeń i zapewnia kroki, które można wykonać w celu rozwiązania problemów z zabezpieczeniami oraz zwiększenia bezpieczeństwa bazy danych.
- Usługa [Advanced Threat Protection](sql-database-threat-detection-overview.md) wykrywa nietypowe działania wskazujące na nieprawidłowe i potencjalnie szkodliwe próby uzyskania dostępu do bazy danych lub wykorzystania jej. Stale monitoruje Twoją bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataków polegających na wstrzyknięciu kodu SQL oraz anomalii we wzorcach dostępu do bazy danych. Alerty usługi Advanced Threat Protection zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.

Aby włączyć wszystkie te funkcje, Włącz opcję SQL AD. Jednym kliknięciem można włączyć usługi AD dla wszystkich baz danych na serwerze SQL Database lub w zarządzanym wystąpieniu. Włączanie ustawień ADS lub zarządzanie nimi wymaga przynależności do roli [programu SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) , roli administratora bazy danych SQL lub roli administratora programu SQL Server. 

Cennik programu ADS jest wyrównany do warstwy Standardowa Azure Security Center, w której każdy chroniony serwer SQL Database lub wystąpienie zarządzane jest liczony jako jeden węzeł. Nowo chronione zasoby kwalifikują się do bezpłatnej wersji próbnej Security Center warstwy Standardowa. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Wprowadzenie z REKLAMami

Poniższe kroki ułatwiają rozpoczęcie pracy z usługą ADS.

## <a name="1-enable-ads"></a>1. Włącz reklamy

Włącz reklamy, przechodząc do opcji **Zaawansowane zabezpieczenia danych** w obszarze nagłówka **zabezpieczenia** dla serwera SQL Database lub wystąpienia zarządzanego. Aby włączyć usługi AD dla wszystkich baz danych na serwerze bazy danych lub wystąpieniu zarządzanym, kliknij pozycję **Włącz zaawansowane zabezpieczenia danych na serwerze**.

> [!NOTE]
> Konto magazynu jest automatycznie tworzone i konfigurowane do przechowywania wyników skanowania **oceny luk w zabezpieczeniach** . Jeśli już włączono usługi AD dla innego serwera w tej samej grupie zasobów i regionie, używane jest istniejące konto magazynu.

![Włącz reklamy](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> Koszt reklam jest wyrównany przy użyciu cen warstwy Standardowa Azure Security Center na węzeł, gdzie węzeł jest całym serwerem SQL Database lub wystąpieniem zarządzanym. W ten sposób płacisz tylko raz na ochronę wszystkich baz danych na serwerze bazy danych lub wystąpieniu zarządzanym za pomocą reklam. Możesz bezpłatnie wypróbować reklamy, korzystając z bezpłatnej wersji próbnej.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Rozpocznij klasyfikowanie danych, śledzenie luk w zabezpieczeniach i badanie alertów dotyczących zagrożeń

Kliknij kartę **& odnajdywania danych** , aby zobaczyć zalecane poufne kolumny do klasyfikowania i klasyfikowania danych za pomocą etykiet trwałych. Kliknij kartę **Ocena luk w zabezpieczeniach** , aby wyświetlić i zarządzać skanowaniem i raportami luk w zabezpieczeniach oraz śledzić schemacie zabezpieczeń. Jeśli alerty zabezpieczeń zostały odebrane, kliknij kartę **Zaawansowana ochrona przed zagrożeniami** , aby wyświetlić szczegóły alertów i wyświetlić skonsolidowany raport dotyczący wszystkich alertów w ramach subskrypcji platformy Azure za pośrednictwem strony alerty zabezpieczeń Azure Security Center.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Zarządzanie ustawieniami reklamy na serwerze SQL Database lub zarządzanym wystąpieniem

Aby wyświetlić ustawienia reklam i zarządzać nimi, przejdź do pozycji **Zaawansowane zabezpieczenia danych** w obszarze nagłówek **zabezpieczenia** dla serwera SQL Database lub wystąpienia zarządzanego. Na tej stronie można włączać lub wyłączać reklamy oraz modyfikować ustawienia oceny luk w zabezpieczeniach i zaawansowanej ochrony przed zagrożeniami dla całego serwera SQL Database lub wystąpienia zarządzanego.

![Ustawienia serwera](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Zarządzanie ustawieniami usługi AD dla bazy danych SQL

Aby zastąpić ustawienia reklamy dla konkretnej bazy danych, zaznacz pole wyboru **Włącz zaawansowane zabezpieczenia danych na poziomie bazy danych** . Tej opcji należy używać tylko w przypadku, gdy istnieje szczególny wymóg otrzymywania osobnych alertów dotyczących zaawansowanej ochrony przed zagrożeniami lub oceny luk w zabezpieczeniach dla pojedynczej bazy danych zamiast alertów i odebranych wyników dla wszystkich baz danych na serwerze bazy danych lub wystąpieniu zarządzanym.

Po zaznaczeniu pola wyboru można skonfigurować odpowiednie ustawienia dla tej bazy danych.
 
![Ustawienia bazy danych i zaawansowanej ochrony przed zagrożeniami](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Zaawansowane ustawienia zabezpieczeń danych dla serwera bazy danych lub wystąpienia zarządzanego można również uzyskać z okienka baza danych usługi AD. W okienku główne reklamy kliknij pozycję **Ustawienia** , a następnie kliknij pozycję **Wyświetl zaawansowane ustawienia serwera zabezpieczeń danych**. 

![Ustawienia bazy danych](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Następne kroki 

- Dowiedz się więcej na temat [klasyfikacji & odnajdywania danych](sql-database-data-discovery-and-classification.md) 
- Dowiedz się więcej na temat [oceny luk w zabezpieczeniach](sql-vulnerability-assessment.md) 
- Dowiedz się więcej na temat [zaawansowanej ochrony przed zagrożeniami](sql-database-threat-detection.md)
- Dowiedz się więcej o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
