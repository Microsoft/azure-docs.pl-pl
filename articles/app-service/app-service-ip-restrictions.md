---
title: Ograniczenia dostępu usługi Azure App Service
description: Dowiedz się, jak zabezpieczyć aplikację w usłudze Azure App Service, określając ograniczenia dostępu.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 298555da2056bc4c16d4d7b16615604f9798b91b
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639268"
---
# <a name="azure-app-service-access-restrictions"></a>Ograniczenia dostępu usługi Azure App Service

Ograniczenia dostępu umożliwiają zdefiniowanie listy priorytetów uporządkowanych zezwalania/odmawiania, która steruje dostępem sieciowym do aplikacji. Lista może zawierać adresy IP lub podsieci sieci wirtualnej platformy Azure. Gdy istnieje jeden lub więcej wpisów, istnieje następnie niejawne "odmówić wszystkich", który istnieje na końcu listy.

Możliwości ograniczeń dostępu współpracuje ze wszystkimi obciążeniami pracy hostowane usługi App Service, w tym; aplikacje internetowe, aplikacje INTERFEJSU API, aplikacje dla systemu Linux, aplikacje kontenerów systemu Linux i funkcje.

Po nałożeniu żądania do aplikacji adres OD jest oceniany na podstawie reguł adresu IP na liście ograniczeń dostępu. Jeśli adres OD znajduje się w podsieci skonfigurowany z punktami końcowymi usługi do microsoft.Web, podsieć źródło jest porównywana z regułami sieci wirtualnej na liście ograniczeń dostępu. Jeśli adres nie jest dozwolony na podstawie reguł na liście, usługa odpowiada kodem stanu [HTTP 403.](https://en.wikipedia.org/wiki/HTTP_403)

Możliwości ograniczeń dostępu jest zaimplementowana w rolach front-end usługi App Service, które są nadrzędne hostów procesu roboczego, gdzie kod jest uruchamiany. W związku z tym ograniczenia dostępu są skutecznie sieci ACL.

Możliwość ograniczenia dostępu do aplikacji sieci web z sieci wirtualnej platformy Azure (VNet) jest nazywana [punktami końcowymi usługi.][serviceendpoints] Punkty końcowe usługi umożliwiają ograniczenie dostępu do usługi wielodostępne z wybranych podsieci. Musi być włączona zarówno po stronie sieci, jak i usługi, z którą jest włączona. Nie działa, aby ograniczyć ruch do aplikacji, które są hostowane w środowisku usługi aplikacji. Jeśli jesteś w środowisku usługi app service, możesz kontrolować dostęp do aplikacji za pomocą reguł adresu IP.

![przepływ ograniczeń dostępu](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Dodawanie i edytowanie reguł ograniczeń dostępu w portalu ##

Aby dodać regułę ograniczeń dostępu do aplikacji, użyj menu, aby otworzyć**ograniczenia dostępu** **do sieci**>i kliknij **pozycję Konfiguruj ograniczenia dostępu**

![Opcje sieci usługi App Service](media/app-service-ip-restrictions/access-restrictions.png)  

W interfejsie użytkownika ograniczeń dostępu można przejrzeć listę reguł ograniczeń dostępu zdefiniowanych dla aplikacji.

![ograniczenia dostępu do listy](media/app-service-ip-restrictions/access-restrictions-browse.png)

Na liście zostaną wyświetlona wszystkie bieżące ograniczenia, które znajdują się w aplikacji. Jeśli masz ograniczenie sieci wirtualnej w aplikacji, tabela pokaże, czy punkty końcowe usługi są włączone dla microsoft.web. Jeśli aplikacja nie ma zdefiniowanych ograniczeń, aplikacja będzie dostępna z dowolnego miejsca.  

## <a name="adding-ip-address-rules"></a>Dodawanie reguł adresów IP

Możesz kliknąć **przycisk [+] Dodaj regułę,** aby dodać nową regułę ograniczenia dostępu. Po dodaniu reguły wejdzie ona w życie natychmiast. Reguły są wymuszane w kolejności priorytetów, zaczynając od najniższej liczby i przechodząc w górę. Istnieje niejawne zaprzeczyć wszystko, co obowiązuje po dodaniu nawet jednej reguły.

Podczas tworzenia reguły należy wybrać opcję zezwalaj/odrzucaj, a także typ reguły. Musisz również podać wartość priorytetu i to, do czego ograniczasz dostęp.  Opcjonalnie można dodać nazwę i opis do reguły.  

![dodawanie reguły ograniczenia dostępu IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Aby ustawić regułę opartą na adresie IP, wybierz typ protokołu IPv4 lub IPv6. Notacja adresu IP musi być określona w notacji CIDR dla adresów IPv4 i IPv6. Aby określić dokładny adres, można użyć czegoś takiego jak 1.2.3.4/32, gdzie pierwsze cztery oktety reprezentują twój adres IP, a /32 jest maską. Notacja IPv4 CIDR dla wszystkich adresów wynosi 0.0.0.0/0. Aby dowiedzieć się więcej o notacji CIDR, możesz przeczytać [bezklasową routing międzydomenowy](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Punkty końcowe usługi

Punkty końcowe usługi umożliwia ograniczenie dostępu do wybranych podsieci sieci wirtualnej platformy Azure. Aby ograniczyć dostęp do określonej podsieci, utwórz regułę ograniczeń z typem sieci wirtualnej. Można wybrać subskrypcję, sieci wirtualnej i podsieci, które mają zezwolić lub odmówić dostępu. Jeśli punkty końcowe usługi nie są jeszcze włączone w witrynie Microsoft.Web dla wybranej podsieci, zostanie ona automatycznie włączona, chyba że zaznaczysz pole z prośbą o nieprzestrzeganie tego. Sytuacja, w której chcesz włączyć go w aplikacji, ale nie podsieć jest w dużej mierze związane, jeśli masz uprawnienia, aby włączyć punkty końcowe usługi w podsieci, czy nie. Jeśli chcesz uzyskać kogoś innego, aby włączyć punkty końcowe usługi w podsieci, można zaznaczyć pole wyboru i mieć aplikację skonfigurowaną dla punktów końcowych usługi w oczekiwaniu na to, że jest włączona później w podsieci. 

![dodawanie reguły ograniczenia dostępu do sieci wirtualnej](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Nie można ograniczyć dostępu do aplikacji uruchomionych w środowisku usługi app service. Gdy aplikacja znajduje się w środowisku usługi aplikacji, możesz kontrolować dostęp do aplikacji za pomocą reguł dostępu do adresu IP. 

Za pomocą punktów końcowych usługi można skonfigurować aplikację za pomocą bram aplikacji lub innych urządzeń WAF. Można również skonfigurować aplikacje wielowarstwowe z bezpiecznymi zapleczem. Aby uzyskać więcej informacji na temat niektórych możliwości, zobacz [Funkcje sieciowe oraz integracja usługi app service](networking-features.md) i [bramy aplikacji z punktami końcowymi usługi.](networking/app-gateway-with-service-endpoints.md)

## <a name="managing-access-restriction-rules"></a>Zarządzanie regułami ograniczeń dostępu

Możesz kliknąć dowolny wiersz, aby edytować istniejącą regułę ograniczeń dostępu. Zmiany wchodzą w życie natychmiast, w tym zmiany w kolejności priorytetów.

![edytowanie reguły ograniczenia dostępu](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Podczas edytowania reguły nie można zmienić typu między regułą adresu IP a regułą sieci wirtualnej. 

![edytowanie reguły ograniczenia dostępu](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Aby usunąć regułę, kliknij **...** na regułę, a następnie kliknij przycisk **Usuń**.

![usuwanie reguły ograniczenia dostępu](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Blokowanie jednego adresu IP ##

Podczas dodawania pierwszej reguły ograniczenia ip usługa doda jawną regułę **Odmów wszystkie** o priorytecie 2147483647. W praktyce jawna **reguła Odmów wszystkich** będzie ostatnią regułą wykonaną i zablokuje dostęp do dowolnego adresu IP, który nie jest jawnie dozwolony przy użyciu **reguły Zezwalaj.**

W scenariuszu, w którym użytkownicy chcą jawnie zablokować pojedynczy adres IP lub blok adresu IP, ale zezwolić na dostęp do wszystkich innych, konieczne jest dodanie jawnej **reguły Zezwalaj na wszystko.**

![blokować pojedynczy adres IP](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>Strona SCM 

Oprócz możliwości kontrolowania dostępu do aplikacji, można również ograniczyć dostęp do witryny scm używanej przez aplikację. Witryna scm jest punktem końcowym wdrażania sieci web, a także konsolą Kudu. Ograniczenia dostępu można oddzielnie przypisać do witryny scm z aplikacji lub użyć tego samego zestawu zarówno dla aplikacji, jak i witryny scm. Po zaznaczeniu tego pola, aby mieć takie same ograniczenia jak aplikacja, wszystko jest wygaszone. Jeśli odznaczysz to pole, niezależnie od ustawień, które wcześniej miałeś w witrynie scm, są stosowane. 

![ograniczenia dostępu do listy](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programowa manipulacja regułami ograniczeń dostępu ##

[Narzędzia Cli platformy Azure](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) i [azure powershell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) ma obsługę edycji ograniczeń dostępu. Przykład dodania ograniczenia dostępu przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Przykład dodania ograniczenia dostępu przy użyciu programu Azure PowerShell:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Wartości można również ustawić ręcznie za pomocą operacji [azure rest interfejsu API](https://docs.microsoft.com/rest/api/azure/) PUT w konfiguracji aplikacji w Menedżerze zasobów lub przy użyciu szablonu Usługi Azure Resource Manager. Na przykład można użyć resources.azure.com i edytować ipSecurityRestrictions bloku, aby dodać wymagane JSON.

Lokalizacja tych informacji w Menedżerze zasobów jest:

management.azure.com/subscriptions/ identyfikator**subskrypcji**/resourceGroups/**grupy zasobów**/providers/Microsoft.Web/sites/nazwa**aplikacji sieci Web**/config/web?api-version=2018-02-01

Składnia JSON dla wcześniejszego przykładu jest:
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

## <a name="azure-functions-access-restrictions"></a>Ograniczenia dostępu usługi Azure Functions

Ograniczenia dostępu są również dostępne dla aplikacji funkcyjnych z taką samą funkcjonalnością jak plany usługi App Service. Włączenie ograniczeń dostępu spowoduje wyłączenie edytora kodu portalu dla niedozwolonych wiadomości IP.

## <a name="next-steps"></a>Następne kroki
[Ograniczenia dostępu dla funkcji platformy Azure](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Integracja bramy aplikacji z punktami końcowymi usługi](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
