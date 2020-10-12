---
title: Jak uruchomić aplikację przy użyciu Menedżera Fortanix enklawy
description: Dowiedz się, jak przekonwertować obrazy z kontenerów za pomocą programu Fortanix enklawy Manager
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: 235f4eb236e144d41ffb9958b09dab0cf5c269b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462432"
---
# <a name="how-to-run-an-application-with-fortanix-enclave-manager"></a>Instrukcje: uruchamianie aplikacji za pomocą Menedżera Fortanix enklawy 

Zacznij korzystać z aplikacji w usłudze Azure poufne dane przy użyciu programu [Fortanix enklawy Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) i [agenta Fortanix Node](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) z poziomu [Fortanix](https://www.fortanix.com/).


Fortanix to dostawca oprogramowania innej firmy z produktami i usługami opartymi na infrastrukturze platformy Azure. Istnieją inni dostawcy innych firm oferujących podobne poufne usługi obliczeniowe na platformie Azure.

> [!Note] 
 > Produkty, do których odwołuje się ten dokument, nie są pod kontrolą firmy Microsoft. Firma Microsoft udostępnia te informacje tylko jako wygoda, a odwołanie do tych produktów innych firm nie oznacza potwierdzenia przez firmę Microsoft.



W tym samouczku pokazano, jak przekonwertować obraz aplikacji na obraz chroniony przed obliczeniami. To środowisko używa oprogramowania [Fortanix](https://www.fortanix.com/) obsługiwanego przez maszyny wirtualne z obsługą technologii Intel SGX platformy DCsv2-Series Azure. To rozwiązanie organizuje krytyczne zasady zabezpieczeń, takie jak weryfikacja tożsamości i kontrola dostępu do danych.

 W celu uzyskania pomocy technicznej dotyczącej usługi Fortanix Dołącz do [społeczności Fortanixa](https://fortanix.com/community/) i użyj #enclavemanager kanału.


## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz konta menedżera Fortanix enklawy, [zarejestruj się](https://em.fortanix.com/auth/sign-up) przed rozpoczęciem.
1. Prywatny rejestr [platformy Docker](https://docs.docker.com/) służący do wypychania przekonwertowanych obrazów aplikacji.
1. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> Konta bezpłatnych wersji próbnych nie mają dostępu do maszyn wirtualnych używanych w tym samouczku. Przeprowadź uaktualnienie do subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem.

## <a name="add-an-application-to-fortanix-enclave-manager"></a>Dodawanie aplikacji do Menedżera Fortanix enklawy
1. Zaloguj się do [Fortanix enklawy Manager (FORTANIX em)](https://em.fortanix.com)
1. Przejdź do strony **konta** i wybierz pozycję **Dodaj konto** , aby utworzyć nowe konto. 
    
![Tworzenie konta](media/how-to-fortanix-enclave-manager/create-account.png)

1. Po utworzeniu konta naciśnij **pozycję Wybierz** , aby wybrać nowo utworzone konto. Teraz możemy rozpocząć rejestrowanie węzłów obliczeniowych i tworzenie aplikacji. 
1. Wybierz przycisk **+ aplikacja** , aby dodać aplikację. W tym przykładzie dodamy aplikację systemu operacyjnego enklawy Server. 

1. Wybierz przycisk **Dodaj** dla aplikacji systemu operacyjnego enklawy. 

    ![Dodawanie aplikacji](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > Ten samouczek obejmuje Dodawanie tylko aplikacji systemu operacyjnego enklawy. [Przeczytaj więcej](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Enclave-Manager) na temat przenoszenia aplikacji EDP Rust do Menedżera Fortanix enklawy. 

6. W tym samouczku użyjemy rejestru platformy Docker Fortanix dla przykładowej aplikacji. Wprowadź szczegółowe informacje z poniższych informacji. Użyj prywatnego rejestru platformy Docker, aby zachować obraz wyjściowy. 
 
    - **Nazwa aplikacji**: serwer aplikacji Python
    - **Opis**: serwer z kolbą Python
    - **Nazwa obrazu wejściowego**: fortanix/Python-Kolba
    - **Nazwa obrazu wyjściowego**: fortanx-Private/Python-SGX
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Rozmiar pamięci**: 1 GB
    - **Liczba wątków**: 128

    *Opcjonalnie*: Uruchom aplikację.
    - **Centrum platformy Docker**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **Aplikacja**: fortanix/Python — Kolba

        Uruchom następujące polecenie:
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. Dodaj certyfikat. Wypełnij informacje, korzystając z poniższych informacji, a następnie wybierz pozycję **dalej**:
    - **Domena**: MojaApl. domena. Dom
    - **Typ**: certyfikat wystawiony przez Menedżera enklawy 
    - **Ścieżka klucza**:/appkey.pem
    - **Typ klucza**: RSA
    - **Ścieżka certyfikatu**:/appcert.pem
    - **Rozmiar klucza RSA**: 2048 bitów
    

## <a name="create-an-image"></a>Tworzenie obrazu
Obraz Fortanix EM to wersja oprogramowania lub wersja aplikacji. Każdy obraz jest skojarzony z jednym skrótem enklawy (MRENCLAVE). 
1. Na stronie **Dodawanie obrazu** wprowadź **poświadczenia rejestru** dla **nazwy obrazu wyjściowego**. Te poświadczenia są używane w celu uzyskania dostępu do rejestru prywatnego platformy Docker, w którym zostanie wypchnięci ten obraz. 

    ![Utwórz obraz](media/how-to-fortanix-enclave-manager/create-image.png)
1. Podaj tag obrazu i wybierz pozycję **Utwórz**.

    ![Dodaj tag](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>Lista dozwolonych domen i obrazów 
Aplikacja, której domena zostanie dodana do listy dozwolonych, spowoduje uzyskanie certyfikatu TLS z Menedżera Fortanix enklawy. Podobnie, gdy aplikacja jest uruchamiana z przekonwertowanego obrazu, podejmie próbę skontaktowania się z menedżerem Fortanix enklawy. Następnie aplikacja będzie żądać certyfikatu TLS. 

Przejdź do karty **zadania** po lewej stronie i zatwierdź oczekujące żądania, aby zezwolić na domenę i obraz. 

## <a name="enroll-compute-node-agent-in-azure"></a>Rejestrowanie agenta węzła obliczeniowego na platformie Azure

### <a name="generate-and-copy-join-token"></a>Generuj i Kopiuj token sprzężenia
W programie Fortanix enklawy Manager utworzysz token. Token ten umożliwia węzłowi obliczeniowemu na platformie Azure samodzielną identyfikację. Musisz podać ten token na maszynie wirtualnej platformy Azure.
1. W konsoli zarządzania wybierz przycisk **+ zarejestruj węzeł** . 
1. Wybierz pozycję **Generuj token** , aby wygenerować token sprzężenia. Skopiuj token.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Rejestrowanie węzłów w agencie węzła Fortanix w portalu Azure Marketplace

Utworzenie agenta węzła Fortanix spowoduje wdrożenie maszyny wirtualnej, interfejsu sieciowego, sieci wirtualnej, sieciowej grupy zabezpieczeń i publicznego adresu IP w grupie zasobów platformy Azure. Za subskrypcję platformy Azure będą naliczane opłaty co godzinę dla maszyny wirtualnej. Przed utworzeniem agenta węzła Fortanix Przejrzyj [stronę cennika maszyny wirtualnej](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) platformy Azure dla serii DCsv2. Usuń zasoby platformy Azure, gdy nie są używane. 

1. Przejdź do [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) i zaloguj się przy użyciu poświadczeń platformy Azure.
1. Na pasku wyszukiwania wpisz **Fortanix poufne Computing Agent Node**. Wybierz aplikację, która zostanie wyświetlona w polu wyszukiwania o nazwie **Agent węzła Fortanix poufnego** , aby przejść do strony głównej oferty. 
     ![Wyszukaj w witrynie Marketplace](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. Wybierz pozycję **Pobierz teraz**, wprowadź informacje w razie potrzeby, a następnie wybierz pozycję **Kontynuuj**. Nastąpi przekierowanie do Azure Portal. 
1. Wybierz pozycję **Utwórz** , aby przejść na stronę wdrożenia agenta Fortanixego dla komputerów poufnych.
1. Na tej stronie będziesz wprowadzać informacje w celu wdrożenia maszyny wirtualnej. Ta maszyna wirtualna jest DCsv2-Series maszynę wirtualną z obsługą technologii Intel SGX z platformy Azure z zainstalowanym oprogramowaniem agenta Node Fortanix. Agent węzła zezwoli na bezpieczne działanie konwertowanego obrazu w węzłach Intel SGX na platformie Azure.  Wybierz **subskrypcję** i **grupę zasobów** , w której chcesz wdrożyć maszynę wirtualną i skojarzone z nią zasoby. 
 
    > [!NOTE]
    > Podczas wdrażania DCsv2-Series maszyn wirtualnych na platformie Azure obowiązują ograniczenia. Może być konieczne przekroczenie limitu przydziału dla dodatkowych rdzeni. Aby uzyskać więcej informacji, Przeczytaj o [rozwiązaniach do przetwarzania danych poufnych na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/confidential-computing/virtual-machine-solutions) . 

1. Wybierz dostępny region.
1. Wprowadź nazwę maszyny wirtualnej w polu **nazwa węzła** . 
1. Wprowadź nazwę użytkownika i hasło (lub klucz SSH) w celu uwierzytelnienia na maszynie wirtualnej.
1. Pozostaw domyślny rozmiar dysku systemu operacyjnego jako 200 i wybierz rozmiar maszyny wirtualnej (Standard_DC4s_v2 wystarczy dla tego samouczka)
1. Wklej token wygenerowany wcześniej w polu **token sprzężenia** .

     ![Wdróż zasób](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent.png)

1. Wybierz pozycję **Recenzja + Utwórz**. Upewnij się, że Walidacja przebiega pomyślnie, a następnie wybierz pozycję **Utwórz**. Po wdrożeniu wszystkich zasobów węzeł obliczeniowy jest teraz rejestrowany w programie enklawy Manager. 

## <a name="run-the-application-image-on-the-compute-node"></a>Uruchamianie obrazu aplikacji w węźle obliczeniowym
Uruchom aplikację, wykonując następujące polecenie. Upewnij się, że jako dane wejściowe dla konkretnej aplikacji zmienisz nazwę adresu IP, portu i skonwertowanego obrazu. 
 
W tym samouczku polecenie do wykonania jest następujące:     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

miejscu 
- *52.152.206.164* jest adresem IP hosta agenta węzła
- *9092* to port, którego Agent węzła nasłuchuje
- *fortanix-Private/Python-SGX* to przekonwertowana aplikacja, którą można znaleźć na karcie obrazy pod kolumną **Nazwa obrazu** w tabeli **obrazy** w portalu fortanix enklawy zarządzanie w sieci Web. 
    
## <a name="verify-and-monitor-the-running-application"></a>Weryfikowanie i monitorowanie uruchomionej aplikacji
1. Wróć do [Menedżera Fortanix enklawy](https://em.fortanix.com/console)
1. Upewnij się, że Pracujesz w ramach **konta** , na którym zarejestrowano węzeł
1. Przejdź do **konsoli zarządzania** , wybierając górną ikonę w okienku nawigacji po lewej stronie. 
1. Wybierz kartę **aplikacja**
1. Sprawdź, czy jest uruchomiona aplikacja ze skojarzonym węzłem obliczeniowym


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i skojarzone zasoby nie będą już potrzebne, można je usunąć. Usunięcie grupy zasobów spowoduje Wyrejestrowanie węzłów skojarzonych z przekonwertowanego obrazu. 

Wybierz grupę zasobów dla maszyny wirtualnej, a następnie wybierz pozycję **Usuń**. Potwierdź nazwę grupy zasobów, aby zakończyć usuwanie zasobów.

Aby usunąć utworzone konto menedżera enklawy Fortanix, przejdź na [stronę konta](https://em.fortanix.com/accounts) w Menedżerze enklawy. Umieść kursor na koncie, które chcesz usunąć. Wybierz czarną czerń kropek w prawym górnym rogu, a następnie wybierz pozycję **Usuń konto**.

  ![delete](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto narzędzi Fortanix do przekonwertowania obrazu aplikacji tak, aby był uruchamiany w oparciu o poufną maszynę wirtualną. Aby uzyskać więcej informacji na temat poufnego przetwarzania maszyn wirtualnych na platformie Azure, zobacz [rozwiązania na Virtual Machines](virtual-machine-solutions.md). 

Aby dowiedzieć się więcej na temat ofert związanych z poufnym przetwarzaniem na platformie Azure, zobacz [Omówienie usługi Azure poufnego przetwarzania](overview.md)

 Dowiedz się, jak wykonywać podobne zadania przy użyciu innych ofert innych firm na platformie Azure, takich jak [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) i [Scone](https://sconedocs.github.io).  