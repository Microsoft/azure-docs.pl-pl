---
title: Tworzenie i usuwanie prywatnych punktów końcowych w klastrze Azure Stream Analytics
description: Dowiedz się, jak zarządzać prywatnymi punktami końcowymi w klastrze Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: cff02bb4b7d0f7e969589a9977a89f89e2674ffc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019418"
---
# <a name="create-and-delete-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Tworzenie i usuwanie prywatnych punktów końcowych w klastrze Azure Stream Analytics

Zadania usługi Azure Stream Analytics uruchomione w klastrze można połączyć z zasobami wejściowymi i wyjściowymi, które znajdują się za zaporą lub siecią wirtualną platformy Azure. Najpierw należy utworzyć prywatny punkt końcowy dla zasobu, takiego jak usługa Azure Event Hub lub Azure SQL Database, w klastrze usługi Stream Analytics. Następnie należy zatwierdzić połączenie prywatnego punktu końcowego z zasobu wejściowego lub wyjściowego.

Po zatwierdzeniu połączenia każde zadanie uruchomione w klastrze Stream Analytics będzie miało dostęp do zasobu za pomocą prywatnego punktu końcowego. W tym artykule opisano sposób tworzenia i usuwania prywatnych punktów końcowych w klastrze usługi Stream Analytics. Możesz tworzyć prywatne punkty końcowe dla Azure SQL Database, Azure Storage, Azure Data Lake Storage Gen2, Azure Event Hub i Azure Service Bus. Prywatne punkty końcowe dla innych usług zostaną dodane wkrótce. 

## <a name="create-private-endpoint-in-stream-analytics-cluster"></a>Tworzenie prywatnego punktu końcowego w klastrze Stream Analytics

W tej sekcji dowiesz się, jak utworzyć prywatny punkt końcowy w klastrze Stream Analytics.

1. W Azure Portal zlokalizuj i wybierz klaster Stream Analytics.

1. W obszarze **Ustawienia** wybierz pozycję **prywatne punkty końcowe**.

1. Wybierz pozycję **Dodaj prywatny punkt końcowy** i wprowadź poniższe informacje, aby wybrać zasób do bezpiecznego dostępu za pomocą prywatnego punktu końcowego.

   |Ustawienie|Wartość|
   |---|---|
   |Nazwa|Wprowadź dowolną nazwę prywatnego punktu końcowego. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę.|
   |Metoda połączenia|Wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu**.<br><br>Możesz wybrać jeden z zasobów, aby bezpiecznie połączyć się z usługą za pomocą prywatnego punktu końcowego, lub połączyć się z zasobem innej osoby przy użyciu identyfikatora zasobu lub aliasu, który zostały Ci udostępnione.|
   |Subskrypcja|Wybierz subskrypcję.|
   |Typ zasobu|Wybierz [Typ zasobu, który jest mapowany na zasób](../private-link/private-endpoint-overview.md#private-link-resource).|
   |Zasób|Wybierz zasób, z którym chcesz nawiązać połączenie za pomocą prywatnego punktu końcowego.|
   |Podzasób docelowy|Typ zasobu zasobu wybranego powyżej, który będzie mógł uzyskać dostęp do prywatnego punktu końcowego.|

   ![Środowisko tworzenia prywatnego punktu końcowego](./media/private-endpoints/create-private-endpoint.png)

1. Zatwierdź połączenie z zasobu docelowego. Jeśli na przykład w poprzednim kroku został utworzony prywatny punkt końcowy do wystąpienia Azure SQL Database, należy przejść do tego wystąpienia SQL Database i zobaczyć oczekujące połączenie, które ma zostać zatwierdzone. Wyświetlenie żądania połączenia może potrwać kilka minut.

    ![Zatwierdź prywatny punkt końcowy](./media/private-endpoints/approve-private-endpoint.png)

1. Można wrócić do klastra Stream Analytics, aby zobaczyć zmianę stanu od **oczekującej zgody klienta** na **OCZEKUJĄCą konfigurację DNS** w celu **skonfigurowania** jej w ciągu kilku minut.

## <a name="delete-a-private-endpoint-in-a-stream-analytics-cluster"></a>Usuwanie prywatnego punktu końcowego w klastrze Stream Analytics

1. W Azure Portal zlokalizuj i wybierz klaster Stream Analytics.

1. W obszarze **Ustawienia** wybierz pozycję **prywatne punkty końcowe**.

1. Wybierz prywatny punkt końcowy, który chcesz usunąć, i wybierz pozycję **Usuń**.

   ![Usuń prywatny punkt końcowy](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Następne kroki

Teraz masz przegląd sposobu zarządzania prywatnymi punktami końcowymi w klastrze Azure Stream Analytics. Następnie można dowiedzieć się, jak skalować klastry i uruchamiać zadania w klastrze:

* [Skalowanie klastra Azure Stream Analytics](scale-cluster.md)
* [Zarządzanie zadaniami Stream Analytics w klastrze Stream Analytics](manage-jobs-cluster.md)
