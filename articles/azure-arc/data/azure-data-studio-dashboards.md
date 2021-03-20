---
title: Pulpity nawigacyjne Azure Data Studio
description: Pulpity nawigacyjne Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a96be6d4da3d292b2e9881652aad28f318ccee8a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92107576"
---
# <a name="azure-data-studio-dashboards"></a>Pulpity nawigacyjne Azure Data Studio

[Azure Data Studio](/sql/azure-data-studio/what-is) zapewnia środowisko podobne do Azure Portal do wyświetlania informacji o zasobach usługi Azure Arc.  Te widoki są nazywane **pulpitami nawigacyjnymi** i mają układ i opcje podobne do tego, co można zobaczyć w odniesieniu do danego zasobu w Azure Portal, ale zapewniają elastyczność wyświetlania tych informacji lokalnie w danym środowisku, w przypadkach, gdy nie masz połączenia dostępnego na platformie Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connecting-to-a-data-controller"></a>Łączenie z kontrolerem danych

### <a name="prerequisites"></a>Wymagania wstępne

- Pobierz [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
- Rozszerzenie Azure Arc jest zainstalowane

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>Określ adres URL punktu końcowego interfejsu API serwera kontrolera danych

Najpierw należy połączyć Azure Data Studio z adresem URL punktu końcowego interfejsu API usługi kontrolera danych.

Aby uzyskać ten punkt końcowy, możesz uruchomić następujące polecenie:

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

Zobaczysz dane wyjściowe, które wyglądają następująco:

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

Jeśli używasz typu modułu równoważenia obciążenia, skopiuj zewnętrzny adres IP i numer portu. Jeśli używasz NodePort, użyj adresu IP serwera interfejsu API Kubernetes i numeru portu znajdującego się w kolumnie porty.

Teraz chcesz utworzyć adres URL do punktu końcowego, łącząc te informacje, takie jak:

```console
https://<ip address>:<port>

Example:
https://52.154.152.24:30080
```

Zwróć uwagę na adres IP, ponieważ będzie on używany w następnym kroku.

### <a name="connect"></a>Connect

1. Otwórz Azure Data Studio

1. Wybierz kartę **połączenia** po lewej stronie

Na dole rozwiń panel o nazwie **kontrolery usługi Azure Arc**.

Kliknij ikonę +, aby dodać nowe połączenie kontrolera danych.

W górnej części ekranu w palecie poleceń wprowadź adres URL, który został utworzony w kroku 1, a następnie kliknij przycisk ENTER.
Wprowadź nazwę użytkownika dla kontrolera danych.  Była to wartość nazwy użytkownika, która została przeniesiona podczas wdrażania kontrolera danych.  Kliknij klawisz ENTER.
Wprowadź hasło dla kontrolera danych.  Była to wartość hasła, która została przeniesiona podczas wdrażania kontrolera danych. Kliknij klawisz ENTER.

Teraz, gdy masz połączenie z kontrolerem danych, możesz wyświetlić pulpity nawigacyjne dla kontrolera danych i wszystkie wystąpienia zarządzane SQL lub zasoby grupy serwerów PostgreSQL w skali.

## <a name="view-the-data-controller-dashboard"></a>Wyświetlanie pulpitu nawigacyjnego kontrolera danych

Kliknij prawym przyciskiem myszy kontroler danych w panelu połączenia w panelu powiększanie **kontrolerów łuku** i wybierz polecenie **Zarządzaj**.

Tutaj można zobaczyć szczegóły dotyczące zasobu kontrolera danych, takie jak nazwa, region, tryb połączenia, Grupa zasobów, subskrypcja, punkt końcowy kontrolera i przestrzeń nazw.  Można również wyświetlić listę wszystkich zarządzanych zasobów bazy danych zarządzanych przez kontroler danych.

Zauważ, że układ jest podobny do tego, co może być widoczne w Azure Portal.

Wygodnie można uruchomić tworzenie wystąpienia zarządzanego SQL lub grupy serwerów PostgreSQL z skalowaniem przez kliknięcie przycisku + nowe wystąpienie.

Możesz również otworzyć Azure Portal w kontekście tego kontrolera danych, klikając przycisk Otwórz w Azure Portal.

## <a name="view-the-sql-managed-instance-dashboards"></a>Wyświetlanie pulpitów nawigacyjnych wystąpienia zarządzanego SQL

Jeśli zostały utworzone wystąpienia zarządzane przez usługę SQL, możesz je zobaczyć na liście w panelu połączenia w panelu połączeń kontrolerach danych platformy Azure, który można rozwinąć pod kontrolerem danych, który zarządza nimi.

Aby wyświetlić pulpit nawigacyjny wystąpienia zarządzanego SQL dla danego wystąpienia, kliknij prawym przyciskiem myszy wystąpienie i wybierz polecenie Zarządzaj.

Panel połączenie zostanie wyświetlony po prawej stronie i pojawi się monit o podanie nazwy logowania/hasła w celu nawiązania połączenia z tym wystąpieniem SQL. Jeśli znasz informacje o połączeniu, możesz je wprowadzić i kliknąć przycisk Połącz.  Jeśli nie wiesz, możesz kliknąć przycisk Anuluj.  W obu przypadkach zostanie on przeniesiony do pulpitu nawigacyjnego, gdy zostanie zamknięty panel połączenie.

Na karcie Omówienie można wyświetlić szczegółowe informacje o wystąpieniu zarządzanym SQL, takie jak grupa zasobów, kontroler danych, Identyfikator subskrypcji, stan, region i inne.  Możesz również kliknąć link, który można kliknij, aby przejść do pulpitów nawigacyjnych Grafana lub Kibana w kontekście tego wystąpienia zarządzanego SQL.

Jeśli masz możliwość nawiązania połączenia z wystąpieniem programu SQL Manage, możesz zobaczyć dodatkowe informacje tutaj.

Możesz usunąć wystąpienie zarządzane SQL z tego miejsca lub otworzyć Azure Portal, aby wyświetlić wystąpienie zarządzane SQL w Azure Portal.

Jeśli klikniesz kartę parametry połączenia po lewej stronie, zobaczysz listę wstępnie skonstruowanych parametrów połączenia dla tego wystąpienia zarządzanego SQL, dzięki czemu można łatwo kopiować/wklejać do różnych innych aplikacji lub kodu.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>Wyświetlanie pulpitów nawigacyjnych grup serwerów PostgreSQL

Jeśli zostały utworzone grupy serwerów PostgreSQL w skali, możesz je zobaczyć na liście w panelu połączenia na kontrolerze danych platformy Azure rozwiń panel, poniżej kontrolera danych, który zarządza nimi.

Aby wyświetlić pulpit nawigacyjny grupy serwerów PostgreSQL dla danej grupy serwerów, kliknij prawym przyciskiem myszy grupę serwerów, a następnie wybierz polecenie Zarządzaj.

Na karcie Omówienie można wyświetlić szczegółowe informacje o grupie serwerów, takie jak grupa zasobów, kontroler danych, Identyfikator subskrypcji, stan, region i inne.  Możesz również kliknąć link, który można kliknij, aby przejść do pulpitów nawigacyjnych Grafana lub Kibana w kontekście tej grupy serwerów.

Możesz usunąć grupę serwerów z tego miejsca lub otworzyć Azure Portal, aby wyświetlić grupę serwerów w Azure Portal.

Po kliknięciu karty parametry połączenia po lewej stronie zostanie wyświetlona lista wstępnie skonstruowanych parametrów połączenia dla tej grupy serwerów, co ułatwia kopiowanie/wklejanie w różnych innych aplikacjach lub kodzie.

Jeśli klikniesz kartę właściwości po lewej stronie, możesz wyświetlić dodatkowe szczegóły.

Jeśli klikniesz kartę Kondycja zasobów po lewej stronie, zobaczysz aktualną kondycję wysokiego poziomu tej grupy serwerów.

Jeśli klikniesz kartę diagnozowanie i rozwiązywanie problemów po lewej stronie, możesz uruchomić Notes rozwiązywania problemów z programem PostgreSQL.

Jeśli klikniesz kartę nowe żądanie obsługi po lewej stronie, możesz uruchomić Azure Portal w kontekście do grupy serwerów i utworzyć żądanie pomocy technicznej platformy Azure.