---
title: Ograniczenia dostępu Azure App Service
description: Dowiedz się, jak zabezpieczyć aplikację w Azure App Service, określając ograniczenia dostępu.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ce5882f2621dc5b8c48bcf5be6d4ea3a2f723bfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962965"
---
# <a name="azure-app-service-access-restrictions"></a>Ograniczenia dostępu Azure App Service

Ograniczenia dostępu umożliwiają definiowanie uporządkowanej listy dozwolonych/zablokowanych priorytetów, która kontroluje dostęp sieciowy do aplikacji. Lista może zawierać adresy IP lub podsieci Virtual Network platformy Azure. Jeśli istnieje co najmniej jeden wpis, na końcu listy jest niejawne "Odmów wszystkich".

Funkcja ograniczeń dostępu współpracuje ze wszystkimi App Service hostowanymi obciążeniami roboczymi, takimi jak: aplikacje sieci Web, aplikacje interfejsu API, aplikacje systemu Linux, aplikacje kontenera systemu Linux i funkcje.

Gdy żądanie zostanie wysłane do aplikacji, adres od jest oceniany pod kątem reguł adresów IP na liście ograniczeń dostępu. Jeśli adres od jest w podsieci skonfigurowanej z punktami końcowymi usługi do Microsoft. Web, podsieć źródłowa jest porównywana z regułami sieci wirtualnej na liście ograniczeń dostępu. Jeśli adres nie zezwala na dostęp na podstawie reguł na liście, usługa odpowie z kodem stanu [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) .

Funkcja ograniczeń dostępu jest implementowana w ramach ról frontonu App Service, które są nadrzędne w stosunku do hostów procesów roboczych, na których działa kod. W związku z tym ograniczenia dostępu są skutecznymi listami ACL sieci.

Możliwość ograniczenia dostępu do aplikacji sieci Web z usługi Azure Virtual Network (VNet) nazywa się [punktami końcowymi usługi][serviceendpoints]. Punkty końcowe usługi umożliwiają ograniczenie dostępu do usługi z obsługą wielu dzierżawców z wybranych podsieci. Musi być włączona zarówno po stronie sieci, jak i w usłudze, w której jest włączona. Nie działa w sposób ograniczający ruch do aplikacji hostowanych w App Service Environment. Jeśli jesteś w App Service Environment, możesz kontrolować dostęp do aplikacji przy użyciu reguł adresów IP.

![przepływ ograniczeń dostępu](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Dodawanie i edytowanie reguł ograniczeń dostępu w portalu ##

Aby dodać regułę ograniczeń dostępu do aplikacji, użyj menu, aby otworzyć **Network** > **ograniczenia dostępu** do sieci i kliknij przycisk **Konfiguruj ograniczenia dostępu**

![Opcje sieci App Service](media/app-service-ip-restrictions/access-restrictions.png)  

Z poziomu interfejsu użytkownika ograniczenia dostępu można przejrzeć listę reguł ograniczeń dostępu zdefiniowanych dla aplikacji.

![Wyświetl ograniczenia dostępu](media/app-service-ip-restrictions/access-restrictions-browse.png)

Na liście zostaną wyświetlone wszystkie bieżące ograniczenia, które znajdują się w aplikacji. Jeśli używasz ograniczenia sieci wirtualnej w aplikacji, w tabeli zostaną wyświetlone, jeśli dla usługi Microsoft. Web włączono punkty końcowe usług. Jeśli aplikacja nie ma zdefiniowanych ograniczeń, aplikacja będzie dostępna z dowolnego miejsca.  

## <a name="adding-ip-address-rules"></a>Dodawanie reguł adresów IP

Możesz kliknąć pozycję **[+] Dodaj regułę** , aby dodać nową regułę ograniczenia dostępu. Po dodaniu reguły zaczną obowiązywać od razu. Reguły są wymuszane w kolejności priorytetu, rozpoczynając od najmniejszej liczby i przechodzenia. Istnieje niejawne odmowa, która obowiązuje po dodaniu nawet jednej reguły.

Podczas tworzenia reguły należy wybrać opcję Zezwalaj/Odmów, a także typ reguły. Należy również podać wartość priorytetu i ograniczyć dostęp do niego.  Opcjonalnie możesz dodać nazwę i opis do reguły.  

![Dodawanie reguły ograniczeń dostępu do adresu IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Aby ustawić regułę opartą na adresie IP, wybierz typ protokołu IPv4 lub IPv6. Należy określić notację adresu IP w notacji CIDR dla adresów IPv4 i IPv6. Aby określić dokładny adres, można użyć czegoś takiego jak 1.2.3.4/32, gdzie pierwsze cztery oktety reprezentują adres IP i/32 jest maską. Notacja CIDR protokołu IPv4 dla wszystkich adresów ma wartość 0.0.0.0/0. Aby dowiedzieć się więcej na temat notacji CIDR, można odczytać [Inter-Domain routingu bezklasowego](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Punkty końcowe usługi

Punkty końcowe usługi umożliwiają ograniczenie dostępu do wybranych podsieci sieci wirtualnej platformy Azure. Aby ograniczyć dostęp do określonej podsieci, należy utworzyć regułę ograniczeń z typem Virtual Network. Możesz wybrać subskrypcję, sieć wirtualną i podsieć, dla której chcesz zezwolić na dostęp lub odmówić dostępu. Jeśli punkty końcowe usługi nie zostały jeszcze włączone w usłudze Microsoft. Web dla wybranej podsieci, zostanie ona automatycznie włączona, chyba że zostanie zaznaczone pole z prośbą o to. Sytuacja, w której chcesz włączyć ją w aplikacji, ale nie podsieć jest w dużym stopniu związana z uprawnieniami do włączania punktów końcowych usługi w podsieci. Jeśli chcesz, aby inni użytkownicy mogli włączyć punkty końcowe usługi w podsieci, możesz zaznaczyć pole wyboru i skonfigurować aplikację pod kątem punktów końcowych usługi, jeśli zostanie ona włączona później w podsieci. 

![Dodaj regułę ograniczenia dostępu do sieci wirtualnej](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Punkty końcowe usługi nie mogą być używane do ograniczania dostępu do aplikacji, które działają w App Service Environment. Gdy aplikacja jest w App Service Environment, możesz kontrolować dostęp do aplikacji za pomocą reguł dostępu do adresów IP. 

Za pomocą punktów końcowych usługi można skonfigurować aplikację przy użyciu bram aplikacji lub innych urządzeń WAF. Możesz również skonfigurować wielowarstwowe aplikacje z bezpiecznymi punktami końcowymi. Aby uzyskać więcej informacji na temat niektórych możliwości, Odczytaj [funkcje sieciowe i App Service](networking-features.md) i [Application Gateway integrację z punktami końcowymi usługi](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> Punkty końcowe usługi są obecnie nieobsługiwane w przypadku aplikacji sieci Web, które używają Połączenie SSL z adresu IP wirtualnego adresu IP (VIP). 
>

## <a name="managing-access-restriction-rules"></a>Zarządzanie regułami ograniczeń dostępu

Możesz kliknąć dowolny wiersz, aby edytować istniejącą regułę ograniczeń dostępu. Zmiany w kolejności priorytetu będą obowiązywać natychmiast.

![Edytowanie reguły ograniczeń dostępu](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Podczas edytowania reguły nie można zmienić typu między regułą adresu IP a regułą Virtual Network. 

![Edytowanie reguły ograniczeń dostępu](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Aby usunąć regułę, kliknij pozycję **...** w regule, a następnie kliknij przycisk **Usuń**.

![Usuń regułę ograniczenia dostępu](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Blokowanie pojedynczego adresu IP ##

Podczas dodawania pierwszej reguły ograniczeń adresów IP usługa doda jawną **odmowę wszystkie** reguły o priorytecie 2147483647. W przypadku jawnej reguły **Odmów cała** reguła zostanie wykonana i zablokują dostęp do dowolnych adresów IP, które nie są jawnie dozwolone przy użyciu reguły **zezwalania** .

W przypadku scenariusza, w którym użytkownicy chcą jawnie blokować pojedynczy adres IP lub blok adresów IP, ale zezwalają na dostęp wszystkich innych elementów, należy dodać jawną regułę **Zezwalaj** .

![Blokuj pojedynczy adres IP](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>Witryna SCM 

Oprócz możliwości kontrolowania dostępu do aplikacji można także ograniczyć dostęp do witryny SCM używanej przez aplikację. Witryna SCM jest punktem końcowym narzędzia Web Deploy, a także konsolą kudu. Można oddzielnie przypisać ograniczenia dostępu do witryny SCM z poziomu aplikacji lub użyć tego samego zestawu zarówno dla aplikacji, jak i dla witryny SCM. Po zaznaczeniu pola wyboru, aby mieć takie same ograniczenia jak aplikacja, wszystko jest puste. W przypadku zaznaczenia tego pola zostaną zastosowane wszystkie ustawienia wcześniej w witrynie SCM. 

![Wyświetl ograniczenia dostępu](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programistyczne manipulowanie regułami ograniczeń dostępu ##

[Interfejs wiersza polecenia platformy Azure](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) i [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) obsługują edytowanie ograniczeń dostępu. Przykład dodawania ograniczenia dostępu przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Przykład dodawania ograniczenia dostępu przy użyciu Azure PowerShell:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Wartości można również ustawić ręcznie przy użyciu operacji Put [interfejsu API REST platformy Azure](/rest/api/azure/) w konfiguracji aplikacji w Menedżer zasobów lub przy użyciu szablonu Azure Resource Manager. Na przykład można użyć resources.azure.com i edytować blok ipSecurityRestrictions, aby dodać wymagany kod JSON.

Lokalizacja tych informacji w Menedżer zasobów:

**Identyfikator subskrypcji**Management.Azure.com/subscriptions//resourceGroups/**grupy zasobów**/Providers/Microsoft.Web/Sites/**Nazwa aplikacji sieci Web**/config/Web? API-Version = 2018 r-02-01

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

## <a name="azure-functions-access-restrictions"></a>Ograniczenia dostępu Azure Functions

Ograniczenia dostępu są również dostępne dla aplikacji funkcji o takich samych funkcjach jak plany App Service. Włączenie ograniczeń dostępu spowoduje wyłączenie edytora kodu portalu dla wszelkich niedozwolonych adresów IP.

## <a name="next-steps"></a>Następne kroki
[Ograniczenia dostępu dla Azure Functions](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Application Gateway integrację z punktami końcowymi usługi](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md