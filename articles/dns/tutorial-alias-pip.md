---
title: 'Samouczek: Tworzenie rekordu Azure DNS aliasu w celu odwoływania się do publicznego adresu IP platformy Azure'
description: W tym samouczku pokazano, jak skonfigurować rekord aliasu usługi Azure DNS w celu odwoływania się do publicznego adresu IP platformy Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: d3017d09e94040d16950598dad360fe32930c16b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80985443"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Samouczek: konfigurowanie rekordu aliasu w celu odwoływania się do publicznego adresu IP platformy Azure 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie infrastruktury sieci.
> * Utwórz maszynę wirtualną serwera sieci Web z publicznym adresem IP.
> * Utwórz rekord aliasu, który wskazuje na publiczny adres IP.
> * Testowanie rekordu aliasu.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne
Do testowania niezbędna jest nazwa domeny, którą można hostować w usłudze Azure DNS. Musisz mieć pełną kontrolę nad tą domeną. Pełna kontrola obejmuje możliwość ustawiania dla domeny rekordów serwera nazw (NS).

Aby uzyskać instrukcje dotyczące hostowania własnej domeny w usłudze Azure DNS, zobacz [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).

Przykładowa domena używana w tym samouczku to contoso.com, ale skorzystaj z własnej nazwy domeny.

## <a name="create-the-network-infrastructure"></a>Tworzenie infrastruktury sieci
Najpierw utwórz sieć wirtualną i podsieć, aby umieścić w nich serwery internetowe.
1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**. W polu wyszukiwania wprowadź *grupę zasobów*, a następnie utwórz grupę zasobów o nazwie **RG-DNS-Alias-pip**.
3. Wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **sieci wirtualnej**.
4. Utwórz sieć wirtualną o nazwie **VNet-Servers**. Umieść ją w grupie zasobów **RG-DNS-Alias-pip**, a następnie nadaj podsieci nazwę **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Tworzenie maszyny wirtualnej serwera sieci Web
1. Wybierz pozycję **Utwórz zasób**  >  **Windows Server 2016 VM**.
2. Wprowadź nazwę **Web-01** i umieść maszynę wirtualną w grupie zasobów **RG-DNS-Alias-TM**. Wprowadź nazwę użytkownika i hasło, a następnie wybierz pozycję **OK**.
3. W obszarze **Rozmiar** wybierz jednostkę magazynową zawierającą 8 GB pamięci RAM.
4. W obszarze **Ustawienia** wybierz sieć wirtualną **VNet-Servers** i podsieć **SN-Web**. W przypadku publicznych portów przychodzących wybierz pozycję **http**  >  **https**  >  **RDP (3389)**, a następnie wybierz przycisk **OK**.
5. Na stronie **Podsumowanie** wybierz pozycję **Utwórz**.

Wykonanie tej procedury trwa kilka minut. Maszyna wirtualna będzie miała dołączoną kartę sieciową, która będzie miała podstawowy dynamiczny adres IP o nazwie Web-01-IP. Publiczny adres IP zmieni się za każdym razem, gdy maszyna wirtualna zostanie ponownie uruchomiona.

### <a name="install-iis"></a>Instalowanie usług IIS

Zainstaluj usługi IIS na maszynie wirtualnej **Web-01**.

1. Połącz się z **siecią Web-01** i zaloguj się.
2. Na pulpicie nawigacyjnym **Menedżer serwera** wybierz pozycję **Dodaj role i funkcje**.
3. Trzykrotnie wybierz pozycję **Dalej**. Na stronie **Role serwera** wybierz pozycję **Serwer sieci Web (IIS)**.
4. Wybierz pozycję **Dodaj funkcje**, a następnie wybierz pozycję **Dalej**.
5. Czterokrotnie wybierz pozycję **Dalej**, a następnie wybierz pozycję **Zainstaluj**. Wykonanie tej procedury trwa kilka minut.
6. Po zakończeniu instalacji wybierz pozycję **Zamknij**.
7. Otwórz przeglądarkę internetową. Przejdź do lokalizacji **localhost**, aby sprawdzić, czy zostanie wyświetlona domyślna strona internetowa usług IIS.

## <a name="create-an-alias-record"></a>Tworzenie rekordu aliasu

Utwórz rekord aliasu wskazujący na publiczny adres IP.

1. Wybierz strefę usługi Azure DNS, aby ją otworzyć.
2. Wybierz pozycję **Zestaw rekordów**.
3. W polu tekstowym **Nazwa** wybierz pozycję **web01**.
4. Pozostaw pole **Typ** ustawione na rekord **A**.
5. Zaznacz pole wyboru **Zestaw rekordów aliasów**.
6. Wybierz pozycję **Wybierz usługę Azure**, a następnie wybierz publiczny adres IP **Web-01-ip**.

## <a name="test-the-alias-record"></a>Testowanie rekordu aliasu

1. W grupie zasobów **RG-DNS-Alias-pip** wybierz maszynę wirtualną **Web-01**. Zanotuj publiczny adres IP.
1. Z poziomu przeglądarki internetowej przejdź do w pełni kwalifikowanej nazwy domeny dla maszyny wirtualnej Web01-01. Przykładowa domena to **web01.contoso.com**. Teraz zostanie wyświetlona domyślna strona internetowa usług IIS.
2. Zamknij przeglądarkę internetową.
3. Zatrzymaj maszynę wirtualną **Web-01**, a następnie uruchom ją ponownie.
4. Po ponownym uruchomieniu maszyny wirtualnej zanotuj jej nowy publiczny adres IP.
5. Otwórz nową przeglądarkę. Ponownie przejdź do w pełni kwalifikowanej nazwy domeny dla maszyny wirtualnej Web01-01. Przykładowa domena to **web01.contoso.com**.

Wykonanie procedury kończy się pomyślnie, ponieważ użyto rekordu aliasu w celu wskazania publicznego zasobu adresu IP, a nie standardowego rekordu A.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych w ramach tego samouczka, usuń grupę zasobów **RG-DNS-Alias-pip**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku został skonfigurowany rekord aliasu w celu odwoływania się do publicznego adresu IP platformy Azure. Aby dowiedzieć się więcej o usłudze Azure DNS i aplikacjach internetowych, przejdź do samouczka dotyczącego aplikacji internetowych.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)
