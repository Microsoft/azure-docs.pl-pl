---
title: Lokalna pamięć podręczna
description: Dowiedz się, jak lokalna pamięć podręczna działa w Azure App Service oraz jak włączyć, zmienić rozmiar i zbadać stan lokalnej pamięci podręcznej aplikacji.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: b3c8f6015b4627d86a0665865fba2f3fdd39589d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080715"
---
# <a name="azure-app-service-local-cache-overview"></a>Omówienie lokalnej pamięci podręcznej Azure App Service

> [!NOTE]
> Lokalna pamięć podręczna nie jest obsługiwana w aplikacjach funkcji ani w kontenerach App Service aplikacjach, takich jak [kontenery systemu Windows](quickstart-custom-container.md?pivots=container-windows) lub [App Service w systemie Linux](overview.md#app-service-on-linux).


Azure App Service zawartość jest przechowywana w usłudze Azure Storage i jest porzucana w sposób trwały jako udział zawartości. Ten projekt jest przeznaczony do pracy z różnymi aplikacjami i ma następujące atrybuty:  

* Zawartość jest współdzielona przez wiele wystąpień maszyny wirtualnej (VM) aplikacji.
* Zawartość jest trwała i może być modyfikowana przez uruchamianie aplikacji.
* Pliki dziennika i pliki danych diagnostycznych są dostępne w tym samym folderze zawartości udostępnionej.
* Publikowanie nowej zawartości bezpośrednio aktualizuje folder zawartości. Możesz natychmiast wyświetlić tę samą zawartość za pomocą witryny sieci Web SCM i działającej aplikacji (zazwyczaj niektóre technologie, takie jak ASP.NET, zainicjuj ponowne uruchomienie aplikacji na niektórych zmianach plików, aby uzyskać najnowszą zawartość).

Chociaż wiele aplikacji korzysta z jednej lub wszystkich tych funkcji, niektóre aplikacje potrzebują tylko magazynu zawartości o wysokiej wydajności, który może być uruchamiany z wysoką dostępnością. Te aplikacje mogą korzystać z wystąpienia maszyny wirtualnej w określonej lokalnej pamięci podręcznej.

Funkcja lokalnej pamięci podręcznej Azure App Service udostępnia widok roli sieci Web zawartości. Ta zawartość jest pamięcią podręczną zapisu i odrzucania zawartości magazynu, która została utworzona asynchronicznie podczas uruchamiania lokacji. Gdy pamięć podręczna jest gotowa, lokacja zostanie przełączona w celu uruchomienia względem zawartości w pamięci podręcznej. Aplikacje działające w lokalnej pamięci podręcznej mają następujące zalety:

* Są one odporne na opóźnienia występujące podczas uzyskiwania dostępu do zawartości w usłudze Azure Storage.
* Są one odporne na planowane uaktualnienia lub nieplanowane przestoje oraz wszelkie inne przerwy w działaniu usługi Azure Storage, które znajdują się na serwerach, które obsługują udział zawartości.
* W związku z tym zmiany udziałów magazynu mają mniejszą liczbę ponownych uruchomień aplikacji.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Jak lokalna pamięć podręczna zmienia zachowanie App Service
* _D:\home_ wskazuje lokalną pamięć podręczną, która jest tworzona w wystąpieniu maszyny wirtualnej podczas uruchamiania aplikacji. _D:\Local_ nadal wskazuje na tymczasowy magazyn specyficzny dla maszyny wirtualnej.
* Lokalna pamięć podręczna zawiera jednorazową kopię folderów _/Konfiguracja_ i _/siteextensions_ magazynu zawartości udostępnionej odpowiednio w _D:\home\site_ i _D:\home\siteextensions_. Pliki są kopiowane do lokalnej pamięci podręcznej podczas uruchamiania aplikacji. Rozmiar dwóch folderów dla każdej aplikacji jest domyślnie ograniczony do 1 GB, ale można go zwiększyć do 2 GB. Należy pamiętać, że w miarę wzrostu rozmiaru pamięci podręcznej ładowanie pamięci podręcznej zajmie więcej czasu. Jeśli zwiększono limit lokalnej pamięci podręcznej na 2 GB, a liczba skopiowanych plików przekracza maksymalny rozmiar wynoszący 2 GB, App Service dyskretnie ignoruje lokalną pamięć podręczną i odczytuje ze zdalnego udziału plików. Jeśli nie zdefiniowano limitu lub limit wynosi mniej niż 2 GB, a skopiowane pliki przekroczą limit, wdrożenie lub zamiana może zakończyć się niepowodzeniem z powodu błędu.
* Lokalna pamięć podręczna to odczyt i zapis. Jednak jakakolwiek modyfikacja zostanie odrzucona, gdy aplikacja przenosi maszyny wirtualne lub zostanie uruchomiona ponownie. Nie należy używać lokalnej pamięci podręcznej dla aplikacji, które przechowują dane o kluczowym znaczeniu w magazynie zawartości.
* _D:\home\LogFiles_ i _D:\home\Data_ zawierają pliki dziennika i dane aplikacji. Dwa podfoldery są przechowywane lokalnie w wystąpieniu maszyny wirtualnej i są kopiowane do magazynu zawartości udostępnionej okresowo. Aplikacje mogą utrwalać pliki i dane dziennika, pisząc je w tych folderach. Kopiowanie do magazynu zawartości udostępnionej jest jednak najlepszym rozwiązaniem, więc możliwe jest utratę plików dziennika i danych z powodu nagłej awarii wystąpienia maszyny wirtualnej.
* Jest to wpływ na [przesyłanie strumieniowe dzienników](troubleshoot-diagnostic-logs.md#stream-logs) . W dziennikach przesyłanych strumieniowo można obserwować maksymalnie jedną minutę.
* W magazynie zawartości udostępnionej istnieje zmiana struktury folderów _plików dziennika_ i folderów _danych_ dla aplikacji korzystających z lokalnej pamięci podręcznej. Istnieją teraz podfoldery, które są zgodne ze wzorcem nazewnictwa "unikatowy identyfikator" + sygnatura czasowa. Każdy z podfolderów odnosi się do wystąpienia maszyny wirtualnej, w której uruchomiona jest aplikacja lub została uruchomiona.
* Inne foldery w _D:\home_ pozostają w lokalnej pamięci podręcznej i nie zostaną skopiowane do magazynu zawartości udostępnionej.
* Wdrażanie aplikacji za pomocą dowolnej obsługiwanej metody publikuje się bezpośrednio w magazynie trwałych zawartości udostępnionej. Aby odświeżyć foldery _D:\home\site_ i _D:\home\siteextensions_ w lokalnej pamięci podręcznej, należy ponownie uruchomić aplikację. Aby zapewnić bezproblemowe cykl życia, zapoznaj się z informacjami w dalszej części tego artykułu.
* Domyślny widok zawartości witryny SCM będzie nadal znajdować się w magazynie zawartości udostępnionej.

## <a name="enable-local-cache-in-app-service"></a>Włącz lokalną pamięć podręczną w App Service
Lokalna pamięć podręczna jest konfigurowana przy użyciu kombinacji ustawień aplikacji zarezerwowanych. Te ustawienia aplikacji można skonfigurować przy użyciu następujących metod:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Konfigurowanie lokalnej pamięci podręcznej przy użyciu Azure Portal
<a name="Configure-Local-Cache-Portal"></a>

Lokalna pamięć podręczna jest włączana dla poszczególnych aplikacji dla sieci Web przy użyciu tego ustawienia aplikacji: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Ustawienia aplikacji Azure Portal: lokalna pamięć podręczna](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Konfigurowanie lokalnej pamięci podręcznej przy użyciu Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "1000"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Zmień ustawienie rozmiaru w lokalnej pamięci podręcznej
Domyślnie rozmiar lokalnej pamięci podręcznej wynosi **1 GB**. Obejmuje to foldery/konfiguracja i/siteextensions, które są kopiowane z magazynu zawartości, a także wszystkie utworzone lokalnie dzienniki i foldery danych. Aby zwiększyć ten limit, użyj ustawienia aplikacji `WEBSITE_LOCAL_CACHE_SIZEINMB` . Można zwiększyć rozmiar do **2 GB** (2000 MB) na aplikację. Należy pamiętać, że ładowanie lokalnej pamięci podręcznej będzie trwać dłużej w miarę wzrostu rozmiaru.

## <a name="best-practices-for-using-app-service-local-cache"></a>Najlepsze rozwiązania dotyczące korzystania z App Service lokalnej pamięci podręcznej
Zalecamy używanie lokalnej pamięci podręcznej w połączeniu z funkcją [środowisk przejściowych](../app-service/deploy-staging-slots.md) .

* Dodaj ustawienie aplikacji *Sticky Notes* `WEBSITE_LOCAL_CACHE_OPTION` z wartością `Always` do miejsca **produkcyjnego** . Jeśli używasz programu `WEBSITE_LOCAL_CACHE_SIZEINMB` , Dodaj go również jako ustawienie programu Sticky Notes do miejsca produkcyjnego.
* Utwórz miejsce **przejściowe** i Opublikuj je w miejscu przejściowym. Zazwyczaj nie ustawia się miejsca przejściowego na używanie lokalnej pamięci podręcznej, aby umożliwić bezproblemowe cykle kompilowania wdrożeń na potrzeby testowania dla miejsca przejściowego, jeśli masz zalety lokalnej pamięci podręcznej w miejscu produkcyjnym.
* Przetestuj lokację względem miejsca przejściowego.  
* Gdy wszystko będzie gotowe, wystaw [operację wymiany między miejscami](../app-service/deploy-staging-slots.md#Swap) przejściowymi i produkcyjnymi.  
* Ustawienia programu Sticky Notes obejmują nazwę i program Sticky Notes w gnieździe. Tak więc gdy miejsce przejściowe zostanie zamienione na środowisko produkcyjne, dziedziczy ustawienia lokalnej pamięci podręcznej. Nowo zamienione miejsce produkcyjne będzie uruchamiane w lokalnej pamięci podręcznej po kilku minutach i zostanie rozgrzane jako część gniazda rozgrzewania po wymianie. W związku z tym po zakończeniu wymiany gniazd miejsce produkcyjne jest uruchamiane względem lokalnej pamięci podręcznej.

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Jak mogę sprawdzić, czy lokalna pamięć podręczna dotyczy mojej aplikacji?
Jeśli aplikacja wymaga magazynu o wysokiej wydajności i niezawodnej zawartości, program nie będzie używać magazynu zawartości do zapisywania krytycznych danych w czasie wykonywania i jest mniejszy niż 2 GB w łącznym rozmiarze, a następnie odpowiedź ma wartość "yes". Aby uzyskać łączny rozmiar folderów/konfiguracja i/siteextensions, można użyć rozszerzenia witryny "Azure Web Apps Disk Usage".

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Jak sprawdzić, czy moja witryna przełączyła się w celu korzystania z lokalnej pamięci podręcznej?
Jeśli używasz funkcji lokalnej pamięci podręcznej w środowiskach przejściowych, operacja wymiany nie zostanie zakończona do momentu wyczerpania lokalnej pamięci podręcznej. Aby sprawdzić, czy lokacja jest uruchomiona względem lokalnej pamięci podręcznej, można sprawdzić zmienną środowiskową procesu roboczego `WEBSITE_LOCALCACHE_READY` . Użyj instrukcji na stronie [zmienna środowiskowa procesu roboczego](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) , aby uzyskać dostęp do zmiennej środowiskowej procesu roboczego w wielu wystąpieniach.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Właśnie opublikowano nowe zmiany, ale moja aplikacja nie wygląda na to. Dlaczego?
Jeśli aplikacja korzysta z lokalnej pamięci podręcznej, należy ponownie uruchomić lokację, aby uzyskać najnowsze zmiany. Nie chcesz publikować zmian w lokacji produkcyjnej? Zobacz Opcje gniazda w sekcji poprzednie najlepsze rozwiązania.

> [!NOTE]
> Opcja [uruchamiania z pakietu](deploy-run-package.md) nie jest zgodna z lokalną pamięcią podręczną.

### <a name="where-are-my-logs"></a>Gdzie znajdują się moje dzienniki?
W przypadku lokalnej pamięci podręcznej dzienniki i foldery danych wyglądają nieco inaczej. Jednak struktura podfolderów pozostaje taka sama, z tą różnicą, że podfoldery są Nestled w podfolderze o formacie "unikatowy identyfikator maszyny wirtualnej" + sygnatura czasowa.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Włączono lokalną pamięć podręczną, ale moja aplikacja nadal jest uruchomiona ponownie. Dlaczego? Uważam, że lokalna pamięć podręczna jest pomocna przy częstym uruchamianiu aplikacji.
Lokalna pamięć podręczna pomaga zapobiegać ponownym uruchomieniu aplikacji powiązanych z magazynem. Jednak aplikacja może nadal zostać ponownie uruchomiona podczas uaktualniania zaplanowanych infrastruktury maszyny wirtualnej. Ogólne ponowne uruchomienia aplikacji z włączoną obsługą lokalnej pamięci podręcznej powinny być mniejsze.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Czy lokalna pamięć podręczna wyklucza wszystkie katalogi, które nie zostaną skopiowane na szybszy dysk lokalny?
W ramach kroku, który kopiuje zawartość magazynu, wykluczony jest folder o nazwie repozytorium. Dzięki temu scenariusze, w których zawartość witryny może zawierać repozytorium kontroli źródła, które mogą nie być konieczne w codziennym działaniu aplikacji. 
