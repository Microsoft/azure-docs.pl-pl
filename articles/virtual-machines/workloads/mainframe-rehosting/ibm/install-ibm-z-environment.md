---
title: Instalowanie środowiska IBM zD&w środowisku deweloperskim/testowym na platformie Azure | Microsoft Docs
description: Wdróż środowisko deweloperskie i testowe programu IBM Z (zD&T) na platformie Azure Virtual Machines (VM) jako usługa (IaaS).
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 32b63256f89d6d051305890c3387140f243e1a70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954330"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Zainstaluj środowisko IBM zD&w środowisku deweloperskim/testowym na platformie Azure

Aby utworzyć środowisko deweloperskie/testowe dla obciążeń oprogramowania mainframe w systemach IBM Z, można wdrożyć środowisko deweloperskie i testowe (zD&T) na maszynie wirtualnej platformy Azure (IaaS).

Dzięki zD&T możesz skorzystać z oszczędności kosztów platformy x86 w mniej krytycznych środowiskach deweloperskich i testowych, a następnie wypchnąć aktualizacje z powrotem do środowiska produkcyjnego systemu Z. Aby uzyskać więcej informacji, zobacz [instrukcje dotyczące instalacji programu IBM ZD&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Platforma Azure i usługa Azure Stack obsługują następujące wersje:

- zD&T Personal Edition
- zD&T Parallel Sysplex
- zD&T Enterprise Edition

Wszystkie wersje programu zD&T działają tylko w systemach x86 z systemem Linux, a nie na serwerze Windows Server. Wersja Enterprise Edition jest obsługiwana w ramach obu Red Hat Enterprise Linux (RHEL) lub Ubuntu/Debian. Na platformie Azure są dostępne zarówno obrazy maszyn wirtualnych RHEL, jak i debian.

W tym artykule opisano, jak skonfigurować program zD&T Enterprise Edition na platformie Azure, aby można było tworzyć środowiska i zarządzać nimi za pomocą serwera zD&T Enterprise Edition sieci Web. Instalowanie zD&T nie instaluje żadnych środowisk. Należy je utworzyć oddzielnie jako pakiety instalacyjne. Na przykład dystrybucje kontrolowane przez deweloperów aplikacji (ADCD) są obrazami ilości środowiska testowego. Są one zawarte w obrazach zip dystrybucji multimediów dostępnych z firmy IBM. Zobacz, jak [skonfigurować środowisko ADCD na platformie Azure](demo.md).

Aby uzyskać więcej informacji, zobacz [Omówienie usługi zD&T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) w centrum wiedzy firmy IBM.

W tym artykule opisano sposób konfigurowania środowiska deweloperskiego i testowego (zD&T) Enterprise Edition na platformie Azure. Następnie można użyć serwera sieci Web zD&T Enterprise Edition do tworzenia środowisk opartych na platformie Azure i zarządzania nimi.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> IBM zezwala na zainstalowanie zD&T Enterprise Edition tylko w środowiskach deweloperskich i testowych — a *nie* w środowiskach produkcyjnych.

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Wymagany jest dostęp do nośnika, który jest dostępny tylko dla klientów i partnerów firmy IBM. Aby uzyskać więcej informacji, skontaktuj się z przedstawicielem firmy IBM lub zapoznaj się z informacjami kontaktowymi w witrynie sieci Web [zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) .

- [Serwer licencjonowania](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Jest to wymagane w celu uzyskania dostępu do środowisk. Sposób tworzenia go zależy od tego, jak Licencjobiorca ma licencję na oprogramowanie firmy IBM:

     - **Serwer licencjonowania oparty na sprzęcie** wymaga urządzenia sprzętowego USB, które zawiera wymierne tokeny niezbędne do uzyskania dostępu do wszystkich części oprogramowania. Musisz uzyskać ten dostęp z firmy IBM.

     - **Serwer licencjonowania oparty na oprogramowaniu** wymaga skonfigurowania scentralizowanego serwera do zarządzania kluczami licencjonowania. Ta metoda jest preferowana i wymaga skonfigurowania kluczy otrzymywanych od firmy IBM na serwerze zarządzania.

## <a name="create-the-base-image-and-connect"></a>Tworzenie obrazu podstawowego i nawiązywanie połączenia

1. W Azure Portal [Utwórz maszynę wirtualną](../../../linux/quick-create-portal.md) z żądaną konfiguracją systemu operacyjnego. W tym artykule przyjęto założenie, że maszyna wirtualna B4ms (z 4 procesorów wirtualnych vCPU i 16 GB pamięci) uruchomiła system Ubuntu 16,04.

2. Po utworzeniu maszyny wirtualnej Otwórz porty przychodzące 22 dla SSH, 21 dla FTP i 9443 dla serwera sieci Web.

3. Uzyskaj poświadczenia SSH wyświetlane w bloku **Przegląd** maszyny wirtualnej za pomocą przycisku **Połącz** . Wybierz kartę **SSH** i skopiuj polecenie SSH Logon do Schowka.

4. Zaloguj się do [powłoki bash](../../../../cloud-shell/quickstart.md) z lokalnego komputera i Wklej polecenie. Będzie on w formacie **SSH \<user id\> \@ \<IP Address\>**. Gdy zostanie wyświetlony monit o podanie poświadczeń, wprowadź je w celu nawiązania połączenia z katalogiem macierzystym.

## <a name="copy-the-installation-file-to-the-server"></a>Kopiuj plik instalacyjny na serwer

Plik instalacyjny dla serwera sieci Web to **ZDT \_ instalacji \_ EE \_ v 12.0.0.1. tgz**. Jest ona uwzględniona na nośniku dostarczonym przez firmę IBM. Musisz przekazać ten plik na maszynę wirtualną Ubuntu.

1. W wierszu polecenia wprowadź następujące polecenie, aby upewnić się, że wszystko jest aktualne w nowo utworzonym obrazie:

    ```
    sudo apt-get update
    ```

2. Utwórz katalog do zainstalowania:

    ```
    mkdir ZDT
    ```

3. Skopiuj plik z komputera lokalnego do maszyny wirtualnej:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> To polecenie kopiuje plik instalacyjny do katalogu ZDT w katalogu macierzystym, co różni się w zależności od tego, czy klient uruchamia system Windows lub Linux.

## <a name="install-the-enterprise-edition"></a>Zainstaluj wersję Enterprise Edition

1. Przejdź do katalogu ZDT i zdekompresuj \_ plik ZDT zainstaluj \_ EE \_ v 12.0.0.1. tgz przy użyciu następujących poleceń:

    ```
    cd ZDT
    tar zxvf ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Uruchom Instalatora:

    ```
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.x86_64
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Wybierz pozycję **1** , aby zainstalować serwer przedsiębiorstwa.

4. Naciśnij **uważnie** i Przeczytaj umowy licencyjne. Na końcu licencji wprowadź **wartość tak** , aby wykonać operację.

5. Po wyświetleniu monitu o zmianę hasła dla nowo utworzonego użytkownika **ibmsys1** Użyj polecenia **sudo passwd ibmsys1** i wprowadź nowe hasło.

6. Aby sprawdzić, czy instalacja zakończyła się pomyślnie

    ```
    dpkg -l | grep zdtapp
    ```

7. Sprawdź, czy dane wyjściowe zawierają ciąg **zdtapp 12.0.0.0**, wskazujący, że gaz pakietu został pomyślnie zainstalowany

### <a name="starting-enterprise-edition"></a>Uruchamianie wersji Enterprise Edition

Należy pamiętać, że po uruchomieniu serwera sieci Web jest on uruchamiany w ramach zD&T, który został utworzony podczas procesu instalacji.

1. Aby uruchomić serwer sieci Web, użyj identyfikatora użytkownika root, aby uruchomić następujące polecenie:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Skopiuj adres URL danych wyjściowych przez skrypt, który wygląda następująco:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Wklej adres URL do przeglądarki sieci Web, aby otworzyć składnik zarządzania dla zD&T instalacji.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie dystrybucji kontrolowanej przez deweloperów aplikacji (ADCD) w programie IBM zD&T v1](./demo.md)
