---
title: PRZESTARZAŁE Wersja systemu Kanaryjskie z pomocą systemu Vamp w klastrze DC/OS platformy Azure
description: Jak korzystać z usługi pomocą systemu Vamp w wersji i zastosować Inteligentne filtrowanie ruchu w klastrze DC/OS Azure Container Service
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: dfdf6e1f8edfb4dafaf93e62090ed51878f9b2aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82734828"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>PRZESTARZAŁE Mikrousługi wersji Kanaryjskich z pomocą systemu Vamp na klastrze DC/OS Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym instruktażu skonfigurujemy pomocą systemu Vamp na Azure Container Service z klastrem DC/OS. Firma Microsoft — wersja demonstracyjna pomocą systemu Vamp "Sava", a następnie Rozwiązywanie niezgodności usługi z programem Firefox przez zastosowanie inteligentnego filtrowania ruchu. 

> [!TIP] 
> W tym instruktażu pomocą systemu Vamp jest uruchamiany w klastrze DC/OS, ale można również użyć pomocą systemu Vamp z Kubernetes jako koordynatorem.
>

## <a name="about-canary-releases-and-vamp"></a>Informacje o wersjach oprogramowania Kanaryjskie i pomocą systemu Vamp


[Wydanie Kanaryjskie](https://martinfowler.com/bliki/CanaryRelease.html) to inteligentna strategia wdrażania przyjęta przez innowacyjne organizacje, takie jak Netflix, Facebook i Spotify. Jest to podejście, które ma sens, ponieważ zmniejsza problemy, wprowadza bezpieczeństwo i zwiększa innowacje. Dlaczego nie wszystkie firmy używają tego programu? Rozszerzanie potoku ciągłej integracji/ciągłego wdrażania w celu uwzględnienia strategii Kanaryjskich zwiększa złożoność i wymaga obszernej wiedzy DevOps i środowiska. Jest to wystarczające do zablokowania mniejszych firm i przedsiębiorstw przed rozpoczęciem pracy. 

[Pomocą systemu Vamp](https://vamp.io/) to system "open source" zaprojektowany w celu ułatwienia tego przejścia i zapewnienia funkcji zwalniania przez narzędzie Kanaryjskie w preferowanym harmonogramie kontenerów. Funkcje Kanaryjskie pomocą systemu Vamp wykraczają poza wdrożenia oparte na procentach. Ruch może być filtrowany i podzielony na wiele warunków, na przykład dla konkretnych użytkowników, zakresów IP lub urządzeń. Pomocą systemu Vamp śledzi i analizuje metryki wydajności, umożliwiając automatyzację na podstawie rzeczywistych danych. Można skonfigurować automatyczne wycofywanie błędów lub skalować poszczególne warianty usługi na podstawie obciążenia lub opóźnienia.

## <a name="set-up-azure-container-service-with-dcos"></a>Konfigurowanie Azure Container Service przy użyciu platformy DC/OS



1. [Wdróż klaster DC/OS](container-service-deployment.md) z jednym serwerem głównym i dwoma agentami o domyślnym rozmiarze. 

2. [Utwórz tunel SSH](../container-service-connect.md) , aby połączyć się z klastrem DC/OS. W tym artykule założono, że tunel jest używany do klastra na porcie lokalnym 80.


## <a name="set-up-vamp"></a>Konfigurowanie pomocą systemu Vamp

Teraz, gdy masz uruchomiony klaster DC/OS, możesz zainstalować pomocą systemu Vamp z poziomu interfejsu użytkownika DC/OS (http: \/ /localhost: 80). 

![Interfejs użytkownika platformy DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Instalacja odbywa się w dwóch etapach:

1. **Wdróż Elasticsearch**.

2. Następnie **Wdróż pomocą systemu Vamp** przez zainstalowanie pakietu pomocą systemu Vamp DC/OS Universe.

### <a name="deploy-elasticsearch"></a>Wdróż Elasticsearch

Pomocą systemu Vamp wymaga Elasticsearch do zbierania metryk i agregacji. Do wdrożenia zgodnego stosu pomocą systemu Vamp Elasticsearch można użyć [obrazów platformy Docker magneticio](https://hub.docker.com/r/magneticio/elastic/) .

1. W interfejsie użytkownika DC/OS przejdź do pozycji **usługi** , a następnie kliknij pozycję **Wdróż usługę**.

2. Wybierz **tryb JSON** z okna podręcznego **Wdróż nową usługę** .

   ![Wybierz tryb JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Wklej poniższy kod JSON. Ta konfiguracja uruchamia kontener z 1 GB pamięci RAM i podstawową kontrolę kondycji na porcie Elasticsearch.
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. Kliknij przycisk **Deploy (Wdróż)**.

   Platforma DC/OS służy do wdrażania kontenera Elasticsearch. Postęp można śledzić na stronie **usługi** .  

   ![Czy wdrożyć e? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Wdróż pomocą systemu Vamp

Gdy Elasticsearch raporty jako **uruchomione**, możesz dodać pakiet platformy pomocą systemu Vamp DC/OS. 

1. Przejdź do programu **Universe** i Wyszukaj pozycję **pomocą systemu Vamp**. 
   ![Pomocą systemu Vamp na platformę DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Kliknij przycisk **Instaluj** obok pakietu pomocą systemu vamp, a następnie wybierz pozycję **Instalacja zaawansowana**.

3. Przewiń w dół i wprowadź następujący Elasticsearch-URL: `http://elasticsearch.marathon.mesos:9200` . 

   ![Wprowadź adres URL Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Kliknij przycisk **Przeglądaj i zainstaluj**, a następnie kliknij przycisk **Instaluj** , aby rozpocząć wdrażanie.  

   Platforma DC/OS wdraża wszystkie wymagane składniki pomocą systemu Vamp. Postęp można śledzić na stronie **usługi** .
  
   ![Wdróż pomocą systemu Vamp jako pakiet Universe](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Po zakończeniu wdrażania możesz uzyskać dostęp do interfejsu użytkownika pomocą systemu Vamp:

   ![Usługa pomocą systemu Vamp na kontrolerze domeny/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Interfejs użytkownika pomocą systemu Vamp](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Wdrażanie pierwszej usługi

Teraz, gdy pomocą systemu Vamp jest uruchomiona, wdróż usługę z planu. 

W najprostszej postaci [pomocą systemu Vamp plan](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments) opisuje punkty końcowe (bramy), klastry i usługi do wdrożenia. Pomocą systemu Vamp używa klastrów do grupowania różnych wariantów tej samej usługi w grupy logiczne dla testowania wydawania i/B.  

W tym scenariuszu jest stosowana Przykładowa aplikacja monolityczna o nazwie [**Sava**](https://github.com/magneticio/sava-product), która jest w wersji 1,0. Monolitu znajduje się w kontenerze platformy Docker, który znajduje się w usłudze Docker Hub w obszarze magneticio/Sava: 1.0.0. Aplikacja normalnie działa na porcie 8080, ale w tym przypadku chcesz ją uwidocznić w porcie 9050. Wdróż aplikację za pośrednictwem usługi pomocą systemu Vamp przy użyciu prostego planu.

1. Przejdź do pozycji **wdrożenia**.

2. Kliknij pozycję **Dodaj**.

3. Wklej w poniższej strategii YAML. Ten plan zawiera jeden klaster z tylko jedną odmianą usługi, która została zmieniona w kolejnym kroku:

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. Kliknij pozycję **Zapisz**. Pomocą systemu Vamp inicjuje wdrożenie.

Wdrożenie jest wyświetlane na stronie **wdrożenia** . Kliknij wdrożenie, aby monitorować jego stan.

![Interfejs użytkownika pomocą systemu Vamp — wdrażanie Sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Usługa Sava w interfejsie użytkownika pomocą systemu Vamp](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Tworzone są dwie bramy, które są wymienione na stronie **bramy** :

* stabilny punkt końcowy do uzyskiwania dostępu do uruchomionej usługi (port 9050) 
* Brama wewnętrzna zarządzana przez pomocą systemu Vamp (więcej informacji na tej bramie). 

![Pomocą systemu Vamp UI-Sava Gateways](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Usługa Sava została wdrożona, ale nie możesz uzyskać do niej dostępu zewnętrznie, ponieważ Azure Load Balancer nie jest już do niej przekazywane. Aby uzyskać dostęp do usługi, zaktualizuj konfigurację sieci platformy Azure.


## <a name="update-the-azure-network-configuration"></a>Aktualizowanie konfiguracji sieci platformy Azure

Pomocą systemu Vamp wdrożono usługę Sava w węzłach agenta DC/OS, uwidaczniając stabilny punkt końcowy na porcie 9050. Aby uzyskać dostęp do usługi spoza klastra DC/OS, wprowadź następujące zmiany w konfiguracji sieci platformy Azure we wdrożeniu klastra: 

1. **Skonfiguruj Azure Load Balancer** dla agentów (zasób o nazwie **dcos-Agent-lb-xxxx**) z sondą kondycji i reguły przesyłania dalej ruchu na porcie 9050 do wystąpień Sava. 

2. **Zaktualizuj grupę zabezpieczeń sieci** dla agentów publicznych (zasób o nazwie **xxxx-Agent-Public-sieciowej grupy zabezpieczeń-xxxx**), aby zezwolić na ruch na porcie 9050.

Aby uzyskać szczegółowe instrukcje dotyczące wykonywania tych zadań przy użyciu Azure Portal, zobacz [Włączanie publicznego dostępu do aplikacji Azure Container Service](container-service-enable-public-access.md). Określ port 9050 dla wszystkich ustawień portu.


Po utworzeniu wszystkiego przejdź do bloku **Przegląd** modułu równoważenia obciążenia systemu DC/OS (zasób o nazwie **dcos-Agent-lb-xxxx**). Znajdź **publiczny adres IP**i użyj adresu w celu uzyskania dostępu do Sava na porcie 9050.

![Azure Portal — Uzyskaj publiczny adres IP](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Uruchamianie wersji programu Kanaryjskie

Załóżmy, że masz nową wersję tej aplikacji, którą chcesz wypróbować w środowisku produkcyjnym. Jest on kontenerem jako magneticio/Sava: 1.1.0 i gotowe do użycia. Pomocą systemu Vamp umożliwia łatwe dodawanie nowych usług do uruchomionego wdrożenia. Te "scalone" usługi są wdrażane wraz z istniejącymi usługami w klastrze i mają przypisaną wagę 0%. Żaden ruch nie jest kierowany do nowo Scalonej usługi do momentu dostosowania dystrybucji ruchu. Suwak wagi w interfejsie użytkownika pomocą systemu Vamp zapewnia pełną kontrolę nad dystrybucją, umożliwiając korektę przyrostową (wydanie Kanaryjskie) lub natychmiastowe wycofywanie.

### <a name="merge-a-new-service-variant"></a>Scalanie nowej wariantu usługi

Aby scalić nową usługę Sava 1,1 z uruchomionym wdrożeniem:

1. W interfejsie użytkownika pomocą systemu Vamp kliknij pozycję **plany**.

2. Kliknij przycisk **Dodaj** i wklej w następującej strategii YAML: ten plan opisuje nową wariant usługi (Sava: 1.1.0) do wdrożenia w ramach istniejącego klastra (sava_cluster).

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. Kliknij pozycję **Zapisz**. Plan jest przechowywany i wyświetlany na stronie **plany** .

4. Otwórz menu Akcja w planie Sava: 1.1 i kliknij pozycję **Scal z**.

   ![Pomocą systemu Vamp — plany interfejsu użytkownika](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Wybierz wdrożenie **Sava** , a następnie kliknij przycisk **Scal**.

   ![Interfejs użytkownika pomocą systemu Vamp — Scal plan z wdrożeniem](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Pomocą systemu Vamp wdraża nową odmianę usługi Sava: 1.1.0, która została opisana w planie obok Sava: 1.0.0 w **sava_cluster** uruchomionym wdrożeniu. 

![Interfejs użytkownika pomocą systemu Vamp — Zaktualizowano wdrożenie Sava](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

Brama **/webport Sava/sava_cluster** (punkt końcowy klastra) jest również aktualizowana, co powoduje dodanie trasy do nowo wdrożonego Sava: 1.1.0. W tym momencie żaden ruch nie jest kierowany w tym miejscu ( **waga** jest ustawiona na 0%).

![Interfejs użytkownika pomocą systemu Vamp — Brama klastra](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Wersja Kanaryjskie

W przypadku obu wersji Sava wdrożonych w tym samym klastrze Dostosuj rozkład ruchu między nimi, przesuwając suwak **wagi** .

1. Kliknij pozycję ![ pomocą systemu Vamp UI — Edytuj ](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) obok pozycji **waga**.

2. Ustaw rozkład wagi na 50%/50%, a następnie kliknij przycisk **Zapisz**.

   ![Pomocą systemu Vamp — suwak wagi bramy](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Wróć do przeglądarki i Odśwież stronę Sava kilka razy. Aplikacja Sava teraz przełącza się między stroną Sava: 1.0 a Sava: 1.1.

   ![zmienne usługi Sava 1.0 i Sava 1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Ta alternatywa strony najlepiej sprawdza się w przypadku trybu "incognito" lub "Anonymous" w przeglądarce ze względu na buforowanie zasobów statycznych.
  >

### <a name="filter-traffic"></a>Filtruj ruch

Załóżmy, że po wdrożeniu wykryto niezgodność w Sava: 1.1.0, która powoduje problemy z wyświetlaniem w przeglądarkach w przeglądarce Firefox. Można ustawić pomocą systemu vamp, aby filtrować ruch przychodzący i kierować wszystkich użytkowników programu Firefox z powrotem do znanej stabilnej Sava: 1.0.0. Ten filtr natychmiast rozwiązuje zakłócenia dla użytkowników programu Firefox, natomiast inne osoby nadal korzystają z zalet ulepszonych Sava: 1.1.0.

Pomocą systemu Vamp używa **warunków** do filtrowania ruchu między trasami w bramie. Ruch jest najpierw filtrowany i kierowany zgodnie z warunkami zastosowanymi do każdej trasy. Cały ruch pozostały jest dystrybuowany zgodnie z ustawieniem wagi bramy.

Można utworzyć warunek filtrowania wszystkich użytkowników Firefox i skierować je do starego Sava: 1.0.0:

1. Na stronie **bramy** /webport sava/sava_cluster kliknij pozycję ![ pomocą systemu Vamp UI-Edytuj, ](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) Aby dodać **warunek** do trasy Sava/sava_cluster/Sava: 1.0.0/webport. 

2. Wprowadź warunek **User-Agent = = Firefox** i kliknij pozycję ![ pomocą systemu Vamp UI-Save (Zapisz) ](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) .

   Pomocą systemu Vamp dodaje warunek z domyślną siłą równą 0%. Aby rozpocząć filtrowanie ruchu, należy dostosować siłę stanu.

3. Kliknij pozycję ![ pomocą systemu Vamp UI-Edytuj, ](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) Aby zmienić **siłę** zastosowana do warunku.
 
4. Ustaw **siłę** na 100%, a następnie kliknij pozycję ![ interfejs użytkownika pomocą systemu Vamp — Zapisz, ](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) Aby zapisać.

   Pomocą systemu Vamp teraz wysyła cały ruch zgodny z warunkiem (wszyscy użytkownicy programu Firefox) do Sava: 1.0.0.

   ![Interfejs użytkownika pomocą systemu Vamp — stosowanie warunku do bramy](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Na koniec Dostosuj wagę bramy, aby wysłać cały ruch (wszyscy użytkownicy niebędący Firefox) do nowego Sava: 1.1.0. Kliknij pozycję ![ pomocą systemu Vamp UI — Edytuj ](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) obok pozycji **waga** i ustaw rozkład wagi, tak aby 100% został skierowany do trasy Sava/sava_cluster/Sava: 1.1.0/webport.

   Cały ruch, który nie jest filtrowany przez warunek, jest teraz kierowany do nowego Sava: 1.1.0.

6. Aby wyświetlić filtr w akcji, Otwórz dwie różne przeglądarki (jedna Firefox i jedna inna przeglądarka) i uzyskaj dostęp do usługi Sava z obu tych programów. Wszystkie żądania programu Firefox są wysyłane do Sava: 1.0.0, podczas gdy wszystkie inne przeglądarki są kierowane do Sava: 1.1.0.

   ![Pomocą systemu Vamp ruch filtru interfejsu użytkownika](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Sumowanie

Ten artykuł był szybkim wprowadzeniem do pomocą systemu Vamp w klastrze DC/OS. W przypadku startów pomocą systemu Vamp się i uruchomiono Azure Container Service w klastrze DC/OS, wdrożono usługę z planem pomocą systemu Vamp i uzyskuje dostęp do niej w uwidocznionym punkcie końcowym (bramie).

Wprowadzono również pewne zaawansowane funkcje pomocą systemu Vamp: scalanie nowej wariantu usługi z uruchomionym wdrożeniem i wprowadzenie go przyrostowo, a następnie Filtrowanie ruchu w celu rozpoznania znanej niezgodności.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o zarządzaniu akcjami pomocą systemu Vamp za pomocą [interfejsu API REST pomocą systemu Vamp](https://docs.vamp.io/how-vamp-works/events-and-metrics#events).

* Twórz skrypty automatyzacji pomocą systemu Vamp w Node.js i uruchamiaj je jako [przepływy pracy pomocą systemu Vamp](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows).

* Zobacz dodatkowe [samouczki pomocą systemu Vamp](https://docs.vamp.io/tutorials/).

