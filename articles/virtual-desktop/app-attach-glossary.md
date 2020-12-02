---
title: Słownik dołączania aplikacji MSIX Virtual Desktop systemu Windows — Azure
description: Słownik aplikacji MSIX dołączać warunki i pojęcia.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7132eae073f3d53a104536076ae801ec9ff93e5f
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518672"
---
# <a name="msix-app-attach-glossary"></a>Dołączanie słownika aplikacji MSIX

Ten artykuł zawiera listę definicji najważniejszych terminów i koncepcji związanych z dołączaniem do aplikacji MSIX.

## <a name="msix-container"></a>Kontener MSIX

Kontener MSIX to miejsce, w którym uruchamiane są aplikacje MSIX. Aby dowiedzieć się więcej, zobacz [kontenery MSIX](/windows/msix/msix-container).

## <a name="msix-application"></a>Aplikacja MSIX 

Aplikacja przechowywana w. Plik MSIX.

## <a name="msix-package"></a>Pakiet MSIX 

Pakiet MSIX to plik MSIX lub aplikacja.

## <a name="msix-share"></a>MSIX

Udział MSIX to udział sieciowy, który przechowuje rozwinięte pakiety MSIX. Udziały MSIX obsługują protokół SMB 3 lub nowszy. Aplikacje są przemieszczane z tego udziału MSIX bez konieczności przenoszenia plików aplikacji na dysk systemowy.

## <a name="msix-image"></a>Obraz MSIX

Obraz MSIX to plik VHD lub VHDx, który zawiera co najmniej jedną spakowaną aplikację MSIX. Każda aplikacja jest dostarczana w obrazie MSIX przy użyciu narzędzia MSIXMGR.

## <a name="repackage"></a>Ponownego

Ponowne pakowanie przybiera nieMSIXą aplikację i konwertuje ją na MSIX przy użyciu narzędzia do pakowania MSIX (MPT). Aby uzyskać więcej informacji, zobacz [Narzędzie MSIX pakowanie — Omówienie](/windows/msix/packaging-tool/tool-overview).

## <a name="expand-an-msix-package"></a>Rozwiń pakiet MSIX

Rozszerzanie pakietu MSIX jest procesem wieloetapowym. Rozszerzenie przyjmuje plik MSIX i umieszcza jego zawartość w pliku VHD (x) lub modelu CIM. 

Aby rozwinąć pakiet MSIX:

1. Pobierz pakiet MSIX (plik MSIX).
2. Zmień nazwę pliku MSIX na plik. zip.
3. Rozpakuj otrzymany plik zip w folderze.
4. Utwórz wirtualny dysk twardy o takim samym rozmiarze jak folder.
5. Zainstaluj dysk VHD.
6. Zainicjuj dysk.
7. Utwórz partycję.
8. Sformatuj partycję.
9. Skopiuj zawartość rozpakowany do dysku VHD.
10. Użyj narzędzia MSIXMGR, aby zastosować listy ACL zawartości dysku VHD.
11. Odinstaluj dysk VHD (x) lub [model wspólnych informacji](#cim).

## <a name="upload-an-msix-package"></a>Przekaż pakiet MSIX 

Przekazywanie pakietu MSIX obejmuje przekazanie dysku VHD (x) lub [modelu CIM](#cim) zawierającego rozwinięty pakiet MSIX do udziału MSIX.

W systemie Windows Virtual Desktop operacje przekazywania odbywają się raz na udział MSIX. Po przekazaniu pakietu wszystkie pule hostów w tej samej subskrypcji mogą odwoływać się do niego.

## <a name="add-an-msix-package"></a>Dodawanie pakietu MSIX

Na pulpicie wirtualnym systemu Windows Dodawanie pakietu MSIX łączy je z pulą hostów.

## <a name="publish-an-msix-package"></a>Publikowanie pakietu MSIX 

W przypadku pulpitu wirtualnego systemu Windows opublikowany pakiet MSIX musi zostać przypisany do usługi domena usługi Active Directory Service (AD DS) Azure Active Directory lub do grupy użytkowników (Azure AD).

## <a name="staging"></a>Przygotowanie

Proces przemieszczania obejmuje dwie rzeczy:

- Instalowanie dysku VHD (x) lub [modelu wspólnych informacji](#cim) na maszynie wirtualnej.
- Powiadamianie systemu operacyjnego o dostępności pakietu MSIX do rejestracji.

## <a name="registration"></a>Rejestracja

Rejestracja sprawia, że dla użytkowników jest dostępny pakiet przemieszczania MSIX. Rejestracja jest zależna od użytkownika. Jeśli nie zarejestrowano jawnie aplikacji dla danego użytkownika, nie będzie można uruchomić aplikacji.

Istnieją dwa typy rejestracji: regularne i opóźnione.

### <a name="regular-registration"></a>Regularna Rejestracja

W regularnych rejestracjach każda aplikacja przypisana do użytkownika jest w pełni zarejestrowana. Rejestracja odbywa się podczas logowania użytkownika do sesji, co może mieć wpływ na czas rozpoczęcia korzystania z pulpitu wirtualnego systemu Windows.

### <a name="delayed-registration"></a>Opóźniona Rejestracja

W przypadku opóźnionej rejestracji każda aplikacja przypisana do użytkownika jest tylko częściowo zarejestrowana. Rejestracja częściowa oznacza, że są rejestrowane kafelki menu Start i skojarzenia plików dwukrotnego kliknięcia. Rejestracja odbywa się, gdy użytkownik loguje się do swojej sesji, więc ma minimalny wpływ na czas potrzebny na rozpoczęcie korzystania z pulpitu wirtualnego systemu Windows. Rejestracja kończy się tylko wtedy, gdy użytkownik uruchamia aplikację w pakiecie MSIX.

Opóźniona Rejestracja jest obecnie konfiguracją domyślną dla dołączania aplikacji MSIX.

## <a name="deregistration"></a>Wyrejestrowywanie

Wyrejestrowanie usuwa zarejestrowany, ale Nieuruchomiony pakiet MSIX dla użytkownika. Wyrejestrowanie ma miejsce, gdy użytkownik wyloguje się z sesji. Podczas wyrejestrowywania aplikacja MSIX dołącza wypychanie danych aplikacji specyficznych dla użytkownika do profilu użytkownika lokalnego.

## <a name="destage"></a>Cofnij przygotowanie

Deprzygotowywanie powiadamia system operacyjny, że pakiet lub aplikacja MSIX, która aktualnie nie jest uruchomiona i nie jest przygotowana dla żadnego użytkownika, może zostać odinstalowany. Spowoduje to usunięcie wszystkich odwołań do tych informacji w systemie operacyjnym.

## <a name="cim"></a>WSPÓLNYCH

Model CIM to nowe rozszerzenie pliku skojarzone z systemem plików obrazów złożonych (CimFS). Instalowanie i Odinstalowywanie plików CIM jest szybsze, ponieważ pliki VHD. Model CIM zużywa również mniej czasu procesora i pamięci niż dysk VHD.

Plik CIM jest plikiem z rozszerzeniem. Rozszerzenie CIM zawierające metadane i co najmniej sześć dodatkowych plików zawierających rzeczywiste dane. Pliki w pliku CIM nie mają rozszerzeń. Poniższa tabela zawiera listę przykładowych plików znalezionych w modelu CIM:

| Nazwa pliku | Rozszerzenie | Rozmiar |
|-----------|-----------|------|
| VSC | WSPÓLNYCH | 1 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | Nie dotyczy | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | Nie dotyczy | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | Nie dotyczy | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | Nie dotyczy | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | Nie dotyczy | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | Nie dotyczy | 264 132 KB |

Poniższa tabela przedstawia porównanie wydajności między dyskami VHD i CimFS. Te liczby były wynikiem przebiegu testu z plikami 500 300 MB w każdym formacie uruchomionym na maszynie DSv4.

|  Specyfikacje                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Średni czas instalacji     | 356 MS                     | 255 MS      |
| Średni czas dezinstalacji   | 1615 MS                    | 36 MS       |
| Użycie pamięci | 6% (z 8 GB)                      | 2% (z 8 GB)       |
| Procesor CPU (liczba skoków)          | Maxed wiele razy | Brak wpływu |

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej o dołączaniu do aplikacji MSIX, zapoznaj się z naszym [omówieniem](what-is-app-attach.md) i [często zadawanymi pytaniami](app-attach-faq.md). W przeciwnym razie Zacznij od [przygotowania do dołączenia do aplikacji](app-attach.md).
