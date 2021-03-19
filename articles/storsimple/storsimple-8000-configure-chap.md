---
title: Konfigurowanie protokołu CHAP dla urządzenia z serii StorSimple 8000 | Microsoft Docs
description: Opisuje sposób konfigurowania protokołu uwierzytelniania typu Challenge Handshake (CHAP) na urządzeniu StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 694d63dacedd8ffcd075e73bb91b5ab0a089b58c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91766500"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurowanie protokołu CHAP dla urządzenia StorSimple

W tym samouczku wyjaśniono, jak skonfigurować protokół CHAP dla urządzenia StorSimple. Procedura opisana w tym artykule dotyczy urządzeń z serii StorSimple 8000.

Protokół CHAP oznacza dla protokołu uwierzytelniania typu Challenge Handshake. Jest to schemat uwierzytelniania używany przez serwery do weryfikowania tożsamości klientów zdalnych. Weryfikacja jest oparta na udostępnionym haśle lub kluczu tajnym. Protokół CHAP może być jednym ze sposobów (jednokierunkowe) lub wzajemnie (dwukierunkowe). Protokół CHAP jest jedynym sposobem, gdy element docelowy uwierzytelnia inicjatora. W ramach wzajemnego lub odwrotnego uwierzytelniania CHAP obiekt docelowy uwierzytelnia inicjatora, a następnie inicjator uwierzytelnia element docelowy. Uwierzytelnianie inicjatora można zaimplementować bez uwierzytelniania docelowego. Uwierzytelnianie docelowe można jednak zaimplementować tylko wtedy, gdy jest również zaimplementowane uwierzytelnianie inicjatora.

Najlepszym rozwiązaniem jest użycie protokołu CHAP w celu zwiększenia bezpieczeństwa iSCSI.

> [!NOTE]
> Należy pamiętać, że protokół IPSEC nie jest obecnie obsługiwany na urządzeniach StorSimple.

Ustawienia protokołu CHAP na urządzeniu StorSimple można skonfigurować w następujący sposób:

* Uwierzytelnianie jednokierunkowe lub jednokierunkowe
* Uwierzytelnianie dwukierunkowe lub wzajemne lub odwrotne

W każdym z tych przypadków Portal urządzenia i oprogramowania inicjatora iSCSI serwera muszą zostać skonfigurowane. Szczegółowe instrukcje dotyczące tej konfiguracji zostały opisane w poniższym samouczku.

## <a name="unidirectional-or-one-way-authentication"></a>Uwierzytelnianie jednokierunkowe lub jednokierunkowe

W przypadku uwierzytelniania jednokierunkowego element docelowy uwierzytelnia inicjatora. To uwierzytelnianie wymaga skonfigurowania ustawień inicjatora protokołu CHAP na urządzeniu StorSimple i oprogramowaniu inicjatora iSCSI na hoście. Szczegółowe procedury dotyczące urządzenia StorSimple i hosta systemu Windows zostały opisane poniżej.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Aby skonfigurować urządzenie na potrzeby uwierzytelniania jednokierunkowego

1. W Azure Portal przejdź do usługi StorSimple Device Manager. Kliknij pozycję **urządzenia** i wybierz i kliknij urządzenie, dla którego chcesz skonfigurować protokół CHAP. Przejdź do pozycji **Ustawienia urządzenia > zabezpieczenia**. W bloku **Ustawienia zabezpieczeń** kliknij pozycję **CHAP**.
   
    ![Inicjator protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. W bloku **protokołu CHAP** i w sekcji **inicjatora protokołu CHAP** :
   
   1. Podaj nazwę użytkownika dla inicjatora protokołu CHAP.
   2. Podaj hasło dla inicjatora protokołu CHAP.
      
      > [!IMPORTANT]
      > Nazwa użytkownika protokołu CHAP musi zawierać mniej niż 233 znaków. Hasło protokołu CHAP musi mieć długość od 12 do 16 znaków. Dłuższa nazwa użytkownika lub hasło powoduje niepowodzenie uwierzytelnienia na hoście z systemem Windows.
   
   3. Potwierdź hasło.

       ![Inicjator protokołu CHAP 2](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat z potwierdzeniem. Kliknij przycisk **OK**, aby zapisać zmiany.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Aby skonfigurować jednokierunkowe uwierzytelnianie na serwerze hosta z systemem Windows
1. Na serwerze hosta systemu Windows uruchom inicjatora iSCSI.
2. W oknie **Właściwości inicjatora iSCSI** wykonaj następujące czynności:
   
   1. Kliknij kartę **odnajdywanie** .
      
       ![Właściwości inicjatora iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Kliknij pozycję **Odnajdź Portal**.
3. W oknie dialogowym **odnajdywanie portalu docelowego** :
   
   1. Określ adres IP urządzenia.
   2. Kliknij pozycję **Zaawansowane**.
      
       ![Odkryj Portal docelowy](./media/storsimple-configure-chap/IC740945.png)
4. W oknie dialogowym **Ustawienia zaawansowane** :
   
   1. Zaznacz pole wyboru **Włącz logowanie przy użyciu protokołu CHAP** .
   2. W polu **Nazwa** Podaj nazwę użytkownika określoną dla inicjatora protokołu CHAP w Azure Portal.
   3. W polu **docelowy klucz tajny** Podaj hasło określone dla inicjatora protokołu CHAP w Azure Portal.
   4. Kliknij przycisk **OK**.
      
       ![Ustawienia zaawansowane — ogólne](./media/storsimple-configure-chap/IC740946.png)
5. Na karcie **obiekty docelowe** okna **Właściwości inicjatora iSCSI** stan urządzenia powinien być wyświetlany jako **połączone**. Jeśli używasz urządzenia z systemem StorSimple 1200, każdy wolumin jest instalowany jako obiekt docelowy iSCSI. W związku z tym należy powtórzyć kroki 3-4 dla każdego woluminu.
   
    ![Woluminy zainstalowane jako oddzielne elementy docelowe](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Jeśli zmienisz nazwę iSCSI, Nowa nazwa zostanie użyta dla nowych sesji iSCSI. Nowe ustawienia nie są używane dla istniejących sesji do momentu wylogowania się i ponownego zalogowania.

Aby uzyskać więcej informacji na temat konfigurowania protokołu CHAP na serwerze hosta z systemem Windows, przejdź do [dodatkowych zagadnień](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Uwierzytelnianie dwukierunkowe lub wzajemne

W przypadku uwierzytelniania dwukierunkowego obiekt docelowy uwierzytelnia inicjatora, a następnie inicjator uwierzytelnia element docelowy. Ta procedura wymaga, aby użytkownik skonfigurował ustawienia inicjatora protokołu CHAP, odwrotne ustawienia protokołu CHAP na urządzeniu oraz oprogramowanie inicjatora iSCSI na hoście. Poniższe procedury opisują procedurę konfigurowania uwierzytelniania wzajemnego na urządzeniu oraz na hoście z systemem Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Aby skonfigurować urządzenie do uwierzytelniania wzajemnego

1. W Azure Portal przejdź do usługi StorSimple Device Manager. Kliknij pozycję **urządzenia** i wybierz i kliknij urządzenie, dla którego chcesz skonfigurować protokół CHAP. Przejdź do pozycji **Ustawienia urządzenia > zabezpieczenia**. W bloku **Ustawienia zabezpieczeń** kliknij pozycję **CHAP**.
   
    ![Obiekt docelowy protokołu CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Przewiń w dół na tej stronie i w sekcji **cel protokołu CHAP** :
   
   1. Podaj **nazwę użytkownika odwrotnego protokołu CHAP** dla urządzenia.
   2. Podaj **odwrotne hasło protokołu CHAP** dla Twojego urządzenia.
   3. Potwierdź hasło.
3. W sekcji **inicjatora protokołu CHAP** :
   
   1. Podaj **nazwę użytkownika** dla urządzenia.
   2. Podaj **hasło** dla swojego urządzenia.
   3. Potwierdź hasło.

       ![Inicjator protokołu CHAP 3](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat z potwierdzeniem. Kliknij przycisk **OK**, aby zapisać zmiany.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Aby skonfigurować uwierzytelnianie dwukierunkowe na serwerze hosta z systemem Windows

1. Na serwerze hosta systemu Windows uruchom inicjatora iSCSI.
2. W oknie **Właściwości inicjatora iSCSI** kliknij kartę **Konfiguracja** .
3. Kliknij przycisk **CHAP**.
4. W oknie dialogowym **hasło wzajemnego uwierzytelniania CHAP inicjatora iSCSI** :
   
   1. Wpisz **hasło wstecznego protokołu CHAP** , które zostało skonfigurowane w Azure Portal.
   2. Kliknij przycisk **OK**.
      
       ![klucz tajny wzajemnego protokołu CHAP inicjatora iSCSI](./media/storsimple-configure-chap/IC740949.png)
5. Kliknij kartę **obiekty docelowe** .
6. Kliknij przycisk **Połącz**. 
7. W oknie dialogowym **łączenie z miejscem docelowym** kliknij przycisk **Zaawansowane**.
8. W oknie dialogowym **Właściwości zaawansowane** :
   
   1. Zaznacz pole wyboru **Włącz logowanie przy użyciu protokołu CHAP** .
   2. W polu **Nazwa** Podaj nazwę użytkownika określoną dla inicjatora protokołu CHAP w Azure Portal.
   3. W polu **docelowy klucz tajny** Podaj hasło określone dla inicjatora protokołu CHAP w Azure Portal.
   4. Zaznacz pole wyboru **Wykonaj wzajemne uwierzytelnianie** .
      
       ![Ustawienia zaawansowane uwierzytelniania wzajemnego](./media/storsimple-configure-chap/IC740950.png)
   5. Kliknij przycisk **OK** , aby ukończyć konfigurowanie protokołu CHAP

Aby uzyskać więcej informacji na temat konfigurowania protokołu CHAP na serwerze hosta z systemem Windows, przejdź do [dodatkowych zagadnień](#additional-considerations).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Funkcja **szybkie łączenie** nie obsługuje połączeń z włączonym protokołem CHAP. Po włączeniu protokołu CHAP upewnij się, że używasz przycisku **Połącz** dostępnego na karcie **obiekty docelowe** , aby nawiązać połączenie z obiektem docelowym.

![Połącz z miejscem docelowym](./media/storsimple-configure-chap/IC740947.png)

W wyświetlonym oknie dialogowym **Połącz z celem** zaznacz pole wyboru **Dodaj to połączenie do listy ulubionych obiektów docelowych** . Wybranie tej opcji gwarantuje, że za każdym razem, gdy komputer zostanie ponownie uruchomiony, podjęto próbę przywrócenia połączenia z ulubionymi obiektami docelowymi iSCSI.

## <a name="errors-during-configuration"></a>Błędy podczas konfiguracji

Jeśli konfiguracja protokołu CHAP jest nieprawidłowa, prawdopodobnie zobaczysz komunikat o błędzie błędu **uwierzytelniania** .

## <a name="verification-of-chap-configuration"></a>Weryfikacja konfiguracji protokołu CHAP

Możesz sprawdzić, czy protokół CHAP jest używany, wykonując następujące kroki.

#### <a name="to-verify-your-chap-configuration"></a>Aby sprawdzić konfigurację protokołu CHAP
1. Kliknij pozycję **Ulubione obiekty docelowe**.
2. Wybierz element docelowy, dla którego włączono uwierzytelnianie.
3. Kliknij przycisk **szczegóły**.
   
    ![Ulubione obiekty docelowe właściwości inicjatora iSCSI](./media/storsimple-configure-chap/IC740951.png)
4. W oknie dialogowym **szczegóły ulubionego elementu docelowego** Zanotuj wpis w polu **Authentication (uwierzytelnianie** ). Jeśli konfiguracja zakończyła się pomyślnie, powinien zostać wyświetlony komunikat **CHAP**.
   
    ![Szczegóły ulubionego elementu docelowego](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zabezpieczeniach StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [korzystaniu z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

