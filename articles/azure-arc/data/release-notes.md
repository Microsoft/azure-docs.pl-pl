---
title: Usługi danych z włączoną funkcją Azure Arc — informacje o wersji
description: Najnowsze informacje o wersji
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 6b4d5c1372a8351f1fe5a6608aff38bf232aabd8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121953"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Informacje o wersji — usługi danych z obsługą usługi Azure ARC (wersja zapoznawcza)

W tym artykule przedstawiono możliwości, funkcje i ulepszenia ostatnio wydane lub udoskonalone dla usług danych z obsługą usługi Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>Luty 2021 r.

### <a name="new-capabilities-and-features"></a>Nowe możliwości i funkcje

Numer wersji interfejsu wiersza polecenia platformy Azure ( `azdata` ): 20.3.1. Pobierz pod adresem [https://aka.ms/azdata](https://aka.ms/azdata) . Instalację można zainstalować `azdata` z [instalacji interfejsu wiersza polecenia platformy Azure ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

Dodatkowe aktualizacje obejmują:

- Usługa SQL Managed Instance z obsługą usługi Azure Arc
   - Wysoka dostępność z zawsze włączonymi grupami dostępności

- Usługa Azure ARC z włączonym skalowaniem PostgreSQL Azure Data Studio: 
   - Na stronie Przegląd jest teraz wyświetlany stan grupy serwerów dla każdego węzła
   - Nowe strony właściwości są teraz dostępne, aby wyświetlić więcej szczegółów na temat grupy serwerów
   - Konfigurowanie parametrów aparatu Postgres na stronie **parametrów węzła**

Problemy związane z tą wersją zawiera temat [znane problemy — usługi danych z obsługą usługi Azure ARC (wersja zapoznawcza)](known-issues.md)

## <a name="january-2021"></a>Styczeń 2021 r.

### <a name="new-capabilities-and-features"></a>Nowe możliwości i funkcje

Numer wersji interfejsu wiersza polecenia platformy Azure ( `azdata` ): 20.3.0. Pobierz pod adresem [https://aka.ms/azdata](https://aka.ms/azdata) . Instalację można zainstalować `azdata` z [instalacji interfejsu wiersza polecenia platformy Azure ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

Dodatkowe aktualizacje obejmują:
- Zlokalizowany portal dostępny dla 17 nowych języków
- Drobne zmiany w plikach polecenia-Native. YAML
- Nowe wersje programu Grafana i Kibana
- Problemy ze środowiskami języka Python w przypadku używania azdata w notesach w Azure Data Studio rozwiązane
- Rozszerzenie pg_audit jest teraz dostępne dla PostgreSQL
- Identyfikator kopii zapasowej nie jest już wymagany podczas wykonywania pełnego przywracania bazy danych PostgreSQL
- Stan (stan kondycji) jest raportowany dla każdego wystąpienia PostgreSQL, które stanowi grupę serwerów.

   We wcześniejszych wersjach stan został zagregowany na poziomie grupy serwerów, a nie na poziomie węzła PostgreSQL.

- Wdrożenia PostgreSQL teraz uznają parametry rozmiaru woluminu wskazane w poleceniach Create
- Parametry wersji aparatu są teraz honorowane podczas edytowania grupy serwerów
- Konwencja nazewnictwa zasobników dla usługi Azure ARC z włączonym skalowaniem PostgreSQL została zmieniona

    Jest teraz w formie: `ServergroupName{c, w}-n` . Na przykład Grupa serwerów z trzema węzłami, jeden węzeł koordynatora i dwa węzły procesu roboczego są reprezentowane jako:
   - `Postgres01c-0` (węzeł koordynatora)
   - `Postgres01w-0` (węzeł procesu roboczego)
   - `Postgres01w-1` (węzeł procesu roboczego)

## <a name="december-2020"></a>Grudzień 2020 r.

### <a name="new-capabilities--features"></a>Nowe możliwości & funkcje

Numer wersji interfejsu wiersza polecenia platformy Azure ( `azdata` ): 20.2.5. Pobierz pod adresem [https://aka.ms/azdata](https://aka.ms/azdata) .

Wyświetlaj punkty końcowe dla wystąpienia zarządzanego SQL i PostgreSQL na potrzeby skalowania przy użyciu interfejsu wiersza polecenia platformy Azure ( `azdata` ) za pomocą `azdata arc sql endpoint list` `azdata arc postgres endpoint list` poleceń i.

Edytuj żądania i limity zasobów wystąpienia zarządzanego SQL (rdzeń procesora CPU i pamięci) przy użyciu Azure Data Studio.

Usługa Azure ARC z włączonym PostgreSQLm skalowania teraz obsługuje przywracanie do punktu w czasie oprócz pełnego przywracania kopii zapasowych zarówno w wersjach 11, jak i 12 z PostgreSQL. Funkcja przywracania do punktu w czasie pozwala wskazać określoną datę i godzinę przywracania.

Konwencja nazewnictwa zasobników dla usługi Azure ARC z włączonym skalowaniem PostgreSQL zmieniła się. Jest teraz w formie: ServergroupName {r, s}-_n_. Na przykład Grupa serwerów z trzema węzłami, jeden węzeł koordynatora i dwa węzły procesu roboczego są reprezentowane jako:
- `postgres02r-0` (węzeł koordynatora)
- `postgres02s-0` (węzeł procesu roboczego)
- `postgres02s-1` (węzeł procesu roboczego)

### <a name="breaking-change"></a>Zmiana podziału

#### <a name="new-resource-provider"></a>Nowy dostawca zasobów

W tej wersji wprowadzono zaktualizowanego [dostawcę zasobów](../../azure-resource-manager/management/azure-services-resource-providers.md) o nazwie `Microsoft.AzureArcData` . Aby można było korzystać z tej funkcji, należy zarejestrować tego dostawcę zasobów. 

Aby zarejestrować tego dostawcę zasobów: 

1. W Azure Portal wybierz pozycję **subskrypcje** 
2. Wybierz subskrypcję
3. W obszarze **Ustawienia** wybierz pozycję **dostawcy zasobów** . 
4. Wyszukaj `Microsoft.AzureArcData` i wybierz pozycję **zarejestruj** 

Szczegółowe kroki można przejrzeć w obszarze [dostawcy zasobów platformy Azure i typy](../../azure-resource-manager/management/resource-providers-and-types.md). Ta zmiana spowoduje również usunięcie wszystkich istniejących zasobów platformy Azure, które zostały przekazane do Azure Portal. Aby można było korzystać z dostawcy zasobów, należy zaktualizować kontroler danych i użyć najnowszego `azdata` interfejsu wiersza polecenia.  

### <a name="platform-release-notes"></a>Informacje o wersji platformy

#### <a name="direct-connectivity-mode"></a>Tryb łączności bezpośredniej

W tej wersji wprowadzono tryb bezpośredniej łączności. Tryb łączności bezpośredniej umożliwia kontrolerowi danych automatyczne przekazywanie informacji o użyciu do platformy Azure. W ramach przekazywania użycia zasób kontrolera danych Arc jest automatycznie tworzony w portalu, jeśli nie został jeszcze utworzony za pomocą `azdata` przekazywania.  

Możesz określić bezpośrednie połączenie podczas tworzenia kontrolera danych. Poniższy przykład tworzy kontroler danych z nazwą z `azdata arc dc create` `arc` trybem bezpośredniej łączności ( `connectivity-mode direct` ). Przed uruchomieniem przykładu Zastąp ciąg `<subscription id>` identyfikatorem subskrypcji.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>Znane problemy

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

#### <a name="postgresql"></a>PostgreSQL

- Usługa Azure ARC z włączonym PostgreSQLem nie zwraca niedokładnego komunikatu o błędzie, gdy nie można go przywrócić do punktu względnego w czasie. Jeśli na przykład do przywracania określono punkt w czasie, który jest starszy niż zawiera kopie zapasowe, przywracanie zakończy się niepowodzeniem z komunikatem o błędzie: błąd: (404). Przyczyna: nie znaleziono. Treść odpowiedzi HTTP: {"Code": 404, "internalStatus": "NOT_FOUND", "Przyczyna": "nie można przywrócić kopii zapasowej serwera...}
W takim przypadku należy ponownie uruchomić polecenie po wskazaniu punktu w czasie, który znajduje się w zakresie dat, dla których istnieją kopie zapasowe. Ten zakres zostanie określony przez wystawienie kopii zapasowych i przejrzenie dat, w których zostały wykonane.
- Przywracanie do punktu w czasie jest obsługiwane tylko w grupach serwerów. Serwer docelowy operacji przywracania do punktu w czasie nie może być serwerem, z którego została wykonana kopia zapasowa. Musi to być inna grupa serwerów. Jednak pełne przywracanie jest obsługiwane przez tę samą grupę serwerów.
- Podczas wykonywania pełnego przywracania wymagany jest identyfikator kopii zapasowej. Domyślnie, jeśli nie jest wskazywany identyfikator kopii zapasowej, zostanie użyta najnowsza kopia zapasowa. To nie działa w tej wersji.

## <a name="october-2020"></a>Październik 2020 r. 

Numer wersji interfejsu wiersza polecenia platformy Azure ( `azdata` ): 20.2.3. Pobierz pod adresem [https://aka.ms/azdata](https://aka.ms/azdata) .

### <a name="breaking-changes"></a>Fundamentalne zmiany

W tej wersji wprowadzono następujące istotne zmiany: 

* W definicji niestandardowego zasobu PostgreSQL (CRD) termin `shards` jest zmieniany na `workers` . Ten termin ( `workers` ) jest zgodny z nazwą parametru wiersza polecenia.

* `azdata arc postgres server delete` monituje o potwierdzenie przed usunięciem wystąpienia usługi Postgres.  Służy `--force` do pomijania monitu.

### <a name="additional-changes"></a>Dodatkowe zmiany

* Dodano nowy parametr opcjonalny do `azdata arc postgres server create` wywołania `--volume-claim mounts` . Wartość jest rozdzielaną przecinkami listą instalacji zgłoszeń zbiorczych. Instalacja z wnioskiem o woluminie to para typu woluminu i nazwy obwodu PVC. Jedynym obsługiwanym typem woluminu jest `backup` .  W PostgreSQL, gdy typ woluminu to `backup` , obwód PVC jest instalowany z `/mnt/db-backups` .  Umożliwia to udostępnianie kopii zapasowych między wystąpieniami PostgresSQL, dzięki czemu można przywrócić kopię zapasową jednego wystąpienia PostgresSQL w innym wystąpieniu.

* Nowe krótkie nazwy niestandardowych definicji zasobów PostgresSQL: 
  * `pg11` 
  * `pg12`
* Przekazywanie danych telemetrycznych zapewnia użytkownikowi:
   * Liczba punktów przekazanych do platformy Azure lub 
   * Jeśli żadne dane nie zostały załadowane na platformę Azure, zostanie wyświetlony monit o ponowne wypróbowanie.
* `azdata arc dc debug copy-logs` teraz odczytuje również z `/var/opt/controller/log` folderu i zbiera dzienniki aparatu PostgreSQL w systemie Linux.
*   Wyświetlaj wskaźnik roboczy podczas tworzenia i przywracania kopii zapasowej za pomocą PostgreSQL.
* `azdata arc postrgres backup list` zawiera teraz informacje o rozmiarze kopii zapasowej.
* Właściwość Name administratora wystąpienia zarządzanego SQL została dodana do prawej kolumny bloku przegląd w Azure Portal.
* Azure Data Studio obsługuje Konfigurowanie liczby węzłów procesu roboczego, rdzeń wirtualny i ustawień pamięci na potrzeby skalowania PostgreSQL. 
* Wersja zapoznawcza obsługuje tworzenie kopii zapasowych/przywracanie dla Postgres w wersji 11 i 12.

## <a name="september-2020"></a>Wrzesień 2020

Usługi danych z włączonym usługą Azure Arc są udostępniane w publicznej wersji zapoznawczej. Usługi danych z obsługą ARC umożliwiają zarządzanie usługami danych w dowolnym miejscu.

- Wystąpienie zarządzane SQL
- PostgreSQL

Aby uzyskać instrukcje, zobacz [co to są usługi danych z włączonym usługą Azure Arc?](overview.md)

## <a name="next-steps"></a>Następne kroki

> **Chcesz, aby wypróbować coś?**  
> Szybko Rozpocznij pracę dzięki usłudze [Azure Arc szybko Rozpocznij pracę](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) na AKS, AWS Elastic Kubernetes Service (eks), usłudze Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure.

- [Instalowanie narzędzi klienta](install-client-tools.md)
- [Utwórz kontroler danych usługi Azure Arc](create-data-controller.md) (najpierw wymagane jest zainstalowanie narzędzi klienta)
- [Tworzenie wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc](create-sql-managed-instance.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)
- [Tworzenie grupy serwerów z Azure Database for PostgreSQLm skalowaniem w usłudze Azure Arc](create-postgresql-hyperscale-server-group.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)
- [Dostawcy zasobów dla usług platformy Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
