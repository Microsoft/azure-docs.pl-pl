---
title: Ograniczenia dostępu Azure App Service
description: Dowiedz się, jak zabezpieczyć aplikację w Azure App Service przez skonfigurowanie ograniczeń dostępu.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e1549dda367105db34272eab8a90c1760dd5bb5c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010184"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Konfigurowanie ograniczeń dostępu Azure App Service

Konfigurując ograniczenia dostępu, można zdefiniować priorytetową listę dozwolonych/zablokowanych priorytetów, która kontroluje dostęp sieciowy do aplikacji. Lista może zawierać adresy IP lub podsieci Virtual Network platformy Azure. W przypadku co najmniej jednego wpisu na końcu listy istnieje niejawne *odmowa* .

Funkcja ograniczeń dostępu współpracuje ze wszystkimi obsługiwanymi Azure App Service obciążeniami. Obciążenia mogą obejmować aplikacje sieci Web, aplikacje interfejsu API, aplikacje systemu Linux, aplikacje kontenera systemu Linux i funkcje.

Gdy żądanie zostanie wysłane do aplikacji, adres od jest oceniany pod kątem reguł adresów IP na liście ograniczeń dostępu. Jeśli adres NADAWCy należy do podsieci, która jest skonfigurowana za pomocą punktów końcowych usługi do firmy Microsoft. Web, podsieć źródłowa jest porównywana z regułami sieci wirtualnej na liście ograniczeń dostępu. Jeśli adres nie zezwala na dostęp na podstawie reguł na liście, usługa odpowie z kodem stanu [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) .

Funkcja ograniczeń dostępu jest implementowana w App Service ról frontonu, które są nadrzędne dla hostów procesu roboczego, w których działa kod. W związku z tym ograniczenia dostępu są skutecznymi listami kontroli dostępu do sieci (ACL).

Możliwość ograniczenia dostępu do aplikacji sieci Web z sieci wirtualnej platformy Azure jest włączana przez [punkty końcowe usługi][serviceendpoints]. Za pomocą punktów końcowych usługi można ograniczyć dostęp do usługi wielodostępnej z wybranych podsieci. Nie działa w sposób ograniczający ruch do aplikacji hostowanych w App Service Environment. Jeśli jesteś w App Service Environment, możesz kontrolować dostęp do aplikacji, stosując reguły adresów IP.

> [!NOTE]
> Punkty końcowe usługi muszą być włączone zarówno po stronie sieci, jak i dla usługi platformy Azure, z którą są włączane. Aby uzyskać listę usług platformy Azure, które obsługują punkty końcowe usługi, zobacz [Virtual Network punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md).
>

![Diagram przepływu ograniczeń dostępu.](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>Dodawanie lub edytowanie reguł ograniczeń dostępu w portalu

Aby dodać regułę ograniczenia dostępu do aplikacji, wykonaj następujące czynności:

1. Zaloguj się w witrynie Azure Portal.

1. W okienku po lewej stronie wybierz pozycję **Sieć**.

1. W okienku **Sieć** w obszarze **ograniczenia dostępu** wybierz pozycję **Konfiguruj ograniczenia dostępu**.

   ![Zrzut ekranu przedstawiający okienko opcje sieci App Service w Azure Portal.](media/app-service-ip-restrictions/access-restrictions.png)  

1. Na stronie **ograniczenia dostępu** Przejrzyj listę reguł ograniczeń dostępu zdefiniowanych dla aplikacji.

   ![Zrzut ekranu strony ograniczenia dostępu w Azure Portal zawierający listę reguł ograniczeń dostępu zdefiniowanych dla wybranej aplikacji.](media/app-service-ip-restrictions/access-restrictions-browse.png)

   Na liście zostaną wyświetlone wszystkie bieżące ograniczenia, które są stosowane do aplikacji. Jeśli używasz ograniczenia sieci wirtualnej w aplikacji, tabela pokazuje, czy punkty końcowe usługi są włączone dla Microsoft. Web. Jeśli w aplikacji nie zdefiniowano żadnych ograniczeń, aplikacja jest dostępna z dowolnego miejsca.  

### <a name="add-an-access-restriction-rule"></a>Dodawanie reguły ograniczeń dostępu

Aby dodać regułę ograniczenia dostępu do aplikacji, w okienku **ograniczenia dostępu** wybierz pozycję **Dodaj regułę**. Po dodaniu reguły zaczyna obowiązywać natychmiast. 

Reguły są wymuszane w kolejności priorytetu, rozpoczynając od najniższej liczby w kolumnie **priorytet** . Niejawne *odmowa* jest stosowane po dodaniu nawet jednej reguły.

W okienku **Dodaj ograniczenie adresów IP** podczas tworzenia reguły wykonaj następujące czynności:

1. W obszarze **Akcja** wybierz opcję **Zezwalaj** lub **Odmów**.  

   ![Zrzut ekranu przedstawiający okienko "Dodawanie ograniczenia adresu IP".](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. Opcjonalnie wprowadź nazwę i opis reguły.  
1. Z listy rozwijanej **Typ** wybierz typ reguły.  
1. W polu **priorytet** wprowadź wartość priorytetu.  
1. Z listy rozwijanej **subskrypcja**, **Virtual Network** i **podsieć** wybierz, do czego chcesz ograniczyć dostęp.  

### <a name="set-an-ip-address-based-rule"></a>Ustawianie reguły opartej na adresie IP

Postępuj zgodnie z procedurą przedstawioną w poprzedniej sekcji, ale z następującą różnicą:
* W kroku 3 z listy rozwijanej **Typ** wybierz pozycję **IPv4** lub **IPv6**. 

Określ adres IP w notacji CIDR (Classless Inter-Domain Routing) dla adresów IPv4 i IPv6. Aby określić adres, można użyć czegoś takiego jak *1.2.3.4/32*, gdzie pierwsze cztery oktety reprezentują adres IP i */32* jest maską. Notacja CIDR protokołu IPv4 dla wszystkich adresów ma wartość 0.0.0.0/0. Aby dowiedzieć się więcej na temat notacji CIDR, zobacz [Inter-Domain Routing bezklasowy](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="use-service-endpoints"></a>Korzystanie z punktów końcowych usługi

Korzystając z punktów końcowych usługi, można ograniczyć dostęp do wybranych podsieci sieci wirtualnej platformy Azure. Aby ograniczyć dostęp do określonej podsieci, należy utworzyć regułę ograniczenia z typem **Virtual Network** . Następnie można wybrać subskrypcję, sieć wirtualną i podsieć, do której ma być dozwolony lub zablokowany dostęp. 

Jeśli punkty końcowe usługi nie są już włączone w usłudze Microsoft. Web dla wybranej podsieci, zostaną automatycznie włączone, chyba że zaznaczysz pole wyboru **Ignoruj brakujące punkty końcowe usługi sieci Web firmy Microsoft.** Scenariusz, w którym można włączyć punkty końcowe usługi w aplikacji, ale nie podsieć, zależy głównie od tego, czy masz uprawnienia do włączania ich w podsieci. 

Jeśli chcesz, aby ktoś inny włączył punkty końcowe usługi w podsieci, zaznacz pole wyboru **Ignoruj brakujące punkty końcowe usługi sieci Web firmy Microsoft** . Aplikacja zostanie skonfigurowana dla punktów końcowych usługi w przewidywaniu włączenia ich później w podsieci. 

![Zrzut ekranu okienka "Dodawanie ograniczenia adresu IP" z wybranym typem Virtual Network.](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Punktów końcowych usługi nie można używać do ograniczania dostępu do aplikacji, które działają w App Service Environment. Gdy aplikacja znajduje się w App Service Environment, możesz kontrolować dostęp do niej, stosując reguły dostępu do adresów IP. 

Za pomocą punktów końcowych usługi można skonfigurować aplikację przy użyciu bram aplikacji lub innych urządzeń zapory aplikacji sieci Web (WAF). Możesz również skonfigurować wielowarstwowe aplikacje z bezpiecznymi zapleczami. Aby uzyskać więcej informacji, zobacz [funkcje sieciowe i App Service](networking-features.md) i [Application Gateway integrację z punktami końcowymi usługi](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> - Punkty końcowe usługi nie są obecnie obsługiwane w przypadku aplikacji sieci Web, które używają wirtualnego adresu IP (VIP) protokołu IP SSL (SSL).
> - Obowiązuje limit 512 wierszy ograniczeń adresów IP lub punktów końcowych usługi. Jeśli potrzebujesz więcej niż 512 wierszy ograniczeń, zalecamy zainstalowanie samodzielnego produktu zabezpieczeń, takiego jak Azure front-drzwi, Azure App Gateway lub WAF.
>

## <a name="manage-access-restriction-rules"></a>Zarządzanie regułami ograniczeń dostępu

Można edytować lub usunąć istniejącą regułę ograniczenia dostępu.

### <a name="edit-a-rule"></a>Edytowanie reguły

1. Aby rozpocząć edytowanie istniejącej reguły ograniczeń dostępu, na stronie **ograniczenia dostępu** kliknij dwukrotnie regułę, którą chcesz edytować.

1. W okienku **Edytowanie ograniczeń adresów IP** wprowadź zmiany, a następnie wybierz pozycję **Aktualizuj regułę**. Zmiany są skuteczne od razu, w tym zmiany w kolejności priorytetów.

   ![Zrzut ekranu przedstawiający okienko "Edytuj ograniczenie adresów IP" w Azure Portal, pokazujący pola istniejącej reguły ograniczeń dostępu.](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

   > [!NOTE]
   > Podczas edytowania reguły nie można przełączać się między regułą adresu IP a regułą sieci wirtualnej. 

   ![Zrzut ekranu okienka "Edytuj ograniczenie adresów IP" w Azure Portal, w którym wyświetlane są ustawienia reguły sieci wirtualnej.](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

### <a name="delete-a-rule"></a>Usuwanie reguły

Aby usunąć regułę, na stronie **ograniczenia dostępu** wybierz wielokropek (**...**) obok reguły, którą chcesz usunąć, a następnie wybierz pozycję **Usuń**.

![Zrzut ekranu strony "ograniczenia dostępu" pokazujący wielokropek obok reguły ograniczenia dostępu do usunięcia.](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>Blokuj pojedynczy adres IP

Po dodaniu pierwszej reguły ograniczeń adresów IP usługa dodaje jawną regułę *Odmów wszystkie* z priorytetem 2147483647. W przypadku jawnej reguły *Odrzuć wszystkie* jest ostatnią regułą, która ma zostać wykonana, i blokuje dostęp do dowolnego adresu IP, który nie jest jawnie dozwolony przez regułę *zezwalania* .

W przypadku scenariusza, w którym chcesz jawnie zablokować pojedynczy adres IP lub blok adresów IP, ale zezwolić na dostęp do wszystkiego innego, Dodaj jawną regułę *Zezwalaj na wszystkie* .

![Zrzut ekranu strony "ograniczenia dostępu" w Azure Portal zawierający pojedynczy zablokowany adres IP.](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>Ograniczanie dostępu do witryny SCM 

Oprócz możliwości kontrolowania dostępu do aplikacji można ograniczyć dostęp do witryny SCM używanej przez aplikację. Lokacja SCM to zarówno punkt końcowy Web Deploy, jak i konsola kudu. Ograniczenia dostępu można przypisać do witryny SCM z poziomu aplikacji osobno lub użyć tego samego zestawu ograniczeń zarówno dla aplikacji, jak i dla witryny SCM. Po wybraniu tych **samych ograniczeń co \<app name>** pole wyboru wszystko jest puste. Jeśli wyczyścisz to pole wyboru, zostaną ponownie zastosowane ustawienia witryny SCM. 

![Zrzut ekranu strony "ograniczenia dostępu" w Azure Portal, informujący, że nie ustawiono żadnych ograniczeń dostępu dla witryny SCM lub aplikacji.](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>Zarządzanie regułami ograniczeń dostępu programowo

Ograniczenia dostępu można dodać programowo, wykonując jedną z następujących czynności: 

* Użyj [interfejsu wiersza polecenia platformy Azure](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true). Na przykład:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
  --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Użyj [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true). Na przykład:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```

Możesz również ustawić wartości ręcznie, wykonując jedną z następujących czynności:

* Użyj operacji Put [interfejsu API REST platformy Azure](/rest/api/azure/) w konfiguracji aplikacji w Azure Resource Manager. Lokalizacja tych informacji w Azure Resource Manager:

  **Identyfikator subskrypcji** Management.Azure.com/subscriptions//resourceGroups/**grupy zasobów**/Providers/Microsoft.Web/Sites/**Nazwa aplikacji sieci Web**/config/Web? API-Version = 2018 r-02-01

* Użyj szablonu ARM. Na przykład można użyć resources.azure.com i edytować blok ipSecurityRestrictions, aby dodać wymagany kod JSON.

  Składnia JSON dla poprzedniego przykładu:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```

## <a name="set-up-azure-functions-access-restrictions"></a>Konfigurowanie ograniczeń dostępu Azure Functions

Ograniczenia dostępu są również dostępne dla aplikacji funkcji o takich samych funkcjach jak plany App Service. Po włączeniu ograniczeń dostępu należy również wyłączyć Edytor kodu Azure Portal dla wszelkich niedozwolonych adresów IP.

## <a name="next-steps"></a>Następne kroki
[Ograniczenia dostępu dla Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Application Gateway integrację z punktami końcowymi usługi](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
