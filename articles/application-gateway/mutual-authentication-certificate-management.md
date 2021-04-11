---
title: Eksportowanie łańcucha certyfikatów zaufanego klienta urzędu certyfikacji na potrzeby uwierzytelniania klientów
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak wyeksportować łańcuch certyfikatów zaufanego urzędu certyfikacji klienta na potrzeby uwierzytelniania klientów na platformie Azure Application Gateway
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 2329dc7426b223ef2c81dd0e2e607bccf73192e6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231556"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>Eksportowanie łańcucha zaufanych certyfikatów urzędu certyfikacji klienta do użycia z uwierzytelnianiem klienta
Aby można było skonfigurować uwierzytelnianie wzajemne przy użyciu klienta lub uwierzytelnianie klienta, Application Gateway wymaga, aby łańcuch certyfikatów zaufanego klienta urzędu certyfikacji został przekazany do bramy. Jeśli masz wiele łańcuchów certyfikatów, musisz utworzyć łańcuchy oddzielnie i przekazać je jako różne pliki na Application Gateway. W tym artykule dowiesz się, jak wyeksportować łańcuch certyfikatów zaufanego urzędu certyfikacji klienta, którego można użyć w konfiguracji uwierzytelniania klienta na bramie.  

## <a name="prerequisites"></a>Wymagania wstępne

Istniejący certyfikat klienta jest wymagany do wygenerowania łańcucha certyfikatów zaufanego klienta urzędu certyfikacji. 

## <a name="export-trusted-client-ca-certificate"></a>Eksportowanie certyfikatu zaufanego klienta urzędu certyfikacji

Certyfikat zaufanego urzędu certyfikacji jest wymagany do zezwalania na uwierzytelnianie klienta na Application Gateway. W tym przykładzie użyjemy certyfikatu TLS/SSL dla certyfikatu klienta, wyeksportuj jego klucz publiczny, a następnie wyeksportuj certyfikaty urzędu certyfikacji z klucza publicznego, aby uzyskać certyfikaty zaufanego urzędu certyfikacji klienta. Następnie będziemy łączyć wszystkie certyfikaty urzędu certyfikacji klienta w jeden łańcuch certyfikatów zaufanego klienta urzędu certyfikacji. 

Poniższe kroki ułatwiają wyeksportowanie pliku PEM lub CER dla certyfikatu:

### <a name="export-public-certificate"></a>Eksportuj certyfikat publiczny 

1. Aby uzyskać plik cer z certyfikatu, otwórz okno **Zarządzaj certyfikatami użytkowników**. Znajdź certyfikat, zazwyczaj w "Certificates-Current User\Personal\Certificates" i kliknij prawym przyciskiem myszy. Kliknij pozycję **Wszystkie zadania**, a następnie kliknij pozycję **Eksportuj**. Spowoduje to otwarcie **Kreatora eksportu certyfikatów**. Jeśli nie możesz znaleźć certyfikatu w ramach bieżącego User\Personal\Certificates, być może przypadkowo otwarto "certyfikaty — komputer lokalny", a nie "Certyfikaty — bieżący użytkownik"). Jeśli chcesz otworzyć Menedżera certyfikatów w bieżącym zakresie użytkownika przy użyciu programu PowerShell, wpisz *certmgr* w oknie konsoli.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia Menedżera certyfikatów z wybranymi certyfikatami i menu kontekstowe ze wszystkimi zadaniami, a następnie eksportuje wybrane.](./media/certificates-for-backend-authentication/export.png)

2. W Kreatorze kliknij pozycję **Dalej**.
    > [!div class="mx-imgBorder"]
    > ![Eksportowanie certyfikatu](./media/certificates-for-backend-authentication/exportwizard.png)

3. Wybierz pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij pozycję **Dalej**.
    > [!div class="mx-imgBorder"]
    > ![Nie Eksportuj klucza prywatnego](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na stronie **Format pliku eksportu** wybierz pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)**, a następnie kliknij pozycję **Dalej**.
    > [!div class="mx-imgBorder"]
    > ![Base-64 — zakodowana](./media/certificates-for-backend-authentication/base64.png)

5. W polu **plik do wyeksportowania** **Przejdź** do lokalizacji, do której chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

    > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawia Kreatora eksportu certyfikatów, w którym można określić plik do wyeksportowania.](./media/certificates-for-backend-authentication/browse.png)

6. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia Kreatora eksportu certyfikatów po zakończeniu eksportowania pliku.](./media/certificates-for-backend-authentication/finish.png)

7. Certyfikat został pomyślnie wyeksportowany.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu pokazuje Kreatora eksportu certyfikatów z komunikatem o powodzeniu.](./media/certificates-for-backend-authentication/success.png)

   Wyeksportowany certyfikat wygląda podobnie do tego:

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia symbol certyfikatu.](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>Eksportuj certyfikaty urzędu certyfikacji z certyfikatu publicznego

Po wyeksportowaniu certyfikatu publicznego wyeksportusz certyfikaty urzędu certyfikacji z certyfikatu publicznego. Jeśli masz tylko główny urząd certyfikacji, musisz tylko wyeksportować ten certyfikat. Jeśli jednak masz 1 + pośrednich urzędów certyfikacji, musisz również wyeksportować wszystkie te z nich. 

1. Po wyeksportowaniu klucza publicznego Otwórz plik.

    > [!div class="mx-imgBorder"]
    > ![Otwórz certyfikat autoryzacji](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![Informacje o certyfikacie](./media/mutual-authentication-certificate-management/general.png)

1. Wybierz kartę Ścieżka certyfikacji, aby wyświetlić urząd certyfikacji.

    > [!div class="mx-imgBorder"]
    > ![Szczegóły certyfikatu](./media/mutual-authentication-certificate-management/cert-details.png) 

1. Wybierz certyfikat główny, a następnie kliknij pozycję **Wyświetl certyfikat**.

    > [!div class="mx-imgBorder"]
    > ![ścieżka certyfikatu](./media/mutual-authentication-certificate-management/root-cert.png) 

   Powinny pojawić się szczegóły certyfikatu głównego.

    > [!div class="mx-imgBorder"]
    > ![Informacje o certyfikatach](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. Wybierz kartę **szczegóły** , a następnie kliknij pozycję **Kopiuj do pliku...**

    > [!div class="mx-imgBorder"]
    > ![Kopiuj certyfikat główny](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. W tym momencie wyodrębnisz szczegóły certyfikatu głównego urzędu certyfikacji z certyfikatu publicznego. Zostanie wyświetlony **Kreator eksportu certyfikatów**. Wykonaj kroki 2-7 z poprzedniej sekcji ([Eksportowanie certyfikatu publicznego](./mutual-authentication-certificate-management.md#export-public-certificate)), aby zakończyć pracę Kreatora eksportu certyfikatów. 

1. Teraz Powtórz kroki 2-6 z tej bieżącej sekcji ([wyeksportuj certyfikaty urzędu certyfikacji z certyfikatu publicznego](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate)) dla wszystkich pośrednich urzędów certyfikacji w celu wyeksportowania wszystkich pośrednich certyfikatów urzędu certyfikacji w formacie X. 509 z kodowaniem Base-64. CER).

    > [!div class="mx-imgBorder"]
    > ![certyfikat pośredni](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    Na przykład należy powtórzyć kroki od 2-6 z tej sekcji w pośrednim urzędzie certyfikacji *MSIT CAZ2* , aby wyodrębnić go jako własny certyfikat. 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>Łączenie wszystkich certyfikatów urzędu certyfikacji w jeden plik

1. Uruchom następujące polecenie ze wszystkimi wyodrębnionymi certyfikatami urzędu certyfikacji. 

    W systemie Windows:
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    W systemie Linux:
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    Uzyskany połączony certyfikat powinien wyglądać podobnie do poniższego:
    
    > [!div class="mx-imgBorder"]
    > ![połączony certyfikat](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>Następne kroki

Teraz masz łańcuch certyfikatów zaufanego klienta urzędu certyfikacji. Można go dodać do konfiguracji uwierzytelniania klienta w Application Gateway, aby umożliwić wzajemne uwierzytelnianie za pomocą bramy. Zobacz [Konfigurowanie uwierzytelniania wzajemnego przy użyciu Application Gateway z portalem](./mutual-authentication-portal.md) lub [Konfigurowanie uwierzytelniania wzajemnego przy użyciu Application Gateway przy użyciu programu PowerShell](./mutual-authentication-powershell.md).

