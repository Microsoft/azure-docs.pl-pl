---
title: 'Szybki start: usługa Microsoft Azure Data Box | Microsoft Docs'
description: W tym przewodniku Szybki start dowiesz się, jak wdrożyć usługę Azure Data Box przy użyciu witryny Azure Portal na potrzeby zamówienia importu. Skonfiguruj usługę Azure Data Box i skopiuj dane w celu przekazania ich na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 03/05/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 3359152455df2701d897689a1f177bf63051b94b
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440721"
---
# <a name="get-started-with-azure-data-box-to-import-data-into-azure"></a>Rozpocznij pracę z usługą Azure Data Box, aby importować dane na platformę Azure

::: zone target="docs"

W tym przewodniku Szybki start opisano sposób wdrażania usługi Azure Data Box przy użyciu witryny Azure Portal na potrzeby zamówienia importu. Przedstawiono między innymi czynności podłączania i konfigurowania urządzenia Data Box oraz kopiowania na nie danych do przekazania na platformę Azure. Czynności opisane w przewodniku Szybki start należy wykonać w witrynie Azure Portal oraz w lokalnym internetowym interfejsie użytkownika na urządzeniu.

Aby zyskać szczegółowe instrukcje dotyczące wdrażania i śledzenia krok po kroku, zobacz [Samouczek: zamawianie urządzenia Azure Data Box](data-box-deploy-ordered.md)

::: zone-end 

::: zone target="chromeless"

W tym przewodniku opisano sposób wdrażania usługi Azure Data Box na potrzeby importowania przy użyciu witryny Azure Portal. Opisane czynności obejmują przeglądanie wymagań wstępnych, podłączanie kabli do urządzenia i nawiązywanie z nim połączenia oraz kopiowanie danych do urządzenia w celu przekazania ich do platformy Azure.

::: zone-end

::: zone target="docs"
 
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

- Sprawdź, czy subskrypcja, której używasz na potrzeby usługi Data Box, to subskrypcja jednego z następujących typów:
    - Umowa klienta firmy Microsoft (MCA) dla nowych subskrypcji lub Microsoft Enterprise Agreement (EA) dla istniejących subskrypcji. Przeczytaj więcej [na temat MCA dla nowych subskrypcji](https://www.microsoft.com/licensing/how-to-buy/microsoft-customer-agreement) i [subskrypcji EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Dowiedz się więcej o [programie Azure CSP](/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Dostęp sponsorowany Microsoft Azure. Dowiedz się więcej o [programie dostępu sponsorowanego Azure](https://azure.microsoft.com/offers/ms-azr-0036p/). 

- Sprawdź, czy masz dostęp na poziomie właściciela lub współautora do subskrypcji, aby móc utworzyć zamówienie urządzenia Data Box.
- Zapoznaj się z [zasadami bezpieczeństwa dotyczącymi urządzenia Data Box](data-box-safety.md).
- Masz komputer-host zawierający dane, które mają zostać skopiowane na urządzenie Data Box. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, można użyć połączenia danych 1 GbE, ale będzie miało to wpływ na szybkość kopiowania. 
- Należy mieć dostęp do płaskiej powierzchni, na której można umieścić urządzenie Data Box. Jeśli chcesz umieścić urządzenie na standardowej półce na stojaku, na stojaku centrum danych potrzebne jest gniazdo 7U. Urządzenie możesz umieścić na stojaku płasko lub pionowo.
- Należy zapewnić następujące kable w celu podłączenia urządzenia Data Box do komputera hosta.
    - Dwa kable miedziane SFP+ Twinax 10 GbE (do użytku z interfejsami sieciowymi DATA 1 i DATA 2)
    - Jeden kabel sieciowy RJ-45 CAT 6 (do użytku z interfejsem sieciowym MGMT)
    - Jeden kabel sieciowy RJ-45 CAT 6A lub RJ-45 CAT 6 (do użytku z interfejsem sieciowym DATA 3 skonfigurowanym odpowiednio dla przepływności 10 Gb/s lub 1 Gb/s)

::: zone-end 

::: zone target="chromeless"

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: zamawianie urządzenia Azure Data Box](data-box-deploy-ordered.md).
2. Urządzenie Data Box zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**. 
3. Zapoznano się z [wytycznymi bezpieczeństwa dotyczącymi urządzenia Data Box](data-box-safety.md).
4. Odebrano jeden kabel zasilający z uziemieniem do korzystania z urządzenia magazynującego o pojemności 100 TB.
5. Masz dostęp do komputera-hosta zawierającego dane, które mają zostać skopiowane do urządzenia Data Box. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, można użyć połączenia danych 1 GbE, ale będzie miało to wpływ na szybkość kopiowania. 
6. Dostęp do płaskiej powierzchni w celu umieszczenia urządzenia Data Box. Jeśli chcesz położyć lub postawić urządzenie na standardowej półce na stojaku, na stojaku potrzebne jest gniazdo 7U.

::: zone-end

::: zone target="docs"

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Zamówienie

Ten krok zajmuje około 5 minut.

1. Utwórz nowy zasób usługi Azure Data Box w witrynie Azure Portal.
2. Wybierz istniejącą subskrypcję, w której włączono tę usługę, a następnie wybierz typ transferu **Import**. Podaj **Kraj źródłowy**, w którym są przechowywane dane, oraz **Docelowy region platformy Azure**, do którego mają zostać przekazane dane.
3. Wybierz pozycję **Data Box**. Maksymalna dostępna do użycia pojemność tego rozwiązania to 80 TB. W przypadku większych ilości danych możesz utworzyć większą liczbę zamówień.
4. Wprowadź szczegóły zamówienia i informacje dotyczące wysyłki. Jeśli ta usługa jest dostępna w Twoim regionie, podaj adresy e-mail na potrzeby powiadomień, zapoznaj się z podsumowaniem i utwórz zamówienie.

Po utworzeniu zamówienia urządzenie zostanie przygotowane do wysłania.



## <a name="cable"></a>Podłączenie 

Ten krok zajmuje około 10 minut.

Po otrzymaniu urządzenia Data Box wykonaj następujące czynności w celu podłączenia przewodów, nawiązania połączenia i uruchomienia urządzenia. Ten krok zajmuje około 10 minut.

1. Jeśli urządzenie nosi jakiekolwiek oznaki uszkodzenia lub naruszenia, nie wykonuj dalszych czynności. Skontaktuj się z działem pomocy technicznej firmy Microsoft, aby otrzymać urządzenie zamienne.
2. Przed podłączeniem przewodów do urządzenia sprawdź, czy masz następujące przewody:
    
    - (Dołączony) Przewód zasilający z uziemieniem przeznaczony do prądu co najmniej 10 A, ze złączem typu IEC60320 C-13 po jednej stronie, umożliwiającym podłączenie do urządzenia.
    - Jeden kabel sieciowy RJ-45 CAT 6 (do użytku z interfejsem sieciowym MGMT)
    - Dwa kable miedziane SFP+ Twinax 10 GbE (do użytku z interfejsami sieciowymi DATA 1 i DATA 2 o przepustowości 10 Gb/s)
    - Jeden kabel sieciowy RJ-45 CAT 6A lub RJ-45 CAT 6 (do użytku z interfejsem sieciowym DATA 3 skonfigurowanym odpowiednio dla przepływności 10 Gb/s lub 1 Gb/s)

3. Wyjmij urządzenie i umieść je na płaskiej powierzchni. 
    
4. Podłącz przewody do urządzenia tak, jak pokazano poniżej.  

    ![Płyta montażowa urządzenia Data Box z podłączonymi kablami](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Podłącz kabel zasilający do urządzenia.
    2. Użyj kabla sieciowego RJ-45 CAT 6, aby podłączyć komputer hosta do portu zarządzania (MGMT) na urządzeniu. 
    3. Użyj kabla miedzianego SFP+ Twinax, aby podłączyć co najmniej jeden interfejs sieciowy 10 Gb/s (wersja preferowana, ale możliwe również użycie interfejsu 1 Gb/s), DATA 1 lub DATA 2, do przesyłu danych. 
    4. Włącz urządzenie. Przycisk zasilania znajduje się na panelu przednim urządzenia.


## <a name="connect"></a>Połącz

Ten krok zajmuje około 5–7 minut.

1. Aby uzyskać hasło urządzenia, przejdź do pozycji **Ogólne > Szczegóły urządzenia** w witrynie [Azure Portal](https://portal.azure.com).
2. Przypisz statyczny adres IP 192.168.100.5 i podsieć 255.255.255.0 do karty Ethernet na komputerze używanym do nawiązania połączenia z urządzeniem Data Box. Uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem `https://192.168.100.10`. Nawiązywanie połączenia może potrwać do 5 minut po włączeniu urządzenia. 
3. Zaloguj się przy użyciu hasła uzyskanego z witryny Azure Portal. Zostanie wyświetlony komunikat o błędzie, informujący o problemie z certyfikatem zabezpieczeń witryny internetowej. Postępuj zgodnie z instrukcjami dotyczącymi używanej przeglądarki, aby przejść do odpowiedniej strony internetowej.
4. Domyślne ustawienie sieci dla interfejsu 10 Gb/s (lub 1 Gb/s) to DHCP. Jeśli jest taka potrzeba, można skonfigurować interfejs jako statyczny i podać adres IP. 

## <a name="copy-data"></a>Kopiowanie danych

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych i prędkości połączenia sieciowego.
 
1. Jeśli używasz hosta z systemem Windows, skorzystaj z narzędzia do kopiowania plików zgodnego z protokołem SMB, na przykład Robocopy. W przypadku hosta z systemem plików NFS skopiuj dane za pomocą polecenia `cp` lub `rsync`. Połącz narzędzie z urządzeniem i rozpocznij kopiowanie danych do udziałów. Aby uzyskać więcej informacji na temat kopiowania danych za pomocą narzędzia Robocopy, zobacz [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10)).
2. Połącz się z udziałami przy użyciu następującej ścieżki: `\\<IP address of your device>\ShareName`. Aby uzyskać poświadczenia dostępu do udziału, przejdź do strony **Connect & copy** (Połączenie i kopiowanie) w lokalnym internetowym interfejsie użytkownika urządzenia Data Box.
3. Upewnij się, że nazwy udziałów, folderów i danych są zgodne z instrukcjami przedstawionymi w temacie [Azure Storage and Data Box service limits (Ograniczenia usług Azure Storage i Data Box)](data-box-limits.md).

## <a name="ship-to-azure"></a>Wysyłka do platformy Azure 

Ta operacja trwa około 10–15 minut.

1. Przejdź do strony **Prepare to ship** (Przygotowanie do wysłania) w lokalnym internetowym interfejsie użytkownika i rozpocznij przygotowanie do wysłania. 
2. Wyłącz urządzenie za pomocą lokalnego internetowego interfejsu użytkownika. Odłącz przewody od urządzenia. 
3. Zwrotna etykieta wysyłkowa powinna być widoczna na wyświetlaczu E-ink. Jeśli etykieta nie jest widoczna na wyświetlaczu E-ink, pobierz etykietę wysyłkową z witryny Azure Portal i umieść ją w przezroczystej koszulce znajdującej się na urządzeniu.
4. Zamknij opakowanie i wyślij do firmy Microsoft. 

## <a name="verify-data"></a>Weryfikacja danych

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych.

1. Gdy urządzenie Data Box zostanie podłączone do sieci w centrum danych platformy Azure, automatycznie rozpocznie się przekazywanie danych na platformę Azure. 
2. Usługa Azure Data Box powiadomi Cię o zakończeniu kopiowania danych w witrynie Azure Portal. 

    1. Sprawdź dzienniki błędów pod kątem ewentualnych niepowodzeń i podejmij odpowiednie działania.
    2. Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ten krok zajmuje około 2–3 minut.

- Możesz anulować zamówienie urządzenia Data Box w witrynie Azure Portal przed jego przetworzeniem. Po przetworzeniu zamówienia nie można go anulować. Realizacja zamówienia będzie kontynuowana aż do etapu ukończenia. Aby anulować zamówienie, w sekcji **Przegląd** kliknij pozycję **Anuluj** na pasku poleceń.

- Możesz usunąć zamówienie, gdy jego stan w witrynie Azure Portal to **Ukończono** lub **Anulowano**. Aby usunąć zamówienie, w sekcji **Przegląd** kliknij pozycję **Usuń** na pasku poleceń.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym przewodnikiem Szybki start wdrożono usługę Azure Data Box w celu zaimportowania danych na platformę Azure. Aby dowiedzieć się więcej na temat zarządzania usługą Azure Data Box, przejdź do kolejnego samouczka: 

> [!div class="nextstepaction"]
> [Use the Azure portal to administer Data Box (Administrowanie usługą Data Box w witrynie Azure Portal)](data-box-portal-admin.md)

::: zone-end
