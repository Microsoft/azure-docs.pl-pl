---
title: Samouczek — Skalowanie automatyczne zestawu skalowania przy użyciu szablonów platformy Azure
description: Dowiedz się, jak za pomocą szablonów usługi Azure Resource Manager automatycznie skalować zestaw skalowania maszyn wirtualnych w odpowiedzi na wzrosty i spadki zapotrzebowania na procesor CPU
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: f94a68833347d662f427fa0944dd83d33458bd14
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746006"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-an-azure-template"></a>Samouczek: skalowanie automatyczne zestawu skalowania maszyn wirtualnych przy użyciu szablonu platformy Azure
Podczas tworzenia zestawu skalowania musisz zdefiniować liczbę wystąpień maszyn wirtualnych, które chcesz uruchamiać. W odpowiedzi na zmieniające się zapotrzebowanie aplikacji możesz automatycznie zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych. Skalowanie automatyczne pozwala spełniać potrzeby klientów lub reagować na zmiany wydajności aplikacji w całym cyklu jej życia. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Używanie funkcji skalowania automatycznego z zestawem skalowania
> * Tworzenie reguł skalowania automatycznego i korzystanie z nich
> * Testy obciążeniowe wystąpień maszyn wirtualnych i wyzwalanie reguł skalowania automatycznego
> * Skalowanie automatyczne do wewnątrz po zmniejszeniu zapotrzebowania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.29 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 


## <a name="define-an-autoscale-profile"></a>Definiowanie profilu skalowania automatycznego
Profil skalowania automatycznego w szablonie platformy Azure definiuje się za pomocą dostawcy zasobów *Microsoft.insights/autoscalesettings* . *Profil* określa pojemność zestawu skalowania oraz wszystkie skojarzone reguły. W poniższym przykładzie zdefiniowano profil o nazwie *Autoscale by percentage based on CPU usage* (Skalowanie automatyczne według wartości procentowej na podstawie użycia procesora CPU) i ustawiono domyślną, minimalną pojemność — *2* — oraz maksymalną pojemność — *10* wystąpień maszyn wirtualnych:

```json
{
"type": "Microsoft.insights/autoscalesettings",
"name": "Autoscale",
"apiVersion": "2015-04-01",
"location": "[variables('location')]",
"scale": null,
"properties": {
  "profiles": [
    {
      "name": "Autoscale by percentage based on CPU usage",
      "capacity": {
        "minimum": "2",
        "maximum": "10",
        "default": "2"
      }
    }
  ]
}
```


## <a name="define-a-rule-to-autoscale-out"></a>Definiowanie reguły skalowania automatycznego w poziomie
Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawie skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Możesz określić, jakie metryki mają być monitorowane, na przykład procesor CPU lub dysk, oraz jak długo obciążenie aplikacji musi przekraczać wartość progową, a także ile wystąpień maszyn wirtualnych ma zostać dodanych do zestawu skalowania.

W poniższym przykładzie zdefiniowano regułę, która zwiększa liczbę wystąpień maszyn wirtualnych w zestawie skalowania, jeśli w okresie pięciu minut średnie obciążenie procesora CPU wzrośnie ponad 70%. Wyzwolenie reguły powoduje zwiększenie liczby wystąpień maszyn wirtualnych o trzy.

W tej regule są używane następujące parametry:

| Parametr         | Wyjaśnienie                                                                                                         | Wartość           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | Metryka wydajności, która jest monitorowana i na której są stosowane akcje zestawu skalowania.                                                   | Procentowe użycie procesora CPU  |
| *timeGrain*       | Częstotliwość zbierania metryk do analizy.                                                                   | 1 minuta        |
| *timeAggregation* | Określa sposób agregacji metryk zebranych do celów analizy.                                                | Średnia         |
| *timeWindow*      | Przedział czasu monitorowania, po którym wartość metryki jest porównywana z wartością progową.                                   | 5 minut       |
| *zakład*        | Operator używany do porównywania danych metryki z wartością progową.                                                     | Większe niż    |
| *próg*       | Wartość, która powoduje wyzwolenie akcji przez regułę skalowania automatycznego.                                                      | 70%             |
| *wskazywa*       | Określa, czy podczas stosowania reguły ma nastąpić skalowanie w pionie czy w poziomie.                                              | Zwiększ        |
| *Wprowadź*            | Wskazuje, że liczba wystąpień maszyn wirtualnych powinna zostać zmieniona o określoną wartość.                                    | Liczba zmian    |
| *wartość*           | Określa liczbę wystąpień maszyn wirtualnych podlegających skalowaniu w pionie lub w poziomie podczas stosowania reguły.                                             | 3               |
| *cooldown*        | Przedział czasu przed ponownym zastosowaniem reguły, który gwarantuje, że akcje skalowania automatycznego zaczną obowiązywać. | 5 minut       |

Do sekcji profilu dostawcy zasobów *Microsoft.insights/autoscalesettings* wspomnianego w poprzedniej sekcji zostanie dodana następująca reguła:

```json
"rules": [
  {
    "metricTrigger": {
      "metricName": "Percentage CPU",
      "metricNamespace": "",
      "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
      "metricResourceLocation": "[variables('location')]",
      "timeGrain": "PT1M",
      "statistic": "Average",
      "timeWindow": "PT5M",
      "timeAggregation": "Average",
      "operator": "GreaterThan",
      "threshold": 70
    },
    "scaleAction": {
      "direction": "Increase",
      "type": "ChangeCount",
      "value": "3",
      "cooldown": "PT5M"
    }
  }
]
```


## <a name="define-a-rule-to-autoscale-in"></a>Definiowanie reguły skalowania automatycznego w pionie
Wieczorami lub w weekendy zapotrzebowanie aplikacji może być mniejsze. Jeśli spadek obciążenia ma cechy stałego trendu w danym okresie, można skonfigurować reguły skalowania automatycznego umożliwiające zmniejszenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Akcja skalowania w pionie ogranicza koszt używania zestawu skalowania, ponieważ jest uruchomionych tylko tyle wystąpień, ile jest wymaganych do zaspokojenia bieżącego zapotrzebowania.

W poniższym przykładzie zdefiniowano regułę umożliwiającą skalowanie w pionie liczby wystąpień maszyn wirtualnych o jeden, jeśli w okresie pięciu minut średnie obciążenie procesora CPU spadnie poniżej 30%. Ta reguła zostanie dodana do profilu skalowania automatycznego po regule skalowania w poziomie:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
    "metricResourceLocation": "[variables('location')]",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-an-autoscaling-scale-set"></a>Tworzenie automatycznego zestawu skalowania
Za pomocą przykładowego szablonu utworzymy zestaw skalowania i zastosujemy reguły automatycznego skalowania. Możesz [przejrzeć cały szablon](https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json) lub [wyświetlić jego sekcję dostawcy zasobów *Microsoft.insights/autoscalesettings*](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/autoscale.json#L220).

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych za pomocą polecenia [az group deployment create](/cli/azure/group/deployment). Po wyświetleniu monitu podaj poświadczenia dla poszczególnych wystąpień maszyn wirtualnych: swoją nazwę użytkownika, taką jak *azureuser* , i hasło:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="generate-cpu-load-on-scale-set"></a>Generowanie obciążenia procesora CPU w zestawie skalowania
Przetestowanie reguł skalowania automatycznego wymaga wygenerowania obciążenia procesora CPU na wystąpieniach maszyn wirtualnych w zestawie skalowania. Symulowane obciążenie procesora CPU powoduje uruchomienie skalowania w poziomie przez reguły skalowania automatycznego, a w efekcie zwiększenie liczby wystąpień maszyn wirtualnych. Gdy symulowane obciążenie procesora CPU zmniejszy się, nastąpi automatyczne skalowanie w pionie i ograniczenie liczby wystąpień maszyn wirtualnych.

Najpierw za pomocą polecenia [az vmss list-instance-connection-info](/cli/azure/vmss) podaj listę adresów i portów służących do nawiązania połączenia z wystąpieniami maszyn wirtualnych w zestawie skalowania:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Następujące przykładowe dane wyjściowe zawierają nazwę wystąpienia, publiczny adres IP modułu równoważenia obciążenia oraz numer portu, do którego reguły translatora adresów sieciowych (NAT) kierują ruch:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Połącz się przez protokół SSH z pierwszym wystąpieniem maszyny wirtualnej. Podaj własny publiczny adres IP i numer portu za pomocą parametru `-p`, jak pokazano w poprzednim poleceniu:

```console
ssh azureuser@13.92.224.66 -p 50001
```

Po zalogowaniu zainstaluj narzędzie **stress** . Uruchom *10 procesów roboczych narzędzia* **stress** , które generują obciążenie procesora CPU. Procesy robocze będą działać przez *420* sekund, co wystarczy do zaimplementowania odpowiedniej akcji przez reguły skalowania automatycznego.

```console
sudo apt-get update
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Gdy narzędzie **stress** wyświetli dane wyjściowe podobne do *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* , naciśnij klawisz *Enter* , aby powrócić do wiersza polecenia.

Aby potwierdzić obecność wygenerowanego obciążenia procesora CPU przez narzędzie **stress** , sprawdź aktywne obciążenie systemu za pomocą narzędzia **top** :

```console
top
```

Zakończ działanie narzędzia **top** , a następnie zamknij połączenie z wystąpieniem maszyny wirtualnej. Narzędzie **stress** będzie kontynuować działanie na wystąpieniu maszyny wirtualnej.

```console
Ctrl-c
exit
```

Połącz się z drugim wystąpieniem maszyny wirtualnej przy użyciu numeru portu z poprzedniego polecenia [az vmss list-instance-connection-info](/cli/azure/vmss):

```console
ssh azureuser@13.92.224.66 -p 50003
```

Zainstaluj i uruchom narzędzie **stress** , a następnie uruchom dziesięć procesów roboczych na drugim wystąpieniu maszyny wirtualnej.

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

I ponownie, gdy narzędzie **stress** wyświetli dane wyjściowe podobne do *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* , naciśnij klawisz *Enter* , aby powrócić do wiersza polecenia.

Zamknij połączenie z drugim wystąpieniem maszyny wirtualnej. Narzędzie **stress** będzie kontynuować działanie na wystąpieniu maszyny wirtualnej.

```console
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Monitorowanie aktywnych reguł skalowania automatycznego
Do monitorowania liczby wystąpień maszyn wirtualnych w zestawie skalowania służy narzędzie **watch** . Po upływie pięciu minut reguły skalowania automatycznego rozpoczynają proces skalowania w poziomie w odpowiedzi na obciążenie procesora CPU wygenerowane przez narzędzie **stress** na poszczególnych wystąpieniach maszyn wirtualnych:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Po osiągnięciu progu procesora CPU reguły skalowania automatycznego zwiększają liczbę wystąpień maszyn wirtualnych w zestawie skalowania. Następujące dane wyjściowe zawierają trzy maszyny wirtualne tworzone w zestawie skalowania podczas automatycznego skalowania w poziomie:

```output
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Gdy narzędzie **stress** zakończy działanie na początkowych wystąpieniach maszyn wirtualnych, średnie obciążenie procesora CPU powróci do typowej wartości. Po kolejnych pięciu minutach reguły skalowania automatycznego inicjują skalowanie w pionie liczby wystąpień maszyn wirtualnych. Najpierw są usuwane wystąpienia maszyn wirtualnych o najwyższych identyfikatorach. Jeśli zestaw skalowania używa zestawów dostępności lub stref dostępności, akcje skalowania w pionie są dystrybuowane równomiernie na te wystąpienia maszyn wirtualnych. Następujące przykładowe dane wyjściowe przedstawiają usuwanie wystąpienia maszyny wirtualnej z zestawu skalowania podczas skalowania w pionie:

```output
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Zakończ działanie narzędzia *watch* za pomocą klawiszy `Ctrl-c`. Co pięć minut zestaw skalowania będzie powtarzał operację skalowania w pionie, usuwając po jednym wystąpieniu maszyny wirtualnej aż do osiągnięcia minimalnej liczby wystąpień równej dwa.


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Aby pozbyć się zestawu skalowania i dodatkowych zasobów, usuń grupę zasobów wraz z całą zawartością za pomocą polecenia [az group delete](/cli/azure/group):

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono automatyczne skalowanie zestawu skalowania w pionie lub w poziomie za pomocą interfejsu wiersza polecenia platformy Azure:

> [!div class="checklist"]
> * Używanie funkcji skalowania automatycznego z zestawem skalowania
> * Tworzenie reguł skalowania automatycznego i korzystanie z nich
> * Testy obciążeniowe wystąpień maszyn wirtualnych i wyzwalanie reguł skalowania automatycznego
> * Skalowanie automatyczne do wewnątrz po zmniejszeniu zapotrzebowania
