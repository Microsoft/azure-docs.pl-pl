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
ms.openlocfilehash: 8100d9e12f107e0c4598876c46453b46c6ee4d0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122004"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Znane problemy — usługi danych z obsługą usługi Azure ARC (wersja zapoznawcza)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>Luty 2021 r.

- Tryb podłączonego klastra jest wyłączony

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

### <a name="postgresql"></a>PostgreSQL

- Usługa Azure ARC z włączonym PostgreSQLem nie zwraca niedokładnego komunikatu o błędzie, gdy nie można go przywrócić do punktu względnego w czasie. Jeśli na przykład do przywracania określono punkt w czasie, który jest starszy niż zawiera kopie zapasowe, przywracanie zakończy się niepowodzeniem z komunikatem o błędzie: `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
W takim przypadku należy ponownie uruchomić polecenie po wskazaniu punktu w czasie, który znajduje się w zakresie dat, dla których istnieją kopie zapasowe. Ten zakres zostanie określony przez wystawienie kopii zapasowych i przejrzenie dat, w których zostały wykonane.
- Przywracanie do punktu w czasie jest obsługiwane tylko w grupach serwerów. Serwer docelowy operacji przywracania do punktu w czasie nie może być serwerem, z którego została wykonana kopia zapasowa. Musi to być inna grupa serwerów. Jednak pełne przywracanie jest obsługiwane przez tę samą grupę serwerów.
- Podczas wykonywania pełnego przywracania wymagany jest identyfikator kopii zapasowej. Domyślnie, jeśli nie jest wskazywany identyfikator kopii zapasowej, zostanie użyta najnowsza kopia zapasowa. To nie działa w tej wersji.

## <a name="next-steps"></a>Następne kroki

> **Chcesz, aby wypróbować coś?**  
> Szybko Rozpocznij pracę dzięki usłudze [Azure Arc szybko Rozpocznij pracę](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) na AKS, AWS Elastic Kubernetes Service (eks), usłudze Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure.

- [Instalowanie narzędzi klienta](install-client-tools.md)
- [Utwórz kontroler danych usługi Azure Arc](create-data-controller.md) (najpierw wymagane jest zainstalowanie narzędzi klienta)
- [Tworzenie wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc](create-sql-managed-instance.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)
- [Tworzenie grupy serwerów z Azure Database for PostgreSQLm skalowaniem w usłudze Azure Arc](create-postgresql-hyperscale-server-group.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)
- [Dostawcy zasobów dla usług platformy Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Informacje o wersji — usługi danych z obsługą usługi Azure ARC (wersja zapoznawcza)](release-notes.md)
