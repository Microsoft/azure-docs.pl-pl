---
title: 'Samouczek: Dodawanie magazynu do klastra usługi Azure FXT Edge'
description: Jak skonfigurować magazyn zaplecza i pseudonamespace na klientach dla usługi Azure FXT Edge
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79239217"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Samouczek: Dodawanie magazynu zaplecza i konfigurowanie wirtualnej przestrzeni nazw 

W tym samouczku wyjaśniono, jak dodać magazyn zaplecza do pamięci podręcznej oraz jak skonfigurować wirtualny system plików dla klienta. 

Klaster nawiązuje połączenie z systemami magazynu zaplecza, aby uzyskać dostęp do żądania klientów danych i przechowywać zmiany w sposób trwały niż w pamięci podręcznej. 

Przestrzeń nazw to kompletny system plików klienta, który umożliwia wymianę magazynu zaplecza bez zmieniania przepływów pracy po stronie klienta. 

Ten samouczek zawiera następujące informacje: 

> [!div class="checklist"]
> * Jak dodać magazyn zaplecza do klastra usługi Azure FXT Edge 
> * Jak zdefiniować ścieżkę dostępną dla klienta dla magazynu

## <a name="about-back-end-storage"></a>Informacje o magazynie zaplecza

Klaster usługi Microsoft Azure FXT Edge używa podstawowej definicji *pliku* do łączenia systemu magazynu zaplecza z klastrem FXT.

Usługa Azure FXT Edge jest zgodna z kilkoma popularnymi systemami sprzętowymi NAS i może używać pustych kontenerów z poziomu obiektów blob platformy Azure lub innych magazynów w chmurze. 

Kontenery magazynu w chmurze muszą być puste po dodaniu, aby system operacyjny FXT mógł całkowicie zarządzać wszystkimi danymi w woluminie magazynu w chmurze. Istniejące dane można przenieść do kontenera chmury po dodaniu kontenera do klastra jako podstawowy plik.

Za pomocą panelu sterowania Dodaj podstawowy plik do systemu.

> [!NOTE]
> 
> Jeśli chcesz korzystać z usługi Amazon AWS lub magazynu Google Cloud, musisz zainstalować licencję funkcji FlashCloud<sup>TM</sup> . Skontaktuj się z przedstawicielem firmy Microsoft, aby uzyskać klucz licencji, a następnie postępuj zgodnie z instrukcjami w starszej wersji przewodnika konfiguracji, aby [dodać lub usunąć licencje funkcji](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
> 
> Obsługa magazynu obiektów blob platformy Azure jest uwzględniona w licencji na oprogramowanie usługi Azure FXT Edge. 

Aby uzyskać szczegółowe informacje na temat dodawania plików podstawowych, zapoznaj się z tymi sekcjami przewodnika po konfiguracji klastra:

* Aby uzyskać więcej informacji na temat wyboru i przygotowania do dodania podstawowego pliku, przeczytaj artykuł [Praca z plikami podstawowymi](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview).
* Aby uzyskać szczegółowe wymagania wstępne i instrukcje krok po kroku, przeczytaj następujące artykuły:

  * [Dodawanie nowego pliku podstawowego serwera NAS](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Dodawanie nowego pliku podstawowego w chmurze](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Po dodaniu podstawowego pliku programu można zaktualizować jego ustawienia na stronie ustawień szczegółów podstawowych plików.

## <a name="add-a-core-filer"></a>Dodawanie podstawowego pliku

Zdefiniuj podstawowy plik programu, klikając przycisk **Utwórz** na stronie **podstawowe**  >  ustawienia**zarządzania plikami podstawowymi** plików.

![Kliknięcie przycisku Utwórz powyżej listy plików podstawowych na stronie Zarządzanie podstawowymi plikami plików](media/fxt-cluster-config/create-core-filer-button.png)

Kreator **dodawania nowego pliku podstawowego** zawiera szczegółowe omówienie procesu tworzenia podstawowego pliku, który łączy się z magazynem zaplecza. Przewodnik konfiguracji klastra zawiera opisy krok po kroku procesu, które różnią się w przypadku magazynu NFS/NAS i magazynu w chmurze (linki znajdują się powyżej). 

Podzadania obejmują:

* Określ typ pliku podstawowego (NAS lub Cloud)

  ![Pierwsza strona Kreatora nowego podstawowego pliku sprzętowego serwera NAS. Opcja "podstawowa usługa plików w chmurze" jest wyłączona i zawiera komunikat o błędzie dotyczący brakującej licencji.](media/fxt-cluster-config/new-nas-1.png)

* Ustaw nazwę podstawowego pliku. Wybierz nazwę, która pomaga administratorom klastra zrozumieć, który system pamięci masowej reprezentuje.

* W przypadku podstawowych plików usługi NAS podaj w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP. Nazwa FQDN jest zalecana dla wszystkich plików podstawowych i jest wymagana w przypadku dostępu do protokołu SMB.

* Wybierz zasady pamięci podręcznej — druga strona kreatora zawiera listę dostępnych zasad pamięci podręcznej dla nowego pliku podstawowego. Aby uzyskać szczegółowe informacje, Przeczytaj [sekcję zasady pamięci podręcznej w podręczniku konfigurowania klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html). 

  ![Druga strona Kreatora nowego podstawowego pliku sprzętowego serwera NAS; menu rozwijane zasady pamięci podręcznej jest otwarte, pokazując kilka wyłączonych opcji i trzy prawidłowe opcje zasad pamięci podręcznej (obejście, buforowanie odczytu i buforowanie odczytu/zapisu).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* W przypadku magazynu w chmurze należy określić usługę w chmurze i poświadczenia dostępu między innymi parametrami. Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [Usługa w chmurze i protokół](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) w podręczniku konfiguracji klastra.

  ![Informacje o podstawowym pliku w chmurze w nowym Kreatorze podstawowego pliku](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  Jeśli dodano już poświadczenia dostępu do chmury dla tego klastra, zostaną one wyświetlone na liście. Zaktualizuj i Dodaj poświadczenia na **Cluster**  >  stronie ustawień**poświadczeń chmury** klastra. 

Po wypełnieniu wszystkich wymaganych ustawień w Kreatorze kliknij przycisk **Dodaj plik** , aby przesłać zmianę.

Po kilku chwilach system magazynu pojawia się na liście podstawowych plików programu **Dashboard** i można uzyskać do nich dostęp za pomocą stron ustawień podstawowych plików.

![podstawowy plik fileing "Flurry-NAS" na stronie Zarządzanie ustawieniami plików podstawowych przy użyciu widoku Szczegóły pliku rozwiniętego](media/fxt-cluster-config/core-filer-in-manage-page.png)

W podstawowym pliku na tym zrzucie ekranu brakuje vserver. Należy połączyć podstawowy plik z vserver i utworzyć połączenie, aby klienci mogli uzyskać dostęp do magazynu. Poniższe kroki opisano w temacie [Konfigurowanie przestrzeni nazw](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Skonfiguruj przestrzeń nazw

Klaster usługi Azure FXT Edge tworzy wirtualny system plików o nazwie *przestrzeń nazw klastra* , który upraszcza dostęp klienta do danych przechowywanych w różnych systemach zaplecza. Ponieważ klienci żądają plików przy użyciu ścieżki wirtualnej, systemy magazynu mogą być dodawane lub zastępowane bez konieczności zmiany przepływu pracy klienta. 

Przestrzeń nazw klastra umożliwia również prezentowanie systemów magazynu w chmurze i NAS w podobnej strukturze plików. 

Vservers klastra utrzymuje przestrzeń nazw i zapewnia obsługę zawartości klientom. Aby utworzyć przestrzeń nazw klastra, należy wykonać dwie czynności: 

1. Utwórz vserver 
1. Konfigurowanie połączeń między systemami magazynu zaplecza a ścieżkami systemu plików dostępnymi dla klienta 

### <a name="create-a-vserver"></a>Utwórz vserver

VServers są wirtualnymi serwerami plików, które kontrolują sposób przepływu danych między klientem a podstawowymi obiektami do tworzenia klastra:

* Adresy IP klientów hosta VServers
* VServers tworzenie przestrzeni nazw i Definiowanie połączeń, które mapują strukturę katalogów wirtualnych dla klienta na Eksporty w magazynie zaplecza
* VServers wymuszanie kontroli dostępu do plików, w tym podstawowych zasad eksportu plików i systemów uwierzytelniania użytkowników
* VServers udostępniaj infrastrukturę protokołu SMB

Przed rozpoczęciem konfigurowania vserver klastra zapoznaj się z połączoną dokumentacją i skontaktuj się z przedstawicielem firmy Microsoft w celu uzyskania pomocy dotyczącej przestrzeni nazw i vservers. Jeśli używasz sieci VLAN, [Utwórz je](fxt-configure-network.md#adjust-network-settings) przed utworzeniem vserver. 

Te sekcje przewodnika po konfiguracji klastra pomogą Ci zaznajomić się z funkcjami FXT vserver i globalną przestrzenią nazw:

* [Tworzenie i praca z VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Korzystanie z globalnej przestrzeni nazw](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Tworzenie VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Wymagany jest co najmniej jeden vserver klastra. 

Aby utworzyć nowy vserver, potrzebne są następujące informacje:

* Nazwa do ustawienia dla vserver

* Zakres adresów IP dostępnych dla klientów, które będą obsługiwane przez vserver

  Podczas tworzenia vserver należy podać pojedynczy zakres ciągłych adresów IP. Więcej adresów można dodać później, korzystając ze strony ustawień sieci nakierowanych na **klienta** .

* Jeśli sieć ma sieci VLAN, których sieci VLAN użyć dla tego vserver

Korzystając ze strony **vserver**  >  **Zarządzaj ustawieniami VServers** , można utworzyć nową vserver. Aby uzyskać szczegółowe informacje [, zobacz Tworzenie vserver](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) w podręczniku konfiguracji klastra. 

![okno podręczne tworzenia nowego vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Tworzenie połączenia

*Połączenie* mapuje ścieżkę magazynu zaplecza na przestrzeń nazw widoczną dla klienta.

Tego systemu można użyć do uproszczenia ścieżki używanej w punktach instalacji klienta oraz bezproblemowego skalowania pojemności, ponieważ jedna ścieżka wirtualna może obsługiwać magazyn z wielu podstawowych plików.

![Strona Kreatora dodawania nowej gałęzi z ustawieniami wypełnionymi](media/fxt-cluster-config/add-junction-full.png)

Zapoznaj się z [ **VServer**  >  **przestrzenią nazw** vserver](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) w podręczniku konfigurowania klastra, aby uzyskać szczegółowe informacje na temat tworzenia rozgałęzienia przestrzeni nazw.

![Strona ustawienia przestrzeni nazw VServer > przedstawiające szczegóły rozgałęzienia](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Konfigurowanie reguł eksportu

Po utworzeniu zarówno vserver, jak i podstawowego pliku, należy dostosować reguły eksportu i wyeksportować zasady kontrolujące sposób, w jaki klienci mogą uzyskiwać dostęp do plików w ramach eksportu plików podstawowych.

Najpierw użyj strony **vserver**  >  **Export Rules** , aby dodać nowe reguły, zmodyfikować zasady domyślne lub utworzyć własne niestandardowe zasady eksportowania.

Po drugie Użyj strony **vserver**  >  **Export** Policy, aby zastosować dostosowane zasady do eksportu podstawowego pliku w przypadku dostępu za pośrednictwem tego vserver.

Przeczytaj artykuł Przewodnik po konfiguracji klastra [kontrolujący dostęp do podstawowych eksportu plików,](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) Aby uzyskać szczegółowe informacje.


## <a name="next-steps"></a>Następne kroki

Po dodaniu magazynu i skonfigurowaniu przestrzeni nazw dla klienta Ukończ początkową konfigurację klastra: 

> [!div class="nextstepaction"]
> [Konfigurowanie ustawień sieci klastrów](fxt-configure-network.md)
