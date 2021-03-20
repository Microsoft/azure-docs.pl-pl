---
title: Konfigurowanie grupy dostępności niezależnej od domeny
description: Dowiedz się, jak skonfigurować grupę dostępności niezależną od domeny Active Directory na SQL Server maszynie wirtualnej na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 0f194101720481f71434709c467d0e3130a0f1f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359459"
---
# <a name="configure-a-workgroup-availability-group"></a>Konfigurowanie grupy dostępności grupy roboczej 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule wyjaśniono kroki niezbędne do Active Directory utworzenia klastra niezależnego od domeny z zawsze włączonymi grupami dostępności. jest to również znany jako klaster grupy roboczej. Ten artykuł koncentruje się na krokach mających zastosowanie do przygotowywania i konfigurowania grupy roboczej oraz grup dostępności, a także błyszczących kroków, które są omówione w innych artykułach, takich jak tworzenie klastra lub wdrażanie grupy dostępności. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować grupę dostępności grupy roboczej, potrzebne są następujące elementy:
- Co najmniej dwie maszyny wirtualne z systemem Windows Server 2016 (lub nowszym), na których działa program SQL Server 2016 (lub nowszy), wdrożone w tym samym zestawie dostępności lub w różnych strefach dostępności przy użyciu statycznych adresów IP. 
- Sieć lokalna z co najmniej 4 bezpłatnymi adresami IP w podsieci. 
- Konto na wszystkich komputerach w grupie administratorów, które ma także prawa sysadmin w SQL Server. 
- Otwórz porty: TCP 1433, TCP 5022, TCP 59999. 

W przypadku odwołania do tego artykułu są używane następujące parametry, ale w razie potrzeby można je zmodyfikować: 

| **Nazwa** | **Parametr** |
| :------ | :---------------------------------- |
| **Węzeł1**   | AGNode1 (10.0.0.4) |
| **Węzeł2**   | AGNode2 (10.0.0.5) |
| **Nazwa klastra** | AGWGAG (10.0.0.6) |
| **Odbiornik** | AGListener (10.0.0.7) | 
| **Sufiks DNS** | ag.wgcluster.example.com | 
| **Nazwa grupy roboczej** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>Ustawianie sufiksu DNS 

W tym kroku Skonfiguruj sufiks DNS dla obu serwerów. Na przykład `ag.wgcluster.example.com`. Dzięki temu można użyć nazwy obiektu, z którym chcesz nawiązać połączenie jako w pełni kwalifikowanego adresu w sieci, na przykład `AGNode1.ag.wgcluster.example.com` . 

Aby skonfigurować sufiks DNS, wykonaj następujące kroki:

1. Protokół RDP w pierwszym węźle i otwiera Menedżer serwera. 
1. Wybierz pozycję **serwer lokalny** , a następnie wybierz nazwę maszyny wirtualnej w polu **Nazwa komputera**. 
1. Wybierz pozycję **Zmień...** **, aby zmienić nazwę tego komputera..**. 
1. Zmień nazwę grupy roboczej tak, aby była znacząca, na przykład `AGWORKGROUP` : 

   ![Zmień nazwę grupy roboczej](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. Wybierz pozycję **więcej...** , aby otworzyć okno dialogowe **sufiks DNS i nazwa NetBIOS komputera** . 
1. Wpisz nazwę sufiksu DNS w obszarze **sufiks podstawowej domeny DNS tego komputera**, na przykład, `ag.wgcluster.example.com` a następnie wybierz przycisk **OK**: 

   ![Zrzut ekranu przedstawia sufiks D N S i nazwę NetBIOS komputera, w którym można wprowadzić wartość.](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. Upewnij się, że **pełna nazwa komputera** ma teraz sufiks DNS, a następnie wybierz przycisk **OK** , aby zapisać zmiany: 

   ![Zrzut ekranu pokazuje, gdzie ma zostać wyświetlona pełna nazwa komputera.](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. Po wyświetleniu odpowiedniego monitu ponownie uruchom serwer. 
1. Powtórz te kroki dla wszystkich innych węzłów, które mają być używane dla grupy dostępności. 

## <a name="edit-a-host-file"></a>Edytowanie pliku hosta

Ponieważ nie istnieje usługa Active Directory, nie ma możliwości uwierzytelniania połączeń systemu Windows. W związku z tym Przypisz zaufanie, edytując plik hosta za pomocą edytora tekstu. 

Aby edytować plik hosta, wykonaj następujące kroki:

1. Protokół RDP w maszynie wirtualnej. 
1. Użyj **Eksploratora plików** , aby przejść do `c:\windows\system32\drivers\etc` . 
1. Kliknij prawym przyciskiem myszy plik **hosts** i Otwórz plik za pomocą **Notatnika** (lub dowolnego innego edytora tekstu).
1. Na końcu pliku Dodaj wpis dla każdego węzła, grupy dostępności i odbiornika w postaci `IP Address, DNS Suffix #comment` podobnej do: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Dodawanie wpisów dla adresu IP, klastra i odbiornika do pliku hosta](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>Ustawianie uprawnień

Ponieważ nie ma Active Directory do zarządzania uprawnieniami, należy ręcznie zezwolić na tworzenie klastra na niewbudowanym koncie administratora lokalnego. 

Aby to zrobić, uruchom następujące polecenie cmdlet programu PowerShell w sesji administracyjnego programu PowerShell w każdym węźle: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Tworzenie klastra trybu failover

W tym kroku utworzysz klaster trybu failover. Jeśli nie znasz tych kroków, możesz je wykonać przy użyciu [samouczka klastra trybu failover](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

Istotne różnice między samouczkiem i co należy zrobić w przypadku klastra grupy roboczej:
- Usuń zaznaczenie pozycji **Magazyn** i **bezpośrednie miejsca do magazynowania** podczas wykonywania walidacji klastra. 
- Podczas dodawania węzłów do klastra Dodaj w pełni kwalifikowaną nazwę, na przykład:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Usuń zaznaczenie pola wyboru **Dodaj wszystkie odpowiednie magazyny do klastra**. 

Po utworzeniu klastra Przypisz statyczny adres IP klastra. W tym celu wykonaj następujące czynności:

1. Na jednym z węzłów Otwórz **Menedżer klastra trybu failover**, wybierz klaster, kliknij prawym przyciskiem myszy **nazwę: \<ClusterNam>** w obszarze **zasoby klastra podstawowe** , a następnie wybierz polecenie **Właściwości**. 

   ![Właściwości uruchamiania dla nazwy klastra](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. Wybierz adres IP w obszarze **adresy IP** , a następnie wybierz pozycję **Edytuj**. 
1. Wybierz pozycję **Użyj statyczne**, podaj adres IP klastra, a następnie wybierz przycisk **OK**: 

   ![Podaj statyczny adres IP dla klastra](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. Sprawdź, czy ustawienia wyglądają prawidłowo, a następnie wybierz przycisk **OK** , aby je zapisać:

   ![Weryfikowanie właściwości klastra](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Utwórz monitor w chmurze 

W tym kroku skonfigurujesz monitor udostępniania w chmurze. Jeśli nie znasz tych kroków, zobacz [wdrażanie monitora chmury dla klastra trybu failover](/windows-server/failover-clustering/deploy-cloud-witness). 

## <a name="enable-the-availability-group-feature"></a>Włącz funkcję grupy dostępności 

W tym kroku Włącz funkcję Grupa dostępności. Jeśli nie znasz kroków, zobacz [Samouczek dotyczący grupy dostępności](availability-group-manually-configure-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificates"></a>Tworzenie kluczy i certyfikatów

W tym kroku utworzysz certyfikaty używane przez logowania SQL na zaszyfrowanym punkcie końcowym. Utwórz folder w każdym węźle, w którym mają być przechowywane kopie zapasowe certyfikatów, na przykład `c:\certs` . 

Aby skonfigurować pierwszy węzeł, wykonaj następujące kroki: 

1. Otwórz **SQL Server Management Studio** i Połącz się z pierwszym węzłem, na przykład `AGNode1` . 
1. Otwórz **nowe okno zapytania** i uruchom następującą instrukcję Transact-SQL (T-SQL) po uaktualnieniu do złożonego i bezpiecznego hasła:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Następnie Utwórz punkt końcowy HADR Cluster i Użyj certyfikatu do uwierzytelniania przez uruchomienie tej instrukcji Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Użyj **Eksploratora plików** , aby przejść do lokalizacji pliku, w którym znajduje się certyfikat, na przykład `c:\certs` . 
1. Ręcznie Utwórz kopię certyfikatu, na przykład `AGNode1Cert.crt` z, z pierwszego węzła, i przenieś ją do tej samej lokalizacji w drugim węźle. 

Aby skonfigurować drugi węzeł, wykonaj następujące kroki: 

1. Połącz się z drugim węzłem za pomocą **SQL Server Management Studio**, takich jak `AGNode2` . 
1. W **nowym oknie zapytania** uruchom następującą instrukcję Transact-SQL (T-SQL) po uaktualnieniu do złożonego i bezpiecznego hasła: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Następnie Utwórz punkt końcowy HADR Cluster i Użyj certyfikatu do uwierzytelniania przez uruchomienie tej instrukcji Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Użyj **Eksploratora plików** , aby przejść do lokalizacji pliku, w którym znajduje się certyfikat, na przykład `c:\certs` . 
1. Ręcznie Utwórz kopię certyfikatu, taką jak `AGNode2Cert.crt` , z drugiego węzła, i przenieś ją do tej samej lokalizacji w pierwszym węźle. 

Jeśli klaster zawiera inne węzły, Powtórz te czynności również, modyfikując odpowiednie nazwy certyfikatów. 

## <a name="create-logins"></a>Tworzenie logowań

Uwierzytelnianie certyfikatu służy do synchronizowania danych między węzłami. Aby to umożliwić, należy utworzyć identyfikator logowania dla drugiego węzła, utworzyć użytkownika do logowania, utworzyć certyfikat dla logowania, aby użyć kopii zapasowej certyfikatu, a następnie udzielić połączenia w punkcie końcowym dublowania. 

Aby to zrobić, najpierw uruchom następujące zapytanie Transact-SQL (T-SQL) w pierwszym węźle, takie jak `AGNode1` : 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Następnie uruchom następujące zapytanie Transact-SQL (T-SQL) w drugim węźle, na przykład `AGNode2` : 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Jeśli klaster zawiera inne węzły, Powtórz te czynności również, modyfikując odpowiednie nazwy certyfikatów i użytkowników. 

## <a name="configure-an-availability-group"></a>Konfigurowanie grupy dostępności

W tym kroku skonfigurujesz grupę dostępności i dodasz do niej bazy danych. Nie twórz teraz odbiornika. Jeśli nie znasz kroków, zobacz [Samouczek dotyczący grupy dostępności](availability-group-manually-configure-tutorial.md#create-the-availability-group). Pamiętaj o zainicjowaniu trybu failover i powrotu po awarii, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. 

   > [!NOTE]
   > Jeśli wystąpi awaria podczas procesu synchronizacji, może być konieczne przyznanie `NT AUTHORITY\SYSTEM` praw sysadmin do tworzenia zasobów klastra w pierwszym węźle, na przykład `AGNode1` tymczasowo. 

## <a name="configure-a-load-balancer"></a>Konfigurowanie modułu równoważenia obciążenia

W tym ostatnim kroku Skonfiguruj moduł równoważenia obciążenia przy użyciu [Azure Portal](availability-group-load-balancer-portal-configure.md) lub [programu PowerShell](availability-group-listener-powershell-configure.md).


## <a name="next-steps"></a>Następne kroki

Aby skonfigurować grupę dostępności, można również użyć [polecenia AZ SQL VM CLI](./availability-group-az-commandline-configure.md) .