---
title: Skonfiguruj laboratorium do uczenia programu MATLAB z Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium do uczenia programu MATLAB z Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85445002"
---
# <a name="setup-a-lab-to-teach-matlab"></a>Konfigurowanie laboratorium do uczenia programu MATLAB

Program [MATLAB](https://www.mathworks.com/products/matlab.html), który jest przeznaczony dla laboratorium Matrix, jest platformą programistyczną [MathWorks](https://www.mathworks.com/).  Łączy moc obliczeniową i wizualizację, co sprawia, że jest to popularne narzędzie w dziedzinach matematycznych, inżynieryjnych, fizyki i chemii.

Jeśli używasz [licencji na szeroką skalę](https://www.mathworks.com/academia/tah-support-program/administrators.html), zapoznaj się z instrukcjami w artykule [pobieranie plików instalacyjnych programu MATLAB](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine) , aby pobrać pliki Instalatora programu MATLAB na komputerze szablonu.  

W tym artykule przedstawiono sposób konfigurowania klasy, która używa oprogramowania klienckiego programu MATLAB z serwerem licencji.

## <a name="license-server"></a>Serwer licencji

Przed zmodyfikowaniem maszyny szablonu dla laboratorium należy skonfigurować serwer do uruchamiania oprogramowania [Menedżera licencji sieci](https://www.mathworks.com/help/install/administer-network-licenses.html) .  Te instrukcje dotyczą tylko instytucji, które wybierają opcję licencjonowania sieci dla programu MATLAB, która umożliwia użytkownikom udostępnianie puli kluczy licencji.  Należy również zapisać plik licencji i klucz instalacji pliku w przyszłości.  Aby uzyskać szczegółowe instrukcje dotyczące pobierania pliku licencji, zobacz pierwszy krok w artykule [Instalowanie Menedżera licencji sieciowych z połączeniem internetowym](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html) .

Szczegółowe instrukcje dotyczące sposobu instalowania serwera licencji są dostępne w obszarze [Instalowanie Menedżera licencji sieciowych z połączeniem internetowym](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html).  Aby włączyć pożyczanie, zapoznaj się z artykułem pozostałej [licencji](https://www.mathworks.com/help/install/license/borrow-licenses.html) .

Przy założeniu, że serwer licencji znajduje się w sieci lokalnej lub prywatnej sieci na platformie Azure, nie zapomnij, aby [Sieć wirtualna była równorzędna](how-to-connect-peer-virtual-network.md) do [konta laboratorium](tutorial-setup-lab-account.md).  Komunikacja równorzędna sieci musi zostać wykonana przed utworzeniem laboratorium, aby maszyny wirtualne laboratorium mogły uzyskać dostęp do serwera licencji.

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby skonfigurować to laboratorium, musisz mieć subskrypcję platformy Azure, aby rozpocząć pracę.  Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). Po otrzymaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services lub użyć istniejącego konta.  Aby utworzyć nowe konto laboratorium, zapoznaj się z [samouczkiem Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md).

Aby utworzyć nowe laboratorium, postępuj zgodnie z [samouczkiem dotyczącym laboratorium zajęć](tutorial-setup-classroom-lab.md).  Zastosuj następujące ustawienia:

| Rozmiar maszyny wirtualnej | Image (Obraz) |
| -------------------- | ----- |
| Śred. | Windows 10 |

System MATLAB jest obsługiwany w większej liczbie systemów operacyjnych.  Aby uzyskać szczegółowe informacje, zobacz sekcję [wymagania systemowe programu MATLAB](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) .

> [!WARNING]
> Przed utworzeniem laboratorium nie zapomnij [sieci wirtualnej](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) dla konta laboratorium w sieci wirtualnej dla serwera licencji.

## <a name="template-machine"></a>Komputer szablonu

Po utworzeniu komputera szablonu Uruchom maszynę i Połącz się z nią, aby wykonać następujące zadania główne.

1. Pobierz pliki instalacyjne oprogramowania klienckiego programu MATLAB.
2. Zainstaluj program MATLAB przy użyciu klucza instalacyjnego pliku.

Instalowanie programu MATLAB będzie procesem wieloetapowym.  Pierwsza część pobierze pliki dla programu MATLAB i innych produktów, które mają być zainstalowane.  Przy użyciu klucza instalacyjnego plików wymagane jest pobranie wszystkich plików instalacyjnych produktów do zainstalowania.  Druga część spowoduje zainstalowanie oprogramowania MATLAB na maszynie wirtualnej szablonu i aktywowanie oprogramowania.  Jeśli szablon maszyny wirtualnej jest skonfigurowany do aktywacji przy użyciu serwera licencji, maszyny wirtualne uczniów będą takie same.

### <a name="download-installation-files"></a>Pobierz pliki instalacyjne

Musisz być administratorem licencji, aby pobrać pliki instalacyjne, a także pobrać plik licencji i klucz instalacji pliku.  Kroki pobierania plików instalacyjnych znajdują się poniżej.

1. Zaloguj się do swojego konta w usłudze [https://www.mathworks.com](https://www.mathworks.com) .
2. Wybierz pozycję **Moje konto**.
3. W sekcji **moje oprogramowanie** na stronie konto kliknij licencję dołączoną do Instalatora Menedżera licencji sieci dla laboratorium.
4. Na stronie szczegóły licencji kliknij pozycję **Pobierz produkty**.
5. Poczekaj na samodzielne wyodrębnienie Instalatora.
6. Uruchom instalatora.  
7. Na stronie **Zaloguj się do konta MathWorks** wprowadź swoje konto MathWorks.
8. Na stronie **Umowa licencyjna MathWorks** Zaakceptuj termin i kliknij przycisk **dalej** .
9. Kliknij listę rozwijaną **Zaawansowane opcje** i wybierz pozycję **Chcę pobrać bez instalacji**.
10. W obszarze **Wybieranie folderu docelowego** kliknij przycisk **dalej**.
11. Wybierz pozycję **Windows** jako platformę komputera, który ma zostać zainstalowany w programie Matlab.
12. Na stronie **Wybierz produkt** upewnij się, że jest ZAZNACZONA opcja MATLAB oraz inne MathWorks produkty, które chcesz zainstalować.
13. Na stronie **Potwierdzanie opcji i pobieranie** kliknij pozycję **Rozpocznij pobieranie**.  
14. Poczekaj na pobranie wybranych produktów.  Kliknij przycisk **Finish** (Zakończ).

Obraz ISO można także pobrać z witryny sieci Web MathWorks.

1. Zaloguj się do swojego konta w usłudze [https://www.mathworks.com](https://www.mathworks.com) .
2. Przejdź do witryny [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads).
3. Wybierz wersję programu MATLAB, którą chcesz zainstalować.
4. Kliknij link "Pobierz {Version}. ISO" poniżej linków pokrewnych, gdzie {Version} jest czymś podobnym do R2020a.
5. Kliknij link do **pobierania wersji** Blue dla systemu Windows.

### <a name="run-installer"></a>Uruchom Instalatora

Po pobraniu plików drugi krok to uruchomienie Instalatora. Aby ukończyć ten krok, musisz być administratorem licencji.  Tylko Administratorzy licencji mogą instalować program MATLAB z kluczem instalacji pliku.

1. Sprawdź pobrany plik licencji i sprawdź, czy wiersz serwera prawidłowo zawiera serwer licencji.  Informacje dotyczące sposobu formatowania pliku licencji można znaleźć w artykule [Aktualizowanie licencji sieciowej](https://www.mathworks.com/help/install/ug/network-license-files.html), [zaciąganie licencji](https://www.mathworks.com/help/install/license/borrow-licenses.html)i [Znajdowanie identyfikatorów hosta](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license) .
2. Uruchom Instalatora programu MATLAB.
3. Na stronie **Zaloguj się do konta MathWorks** wprowadź swoje konto MathWorks.
4. Na stronie **Umowa licencyjna MathWorks** Zaakceptuj termin i kliknij przycisk **dalej** .
5. Kliknij listę rozwijaną **Zaawansowane opcje** i wybierz **mam klucz instalacji pliku**.
6. Na stronie **Zainstaluj klucz instalacji pliku** wprowadź klucz instalacji pliku dla serwera licencji.   Kliknij przycisk **Dalej**.
7. Na stronie **Wybierz plik licencji** przejdź do pliku licencji zapisanego przed wcześniejszym pobraniem plików instalacyjnych.
8. Na stronie **Wybieranie folderu docelowego** kliknij przycisk **dalej**.
9. Na stronie **Wybierz produkty** kliknij przycisk **dalej**.
10. Na stronie **Wybieranie opcji** kliknij przycisk **dalej**.
11. Na stronie **Potwierdzanie wyboru i instalacji** kliknij przycisk **Rozpocznij instalację**.
12. Na stronie **ukończenie instalacji** Sprawdź, czy jest zaznaczona opcja **Aktywuj program MATLAB** .  Kliknij przycisk **Finish** (Zakończ).

## <a name="cost-estimate"></a>Oszacowanie kosztów

Przyjrzyjmy się możliwemu szacunkowi kosztów dla tej klasy.  To oszacowanie nie obejmuje kosztu uruchomienia serwera licencji.  Będziemy używać klasy 25 studentów.  Zaplanowana godzina klasy wynosi 20 godzin.  Ponadto każdy student otrzymuje limit 10 godzin dla prac domowych lub przydziałów poza zaplanowanym czasem klasy.  Wybrany rozmiar maszyny wirtualnej to średnia, czyli 55 jednostek laboratorium.

Oto przykład możliwego oszacowania kosztów dla tej klasy:

25 studentów \* (20 zaplanowanych godzin + 10 godzin przydziału) \* 55 jednostek laboratoryjnych \*  0,01 USD za godzinę = 412,50 USD

>[!IMPORTANT]
> Oszacowanie kosztów odbywa się tylko na przykład.  Aby uzyskać aktualne informacje o cenach, zobacz [Cennik usługi Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne do konfigurowania dowolnego laboratorium.

- [Tworzenie i publikowanie szablonu oraz zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users)
