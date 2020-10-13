---
title: Konfigurowanie kontroli dostępu do sieci
titleSuffix: Azure SignalR Service
description: Skonfiguruj kontrolę dostępu do sieci dla usługi Azure Signal.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 72532029b2d9258dba7dea82bb5c5fc8b2673300
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536226"
---
# <a name="configure-network-access-control"></a>Konfigurowanie kontroli dostępu do sieci

Usługa Azure Signal Service pozwala zabezpieczyć i kontrolować poziom dostępu do punktu końcowego usługi w oparciu o typ żądania oraz podzestaw używanych sieci. W przypadku skonfigurowania reguł sieci tylko aplikacje żądające danych za pośrednictwem określonego zestawu sieci mogą uzyskiwać dostęp do usługi Azure Signaler.

Usługa Azure Signal Service ma publiczny punkt końcowy, który jest dostępny za pomocą Internetu. Możesz również utworzyć [prywatne punkty końcowe dla usługi Azure Signal Service](howto-private-endpoints.md). Prywatny punkt końcowy przypisuje prywatny adres IP z sieci wirtualnej do usługi Azure Signal i zabezpiecza cały ruch między siecią wirtualną a usługą Azure Signal link za pośrednictwem prywatnego łącza. Kontrola dostępu do sieci usługi Azure Signal Service zapewnia kontrolę dostępu dla zarówno publicznego punktu końcowego, jak i prywatnych punktów końcowych.

Opcjonalnie możesz zezwolić na dostęp lub odmówić określonych typów żądań dla publicznego punktu końcowego i każdego prywatnego punktu końcowego. Na przykład można zablokować wszystkie [połączenia serwera](signalr-concept-internals.md#server-connections) z publicznego punktu końcowego i upewnić się, że pochodzą one tylko z określonej sieci wirtualnej.

Aplikacja, która uzyskuje dostęp do usługi sygnalizującej platformy Azure, gdy reguły kontroli dostępu do sieci nadal wymagają odpowiedniej autoryzacji dla żądania.

## <a name="scenario-a---no-public-traffic"></a>Scenariusz A — brak ruchu publicznego

Aby całkowicie odmówić całego ruchu publicznego, należy najpierw skonfigurować regułę sieci publicznej w taki sposób, aby zezwalała na brak typu żądania. Następnie należy skonfigurować reguły zezwalające na dostęp do ruchu pochodzącego z określonych sieci wirtualnych. Ta konfiguracja umożliwia tworzenie bezpiecznej granicy sieci dla aplikacji.

## <a name="scenario-b---only-client-connections-from-public-network"></a>Scenariusz B — tylko połączenia klienckie z sieci publicznej

W tym scenariuszu regułę sieci publicznej można skonfigurować tak, aby zezwalała na [połączenia klienckie](signalr-concept-internals.md#client-connections) tylko z sieci publicznej. Następnie można skonfigurować reguły sieci prywatnej w taki sposób, aby zezwalały na inne typy żądań pochodzących z określonej sieci wirtualnej. Ta konfiguracja powoduje ukrycie serwerów aplikacji z sieci publicznej i ustanawianie bezpiecznych połączeń między serwerami aplikacji i usługą Azure Signal Service.

## <a name="managing-network-access-control"></a>Zarządzanie kontrolą dostępu do sieci

Za pomocą Azure Portal można zarządzać kontrolą dostępu do sieci dla usługi Azure Signal Service.

### <a name="azure-portal"></a>Azure Portal

1. Przejdź do usługi Azure sygnalizującej, która ma zostać zabezpieczona.

1. Kliknij menu Ustawienia o nazwie **Kontrola dostępu do sieci**.

    ![Lista ACL sieci w portalu](media/howto-network-access-control/portal.png)

1. Aby edytować akcję domyślną, przełącz przycisk **Zezwalaj/Odmów** .

    > [!TIP]
    > Akcja domyślna to Akcja podejmowana w przypadku braku dopasowania reguły listy ACL. Na przykład jeśli akcja domyślna to **Odmów**, wówczas typy żądań, które nie są jawnie zatwierdzone poniżej, będą odrzucane.

1. Aby edytować regułę sieci publicznej, wybierz pozycję dozwolone typy żądań w obszarze **Sieć publiczna**.

    ![Edytowanie listy ACL sieci publicznej w portalu ](media/howto-network-access-control/portal-public-network.png)

1. Aby edytować reguły sieci prywatnych punktów końcowych, wybierz dozwolone typy żądań w każdym wierszu w obszarze **połączenia prywatnych punktów końcowych**.

    ![Edytowanie listy ACL prywatnych punktów końcowych w portalu ](media/howto-network-access-control/portal-private-endpoint.png)

1. Aby zastosować zmiany, kliknij pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [łączu prywatnym platformy Azure](/azure/private-link/private-link-overview).
