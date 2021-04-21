---
title: Przewodnik rozwiązywania problemów z Azure Service Bus | Microsoft Docs
description: Dowiedz się więcej o poradach i zaleceniach dotyczących rozwiązywania kilku problemów, które mogą wystąpić podczas korzystania z Azure Service Bus.
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: 27249d7e016ea8aee0552bbbf1687647760d4b6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786571"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Przewodnik rozwiązywania problemów z Azure Service Bus
Ten artykuł zawiera wskazówki i zalecenia dotyczące rozwiązywania kilku problemów, które mogą wystąpić podczas korzystania z Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemy z łącznością, certyfikatem lub limitem czasu
Poniższe kroki mogą pomóc w rozwiązywaniu problemów z łącznością/certyfikatem/limitem czasu dla wszystkich usług w obszarze *.servicebus.windows.net. 

- Przejdź do witryny lub [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Ułatwia sprawdzenie, czy masz problemy z filtrowaniem adresów IP, siecią wirtualną lub łańcuchem certyfikatów, które są typowe w przypadku korzystania z zestawu Java SDK.

    Przykład pomyślnego komunikatu:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Przykład komunikatu o błędzie:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Uruchom następujące polecenie, aby sprawdzić, czy jakikolwiek port jest zablokowany w zaporze. Używane porty to 443 (HTTPS), 5671 (AMQP) i 9354 (Net Messaging/SBMP). W zależności od używanej biblioteki używane są również inne porty. Oto przykładowe polecenie, które sprawdza, czy port 5671 jest zablokowany. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    W systemie Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Jeśli występują sporadyczne problemy z łącznością, uruchom następujące polecenie, aby sprawdzić, czy istnieją jakiekolwiek porzucone pakiety. To polecenie spróbuje nawiązać 25 różnych połączeń TCP co 1 sekundę z usługą. Następnie możesz sprawdzić, ile z nich zakończyło się powodzeniem lub niepowodzeniem, a także zobaczyć opóźnienie połączenia TCP. Narzędzie możesz pobrać `psping` tutaj. [](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Możesz użyć równoważnych poleceń, jeśli używasz innych narzędzi, takich jak `tnc` `ping` , i tak dalej. 
- Uzyskaj ślad sieci, jeśli poprzednie kroki nie pomogą i nie przeanalizuj go przy użyciu narzędzi, takich [jak Wireshark.](https://www.wireshark.org/) W [razie Pomoc techniczna Microsoft](https://support.microsoft.com/) skontaktuj się z działem pomocy. 
- Aby znaleźć odpowiednie adresy IP do dodania do listy zezwalania dla połączeń, zobacz Jakie adresy IP należy dodać do [listy zezwalania.](service-bus-faq.yml#what-ip-addresses-do-i-need-to-add-to-allow-list-) 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemy, które mogą wystąpić podczas uaktualniania/ponownego uruchamiania usługi

### <a name="symptoms"></a>Objawy
- Żądania mogą być chwilowo ograniczane.
- Może wystąpić spadek w przychodzących komunikatach/żądaniach.
- Plik dziennika może zawierać komunikaty o błędach.
- Aplikacje mogą zostać odłączone od usługi na kilka sekund.

### <a name="cause"></a>Przyczyna
Uaktualnienia i ponowne uruchomienia usługi zaplecza mogą powodować te problemy w aplikacjach.

### <a name="resolution"></a>Rozwiązanie
Jeśli kod aplikacji używa zestawu SDK, zasady ponawiania prób są już wbudowane i aktywne. Aplikacja zostanie ponownie nawiązyna bez znaczącego wpływu na aplikację/przepływ pracy.

## <a name="unauthorized-access-send-claims-are-required"></a>Nieautoryzowany dostęp: wymagane jest wysyłanie oświadczeń

### <a name="symptoms"></a>Objawy 
Ten błąd może zostać wyświetlony podczas próby uzyskania dostępu do tematu usługi Service Bus z usługi Visual Studio na komputerze lokalnym przy użyciu tożsamości zarządzanej przypisanej przez użytkownika z uprawnieniami do wysyłania.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Przyczyna
Tożsamość nie ma uprawnień dostępu do Service Bus tematu. 

### <a name="resolution"></a>Rozwiązanie
Aby usunąć ten błąd, zainstaluj [bibliotekę Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)  Aby uzyskać więcej informacji, zobacz [Lokalne uwierzytelnianie programowe.](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication) 

Aby dowiedzieć się, jak przypisywać uprawnienia do ról, zobacz [Authenticate a managed identity with Azure Active Directory to access Azure Service Bus resources (Uwierzytelnianie](service-bus-managed-service-identity.md)tożsamości zarządzanej za pomocą usługi Azure Active Directory w celu uzyskania dostępu do Azure Service Bus zasobów).

## <a name="service-bus-exception-put-token-failed"></a>Service Bus wyjątku: Token put nie powiódł się

### <a name="symptoms"></a>Objawy
Podczas próby wysłania więcej niż 1000 komunikatów przy użyciu tego samego Service Bus zostanie wyświetlony następujący komunikat o błędzie: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Przyczyna
Istnieje limit liczby tokenów używanych do wysyłania i odbierania komunikatów przy użyciu jednego połączenia z Service Bus nazw. Jest to 1000. 

### <a name="resolution"></a>Rozwiązanie
Otwórz nowe połączenie z przestrzenią nazw Service Bus, aby wysłać więcej komunikatów.

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>Dodawanie reguły sieci wirtualnej przy użyciu programu PowerShell kończy się niepowodzeniem

### <a name="symptoms"></a>Objawy
Skonfigurowano dwie podsieci z jednej sieci wirtualnej w regułę sieci wirtualnej. Próba usunięcia jednej podsieci przy użyciu polecenia cmdlet [Remove-AzServiceBusVirtualNetworkRule](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule) nie powoduje usunięcia podsieci z reguły sieci wirtualnej. 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>Przyczyna
Identyfikator Azure Resource Manager określony dla podsieci może być nieprawidłowy. Może się to zdarzyć, gdy sieć wirtualna znajduje się w innej grupie zasobów niż ta, która ma Service Bus nazw. Jeśli nie określisz jawnie grupy zasobów sieci wirtualnej, polecenie interfejsu wiersza polecenia konstruuje identyfikator Azure Resource Manager przy użyciu grupy zasobów Service Bus nazw. W związku z tym nie może usunąć podsieci z reguły sieci. 

### <a name="resolution"></a>Rozwiązanie
Określ pełny Azure Resource Manager podsieci, który zawiera nazwę grupy zasobów, która ma sieć wirtualną. Na przykład:

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Azure Resource Manager wyjątki](service-bus-resource-manager-exceptions.md). Zawiera listę wyjątków generowanych podczas interakcji z Azure Service Bus przy użyciu Azure Resource Manager (za pośrednictwem szablonów lub wywołań bezpośrednich).
- [Wyjątki obsługi komunikatów](service-bus-messaging-exceptions.md). Zawiera listę wyjątków generowanych przez .NET Framework dla Azure Service Bus.