---
title: Lokalna pamięć podręczna
description: Dowiedz się, jak działa lokalna pamięć podręczna w usłudze Azure App Service oraz jak włączyć, zmienić rozmiar i zbadać stan lokalnej pamięci podręcznej aplikacji.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 1945730acaddb0c1c7ee1b28eeb926635efad643
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78227884"
---
# <a name="azure-app-service-local-cache-overview"></a>Omówienie lokalnej pamięci podręcznej usługi Azure App Service

> [!NOTE]
> Lokalna pamięć podręczna nie jest obsługiwana w aplikacjach funkcji ani konteneryzowanych aplikacjach usługi App Service, takich jak [kontenery systemu Windows](app-service-web-get-started-windows-container.md) lub [usługa App Service w systemie Linux](containers/app-service-linux-intro.md).


Zawartość usługi Azure App Service jest przechowywana w usłudze Azure Storage i jest udostępniana w trwały sposób jako udział zawartości. Ten projekt jest przeznaczony do pracy z różnymi aplikacjami i ma następujące atrybuty:  

* Zawartość jest współużytkowana przez wiele wystąpień aplikacji na wielu maszynach wirtualnych.The content is shared across multiple virtual machine (VM) instances of the app.
* Zawartość jest trwała i może być modyfikowana przez uruchamianie aplikacji.
* Pliki dziennika i pliki danych diagnostycznych są dostępne w tym samym folderze udostępnionej zawartości.
* Publikowanie nowej zawartości bezpośrednio aktualizuje folder zawartości. Możesz natychmiast wyświetlić tę samą zawartość za pośrednictwem witryny SCM i uruchomionej aplikacji (zazwyczaj niektóre technologie, takie jak ASP.NET inicjują ponowne uruchomienie aplikacji na niektóre zmiany plików, aby uzyskać najnowszą zawartość).

Podczas gdy wiele aplikacji korzysta z jednej lub wszystkich z tych funkcji, niektóre aplikacje potrzebują tylko wydajnego sklepu z zawartością tylko do odczytu, z którego mogą być uruchamiane z wysoką dostępnością. Te aplikacje mogą korzystać z wystąpienia maszyny Wirtualnej określonej lokalnej pamięci podręcznej.

Funkcja lokalnej pamięci podręcznej usługi Azure App Service udostępnia widok roli sieci Web zawartości. Ta zawartość jest pamięcią podręczną zapisu, ale odrzucaj zawartość magazynu, która jest tworzona asynchronicznie w miejscu uruchamiania. Gdy pamięć podręczna jest gotowa, lokacja jest przełączana do uruchamiania względem zawartości buforowanej. Aplikacje uruchamiane w lokalnej pamięci podręcznej mają następujące zalety:

* Są one odporne na opóźnienia, które występują podczas uzyskiwania dostępu do zawartości w usłudze Azure Storage.
* Są one odporne na planowane uaktualnienia lub nieplanowane przestoje i wszelkie inne zakłócenia w usłudze Azure Storage, które występują na serwerach, które obsługują udział w zawartości.
* Mają mniej ponownych uruchomień aplikacji z powodu zmian udziału magazynu.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Jak lokalna pamięć podręczna zmienia zachowanie usługi App Service
* _D:\home_ wskazuje lokalną pamięć podręczną, która jest tworzona w wystąpieniu maszyny Wirtualnej podczas uruchamiania aplikacji. _D:\local_ kontynuuje wskazywalowanie tymczasowego magazynu specyficznego dla maszyny Wirtualnej.
* Lokalna pamięć podręczna zawiera jednorazową kopię folderów _/site_ i _/siteextensions_ udostępnionego magazynu zawartości, odpowiednio w _D:\home\site_ i _D:\home\siteextensions._ Pliki są kopiowane do lokalnej pamięci podręcznej po uruchomieniu aplikacji. Rozmiar dwóch folderów dla każdej aplikacji jest domyślnie ograniczony do 300 MB, ale można go zwiększyć do 2 GB. Jeśli skopiowane pliki przekraczają rozmiar lokalnej pamięci podręcznej, usługa App Service po cichu ignoruje lokalną pamięć podręczną i odczyt z zdalnego udziału plików.
* Lokalna pamięć podręczna jest odczytu i zapisu. Jednak wszelkie modyfikacje są odrzucane, gdy aplikacja przenosi maszyny wirtualne lub zostanie ponownie uruchomiony. Nie należy używać lokalnej pamięci podręcznej dla aplikacji, które przechowują dane o znaczeniu krytycznym w magazynie zawartości.
* _D:\home\LogFiles_ i _D:\home\Dane_ zawierają pliki dziennika i dane aplikacji. Dwa podfoldery są przechowywane lokalnie w wystąpieniu maszyny Wirtualnej i są okresowo kopiowane do udostępnionego magazynu zawartości. Aplikacje mogą utrwalać pliki dziennika i dane, zapisując je w tych folderach. Jednak kopia do magazynu zawartości udostępnionej jest najlepszym wysiłkiem, więc jest możliwe dla plików dziennika i danych, które mają zostać utracone z powodu nagłej awarii wystąpienia maszyny Wirtualnej.
* [Przesyłanie strumieniowe dziennika](troubleshoot-diagnostic-logs.md#stream-logs) zależy od kopii najlepszych starań. Można zaobserwować maksymalnie jednominutowe opóźnienie w dziennikach przesyłanych strumieniowo.
* W magazynie zawartości udostępnionej nastąpiła zmiana w strukturze folderów _LogFiles_ i _Data_ dla aplikacji korzystających z lokalnej pamięci podręcznej. Obecnie znajdują się w nich podfoldery, które są zgodne ze wzorcem nazewnictwa "unikatowy identyfikator" + sygnatura czasowa. Każdy z podfolderów odpowiada wystąpieniu maszyny Wirtualnej, w którym aplikacja jest uruchomiona lub została uruchomiona.
* Inne foldery w _D:\home_ pozostają w lokalnej pamięci podręcznej i nie są kopiowane do udostępnionego magazynu zawartości.
* Wdrażanie aplikacji za pośrednictwem dowolnej obsługiwanej metody publikuje bezpośrednio do magazynu zawartości udostępnionej trwałe. Aby odświeżyć foldery _D:\home\site_ i _D:\home\siteextensions_ w lokalnej pamięci podręcznej, aplikacja musi zostać ponownie uruchomiona. Aby ułatwić bezproblemowy cykl życia, zobacz informacje w dalszej części tego artykułu.
* Domyślnym widokiem zawartości witryny SCM nadal jest widok udostępnionego magazynu zawartości.

## <a name="enable-local-cache-in-app-service"></a>Włączanie lokalnej pamięci podręcznej w usłudze aplikacji
Konfigurowanie lokalnej pamięci podręcznej przy użyciu kombinacji ustawień aplikacji zarezerwowanej. Te ustawienia aplikacji można skonfigurować przy użyciu następujących metod:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Konfigurowanie lokalnej pamięci podręcznej przy użyciu witryny Azure portal
<a name="Configure-Local-Cache-Portal"></a>

Włącz lokalną pamięć podręczną na podstawie aplikacji sieci Web przy użyciu tego ustawienia aplikacji:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Ustawienia aplikacji portalu Azure: lokalna pamięć podręczna](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Konfigurowanie lokalnej pamięci podręcznej przy użyciu usługi Azure Resource Manager
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
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Zmienianie ustawienia rozmiaru w lokalnej pamięci podręcznej
Domyślnie rozmiar lokalnej pamięci podręcznej wynosi **300 MB**. Obejmuje to foldery /site i /siteextensions, które są kopiowane z magazynu zawartości, a także wszelkie lokalnie utworzone dzienniki i foldery danych. Aby zwiększyć ten limit, `WEBSITE_LOCAL_CACHE_SIZEINMB`użyj ustawienia aplikacji . Rozmiar można zwiększyć do **2 GB** (2000 MB) na aplikację.

## <a name="best-practices-for-using-app-service-local-cache"></a>Najważniejsze wskazówki dotyczące korzystania z lokalnej pamięci podręcznej usługi App Service
Zaleca się używanie lokalnej pamięci podręcznej w połączeniu z funkcją [Środowiska przejściowe.](../app-service/deploy-staging-slots.md)

* Dodaj `WEBSITE_LOCAL_CACHE_OPTION` *ustawienie lepkiej* `Always` aplikacji z wartością do gniazda **produkcyjnego.** Jeśli używasz `WEBSITE_LOCAL_CACHE_SIZEINMB`, dodaj go również jako lepkie ustawienie do gniazda produkcyjnego.
* Utwórz miejsce **przejściowe** i opublikuj je w miejscu przejściowym. Zazwyczaj nie należy ustawiać miejsca przejściowego, aby użyć lokalnej pamięci podręcznej, aby włączyć bezproblemowy cykl życia kompilacji i wdrożenia testu dla przemieszczania, jeśli uzyskasz korzyści z lokalnej pamięci podręcznej dla gniazda produkcyjnego.
* Przetestuj witrynę pod względem miejsca przemieszczania.  
* Gdy będzie gotowy, wystaw [operację wymiany](../app-service/deploy-staging-slots.md#Swap) między miejscami przejściowymi i produkcyjnymi.  
* Ustawienia przyklejone obejmują nazwę i przyklejony do gniazda. Tak więc, gdy miejsce przemieszczania zostanie zamienione na produkcję, dziedziczy ustawienia aplikacji lokalnej pamięci podręcznej. Nowo zamienione gniazdo produkcyjne zostanie uruchomione w lokalnej pamięci podręcznej po kilku minutach i zostanie rozgrzane jako część rozgrzewki gniazda po wymianie. Po zakończeniu wymiany gniazda gniazdo jest uruchomione w lokalnej pamięci podręcznej.

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Jak sprawdzić, czy lokalna pamięć podręczna dotyczy mojej aplikacji?
Jeśli aplikacja potrzebuje wysokiej wydajności, niezawodny magazyn zawartości, nie używa magazynu zawartości do zapisu krytycznych danych w czasie wykonywania i jest mniejsza niż 2 GB w całkowitym rozmiarze, odpowiedź brzmi "tak"! Aby uzyskać całkowity rozmiar folderów /site i /siteextensions, możesz użyć rozszerzenia witryny "Użycie dysku aplikacji Azure Web Apps".

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Jak sprawdzić, czy moja witryna przełączyła się na korzystanie z lokalnej pamięci podręcznej?
Jeśli używasz funkcji lokalnej pamięci podręcznej ze środowiskami przejściowymi, operacja wymiany nie zostanie ukończona, dopóki pamięć podręczna lokalna nie zostanie rozgrzana. Aby sprawdzić, czy witryna jest uruchomiona w lokalnej pamięci `WEBSITE_LOCALCACHE_READY`podręcznej, można sprawdzić zmienną środowiska procesu roboczego . Użyj instrukcji na stronie [zmiennej środowiskowej procesu roboczego,](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) aby uzyskać dostęp do zmiennej środowiskowej procesu roboczego w wielu wystąpieniach.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Właśnie opublikowałem nowe zmiany, ale moja aplikacja nie wydaje się je mieć. Dlaczego?
Jeśli aplikacja korzysta z lokalnej pamięci podręcznej, musisz ponownie uruchomić witrynę, aby uzyskać najnowsze zmiany. Nie chcesz publikować zmian w zakładzie produkcyjnym? Zobacz opcje gniazda w poprzedniej sekcji najważniejsze wskazówki.

### <a name="where-are-my-logs"></a>Gdzie są moje dzienniki?
W lokalnej pamięci podręcznej dzienniki i foldery danych wyglądają nieco inaczej. Jednak struktura podfolderów pozostaje taka sama, z tą różnicą, że podfoldery są zagnieżdżone podfolderem o formacie "unikatowy identyfikator maszyny Wirtualnej" + sygnatura czasowa.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Mam włączoną lokalną pamięć podręczną, ale moja aplikacja nadal jest uruchamiana ponownie. Dlaczego? Myślałem, że lokalna pamięć podręczna pomogła w częstym ponownym uruchomieniu aplikacji.
Lokalna pamięć podręczna pomaga zapobiegać ponownemu uruchomieniu aplikacji związanych z magazynem. Jednak aplikacja może nadal ulegać ponownemu uruchomieniu podczas planowanych uaktualnień infrastruktury maszyny Wirtualnej. Ogólna aplikacja uruchamia ponownie, że wystąpi z włączoną lokalną pamięć podręczną powinna być mniejsza.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Czy lokalna pamięć podręczna wyklucza skopiowanie katalogów na szybszy dysk lokalny?
W ramach kroku, który kopiuje zawartość magazynu, każdy folder o nazwie repozytorium jest wykluczony. Pomaga to w scenariuszach, w których zawartość witryny może zawierać repozytorium kontroli źródła, które może nie być potrzebne w codziennym działaniu aplikacji. 
