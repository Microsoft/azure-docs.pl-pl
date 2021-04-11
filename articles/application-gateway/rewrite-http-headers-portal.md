---
title: Ponowne zapisywanie nagłówków żądań i odpowiedzi HTTP w portalu — Application Gateway platformy Azure
description: Dowiedz się, jak za pomocą Azure Portal skonfigurować Application Gateway platformy Azure do ponownego zapisywania nagłówków HTTP w żądaniach i odpowiedziach przesyłanych przez bramę
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a77476086d6100cbaf49d54791972940cca0644f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708938"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Ponowne zapisywanie nagłówków żądań i odpowiedzi HTTP przy użyciu usługi Azure Application Gateway — Azure Portal

W tym artykule opisano sposób użycia Azure Portal w celu skonfigurowania wystąpienia [jednostki SKU Application Gateway v2](./application-gateway-autoscaling-zone-redundant.md) w celu ponownego zapisania nagłówków HTTP w żądaniach i odpowiedziach.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby wykonać kroki opisane w tym artykule, musisz mieć wystąpienie jednostki SKU Application Gateway v2. Ponowne zapisywanie nagłówków nie jest obsługiwane w jednostce SKU v1. Jeśli nie masz jednostki SKU w wersji 2, przed rozpoczęciem Utwórz wystąpienie [jednostki sku Application Gateway v2](./tutorial-autoscale-ps.md) .

## <a name="create-required-objects"></a>Tworzenie wymaganych obiektów

Aby skonfigurować ponowne zapisywanie nagłówka HTTP, należy wykonać te kroki.

1. Utwórz obiekty wymagane do ponownego zapisania nagłówka HTTP:

   - **Akcja ponownego zapisu**: służy do określania pól żądania i nagłówka żądania, które mają być ponownie zapisane, oraz do nowej wartości nagłówków. Możliwe jest skojarzenie jednego lub więcej warunków ponownego zapisu z akcją ponownego zapisu.

   - **Warunek ponownego zapisu**: opcjonalna konfiguracja. Warunki ponownego zapisu sprawdzają zawartość żądań i odpowiedzi HTTP (S). Akcja ponownego zapisu zostanie wykonana, jeśli żądanie HTTP (S) lub odpowiedź pasuje do warunku ponownego zapisu.

     Jeśli powiążesz więcej niż jeden warunek z akcją, Akcja występuje tylko wtedy, gdy wszystkie warunki są spełnione. Innymi słowy, operacja jest operacją logiczną i.

   - **Reguła ponownego zapisywania**: zawiera kilka kombinacji warunku akcji ponownego zapisu/ponownego zapisywania.

   - **Sekwencja reguł**: pomaga określić kolejność wykonywania reguł ponownego zapisywania. Ta konfiguracja jest przydatna, jeśli masz wiele reguł ponownego zapisywania w zestawie do wielokrotnego zapisu. Reguła ponownego zapisu, która ma niższą wartość sekwencji reguł, jest uruchamiana jako pierwsza. Jeśli ta sama wartość sekwencji reguł zostanie przypisana do dwóch reguł ponownego zapisywania, kolejność wykonywania nie jest deterministyczna.

   - **Zestaw do ponownego zapisu**: zawiera wiele reguł ponownego zapisywania, które zostaną skojarzone z regułą routingu żądania.

2. Dołącz zestaw ponownych zapisów do reguły routingu. Konfiguracja ponownego zapisywania jest dołączona do odbiornika źródłowego za pośrednictwem reguły routingu. W przypadku korzystania z podstawowej reguły routingu, konfiguracja ponownego zapisywania nagłówka jest skojarzona z odbiornikiem źródłowym i jest ponownym zapisem nagłówka globalnego. W przypadku korzystania z reguły routingu opartej na ścieżce, konfiguracja ponownego zapisywania nagłówka jest definiowana na mapie ścieżki URL. W takim przypadku ma zastosowanie tylko do obszaru określonej ścieżki w lokacji.

Można utworzyć wiele zestawów wielokrotnego zapisu nagłówka HTTP i zastosować każdy ponowny zapis ustawiony dla wielu odbiorników. Można jednak zastosować tylko jeden wielokrotny zapis do określonego odbiornika.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.

## <a name="configure-header-rewrite"></a>Konfigurowanie ponownego zapisywania nagłówka

W tym przykładzie zmodyfikujemy adres URL przekierowania przez ponowne zapisanie nagłówka lokalizacji w odpowiedzi HTTP wysyłanej przez aplikację zaplecza.

1. Wybierz pozycję **wszystkie zasoby**, a następnie wybierz bramę aplikacji.

2. Wybierz pozycję **Zapisz ponownie** w lewym okienku.

3. Wybierz pozycję **Zapisz ponownie zestaw**:

   ![Dodaj zestaw do ponownego zapisu](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Podaj nazwę zestawu do ponownego zapisu i skojarz go z regułą routingu:

   - Wprowadź nazwę zestawu do ponownego zapisu w polu **Nazwa** .
   - Wybierz co najmniej jedną regułę podaną na liście **skojarzone reguły routingu** . Można wybrać tylko reguły, które nie zostały skojarzone z innymi zestawami ponownego zapisywania. Reguły, które zostały już skojarzone z innymi zestawami ponownego zapisywania, są wygaszone.
   - Wybierz opcję **Dalej**.
   
     ![Dodaj nazwę i skojarzenie](media/rewrite-http-headers-portal/name-and-association.png)

5. Utwórz regułę ponownego zapisywania:

   - Wybierz pozycję **Dodaj regułę ponownego zapisywania**.

     ![Dodaj regułę ponownego zapisu](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Wprowadź nazwę reguły ponownego zapisu w polu **Nazwa reguły ponownego zapisywania** . Wprowadź liczbę w polu **sekwencja reguł** .

     ![Dodaj nazwę reguły ponownego zapisu](media/rewrite-http-headers-portal/rule-name.png)

6. W tym przykładzie zapiszemy nagłówek lokalizacji tylko wtedy, gdy zawiera odwołanie do azurewebsites.net. W tym celu Dodaj warunek, aby sprawdzić, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.net:

   - Wybierz pozycję **Dodaj warunek** , a następnie zaznacz pole zawierające instrukcje **if** , aby je rozwinąć.

     ![Dodawanie warunku](media/rewrite-http-headers-portal/add-condition.png)

   - Na liście **Typ zmiennej do sprawdzenia** wybierz pozycję **nagłówek HTTP**.

   - Na liście **Typ nagłówka** wybierz pozycję **odpowiedź**.

   - Ponieważ w tym przykładzie oceniamy nagłówek lokalizacji, który jest wspólnym nagłówkiem, w obszarze **Nazwa nagłówka** wybierz pozycję **wspólny nagłówek** .

   - Na liście **wspólny nagłówek** wybierz pozycję **Lokalizacja**.

   - W obszarze **uwzględnianie wielkości** liter wybierz pozycję **nie**.

   - Na liście **operator** wybierz pozycję **równe (=)**.

   - Wprowadź wzorzec wyrażenia regularnego. W tym przykładzie użyjemy wzorca `(https?)://.*azurewebsites.net(.*)$` .

   - Wybierz przycisk **OK**.

     ![Konfigurowanie warunku if](media/rewrite-http-headers-portal/condition.png)

7. Dodaj akcję w celu ponownego zapisania nagłówka lokalizacji:

   - Na liście **Typ akcji** wybierz pozycję **Ustaw**.

   - Na liście **Typ nagłówka** wybierz pozycję **odpowiedź**.

   - W obszarze **Nazwa nagłówka** wybierz pozycję **wspólny nagłówek**.

   - Na liście **wspólny nagłówek** wybierz pozycję **Lokalizacja**.

   - Wprowadź wartość nagłówka. W tym przykładzie użyjemy `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartości nagłówka. Ta wartość spowoduje zamienienie *azurewebsites.NET* z *contoso.com* w nagłówku lokalizacji.

   - Wybierz przycisk **OK**.

     ![Dodawanie akcji](media/rewrite-http-headers-portal/action.png)

8. Wybierz pozycję **Utwórz** , aby utworzyć zestaw do ponownego zapisywania:

   ![Wybieranie pozycji Utwórz](media/rewrite-http-headers-portal/create.png)

9. Zostanie otwarty widok ponowne zapisywanie zestawu. Sprawdź, czy utworzony zestaw do ponownego zapisywania znajduje się na liście zestawów do ponownego zapisu:

   ![Zapisz ponownie widok zestawu](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o konfigurowaniu niektórych typowych przypadków użycia, zobacz [typowe scenariusze ponownego zapisywania nagłówka](./rewrite-http-headers.md).