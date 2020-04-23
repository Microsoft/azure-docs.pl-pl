---
title: Instalowanie mongodb na maszynie Wirtualnej systemu Windows na platformie Azure
description: Dowiedz się, jak zainstalować mongodb na maszynie Wirtualnej platformy Azure z systemem Windows Server 2012 R2 utworzonym za pomocą modelu wdrażania Menedżera zasobów.
documentationcenter: ''
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: a5ba7d7fce3f3eabd223956ca8d9cc824fbd0c5f
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869447"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalowanie i konfigurowanie usługi MongoDB na maszynie Wirtualnej systemu Windows na platformie Azure
[MongoDB](https://www.mongodb.org) jest popularną bazą danych NoSQL typu open source o wysokiej wydajności. W tym artykule przewodnik po zainstalowaniu i skonfigurowaniu usługi MongoDB na maszynie wirtualnej systemu Windows Server 2016 na platformie Azure. MongoDB można również [zainstalować na maszynie Wirtualnej z systemem Linux na platformie Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed zainstalowaniem i skonfigurowaniem mongodb, należy utworzyć maszynę wirtualną i, najlepiej, dodać dysk danych do niego. Zobacz następujące artykuły, aby utworzyć maszynę wirtualną i dodać dysk danych:

* Utwórz maszynę wirtualną systemu Windows Server przy użyciu [portalu Azure](quick-create-portal.md) lub [programu Azure PowerShell](quick-create-powershell.md).
* Dołącz dysk danych do maszyny Wirtualnej systemu Windows Server przy użyciu [portalu Azure lub](attach-managed-disk-portal.md) programu Azure [PowerShell](attach-disk-ps.md).

Aby rozpocząć instalowanie i konfigurowanie usługi MongoDB, [zaloguj się do maszyny Wirtualnej systemu Windows Server](connect-logon.md) przy użyciu pulpitu zdalnego.

## <a name="install-mongodb"></a>Instalowanie bazy danych MongoDB
> [!IMPORTANT]
> Funkcje zabezpieczeń MongoDB, takie jak uwierzytelnianie i powiązanie adresów IP, nie są domyślnie włączone. Funkcje zabezpieczeń powinny być włączone przed wdrożeniem mongodb w środowisku produkcyjnym. Aby uzyskać więcej informacji, zobacz [MongoDB Security and Authentication](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Po nawiązaniu połączenia z maszyną wirtualną za pomocą pulpitu zdalnego otwórz program Internet Explorer z paska zadań.
2. **Wybierz pozycję Użyj zalecanych ustawień zabezpieczeń, prywatności i zgodności** podczas pierwszego otwarcia programu Internet Explorer, a następnie kliknij przycisk **OK**.
3. Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest domyślnie włączona. Dodaj witrynę MongoDB do listy dozwolonych witryn:
   
   * Wybierz ikonę **Narzędzia** w prawym górnym rogu.
   * W **obszarze Opcje internetowe**wybierz kartę **Zabezpieczenia,** a następnie wybierz ikonę **Zaufane witryny.**
   * Kliknij przycisk **Witryny.** Dodaj *https://\*mongodb.com* do listy zaufanych witryn, a następnie zamknij okno dialogowe.
     
     ![Konfigurowanie ustawień zabezpieczeń programu Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Przejdź do strony [MongoDB -](https://www.mongodb.com/downloads) https://www.mongodb.com/downloads)Pliki do pobrania ( .
5. W razie potrzeby wybierz wersję **Community Server,** a następnie wybierz najnowszą bieżącą wersję stabilną dla*64-bitowego systemu Windows Server 2008 R2 lub nowszych*. Aby pobrać instalatora, kliknij przycisk **POBIERZ (msi)**.
   
    ![Pobierz instalator MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Uruchom instalator po zakończeniu pobierania.
6. Przeczytaj i zaakceptuj umowę licencyjną. Po wyświetleniu monitu wybierz pozycję **Zakończ** instalację.
7. W razie potrzeby, można również zainstalować Compass, graficzny interfejs dla MongoDB.
8. Na ostatnim ekranie kliknij pozycję **Zainstaluj**.

## <a name="configure-the-vm-and-mongodb"></a>Konfigurowanie maszyny Wirtualnej i mongodb
1. Zmienne ścieżki nie są aktualizowane przez instalatora MongoDB. Bez lokalizacji MongoDB `bin` w zmiennej ścieżki, należy określić pełną ścieżkę za każdym razem, gdy używasz pliku wykonywalnego MongoDB. Aby dodać lokalizację do zmiennej ścieżki:
   
   * Kliknij prawym przyciskiem myszy menu **Start,** a następnie wybierz polecenie **System**.
   * Kliknij **pozycję Zaawansowane ustawienia systemowe**, a następnie kliknij pozycję **Zmienne środowiskowe**.
   * W obszarze **Zmienne systemowe**wybierz pozycję **Ścieżka**, a następnie kliknij pozycję **Edytuj**.
     
     ![Konfigurowanie zmiennych PATH](./media/install-mongodb/configure-path-variables.png)
     
     Dodaj ścieżkę do folderu MongoDB. `bin` MongoDB jest zazwyczaj instalowany w *C:\Program Files\MongoDB*. Sprawdź ścieżkę instalacji na maszynie Wirtualnej. W poniższym przykładzie dodano domyślną `PATH` lokalizację instalacji bazy danych MongoDB do zmiennej:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Pamiętaj, aby dodać średnik`;`wiodący ( ), aby wskazać, `PATH` że dodajesz lokalizację do zmiennej.

2. Tworzenie katalogów danych i dzienników MongoDB na dysku danych. Z menu **Start** wybierz polecenie **Wiersz polecenia**. Poniższe przykłady tworzą katalogi na dysku F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Uruchom wystąpienie bazy danych MongoDB za pomocą następującego polecenia, odpowiednio dostosowując ścieżkę do katalogów danych i dzienników:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Może upłynąć kilka minut, aby MongoDB przydzielić pliki dziennika i rozpocząć nasłuchiwanie połączeń. Wszystkie komunikaty dziennika są kierowane do pliku *F:\MongoLogs\mongolog.log* w miarę `mongod.exe` uruchamiania serwera i przydzielania plików dziennika.
   
   > [!NOTE]
   > Wiersz polecenia pozostaje skoncentrowany na tym zadaniu, gdy twoje wystąpienie MongoDB jest uruchomione. Pozostaw otwarte okno wiersza polecenia, aby kontynuować uruchamianie bazy danych MongoDB. Lub zainstaluj MongoDB jako usługę, jak opisano w następnym kroku.

4. Aby uzyskać bardziej niezawodne środowisko `mongod.exe` MongoDB, zainstaluj jako usługę. Tworzenie usługi oznacza, że nie trzeba pozostawiać wiersza polecenia uruchomionego za każdym razem, gdy chcesz użyć MongoDB. Utwórz usługę w następujący sposób, odpowiednio dostosowując ścieżkę do katalogów danych i dzienników:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Poprzednie polecenie tworzy usługę o nazwie MongoDB, z opisem "Mongo DB". Określono również następujące parametry:
   
   * Opcja `--dbpath` określa lokalizację katalogu danych.
   * Opcja `--logpath` musi służyć do określenia pliku dziennika, ponieważ uruchomiona usługa nie ma okna polecenia do wyświetlania danych wyjściowych.
   * Opcja `--logappend` określa, że ponowne uruchomienie usługi powoduje, że dane wyjściowe dołączyć do istniejącego pliku dziennika.
   
   Aby uruchomić usługę MongoDB, uruchom następujące polecenie:
   
    ```
    net start MongoDB
    ```
   
    Aby uzyskać więcej informacji na temat tworzenia usługi MongoDB, zobacz [Konfigurowanie usługi systemu Windows dla mongodb](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testowanie instancji MongoDB
Z MongoDB działa jako pojedyncze wystąpienie lub zainstalowane jako usługa, można teraz rozpocząć tworzenie i korzystanie z baz danych. Aby uruchomić powłokę administracyjną MongoDB, otwórz inne okno wiersza polecenia z menu **Start** i wprowadź następujące polecenie:

```
mongo  
```

Za pomocą `db` polecenia można wyświetlić listę baz danych. Wstaw niektóre dane w następujący sposób:

```
db.foo.insert( { a : 1 } )
```

Wyszukaj dane w następujący sposób:

```
db.foo.find()
```

Dane wyjściowe są podobne do poniższego przykładu:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Zamknij `mongo` konsolę w następujący sposób:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurowanie reguł zapory i sieciowej grupy zabezpieczeń
Teraz, gdy MongoDB jest zainstalowany i uruchomiony, otwórz port w Zaporze systemu Windows, dzięki czemu można zdalnie połączyć się z MongoDB. Aby utworzyć nową regułę przychodzącą zezwalającą na port TCP 27017, otwórz administracyjny monit programu PowerShell i wprowadź następujące polecenie:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Regułę można również utworzyć za pomocą narzędzia do zarządzania graficznego **Zapora systemu Windows z zabezpieczeniami zaawansowanymi.** Utwórz nową regułę przychodzącą, aby zezwolić na port TCP 27017.

W razie potrzeby utwórz regułę sieciowej grupy zabezpieczeń, aby zezwolić na dostęp do usługi MongoDB spoza istniejącej podsieci sieci wirtualnej platformy Azure. Reguły sieciowej grupy zabezpieczeń można utworzyć przy użyciu [witryny Azure portal](nsg-quickstart-portal.md) lub [programu Azure PowerShell](nsg-quickstart-powershell.md). Podobnie jak w przypadku reguł Zapory systemu Windows, zezwalaj na port TCP 27017 na interfejs sieci wirtualnej maszyny Wirtualnej MongoDB.

> [!NOTE]
> Port TCP 27017 jest portem domyślnym używanym przez MongoDB. Ten port można zmienić `--port` przy `mongod.exe` użyciu parametru podczas ręcznego uruchamiania lub z usługi. Jeśli zmienisz port, upewnij się, że w poprzednich krokach zaktualizowano reguły Zapory systemu Windows i sieciowej grupy zabezpieczeń.


## <a name="next-steps"></a>Następne kroki
W tym samouczku dowiesz się, jak zainstalować i skonfigurować mongodb na maszynie Wirtualnej systemu Windows. Teraz możesz uzyskać dostęp do mongodb na maszynie Wirtualnej systemu Windows, postępujących zgodnie z zaawansowanymi tematami w [dokumentacji MongoDB](https://docs.mongodb.com/manual/).

