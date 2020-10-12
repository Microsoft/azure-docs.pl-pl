---
title: Konfigurowanie dystrybucji kontrolowanej przez deweloperów aplikacji (ADCD) w programie IBM zD&T v1 | Microsoft Docs
description: Uruchamiaj środowisko deweloperskie i testowe środowiska (zD&T) na platformie Azure Virtual Machines.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: b2509539551b3991690e6d0313e069ae015eb892
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87052410"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Konfigurowanie dystrybucji kontrolowanej przez deweloperów aplikacji (ADCD) w programie IBM zD&T v1

Środowisko programistyczne (zD&T) firmy IBM i środowisko testowe można uruchomić na platformie Azure Virtual Machines. To środowisko emuluje architekturę IBM Z serii. Może hostować różne systemy operacyjne z serii Z lub instalacje (nazywane również wystąpieniami lub pakietami), które są udostępniane za pomocą dostosowanych pakietów o nazwie ADCDs.

W tym artykule opisano sposób konfigurowania wystąpienia usługi ADCD w środowisku zD&T na platformie Azure. ADCDs Twórz kompletne implementacje systemu operacyjnego Z serii Z dla środowisk deweloperskich i testowych, które działają w zD&T.

Podobnie jak zD&T, ADCDs są dostępne tylko dla klientów i partnerów firmy IBM i są przeznaczone wyłącznie do celów deweloperskich i testowych. Nie są one używane w środowiskach produkcyjnych. Wiele pakietów instalacyjnych firmy IBM jest dostępnych do pobrania za poorednictwem [usługi Passport](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) lub [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Środowisko zD&T][ibm-install-z] zostało wcześniej skonfigurowane na platformie Azure. W tym artykule przyjęto założenie, że używasz tego samego utworzonego wcześniej obrazu maszyny wirtualnej w programie Ubuntu 16,04.

- Dostęp do multimediów ADCD za pomocą technologii IBM PartnerWorld lub usługi Passport.

- [Serwer licencjonowania](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Jest to wymagane do uruchomienia IBM zD&T. Sposób tworzenia go zależy od tego, jak Licencjobiorca ma licencję na oprogramowanie firmy IBM:

  - **Serwer licencjonowania oparty na sprzęcie** wymaga urządzenia sprzętowego USB, które zawiera wymierne tokeny niezbędne do uzyskania dostępu do wszystkich części oprogramowania. Musisz uzyskać ten dostęp z firmy IBM.

  - **Serwer licencjonowania oparty na oprogramowaniu** wymaga skonfigurowania scentralizowanego serwera do zarządzania kluczami licencjonowania. Ta metoda jest preferowana i wymaga skonfigurowania kluczy otrzymywanych od firmy IBM na serwerze zarządzania.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Pobierz pakiety instalacyjne z usługi Passport

Wymagany jest dostęp do nośnika ADCD. W poniższych krokach przyjęto założenie, że jesteś klientem firmy IBM i można korzystać z usługi Passport. Partnerzy IBM mogą korzystać z programu [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

> [!NOTE]
> W tym artykule przyjęto założenie, że komputer z systemem Windows jest używany w celu uzyskania dostępu do Azure Portal i pobrania nośnika IBM. Jeśli używasz komputera z systemem Mac lub Ubuntu, polecenia i proces uzyskiwania nośnika IBM mogą się nieco różnić.

1. Zaloguj się do [korzystania z usługi Passport](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Wybierz pozycję **Pobieranie oprogramowania** i **dostęp do nośnika**.

3. Wybierz pozycję **program i numer umowy**, a następnie kliknij przycisk **Kontynuuj**.

4. Wprowadź opis części lub numer części, a następnie kliknij pozycję **Wyszukiwacz**.

5. Opcjonalnie kliknij listę porządku alfabetycznego, aby wyświetlić i wyświetlić theproduct według nazwy.

6. Wybierz **wszystkie systemy operacyjne** w **polu system operacyjny**i **wszystkie języki** w **polu Języki**. Następnie kliknij pozycję **Przejdź**.

7. Kliknij pozycję **Wybierz pojedyncze pliki** , aby rozwinąć listę i wyświetlić poszczególne nośniki do pobrania.

8. Sprawdź pakiety, które chcesz pobrać, wybierz pozycję **Pobierz**, a następnie Pobierz pliki do żądanego katalogu.

## <a name="upload-the-adcd-packages"></a>Przekaż pakiety ADCD

Teraz, gdy masz pakiety, musisz przekazać je na maszynę wirtualną na platformie Azure.

1. W Azure Portal zainicjować sesję **SSH** z utworzoną maszyną wirtualną Ubuntu. Przejdź do maszyny wirtualnej, wybierz blok **Przegląd** , a następnie wybierz pozycję **Połącz**.

2. Wybierz kartę **SSH** , a następnie skopiuj polecenie SSH do Schowka.

3. Zaloguj się do maszyny wirtualnej przy użyciu swoich poświadczeń i wybranego [klienta SSH](../../../linux/use-remote-desktop.md) . W tej wersji demonstracyjnej są stosowane rozszerzenia Linux dla systemu Windows 10, które dodaje powłokę bash do wiersza polecenia systemu Windows. Tu działa również.

4. Po zalogowaniu Utwórz katalog, aby przekazać pakiety firmy IBM. Należy pamiętać, że system Linux uwzględnia wielkość liter. Na przykład w tym pokazie założono, że pakiety są przekazywane do:

    `/home/MyUserID/ZDT/adcd/nov2017/volumes`

5. Przekaż pliki przy użyciu klienta SSH, takiego jak[WinSCP](https://winscp.net/eng/index.php). Ponieważ punkt połączenia usługi jest częścią protokołu SSH, używa portu 22, który jest używany przez protokół SSH. Jeśli komputer lokalny nie jest systemem Windows, możesz wpisać [polecenie SCP](http://man7.org/linux/man-pages/man1/scp.1.html) w sesji SSH.

6. Zainicjuj przekazywanie do utworzonego katalogu maszyn wirtualnych platformy Azure, który stanowi magazyn obrazów dla zD&T.

    > [!NOTE]
    > Upewnij się, że **ADCDTOOLS.XML** znajduje się w przekazaniu do katalogu macierzystego/mój **UserID/ZDT/adcd/nov2017** . Będzie potrzebny później.

7. Poczekaj na przekazanie plików, co może zająć trochę czasu w zależności od połączenia z platformą Azure.

8. Po zakończeniu przekazywania przejdź do katalogu woluminów i zdekompresuj wszystkie woluminy **GZ** :

    ```console
    gunzip \*.gz
    ```
    
![Eksplorator plików z odkompresowanymi woluminami GZ](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Konfigurowanie magazynu obrazów

Następnym krokiem jest skonfigurowanie zD&T w celu używania przekazanych pakietów. Proces magazynu obrazów w programie zD&T pozwala na instalowanie obrazów i korzystanie z nich. Może korzystać z protokołu SSH lub FTP.

1. Uruchom **zDTServer**. Aby to zrobić, musisz być na poziomie głównym. Wprowadź dwa następujące polecenia w kolejności:
    ```console
    sudo su -
    /opt/ibm/zDT/bin/startServer
    ```
2. Zwróć uwagę na adres URL danych wyjściowych przez polecenie i Użyj tego adresu URL w celu uzyskania dostępu do serwera sieci Web. Wygląda podobnie do:
     > https://(nazwa lub adres IP maszyny wirtualnej): 9443/ZDTMC/index.html
     >
     > Pamiętaj, że dostęp do sieci Web jest używany przez port 9443. Użyj tego, aby zalogować się do serwera sieci Web. Identyfikator użytkownika dla ZD&T jest **zdtadmin** , a hasło to **hasło**.

    ![Ekran powitalny IBM zD&T Enterprise Edition](media/02-welcome.png)

3. Na stronie **Szybki Start** w obszarze **Konfiguracja**wybierz pozycję **Magazyn obrazów**.

     ![Ekran IBM zD&T Enterprise Edition Szybki start](media/03-quickstart.png)

4. Na stronie **Konfigurowanie magazynu obrazu** wybierz pozycję **SSH protokół transferu plików**.

5. W polu **Nazwa hosta**wpisz **localhost** i wprowadź ścieżkę do katalogu, w którym zostały przekazane obrazy. Na przykład/home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Wprowadź **Identyfikator użytkownika** i **hasło** dla maszyny wirtualnej. Nie należy używać identyfikatora użytkownika ZD&T i hasła.

7. Przetestuj połączenie, aby upewnić się, że masz dostęp, a następnie wybierz pozycję **Zapisz** , aby zapisać konfigurację.

## <a name="configure-the-target-environments"></a>Konfigurowanie środowisk docelowych

Następnym krokiem jest skonfigurowanie środowiska docelowego zD&T. To emulowane środowisko hostowane polega na tym, że obrazy są uruchamiane.

1. Na stronie **Szybki Start** w obszarze **Konfiguracja**wybierz pozycję **środowiska docelowe**.

2. Na stronie **Konfiguruj środowiska docelowe** wybierz pozycję **Dodaj cel**.

3. Wybierz pozycję **Linux**. IBM obsługuje dwa typy środowisk, Linux i Cloud (OpenStack), ale ta wersja demonstracyjna jest uruchamiana w systemie Linux.

4. Na stronie **Dodawanie środowiska docelowego** dla **nazwy hosta**wprowadź wartość **localhost**. Pozostaw **port SSH** ustawiony na **22**.

5. W polu **etykieta środowiska docelowego** Wprowadź etykietę, taką jak **MyCICS.**

     ![Dodaj ekran środowiska docelowego](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Skonfiguruj ADCD i Wdróż

Po wykonaniu poprzednich kroków konfiguracyjnych należy skonfigurować zD&T, aby używać pakietów i środowiska docelowego. Ponownie Użyj procesu magazynowania obrazów w programie zD&T, który umożliwia instalowanie i używanie obrazów. Może korzystać z protokołu SSH lub FTP.

1. Na stronie **Szybki Start** w obszarze **Konfigurowanie**wybierz pozycję **ADCD**. Zostanie wyświetlony zestaw instrukcji informujących o czynnościach, które należy wykonać przed zainstalowaniem pakietu ADCD. Wyjaśnia to, dlaczego nazywamy katalog docelowy tak jak wcześniej.

2. Przy założeniu, że wszystkie obrazy zostały przekazane do prawidłowych katalogów, kliknij link **ADCD** w prawym dolnym rogu (widocznym w kroku 7 na poniższym zrzucie ekranu).

     ![IBM zD&T Enterprise Edition — Konfigurowanie ekranu ADCD](media/05-adcd.png)

## <a name="create-the-image"></a>Tworzenie obrazu

Po ukończeniu poprzedniego kroku konfiguracji zostanie wyświetlona strona **Tworzenie obrazu przy użyciu składników ADCD** .

1. Wybierz wolumin (lis 2017 w tym przypadku), aby wyświetlić różne pakiety, które znajdują się w tym woluminie.

2. Na potrzeby tego pokazu wybierz pozycję **system kontroli informacji klienta (CICS) — 5,3**.

3. W polu **Nazwa obrazu** wpisz nazwę obrazu, na przykład **obraz MyCICS**.

4. Wybierz przycisk **Utwórz obraz** w prawym dolnym rogu.

     ![IBM zD&T Enterprise Edition — Tworzenie obrazu przy użyciu ekranu składników ADCD](media/06-adcd.png)

5. W wyświetlonym oknie, informując o pomyślnym wdrożeniu obrazu, wybierz pozycję **Wdróż obrazy**.

6. Na stronie **wdrażanie obrazu na środowisko docelowe** wybierz obraz utworzony na poprzedniej stronie (**obraz MyCICS**) i utworzone wcześniej środowisko docelowe (**MyCICS**).

7. Na następnym ekranie podaj poświadczenia dla maszyny wirtualnej (czyli nie poświadczenia ztadmin).

8. W okienku właściwości wprowadź liczbę **procesorów centralnych (CPs)**, ilość **pamięci systemowej (GB)** i **katalog wdrożenia** dla uruchomionego obrazu. Ponieważ jest to wersja demonstracyjna, Zachowaj ją małymi literami.

9. Upewnij się, że pole jest wybrane do **automatycznego wydawania polecenia IPL do systemu operacyjnego i po wdrożeniu**.

     ![Ekran właściwości](media/07-properties.png)

10. Wybierz pozycję **ukończone**.

11. Wybierz pozycję **Wdróż obraz** na stronie **wdrażanie obrazu na środowisko docelowe** .

Obraz można teraz wdrożyć i będzie gotowy do zainstalowania przez emulator terminalu 3270.

> [!NOTE]
> Jeśli zostanie wyświetlony komunikat o błędzie informujący o braku wystarczającej ilości miejsca na dysku, należy pamiętać, że region wymaga 151 GB.

Gratulacje! Teraz działa środowisko mainframe firmy IBM na platformie Azure.

## <a name="learn-more"></a>Dowiedz się więcej

- [Migracja komputera mainframe: mitów i fakty](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale na platformie Azure](../../../linux/ibm-db2-purescale-azure.md)
- [Rozwiązywanie problemów](../../../troubleshooting/index.yml)
- [Sztuczna komputera mainframe do migracji na platformę Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
