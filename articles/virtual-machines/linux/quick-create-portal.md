---
title: Szybki Start — Tworzenie maszyny wirtualnej z systemem Linux w Azure Portal
description: W tym przewodniku szybki start dowiesz się, jak utworzyć maszynę wirtualną z systemem Linux przy użyciu Azure Portal.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/25/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 405cb107711845a6699e09bc09a1d2d9f3005cd6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87499980"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Szybki start: tworzenie maszyny wirtualnej z systemem Linux w witrynie Azure Portal

Maszyny wirtualne platformy Azure można utworzyć za pomocą witryny Azure Portal. Azure Portal to oparty na przeglądarce interfejs użytkownika służący do tworzenia zasobów platformy Azure. W tym przewodniku szybki start pokazano, jak za pomocą Azure Portal wdrożyć maszynę wirtualną z systemem Linux przy użyciu programu Ubuntu 18,04 LTS. Aby zobaczyć działanie maszyny wirtualnej, połączysz się z nią za pomocą protokołu SSH i zainstalujesz serwer internetowy NGINX.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Azure Portal](https://portal.azure.com) , jeśli jeszcze tego nie zrobiono.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wpisz **maszyny wirtualne** w wyszukiwaniu.
1. W obszarze **usługi**wybierz pozycję **maszyny wirtualne**.
1. Na stronie **maszyny wirtualne** wybierz pozycję **Dodaj**. Zostanie otwarta strona **Tworzenie maszyny wirtualnej** .
1. Na karcie **Podstawowe**, w obszarze **Szczegóły projektu**, upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz opcję **Utwórz nową** grupę zasobów. Wpisz nazwę *zasobu* . *. 

    ![Zrzut ekranu przedstawiający sekcję Szczegóły projektu, w której można wybrać subskrypcję platformy Azure i grupę zasobów dla maszyny wirtualnej](./media/quick-create-portal/project-details.png)

1. W obszarze **szczegóły wystąpienia**wpisz *myVM* dla **nazwy maszyny wirtualnej**, wybierz *Wschodnie stany USA* dla **regionu**i wybierz *Ubuntu 18,04 LTS* dla Twojego **obrazu**. Inne wartości pozostaw domyślne.

    ![Zrzut ekranu przedstawiający sekcję Szczegóły wystąpienia, w której podano nazwę maszyny wirtualnej i wybierz jej region, obraz i rozmiar](./media/quick-create-portal/instance-details.png)

1. W obszarze **konto administratora**wybierz pozycję **publiczny klucz SSH**.

1. W polu **Nazwa użytkownika** wpisz *azureuser*.

1. W polu **Źródło klucza publicznego SSH**pozostaw wartość domyślną **Generuj nową parę kluczy**, a następnie wpisz *klucze* dla **nazwy pary kluczy**.

    ![Zrzut ekranu przedstawiający sekcję konta administratora, w której wybierasz typ uwierzytelniania i podaj poświadczenia administratora](./media/quick-create-portal/administrator-account.png)

1. W obszarze publiczne porty przychodzące dla **reguł portów ruchu**przychodzącego  >  **Public inbound ports**wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **SSH (22)** i **http (80)** z listy rozwijanej. 

    ![Zrzut ekranu przedstawiający sekcję reguły portów ruchu przychodzącego, w której wybierane są dozwolone porty przychodzące](./media/quick-create-portal/inbound-port-rules.png)

1. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.

1. Na stronie **Tworzenie maszyny wirtualnej** wyświetlone są szczegółowe informacje o maszynie wirtualnej, którą masz zamiar utworzyć. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Gdy zostanie otwarte okno **Generuj nową parę kluczy** , wybierz pozycję **Pobierz klucz prywatny i utwórz zasób**. Plik klucza zostanie pobrany jako **klucze. pem**. Upewnij się, że wiesz `.pem` , gdzie plik został pobrany, w następnym kroku będzie potrzebna ścieżka do niego.

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

1. Na stronie nowej maszyny wirtualnej wybierz publiczny adres IP i skopiuj go do Schowka.


    ![Zrzut ekranu przedstawiający sposób kopiowania adresu IP dla maszyny wirtualnej](./media/quick-create-portal/ip-address.png)

## <a name="connect-to-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Utwórz połączenie SSH z maszyną wirtualną.

1. Jeśli pracujesz na komputerze Mac lub z systemem Linux, Otwórz wiersz polecenia bash. Jeśli korzystasz z komputera z systemem Windows, Otwórz wiersz polecenia programu PowerShell. 

1. Po wyświetleniu monitu Otwórz połączenie SSH z maszyną wirtualną. Zastąp adres IP adresem z maszyny wirtualnej i Zastąp ścieżkę do `.pem` ścieżki, w której plik klucza został pobrany.

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> Utworzony klucz SSH może być używany podczas następnego tworzenia maszyny wirtualnej na platformie Azure. Po następnym utworzeniu maszyny wirtualnej wybierz opcję **Użyj klucza przechowywanego w usłudze Azure** dla **źródła klucza publicznego** . Masz już klucz prywatny na komputerze, więc nie musisz pobierać żadnych informacji.

## <a name="install-web-server"></a>Instalowanie serwera internetowego

Aby zobaczyć działanie maszyny wirtualnej, zainstaluj serwer internetowy NGINX. Z poziomu sesji SSH zaktualizuj źródła pakietu, a następnie zainstaluj najnowszą wersję pakietu NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Gdy skończysz, wpisz polecenie `exit`, aby opuścić sesję SSH.


## <a name="view-the-web-server-in-action"></a>Oglądanie działającego serwera internetowego

Użyj wybranej przeglądarki internetowej, aby wyświetlić domyślną strona powitalną serwera NGINX. Wpisz publiczny adres IP maszyny wirtualnej jako adres sieci Web. Publiczny adres IP można znaleźć na stronie przeglądu maszyny wirtualnej lub jako część wcześniej użytych parametrów połączenia SSH.

![Zrzut ekranu przedstawiający domyślną witrynę NGINX w przeglądarce](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz opcję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono prostą maszynę wirtualną, utworzono sieciową grupę zabezpieczeń i regułę oraz zainstalowano podstawowy serwer internetowy. Aby dowiedzieć się więcej o maszynach wirtualnych platformy Azure, przejdź do samouczka dla maszyn wirtualnych z systemem Linux.

> [!div class="nextstepaction"]
> [Samouczki dla maszyny wirtualnej platformy Azure z systemem Linux](./tutorial-manage-vm.md)
