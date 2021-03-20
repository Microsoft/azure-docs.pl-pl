---
title: Tworzenie infrastruktury dla klastra w systemie AWS
description: W tym samouczku dowiesz się, jak skonfigurować infrastrukturę usług AWS na potrzeby uruchamiania klastra usługi Service Fabric.
ms.topic: tutorial
ms.date: 05/11/2018
ms.custom: mvc
ms.openlocfilehash: c7a18b0dcdc04bdf66ac4b36ce7376ee018eb238
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842907"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>Samouczek: tworzenie infrastruktury usług AWS do obsługi klastra usługi Service Fabric

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. Ta seria samouczków przedstawia proces tworzenia klastra autonomicznego hostowanego w usłudze AWS i instalowania w nim aplikacji.

Niniejszy samouczek jest pierwszą częścią serii. W tym artykule przedstawiono sposób generowania zasobów usług AWS wymaganych do hostowania klastra autonomicznego usługi Service Fabric. W następnych artykułach opisano sposób instalowania autonomicznego zestawu usługi Service Fabric, instalowania w klastrze przykładowej aplikacji i na koniec czyszczenia klastra.

Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu wystąpień EC2
> * Modyfikowanie grupy zabezpieczeń
> * Zaloguj się do jednego z wystąpień
> * Przygotowywanie wystąpienia na potrzeby usługi Service Fabric

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka jest potrzebne konto usług AWS.  Jeśli nie masz jeszcze konta, przejdź do [konsoli usług AWS](https://aws.amazon.com/), aby je utworzyć.

## <a name="create-ec2-instances"></a>Tworzenie wystąpień EC2

Zaloguj się do konsoli usługi AWS > wprowadź **EC2** w polu wyszukiwania, > **EC2 serwery wirtualne w chmurze**

![Wyszukiwanie w konsoli usług AWS][aws-console]

Wybierz pozycję **Uruchom wystąpienie**. Na następnym ekranie wybierz przycisk **Wybierz** obok nazwy systemu Microsoft Windows Server 2016 Base.

![Wybór wystąpienia EC2][aws-ec2instance]

Wybierz pozycję **t2.medium**, a następnie pozycję **Dalej: skonfiguruj szczegóły wystąpienia**. Na następnym ekranie zmień liczbę wystąpień na `3` i wybierz pozycję **Szczegóły zaawansowane**, aby rozwinąć tę sekcję.

Aby połączyć maszyny wirtualne z usługą Service Fabric, maszyny wirtualne, które hostują infrastrukturę, muszą mieć takie same poświadczenia.  Istnieją dwa podstawowe sposoby na uzyskanie spójnych poświadczeń: dołączenie wszystkich hostów do tej samej domeny lub ustawienie takiego samego hasła administratora na każdej maszynie wirtualnej.  W tym samouczku użyto skryptu danych użytkownika w celu ustawienia dla wszystkich wystąpień EC2 takiego samego hasła.  W środowisku produkcyjnym dołączenie hostów do domeny systemu Windows jest bardziej bezpieczne.

Wprowadź następujący skrypt w polu danych użytkownika w konsoli:

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

Po wprowadzeniu skryptu programu PowerShell wybierz przycisk **Przejrzyj i uruchom**.

![Przeglądanie i uruchamianie EC2][aws-ec2configure2]

Na ekranie przeglądu wybierz pozycję **Uruchom**.  Następnie wybierz z listy rozwijanej wartość **Kontynuuj bez pary kluczy** i zaznacz pole wyboru wskazujące, że znasz hasło.

![Wybór pary kluczy usług AWS][aws-keypair]

Na koniec wybierz przycisk **Uruchom wystąpienia**, a następnie pozycję **Wyświetl wystąpienia**.  W ten sposób utworzono podstawę dla klastra usługi Service Fabric. Teraz trzeba skonfigurować same wystąpienia, aby przygotować je do konfiguracji usługi Service Fabric.

## <a name="modify-the-security-group"></a>Modyfikowanie grupy zabezpieczeń

Usługa Service Fabric wymaga otwarcia niektórych portów między hostami w klastrze. Aby otworzyć te porty w infrastrukturze usług AWS, wybierz jedno z utworzonych wystąpień. Następnie wybierz nazwę grupy zabezpieczeń, na przykład **launch-wizard-1**. Teraz wybierz kartę **Przychodzące**.

Aby uniknąć otwierania tych portów dla wszystkich użytkowników na świecie, należy otworzyć je tylko dla hostów z tej samej grupy zabezpieczeń. Zanotuj identyfikator grupy zabezpieczeń, w tym przykładzie jest to **sg-c4fb1eba**.  Wybierz pozycję **Edytuj**.

Następnie dodaj cztery reguły do grupy zabezpieczeń pod kątem zależności usług i jeszcze trzy dla samej usługi Service Fabric. Pierwsza reguła zezwala na ruch ICMP, aby można było przeprowadzić podstawowe monitorowanie połączeń. Pozostałe reguły otwierają porty wymagane do włączenia protokołu SMB i rejestru zdalnego.

W przypadku pierwszej reguły wybierz przycisk **Dodaj regułę**, a następnie z listy rozwijanej wybierz pozycję **Wszystkie ICMP — IPv4**. Wybierz pole wprowadzania obok wartości Niestandardowe i wprowadź zanotowany wcześniej identyfikator grupy zabezpieczeń.

Wykonaj podobne czynności w przypadku ostatnich trzech zależności.  Wybierz przycisk **Dodaj regułę**, z listy rozwijanej wybierz pozycję **Niestandardowa reguła TCP** i jako zakres portów wprowadź dla każdej reguły jedną z wartości: `135`, `137-139` lub `445`. Na koniec w polu Źródło wprowadź swój identyfikator grupy zabezpieczeń.

![Porty grupy zabezpieczeń][aws-ec2securityports]

Teraz, po otwarciu portów dla zależności, musisz wykonać takie same czynności względem portów używanych przez samą usługę Service Fabric do komunikowania się. Wybierz przycisk **Dodaj regułę**, z listy rozwijanej wybierz pozycję **Niestandardowa reguła TCP**, jako zakres portów wprowadź wartość `20001-20031` i w polu Źródło wprowadź grupę zabezpieczeń.

Następnie dodaj regułę dla zakresu portów efemerycznych.  Wybierz przycisk **Dodaj regułę**, z listy rozwijanej wybierz pozycję **Niestandardowa reguła TCP** i jako zakres portów wprowadź wartość `20606-20861`. Na koniec w polu Źródło wprowadź swój identyfikator grupy zabezpieczeń.

Dwie ostatnie reguły dla usługi Service Fabric powodują otwarcie jej dla wszystkich użytkowników na świecie, aby móc zarządzać klastrem usługi Service Fabric z komputera osobistego. Wybierz przycisk **Dodaj regułę**, z listy rozwijanej wybierz pozycję **Niestandardowa reguła TCP** i jako zakres portów wprowadź jedną z wartości — `19000-19003` lub `19080-19081`, a następnie wybierz z listy rozwijanej Źródło wartość Dowolne miejsce.

Na koniec trzeba otworzyć port 8080, aby móc nawiązać połączenie z aplikacją po jej wdrożeniu. Wybierz przycisk **Dodaj regułę**, z listy rozwijanej wybierz pozycję **Niestandardowa reguła TCP** i jako zakres portów wprowadź wartość `8080`, a następnie wybierz z listy rozwijanej Źródło wartość Dowolne miejsce.

Teraz wszystkie reguły są już wprowadzone. Wybierz pozycję **Zapisz**.

## <a name="connect-to-an-instance-and-validate-connectivity"></a>Nawiązywanie połączenia z wystąpieniem i weryfikowanie łączności

Na karcie grupy zabezpieczeń wybierz pozycję **Wystąpienia** z menu po lewej stronie.  Wybierz poszczególne wystąpienia, które zostały utworzone, i zanotuj ich prywatne adresy IP. W poniższych przykładach użyto adresów `172.31.21.141` i `172.31.20.163`.

Po zanotowaniu wszystkich adresów IP wybierz jedno z wystąpień, z którym ma zostać nawiązane połączenie, kliknij go prawym przyciskiem myszy i wybierz polecenie **Połącz**.  W tym miejscu możesz pobrać plik RDP dla tego konkretnego wystąpienia.  Wybierz przycisk **Pobierz plik pulpitu zdalnego**, a następnie otwórz pobierany plik w celu nawiązania połączenia pulpitu zdalnego (RDP) z tym wystąpieniem.  Po wyświetleniu monitu wprowadź hasło `serv1ceF@bricP@ssword`.

![Pobieranie pliku pulpitu zdalnego][aws-rdp]

Po pomyślnym nawiązaniu połączenia z wystąpieniem zweryfikuj połączenie i możliwość udostępnienia plików.  Wcześniej zebrano adresy IP wszystkich wystąpień. Wybierz wystąpienie, z którym aktualnie nawiązano połączenie. Przejdź do menu **Start**, wprowadź `cmd` i wybierz pozycję **Wiersz polecenia**.

W tych przykładach nawiązano połączenie RDP z następującym adresem IP: 172.31.21.141. Wszystkie testy łączności są następnie przeprowadzane względem drugiego adresu IP: 172.31.20.163.

Aby sprawdzić, czy podstawowe połączenie działa, użyj polecenia ping.

```
ping 172.31.20.163
```

Jeśli w Twoich danych wyjściowych cztery razy pojawia się komunikat podobny do tego: `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128`, oznacza to, że połączenie między wystąpieniami działa.  Teraz użyj następującego polecenia, aby zweryfikować, że udostępnianie za pomocą protokołu SMB działa:

```
net use * \\172.31.20.163\c$
```

Powinien zostać wyświetlony komunikat podobny do następującego: `Drive Z: is now connected to \\172.31.20.163\c$.`.

## <a name="prep-instances-for-service-fabric"></a>Przygotowywanie wystąpień na potrzeby usługi Service Fabric

Jeśli wszystkie opisywane czynności były tworzone od podstaw, należało wykonać jeszcze kilka dodatkowych kroków.  Należało sprawdzić, czy rejestr zdalny jest uruchomiony, włączyć protokół SMB i otworzyć porty wymagane dla protokołu SMB oraz rejestru zdalnego.

Aby sobie ułatwić pracę, wszystkie te czynności zostały wykonane podczas inicjowania wystąpień za pomocą skryptu danych użytkownika.

W celu włączenia protokołu SMB użyto tego polecenia programu PowerShell:

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

W celu otwarcia portów w zaporze użyto tego polecenia programu PowerShell:

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>Następne kroki

W części pierwszej tej serii przedstawiono sposób uruchamiania trzech wystąpień EC2 i ich konfigurowania na potrzeby instalacji usługi Service Fabric:

> [!div class="checklist"]
> * Tworzenie zestawu wystąpień EC2
> * Modyfikowanie grupy zabezpieczeń
> * Zaloguj się do jednego z wystąpień
> * Przygotowywanie wystąpienia na potrzeby usługi Service Fabric

Przejdź do części drugiej tej serii, aby skonfigurować usługę Service Fabric w klastrze.

> [!div class="nextstepaction"]
> [Zainstaluj usługę Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png