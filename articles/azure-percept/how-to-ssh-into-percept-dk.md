---
title: Nawiązywanie połączenia z platformą Azure Percept DK za pośrednictwem protokołu SSH
description: Dowiedz się, jak za pomocą protokołu SSH do usługi Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39ee1c1cc5b52dc62e3199536234c1f7d9381436
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721481"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Nawiązywanie połączenia z platformą Azure Percept DK za pośrednictwem protokołu SSH

Postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować połączenie SSH z platformą Azure Percept DK za pośrednictwem [OpenSSH lub instrukcji](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Wymagania wstępne

- Komputer-host z systemem Windows, Linux lub OS X z możliwością Wi-Fi
- Klient SSH (zobacz następną sekcję, aby uzyskać wskazówki dotyczące instalacji)
- Azure Percept DK (dev Kit)
- Logowanie SSH utworzone podczas [instalacji usługi Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="install-your-preferred-ssh-client"></a>Instalowanie preferowanego klienta SSH

Jeśli na komputerze hosta jest uruchomiony system Linux lub OS X, usługi SSH są zawarte w tych systemach operacyjnych i można je uruchomić bez oddzielnej aplikacji klienckiej. Więcej informacji na temat sposobu uruchamiania usług SSH można znaleźć w dokumentacji produktu systemu operacyjnego.

Jeśli na komputerze hosta jest uruchomiony system Windows, można wybrać jedną z następujących opcji klienta SSH: OpenSSH i.

### <a name="openssh"></a>OpenSSH

System Windows 10 zawiera wbudowanego klienta SSH o nazwie OpenSSH, który można uruchomić za pomocą prostego polecenia w wierszu polecenia. Zalecamy używanie OpenSSH z usługą Azure Percept, jeśli jest ona dostępna. Aby sprawdzić, czy na komputerze z systemem Windows jest zainstalowany program OpenSSH, wykonaj następujące kroki:

1. Przejdź do pozycji **Rozpocznij**  ->  **Ustawienia**.

1. Wybierz pozycję **Aplikacje**.

1. W obszarze **aplikacje & funkcje** wybierz pozycję **funkcje opcjonalne**.

1. Wpisz **OpenSSH Client** na pasku wyszukiwania **zainstalowanych funkcji** . Jeśli OpenSSH pojawia się, klient jest już zainstalowany i można przejść do następnej sekcji. Jeśli nie widzisz OpenSSH, kliknij pozycję **Dodaj funkcję**.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="Zrzut ekranu przedstawiający ustawienia pokazujące stan instalacji OpenSSH.":::

1. Wybierz pozycję **OpenSSH Client** , a następnie kliknij przycisk **Instaluj**. Możesz teraz przejść do następnej sekcji. Jeśli OpenSSH nie jest dostępna do zainstalowania na komputerze, wykonaj poniższe kroki, aby zainstalować program klienta SSH innej firmy.

### <a name="putty"></a>PuTTY

Jeśli komputer z systemem Windows nie zawiera OpenSSH, [zalecamy użycie polecenia](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). Aby pobrać i zainstalować instrukcje, wykonaj następujące czynności:

1. Przejdź do [strony pobieranie pobrane](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. W obszarze **pliki pakietu** kliknij plik 32-bitowy lub 64-bitowy. msi, aby pobrać Instalatora. Jeśli nie masz pewności, którą wersję wybrać, zapoznaj się z [często zadawanymi pytaniami](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit).

1. Kliknij Instalatora, aby rozpocząć proces instalacji. Postępuj zgodnie z instrukcjami, zgodnie z potrzebami.

1. Gratulacje! Pomyślnie zainstalowano klienta w programie SSH.

## <a name="initiate-the-ssh-connection"></a>Inicjowanie połączenia SSH

1. Włącz usługę Azure Percept DK.

1. Jeśli Twój zestaw deweloperski jest już połączony z siecią za pośrednictwem sieci Ethernet lub Wi-Fi, przejdź do następnego kroku. W przeciwnym razie Podłącz komputer hosta bezpośrednio do punktu dostępu Wi-Fi zestawu deweloperskiego. Podobnie jak w przypadku łączenia się z innymi sieciami Wi-Fi, Otwórz ustawienia sieci i Internetu na komputerze, kliknij następującą sieć i wprowadź hasło sieciowe po wyświetleniu monitu:

    - **Nazwa sieci**: w zależności od wersji systemu operacyjnego zestawu deweloperskiego nazwa Wi-Fi punktu dostępu to **SCZ-xxxx** lub **APD-xxxx** (gdzie "XXXX" to ostatnie cztery cyfry adresu MAC zestawu deweloperów).
    - **Hasło**: można znaleźć na karcie powitalnej, która została dostarczona z zestawem deweloperskim

    > [!WARNING]
    > Po nawiązaniu połączenia z punktem dostępu do usługi Azure Percept Wi-Fi, komputer-host tymczasowo utraci połączenie z Internetem. Aktywne wywołania konferencji wideo, przesyłania strumieniowego sieci Web lub inne środowiska sieciowe będą przerywane.

1. Ukończ proces połączenia SSH zgodnie z klientem SSH.

### <a name="using-openssh"></a>Korzystanie z OpenSSH

1. Otwórz wiersz polecenia (**Uruchom**  ->  **wiersz polecenia**).

1. Wprowadź następujące polecenie w wierszu polecenia:

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    Jeśli komputer jest połączony z punktem dostępu Wi-Fi zestawu deweloperskiego, adres IP zostanie 10.1.1.1. Jeśli Twój zestaw deweloperski jest połączony za pośrednictwem sieci Ethernet, Użyj lokalnego adresu IP urządzenia, które można pobrać z routera lub koncentratora sieci Ethernet. Jeśli Twój zestaw deweloperski jest połączony za pośrednictwem sieci Wi-Fi, musisz użyć adresu IP, który został przypisany do zestawu deweloperskiego podczas [instalacji usługi Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Jeśli Twój zestaw deweloperski jest połączony z siecią Wi-Fi, ale nie znasz swojego adresu IP, przejdź do usługi Azure Percept Studio i [Otwórz strumień wideo na urządzeniu](./how-to-view-video-stream.md). Na pasku adresu na karcie Przeglądarka strumienia wideo zostanie wyświetlony adres IP urządzenia.

1. Po wyświetleniu monitu wprowadź hasło SSH.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="Zrzut ekranu przedstawiający Logowanie przy otwartym wierszu polecenia SSH.":::

1. Jeśli po raz pierwszy łączysz się z zestawem deweloperskim za pomocą OpenSSH, może być również wyświetlany monit o zaakceptowanie klucza hosta. Wprowadź **wartość tak** , aby zaakceptować klucz.

1. Gratulacje! Pomyślnie nawiązano połączenie z zestawem deweloperskim za pośrednictwem protokołu SSH.

### <a name="using-putty"></a>Korzystanie z

1. Otwórz program PuTTY. Wprowadź następujące polecenie w oknie **konfiguracji** , a następnie kliknij pozycję **Otwórz** , aby SSH w twoim zestawie deweloperskim:

    1. Nazwa hosta: [adres IP]
    1. Port: 22
    1. Typ połączenia: SSH

    **Nazwa hosta** jest adresem IP Twojego zestawu deweloperskiego. Jeśli komputer jest połączony z punktem dostępu Wi-Fi zestawu deweloperskiego, adres IP zostanie 10.1.1.1. Jeśli Twój zestaw deweloperski jest połączony za pośrednictwem sieci Ethernet, Użyj lokalnego adresu IP urządzenia, które można pobrać z routera lub koncentratora sieci Ethernet. Jeśli Twój zestaw deweloperski jest połączony za pośrednictwem sieci Wi-Fi, musisz użyć adresu IP, który został przypisany do zestawu deweloperskiego podczas [instalacji usługi Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Jeśli Twój zestaw deweloperski jest połączony z siecią Wi-Fi, ale nie znasz swojego adresu IP, przejdź do usługi Azure Percept Studio i [Otwórz strumień wideo na urządzeniu](./how-to-view-video-stream.md). Na pasku adresu na karcie Przeglądarka strumienia wideo zostanie wyświetlony adres IP urządzenia.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Zrzut ekranu przedstawiający okno konfiguracji.":::

1. Zostanie otwarty terminal. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło SSH do terminalu.

1. Gratulacje! Pomyślnie nawiązano połączenie z zestawem deweloperskim za pośrednictwem protokołu SSH.

## <a name="next-steps"></a>Następne kroki

Po nawiązaniu połączenia z usługą Azure Percept DK za pośrednictwem protokołu SSH można wykonywać różne zadania, w tym [Rozwiązywanie problemów z urządzeniami](./troubleshoot-dev-kit.md) i [aktualizacjami USB](./how-to-update-via-usb.md).