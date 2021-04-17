---
title: Windows Virtual Desktop słownik dołączania aplikacji MSIX — Azure
description: Słownik aplikacji MSIX zawiera terminy i pojęcia.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: c5c596735ad91f38d5ba4217135a9373d2856182
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538455"
---
# <a name="msix-app-attach-glossary"></a>Słownik dołączania aplikacji MSIX

Ten artykuł zawiera listę definicji kluczowych terminów i pojęć związanych z dołączaniem aplikacji MSIX.

## <a name="msix-container"></a>Kontener MSIX

Kontener MSIX to miejsce, w którym są uruchamiane aplikacje MSIX. Aby dowiedzieć się więcej, zobacz [Kontenery MSIX.](/windows/msix/msix-container)

## <a name="msix-application"></a>Aplikacja MSIX 

Aplikacja przechowywana w . Plik MSIX.

## <a name="msix-package"></a>Pakiet MSIX 

Pakiet MSIX to plik lub aplikacja MSIX.

## <a name="msix-share"></a>Udział MSIX

Udział MSIX to udział sieciowy, który zawiera rozwinięte pakiety MSIX. Udziały MSIX muszą obsługiwać SMB 3 lub nowszy. Udziały muszą być również dostępne dla Virtual Machines (VM) na koncie systemowym puli hostów. Pakiety MSIX są przemieszczane z udziału MSIX bez konieczności przenoszenia plików aplikacji na dysk systemowy. 

## <a name="msix-image"></a>Obraz MSIX

Obraz MSIX to plik VHD, VHDx lub CIM zawierający co najmniej jedną spakowana aplikacja MSIX. Każda aplikacja jest dostarczana w obrazie MSIX przy użyciu narzędzia MSIXMGR.

## <a name="repackage"></a>Przepakowania

Ponowne pakowanie pobiera aplikację w formacie innym niż MSIX i konwertuje ją na plik MSIX przy użyciu narzędzia MSIX Packaging Tool (MPT). Aby uzyskać więcej informacji, zobacz [Omówienie narzędzia do tworzenia pakietów MSIX.](/windows/msix/packaging-tool/tool-overview)

## <a name="expand-an-msix-package"></a>Rozwijanie pakietu MSIX

Rozszerzanie pakietu MSIX jest procesem wieloetapowym. Rozszerzenie pobiera plik MSIX i umieszcza jego zawartość w pliku VHD(x) lub CIM. 

Aby rozwinąć pakiet MSIX:

1. Pobierz pakiet MSIX (plik MSIX).
2. Zmień nazwę pliku MSIX na plik zip.
3. Rozpakować wynikowy plik zip w folderze.
4. Utwórz dysk VHD o takim samym rozmiarze jak folder.
5. Zainstaluj dysk VHD.
6. Zaimicjuj dysk.
7. Utwórz partycję.
8. Sformatuj partycję.
9. Skopiuj rozpakowana zawartość na wirtualny dysk twardy.
10. Użyj narzędzia MSIXMGR, aby zastosować adresy ACL do zawartości dysku VHD.
11. Odinstaluj dysk VHD(x) lub [cim.](#cim)

## <a name="upload-an-msix-package"></a>Przekazywanie pakietu MSIX 

Przekazywanie pakietu MSIX obejmuje przekazanie wirtualnego dysku twardego (x) lub dysku [CIM](#cim) zawierającego rozwinięty pakiet MSIX do udziału MSIX.

W Windows Virtual Desktop przekazywanie odbywa się raz na udział MSIX. Po przesłaniu pakietu wszystkie pule hostów w tej samej subskrypcji mogą się do niego odwoływać.

## <a name="add-an-msix-package"></a>Dodawanie pakietu MSIX

W Windows Virtual Desktop dodanie pakietu MSIX łączy go z pulą hostów.

## <a name="publish-an-msix-package"></a>Publikowanie pakietu MSIX 

W Windows Virtual Desktop opublikowany pakiet MSIX musi być przypisany do użytkownika lub grupy użytkowników usługi domena usługi Active Directory Service (AD DS) lub Azure Active Directory (Azure AD).

## <a name="staging"></a>Przygotowanie

Proces przejściowy obejmuje dwie rzeczy:

- Instalowanie dysku VHD(x) lub [cim na](#cim) maszynie wirtualnej.
- Powiadamianie systemu operacyjnego, że pakiet MSIX jest dostępny do rejestracji.

## <a name="registration"></a>Rejestracja

Rejestracja umożliwia użytkownikom dostęp do przesetapiowego pakietu MSIX. Rejestrowanie jest rejestrowane na 1 użytkownika. Jeśli nie zarejestrowano jawnie aplikacji dla tego określonego użytkownika, nie będzie można jej uruchomić.

Istnieją dwa typy rejestracji: regularne i opóźnione.

### <a name="regular-registration"></a>Regularna rejestracja

W przypadku zwykłej rejestracji każda aplikacja przypisana do użytkownika jest w pełni zarejestrowana. Rejestracja odbywa się w czasie, gdy użytkownik się do sesji, co może mieć wpływ na czas rozpoczęcia korzystania z Windows Virtual Desktop.

### <a name="delayed-registration"></a>Opóźniona rejestracja

W przypadku opóźnionej rejestracji każda aplikacja przypisana do użytkownika jest tylko częściowo zarejestrowana. Rejestracja częściowa oznacza, że menu Start i skojarzenia plików dwukrotnego kliknięcia są rejestrowane. Rejestracja odbywa się podczas rejestrowania się użytkownika w sesji, więc ma minimalny wpływ na czas rozpoczęcia korzystania z Windows Virtual Desktop. Rejestracja zostanie ukończona tylko wtedy, gdy użytkownik uruchomi aplikację w pakiecie MSIX.

Opóźniona rejestracja jest obecnie konfiguracją domyślną dołączania aplikacji MSIX.

## <a name="deregistration"></a>Wyrejestrowanie

Wyrejestrowanie usuwa zarejestrowany, ale nie uruchomiony pakiet MSIX dla użytkownika. Wyrejestrowanie ma miejsce, gdy użytkownik wyrejestrować się ze swojej sesji. Podczas wyrejestrowania aplikacja MSIX dołącza dane aplikacji specyficzne dla użytkownika do profilu użytkownika lokalnego.

## <a name="destage"></a>Destage (Destage)

Destaging powiadamia system operacyjny, że pakiet MSIX lub aplikacja, która obecnie nie jest uruchomiona i nie jest przeinstalowana dla żadnego użytkownika, może zostać odinstalowana. Spowoduje to usunięcie wszystkich odwoływać się do niego w system operacyjny.

## <a name="cim"></a>Cim

. CIM to nowe rozszerzenie pliku skojarzone z systemem plików obrazów złożonych (CimFS). Instalowanie i odinstalowanie plików CIM jest szybsze niż w przypadku plików VHD. Ponadto w przypadku pamięci i procesora JEST zużywana mniejsza ilość pamięci niż w przypadku wirtualnego dysku twardego.

Plik CIM to plik z . Rozszerzenie CIM zawierające metadane i co najmniej sześć dodatkowych plików zawierających rzeczywiste dane. Pliki w pliku CIM nie mają rozszerzeń. W poniższej tabeli znajduje się lista przykładowych plików, które można znaleźć w ujściu cim:

| Nazwa pliku | Wewnętrzny | Rozmiar |
|-----------|-----------|------|
| Vsc | Cim | 1 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | NA | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | NA | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | NA | 264 132 KB |

W poniższej tabeli przedstawiono porównanie wydajności dysków VHD i CimFS. Te liczby były wynikiem uruchomienia testowego z pięcioma sto plikami o rozmiarze 300 MB w każdym formacie uruchomionym na maszynie DSv4.

|  Specyfikacje                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Średni czas instalacji     | 356 ms                     | 255 ms      |
| Średni czas odinstalowania   | 1615 ms                    | 36 ms       |
| Użycie pamięci | 6% (z 8 GB)                      | 2% (z 8 GB)       |
| Procesor CPU (skok liczby)          | Wiele razy maksymalnego poziomu | Brak wpływu |

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej na temat dołączania aplikacji MSIX, zapoznaj się z naszym omówieniem [i](what-is-app-attach.md) często zadawanymi [pytaniami.](app-attach-faq.md) W przeciwnym razie rozpoczynanie pracy [z konfigurowaniem dołączania aplikacji.](app-attach.md)
