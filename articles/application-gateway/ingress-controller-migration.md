---
title: Jak przeprowadzić migrację z platformy Azure Application Gateway kontroler transferu danych przychodzących Helm do AGIC
description: Ten artykuł zawiera instrukcje dotyczące migracji z programu AGIC wdrożonego za pośrednictwem Helm do AGIC wdrożonego jako dodatek AKS
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050838"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>Migrowanie z AGIC Helm do dodatku AGIC 

Jeśli masz już AGIC wdrożony za pomocą Helm, ale chcesz przeprowadzić migrację do AGIC wdrożoną jako dodatek AKS, poniższe kroki pomogą Ci w przeprowadzeniu procesu migracji. 

## <a name="prerequisites"></a>Wymagania wstępne 
Przed rozpoczęciem procesu migracji należy sprawdzić kilka rzeczy. 
  - Czy używasz dowolnych funkcji z AGIC Helm, które [nie są obecnie obsługiwane przez dodatek AGIC](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)?
  - Czy używasz więcej niż jednego wdrożenia AGIC Helm na klaster AKS? 
  - Czy używasz wielu wdrożeń AGIC Helm do jednego Application Gateway? 

Jeśli otrzymasz odpowiedź tak na dowolne z powyższych pytań, dodatek AGIC nie będzie obsługiwał Twojego przypadku użycia, dlatego najlepiej będzie nadal korzystać z AGIC Helm w międzyczasie. W przeciwnym razie Kontynuuj proces migracji poniżej w godzinach poza godzinami pracy. 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>Znajdź Application Gateway identyfikator zasobu, który jest obecnie elementem docelowym AGIC Helm 
Przejdź do Application Gateway, do którego należy wdrożenie AGIC Helm. Skopiuj i Zapisz identyfikator zasobu tego Application Gateway. Identyfikator zasobu będzie potrzebny w późniejszym kroku. Identyfikator zasobu można znaleźć w portalu, na karcie właściwości Application Gateway lub za pomocą interfejsu wiersza polecenia platformy Azure. Poniższy przykład zapisuje Application Gateway IDENTYFIKATORem zasobu do *appgwId* dla bramy o nazwie *myApplicationGateway* *w grupie zasobów*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>Usuwanie AGIC Helm z klastra AKS
Za pomocą interfejsu wiersza polecenia platformy Azure Usuń wdrożenie AGIC Helm z klastra. Przed włączeniem dodatku AGIC AKS należy najpierw usunąć wdrożenie AGIC Helm. Należy pamiętać, że wszelkie zmiany, które wystąpiły w ramach klastra AKS, między upływem czasu usunięcia wdrożenia AGIC Helm oraz momentu włączenia dodatku AGIC nie zostaną odzwierciedlone w Application Gateway, w związku z czym ten proces migracji powinien zostać wykonany poza godzinami pracy, aby zminimalizować wpływ. Application Gateway nadal będzie miała ostatnią konfigurację stosowaną przez AGIC, więc nie wpłynie to na istniejące reguły routingu. 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>Włącz dodatek AGIC przy użyciu istniejących Application Gateway 
Teraz można włączyć dodatek AGIC w klastrze AKS, aby kierować istniejącym Application Gateway za pomocą interfejsu wiersza polecenia platformy Azure lub portalu. Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby włączyć dodatek AGIC w klastrze AKS. Przykładem jest włączenie dodatku w klastrze o nazwie My *cluster* w grupie zasobów o nazwie Moja *resourceName* przy użyciu *appgwId* identyfikatora zasobu, Application Gateway który został zapisany powyżej we wcześniejszym kroku. 


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Alternatywnie możesz przejść do klastra usługi AKS w portalu przy użyciu tego [linku](https://portal.azure.com/?feature.aksagic=true) i włączyć dodatek AGIC na karcie sieci w klastrze. Wybierz istniejące Application Gateway z menu rozwijanego, po wybraniu którego Application Gateway dodatek powinien być elementem docelowym. 

![Application Gateway portalu transferu danych przychodzących](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>Następne kroki
- [**Application Gateway Rozwiązywanie problemów z kontrolerem**](ingress-controller-troubleshoot.md)danych przychodzących: Przewodnik rozwiązywania problemów z AGIC 
- [**Application Gateway adnotacje kontrolera**](ingress-controller-annotations.md)transferu danych przychodzących: Lista adnotacji w AGIC 