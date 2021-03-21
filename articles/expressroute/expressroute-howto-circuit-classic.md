---
title: 'Azure ExpressRoute: modyfikowanie obwodu: PowerShell: klasyczny'
description: W tym artykule omówiono procedurę sprawdzania stanu, aktualizowania lub usuwania i anulowania aprowizacji obwodu klasycznego modelu wdrażania ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/05/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be45d49d3f445810c7ac6a38e3e12abe178a4bed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018177"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Modyfikowanie obwodu usługi ExpressRoute przy użyciu programu PowerShell (wersja klasyczna)

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

W tym artykule omówiono procedurę sprawdzania stanu, aktualizowania lub usuwania i anulowania aprowizacji obwodu klasycznego modelu wdrażania ExpressRoute. Dotyczy on klasycznego modelu wdrożenia.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Modele wdrażania Azure — informacje**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Pobieranie stanu obwodu

Te informacje można pobrać w dowolnym momencie przy użyciu `Get-AzureCircuit` polecenia cmdlet. Wywołanie bez parametrów wyświetla wszystkie obwody.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Możesz uzyskać informacje dotyczące określonego obwodu usługi ExpressRoute, przechodząc jako parametr do wywołania.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Aby uzyskać szczegółowe opisy wszystkich parametrów, należy uruchomić następujący przykład:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Modyfikowanie obwodu

Niektóre właściwości obwodu usługi ExpressRoute można modyfikować bez wpływu na łączność.

W przypadku braku przestojów można wykonać następujące zadania:

* Włącz lub Wyłącz dodatek ExpressRoute Premium dla obwodu usługi ExpressRoute.
* Zwiększ przepustowość obwodu ExpressRoute pod warunkiem, że na porcie jest dostępna pojemność. Obniżenie przepustowości obwodu nie jest obsługiwane.
* Zmień plan pomiaru z danych mierzonych na dane nieograniczone. Zmiana planu pomiaru z nieograniczonej ilości danych na dane taryfowe nie jest obsługiwana.
* Można włączać i wyłączać *Zezwalanie na klasyczne operacje*.

Więcej informacji na temat limitów i ograniczeń można znaleźć w temacie [ExpressRoute FAQ (często zadawane pytania](expressroute-faqs.md) ).

### <a name="enable-the-expressroute-premium-add-on"></a>Włącz dodatek ExpressRoute Premium

Dodatek ExpressRoute Premium dla istniejącego obwodu można włączyć za pomocą następującego polecenia cmdlet programu PowerShell:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

W obwodzie będzie teraz dostępna funkcja dodatku ExpressRoute Premium. Gdy tylko polecenie zostało pomyślnie uruchomione, rozpocznie się rozliczanie dla możliwości dodatku Premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Wyłącz dodatek ExpressRoute Premium

> [!IMPORTANT]
> Ta operacja może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż dozwolone dla obwodu standardowego.
>
>

#### <a name="considerations"></a>Zagadnienia do rozważenia

* Upewnij się, że liczba sieci wirtualnych podłączonych do obwodu jest mniejsza niż 10 przed obniżeniem poziomu warstwy Premium do warstwy Standardowa. Jeśli tego nie zrobisz, żądanie aktualizacji zakończy się niepowodzeniem, a opłaty są naliczane.
* Należy odłączyć wszystkie sieci wirtualne w innych regionach geopolitycznych. Jeśli tego nie zrobisz, żądanie aktualizacji zakończy się niepowodzeniem, a opłaty są naliczane.
* Tabela tras musi być krótsza niż 4 000 tras dla prywatnej komunikacji równorzędnej. Jeśli rozmiar tabeli tras jest większy niż 4 000 tras, sesja protokołu BGP spadnie i nie zostanie ponownie włączona, dopóki liczba anonsowanych prefiksów nie spadnie poniżej 4 000.

#### <a name="to-disable-the-premium-add-on"></a>Aby wyłączyć dodatek Premium

Dodatek ExpressRoute Premium dla istniejącego obwodu można wyłączyć za pomocą następującego polecenia cmdlet programu PowerShell:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Aktualizowanie przepustowości obwodu ExpressRoute

Zapoznaj się z [ExpressRoute często zadawanymi pytaniami](expressroute-faqs.md) dotyczącymi obsługiwanych opcji przepustowości dla dostawcy. Można wybrać dowolny rozmiar, który jest większy niż rozmiar istniejącego obwodu, o ile jest to port fizyczny (na którym jest tworzony obwód).

> [!IMPORTANT]
> Może być konieczne ponowne utworzenie obwodu ExpressRoute, jeśli nie ma odpowiedniej pojemności na istniejącym porcie. Nie można uaktualnić obwodu, jeśli w tej lokalizacji nie ma dodatkowej pojemności.
>
> Nie można zmniejszyć przepustowości obwodu ExpressRoute bez zakłóceń. Obniżenie przepustowości wymaga anulowania aprowizacji obwodu ExpressRoute, a następnie ponownego aprowizacji nowego obwodu ExpressRoute.
>
>

#### <a name="resize-a-circuit"></a>Zmień rozmiar obwodu

Po podjęciu decyzji o żądanym rozmiarze możesz użyć następującego polecenia, aby zmienić rozmiar obwodu:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Gdy rozmiar obwodu zostanie osiągnięty po stronie firmy Microsoft, należy skontaktować się z dostawcą połączenia w celu zaktualizowania konfiguracji po stronie, aby dopasować tę zmianę. Rozliczenia zaczynają obowiązywać dla zaktualizowanej przepustowości od tego momentu.

Jeśli podczas zwiększania przepustowości obwodu zostanie wyświetlony następujący błąd, oznacza to, że nie ma wystarczającej przepustowości w porcie fizycznym, w którym jest tworzony istniejący obwód. Należy usunąć ten obwód i utworzyć nowy obwód rozmiaru, którego potrzebujesz.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Cofanie aprowizacji i usuwanie obwodu

### <a name="considerations"></a>Zagadnienia do rozważenia

* Aby ta operacja zakończyła się powodzeniem, należy odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Sprawdź, czy istnieją sieci wirtualne połączone z obwodem, jeśli ta operacja nie powiedzie się.
* Jeśli stan aprowizacji dostawcy usługi obwodu ExpressRoute jest inicjowany lub **Zainicjowano obsługę administracyjną** , należy skontaktować się z dostawcą usług w celu **anulowania aprowizacji** obwodu po stronie. Nadal rezerwujemy zasoby i obciążamy Cię, dopóki dostawca usług nie ukończy anulowania aprowizacji obwodu i powiadamia nas.
* Jeśli dostawca usług anulował **obsługę administracyjną** obwodu (stan aprowizacji dostawcy usług jest ustawiony na nieudostępniane), można następnie usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.

#### <a name="delete-a-circuit"></a>Usuwanie obwodu

Obwód ExpressRoute można usunąć, uruchamiając następujące polecenie:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
