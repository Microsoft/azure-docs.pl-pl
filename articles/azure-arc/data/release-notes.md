---
title: Usługi danych z włączoną funkcją Azure Arc — informacje o wersji
description: Najnowsze informacje o wersji
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: 2da8bd0b36b553a4b5f85b6f79987ab1a7b8d5a7
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286572"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Informacje o wersji — usługi danych z obsługą usługi Azure ARC (wersja zapoznawcza)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>Październik 2020 r. 

Numer wersji interfejsu wiersza polecenia platformy Azure ( `azdata` ): 20.2.3. Pobierz pod adresem [https://aka.ms/azdata](https://aka.ms/azdata) .

### <a name="breaking-changes"></a>Zmiany powodujące niezgodność

W tej wersji wprowadzono następujące istotne zmiany: 

* Pliki definicji zasobów niestandardowych PostgreSQL (CRD) zamieniają `shards` nazwę terminu na `workers` . Ten termin ( `workers` ) jest zgodny z nazwą parametru wiersza polecenia.

* `azdata arc postgres server delete` monituje o potwierdzenie przed usunięciem wystąpienia usługi Postgres.  Służy `--force` do pomijania monitu.

### <a name="additional-changes"></a>Dodatkowe zmiany

* Dodano nowy parametr opcjonalny do `azdata arc postgres server create` wywołania `--volume-claim mounts` . Wartość jest rozdzielaną przecinkami listą instalacji zgłoszeń zbiorczych. Instalacja z wnioskiem o woluminie to para typu woluminu i nazwy obwodu PVC. Jedynym obsługiwanym typem woluminu jest `backup` .  W PostgreSQL, gdy typ woluminu to `backup` , obwód PVC jest instalowany z `/mnt/db-backups` .  Umożliwia to udostępnianie kopii zapasowych między wystąpieniami PostgresSQL, dzięki czemu można przywrócić kopię zapasową jednego wystąpienia PostgresSQL w innym wystąpieniu.

* Nowe krótkie nazwy niestandardowych definicji zasobów PostgresSQL: 

  * `pg11` 

  * `pg12`

* Przekazywanie danych telemetrycznych przez program zapewnia użytkownikowi:

   * Liczba punktów przekazanych do platformy Azure

     lub 

   * Jeśli żadne dane nie zostały załadowane na platformę Azure, zostanie wyświetlony monit o ponowne wypróbowanie.

* `azdata arc dc debug copy-logs` teraz odczytuje również z `/var/opt/controller/log` folderu i zbiera dzienniki Postgres.

*   Wyświetlaj wskaźnik roboczy podczas Postgres tworzenia i przywracania kopii zapasowej.

* `azdata arc postrgres backup list` zawiera teraz informacje o rozmiarze kopii zapasowej.

* Właściwość Name administratora wystąpienia zarządzanego SQL została dodana do prawej kolumny bloku przegląd w Azure Portal.

* Azure Data Studio obsługuje Konfigurowanie liczby węzłów procesu roboczego, rdzeń wirtualny i ustawień pamięci dla grupy serwerów. 

* Wersja zapoznawcza obsługuje tworzenie kopii zapasowych/przywracanie dla Postgres w wersji 11 i 12.

## <a name="september-2020"></a>Wrzesień 2020

Usługi danych z włączonym usługą Azure Arc są udostępniane w publicznej wersji zapoznawczej. Usługi danych z włączonymi łukiemmi umożliwiają zarządzanie usługami danych w dowolnym miejscu.

- Wystąpienie zarządzane SQL
- PostgreSQL

Aby uzyskać instrukcje, zobacz [co to są usługi danych z włączonym usługą Azure Arc?](overview.md)

## <a name="known-limitations-and-issues"></a>Znane ograniczenia i problemy

- Nazwy wystąpień nie mogą być dłuższe niż 13 znaków
- Brak uaktualnienia w miejscu dla kontrolera danych usługi Azure ARC lub wystąpienia bazy danych.
- Obrazy kontenerów usług danych z obsługą usługi Arc nie są podpisane.  Może być konieczne skonfigurowanie węzłów Kubernetes w taki sposób, aby zezwalały na ściąganie niepodpisanych obrazów kontenerów.  Na przykład jeśli używasz platformy Docker jako środowiska uruchomieniowego kontenera, możesz ustawić zmienną środowiskową DOCKER_CONTENT_TRUST = 0 i uruchomić ponownie.  Inne środowiska uruchomieniowe kontenera mają podobne opcje, tak jak na platformie [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Nie można utworzyć grup serwera SQL z włączonym usługą Azure Arc ani na podstawie Azure Portal.
- Na razie w przypadku korzystania z systemu plików NFS `allowRunAsRoot` `true` przed utworzeniem kontrolera danych usługi Azure Arc należy ustawić wartość w pliku profilu wdrożenia.
- Tylko uwierzytelnianie logowania SQL i PostgreSQL.  Brak obsługi Azure Active Directory ani Active Directory.
- Tworzenie kontrolera danych w systemie OpenShift wymaga obniżonych ograniczeń zabezpieczeń.  Szczegółowe informacje znajdują się w dokumentacji.
- Skalowanie liczby węzłów roboczych PostgresSQL _w dół_ nie jest obsługiwane.
- Jeśli używasz aparatu usługi Azure Kubernetes (aparat AKS) na Azure Stack Hub z kontrolerem danych usługi Azure Arc i wystąpieniami baz danych, uaktualnienie do nowszej wersji Kubernetes nie jest obsługiwane. Odinstaluj kontroler danych usługi Azure Arc i wszystkie wystąpienia bazy danych przed uaktualnieniem klastra Kubernetes.
- Wersja zapoznawcza nie obsługuje wykonywania kopii zapasowych/przywracania dla aparatu Postgres w wersji 11. (Rozwiązane w październiku, 2020) Obsługuje tylko tworzenie kopii zapasowych i przywracanie dla Postgres w wersji 12.
- Usługa Azure Kubernetes Service (AKS), klastry obejmujące [wiele stref dostępności](../../aks/availability-zones.md) nie są obecnie obsługiwane dla usług danych z obsługą usługi Azure Arc. Aby uniknąć tego problemu, podczas tworzenia klastra AKS w Azure Portal, jeśli wybierzesz region, w którym strefy są dostępne, wyczyść wszystkie strefy z kontrolki zaznaczenie. Zobacz poniższy obraz:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Wyczyść pola wyboru dla każdej strefy, aby określić brak.":::


### <a name="known-issues-for-azure-arc-enabled-postgresql-hyperscale"></a>Znane problemy dotyczące usługi Azure ARC z włączonym skalowaniem PostgreSQL   

- Ponowne utworzenie grupy serwerów o nazwie grupy serwerów, która została właśnie usunięta, może się nie powieść lub zawiesić. 
   - **Obejście problemu** Nie należy ponownie używać tej samej nazwy podczas ponownego tworzenia grupy serwerów lub oczekiwania na usługę równoważenia obciążenia/zewnętrzną wcześniej grupę serwerów. Przy założeniu, że nazwa usuniętej grupy serwerów `postgres01` i była hostowana w przestrzeni nazw `arc` , przed ponownym utworzeniem grupy serwerów o tej samej nazwie Zaczekaj, aż `postgres01-external-svc` nie zostanie wyświetlony w danych wyjściowych polecenia polecenia kubectl `kubectl get svc -n arc` .
 
- Załadowanie strony przeglądu i strony Konfiguracja usługi obliczeniowej i magazynu w Azure Data Studio jest powolne. 



## <a name="next-steps"></a>Następne kroki
  
> **Chcesz, aby wypróbować coś?**  
> Szybko Rozpocznij pracę z usługą [Azure Arc szybko Rozpocznij pracę](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) w usłudze Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (eks), aparacie Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure.

[Instalowanie narzędzi klienta](install-client-tools.md)

[Utwórz kontroler danych usługi Azure Arc](create-data-controller.md) (najpierw wymagane jest zainstalowanie narzędzi klienta)

[Tworzenie wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc](create-sql-managed-instance.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)

[Tworzenie grupy serwerów z Azure Database for PostgreSQLm skalowaniem w usłudze Azure Arc](create-postgresql-hyperscale-server-group.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)
