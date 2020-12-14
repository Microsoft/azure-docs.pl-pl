---
title: Gotowość do produkcji i najlepsze praktyki — Azure
description: Ten artykuł zawiera wskazówki dotyczące konfigurowania i wdrażania usługi Live Video Analytics w module IoT Edge w środowiskach produkcyjnych.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 56982d84b7ffac718072683076657d56a2691d6c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400560"
---
# <a name="production-readiness-and-best-practices"></a>Gotowość do produkcji i najlepsze rozwiązania

Ten artykuł zawiera wskazówki dotyczące konfigurowania i wdrażania usługi Live Video Analytics w module IoT Edge w środowiskach produkcyjnych. Należy również zapoznać [się z tematem przygotowanie do wdrożenia rozwiązania IoT Edge w artykule produkcyjnym](../../iot-edge/production-checklist.md) dotyczącym przygotowywania IoT Edge rozwiązania. 

> [!NOTE]
> Należy skontaktować się z działami IT organizacji w zakresie aspektów związanych z bezpieczeństwem.

## <a name="running-the-module-as-a-local-user"></a>Uruchamianie modułu jako użytkownik lokalny

Po wdrożeniu narzędzia do analizy filmów wideo na żywo w IoT Edge module na urządzeniu brzegowym domyślnie jest ono uruchamiane z podniesionymi uprawnieniami. Gdy to zrobisz, w przypadku sprawdzenia dzienników w module ( `sudo iotedge logs {name-of-module}` ) zostaną wyświetlone następujące elementy:

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

W poniższych sekcjach omówiono, jak można rozwiązać powyższe ostrzeżenie.

### <a name="creating-and-using-a-local-user-account"></a>Tworzenie konta użytkownika lokalnego i korzystanie z niego

W środowisku produkcyjnym można uruchomić usługę analiza filmów wideo IoT Edge na żywo, korzystając z konta o jak najmniejszej liczbie uprawnień. Poniższe polecenia, na przykład pokazują, jak utworzyć konto użytkownika lokalnego na maszynie wirtualnej z systemem Linux:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

Następnie w manifeście wdrożenia można ustawić zmienne środowiskowe LOCAL_USER_ID i LOCAL_GROUP_ID do tego, czy użytkownik nie jest użytkownikiem głównym i grupą:

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>Przyznawanie uprawnień do magazynu urządzeń

Funkcja analizy filmów wideo na żywo w module IoT Edge wymaga możliwości zapisu plików w lokalnym systemie plików, gdy:

* Za pomocą właściwości sznurka modułu [`applicationDataDirectory`](module-twin-configuration-schema.md#module-twin-properties) , w której należy określić katalog w lokalnym systemie plików do przechowywania danych konfiguracyjnych.
* Aby nagrać wideo z chmurą za pomocą grafu multimediów, moduł wymaga użycia katalogu na urządzeniu brzegowym jako pamięci podręcznej (Zobacz artykuł [ciągłego rejestrowania wideo](continuous-video-recording-concept.md) , aby uzyskać więcej informacji).
* [Nagrywanie do pliku lokalnego](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources), gdzie należy określić ścieżkę pliku dla nagrania wideo.

Jeśli zamierzasz korzystać z któregoś z powyższych, upewnij się, że powyższe konto użytkownika ma dostęp do odpowiedniego katalogu. Rozważmy na przykład applicationDataDirectory. Można utworzyć katalog na urządzeniu brzegowym i połączyć magazyn urządzeń z magazynem modułów. 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

Następnie w obszarze Utwórz opcje dla modułu Edge w manifeście wdrożenia można dodać ustawienie powiązania mapowanie katalogu ("var/Local/MediaServices/") powyżej do katalogu w module (na przykład "/var/lib/azuremediaservices/"). I Użyj tego ostatniego katalogu jako wartości dla applicationDataDirectory.

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

Jeśli zobaczysz przykładowe wykresy multimedialne dla przewodnika Szybki Start i samouczków, takie jak [ciągłe nagrywanie filmów wideo](continuous-video-recording-tutorial.md), należy zauważyć, że Katalog pamięci podręcznej multimediów (localMediaCachePath) używa podkatalogu w applicationDataDirectory. Jest to zalecane podejście, ponieważ pamięć podręczna zawiera dane przejściowe.

### <a name="naming-video-assets-or-files"></a>Nazywanie elementów zawartości lub plików wideo

Wykresy multimediów umożliwiają tworzenie zasobów w chmurze lub plików MP4 na brzegu. Zasoby multimedialne mogą być generowane przez [ciągłe nagrywanie wideo](continuous-video-recording-tutorial.md) lub [Rejestrowanie wideo oparte na zdarzeniach](event-based-video-recording-tutorial.md). Chociaż te zasoby i pliki mogą być nazwane zgodnie z oczekiwaniami, zalecaną strukturą nazewnictwa dla zasobów multimediów ciągłego nagrywania wideo jest " &lt; anytext &gt; -$ {System. GraphTopologyName}-$ {system. GraphInstanceName}".   

Wzorzec podstawienia jest zdefiniowany przez znak $, po którym następują nawiasy klamrowe: **$ {VariableName}**.  

Na przykład można ustawić assetNamePattern w ujścia zasobów w następujący sposób:

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

W przypadku zasobów generowanych na podstawie zdarzeń, zalecany wzorzec nazewnictwa to " &lt; anytext &gt; -$ {System. DateTime}". Zmienna systemowa zapewnia, że zasoby nie są zastępowane w przypadku wystąpienia zdarzeń w tym samym czasie. Na przykład można ustawić assetNamePattern w ujścia zasobów w następujący sposób:

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

W przypadku uruchamiania wielu wystąpień tego samego wykresu można użyć nazwy topologii wykresu i nazwy wystąpienia, aby odróżnić. Na przykład można ustawić assetNamePattern w ujścia zasobów w następujący sposób:

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName}-${System.DateTime}"
```

W przypadku opartych na zdarzeniach klipów wideo MP4 generowanych na podstawie zdarzeń, zalecany wzorzec nazewnictwa powinien zawierać wartość DateTime i dla wielu wystąpień tego samego wykresu zaleca się użycie zmiennych systemowych GraphTopologyName i GraphInstanceName. Przykładowo można ustawić filePathPattern w ujścia plików w następujący sposób: 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

Lub 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```
>[!NOTE]
> W powyższym przykładzie zmienna **fileSinkOutputName** jest przykładową nazwą zmiennej zdefiniowaną w topologii grafu. To **nie** jest zmienna systemowa. 

#### <a name="system-variables"></a>Zmienne systemowe
Niektóre zdefiniowane zmienne systemowe, których można użyć, to:

|Zmienna systemowa|Opis|Przykład|
|-----------|-----------|-----------|
|System. DateTime|Data i godzina UTC w formacie zgodnym z ISO8601 (podstawowa reprezentacja YYYYMMDDThhmmss).|20200222T173200Z|
|System. PreciseDateTime|Data i godzina UTC w formacie zgodnym z ISO8601 w milisekundach (podstawowa reprezentacja YYYYMMDDThhmmss. SSS).|20200222T 173200.123 Z|
|System. GraphTopologyName|Podana przez użytkownika nazwa topologii wykonywanej grafu.|IngestAndRecord|
|System. GraphInstanceName|Podana przez użytkownika nazwa wykonywanego wystąpienia grafu.|camera001|

>[!TIP]
> Nie można używać elementu System. PreciseDateTime podczas nadawania nazw zasobom ze względu na "." w nazwie
### <a name="keeping-your-vm-clean"></a>Utrzymywanie oczyszczania maszyny wirtualnej

Maszyna wirtualna z systemem Linux, która jest używana jako urządzenie brzegowe, może przestać odpowiadać, jeśli nie jest okresowo zarządzana. Niezbędne jest czyszczenie pamięci podręcznych, wyeliminowanie zbędnych pakietów i usunięcie nieużywanych kontenerów z maszyny wirtualnej. Aby to zrobić, to zestaw zalecanych poleceń, których można użyć na maszynie wirtualnej brzegowej.

1. `sudo apt-get clean`

    Polecenie apt-get clean czyści lokalne repozytorium pobranych plików pakietów, które pozostało w/var/cache. Katalogi, które czyści, to/var/cache/apt/archives/i/var/cache/apt/archives/Partial/. Jedyne pliki, które pozostawia w/var/cache/apt/archives, to plik blokady i podkatalog częściowy. Polecenie apt-get Clean służy zwykle do wyczyszczenia miejsca na dysku w razie potrzeby, zazwyczaj w ramach regularnie zaplanowanej konserwacji. Aby uzyskać więcej informacji, zobacz [czyszczenie przy użyciu apt-get](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html).
1. `sudo apt-get autoclean`

    Opcja apt-get autoclean, taka jak apt-get clean, czyści lokalne repozytorium pobranych plików pakietów, ale usuwa tylko te pliki, których nie można już pobrać i które nie są użyteczne. Pomaga utrzymać, że pamięć podręczna jest zbyt duża.
1. `sudo apt-get autoremove1`

    Opcja autousuwania usuwa pakiety, które zostały zainstalowane automatycznie, ponieważ inny pakiet wymagał tego, ale z tymi innymi pakietami został usunięty, nie są już potrzebne.
1. `sudo docker image ls` — Zawiera listę obrazów platformy Docker w systemie brzegowym
1. `sudo docker system prune`

    Platforma Docker podejmuje ostrożne podejście do czyszczenia nieużywanych obiektów (często nazywanych "odzyskiwaniem pamięci"), takich jak obrazy, kontenery, woluminy i sieci: te obiekty zwykle nie są usuwane, chyba że jawnie poprosimy platformę Docker. Może to spowodować, że platforma Docker będzie używać dodatkowego miejsca na dysku. Dla każdego typu obiektu Docker udostępnia polecenie Oczyść. Ponadto można użyć Oczyść oczyszczania systemu platformy Docker, aby oczyścić jednocześnie wiele typów obiektów. Aby uzyskać więcej informacji, zobacz [oczyszczanie nieużywanych obiektów platformy Docker](https://docs.docker.com/config/pruning/).
1. `sudo docker rmi REPOSITORY:TAG`

    Gdy aktualizacje są wykonywane w module brzegowym, platforma Docker może nadal znajdować się w starszych wersjach modułu brzegowego. W takim przypadku zaleca się użycie polecenia Docker RMI w celu usunięcia określonych obrazów identyfikowanych przez tag wersji obrazu.

## <a name="next-steps"></a>Następne kroki

[Szybki Start: Rozpoczynanie pracy — Analiza filmów wideo na żywo na IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
