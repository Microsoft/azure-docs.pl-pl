---
title: Konfigurowanie ustawień bazy danych w usłudze Azure API for FHIR
description: W tym artykule opisano sposób konfigurowania ustawień bazy danych w usłudze Azure API for FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: adc6fdf144927d10f811a00aa33f244cfdc25042
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "84871764"
---
# <a name="configure-database-settings"></a>Konfigurowanie ustawień bazy danych 

Interfejs API platformy Azure dla usługi FHIR używa bazy danych do przechowywania danych. Wydajność podstawowej bazy danych zależy od liczby jednostek żądań wybranych podczas aprowizacji usług lub w ustawieniach bazy danych po zainicjowaniu obsługi administracyjnej usługi.

Usługa Azure API for FHIR zażyczy koncepcji jednostek ru z Cosmos DB (zobacz [jednostki żądań w Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/request-units)) podczas ustawiania wydajności źródłowej bazy danych. 

Obsługa przepływności musi być zapewniona, aby zapewnić, że dla bazy danych są dostępne wystarczające zasoby systemowe. Ile jednostek ru potrzebujesz dla aplikacji, zależy od wykonywanych operacji. Operacje mogą być różne od prostych operacji odczytu i zapisu do bardziej złożonych zapytań. 

> [!NOTE]
> Ponieważ różne operacje zużywają różną liczbę RU, zwracamy rzeczywistą liczbę jednostek ru zużytych w każdym wywołaniu interfejsu API w nagłówku odpowiedzi. W ten sposób można profilować liczbę jednostek ru używanych przez aplikację.

## <a name="update-throughput"></a>Przepustowość aktualizacji
Aby zmienić to ustawienie w Azure Portal, przejdź do interfejsu API platformy Azure dla usługi FHIR i Otwórz blok bazy danych. Następnie zmień zainicjowaną przepływność na żądaną wartość w zależności od potrzeb związanych z wydajnością. Można zmienić wartość maksymalnie 10 000 RU/s. Jeśli potrzebujesz wyższej wartości, skontaktuj się z pomocą techniczną platformy Azure.

> [!NOTE] 
> Wyższa wartość oznacza wyższy interfejs API platformy Azure do FHIR przepływności i wyższe koszty usługi.

![Cosmos DB konfiguracji](media/database/database-settings.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób aktualizowania jednostek ru dla interfejsu API platformy Azure dla usługi FHIR. Następnie wdróż w pełni zarządzany interfejs API platformy Azure dla usługi FHIR:
 
>[!div class="nextstepaction"]
>[Wdrażanie usługi Azure API for FHIR](fhir-paas-portal-quickstart.md)