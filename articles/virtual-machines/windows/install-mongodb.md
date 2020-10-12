---
title: Instalowanie MongoDB na maszynie wirtualnej z systemem Windows na platformie Azure
description: Dowiedz się, jak zainstalować MongoDB na maszynie wirtualnej platformy Azure z systemem Windows Server 2012 R2 utworzonym przy użyciu modelu wdrażania Menedżer zasobów.
documentationcenter: ''
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: d02fa8fa23b587db06f3d2d1e08f0a8565471123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510423"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalowanie i Konfigurowanie MongoDB na maszynie wirtualnej z systemem Windows na platformie Azure
[MongoDB](https://www.mongodb.org) to popularna baza danych NoSQL typu open source o wysokiej wydajności. Ten artykuł przeprowadzi Cię przez proces instalowania i konfigurowania MongoDB na maszynie wirtualnej z systemem Windows Server 2016 na platformie Azure. Możesz również [zainstalować MongoDB na maszynie wirtualnej z systemem Linux na platformie Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed zainstalowaniem i skonfigurowaniem programu MongoDB należy utworzyć maszynę wirtualną i najlepiej dodać do niej dysk danych. Zapoznaj się z następującymi artykułami, aby utworzyć maszynę wirtualną i dodać dysk z danymi:

* Utwórz maszynę wirtualną z systemem Windows Server przy użyciu [Azure Portal](quick-create-portal.md) lub [Azure PowerShell](quick-create-powershell.md).
* Dołączanie dysku danych do maszyny wirtualnej z systemem Windows Server przy użyciu [Azure Portal](attach-managed-disk-portal.md) lub [Azure PowerShell](attach-disk-ps.md).

Aby rozpocząć instalowanie i Konfigurowanie MongoDB, [Zaloguj się do maszyny wirtualnej z systemem Windows Server](connect-logon.md) przy użyciu pulpit zdalny.

## <a name="install-mongodb"></a>Instalowanie bazy danych MongoDB
> [!IMPORTANT]
> Funkcje zabezpieczeń MongoDB, takie jak uwierzytelnianie i powiązania adresów IP, nie są domyślnie włączone. Funkcje zabezpieczeń należy włączyć przed wdrożeniem MongoDB w środowisku produkcyjnym. Aby uzyskać więcej informacji, zobacz [MongoDB Security and Authentication](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Po nawiązaniu połączenia z maszyną wirtualną przy użyciu Pulpit zdalny Otwórz program Internet Explorer z paska zadań.
2. Wybierz opcję **Użyj zalecanych zabezpieczeń, ochrony prywatności i ustawień zgodności** podczas pierwszego otwarcia programu Internet Explorer, a następnie kliknij przycisk **OK**.
3. Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest domyślnie włączona. Dodaj witrynę sieci Web MongoDB do listy dozwolonych witryn:

   * Wybierz ikonę **Narzędzia** w prawym górnym rogu.
   * W **Opcje internetowe**, wybierz kartę **zabezpieczenia** , a następnie wybierz ikonę **Zaufane witryny** .
   * Kliknij przycisk **Lokacje** . Dodaj *https:// \* . MongoDB.com* do listy zaufanych witryn, a następnie zamknij okno dialogowe.

     ![Konfigurowanie ustawień zabezpieczeń programu Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Przejdź do strony [MongoDB — pliki do pobrania](https://www.mongodb.com/downloads) ( https://www.mongodb.com/downloads) .
5. W razie potrzeby wybierz wersję **serwer Community** , a następnie wybierz najnowszą bieżącą, stabilną wersję dla*systemu Windows Server 2008 R2 64-bit i nowszego*. Aby pobrać Instalatora, kliknij pozycję **Pobierz (msi)**.

    ![Pobierz instalatora MongoDB](./media/install-mongodb/download-mongodb.png)

    Po zakończeniu pobierania uruchom Instalatora.
6. Przeczytaj i zaakceptuj umowę licencyjną. Po wyświetleniu monitu wybierz pozycję **Ukończ** instalację.
7. W razie potrzeby można również zainstalować kompas, interfejs graficzny dla MongoDB.
8. Na ostatnim ekranie kliknij przycisk **Instaluj**.

## <a name="configure-the-vm-and-mongodb"></a>Skonfiguruj maszynę wirtualną i MongoDB
1. Zmienne ścieżki nie są aktualizowane przez Instalatora MongoDB. Bez lokalizacji MongoDB `bin` w zmiennej PATH należy określić pełną ścieżkę za każdym razem, gdy używasz pliku wykonywalnego MongoDB. Aby dodać lokalizację do zmiennej PATH:

   * Kliknij prawym przyciskiem myszy menu **Start** , a następnie wybierz pozycję **system**.
   * Kliknij pozycję **Zaawansowane ustawienia systemu**, a następnie kliknij pozycję **zmienne środowiskowe**.
   * W obszarze **zmienne systemowe**wybierz pozycję **ścieżka**, a następnie kliknij przycisk **Edytuj**.

     ![Konfiguruj zmienne ścieżki](./media/install-mongodb/configure-path-variables.png)

     Dodaj ścieżkę do `bin` folderu MongoDB. MongoDB jest zazwyczaj instalowany w *katalogu C:\Program Files\MongoDB*. Sprawdź ścieżkę instalacji na maszynie wirtualnej. Poniższy przykład dodaje domyślną lokalizację instalacji MongoDB do `PATH` zmiennej:

     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```

     > [!NOTE]
     > Pamiętaj, aby dodać wiodący średnik ( `;` ), aby wskazać, że dodajesz lokalizację do `PATH` zmiennej.

2. Utwórz MongoDB danych i katalogów dzienników na dysku z danymi. Z menu **Start** wybierz **polecenie Wiersz polecenia**. Poniższe przykłady tworzą katalogi na dysku F:

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Uruchom wystąpienie MongoDB za pomocą następującego polecenia, dostosowując ścieżkę do danych i katalogów dzienników odpowiednio:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    Przydzielenie plików dziennika przez MongoDB może potrwać kilka minut i rozpocząć nasłuchiwanie połączeń. Wszystkie komunikaty dziennika są kierowane do pliku *F:\MongoLogs\mongolog.log* , gdy `mongod.exe` serwer zostanie uruchomiony i przydzieli pliki dziennika.

   > [!NOTE]
   > Wiersz polecenia pozostaje skoncentrowany na tym zadaniu, gdy wystąpienie MongoDB jest uruchomione. Pozostaw otwarte okno wiersza polecenia, aby kontynuować działanie MongoDB. Lub zainstaluj MongoDB jako usługę, jak opisano w następnym kroku.

4. Aby uzyskać bardziej niezawodne środowisko MongoDB, zainstaluj je `mongod.exe` jako usługę. Tworzenie usługi oznacza, że nie musisz opuszczać wiersza polecenia uruchamianego za każdym razem, gdy chcesz użyć MongoDB. Utwórz usługę w następujący sposób, dostosowując ścieżkę do danych i katalogów dzienników odpowiednio:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```

    Poprzednie polecenie tworzy usługę o nazwie MongoDB, z opisem "Mongo DB". Określono również następujące parametry:

   * `--dbpath`Opcja określa lokalizację katalogu danych.
   * `--logpath`Opcja musi być użyta do określenia pliku dziennika, ponieważ uruchomiona usługa nie ma okna polecenia, aby wyświetlić dane wyjściowe.
   * `--logappend`Opcja określa, że ponowne uruchomienie usługi powoduje dołączenie danych wyjściowych do istniejącego pliku dziennika.

   Aby uruchomić usługę MongoDB, uruchom następujące polecenie:

    ```
    net start MongoDB
    ```

    Aby uzyskać więcej informacji na temat tworzenia usługi MongoDB, zobacz [Konfigurowanie usługi systemu Windows dla MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testowanie wystąpienia MongoDB
Dzięki MongoDB uruchomionemu jako pojedynczemu wystąpieniu lub zainstalowanemu jako usługa możesz teraz zacząć tworzyć i korzystać z baz danych. Aby uruchomić powłokę administracyjną MongoDB, Otwórz inne okno wiersza polecenia z menu **Start** , a następnie wprowadź następujące polecenie:

```
mongo
```

Możesz wyświetlić listę baz danych za pomocą `db` polecenia. Wstaw dane w następujący sposób:

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

## <a name="configure-firewall-and-network-security-group-rules"></a>Konfigurowanie reguł zapory i sieciowych grup zabezpieczeń
Teraz, gdy MongoDB jest zainstalowana i uruchomiona, otwórz port w zaporze systemu Windows, aby umożliwić zdalne nawiązywanie połączenia z MongoDB. Aby utworzyć nową regułę ruchu przychodzącego zezwalającą na port TCP 27017, Otwórz wiersz administracyjny programu PowerShell i wprowadź następujące polecenie:

```powershell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Regułę można także utworzyć przy użyciu narzędzia do zarządzania w trybie graficznym **zapory systemu Windows z zabezpieczeniami zaawansowanymi** . Utwórz nową regułę ruchu przychodzącego zezwalającą na port TCP 27017.

W razie potrzeby utwórz regułę sieciowej grupy zabezpieczeń, aby zezwolić na dostęp do MongoDB spoza istniejącej podsieci sieci wirtualnej platformy Azure. Reguły sieciowej grupy zabezpieczeń można utworzyć przy użyciu [Azure Portal](nsg-quickstart-portal.md) lub [Azure PowerShell](nsg-quickstart-powershell.md). Podobnie jak w przypadku reguł zapory systemu Windows, Zezwól na port TCP 27017 do interfejsu sieci wirtualnej maszyny wirtualnej MongoDB.

> [!NOTE]
> Port TCP 27017 jest domyślnym portem używanym przez MongoDB. Można zmienić ten port przy użyciu `--port` parametru podczas `mongod.exe` ręcznego uruchamiania lub z usługi. Jeśli zmienisz port, pamiętaj o zaktualizowaniu zasad zapory systemu Windows i sieciowych grup zabezpieczeń w powyższych krokach.


## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób instalowania i konfigurowania MongoDB na maszynie wirtualnej z systemem Windows. Teraz możesz uzyskiwać dostęp do MongoDB na maszynie wirtualnej z systemem Windows, wykonując instrukcje zaawansowane w [dokumentacji MongoDB](https://docs.mongodb.com/manual/).

