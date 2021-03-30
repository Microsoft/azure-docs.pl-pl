---
title: Dostosowywanie reguł przy użyciu portalu — Zapora aplikacji sieci Web platformy Azure
description: Ten artykuł zawiera informacje dotyczące sposobu dostosowywania reguł zapory aplikacji sieci Web w Application Gateway z Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048381"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Dostosowywanie reguł zapory aplikacji sieci Web przy użyciu Azure Portal

Zapora aplikacji sieci Web Application Gateway Azure (WAF) zapewnia ochronę aplikacji sieci Web. Te zabezpieczenia są udostępniane przez zestaw reguł programu Open Web Application Security (OWASP) Core (KSR). Niektóre reguły mogą spowodować fałszywie dodatnie i blokować rzeczywisty ruch. Z tego powodu Application Gateway oferuje możliwość dostosowywania grup reguł i reguł. Aby uzyskać więcej informacji na temat określonych grup reguł i reguł, zobacz [Lista zasad i reguł reguł KSR aplikacji sieci Web](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Jeśli Brama aplikacji nie korzysta z warstwy WAF, opcja uaktualnienia bramy aplikacji do warstwy WAF zostanie wyświetlona w okienku po prawej stronie. 

![Włącz WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Wyświetlanie zasad i grup reguł

**Aby wyświetlić grupy i reguły reguł**
1. Przejdź do bramy aplikacji, a następnie wybierz pozycję **Zapora aplikacji sieci Web**.  
2. Wybierz **zasady WAF**.
2. Wybierz **reguły zarządzane**.

   Ten widok przedstawia tabelę na stronie wszystkich grup reguł dostarczonych z wybranym zestawem reguł. Zaznaczone są wszystkie pola wyboru reguły.

## <a name="disable-rule-groups-and-rules"></a>Wyłączanie grup reguł i reguł

> [!IMPORTANT]
> Należy zachować ostrożność podczas wyłączania reguł lub grup reguł. Może to spowodować zwiększenie zagrożeń bezpieczeństwa.

Po wyłączeniu reguł można wyłączyć całą grupę reguł lub określone reguły w jednej lub kilku grupach reguł. 

**Aby wyłączyć grupy reguł lub określone reguły**

   1. Wyszukaj reguły lub grupy reguł, które chcesz wyłączyć.
   2. Zaznacz pola wyboru dla reguł, które chcesz wyłączyć. 
   3. Wybierz akcję w górnej części strony (Włącz/Wyłącz) dla wybranych reguł.
   2. Wybierz pozycję **Zapisz**. 

![Zapisz zmiany][3]

## <a name="mandatory-rules"></a>Reguły obowiązkowe

Poniższa lista zawiera warunki, które powodują, że WAF zablokują żądanie w trybie zapobiegania. W trybie wykrywania są one rejestrowane jako wyjątki.

Nie można ich skonfigurować ani wyłączyć:

* Nie można przeanalizować treści żądania w wyniku zablokowania żądania, chyba że inspekcja treści jest wyłączona (XML, JSON, dane formularza)
* Długość danych treści żądania (bez plików) jest większa niż skonfigurowany limit
* Treść żądania (w tym pliki) jest większa niż limit
* Wystąpił błąd wewnętrzny w aparacie WAF

Specyficzne dla KSR 3. x:

* Próg limitu przychodzącego wyniku anomalii

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu wyłączonych reguł można dowiedzieć się, jak wyświetlić dzienniki WAF. Aby uzyskać więcej informacji, zobacz [Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
