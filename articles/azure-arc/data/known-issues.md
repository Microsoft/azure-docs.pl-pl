---
title: Usługi danych z włączonym usługą Azure Arc — znane problemy
description: Najnowsze znane problemy
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: ee652047a33d73ece2d7648905fa590d90b1fb2f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029509"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Znane problemy — usługi danych z obsługą usługi Azure ARC (wersja zapoznawcza)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>Marzec 2021

### <a name="data-controller"></a>Kontroler danych

- W trybie bezpośredniego łączenia można utworzyć kontroler danych z Azure Portal. Wdrażanie z innymi narzędziami usług danych z funkcją Azure Arc nie są obsługiwane. W tej wersji nie można wdrożyć kontrolera danych w trybie bezpośredniego łączenia z żadnym z następujących narzędzi.
   - Azure Data Studio
   - Interfejs wiersza polecenia platformy Azure ( `azdata` )
   - Narzędzia natywne Kubernetes

   [Wdrażanie usługi Azure Arc Data Controller | Tryb łączenia bezpośredniego](deploy-data-controller-direct-mode.md) wyjaśnia, jak utworzyć kontroler danych w portalu. 

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Usługa Azure ARC z włączonym skalowaniem PostgreSQL

- Wdrożenie grupy serwerów Postgres w systemie Arc w kontrolerze danych z włączonym trybem bezpośredniego połączenia nie jest obsługiwane.
- Przekazywanie nieprawidłowej wartości do `--extensions` parametru podczas edytowania konfiguracji grupy serwerów w celu włączenia dodatkowych rozszerzeń powoduje nieprawidłowe Resetowanie listy włączonych rozszerzeń do tego, co było w czasie tworzenia grupy serwerów i uniemożliwia użytkownikowi tworzenie dodatkowych rozszerzeń. Jedyne obejście dostępne tylko wtedy, gdy to nastąpi, usunięcie grupy serwerów i jej ponowne wdrożenie.

## <a name="february-2021"></a>Luty 2021 r.

### <a name="data-controller"></a>Kontroler danych

- Tryb klastra bezpośredniego łączenia jest wyłączony

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Usługa Azure ARC z włączonym skalowaniem PostgreSQL

- Przywracanie do punktu w czasie nie jest obsługiwane przez teraz w magazynie NFS.
- Nie można jednocześnie włączyć i skonfigurować rozszerzenia pg_cron. W tym celu należy użyć dwóch poleceń. Jedno polecenie umożliwiające włączenie go i jedno polecenie w celu jego skonfigurowania. 

   Na przykład:
   ```console
   § azdata arc postgres server edit -n myservergroup --extensions pg_cron 
   § azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
   ```

   Pierwsze polecenie wymaga ponownego uruchomienia grupy serwerów. Dlatego przed wykonaniem drugiego polecenia upewnij się, że stan grupy serwerów przeszedł z aktualizacji na gotowe. Jeśli wykonujesz drugie polecenie przed ukończeniem ponownego uruchomienia, zakończy się niepowodzeniem. W takim przypadku po prostu poczekaj chwilę, a następnie ponownie wykonaj drugie polecenie.

## <a name="introduced-prior-to-february-2021"></a>Wprowadzono przed luty 2021

### <a name="data-controller"></a>Kontroler danych

- W usłudze Azure Kubernetes Service (AKS), Kubernetes wersja 1.19. x nie jest obsługiwana.
- W systemie Kubernetes 1,19 `containerd` nie jest obsługiwana.
- Zasób kontrolera danych na platformie Azure jest obecnie zasobem platformy Azure. Wszystkie aktualizacje, takie jak usuwanie, nie są propagowane z powrotem do klastra Kubernetes.
- Nazwy wystąpień nie mogą być dłuższe niż 13 znaków
- Brak uaktualnienia w miejscu dla kontrolera danych usługi Azure ARC lub wystąpienia bazy danych.
- Obrazy kontenerów usług danych z obsługą usługi Arc nie są podpisane.  Może być konieczne skonfigurowanie węzłów Kubernetes w taki sposób, aby zezwalały na ściąganie niepodpisanych obrazów kontenerów.  Na przykład jeśli używasz platformy Docker jako środowiska uruchomieniowego kontenera, możesz ustawić zmienną środowiskową DOCKER_CONTENT_TRUST = 0 i uruchomić ponownie.  Inne środowiska uruchomieniowe kontenera mają podobne opcje, tak jak na platformie [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Nie można utworzyć grup serwera SQL z włączonym usługą Azure Arc ani na podstawie Azure Portal.
- Na razie w przypadku korzystania z systemu plików NFS `allowRunAsRoot` `true` przed utworzeniem kontrolera danych usługi Azure Arc należy ustawić wartość w pliku profilu wdrożenia.
- Tylko uwierzytelnianie logowania SQL i PostgreSQL.  Brak obsługi Azure Active Directory ani Active Directory.
- Tworzenie kontrolera danych w systemie OpenShift wymaga obniżonych ograniczeń zabezpieczeń.  Szczegółowe informacje znajdują się w dokumentacji.
- Jeśli używasz aparatu usługi Azure Kubernetes Service (AKS) na Azure Stack Hub z kontrolerem danych usługi Azure Arc i wystąpieniami baz danych, uaktualnienie do nowszej wersji Kubernetes nie jest obsługiwane. Odinstaluj kontroler danych usługi Azure Arc i wszystkie wystąpienia bazy danych przed uaktualnieniem klastra Kubernetes.
- Klastry AKS obejmujące [wiele stref dostępności](../../aks/availability-zones.md) nie są obecnie obsługiwane dla usług danych z obsługą usługi Azure Arc. Aby uniknąć tego problemu, podczas tworzenia klastra AKS w Azure Portal, jeśli wybierzesz region, w którym strefy są dostępne, wyczyść wszystkie strefy z kontrolki zaznaczenie. Zobacz poniższy obraz:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Wyczyść pola wyboru dla każdej strefy, aby określić brak.":::


## <a name="next-steps"></a>Następne kroki

> **Chcesz, aby wypróbować coś?**  
> Szybko Rozpocznij pracę dzięki usłudze [Azure Arc szybko Rozpocznij pracę](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) na AKS, AWS Elastic Kubernetes Service (eks), usłudze Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure.

- [Instalowanie narzędzi klienta](install-client-tools.md)
- [Utwórz kontroler danych usługi Azure Arc](create-data-controller.md) (najpierw wymagane jest zainstalowanie narzędzi klienta)
- [Tworzenie wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc](create-sql-managed-instance.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)
- [Tworzenie grupy serwerów z Azure Database for PostgreSQLm skalowaniem w usłudze Azure Arc](create-postgresql-hyperscale-server-group.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)
- [Dostawcy zasobów dla usług platformy Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Informacje o wersji — usługi danych z obsługą usługi Azure ARC (wersja zapoznawcza)](release-notes.md)
