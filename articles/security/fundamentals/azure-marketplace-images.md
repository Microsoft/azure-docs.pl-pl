---
title: Zalecenia dotyczące zabezpieczeń obrazów portalu Azure Marketplace | Microsoft Docs
description: Ten artykuł zawiera zalecenia dotyczące obrazów uwzględnionych w miejscu na rynku
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 7c317a0b4fea0c981b227bace00c1b8924fd582c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89536386"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Zalecenia dotyczące zabezpieczeń obrazów portalu Azure Marketplace

Obraz musi spełniać te zalecenia dotyczące konfiguracji zabezpieczeń. Pozwala to zachować wysoki poziom zabezpieczeń obrazów rozwiązań partnerskich w portalu Azure Marketplace.

Przed przesłaniem obrazu należy zawsze uruchomić Wykrywanie luk w zabezpieczeniach. Jeśli wykryjesz lukę w zabezpieczeniach w publikowanym obrazie, musisz powiadomić klientów w odpowiednim czasie o lukę i sposobach jego naprawy.

## <a name="open-source-based-images"></a>Obrazy na podstawie otwartych źródeł

| Kategoria | Zaznacz |
| -------- | ----- |
| Zabezpieczenia                                                     | Zainstaluj wszystkie najnowsze poprawki zabezpieczeń dla dystrybucji systemu Linux.                                                                                                                                                                                                              |
| Zabezpieczenia                                                     | Postępuj zgodnie z wytycznymi branżowymi, aby zabezpieczyć obraz maszyny wirtualnej dla określonej dystrybucji systemu Linux.                                                                                                                                                                                     |
| Zabezpieczenia                                                     | Ogranicz obszar narażony na ataki, utrzymując minimalny wpływ na niezbędną rolę, funkcje, usługi i porty sieci systemu Windows Server.                                                                                                                                               |
| Zabezpieczenia                                                     | Skanuj kod źródłowy i otrzymany obraz maszyny wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                                                                                                                                   |
| Zabezpieczenia                                                     | Obraz VHD obejmuje tylko niepotrzebne zablokowane konta, które nie mają domyślnych haseł, które mogłyby zezwalać na logowanie interakcyjne. Brak tylnych drzwi.                                                                                                                                           |
| Zabezpieczenia                                                     | Wyłącz reguły zapory, chyba że aplikacja opiera się na nich, na przykład urządzenie zapory.                                                                                                                                                                             |
| Zabezpieczenia                                                     | Usuń wszystkie informacje poufne z obrazu wirtualnego dysku twardego, takie jak testowanie kluczy SSH, plików hostów i plików dzienników oraz niepotrzebnych certyfikatów.                                                                                                                                       |
| Zabezpieczenia                                                     | Unikaj używania LVM.                                                                                                                                                                                                                                            |
| Zabezpieczenia                                                     | Uwzględnij najnowsze wersje wymaganych bibliotek: </br> -OpenSSL v 1.0 lub nowszy </br> — Python 2,5 lub nowszy (zdecydowanie zalecane jest środowisko Python 2.6 +) </br> — Pakiet pyasn1 języka Python, jeśli nie został jeszcze zainstalowany </br> -d. OpenSSL v 1,0 lub nowszy                                                                |
| Zabezpieczenia                                                     | Wyczyść wpisy historii bash/Shell.                                                                                                                                                                                                                                             |
| Sieć                                                   | Domyślnie Dołącz serwer SSH. Ustaw opcję utrzymywanie aktywności SSH na sshd konfiguracji z następującą opcją: ClientAliveInterval 180.                                                                                                                                                        |
| Sieć                                                   | Usuń niestandardową konfigurację sieci z obrazu. Usuń plik resolv. conf: `rm /etc/resolv.conf` .                                                                                                                                                                                |
| Wdrożenie                                                   | Zainstaluj najnowszego agenta systemu Azure Linux.</br> -Zainstaluj przy użyciu pakietu RPM lub deb.  </br> — Można również użyć procesu instalacji ręcznej, ale pakiety Instalatora są zalecane i preferowane. </br> — Jeśli ręcznie Zainstaluj agenta z repozytorium GitHub, najpierw skopiuj `waagent` plik do `/usr/sbin` i uruchom (jako główny): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Plik konfiguracji agenta jest umieszczony pod adresem `/etc/waagent.conf` . |
| Wdrożenie                                                   | Upewnij się, że pomoc techniczna systemu Azure zapewnia naszym partnerom dane wyjściowe w konsoli szeregowej, jeśli jest to możliwe, i podaj odpowiedni limit czasu dla instalowania dysków systemu operacyjnego z magazynu Dodaj następujące parametry do wiersza rozruchu jądra obrazu: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` . |
| Wdrożenie                                                   | Brak partycji wymiany na dysku systemu operacyjnego. Można zażądać wymiany na lokalnym dysku zasobu przez agenta systemu Linux.         |
| Wdrożenie                                                   | Utwórz jedną partycję główną dla dysku systemu operacyjnego.      |
| Wdrożenie                                                   | 64-bitowy tylko system operacyjny.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Obrazy systemu Windows Server

| Kategoria | Zaznacz |
|--------- | ----- |
| Zabezpieczenia                                                         | Użyj bezpiecznego obrazu podstawowego systemu operacyjnego. Wirtualny dysk twardy używany do źródła dowolnego obrazu na podstawie systemu Windows Server musi pochodzić z obrazów systemu operacyjnego Windows Server dostarczonych za pośrednictwem Microsoft Azure. |
| Zabezpieczenia                                                         | Zainstaluj wszystkie najnowsze aktualizacje zabezpieczeń.                                                                                                                                     |
| Zabezpieczenia                                                         | Aplikacje nie powinny być zależne od nazw użytkowników z ograniczeniami, takich jak administrator, root lub administrator.                                                                |
| Zabezpieczenia                                                         | Włącz szyfrowanie dysków funkcją BitLocker zarówno dla dysków twardych systemu operacyjnego, jak i dysków twardych z danymi.                                                             |
| Zabezpieczenia                                                         | Ogranicz obszar narażony na ataki, utrzymując minimalny wpływ na tylko niezbędne role, funkcje, usługi i porty sieci systemu Windows Server.                         |
| Zabezpieczenia                                                         | Skanuj kod źródłowy i otrzymany obraz maszyny wirtualnej w poszukiwaniu złośliwego oprogramowania.                                                                                                                     |
| Zabezpieczenia                                                         | Ustaw aktualizację zabezpieczeń obrazów systemu Windows Server na autoaktualizację.                                                                                                                |
| Zabezpieczenia                                                         | Obraz VHD obejmuje tylko niepotrzebne zablokowane konta, które nie mają domyślnych haseł, które mogłyby zezwalać na logowanie interakcyjne. Brak tylnych drzwi.                             |
| Zabezpieczenia                                                         | Wyłącz reguły zapory, chyba że aplikacja opiera się na nich, na przykład urządzenie zapory.                                                               |
| Zabezpieczenia                                                         | Usuń wszystkie informacje poufne z obrazu VHD, w tym pliki hostów, pliki dzienników i niepotrzebne certyfikaty.                                              |
| Wdrożenie                                                       | 64-bitowy tylko system operacyjny.                            |

Nawet jeśli Twoja organizacja nie ma obrazów w portalu Azure Marketplace, rozważ sprawdzenie konfiguracji obrazów systemów Windows i Linux przed zaleceń.

