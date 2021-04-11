---
title: Tworzenie i używanie domeny niestandardowej
description: Połącz domenę niestandardową z maszyną wirtualną na platformie Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: c4797420904b6dc03550f658c2aa950a4de99c9c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107250999"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Dodaj domenę niestandardową do maszyny wirtualnej lub zasobu platformy Azure

Na platformie Azure istnieje wiele sposobów łączenia domeny niestandardowej z maszyną wirtualną lub zasobem. Dla każdego zasobu z publicznym adresem IP (maszyna wirtualna, Load Balancer, Application Gateway) najbardziej prostym sposobem jest utworzenie zestawu rekordów w odpowiednim rejestratorze domeny. 

## <a name="prerequisites"></a>Wymagania wstępne 
- Wymagana jest maszyna wirtualna z serwerem sieci Web, na którym działa program. Możesz użyć [przewodnika Szybki Start](./linux/quick-create-cli.md) , aby utworzyć maszynę wirtualną i dodać Nginx.

- Maszyna wirtualna musi być dostępna dla sieci Web (Otwórz port 80 lub 443). W celu zapewnienia bezpieczniejszego wdrożenia należy umieścić maszynę wirtualną za modułem równoważenia obciążenia lub Application Gateway. Aby uzyskać więcej informacji, zobacz [Szybki Start: Load Balancer](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal?tabs=option-1-create-load-balancer-standard).

- Mieć istniejącą domenę i dostęp do ustawień DNS. Aby uzyskać więcej informacji, zobacz [kupowanie domeny niestandardowej dla Azure App Service](../app-service/manage-custom-dns-buy-domain.md).


## <a name="add-custom-domain-to-vm-public-ip-address"></a>Dodaj domenę niestandardową do publicznego adresu IP maszyny wirtualnej

Podczas tworzenia maszyny wirtualnej w Azure Portal zostanie automatycznie utworzony publiczny zasób IP dla maszyny wirtualnej. Publiczny adres IP jest pokazywany na stronie Przegląd maszyny wirtualnej. 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="Pokazuje publiczny adres IP w sekcji podstawowe strony Przegląd maszyny wirtualnej.":::

W przypadku wybrania adresu IP można zobaczyć więcej informacji na jego temat. Upewnij się, że **przypisanie adresu IP** jest ustawione na wartość **static (statyczny**). Statyczny adres IP nie ulegnie zmianie, jeśli maszyna wirtualna lub zasób zostanie ponownie uruchomiony lub zamknięty.

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="Pokazuje konfigurację publicznego adresu IP, aby zobaczyć, czy adres IP jest statyczny.":::

Jeśli adres IP nie jest statyczny, konieczne będzie utworzenie nazwy FQDN. 

1. Wybierz maszynę wirtualną w portalu. 
1. W menu po lewej stronie wybierz pozycję **Właściwości** .
1. W obszarze **publiczna nazwa ADDRESS\DNS IP etykieta**, wybierz adres IP.
2. W obszarze **etykieta nazwy DNS** wprowadź prefiks, którego chcesz użyć.
3. Wybierz pozycję **Zapisz** w górnej części strony.
4. Wybierz pozycję **Przegląd** w menu po lewej stronie, aby wrócić do bloku przegląd maszyny wirtualnej.
5. Sprawdź, czy *nazwa DNS* jest wyświetlana poprawnie. 

Otwórz przeglądarkę i wprowadź adres IP lub nazwę FQDN i sprawdź, czy jest wyświetlana zawartość sieci Web działająca na maszynie wirtualnej.
 
Po sprawdzeniu statycznego adresu IP lub nazwy FQDN przejdź do dostawcy domeny i przejdź do ustawień DNS.

Po dodaniu *rekordu A* wskazujący na publiczny adres IP lub nazwę FQDN. Na przykład procedura rejestratora domen GoDaddy wygląda następująco:
1. Zaloguj się i wybierz domenę niestandardową do użycia.
2. W sekcji **domeny** wybierz pozycję **Zarządzaj wszystkimi**, a następnie wybierz pozycję **DNS | Zarządzanie strefami**.
3. W obszarze **Nazwa domeny** wprowadź domenę niestandardową, a następnie wybierz pozycję **Wyszukaj**.
4. Na stronie zarządzania systemem DNS wybierz pozycję Dodaj, a następnie na liście Typ wybierz pozycję A.
5. Wypełnij pola wpisu:
    - Typ **: pozostaw zaznaczone** .
    - Host: wprowadź **@**
    - Wskazuje na: wprowadź publiczny adres IP lub nazwę FQDN maszyny wirtualnej. 
    - Czas wygaśnięcia: Wybierz jedną godzinę.
6. Wybierz pozycję **Zapisz**.

Wpis rekordu A zostanie dodany do tabeli rekordów DNS.
 
Po utworzeniu rekordu zwykle trwa około godziny w przypadku propagacji DNS, ale czasami może trwać do 48 godzin. 


 
## <a name="next-steps"></a>Następne kroki
[Omówienie kończenia protokołu TLS i kompleksowej usługi TLS z Application Gateway](../application-gateway/ssl-overview.md).

 
