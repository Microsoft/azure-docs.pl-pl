---
title: Cykl życia pomocy technicznej usługi Azure Red Hat OpenShift
description: Poznaj cykl wsparcia technicznego i obsługiwane wersje usługi Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: fca01c77a1ff47cbeee167eb408ed9f29a1307bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634332"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Cykl życia wsparcia platformy Azure Red Hat OpenShift 4

Red Hat zwalnia drobne wersje OpenShift kontenerów platformy Red Hat (OCP) co trzy miesiące. Te wersje obejmują nowe funkcje i ulepszenia. Wersje poprawek są częstsze (zazwyczaj cotygodniowo) i są przeznaczone tylko dla krytycznych poprawek błędów w wersji pomocniczej. Te wersje poprawek mogą zawierać poprawki dotyczące luk w zabezpieczeniach lub poważnych usterek.

Azure Red Hat OpenShift jest oparty na określonych wersjach OCP. W tym artykule opisano wersje OCP, które są obsługiwane przez usługę Azure Red Hat OpenShift oraz szczegółowe informacje o uaktualnieniach, zaniechaniu i zasadach pomocy technicznej.

## <a name="red-hat-openshift-versions"></a>Red Hat OpenShift wersje

Platforma kontenera Red Hat OpenShift używa wersji semantycznej. W przypadku wersji semantycznej są używane różne poziomy numerów wersji w celu określenia różnych poziomów wersji. W poniższej tabeli przedstawiono różne części numeru wersji semantycznej, w tym przypadku przy użyciu przykładowego numeru wersji 4.4.11.

|Wersja główna (x)|Wersja pomocnicza (y)|Poprawka (z)|
|-|-|-|
|4|4|11|

Każda liczba w wersji wskazuje ogólną zgodność z poprzednią wersją:

* **Wersja główna**: w tej chwili nie są planowane żadne wersje główne wersji. Wersje główne zmieniają się, gdy niezgodne zmiany interfejsu API lub zgodność z poprzednimi wersjami mogą zostać uszkodzone.
* **Wersja pomocnicza**: wydano około co trzy miesiące. Uaktualnienia wersji pomocniczej mogą obejmować Dodatki do funkcji, ulepszenia, operacje usuwania, poprawki błędów, ulepszenia zabezpieczeń i inne ulepszenia.
* **Poprawki**: są zwykle wydawane co tydzień lub w razie konieczności. Uaktualnienia wersji poprawek mogą zawierać poprawki błędów, ulepszenia zabezpieczeń i inne ulepszenia.

Klienci powinni mieć możliwość uruchomienia najnowszej wersji, która jest uruchomiona. Na przykład jeśli klaster produkcyjny znajduje się na 4,4, a 4,5 to najnowsza ogólnie dostępna wersja dla 4 serii, należy przeprowadzić uaktualnienie do 4,5 jak najszybciej.

### <a name="upgrade-channels"></a>Kanały uaktualnienia

Kanały uaktualniania są powiązane z wersją pomocniczą platformy kontenera Red Hat OpenShift (OCP). Na przykład kanały uaktualnienia OCP 4,4 nigdy nie będą obejmować uaktualnienia do wersji 4,5. Kontrola kanałów uaktualnia tylko wybór wydania i nie ma wpływu na wersję klastra.

Azure Red Hat OpenShift 4 obsługuje tylko stabilne kanały. Na przykład: stabilne-4,4.

Możesz użyć kanału stabilnego-4,5, aby uaktualnić poprzednią wersję pomocniczą usługi Azure Red Hat OpenShift. Klastry uaktualnione za pomocą szybkich i przednich kanałów nie będą obsługiwane.

Jeśli zmienisz kanał, który nie zawiera bieżącej wersji, zostanie wyświetlony alert, a aktualizacje nie mogą być zalecane, ale możesz bezpiecznie zmienić je z powrotem do oryginalnego kanału w dowolnym momencie.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Zasady obsługi wersji platformy kontenera Red Hat OpenShift

W systemie Azure Red Hat OpenShift są obsługiwane dwie wersje pomocnicze platformy kontenera Red Hat OpenShift:
* Najnowsza wersja pomocnicza systemu, wydawana w systemie Red Hat OpenShift (którą będziemy odnosić jako N)
* Jedna Poprzednia wersja pomocnicza (N-1)

Jeśli jest dostępny w stabilnym kanale uaktualnienia, można również obsługiwać nowsze wersje pomocnicze (N + 1, N + 2) dostępne w nadrzędnym elemencie.

Aktualizacje poprawek krytycznych są automatycznie stosowane do klastrów przez usługę Azure Red Hat OpenShift site niezawodność inżynierów (SRE). Klienci, którzy chcą zainstalować aktualizacje poprawek z wyprzedzeniem, są bezpłatne.

Na przykład jeśli w systemie Azure Red Hat OpenShift wprowadzono 4.5. z dzisiaj, pomoc techniczna jest świadczona dla następujących wersji:

|Nowa wersja pomocnicza|Lista obsługiwanych wersji|
|-|-|
|4.5. z|4.5. z, 4.4. z|

". z" jest reprezentatywna dla wersji poprawek. Jeśli jest dostępny w stabilnym kanale uaktualnienia, klienci mogą również uaktualnić program do wersji 4.6. z.

Gdy zostanie wprowadzona nowa wersja pomocnicza, najstarsza wersja pomocnicza jest przestarzała i usunięta. Załóżmy na przykład, że aktualna lista obsługiwanych wersji to 4.5. z i 4.4. z. Gdy usługa Azure Red Hat OpenShift w wersji 4.6. z, wersja 4.4. z zostanie usunięta i nie będzie można jej obsłużyć w ciągu 30 dni.

> [!NOTE]
> Należy pamiętać, że jeśli klienci korzystają z nieobsługiwanej wersji systemu Red Hat OpenShift, mogą zostać poproszeni o uaktualnienie w przypadku żądania pomocy technicznej dotyczącej klastra. Klastry z nieobsługiwanymi wersjami Red Hat OpenShift nie są objęte umową SLA Red Hat OpenShift platformy Azure.

## <a name="release-and-deprecation-process"></a>Proces wydania i wycofania

Możesz odwoływać się do przyszłych wersji i zaniecheń w kalendarzu programu Azure Red Hat OpenShift.

Nowe wersje pomocnicze platformy kontenera Red Hat OpenShift:
* Zespół usługi Azure Red Hat OpenShift SRE publikuje wstępne zawiadomienie o planowanej dacie nowej wersji, a odpowiednia stara wersja jest przestarzała w [informacjach o wersji usługi Azure Red Hat OpenShift](https://github.com/Azure/OpenShift/releases) , co najmniej 30 dni przed usunięciem.
* Zespół usługi Azure Red Hat OpenShift SRE publikuje powiadomienie o kondycji usługi dostępne dla wszystkich klientów korzystających z systemu Azure Red Hat OpenShift i dostęp do portalu, a następnie wysyła wiadomość e-mail do administratorów subskrypcji z datami usunięcia planowanej wersji.
* Klienci mają 30 dni od usunięcia wersji, aby przeprowadzić uaktualnienie do obsługiwanej wersji pomocniczej, aby nadal otrzymywać pomoc techniczną.

Nowe wersje poprawek platformy kontenera Red Hat OpenShift:
* Ze względu na pilną naturę wersji poprawek mogą one zostać wprowadzone do usługi przez usługę Azure Red Hat OpenShift SRE Team, gdy staną się dostępne.
* Ogólnie rzecz biorąc, zespół usługi Azure Red Hat OpenShift SRE nie wykonuje szerokiej komunikacji na potrzeby instalacji nowych wersji poprawki. Jednak zespół stale monitoruje i sprawdza poprawność dostępnych poprawek w CVE, aby obsługiwać je w odpowiednim czasie. Jeśli wymagana jest akcja klienta, zespół powiadomi klientów o uaktualnieniu.

## <a name="supported-versions-policy-exceptions"></a>Obsługiwane wyjątki zasad wersji

Zespół usługi Azure Red Hat OpenShift SRE zastrzega sobie prawo do dodawania lub usuwania nowych/istniejących wersji lub opóźniania przyszłych wersji, które zostały zidentyfikowane, aby mieć co najmniej jeden krytyczny wpływ na błędy lub problemy z zabezpieczeniami bez wcześniejszego powiadomienia.

Określone wydania poprawek mogą być pominięte lub można przyspieszyć wprowadzanie w zależności od ważności usterki lub problemu z zabezpieczeniami.

## <a name="azure-portal-and-cli-versions"></a>Wersje Azure Portal i interfejsu wiersza polecenia

W przypadku wdrażania klastra usługi Azure Red Hat OpenShift w portalu lub za pomocą interfejsu wiersza polecenia platformy Azure klaster jest domyślnie do najnowszej wersji pomocniczej (N) i najnowszej krytycznej poprawki. Jeśli na przykład usługa Azure Red Hat OpenShift obsługuje 4.5. z i 4.4. z, domyślna wersja dla nowych instalacji to 4.5. z. Klienci, którzy chcą korzystać z najnowszej wersji klasy pomocniczej OCP (N + 1, N + 2), mogą w dowolnym momencie uaktualnić swój klaster do wszystkich wydań dostępnych w stabilnych kanałach uaktualniania.

## <a name="azure-red-hat-openshift-release-calendar"></a>Kalendarz usługi Azure Red Hat OpenShift

Zapoznaj się z następującym przewodnikiem dotyczącym [historycznej wersji platformy kontenera Red Hat OpenShift (nadrzędnych)](https://access.redhat.com/support/policy/updates/openshift/#dates).

|Wersja OCP|Wersja nadrzędna|Ogólna dostępność usługi Azure Red Hat OpenShift|Koniec okresu istnienia|
|-|-|-|-|
|4.3|Styczeń 2020 r.|Kwiecień 2020 r.| Sierpień 2020 r.|
|4.4|Maj 2020 r.|Lipiec 2020 r.|4,6 GA|
|4.5|Lipiec 2020 r.| Listopad 2020 r.|4,7 GA
|4,6|Październik 2020 r.| Luty 2021 r.|4,8 GA|

## <a name="faq"></a>Często zadawane pytania

**Co się stanie, gdy użytkownik uaktualnia klaster OpenShift z nieobsługiwaną wersją pomocniczą?**

Jeśli korzystasz z wersji N-2 lub starszej, oznacza to, że użytkownik jest poza pomocą techniczną i zostanie poproszony o uaktualnienie. Gdy uaktualnienie z wersji N-2 do N-1 powiedzie się, nastąpi powrót do naszych zasad pomocy technicznej. Na przykład:
* Jeśli najstarsza obsługiwana wersja usługi Azure Red Hat OpenShift to 4.4. z i używasz programu 4.3. z lub starszej wersji, jesteś poza pomocą techniczną.
* Gdy uaktualnienie z programu 4.3. z do 4.4. z lub nowsze powiedzie się, nastąpi powrót do naszych zasad pomocy technicznej.

Przywrócenie klastra do poprzedniej wersji lub wycofanie nie jest obsługiwane. Obsługiwane jest tylko uaktualnienie do nowszej wersji.

**Co oznacza "poza działem pomocy technicznej"?**

"Poza działem pomocy technicznej" oznacza, że uruchomiona wersja znajduje się poza listą obsługiwanych wersji, a użytkownik może zostać poproszony o uaktualnienie klastra do obsługiwanej wersji, o ile nie znajduje się w 30-dniowym okresie prolongaty po wycofaniu wersji. Ponadto w systemie Azure Red Hat OpenShift nie są oferowane żadne gwarancje środowiska uruchomieniowego lub umowy SLA dla klastrów spoza listy obsługiwanych wersji po zakończeniu 30-dniowego okresu prolongaty.
