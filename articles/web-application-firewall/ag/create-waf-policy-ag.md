---
title: Utwórz zasady zapory aplikacji sieci Web (WAF) dla Application Gateway
description: Dowiedz się, jak utworzyć zasady zapory aplikacji sieci Web dla Application Gateway.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 26078c3757e42c3e290a5f4122461b287582fb80
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96518825"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Tworzenie zasad zapory aplikacji sieci Web dla Application Gateway

Skojarzenie zasad WAF z odbiornikami umożliwia ochronę wielu lokacji za jednym WAF, aby były chronione przez różne zasady. Na przykład jeśli w WAF znajduje się pięć witryn, można mieć pięć oddzielnych zasad WAF (jeden dla każdego odbiornika), aby dostosować wykluczenia, reguły niestandardowe i zarządzane zestaw reguł dla jednej lokacji bez wpływu na pozostałe cztery. Jeśli do wszystkich lokacji mają być stosowane pojedyncze zasady, można po prostu skojarzyć zasady z Application Gateway zamiast poszczególnych odbiorników, aby zastosować je globalnie. Zasady mogą być również stosowane do reguły routingu opartej na ścieżce. 

Możesz wprowadzić dowolną liczbę zasad. Po utworzeniu zasad, muszą one być skojarzone z Application Gateway, aby można je było uwzględnić, ale mogą być skojarzone z dowolną kombinacją bram aplikacji i odbiorników. 

Jeśli do Application Gateway zastosowano zasady, a następnie zastosujesz inne zasady do odbiornika w tym Application Gateway, zasady odbiornika zostaną zastosowane, ale tylko dla odbiorników, do których są przypisane. Zasady Application Gateway nadal dotyczą wszystkich innych odbiorników, do których nie są przypisane określone zasady. 

   > [!NOTE]
   > Gdy zasady zapory są skojarzone z usługą WAF, zawsze muszą istnieć zasady skojarzone z tym WAF. Te zasady mogą zostać zastąpione, ale usunięcie skojarzenia zasad z WAF całkowicie nie jest obsługiwane. 

Wszystkie nowe ustawienia WAF zapory aplikacji sieci Web (niestandardowe reguły, zarządzane konfiguracje rulset, wykluczenia itp.) na żywo w ramach zasad WAF. Jeśli masz istniejące WAF, te ustawienia mogą nadal istnieć w konfiguracji WAF. Aby zapoznać się z instrukcjami dotyczącymi sposobu przenoszenia do nowych zasad WAFymi, zobacz sekcję [Migrowanie konfiguracji WAF do zasad WAF](#migrate) w dalszej części tego artykułu. 

## <a name="create-a-policy"></a>Tworzenie zasad

Najpierw Utwórz podstawowe zasady WAF z zarządzanym domyślnym zestawem reguł (DRS) przy użyciu Azure Portal.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**. Wyszukaj pozycję **WAF**, wybierz pozycję **Zapora aplikacji sieci Web**, a następnie wybierz pozycję **Utwórz**.
2. Na stronie **Tworzenie zasad WAF** , **podstawowe** karty, wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Zasady dla     |WAF regionalny (Application Gateway)|
   |Subskrypcja     |Wybierz nazwę subskrypcji|
   |Grupa zasobów     |Wybierz grupę zasobów|
   |Nazwa zasady     |Wpisz unikatową nazwę zasad WAF.|
3. Na karcie **skojarzenie** wprowadź jedno z następujących ustawień, a następnie wybierz pozycję **Dodaj**:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Skojarz Application Gateway     |Wybierz nazwę profilu Application Gateway.|
   |Kojarzenie odbiorników     |Wybierz nazwę odbiornika Application Gateway, a następnie wybierz pozycję **Dodaj**.|

   > [!NOTE]
   > W przypadku przypisywania zasad do Application Gateway (lub odbiornika), które mają już zasady, pierwotne zasady zostaną zastąpione i zastąpione przez nowe zasady.
4. Wybierz pozycję **Przeglądanie i tworzenie**, a następnie wybierz pozycję **Utwórz**.

   ![Podstawy zasad WAF](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Konfigurowanie reguł WAF (opcjonalnie)

W przypadku tworzenia zasad WAF domyślnie jest on w trybie *wykrywania* . W trybie wykrywania WAF nie blokuje żadnych żądań. Zamiast tego pasujące reguły WAF są rejestrowane w dziennikach WAF. Aby wyświetlić WAF w działaniu, można zmienić ustawienia trybu na *zapobieganie*. W trybie zapobiegania, pasujące reguły zdefiniowane w wybranym w zestawie reguł programu KSR są blokowane i/lub rejestrowane w dziennikach WAF.

## <a name="managed-rules"></a>Reguły zarządzane

Reguły OWASP zarządzane przez platformę Azure są domyślnie włączone. Aby wyłączyć pojedynczą regułę w grupie reguł, rozwiń reguły w tej grupie reguł, zaznacz pole wyboru przed numerem reguły, a następnie na karcie powyżej wybierz pozycję **Wyłącz** .

[![Zarządzane reguły ](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Reguły niestandardowe

Aby utworzyć regułę niestandardową, wybierz pozycję **Dodaj regułę** niestandardową na karcie **reguły niestandardowe** . Spowoduje to otwarcie strony Konfiguracja reguły niestandardowej. Poniższy zrzut ekranu przedstawia przykład niestandardowej reguły skonfigurowanej do blokowania żądania, jeśli ciąg zapytania zawiera tekst *blockme*.

[![Edytuj regułę ](../media/create-waf-policy-ag/edit-custom-rule.png) niestandardową](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Migrowanie konfiguracji WAF do zasad WAF

Jeśli masz już istniejącą WAF, być może zauważono pewne zmiany w portalu. Najpierw należy określić rodzaj zasad, które zostały włączone w WAF. Istnieją trzy potencjalne Stany:

- Brak zasad WAF
- Zasady dotyczące tylko reguł niestandardowych
- Zasady WAF

Możesz określić, który stan WAF, przeglądając go w portalu. Jeśli ustawienia WAF są widoczne i można je zmienić z poziomu widoku Application Gateway, WAF jest w stanie 1.

[![Konfiguracja ](../media/create-waf-policy-ag/waf-configure.png) WAF](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

W przypadku wybrania **zapory aplikacji sieci Web** , która zawiera skojarzone zasady, WAF jest w stanie 2 lub 3. Po przejściu do zasad, jeśli są wyświetlane **tylko** reguły niestandardowe i skojarzone bramy aplikacji, są to zasady dotyczące tylko reguł niestandardowych.

![Reguły niestandardowe zapory aplikacji internetowej](../media/create-waf-policy-ag/waf-custom-rules.png)

Jeśli są również wyświetlane ustawienia zasad i reguły zarządzane, są to pełne zasady zapory aplikacji sieci Web. 

![Ustawienia zasad WAF](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrowanie do zasad WAF

Jeśli masz zasady niestandardowe tylko dla zasad WAFymi, możesz chcieć przejść do nowych zasad WAF. W przyszłości zasady zapory będą obsługiwały ustawienia zasad WAFymi, zarządzane zestawu reguł, wykluczenia i wyłączone grupy reguł. Zasadniczo wszystkie konfiguracje WAF, które były wcześniej wykonywane w ramach Application Gateway, są teraz realizowane za pomocą zasad WAF. 

Zmiany tylko do reguły niestandardowej WAF zasady są wyłączone. Aby edytować wszystkie ustawienia WAF, takie jak wyłączanie reguł, Dodawanie wykluczeń itp., należy przeprowadzić migrację do nowego zasobu zasad zapory najwyższego poziomu.

Aby to zrobić, Utwórz *zasady zapory aplikacji sieci Web* i skojarz je z wybranymi Application Gateway i odbiornikami. Te nowe zasady muszą być dokładnie takie same, jak Bieżąca konfiguracja WAF, co oznacza, że każda reguła niestandardowa, wykluczanie, reguła wyłączona itp. musi zostać skopiowana do nowo tworzonych zasad. Po skojarzeniu zasad z Application Gateway można nadal wprowadzać zmiany w regułach i ustawieniach WAF. Można to również zrobić przy użyciu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [kojarzenie zasad WAF z istniejącym Application Gateway](associate-waf-policy-existing-gateway.md).

Opcjonalnie możesz użyć skryptu migracji, aby przeprowadzić migrację do zasad WAFymi. Aby uzyskać więcej informacji, zobacz [Migrowanie zasad zapory aplikacji sieci Web przy użyciu Azure PowerShell](migrate-policy.md).

## <a name="force-mode"></a>Tryb wymuszania

Jeśli nie chcesz kopiować wszystkiego do zasad, które są dokładnie takie same jak w przypadku bieżącej konfiguracji, możesz ustawić WAF w tryb "Wymuś". Uruchom następujący kod Azure PowerShell, a WAF będzie w trybie wymuszania. Następnie można skojarzyć wszystkie zasady WAF z WAF, nawet jeśli nie ma dokładnie tych samych ustawień co konfiguracja. 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -Name <your Application Gateway name> -ResourceGroupName <your Resource Group name>
$appgw.ForceFirewallPolicyAssociation = $true
```

Następnie procees się z procedurami kojarzenia zasad WAF z bramą aplikacji. Aby uzyskać więcej informacji, zobacz [kojarzenie zasad WAF z istniejącym Application Gateway.](associate-waf-policy-existing-gateway.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [grupach i regułach reguł KSR aplikacji sieci Web](application-gateway-crs-rulegroups-rules.md).
