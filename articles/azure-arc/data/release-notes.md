---
title: Usługi danych z włączoną funkcją Azure Arc — informacje o wersji
description: Najnowsze informacje o wersji
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3c20bbd3ab02cd1eccd00e2d36c14eebf2f63205
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360319"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Informacje o wersji — usługi danych z obsługą usługi Azure ARC (wersja zapoznawcza)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>Wrzesień 2020 r.

Usługi danych z włączonym usługą Azure Arc są udostępniane w publicznej wersji zapoznawczej. Usługi danych z włączonymi łukiemmi umożliwiają zarządzanie usługami danych w dowolnym miejscu.

- Wystąpienie zarządzane SQL
- PostgreSQL

Aby uzyskać instrukcje, zobacz [co to są usługi danych z włączonym usługą Azure Arc?](overview.md)

### <a name="known-issues"></a>Znane problemy

Do tej wersji mają zastosowanie następujące problemy:

* **Usuwanie grupy serwerów PostgreSQL w ramach skalowania**: Jeśli zmieniono konfigurację grupy serwerów lub wystąpienia, poczekaj na zakończenie operacji edycji przed usunięciem grupy serwerów PostgreSQL.

* ** `azdata notebook run` może się nie powieść**: Aby obejść ten problem, uruchom polecenie `azdata notebook run` w środowisku wirtualnym języka Python. Ten problem występuje również w przypadku nieudanej próby utworzenia wystąpienia zarządzanego SQL lub grupy serwerów PostgreSQL w ramach skalowania przy użyciu Kreatora wdrażania Azure Data Studio. W takim przypadku można otworzyć Notes i kliknąć przycisk **Uruchom wszystko** w górnej części notesu.

## <a name="next-steps"></a>Następne kroki

> **Chcesz, aby wypróbować coś?**  
> Szybko Rozpocznij pracę z usługą [Azure Arc szybko Rozpocznij pracę](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) w usłudze Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (eks), aparacie Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure.

[Instalowanie narzędzi klienta](install-client-tools.md)

[Utwórz kontroler danych usługi Azure Arc](create-data-controller.md) (najpierw wymagane jest zainstalowanie narzędzi klienta)

[Tworzenie wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc](create-sql-managed-instance.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)

[Tworzenie grupy serwerów z Azure Database for PostgreSQLm skalowaniem w usłudze Azure Arc](create-postgresql-hyperscale-server-group.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)

## <a name="known-limitations-and-issues"></a>Znane ograniczenia i problemy

- Nazwy wystąpień zarządzanych SQL nie mogą być dłuższe niż 13 znaków
- Brak uaktualnienia w miejscu dla kontrolera danych usługi Azure ARC lub wystąpienia bazy danych.
- Obrazy kontenerów usług danych z obsługą usługi Arc nie są podpisane.  Może być konieczne skonfigurowanie węzłów Kubernetes w taki sposób, aby zezwalały na ściąganie niepodpisanych obrazów kontenerów.  Na przykład jeśli używasz platformy Docker jako środowiska uruchomieniowego kontenera, możesz ustawić zmienną środowiskową DOCKER_CONTENT_TRUST = 0 i uruchomić ponownie.  Inne środowiska uruchomieniowe kontenera mają podobne opcje, tak jak na platformie [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Nie można utworzyć grup serwera SQL z włączonym usługą Azure Arc ani na podstawie Azure Portal.
- Na razie w przypadku korzystania z systemu plików NFS należy ustawić allowRunAsRoot na wartość true w pliku profilu wdrożenia przed utworzeniem kontrolera danych usługi Azure Arc.
- Tylko uwierzytelnianie logowania SQL i PostgreSQL.  Brak obsługi Azure Active Directory ani Active Directory.
- Tworzenie kontrolera danych w systemie OpenShift wymaga obniżonych ograniczeń zabezpieczeń.  Szczegółowe informacje znajdują się w dokumentacji.
- Skalowanie liczby węzłów roboczych Postgres _w dół_ nie jest obsługiwane.
- Jeśli używasz aparatu usługi Azure Kubernetes (aparat AKS) na Azure Stack Hub z kontrolerem danych usługi Azure Arc i wystąpieniami baz danych, uaktualnienie do nowszej wersji Kubernetes nie jest obsługiwane. Odinstaluj kontroler danych usługi Azure Arc i wszystkie wystąpienia bazy danych przed uaktualnieniem klastra Kubernetes.
- Wersja zapoznawcza nie obsługuje wykonywania kopii zapasowych/przywracania dla aparatu Postgres w wersji 11. Obsługuje tylko tworzenie kopii zapasowych i przywracanie dla Postgres w wersji 12.
- Usługa Azure Kubernetes Service (AKS), klastry obejmujące [wiele stref dostępności](../../aks/availability-zones.md) nie są obecnie obsługiwane dla usług danych z obsługą usługi Azure Arc. Aby uniknąć tego problemu, podczas tworzenia klastra AKS w Azure Portal, jeśli wybierzesz region, w którym strefy są dostępne, wyczyść wszystkie strefy z kontrolki zaznaczenie. Zobacz poniższy obraz:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Wyczyść pola wyboru dla każdej strefy, aby określić brak.":::

  
