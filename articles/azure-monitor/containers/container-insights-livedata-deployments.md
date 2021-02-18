---
title: Wyświetl Azure Monitor dla wdrożeń kontenerów (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule opisano widok Kubernetes wdrożeń w czasie rzeczywistym bez użycia polecenia kubectl w Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 2f1eac82ce67818c7bf86ce3ca8924155d8ee2aa
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616263"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Jak wyświetlać wdrożenia (wersja zapoznawcza) w czasie rzeczywistym

Za pomocą Azure Monitor dla kontenerów Funkcja Wyświetl wdrożenia (wersja zapoznawcza) emuluje bezpośredni dostęp do obiektów wdrożenia Kubernetes w czasie rzeczywistym przez udostępnienie `kubeclt get deployments` `kubectl describe deployment {your deployment}` poleceń i.

>[!NOTE]
>Klastry AKS włączone jako [klastry prywatne](https://azure.microsoft.com/updates/aks-private-cluster/) są nieobsługiwane w przypadku tej funkcji. Ta funkcja używa bezpośrednio dostępu do interfejsu API Kubernetes za pomocą serwera proxy z przeglądarki. Włączenie zabezpieczeń sieci w celu blokowania interfejsu API Kubernetes z tego serwera proxy spowoduje zablokowanie tego ruchu.

Aby dowiedzieć się więcej, zapoznaj się z dokumentacją Kubernetes dotyczącą [wdrożeń](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

## <a name="how-it-works"></a>Jak to działa

Funkcja dane dynamiczne (wersja zapoznawcza) bezpośrednio uzyskuje dostęp do interfejsu API Kubernetes. dodatkowe informacje o modelu uwierzytelniania można znaleźć [tutaj](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

Funkcja wdrożenia (wersja zapoznawcza) wykonuje jednorazowe (odświeżane) obciążenie w punkcie końcowym wdrożenia `/apis/apps/v1/deployments` . Umożliwia wybranie danego wdrożenia i załadowanie szczegółowych informacji o tym konkretnym wdrożeniu do punktu końcowego wdrożenia `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}` .

Wybranie opcji **Odśwież** w lewym górnym rogu strony spowoduje odświeżenie listy wdrożenia. To symuluje ponowne uruchomienie `kubectl` polecenia.

>[!IMPORTANT]
>Podczas wykonywania tej funkcji żadne dane nie są trwale przechowywane. Wszystkie informacje przechwycone podczas sesji są usuwane, gdy zamkniesz przeglądarkę lub opuścisz ją.

>[!NOTE]
>Nie można przypiąć danych na żywo (wersja zapoznawcza) z konsoli programu do pulpitu nawigacyjnego platformy Azure.

## <a name="deployments-describe"></a>Opis wdrożeń

Aby wyświetlić szczegółowe informacje dotyczące wdrożenia, które jest równoważne z `kubectl describe deployment` , wykonaj następujące czynności.

1. W Azure Portal przejdź do grupy zasobów klastra AKS i wybierz zasób AKS.

2. Na pulpicie nawigacyjnym klastra AKS, w obszarze **monitorowanie** po lewej stronie, wybierz pozycję **szczegółowe informacje**.

3. Wybierz kartę **wdrożenia (wersja zapoznawcza)** .

    ![Widok wdrożeń w Azure Portal](./media/container-insights-livedata-deployments/deployment-view.png)

Widok przedstawia listę wszystkich uruchomionych wdrożeń wraz z obszarem nazw i innymi szczegółowymi informacjami, które emulują wykonywanie polecenia `kubectl get deployments –all-namespaces` . Wyniki można sortować, wybierając jedną z kolumn.

![Szczegóły okienka właściwości wdrożenia](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Po wybraniu wdrożenia z listy okienko właściwości zostanie automatycznie wyświetlone po prawej stronie. Zawiera informacje dotyczące wybranego wdrożenia, które można wyświetlić, jeśli uruchomiono polecenie `kubectl describe deployment {deploymentName}` . Być może zauważono, że w opisie informacji brakuje pewnych szczegółów. W szczególności brakuje **szablonu** . Wybranie zakładki **RAW** pozwala przejść do szczegółów nieanalizowanych opisów.

![Szczegóły szczegółów okienka właściwości wdrożenia](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Podczas przeglądania szczegółów wdrożenia można zobaczyć dzienniki kontenerów i zdarzenia w czasie rzeczywistym. Wybierz pozycję **Pokaż konsolę na żywo** i konsolę dane dynamiczne (wersja zapoznawcza) pojawi się poniżej siatki wdrożenia, w której można wyświetlić dane dziennika na żywo w strumieniu ciągłym. Jeśli wskaźnik stanu pobierania zawiera zielony znacznik wyboru, który znajduje się po prawej stronie okienka, oznacza to, że dane mogą być pobierane i rozpoczyna przesyłanie strumieniowe do konsoli programu.

Można również filtrować według przestrzeni nazw lub zdarzeń na poziomie klastra. Aby dowiedzieć się więcej na temat wyświetlania danych w czasie rzeczywistym w konsoli programu, zobacz [Wyświetlanie danych na żywo (wersja zapoznawcza) za pomocą Azure monitor dla kontenerów](container-insights-livedata-overview.md).

![Wdrożenia wyświetlanie danych na żywo w konsoli](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Następne kroki

- Aby kontynuować uczenie się, jak używać Azure Monitor i monitorować inne aspekty klastra AKS, zobacz [Wyświetlanie usługi Azure Kubernetes Service Health](container-insights-analyze.md).

- Wyświetl [przykłady zapytań dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby wyświetlić wstępnie zdefiniowane zapytania i przykłady do tworzenia alertów, wizualizacji lub przeprowadzenia dalszej analizy klastrów.
