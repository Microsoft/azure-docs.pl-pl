---
title: Bezpieczny rozruch oprogramowania układowego — zabezpieczenia platformy Azure
description: Techniczne Omówienie bezpiecznego rozruchu oprogramowania układowego platformy Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557847"
---
# <a name="secure-boot"></a>Bezpieczny rozruch

Bezpieczny rozruch to funkcja [Unified Extensible Firmware Interface](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI), która wymaga weryfikacji wszystkich oprogramowania układowego niskiego poziomu i składników oprogramowania przed załadowaniem. Podczas rozruchu, bezpieczny rozruch w interfejsie UEFI sprawdza podpis każdego z części oprogramowania rozruchowego, w tym sterowników oprogramowania układowego UEFI (nazywanych również opcjami ROM), aplikacji Extensible Firmware Interface (EFI) oraz sterowników i plików binarnych systemu operacyjnego. Jeśli podpisy są prawidłowe lub zaufane przez producenta oryginalnego sprzętu (OEM), komputer uruchomi się i oprogramowanie układowe zapewnia kontrolę nad systemem operacyjnym.

## <a name="components-and-process"></a>Składniki i proces

Bezpieczny rozruch opiera się na następujących kluczowych składnikach:

- Klucz platformy (PK) — ustanawia zaufanie między właścicielem platformy (Microsoft) i oprogramowaniem układowym. Publiczna połowa to PKpub, a druga połowa to PKpriv.
- Baza danych klucza rejestracji klucza (KEK) — ustanawia zaufanie między systemem operacyjnym a oprogramowaniem układowym platformy. Publiczna połowa to KEKpub, a druga połowa to KEKpriv.
- Baza danych sygnatur (DB) — zawiera skróty dla zaufanych podpisywania (klucze publiczne i certyfikaty) modułów oprogramowania układowego i kodu oprogramowania autoryzowanych do współpracy z oprogramowaniem układowym platformy.
- Baza danych odwołanych sygnatur (DBX) — zawiera odwołane skróty modułów kodu, które zostały zidentyfikowane jako złośliwe, zagrożone, naruszone lub niezaufane. Jeśli skrót znajduje się w bazie danych sygnatury i w bazie danych o odwołanych sygnaturach, baza, której dotyczy odwołanie, pobiera poprzedniki.

Poniższy rysunek i proces wyjaśniają, jak te składniki są aktualizowane:

![Diagram przedstawiający składniki bezpiecznego rozruchu.](./media/secure-boot/secure-boot.png)

Producent OEM przechowuje skróty bezpiecznego rozruchu na komputerze nieulotnej pamięci RAM (NV-RAM) w momencie produkcji.

1. Baza danych podpisów (DB) jest zapełniona sygnaturami lub skrótami obrazu aplikacji UEFI, modułami ładującymi systemu operacyjnego (takimi jak system operacyjny Microsoft lub Menedżer rozruchu) oraz zaufanymi sterownikami UEFI.
2. Baza danych odwołanych podpisów (DBX) jest wypełniana skrótami modułów, które nie są już zaufane.
3. Baza danych klucza rejestracji klucza (KEK) jest wypełniona przy użyciu kluczy podpisywania, których można użyć do zaktualizowania bazy danych sygnatur i bazy danych odrzuconych podpisów. Bazy danych można edytować za pomocą aktualizacji, które są podpisane przy użyciu poprawnego klucza, lub za pomocą aktualizacji, które są autoryzowane przez autoryzowanego użytkownika w menu oprogramowania układowego.
4. Po dodaniu baz danych bazy danych, DBX i KEK, a końcowe sprawdzanie poprawności oprogramowania układowego i testowanie zostało zakończone, producent OEM blokuje edycję oprogramowania układowego i generuje klucz platformy (PK). Klucz podstawowy może służyć do podpisywania aktualizacji KEK lub do wyłączenia bezpiecznego rozruchu.

Na każdym etapie procesu rozruchu podsumowania oprogramowania układowego, programu inicjującego, systemu operacyjnego, sterowników jądra i innych artefaktów łańcucha rozruchowego są obliczane i porównywane z akceptowalnymi wartościami. Nie można załadować oprogramowania układowego i oprogramowania wykrytego jako niezaufane. W ten sposób można zablokować ataki złośliwego oprogramowania lub przed uruchomieniem złośliwego oprogramowania.

## <a name="secure-boot-on-the-azure-fleet"></a>Bezpieczny rozruch w ramach floty platformy Azure
Dzisiaj wszystkie maszyny, które zostały dołączone i wdrożone w ramach floty obliczeniowej platformy Azure do obsługi obciążeń klientów, pochodzą z podłóg fabryk z włączonym bezpiecznym rozruchem. Odpowiednie narzędzia i procesy są stosowane na każdym etapie potoku buildout i integracji sprzętu, aby upewnić się, że Włączanie bezpiecznego rozruchu nie zostało cofnięte ani przez złośliwe przeznaczenie.

Weryfikowanie, czy skróty bazy danych i DBX są poprawne, zapewnia:

- Program inicjujący znajduje się w jednej z wpisów bazy danych
- Podpis programu inicjującego jest prawidłowy
- Rozruch hosta za pomocą zaufanego oprogramowania

 Sprawdzając sygnatury KEKpub i PKpub, możemy potwierdzić, że tylko zaufane strony mają uprawnienia do modyfikowania definicji oprogramowania uznawanego za zaufanego. Wreszcie dzięki zapewnieniu, że bezpieczny rozruch jest aktywny, możemy sprawdzić, czy te definicje są wymuszane.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tego, co robimy, aby zwiększyć integralność i bezpieczeństwo platformy, zobacz:

- [Zabezpieczenia oprogramowania układowego](firmware.md)
- [Mierzony zaświadczanie rozruchu i hosta](measured-boot-host-attestation.md)
- [Projekt Cerberus](project-cerberus.md)
- [Szyfrowanie danych magazynowanych](encryption-atrest.md)
- [Zabezpieczenia funkcji hypervisor](hypervisor.md)