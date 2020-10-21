---
title: 'Szybki start: Eksportowanie danych na potrzeby usługi Microsoft Azure Data Box'
description: Dowiedz się, jak szybko wyeksportować dane usługi Azure Data Box w witrynie Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 07/17/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 789bd2f62673e7faf562d3a407bf2f0a4fd861bf
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125496"
---
# <a name="quickstart-get-started-with-azure-data-box-to-export-data-from-azure"></a>Szybki start: Rozpoczynanie pracy z usługą Azure Data Box w celu wyeksportowania danych z platformy Azure

W tym przewodniku Szybki start opisano sposób eksportowania danych z platformy Azure do lokalizacji przy użyciu witryny Azure Portal. Kroki obejmują sposób podłączania kabli, konfigurowania i kopiowania danych z platformy Azure. Czynności opisane w przewodniku Szybki start należy wykonać w witrynie Azure Portal oraz w lokalnym internetowym interfejsie użytkownika na urządzeniu.

Aby zyskać szczegółowe instrukcje dotyczące wdrażania i śledzenia krok po kroku, zobacz [Samouczek: Tworzenie zamówienia eksportu na potrzeby usługi Azure Data Box](data-box-deploy-export-ordered.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

* Sprawdź, czy subskrypcja, której używasz na potrzeby usługi Data Box, to subskrypcja jednego z następujących typów:
  * Microsoft Enterprise Agreement (EA). Dowiedz się więcej na temat [subskrypcji umowy EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
  * Cloud Solution Provider (CSP). Dowiedz się więcej o [programie Azure CSP](/azure/cloud-solution-provider/overview/azure-csp-overview).
  * Dostęp sponsorowany Microsoft Azure. Dowiedz się więcej o [programie dostępu sponsorowanego Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).

* Sprawdź, czy masz dostęp na poziomie właściciela lub współautora do subskrypcji, aby móc utworzyć zamówienie urządzenia Data Box.
* Zapoznaj się z [zasadami bezpieczeństwa dotyczącymi urządzenia Data Box](data-box-safety.md).
* Upewnij się, że masz urządzenie Azure Data Box umożliwiające przeniesienie danych do lokalizacji. Na komputerze hosta wymagane jest:
  * Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
  * Połączenie z siecią o dużej szybkości. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, można użyć połączenia danych 1 GbE, ale będzie miało to wpływ na szybkość kopiowania.
* Należy mieć dostęp do płaskiej powierzchni, na której można umieścić urządzenie Data Box. Jeśli chcesz umieścić urządzenie na standardowej półce na stojaku, na stojaku centrum danych potrzebne jest gniazdo 7U. Urządzenie możesz umieścić na stojaku płasko lub pionowo.
* Należy zapewnić następujące kable w celu podłączenia urządzenia Data Box do komputera hosta.
  * Dwa kable miedziane SFP+ Twinax 10 GbE (do użytku z interfejsami sieciowymi DATA 1 i DATA 2)
  * Jeden kabel sieciowy RJ-45 CAT 6 (do użytku z interfejsem sieciowym zarządzania — MGMT)
  * Jeden kabel sieciowy RJ-45 CAT 6A lub RJ-45 CAT 6 (do użytku z interfejsem sieciowym DATA 3 skonfigurowanym odpowiednio dla przepływności 10 GB/s lub 1 GB/s)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Zamówienie

Ten krok zajmuje około 10 minut.

1. Utwórz nowy zasób usługi Azure Data Box w witrynie Azure Portal.
2. Wybierz istniejącą subskrypcję, w której włączono tę usługę, a następnie wybierz typ transferu **Eksport do platformy Azure**. Podaj **Źródłowy region świadczenia usługi Azure**, w którym są przechowywane dane, oraz **Kraj docelowy**, do którego mają zostać przekazane dane.
3. Wybierz pozycję **Data Box**. Maksymalna dostępna do użycia pojemność tego rozwiązania to 80 TB. W przypadku większych ilości danych możesz utworzyć większą liczbę zamówień.
4. Wybierz pozycję **Dodaj konto magazynu i typ eksportu**, a następnie wybierz opcję **Eksportuj**.
5. Wprowadź szczegóły zamówienia i informacje dotyczące wysyłki. Jeśli ta usługa jest dostępna w Twoim regionie, podaj adresy e-mail na potrzeby powiadomień, zapoznaj się z podsumowaniem i utwórz zamówienie.

Po utworzeniu zamówienia urządzenie zostanie przygotowane do wysłania.

## <a name="cable"></a>Podłączenie

Ten krok zajmuje około 10 minut.

Po otrzymaniu urządzenia Data Box wykonaj następujące czynności w celu podłączenia przewodów, nawiązania połączenia i uruchomienia urządzenia. Ten krok zajmuje około 10 minut.

1. Jeśli urządzenie nosi jakiekolwiek oznaki uszkodzenia lub naruszenia, nie wykonuj dalszych czynności. Skontaktuj się z działem pomocy technicznej firmy Microsoft, aby otrzymać urządzenie zamienne.
2. Przed podłączeniem przewodów do urządzenia sprawdź, czy masz następujące przewody:

   * Przewód zasilający z uziemieniem (dołączony) przeznaczony do prądu co najmniej 10 A ze złączem typu IEC60320 C-13 po jednej stronie umożliwiającym podłączenie do urządzenia.
   * Jeden kabel sieciowy RJ-45 CAT 6 (do użytku z interfejsem sieciowym zarządzania — MGMT)
   * Dwa kable miedziane SFP+ Twinax 10 GbE (do użytku z interfejsami sieciowymi DATA 1 i DATA 2 o przepustowości 10 Gb/s)
   * Jeden kabel sieciowy RJ-45 CAT 6A lub RJ-45 CAT 6 (do użytku z interfejsem sieciowym DATA 3 skonfigurowanym odpowiednio dla przepływności 10 GB/s lub 1 GB/s)

3. Wyjmij urządzenie i umieść je na płaskiej powierzchni.

4. Podłącz przewody do urządzenia tak, jak pokazano poniżej.  

    ![Płyta montażowa urządzenia Data Box z podłączonymi kablami](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Podłącz kabel zasilający do urządzenia.
    2. Użyj kabla sieciowego RJ-45 CAT 6, aby podłączyć komputer hosta do portu MGMT na urządzeniu.
    3. Użyj kabla miedzianego SFP+ Twinax, aby podłączyć co najmniej jeden interfejs sieciowy 10 Gb/s (wersja preferowana, ale możliwe również użycie interfejsu 1 Gb/s), DATA 1 lub DATA 2, do przesyłu danych.
    4. Włącz urządzenie. Przycisk zasilania znajduje się na panelu przednim urządzenia.

## <a name="connect"></a>Połącz

Ten krok zajmuje około 5–7 minut.

1. Aby uzyskać hasło urządzenia, przejdź do pozycji **Ogólne** > **Szczegóły urządzenia** w witrynie [Azure Portal](https://portal.azure.com).
2. Przypisz statyczny adres IP 192.168.100.5 i podsieć 255.255.255.0 do karty Ethernet na komputerze używanym do nawiązania połączenia z urządzeniem Data Box. Uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem `https://192.168.100.10`. Nawiązywanie połączenia może potrwać do 5 minut po włączeniu urządzenia.
3. Zaloguj się przy użyciu hasła uzyskanego z witryny Azure Portal. Zostanie wyświetlony komunikat o błędzie informujący o problemie z certyfikatem zabezpieczeń witryny internetowej. Postępuj zgodnie z instrukcjami dotyczącymi używanej przeglądarki, aby przejść do odpowiedniej strony internetowej.
4. Domyślne ustawienie sieci dla interfejsu 10 Gb/s (lub 1 Gb/s) to DHCP. Jeśli jest taka potrzeba, można skonfigurować interfejs jako statyczny i podać adres IP.

## <a name="copy-data"></a>Kopiowanie danych

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych i prędkości połączenia sieciowego.

1. Jeśli używasz klienta z systemem Windows, skorzystaj z narzędzia do kopiowania plików zgodnego z protokołem SMB, na przykład Robocopy. W przypadku hosta z systemem plików NFS skopiuj dane za pomocą polecenia `cp` lub `rsync`. Aby uzyskać więcej informacji na temat kopiowania danych za pomocą narzędzia Robocopy, zobacz [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10)).
2. Nawiąż połączenie z udziałami urządzeń i rozpocznij kopiowanie danych do komputera hosta.
<!-- 1. Connect to the device shares using the path:`\\<IP address of your device>\ShareName`. To get the share access credentials, go to the **Connect & copy** page in the local web UI of the Data Box. --> 

## <a name="ship-to-azure"></a>Wysyłka do platformy Azure

Ta operacja trwa około 10–15 minut.

1. Przejdź do strony **Prepare to ship** (Przygotowanie do wysłania) w lokalnym internetowym interfejsie użytkownika i rozpocznij przygotowanie do wysłania.
2. Wyłącz urządzenie za pomocą lokalnego internetowego interfejsu użytkownika. Odłącz przewody od urządzenia.
3. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
4. Zwrotna etykieta wysyłkowa powinna być widoczna na wyświetlaczu E-ink. Jeśli na wyświetlaczu E-ink nie widać etykiety, przejdź do pozycji **Przegląd** > **Pobierz etykietę wysyłkową** w witrynie Azure Portal. Pobierz etykietę wysyłkową i dołącz ją do urządzenia.
5. Jeśli zwracasz urządzenie, zamów odbiór paczki przez regionalną firmę kurierską.
6. Po odebraniu urządzenia Data Box i zeskanowaniu go przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ten krok zajmuje około 2–3 minut.

* Możesz anulować zamówienie urządzenia Data Box w witrynie Azure Portal przed jego przetworzeniem. Po przetworzeniu zamówienia nie można go anulować. Realizacja zamówienia będzie kontynuowana aż do etapu ukończenia. Aby anulować zamówienie, w sekcji **Przegląd** wybierz pozycję **Anuluj** na pasku poleceń.

* Możesz usunąć zamówienie, gdy jego stan w witrynie Azure Portal to **Ukończono** lub **Anulowano**. Aby usunąć zamówienie, w sekcji **Przegląd** wybierz pozycję **Usuń** na pasku poleceń.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono zamówienie eksportu z platformy Azure do usługi Azure Data Box. Aby dowiedzieć się więcej na temat zarządzania usługą Azure Data Box, przejdź do kolejnego samouczka:

> [!div class="nextstepaction"]
> [Use the Azure portal to administer Data Box (Administrowanie usługą Data Box w witrynie Azure Portal)](data-box-portal-admin.md)