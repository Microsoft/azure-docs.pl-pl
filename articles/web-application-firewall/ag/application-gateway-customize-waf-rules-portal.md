---
title: Dostosowywanie reguł przy użyciu portalu — Azure Web Application Firewall
description: Ten artykuł zawiera informacje na temat dostosowywania reguł Web Application Firewall w Application Gateway za pomocą Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 04/21/2021
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 0ab122d178e5390a53e5a3a39f1b7763b298dc6d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878330"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Dostosowywanie Web Application Firewall reguł przy użyciu Azure Portal

Aplikacja Azure Application Gateway Web Application Firewall (WAF) zapewnia ochronę aplikacji internetowych. Te zabezpieczenia są zapewniane przez Open Web Application Security Project (OWASP) Core Rule Set (CRS). Niektóre reguły mogą powodować wyniki fałszywie dodatnie i blokować rzeczywisty ruch. Z tego powodu Application Gateway możliwość dostosowywania grup reguł i reguł. Aby uzyskać więcej informacji na temat określonych grup reguł i reguł, zobacz [List of Web Application Firewall CRS rule groups and rules (Lista](application-gateway-crs-rulegroups-rules.md)grup i reguł crs).

>[!NOTE]
> Jeśli brama aplikacji nie korzysta z warstwy aplikacji internetowej, opcja uaktualnienia bramy aplikacji do warstwy WAF jest wyświetlana w okienku po prawej stronie. 

:::image type="content" source="../media/application-gateway-customize-waf-rules-portal/1.png" alt-text="Włączanie funkcji WAF"::: 

## <a name="view-rule-groups-and-rules"></a>Wyświetlanie grup reguł i reguł

**Aby wyświetlić grupy reguł i reguły**
1. Przejdź do bramy aplikacji, a następnie wybierz **pozycję Zapora aplikacji internetowej.**  
2. Wybierz zasady **WAF.**
2. Wybierz **pozycję Reguły zarządzane.**

   Ten widok przedstawia tabelę na stronie wszystkich grup reguł dostarczonych z wybranym zestawem reguł. Wszystkie pola wyboru reguły są zaznaczone.

## <a name="disable-rule-groups-and-rules"></a>Wyłączanie grup reguł i reguł

> [!IMPORTANT]
> Podczas wyłączania grup reguł lub reguł należy zachować ostrożność. Może to spowodować zwiększone zagrożenia bezpieczeństwa.

**Aby wyłączyć grupy reguł lub określone reguły**

   1. Wyszukaj reguły lub grupy reguł, które chcesz wyłączyć.
   2. Zaznacz pola wyboru dla reguł, które chcesz wyłączyć. 
   3. Wybierz akcję w górnej części strony (włącz/wyłącz) dla wybranych reguł.
   2. Wybierz pozycję **Zapisz**.
    :::image type="content" source="../media/application-gateway-customize-waf-rules-portal/figure3.png" alt-text="Zapisywanie wyłączonych reguł"::: 

## <a name="mandatory-rules"></a>Reguły obowiązkowe

Na poniższej liście znajdują się warunki, które powodują zablokowanie żądania przez WAF w trybie zapobiegania. W trybie wykrywania są rejestrowane jako wyjątki.

Nie można ich skonfigurować ani wyłączyć:

* Niepowodzenie analizy treści żądania powoduje zablokowanie żądania, chyba że inspekcja treści jest wyłączona (dane XML, JSON, formularz)
* Długość danych treści żądania (bez plików) jest większa niż skonfigurowany limit
* Treść żądania (w tym pliki) jest większa niż limit
* Wystąpił wewnętrzny błąd w a aparatze aplikacji sieci Szkieletowej

Specyficzny dla crs 3.x:

* Wynik anomalii ruchu przychodzącego przekroczył próg

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu wyłączonych reguł możesz dowiedzieć się, jak wyświetlać dzienniki usługi WAF. Aby uzyskać więcej informacji, zobacz [Application Gateway diagnostyki](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).