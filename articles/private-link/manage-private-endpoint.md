---
title: Zarządzanie połączeniem prywatnego punktu końcowego na platformie Azure
description: Dowiedz się, jak zarządzać połączeniami prywatnego punktu końcowego na platformie Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2987cd1ff8c678f7079e13e8b9bc657817c066f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95531376"
---
# <a name="manage-a-private-endpoint-connection"></a>Zarządzanie połączeniem z prywatnym punktem końcowym
Łącze prywatne platformy Azure działa w modelu przepływu wywołań zatwierdzania, w którym odbiorca usługi linku prywatnego może zażądać połączenia z dostawcą usług w celu korzystania z usługi. Dostawca usług może następnie zdecydować, czy zezwolić konsumentowi na nawiązanie połączenia. Link prywatny platformy Azure umożliwia dostawcom usług Zarządzanie połączeniem prywatnego punktu końcowego w swoich zasobach. Ten artykuł zawiera instrukcje dotyczące zarządzania połączeniami prywatnego punktu końcowego.

![Zarządzanie prywatnymi punktami końcowymi](media/manage-private-endpoint/manage-private-endpoint.png)

Istnieją dwie metody zatwierdzania połączeń, z których może wybierać odbiorcy usługi link prywatny:
- **Automatyczne**: Jeśli klient usługi ma uprawnienia kontroli dostępu platformy Azure do zasobu dostawcy usługi, może wybrać metodę automatycznego zatwierdzania. W takim przypadku, gdy żądanie osiągnie zasób dostawcy usługi, nie jest wymagane żadne działanie od usługodawcy, a połączenie jest automatycznie zatwierdzane. 
- **Ręczne**: w przeciwieństwie do tego, czy konsument usługi nie ma uprawnień kontroli RBAC platformy Azure w ramach zasobu dostawcy usług, odbiorca może wybrać metodę ręcznego zatwierdzania. W takim przypadku żądanie połączenia pojawia się w zasobach usługi jako **oczekujące**. Aby można było nawiązać połączenia, dostawca usług musi ręcznie zatwierdzić żądanie. W przypadku ręcznych, konsument usługi może także określić komunikat z żądaniem, aby dostarczyć więcej kontekstu do dostawcy usług. Dostawca usług zawiera następujące opcje do wyboru dla wszystkich połączeń prywatnych punktów końcowych: **zatwierdzone**, **odrzucone**, **Usuń**.

W poniższej tabeli przedstawiono różne akcje dostawcy usług oraz Stany połączeń powstających dla prywatnych punktów końcowych.  Dostawca usług może również zmienić stan połączenia prywatnego połączenia punktu końcowego w późniejszym czasie bez interwencji konsumenta. Akcja spowoduje zaktualizowanie stanu punktu końcowego po stronie klienta. 


|Akcja dostawcy usług   |Stan prywatnego punktu końcowego klienta usługi   |Opis   |
|---------|---------|---------|
|Brak    |    Oczekiwanie     |    Połączenie jest tworzone ręcznie i oczekuje na zatwierdzenie przez właściciela zasobu link prywatny.       |
|Zatwierdzenie    |  Approved (Zatwierdzono)       |  Połączenie zostało automatycznie lub ręcznie zatwierdzone i jest gotowe do użycia.     |
|Reject     | Odrzucone        | Połączenie zostało odrzucone przez właściciela zasobu link prywatny.        |
|Usuń    |  Odłączony       | Połączenie zostało usunięte przez właściciela zasobu linku prywatnego, prywatny punkt końcowy zmieni się na format i powinien zostać usunięty w celu oczyszczenia.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Zarządzanie połączeniami z prywatnym punktem końcowym w zasobach usługi Azure PaaS
Portal jest preferowaną metodą zarządzania połączeniami prywatnego punktu końcowego w zasobach usługi Azure PaaS. Obecnie nie mamy obsługi środowiska PowerShell/interfejsu wiersza polecenia do zarządzania połączeniami w ramach zasobów usługi Azure PaaS.
1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
2. Przejdź do prywatnego centrum linków.
3. W obszarze **zasoby** wybierz typ zasobu, do którego chcesz zarządzać połączeniami prywatnego punktu końcowego.
4. Dla każdego typu zasobu można wyświetlić liczbę skojarzonych z nim połączeń prywatnych punktów końcowych. Zasoby można filtrować zgodnie z wymaganiami.
5. Wybierz połączenia prywatnego punktu końcowego.  W obszarze połączenia na liście Wybierz połączenie, które chcesz zarządzać. 
6. Stan połączenia można zmienić, wybierając pozycję z opcji w górnej części.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Zarządzanie połączeniami prywatnego punktu końcowego w usłudze linku prywatnego klienta/partnera

Azure PowerShell i interfejs wiersza polecenia platformy Azure są preferowanymi metodami zarządzania połączeniami prywatnego punktu końcowego w usługach partnerskich firmy Microsoft lub w usługach należących do klienta. Obecnie nie mamy pomocy technicznej portalu do zarządzania połączeniami w usłudze linku prywatnego.  
 
### <a name="powershell"></a>PowerShell 
  
Aby zarządzać połączeniami prywatnych punktów końcowych, użyj następujących poleceń programu PowerShell.  
#### <a name="get-private-link-connection-states"></a>Pobierz Stany połączeń linków prywatnych 
Użyj `Get-AzPrivateLinkService` polecenia cmdlet, aby uzyskać połączenia prywatnych punktów końcowych i ich Stanów.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Zatwierdź połączenie prywatnego punktu końcowego 
 
Użyj `Approve-AzPrivateEndpointConnection` polecenia cmdlet, aby zatwierdzić połączenie prywatnego punktu końcowego. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Odmowa połączenia prywatnego punktu końcowego 
 
Użyj `Deny-AzPrivateEndpointConnection` polecenia cmdlet, aby odrzucić połączenie prywatnego punktu końcowego. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Usuń połączenie prywatnego punktu końcowego 
 
Użyj `Remove-AzPrivateEndpointConnection` polecenia cmdlet, aby usunąć połączenie prywatnego punktu końcowego. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 
 
Służy `az network private-link-service update` do zarządzania połączeniami prywatnego punktu końcowego. Stan połączenia jest określony w ```azurecli connection-status``` parametrze. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej o prywatnych punktach końcowych](private-endpoint-overview.md)
 
