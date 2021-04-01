---
title: Ponowne zapisywanie adresu URL i ciągu zapytania za pomocą usługi Azure Application Gateway — Azure Portal
description: Dowiedz się, jak za pomocą Azure Portal skonfigurować Application Gateway platformy Azure, aby ponownie napisać adres URL i ciąg zapytania
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 7/16/2020
ms.author: surmb
ms.openlocfilehash: ec58c6f97efdbcb91071bcea98bbbc614833246d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215777"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal-preview"></a>Ponowne zapisywanie adresu URL za pomocą usługi Azure Application Gateway — Azure Portal (wersja zapoznawcza)

W tym artykule opisano sposób użycia Azure Portal w celu skonfigurowania wystąpienia [jednostki SKU Application Gateway v2](application-gateway-autoscaling-zone-redundant.md) w celu ponownego zapisania adresu URL.

>[!NOTE]
> Funkcja ponownego zapisywania adresu URL jest w wersji zapoznawczej i jest dostępna tylko dla Standard_v2 i WAF_v2 jednostki SKU Application Gateway. Nie jest to zalecane do użycia w środowisku produkcyjnym. Aby dowiedzieć się więcej na temat wersji zapoznawczych, zobacz [warunki użytkowania tutaj](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby wykonać kroki opisane w tym artykule, musisz mieć wystąpienie jednostki SKU Application Gateway v2. Ponowne zapisywanie adresu URL nie jest obsługiwane w jednostce SKU v1. Jeśli nie masz jednostki SKU w wersji 2, przed rozpoczęciem Utwórz wystąpienie [jednostki sku Application Gateway v2](tutorial-autoscale-ps.md) .

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.

## <a name="configure-url-rewrite"></a>Konfigurowanie ponownego zapisywania adresów URL

W poniższym przykładzie, gdy adres URL żądania zawiera */article*, Ścieżka adresu URL i ciąg zapytania adresu URL są ponownie zapisywane

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. Wybierz pozycję **wszystkie zasoby**, a następnie wybierz bramę aplikacji.

2. Wybierz pozycję **Zapisz ponownie** w lewym okienku.

3. Wybierz pozycję **Zapisz ponownie zestaw**:

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="Dodaj zestaw do ponownego zapisu":::

4. Podaj nazwę zestawu do ponownego zapisu i skojarz go z regułą routingu:

    a. Wprowadź nazwę zestawu do ponownego zapisu w polu **Nazwa** .
    
    b. Wybierz co najmniej jedną regułę podaną na liście **skojarzone reguły routingu** . Służy do kojarzenia konfiguracji ponownego zapisywania z odbiornikiem źródłowym za pośrednictwem reguły routingu. Można wybrać tylko te reguły routingu, które nie zostały skojarzone z innymi zestawami ponownego zapisywania. Reguły, które zostały już skojarzone z innymi zestawami ponownego zapisywania, są wyszarzone.
    
    c. Wybierz pozycję **Next** (Dalej).
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="Skojarz z regułą":::

5. Utwórz regułę ponownego zapisywania:

    a. Wybierz pozycję **Dodaj regułę ponownego zapisywania**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="Zrzut ekranu, który wyróżnia regułę ponownego zapisywania.":::
    
    b. Wprowadź nazwę reguły ponownego zapisu w polu **Nazwa reguły ponownego zapisywania** . Wprowadź liczbę w polu **sekwencja reguł** .

6. W tym przykładzie będziemy ponownie pisać ścieżkę URL i ciąg zapytania adresu URL tylko wtedy, gdy ścieżka zawiera */article*. W tym celu Dodaj warunek, aby sprawdzić, czy ścieżka URL zawiera */article*

    a. Wybierz pozycję **Dodaj warunek** , a następnie zaznacz pole zawierające instrukcje **if** , aby je rozwinąć.
    
    b. Ponieważ w tym przykładzie chcemy sprawdzić wzorzec */article* w ścieżce URL, na liście **Typ zmiennej do sprawdzenia** wybierz pozycję **Zmienna serwera**.
    
    c. Na liście **Zmienna serwera** wybierz uri_path
    
    d. W obszarze **uwzględnianie wielkości** liter wybierz pozycję **nie**.
    
    e. Na liście **operator** wybierz pozycję **równe (=)**.
    
    f. Wprowadź wzorzec wyrażenia regularnego. W tym przykładzie użyjemy wzorca `.*article/(.*)/(.*)`
    
      () służy do przechwytywania podciągu w celu późniejszego użycia w redagowaniu wyrażenia służącego do ponownego zapisywania ścieżki URL. Aby uzyskać więcej informacji, zobacz [tutaj](rewrite-http-headers-url.md#capturing).

    przykład Wybierz przycisk **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="Warunek":::

 

7. Dodaj akcję, aby ponownie napisać adres URL i ścieżkę URL

   a. Na liście **Typ ponownego zapisu** wybierz pozycję **adres URL**.

   b. Na liście **Typ akcji** wybierz pozycję **Ustaw**.

   c. W obszarze **składniki** wybierz pozycję **Ścieżka adresu URL i ciąg zapytania adresu URL** .

   d. W polu **adres URL** wprowadź nową wartość ścieżki. W tym przykładzie będziemy używać **/article.aspx** 

   e. W polu **wartość ciągu zapytania adresu URL** wprowadź nową wartość ciągu zapytania adresu URL. W tym przykładzie będziemy używać **identyfikatora = {var_uri_path_1} &tytułu = {var_uri_path_2}**
    
    `{var_uri_path_1}` i `{var_uri_path_1}` są używane do pobierania podciągów przechwytywanych podczas oceniania warunku w tym wyrażeniu `.*article/(.*)/(.*)`
    
   f. Wybierz przycisk **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="Akcja":::

8. Kliknij przycisk **Utwórz** , aby utworzyć zestaw do ponownego zapisywania.

9. Sprawdź, czy nowy zestaw do ponownego zapisu pojawia się na liście zestawów do ponownego zapisu

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="Dodaj regułę ponownego zapisu":::

## <a name="verify-url-rewrite-through-access-logs"></a>Weryfikowanie zapisywania adresów URL za poorednictwem dzienników dostępu

Zwróć uwagę na następujące pola w dziennikach dostępu, aby sprawdzić, czy nastąpiło ponowne zapis adresu URL zgodnie z oczekiwaniami.

* **originalRequestUriWithArgs**: to pole zawiera pierwotny adres URL żądania
* **RequestUri**: to pole zawiera adres URL po operacji ponownego zapisu na Application Gateway

Aby uzyskać więcej informacji na temat wszystkich pól w dziennikach dostępu, zobacz [tutaj](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku).

##  <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat sposobu konfigurowania ponownego zapisu dla niektórych typowych przypadków użycia, zobacz [typowe scenariusze ponownego zapisywania](rewrite-http-headers.md).
